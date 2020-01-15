1. **JSP 4 中基本语法:**
    * JSP 注释: `<%-- 注释内容 --%>`
    * JSP 声明: JSP 页面声明的变量或方法将转化成 JSP 生成的对应的 Servlet 的成员变量或方法
        ```JSP
        <%!
            public int count;

            public String hello() {
                return "hello";
            }
        %>
        ```
    * 输出表达式: `<%="hello"%>` 等同于 `out.println("hello");` 输出表达式后面没有分号.
    * JSP 脚本: 
        ``` JSP
        <body>
        <table bgcolor="#f5f5dc" border="1" width="500px">
            <%
                for (int i = 0; i < 10; i++) {
            %>
                <tr>
                    <td>循环值:</td>
                    <td><%=i%></td>
                </tr>
            <%
                }
            %>
        </table>
    </body>
        ```
2. **JSP 的三个编译指令:**
   * page: 
     * language: 表明当前页面脚本语言种类.
     * extends: 指定编译产生的 Java 类所继承的父类或所实现的接口.
     * import: 导入包.
     * session: 指定页面是否需要 session.
     * buffer: 指定输出缓冲区的大小.
     * autoFlush: 指定当缓冲区满时, 是否强制输出缓冲区的内容.
     * info: 设置 JSP 程序的信息.
     * errorPage: 指定错误处理页面.
     * isErrorPage: 设置本页面是否为错误处理程序.
     * contentType: 设定网页的文件格式和编码字符集.
     * pageEncoding: 指定生成网页的编码字符集.
   * include: 将一个外部文件嵌入到当前 JSP 文件中, 同时解析这个页面中的 JSP 语句.
        ```JSP
        <body>
            <h1>Before Include</h1>
            <%@include file="table.jsp"%>
            <h1>After Include</h1>
        </body>

        ```
   * taglib: 用于定义和访问自定义标签.
3. **JSP 7 个动作指令:**   
    * forward: 执行页面转向, 将请求转发到下一个页面. 执行页面转向, 浏览器地址不变.
        ```JSP
        <!--原始页面-->
        <jsp:forward page="forward-result.jsp">
            <jsp:param name="username" value="admin"/>
        </jsp:forward>
        <!--转发页面-->
        <%=request.getParameter("username")%>
        ```
    * userBean, setProperty, getProperty: 初始化 Java 示例, 并设置相应的属性值.
        ```JSP
            <jsp:useBean id="user" class="study.User" scope="page">
            <jsp:setProperty name="user" value="admin">
            <!--输出属性值-->
            <jsp:getPropery name="user" value="admin">
        ```
    * param: 用于设置参数值, 与 `jsp:include, jsp:forward, jsp:plugin` 结合使用.