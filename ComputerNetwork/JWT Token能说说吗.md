JWT（JSON Web Token）是一种基于 JSON 的开放标准（RFC 7519），用于在网络应用间安全地传递信息。它常用于身份验证（Authentication）和信息交换（Data Exchange），尤其适合分布式系统或跨域场景。以下是关键点解析：

---

### **1. JWT 的核心结构**
JWT 由三部分组成，通过 `.` 分隔，形式为 `xxxxx.yyyyy.zzzzz`：
- **Header（头部）**  
  包含令牌类型（`JWT`）和签名算法（如 `HS256`、`RS256`）。  
  示例：`{"alg": "HS256", "typ": "JWT"}` → Base64Url 编码。

- **Payload（负载）**  
  存储实际数据（称为“声明”），如用户 ID、权限、过期时间等。  
  分为三种声明：  
  - **Registered claims**（预定义字段，如 `iss`（签发者）、`exp`（过期时间））。  
  - **Public claims**（自定义字段，需防冲突）。  
  - **Private claims**（双方约定的私有字段）。  
    示例：`{"user_id": 123, "exp": 1735689600}` → Base64Url 编码。

- **Signature（签名）**  
  通过 Header 指定的算法，对 `Header.Payload` 和密钥（Secret Key）生成签名，用于验证令牌完整性。  
  公式：`HMACSHA256(Header + "." + Payload, Secret Key)`。

---

### **2. 工作流程**
1. **用户登录**：客户端提交用户名/密码到服务器。  
2. **生成 JWT**：服务器验证通过后，生成 JWT 并返回给客户端。  
3. **存储 JWT**：客户端通常将 JWT 存在 `localStorage`、Cookie 或内存中。  
4. **请求携带 JWT**：客户端在后续请求的 `Authorization` 头中附加 `Bearer <JWT>`。  
5. **验证 JWT**：服务器通过密钥验证签名，检查有效期、权限等，若有效则处理请求。

---

### **3. 优缺点**
**优点**：  
- **无状态**：服务器无需存储会话信息，适合分布式系统。  
- **跨域友好**：可通过 HTTP 头传递，天然支持 CORS。  
- **可扩展**：Payload 可自定义字段，灵活传递用户信息。  
- **标准化**：广泛支持，多种语言有成熟库（如 `jsonwebtoken`、`PyJWT`）。

**缺点**：  
- **令牌无法即时失效**：一旦签发，在过期前始终有效（需配合黑名单或短有效期缓解）。  
- **潜在安全风险**：若存储不当（如存入 Cookie 未设 `HttpOnly`），可能被 XSS 攻击窃取。  
- **Payload 不加密**：默认仅 Base64Url 编码，敏感数据需额外加密（如使用 JWE）。

---

### **4. 示例代码（Node.js）**
```javascript
// 生成 JWT
const jwt = require('jsonwebtoken');
const token = jwt.sign({ userId: 123 }, 'secret_key', { expiresIn: '1h' });
console.log(token); // 输出类似 xxxxx.yyyyy.zzzzz

// 验证 JWT
jwt.verify(token, 'secret_key', (err, decoded) => {
  if (err) return console.error('无效的 Token');
  console.log(decoded.userId); // 输出 123
});
```

---

### **5. 适用场景**
- **API 认证**：移动端、单页应用（SPA）调用后端接口。  
- **单点登录（SSO）**：跨子域共享身份验证状态。  
- **临时权限授权**：如生成有时限的文件下载链接。  
- **微服务间通信**：服务间传递经过验证的用户信息。

---

### **6. 安全建议**
- **使用 HTTPS**：防止令牌在传输中被窃听。  
- **缩短有效期**：搭配 Refresh Token 实现长效刷新机制。  
- **避免敏感数据**：不要在 Payload 中存储密码等敏感信息。  
- **防御 XSS/CSRF**：通过 `HttpOnly` Cookie 存储 Token，或使用 SameSite 属性。

---

若有具体使用场景或问题（如如何刷新 Token、与 OAuth 的区别），可进一步探讨！