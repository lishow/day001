# python学习
## day001
### 安装pycharm
### 编写第一个程序helloworld
file > newproject > pure python > location:D:/pycharmproject/day001 >create  
右键day001 > new > pythonfile > name:helloworld > ok > `print('hello world ')` > 右键代码窗口 > run
### python实现简单的web服务器（实验楼练手项目）
1.http协议的基本原理  
* web程序一般依赖于tcp/ip协议，使用socket（套接字）实现计算机之间的通信。tcp socket由一个IP地址和端口号组成。  
* IP地址是一个32位的二进制数，通常被分割为4个“8位二进制数”，174.136.14.108是它们的十进制形式。  
* 简单理解socket  
* http（超文本传输协议）  
请求 HTTP method：HTTP请求方法。 URL：client请求文件的路径。 header field：HTTP头的键值对。body：负载数据。  
响应 status code：状态码 200/404。 status phrase：对状态码的描述
## day002
### python实现简单的web服务器(接)
2.在编辑器新建了项目day002 > 文件server.py  
3.基本概念  
* 等待一个向我们服务器的HTTP请求  
* 解析该请求  
* _了解该请求的内容_  
* _服务器根据请求抓取需要的数据（从服务器本地文件中读取或者程序动态生成）_  
* _将数据格式化为请求需要的格式_  
* 返回HTTP响应  
 
尝试编写第一个Web服务器  
我们的工作是斜体部分内容
正体部分内容python标准库中的BaseHTTPServer可以帮助我们模块处理  
 
```
# -*- coding:utf-8 -*-
import http.server


class RequestHandler(http.server.BaseHTTPRequestHandler):
    """处理请求并返回页面"""

    # 页面模板
    Page = ('\n'
            '        <html>\n'
            '        <body>\n'
            '        <p>Hello,web!</p>\n'
            '        </body>\n'
            '        </html>\n'
            '       ')

    # 处理一个GET请求
    def do_GET(self):
        self.send_response(200)
        self.send_header("Content-Type", "text/html")
        self.send_header("Content-Length", str(len(self.Page)))
        self.end_headers()
        self.wfile.write(self.Page.encode('utf-8'))


if __name__ == '__main__':
    serverAddress = ('', 8080)
    server = http.server.HTTPServer(serverAddress, RequestHandler)
    server.serve_forever()
```
调试过程中没有注意大小写和缩进的区分，浏览器一直是501 不支持‘GET’方法
在寻求了[大佬shisiying](http://www.xhzyxed.cn/)的帮助后，成功得到了200  

显示请求的信息  
```
# -*- coding:utf-8 -*-
import http.server


class RequestHandler(http.server.BaseHTTPRequestHandler):
    """处理请求并返回页面"""

    # 页面模板
    Page = '''\
    <html>
    <body>
    <table>
    <tr>  <td>Header</td>         <td>Value</td>          </tr>
    <tr>  <td>Date and time</td>  <td>{date_time}</td>    </tr>
    <tr>  <td>Client host</td>    <td>{client_host}</td>  </tr>
    <tr>  <td>Client port</td>    <td>{client_port}</td>  </tr>
    <tr>  <td>Command</td>        <td>{command}</td>      </tr>
    <tr>  <td>Path</td>           <td>{path}</td>         </tr>
    </table>
    </body>
    </html>'''

    def do_GET(self):
        page = self.create_page()
        self.send_content(page)

    def create_page(self):
        values = {
            'date_time': self.date_time_string(),
            'client_host': self.client_address[0],
            'client_port': self.client_address[1],
            'command': self.command,
            'path': self.path
        }
        page = self.Page.format(**values)
        return page

    def send_content(self, page):
        self.send_response(200)
        self.send_header("Content-Type", "text/html")
        self.send_header("Content-Length", str(len(self.Page)))
        self.end_headers()
        self.wfile.write(self.Page.encode('utf-8'))


if __name__ == '__main__':
    serverAddress = ('', 8080)
    server = http.server.HTTPServer(serverAddress, RequestHandler)
    server.serve_forever()
```
