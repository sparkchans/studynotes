1.**Web 应用结构:**   
* 应用名称: 
  * WEB-INF: 是一个特殊的文件夹, 容器会包含该文件夹下的内容, 客户端浏览器无法直接访问该文件夹下的内容.
    * classes: 存放应用的 class 文件.
    * lib: 存放应用所依赖的包.
    * web.xml: 配置该该 Web 应用. 每个容器都会提供一个系统的 web.xml 文件, 用于描述所有 Web 应用的配置属性. Tomcat 容器的系统 web.xml 放在 Tomcat 的 conf 路径下. web.xml 文件可以配置的 Web 应用如下:
      * JSP
      * Servlet
      * Listener
      * Filter
      * 配置标签库
      * 配置 JSP 属性
      * 资源引用
      * Web 应用首页
  * JSP 文件: 客户端可以直接访问应用目录下可存放的 JSP 文件.
