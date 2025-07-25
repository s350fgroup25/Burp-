**Web cache deception attacks**
- goal : 存在一個快取規則來儲存以.js結尾的請求的回應
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
 
**Exploiting path mapping**
 Lab : https://0a86005804fbe54880ed3f7400d6000d.web-security-academy.net/
Request:
- change /my-account/ to /my-account/abc.js
- add X-Cache: miss 
- add Cache-Control: max-age=30 
Response:
- X-Cache chnage to hit -- > X-Cache: hit

=> Create 惡意 website (html)
- <script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account/wcd.js"</script>
- After others login , we can access this website , get /my-account/wcd.js to get key

Reference : 
- X-Cache: miss : 此回應不是從快取提供
- X-Cache: hit : 該請求來自快取
- Cache-Control: max-age=30 : 如果回應已被緩存，則應將其儲存 30 秒

**Exploiting delimiter discrepancies**
- 分隔符號 URL  [?]
- 分隔符號 Java Spring [;]
- 分隔符號 Ruby on Rails [.]
- 分隔符號 OpenLiteSpeed  [%00]
	- 編碼字元 [%00]
	- 如果快取使用 Akamai 或 Fastly，它會將%00和之後的所有內容解釋為路徑
 - 使用分隔符號差異，為快取伺服器（而非來源伺服器）檢視的路徑新增靜態副檔名

1. 在目標終端節點的 URL 中新增任意字串 :
   => /settings/users/list --> /settings/users/listaaa
2. 添加一個可能的分隔符
   =>/settings/users/list;aaa
   => 如果回應與基本回應相同，則表示該;字元用作分隔符
   => 如果它將對路徑的回應與任意字串匹配，則表示該; 字元不用作分隔符

Lab : 
























   
