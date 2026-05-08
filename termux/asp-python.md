# Asp-python

基于 apache2 的 cgi, 在 termux 上实现用 python 模拟 ASP.

*Apache2 配置文件修改*
```bash
nvim $PREFIX/etc/apache2/httpd.conf
```

找到 <Directory "/data/data/com.termux/files/usr/share/apache2/default-site/htdocs">
并在 Options 添加 ExecCGI, 添加后结果如下：
```bash
<Directory "/data/data/com.termux/files/usr/share/apache2/default-site/htdocs">
    Options Indexes FollowSymLinks ExecCGI
    AllowOverride All
    Require all granted
</Directory>

```

*Asp 文件创建*
```bash
nvim $PREFIX/share/apache2/default-site/htdocs/test.asp
```

写入内容(AI 示例)
```python
#!/data/data/com.termux/files/usr/bin/python3
# -*- coding: utf-8 -*-
from datetime import datetime
from urllib.parse import parse_qs
import os
import sys

# ===================== 仿 ASP Request 对象 =====================
# 同时处理 GET 和 POST 参数
params = {}

# 1. 解析 GET 参数（仿 Request.QueryString）
query_string = os.environ.get("QUERY_STRING", "")
get_params = parse_qs(query_string)
params.update(get_params)

# 2. 解析 POST 参数（仿 Request.Form）
if os.environ.get("REQUEST_METHOD") == "POST":
    try:
        content_length = int(os.environ.get("CONTENT_LENGTH", 0))
        if content_length > 0:
            post_data = sys.stdin.read(content_length)
            post_params = parse_qs(post_data)
            params.update(post_params)
    except Exception:
        pass  # 处理读取失败的情况

# 获取参数（仿 Request("name")）
name = params.get("name", ["访客"])[0]
age = params.get("age", ["未知"])[0]
redirect = params.get("redirect", [""])[0]  # 跳转目标URL

# 仿 ASP Now()
now_time = datetime.now().strftime("%Y-%m-%d %H:%M:%S")

# ===================== 仿 ASP Response.Redirect 跳转 =====================
if redirect and redirect.startswith(("http://", "https://", "/")):
    # 输出跳转响应头
    print("Status: 302 Found")
    print(f"Location: {redirect}")
    print("Content-type: text/html; charset=utf-8\n")
    print(f"""
    <html><body>
    <p>正在跳转到 <a href="{redirect}">{redirect}</a>...</p>
    </body></html>
    """)
    sys.exit()  # 终止脚本，不输出后续内容

# ===================== CGI 响应头（正常页面） =====================
print("Content-type: text/html; charset=utf-8\n")

# ===================== HTML 页面输出 =====================
html_start = """
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>Apache + Python CGI 仿 ASP 页面（带POST+跳转）</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            margin-top: 50px;
            background-color: #f4f4f4;
        }
        .container {
            width: 80%;
            margin: 0 auto;
            background: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        table {
            margin: 30px auto;
            border-collapse: collapse;
            width: 80%;
        }
        td, th {
            border: 1px solid #999;
            padding: 8px 20px;
        }
        th {
            background-color: #007BFF;
            color: white;
        }
        .form-box {
            margin: 20px auto;
            width: 50%;
            text-align: left;
        }
        input, button {
            width: 100%;
            padding: 8px;
            margin: 5px 0;
            box-sizing: border-box;
        }
        button {
            background-color: #007BFF;
            color: white;
            border: none;
            cursor: pointer;
        }
        button:hover {
            background-color: #0056b3;
        }
    </style>
</head>
<body>
    <div class="container">
        <h2>🎉 Apache + Python CGI 高仿 ASP 动态网页</h2>
        <p>当前访问用户：<strong>"""
html_mid = """</strong></p>
        <p>用户年龄：<strong>"""
html_mid2 = """</strong></p>
        <p>服务器当前时间：<strong>"""
html_mid3 = """</strong></p>

        <h3>动态生成数据表格（仿 ASP 循环+条件样式）</h3>
        <table>
            <tr><th>序号</th><th>数值</th><th>翻倍结果</th><th>奇偶行样式</th></tr>
"""

# 输出前半部分
print(html_start + name + html_mid + age + html_mid2 + now_time + html_mid3)

# 仿 ASP For 循环 + If 条件
for i in range(1, 7):
    double_val = i * 2
    if i % 2 == 0:
        bg_color = "#f0f8ff"
        parity = "偶数行"
    else:
        bg_color = "#ffffff"
        parity = "奇数行"
    print(f'<tr style="background:{bg_color}">')
    print(f'    <td>{i}</td>')
    print(f'    <td>{i}</td>')
    print(f'    <td>{double_val}</td>')
    print(f'    <td>{parity}</td>')
    print('</tr>')

# 表单部分（含GET、POST、跳转功能）
html_end = """
        </table>

        <h3>1. 仿 ASP GET 表单提交</h3>
        <div class="form-box">
            <form method="get" action="/test.asp">
                <label for="name">姓名：</label>
                <input type="text" id="name" name="name" placeholder="请输入姓名">
                <label for="age">年龄：</label>
                <input type="text" id="age" name="age" placeholder="请输入年龄">
                <button type="submit">GET 提交</button>
            </form>
        </div>

        <h3>2. 仿 ASP POST 表单提交</h3>
        <div class="form-box">
            <form method="post" action="/test.asp">
                <label for="name">姓名：</label>
                <input type="text" id="name" name="name" placeholder="请输入姓名">
                <label for="age">年龄：</label>
                <input type="text" id="age" name="age" placeholder="请输入年龄">
                <button type="submit">POST 提交</button>
            </form>
        </div>

        <h3>3. 仿 ASP Response.Redirect 跳转</h3>
        <div class="form-box">
            <form method="get" action="/test.asp">
                <label for="redirect">跳转目标URL：</label>
                <input type="text" id="redirect" name="redirect" placeholder="例：https://termux.dev 或 /index.html">
                <button type="submit">立即跳转</button>
            </form>
        </div>

        <p>✅ 已实现仿 ASP 核心功能：<br>
        Request.QueryString / Request.Form / Response.Redirect / 循环表格 / 条件样式</p>
    </div>
</body>
</html>
"""
print(html_end)
```

赋予权限
```bash
chmod +x $PREFIX/share/apache2/default-site/htdocs/test.asp
```

*启动 apache*
```bash
apachectl start
```

apache 命令
```bash
apachectl start
```
```bash
apachectl stop
```
```bash
apachectl restart
```

*最后：*
- [x] 要加 ExecGGI
- [x] 要赋予权限
- [x] 重启 apache       

