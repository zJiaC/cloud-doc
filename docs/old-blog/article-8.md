# 发现错误过程
在开发spring-boot整合druid链接池(1.1.2版本)的项目时，同事再开发的时候碰到了，connection holder is null这样一个错误。
<!--more-->
帮助其解决时通过google发现可能是druid出现的错误。
但按照网上所说只要配置这几个参数：
配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒 
timeBetweenEvictionRunsMillis 
配置一个连接在池中最小生存的时间，单位是毫秒 
minEvictableIdleTimeMillis 
关闭长时间不使用的连接超时时间，单位秒 
removeAbandonedTimeout 
关闭长时间不使用的连接
removeAbandoned 
实际上并不起作用。
最后发现这篇博文：
* [connection holder is null](http://timerbin.iteye.com/blog/2332995)
发现确实是由多数据源，开启事务导致了这个错误,且未配置单独的事务管理器，所以也没指定那个事务管理器。
然后通过阅读源码：
一开始创建连接会实例化ConnectionHolder
```java
  public static Connection getConnection(DataSource dataSource) throws CannotGetJdbcConnectionException {
    try {
      return doGetConnection(dataSource);
    } catch (SQLException var2) {
      throw new CannotGetJdbcConnectionException("Could not get JDBC Connection", var2);
    }
  }

  public static Connection doGetConnection(DataSource dataSource) throws SQLException {
    Assert.notNull(dataSource, "No DataSource specified");
    ConnectionHolder conHolder = (ConnectionHolder)TransactionSynchronizationManager.getResource(dataSource);
    if (conHolder == null || !conHolder.hasConnection() && !conHolder.isSynchronizedWithTransaction()) {
      logger.debug("Fetching JDBC Connection from DataSource");
      Connection con = dataSource.getConnection();
      if (TransactionSynchronizationManager.isSynchronizationActive()) {
        logger.debug("Registering transaction synchronization for JDBC Connection");
        ConnectionHolder holderToUse = conHolder;
        if (conHolder == null) {
          holderToUse = new ConnectionHolder(con);
        } else {
          conHolder.setConnection(con);
        }

        holderToUse.requested();
        TransactionSynchronizationManager.registerSynchronization(new DataSourceUtils.ConnectionSynchronization(holderToUse, dataSource));
        holderToUse.setSynchronizedWithTransaction(true);
        if (holderToUse != conHolder) {
          TransactionSynchronizationManager.bindResource(dataSource, holderToUse);
        }
      }

      return con;
    } else {
      conHolder.requested();
      if (!conHolder.hasConnection()) {
        logger.debug("Fetching resumed JDBC Connection from DataSource");
        conHolder.setConnection(dataSource.getConnection());
      }

      return conHolder.getConnection();
    }
  }
```
但执行完之后忘记了手动释放连接，只是connection.close();导致了错误。
```java
  public static void releaseConnection(Connection con, DataSource dataSource) {
    try {
      doReleaseConnection(con, dataSource);
    } catch (SQLException var3) {
      logger.debug("Could not close JDBC Connection", var3);
    } catch (Throwable var4) {
      logger.debug("Unexpected exception on closing JDBC Connection", var4);
    }

  }

  public static void doReleaseConnection(Connection con, DataSource dataSource) throws SQLException {
    if (con != null) {
      if (dataSource != null) {
        ConnectionHolder conHolder = (ConnectionHolder)TransactionSynchronizationManager.getResource(dataSource);
        if (conHolder != null && connectionEquals(conHolder, con)) {
          conHolder.released();
          return;
        }
      }

      logger.debug("Returning JDBC Connection to DataSource");
      doCloseConnection(con, dataSource);
    }
  }

  public static void doCloseConnection(Connection con, DataSource dataSource) throws SQLException {
    if (!(dataSource instanceof SmartDataSource) || ((SmartDataSource)dataSource).shouldClose(con)) {
      con.close();
    }

  }
```
除了上面外，我这边的解决办法是:
DataSourceUtils.releaseConnection(connection, this.dataSource);//正常执行完语句，调用该方法关闭该数据源的连接,这样就会正常释放连接。
