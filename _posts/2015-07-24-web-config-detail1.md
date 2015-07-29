---
layout: post
title: web.xml配置详解 一
category: Web
date: 2015-07-24
---

<?xml version="1.0" encoding="UTF-8"?>  
  
<web-app  xmlns="http://java.sun.com/xml/ns/j2ee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd" version="2.4">  
      
<display-name>网站名称</display-name>  
<description>网站描述</description>  
     
<!-- icon元素包含small-icon和large-icon两个子元素.用来指定web站台中小图标和大图标的路径. -->  
<icon>  
    <!--small-icon元素应指向web站台中某个小图标的路径,大小为16 X 16 pixel,但是图象文件必须为GIF或JPEG格式,扩展名必须为:.gif或.jpg. -->  
    <small-icon>路径/small.gif</small-icon>  
    <!--large-icon元素应指向web站台中某个大图表路径,大小为32 X 32 pixel,但是图象文件必须为GIF或JPEG的格式,扩展名必须为; gif或jpg.-->  
    <large-icon>路径/large.jpg</large-icon>  
</icon>  
  
<!-- 配置集群的时候，要用到，在这篇文章：“apache+tomcat集群、负载均衡及session复制”里的第三条（http://jiajun.iteye.com/admin/blogs/278586） -->  
<distributable/>  
  
<!-- context-param 元素用来设定web站台的环境参数(context),它包含两个子元素:param-name和param-value.,如spring的典型配置  -->  
<context-param>  
    <!-- 设定Context名称  -->  
    <param-name>contextConfigLocation</param-name>  
    <!-- 设定Context名称的值  -->  
    <param-value>/WEB-INF/spring-config.xml</param-value>  
</context-param>  
<!-- 备注：此所设定的参数,在JSP网页中可以使用下列方法来取得:${initParam.param_name}  
       若在Servlet可以使用下列方法来获得:String param_name=getServletContext().getInitParamter("param_name");   
-->  
  
<!-- filter元素用来声明filter的相关设定.filter元素除了下面介绍的的子元素之外,还包括下面<servlet>绍过的<icon>,<display-name>,<description>,<init-param>,其用途一样.-->  
<filter>  
  <!-- 定义Filter的名称.  -->  
  <filter-name>URIFilter</filter-name>  
  <!-- 定义Filter的类名称 -->  
  <filter-class>org.cjj.filter.URIFilter</filter-class>  
  <init-param>  
     <param-name>encoding</param-name>  
     <param-value>UTF-8</param-value>  
  </init-param>  
</filter>  
<!-- filter-mapping 元素的两个主要子元素filter-name和url-pattern.用来定义Filter所对应的URL.和下面的servlet-mapping一样  -->  
<filter-mapping>  
  <!-- 定义Filter的名称,和上面定义的Filter名称要一致 -->  
  <filter-name>URIFilter</filter-name>  
  <!-- ilter所对应的RUL,这里是所有的URL -->  
  <url-pattern>/*</url-pattern>  
</filter-mapping>  

<!-- 这里配置和上面的Filter配置一样 -->  
<servlet>  
  <description><![CDATA[Application Setup]]></description>  
  <servlet-name>HTML Action Servlet</servlet-name>  
  <servlet-class>org.cjj.servlet.ActionServlet</servlet-class>  
  <init-param>  
     <param-name>config</param-name>  
     <param-value>/WEB-INF/struts-config.xml</param-value>  
  </init-param>  
  <init-param>  
     <param-name>debug</param-name>  
     <param-value>2</param-value>  
  </init-param>  
  <init-param>  
     <param-name>detail</param-name>  
     <param-value>2</param-value>  
  </init-param>  
  <load-on-startup>0</load-on-startup>  
</servlet>  
<!-- 和上面的filter-mapping,一样 -->  
<servlet-mapping>  
  <servlet-name>HTML Action Servlet</servlet-name>  
  <url-pattern>*.do</url-pattern>  
</servlet-mapping>  

<!-- listener元素用来定义Listener接口,它的主要子元素为<listener-class>  -->  
<listener>  
  <!-- ServletContextListener接口的实现，监听Content -->  
  <listener-class>org.cjj.listener.ContextListener</listener-class>  
</listener>  
<listener>  
  <!-- HttpSessionListener, HttpSessionAttributeListener接口的实现，监听session -->  
  <listener-class>org.cjj.listener.SessionListener</listener-class>  
</listener>  
 
<!-- session-config包含一个子元素session-timeout.定义web站台中的session参数.  -->  
<session-config>  
  <!-- 定义这个web站台所有session的有效期限.单位为分钟. 例子中为600分钟 -->  
  <session-timeout>600</session-timeout>  
</session-config>  

<!-- mime-mapping包含两个子元素extension和mime-type.定义某一个扩展名和某一MIME Type做对映.  -->  
<mime-mapping>  
    <!-- 扩展名称  -->  
    <extension>htm</extension>  
    <!-- MIME格式  -->  
    <mime-type>text/html</mime-type>  
</mime-mapping>  
<mime-mapping>  
    <extension>flv</extension>  
    <mime-type>video/x-flv</mime-type>  
</mime-mapping>  
<mime-mapping>  
    <extension>doc</extension>  
    <mime-type>application/vnd.ms-word</mime-type>  
</mime-mapping>  
<mime-mapping>  
    <extension>xls</extension>  
    <mime-type>application/vnd.ms-excel</mime-type>  
</mime-mapping>   

<!-- welcome-file-list包含子元素welcome-file,用来定义首页列单,如：  -->  
<welcome-file-list>  
    <welcome-file>index.jsp</welcome-file>  
    <welcome-file>index.html</welcome-file>  
</welcome-file-list>  

<!-- error-page元素包含三个子元素error-code,exception-type和location.将错误代码(Error Code)或异常(Exception)的种类对应到web站台资源路径.-->  
<error-page>  
    <!-- HTTP Error code -->  
    <error-code>403</error-code>  
    <!-- 在web站点内的相关资源路径  -->  
    <location>路径/403.jsp</location>  
</error-page>  
<error-page>  
    <error-code>404</error-code>  
    <location>路径/404.jsp</location>  
</error-page>  
<error-page>  
    <error-code>500</error-code>  
    <location>路径/500.jsp</location>  
</error-page>  
<error-page>  
    <!-- 一个完整名称的Java异常类型  -->  
    <exception-type>java.lang.Exception</exception-type>  
     <location>路径/exception.jsp</location>  
</error-page>   

<!-- jsp-config元素主要用来设定JSP的相关配置,<jsp:config>包括<taglib>和<jsp-property-group>两个子元素.  
              其中<taglib>元素在JSP 1.2时就已经存在了;而<jsp-property-group>是JSP 2.0新增的元素.  
-->  
<jsp-config>  
    <!-- taglib元素包含两个子元素taglib-uri和taglib-location.用来设定JSP网页用到的Tag Library路径.  -->  
    <taglib>  
        <!-- 定义TLD文件的URI,JSP网页的taglib指令可以经由这个URI存取到TLD文件.  -->  
        <taglib-uri>core</taglib-uri>  
        <!-- TLD文件对应Web站台的存放位置.  -->  
        <taglib-location>/WEB-INF/tld/jstl-1-core.tld</taglib-location>  
    </taglib>  
    <taglib>  
        <taglib-uri>bean</taglib-uri>  
        <taglib-location>/WEB-INF/tld/struts-bean.tld</taglib-location>  
    </taglib>  
    <taglib>  
        <taglib-uri>html</taglib-uri>  
        <taglib-location>/WEB-INF/tld/struts-html.tld</taglib-location>  
    </taglib>  
    <taglib>  
        <taglib-uri>logic</taglib-uri>  
        <taglib-location>/WEB-INF/tld/struts-logic.tld</taglib-location>  
    </taglib>  
    <taglib>  
        <taglib-uri>tiles</taglib-uri>  
        <taglib-location>/WEB-INF/tld/struts-tiles.tld</taglib-location>  
    </taglib>  
     <!-- jsp-property-group元素包含8个元素 -->  
     <jsp-property-group>  
        <!-- 此设定的说明  -->  
        <description>Special property group for JSP Configuration JSP example.</description>  
        <!-- 此设定的名称  -->  
        <display-name>JSPConfiguration</display-name>  
        <!-- 设定值所影响的范围,如:/CH2 或者/*.jsp  -->  
        <uri-pattern>/*</uri-pattern>  
        <!-- 若为true,表示不支持EL语法.  -->  
        <el-ignored>true</el-ignored>  
        <!-- 若为true表示不支持<%scription%>语法.  -->  
        <scripting-invalid>false</scripting-invalid>   
        <!-- 设置JSP网页的抬头,扩展名为.jspf  -->  
        <include-prelude>.jspf</include-prelude>   
        <!-- 设置JSP网页的结尾,扩展名为.jspf  -->  
        <include-coda>.jspf</include-coda>   
        <!-- 编码 -->  
        <page-encoding>utf-8</page-encoding>  
    </jsp-property-group>  
</jsp-config>  
  
<!-- esource-ref元素包括五个子元素description,res-ref-name,res-type,res-auth,res-sharing-scope.利用JNDI取得站台可  -->  
<resource-ref>  
    <!-- 资源说明  -->  
    <description>JNDI JDBC DataSource of JSPBook</description>  
    <!-- 资源名称  -->  
    <res-ref-name>jdbc/sample_db</res-ref-name>  
    <!-- 资源种类  -->  
    <res-type>javax.sql.DataSoruce</res-type>  
    <!-- 资源由Application或Container来许可,如下面为Container  -->  
    <res-auth>Container</res-auth>  
    <!-- 资源是否可以共享.默认值为 Shareable 可选择Shareable|Unshareable -->  
    <res-sharing-scope>Unshareable</res-sharing-scope>   
</resource-ref>   
      
</web-app>  


