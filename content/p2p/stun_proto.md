> `NAT会话遍历实用程序(STUN)`是一种服务于作为其他协议处理`NAT`穿透的工具 | 可以使用它来确定分配的`IP`地址和端口并通过`NAT`访问它 | 它还可以用于检查两个端点之间的连通性 | `STUN`与许多现有的`NAT`一起工作 | 并作为保持`NAT`绑定活动的协议.   
`STUN`本身并不是一个`NAT`穿越解决方案 | 而是作为一种工具在`NAT`穿透解决方案的上下文中使用.  

`STUN`协议可以使用`TCP`或者`UDP`传输，但一般情况下使用`UDP` | 一个完整的`STUN`消息由一个消息头和多个属性组成.

```urlpreview
https://www.rfc-editor.org/rfc/rfc8489
```

---

### STUN消息头

```text
            0                   1                   2                   3
            0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
           +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
           |0 0|     STUN Message Type     |         Message Length        |
           +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
           |                         Magic Cookie                          |
           +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
           |                                                               |
           |                     Transaction ID (96 bits)                  |
           |                                                               |
           +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

| 字段           | 长度 | 说明                   |
|----------------|-----|------------------------|
| type           | 2   | 消息类型                |
| len            | 2   | 消息长度                |
| cookie         | 4   | 固定值: `0x2112A442u32` |
| transaction id | 12  | 交易号                  |


#### 消息类型

| 类型 | 值 |
|------|----|
| BindingRequest | `0x0001` |
| BindingResponse | `0x0101` |
| BindingError | `0x0111` |
| AllocateRequest | `0x0003` |
| AllocateResponse | `0x0103` |
| AllocateError | `0x0113` |
| CreatePermissionRequest | `0x0008` |
| CreatePermissionResponse | `0x0108` |
| CreatePermissionError | `0x0118` |
| ChannelBindRequest | `0x0009` |
| ChannelBindResponse | `0x0109` |
| ChannelBindError | `0x0119` |
| RefreshRequest | `0x0004` |
| RefreshResponse | `0x0104` |
| RefreshError | `0x0114` |
| SendIndication | `0x0016` |
| DataIndication | `0x0017` |


#### 交易号

`STUN`消息中的交易号是客户端生成的随机数据，服务端返回`STUN`响应的时候需要关联请求交易号.

---

### STUN消息属性

```text
            0                   1                   2                   3
            0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
           +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
           |         Type                  |            Length             |
           +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
           |                         Value (variable)                ....
           +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

| 字段           | 长度 | 说明    |
|----------------|-----|---------|
| type           | 2   | 属性类型 |
| len            | 2   | 内容长度 |
| value          |     | 属性内容 |

> 属性内容为了对齐会存在填充位，长度为`4`的倍数，如果不足则填充`0`，如：内容长度为`5`，则`len`为`5`，但`value`的长度为`8`， 填充`3`个`0`， 读取`value`的时候需要排除掉填充位.


#### 属性类型

| 类型 | 值 |
|------|----|
| UserName | `0x0006` |
| Data | `0x0013` |
| Realm | `0x0014` |
| Nonce | `0x0015` |
| XorPeerAddress | `0x0012` |
| XorRelayedAddress | `0x0016` |
| XorMappedAddress | `0x0020` |
| MappedAddress | `0x0001` |
| ResponseOrigin | `0x802B` |
| Software | `0x8022` |
| MessageIntegrity | `0x0008` |
| ErrorCode | `0x0009` |
| Lifetime | `0x000D` |
| ReqeestedTransport | `0x0019` |
| Fingerprint | `0x8028` |
| ChannelNumber | `0x000C` |
| IceControlled | `0x8029` |
| Priority | `0x0024` |
| UseCandidate | `0x0025` |
| IceControlling | `0x802A` |


#### UserName
> 这个属性在确保消息一致性时使用，这个属性包含一个可变长度的字符串，字符串编码必须为`UTF-8`，对于最大长度有限制，最大长度为`508`.

#### Date
> 在`DataIndication`消息中使用，这个属性的值是可变长度的.

#### Realm
> 可以出现在请求或者响应中，但一般在确保消息一致性的时候使用，字符串编码必须为`UTF-8`，最大长度为`127`.

#### Nonce
> 一段随机字符串，可以出现在请求或者响应中，字符串编码必须为`UTF-8`，最大长度为`127`，对于一个对端`Nonce`值是唯一并且持久的，这里可以理解为对端的标识，`Nonce`存在过期时间，过期之后需要重新刷新随机字符串，默认过期时间为`3600`秒.

#### Software
> 包含自身程序的文本描述，这个值对于协议没有任何影响，一般情况下包含制造商和版本，用于参考信息.

#### MessageIntegrity
> 消息完整性检查使用，包含一个`HMAC-SHA1`值，对于短期凭证和长期凭证有不同的计算方法.  
这里以长期认证举例: `HMAC-SHA1(username + password + realm)`

#### XorPeerAddress
> 指定对等方的地址和端口. `(XOR)`

#### XorRelayedAddress
> 服务器分配的地址和端口. `(XOR)`

#### XorMappedAddress
> 与`MappedAddress`相同. `(XOR)`

#### MappedAddress
> 客户端的自反地址. `(客户端NAT最外层地址)`

#### ResponseOrigin
> 服务器的对外地址. `(用来处理双重NAT)`

#### ErrorCode
> 用于错误消息响应.

```text
           0                   1                   2                   3
           0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
          +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
          |           Reserved, should be 0         |Class|     Number    |
          +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
          |      Reason Phrase (variable)                                ..
          +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

| 字段           | 长度 | 说明    |
|----------------|-----|---------|
| code           | 2   | 错误代码 |
| message        |     | 错误消息 |

| 错误码 | 值 |
|------|----|
| TryAlternate | `0x0300` |
| BadRequest | `0x0400` |
| Unauthorized | `0x0401` |
| Forbidden | `0x0403` |
| RequestTimedout | `0x0408` |
| UnknownAttribute | `0x0414` |
| AllocationMismatch | `0x0425` |
| StaleNonce | `0x0426` |
| AddressFamilyNotSupported | `0x0428` |
| WrongCredentials | `0x0429` |
| UnsupportedTransportAddress | `0x042A` |
| AllocationQuotaReached | `0x0456` |
| ServerError | `0x0500` |
| InsufficientCapacity | `0x0508` |

#### Lifetime
> 用于指示和刷新生命周期.

#### ReqeestedTransport
> 客户端请求使用的传输协议.

```text
           0                   1                   2                   3
           0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
          +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
          |    Protocol   |                    RFFU                       |
          +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

| 协议     | 值   |
|----------|------|
| UDP      | 0x01 |
| TCP      | 0x02 |

#### Fingerprint
> `STUN`消息摘要，这个属性必须是属性列表的最后一个属性，通过对整个消息经常`CRC`计算得到.

```callout, theme: grey
在计算`Fingerprint`之前，STUN消息的长度字段必须包含`Fingerprint`属性的长度，但并不用真正包含`Fingerprint`字段.
```

#### ChannelNumber
> 在绑定和发送`channel`消息时使用，内部包含一个通道号，这个属性的长度是`4`个字节.

```text
           0                   1                   2                   3
           0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
          +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
          |        Channel Number         |         RFFU = 0              |
          +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

---

#### #Address

```text
           0                   1                   2                   3
           0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
          +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
          |0 0 0 0 0 0 0 0|    Family     |           Port                |
          +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
          |                                                               |
          |                 Address (32 bits or 128 bits)                 |
          |                                                               |
          +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

| 字段           | 长度    | 说明      |
|----------------|--------|-----------|
| family         | 2      | ip family |
| port           | 2      | 端口       |
| address        | 4 / 16 | 地址       |

| IP Family | 值   |
|-----------|------|
| IPv4      | 0x01 |
| IPv6      | 0x02 |


#### #XOR Address

```text
            0                   1                   2                   3
            0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
           +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
           |0 0 0 0 0 0 0 0|    Family     |         X-Port                |
           +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
           |                X-Address (Variable)
           +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

```callout, theme: grey
`Family`字段代表IP地址族，其编码与`MAPPED-ADDRESS`中的`Family`字段相同.      

`X-Port`是通过对映射端口与`magic cookie`的最高`16`位进行异或计算得出的，如果是`IPv4`，则`X-Address`是通过对映射的`IP`地址与`magic cookie`进行异或计算得出的，如果`IP`是`IPv6`，则`X-Address`是通过对映射的`IP`地址与`magic cookie`和`96`位`transaction id`串联进行异或运算来计算的，在所有情况下，`XOR`操作都以网络字节顺序（即它们将在消息中编码的顺序）对其输入进行操作.

编码和处理属性值前`8`位的规则、处理属性多次出现的规则、处理地址族的规则与`MAPPED-ADDRESS`相同.
```

---

### 实现

```urlpreview
https://github.com/colourful-rtc/turn-rs
```

这是一个纯`Rust`实现的`STUN/TURN`服务器，内部包含`STUN`消息编解码器和`TURN`会话处理库.