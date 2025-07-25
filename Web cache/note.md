**Web cache deception attacks**
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

/* 
- X-Cache: miss : 此回應不是從快取提供
- X-Cache: hit : 該請求來自快取
- Cache-Control: max-age=30 : 如果回應已被緩存，則應將其儲存 30 秒
*/
