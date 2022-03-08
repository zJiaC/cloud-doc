<center> Web Service基本概念</center>
====================================

> Web Service也叫XML Web Service WebService是一种可以接收从Internet或者Intranet上的其它系统中传递过来的请求，轻量级的独立的通讯技术。是:通过SOAP在Web上提供的软件服务，使用WSDL文件进行说明，并通过UDDI进行注册。

<!--more-->

> XML：(Extensible Markup Language)扩展型可标记语言。面向短期的临时数据处理、面向万维网络，是Soap的基础。

> Soap：(Simple Object Access Protocol)简单对象存取协议。是XML Web Service 的通信协议。当用户通过UDDI找到你的WSDL描述文档后，他通过可以SOAP调用你建立的Web服务中的一个或多个操作。SOAP是XML文档形式的调用方法的规范，它可以支持不同的底层接口，像HTTP(S)或者SMTP。

> WSDL：(Web Services Description Language) WSDL 文件是一个 XML 文档，用于说明一组 SOAP 消息以及如何交换这些消息。大多数情况下由软件自动生成和使用。

> UDDI (Universal Description, Discovery, and Integration) 是一个主要针对Web服务供应商和使用者的新项目。在用户能够调用Web服务之前，必须确定这个服务内包含哪些商务方法，找到被调用的接口定义，还要在服务端来编制软件，UDDI是一种根据描述文档来引导系统查找相应服务的机制。UDDI利用SOAP消息机制（标准的XML/HTTP）来发布，编辑，浏览以及查找注册信息。它采用XML格式来封装各种不同类型的数据，并且发送到注册中心或者由注册中心来返回需要的数据。


 问题及解决办法
 =============

* 对接接口时抛出wsse的安全问题
  解决的办法 [java使用Message Handler修改WebService客户端的SOAP头](http://outofmemory.cn/code-snippet/2344/java-usage-Message-Handler-modify-WebService-customer-duan-SOAP-tou)

* 在调用webService时，有时候需要在SOAP头中插入信息，比如鉴权信息。

    下面的例子演示如何设置给WebService设置授权信息。

    首先我们需要实现SOAPHandler接口的类，这个类决定了要调用那些Handler，以什么顺序调用。

    最后我们需要给WebService的客户端添加HandlerResolver类实例.

    默认情况下SOAP头是空的：

``` xml
<S:Header>
     <wsse:Security xmlns:wsse="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd">                              
          <wsse:UsernameToken xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">                              
               <wsse:Username>TestUser</wsse:Username>
               <wsse:Password Type="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-username-token-profile-1.0#PasswordText">TestPassword</wsse:Password>
          </wsse:UsernameToken>
     </wsse:Security>
</S:Header>
如下是SOAPHandler<SOAPMessageContext>的实现，这里有好多方法可以实现，但在这里我们仅需要实现handleMessage()方法。
```

``` java
package cn.outofmemory.ws.example;

import java.util.Set;
import javax.xml.namespace.QName;
import javax.xml.soap.SOAPElement;
import javax.xml.soap.SOAPEnvelope;
import javax.xml.soap.SOAPHeader;
import javax.xml.soap.SOAPMessage;
import javax.xml.ws.handler.MessageContext;
import javax.xml.ws.handler.soap.SOAPHandler;
import javax.xml.ws.handler.soap.SOAPMessageContext;

/**
 *
 * @author outofmemory.cn
 */
public class HeaderHandler implements SOAPHandler<SOAPMessageContext> {

    public boolean handleMessage(SOAPMessageContext smc) {

        Boolean outboundProperty = (Boolean) smc.get(MessageContext.MESSAGE_OUTBOUND_PROPERTY);

        if (outboundProperty.booleanValue()) {

            SOAPMessage message = smc.getMessage();

            try {

                SOAPEnvelope envelope = smc.getMessage().getSOAPPart().getEnvelope();
                SOAPHeader header = envelope.addHeader();

                SOAPElement security =
                        header.addChildElement("Security", "wsse", "http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd");

                SOAPElement usernameToken =
                        security.addChildElement("UsernameToken", "wsse");
                usernameToken.addAttribute(new QName("xmlns:wsu"), "http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd");

                SOAPElement username =
                        usernameToken.addChildElement("Username", "wsse");
                username.addTextNode("TestUser");

                SOAPElement password =
                        usernameToken.addChildElement("Password", "wsse");
                password.setAttribute("Type", "http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-username-token-profile-1.0#PasswordText");
               password.addTextNode("TestPassword");

                //Print out the outbound SOAP message to System.out
                message.writeTo(System.out);
                System.out.println("");

            } catch (Exception e) {
                e.printStackTrace();
            }

        } else {
            try {

                //This handler does nothing with the response from the Web Service so
                //we just print out the SOAP message.
                SOAPMessage message = smc.getMessage();
                message.writeTo(System.out);
                System.out.println("");

            } catch (Exception ex) {
                ex.printStackTrace();
            } 
        }

        return outboundProperty;

    }

    public Set getHeaders() {
        //throw new UnsupportedOperationException("Not supported yet.");
        return null;
    }

    public boolean handleFault(SOAPMessageContext context) {
        //throw new UnsupportedOperationException("Not supported yet.");
        return true;
    }

    public void close(MessageContext context) {
    //throw new UnsupportedOperationException("Not supported yet.");
    }
}
如下是HandlerResolver的实现类定义：

package cn.outofmemory.ws.example;

import java.util.ArrayList;
import java.util.List;
import javax.xml.ws.handler.Handler;
import javax.xml.ws.handler.HandlerResolver;
import javax.xml.ws.handler.PortInfo;

/**
 *
 * @author outofmemory.cn
 */
public class HeaderHandlerResolver implements HandlerResolver {

public List<Handler> getHandlerChain(PortInfo portInfo) {
      List<Handler> handlerChain = new ArrayList<Handler>();

      HeaderHandler hh = new HeaderHandler();

      handlerChain.add(hh);

      return handlerChain;
   }
}
下面是调用webService的代码：

   JavadbWebServiceService service = new JavadbWebServiceService();

   HeaderHandlerResolver handlerResolver = new HeaderHandlerResolver();
   service.setHandlerResolver(handlerResolver);

   JavadbWebService port = service.getJavadbWebServicePort();

   //调用web service
   String currentTime = port.getTime();

   System.out.println("Current time is: " + currentTime);
```


* 若是用vpn调试则需使用
  -Djava.net.preferIPv4Stack=true    

# 备注

* java自带工具，可以使用命令行wsimport构建WebService客户端,需要注意的一点生成代码时需要指定编码格式，否则获取数据会出现乱码导致无法解析.
