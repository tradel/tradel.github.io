<!--
.. title: Using Jackson 2.x with Jersey 1.x
.. slug: using-jackson-2-with-jersey-1
.. date: 2014-01-31 14:02:04 UTC-05:00
.. tags: java, jersey, jackson
.. category: Java
.. link:
.. description:
.. type: text
-->


It turns out that the key is adding `com.fasterxml.jackson.jaxrs.json` to the
list of packages to be scanned for configuration properties.

<!-- TEASER_END -->

Here's a complete example from my `web.xml`:

``` xml hl_lines="10"
    <servlet>
        <servlet-name>api-v2-servlet</servlet-name>
        <servlet-class>com.sun.jersey.spi.container.servlet.ServletContainer</servlet-class>
        <init-param>
            <param-name>com.sun.jersey.api.json.POJOMappingFeature</param-name>
            <param-value>true</param-value>
        </init-param>
        <init-param>
            <param-name>com.sun.jersey.config.property.packages</param-name>
            <param-value>com.singularity.ee.controller.servlet.rest.v2;com.fasterxml.jackson.jaxrs.json</param-value>
        </init-param>
        <init-param>
            <param-name>com.sun.jersey.spi.container.ContainerResponseFilters</param-name>
            <param-value>com.sun.jersey.server.linking.LinkFilter;com.sun.jersey.api.container.filter.LoggingFilter</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>api-v2-servlet</servlet-name>
        <url-pattern>/api/v2/*</url-pattern>
    </servlet-mapping>
```
