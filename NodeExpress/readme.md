NodeJS Express 架設至IIS上 筆記
===

## 步驟一
檢查IIS管理員 是否有沒有開啟 ASP.NET等功能
如下：
控制台 -> 程式集 -> 程式和功能 -> 開啟或關閉 Windows 功能 ->Internet Informationternet Information Service -> 全球資訊網服務 -> 應用程式開發功能

將下面的功能...ASP,.NET...等等..都打勾安裝...

## 步驟二
[安裝](https://codemirror.net/demo/sublime.html)  IISNode 

選擇正確的版本（64位或32位）下載文件後，安裝它！

測試IISNode

測試正確安裝的IISNode，可以使用setupsamples.bat腳本創建IISNode實例。
cmd->cd C:/Program Files/iisnode -> setupsamples.bat

現在，只要您運行localhost，您就可以在瀏覽器中轉到“ localhost/node”。

## 步驟三
[安裝](https://www.iis.net/downloads/microsoft/url-rewrite) URL Rewriting

## 步驟四
IIS管理員 

1.新增應用程式集區
(一)名稱自訂：iisnode  
(二)版本：.NET Framework v4以上
(三)模式：整合式

2.新增網站
(一)站台名稱自訂：FundMobile
(二)應用程式集區：iisnode
(三)實體路徑：自選
(四)繫結：自訂

3.處理常式對應
(一)檢查處理常式對應內是否有iisnode
(二)沒有 則新增一個 (路徑：*.js 模組：iisnode)

## 步驟五
建立server.js文件

```javascript=
const express = require('express')
const next = require('next')
const { parse } = require('url')
const dev = process.env.NODE_ENV !== 'production'
const app = next({ dev })
const handle = app.getRequestHandler()
const port = process.env.PORT || 3000
app.prepare()
.then(() => {
  const server = express()

  server.get('*', (req, res) => {
    const parsedUrl = parse(req.url, true)
    return handle(req, res , parsedUrl)
  })

  server.listen(port, (err) => {
    if (err) throw err
    console.log('> Ready on http://localhost:3000')
  })
})
.catch((ex) => {
  process.exit(1)
})
```

## 步驟六
建立web.config
```XML=
<configuration>
    <system.webServer>
        <handlers>
            <add name="iisnode" path="*.js" verb="*" modules="iisnode" resourceType="Unspecified" requireAccess="Script" />
        </handlers>

        <rewrite>
            <rules>
                <rule name="all">
                    <match url="/*" />
                    <conditions logicalGrouping="MatchAll" trackAllCaptures="false" />
                    <action type="Rewrite" url="server.js" />
                </rule>				
            </rules>
        </rewrite>

        <iisnode promoteServerVars="REMOTE_ADDR" watchedFiles="*.js;node_modules\*;routes\*.js;views\*.jade" nodeProcessCommandLine="C:\Program Files\nodejs\node.exe" devErrorsEnabled="true" /> 
　　</system.webServer> 
</configuration>
```

## 步驟七  

完成～～～～～