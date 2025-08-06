# Authentication (身份驗證)
- Knowledge factor (know) : 密碼或安全問題的答案
- Possession factors (have) :手機或安全令牌
- Inherence factors (are) : 生物特徵或行為模式
- Authentication != Authorization (授權)

## 漏洞
- 無法充分防禦暴力攻擊 brute-force
- 身份驗證機制被攻擊者完全繞過 (bypass)
- 攻破高權限帳戶（例如係統管理員帳戶）
- 獲得內部基礎設施的存取權限
- 敏感資訊

# Vulnerabilities in password-based login
- 攻擊者只要知道秘密密碼，就足以證明使用者的身分
### Brute-force attacks 
- 使用反覆試驗的系統來猜測有效的使用者憑證
- 完全隨機的猜測
- 破解難度取決於密碼的強度
### Username enumeration
- 當您輸入有效的使用者名稱但密碼錯誤時
- 註冊表單上輸入已被佔用的使用者名稱時
- 攻擊者能夠快速產生有效使用者名稱的候選清單
### Pay attention
- Status code ; 200 / 443
- Error messages : username AND password are incorrect 
- Response times : 任何偏離此時間的請求都表示後台發生了一些異常

##  Username enumeration
### Lab: Username enumeration via different responses
- Proxy : POST /login
- Intruderd : username=§invalid-username§
  - Payloads :  Simple list
  - Length :
    - Invalid username
    - Incorrect password
- Intruderd :username=identified-user&password=§invalid-password§

### Lab: Username enumeration via subtly different responses
- Invalid username or password.
- username=identified-user&password=§invalid-password§

### Lab: Username enumeration via response timing

## Flawed brute-force protection
- 如果登入失敗次數過多，您的 IP 就會被封鎖
- 如果 IP 擁有者成功登錄，失敗嘗試次數的計數器就會重置
- 只需在整個單字表中定期包含您自己的登入憑證就足以使這種防禦幾乎毫無用處

### Lab: Broken brute-force protection, IP block

## Account locking
- 伺服器發出的指示帳戶已被鎖定的回應也可能幫助攻擊者列舉使用者名稱
- Bypass
  - 建立一個可能有效的候選用戶名清單
  - 確定一個你認為至少有一位使用者可能使用的密碼清單。
  - 使用 Burp Intruder 等工具，將每個選定的密碼與每個候選使用者名稱進行嘗試。
  - 字典攻擊 username:password
### Lab: Username enumeration via account lock

## User rate limiting




## Vulnerabilities in multi-factor authentication
### 
## Vulnerabilities in other authentication mechanisms
### 
## Preventing attacks on your own authentication mechanisms
