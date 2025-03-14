### TCP 三次握手（Three-Way Handshake）

TCP（三次握手）是 TCP 协议在建立连接时采用的机制，以确保连接的可靠性。三次握手的主要目的是 **确认双方的发送和接收能力，防止重复连接**。以下是具体过程：

#### **第一步：客户端发送 SYN**

- **客户端**（Client）向 **服务器**（Server）发送 **SYN（synchronize）** 标志的数据包，表示请求建立连接。
- 该数据包包含一个随机 **初始序列号（Sequence Number, seq=x）**，用于标识后续数据包的顺序。
- **状态变化：** 客户端 → **SYN_SENT（同步已发送）**。

#### **第二步：服务器回复 SYN-ACK**

- **服务器** 收到 SYN 包后，回复 **SYN-ACK（synchronize-acknowledge）** 数据包，表示同意建立连接。
- 服务器发送： 
  - **SYN = 1**（表示同步请求）
  - **ACK = 1**（确认收到客户端的 SYN）
  - **确认号（ACK Number）= x+1**（表示已收到 x）
  - **服务器自己的初始序列号（seq=y）**。
- **状态变化：** 服务器 → **SYN_RECEIVED（同步已接收）**。

#### **第三步：客户端发送 ACK**

- 客户端

   收到 SYN-ACK 后，回复一个 

  ACK（Acknowledgment）

   确认数据包： 

  - **ACK = 1**
  - **ACK Number = y+1**（确认收到 y）
  - **Sequence Number = x+1**。

- 服务器收到该 ACK 后，TCP 连接正式建立。

- 状态变化：

  - 客户端 → **ESTABLISHED（已建立）**。
  - 服务器 → **ESTABLISHED（已建立）**。

------

### **为什么需要三次握手？**

三次握手的主要作用是防止 **历史连接请求（重复的 SYN 包）** 影响新的连接，确保双方的发送与接收能力正常。

如果只用 **两次握手**，可能会出现：

- 旧的 SYN 请求因网络延迟到达服务器，服务器回应 SYN-ACK，但客户端已不需要该连接，造成 **资源浪费**。

三次握手可以确保：

1. **客户端确认** 服务器收到了它的 SYN。
2. **服务器确认** 客户端收到了它的 SYN-ACK。
3. **客户端再次确认** 服务器收到 ACK，连接才算真正建立。

------

### **TCP 三次握手的示意图**

```plaintext
Client → SYN (seq=x) → Server
Client ← SYN-ACK (seq=y, ack=x+1) ← Server
Client → ACK (seq=x+1, ack=y+1) → Server
```

连接建立后，双方就可以正式开始数据传输了。

------

### **总结**

TCP 三次握手的过程：

1. **客户端发送 SYN** 请求连接，包含随机序列号 `x`。
2. **服务器回应 SYN-ACK**，包含自己的随机序列号 `y`，并确认 `x+1`。
3. **客户端发送 ACK**，确认 `y+1`，连接建立。

这样，TCP 连接就能可靠地建立，确保双方都具备通信能力！ 🚀