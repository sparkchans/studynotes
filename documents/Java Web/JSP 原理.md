1. **JSP 组成:**   
   * 静态部分: 标准的 HTML 标签, 静态的页面内容.
   * 动态部分: 受 Java 控制的部分, 由 Java 脚本动态生成.
2. **JSP 的实质:**   
JSP 是 Servlet 的一种简化, 容器(这里指的是 Tomcat)会为 JSP 生成一个相对应的 Servlet 实现类, JSP 的内容在该实现类的 `service()` 进行输出. 对于 Tomcat 容器来说, 对每一个放在 Web 应用根目录下的 JSP 都会在 Tomcat 的 work\Catalina\localhost\应用名称\org\apache\jsp 目录下生成对应的 Java 文件和编译后的 class 文件.
    ```JSP
    <!--JSP 文件-->
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
    <head>
        <title>Title</title>
    </head>
    <body>
        <h1>This JSP</h1>
    </body>
    </html>

    // 生成的 Servlet 的 service() 方法
    public void _jspService(final javax.servlet.http.HttpServletRequest request, final javax.servlet.http.HttpServletResponse response)
      throws java.io.IOException, javax.servlet.ServletException {
        // 省略其它代码

        // 这里生成内容和 JSP 页面内容相对应
        out.write("\n");
        out.write("<html>\n");
        out.write("<head>\n");
        out.write("    <title>Title</title>\n");
        out.write("</head>\n");
        out.write("<body>\n");
        out.write("    <h1>This is JSP</h1>\n");
        out.write("</body>\n");
        out.write("</html>\n");

        // 省略其它代码
    }
    ```
