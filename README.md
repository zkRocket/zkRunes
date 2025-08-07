# zkRunes

## 概述

`ZKRunes`是`ZenKeep` 协议的应用合约。 该应用允许用户在BTC上执行Runes操作，使用Eth 作为定序器，解决BTC Runes 中定序器中心化的问题。

`ZKRunes` 根据字节流 `appData` 解码出具体的操作指令，完成：

- 部署新 Token（Deploy）
- 铸造 Token（Mint）
- 转账 Token（Transfer）
- 销毁 Token（Burn）

支持的 Token 类型包括：

- ERC20Capped
- ERC721Capped
- 用户自定义合约地址的Token，`ZKRunes` 要有权限执操作。

---

## 操作类型说明

| 操作名称 | 操作代码 (operation) |
|----------|-----------------------|
| Deploy   | 1                     |
| Mint     | 2                     |
| Transfer | 3                     |
| Burn     | 4                     |

---

## Token 类型说明

| 类型名称           | 类型代码 (type) |
|--------------------|------------------|
| ERC20Capped        | 1                |
| ERC721Capped       | 2                |
| 用户自定义地址 Token | 3              |

---



## 操作格式(编码)说明

```
fields:  operation  type  operation_specific_data
len:       1           1           (见下面)
```


### ✅ Deploy

#### ▶ ERC20Capped

| 字段名           | 说明           | 长度（bytes） |
|------------------|----------------|----------------|
| operation        | 操作码（1）    | 1              |
| type             | 类型码（1）    | 1              |
| name             | Token 名称     | 8              |
| symbol           | Token 符号     | 4              |
| totalSupply      | 总发行量       | 16             |
| decimals         | 精度           | 1              |
| maxInEachMint    | 单次最大 Mint  | 1              |

#### ▶ ERC721Capped

| 字段名           | 说明           | 长度（bytes） |
|------------------|----------------|----------------|
| operation        | 操作码（1）    | 1              |
| type             | 类型码（2）    | 1              |
| name             | Token 名称     | 8              |
| symbol           | Token 符号     | 4              |
| totalSupply      | 总发行量       | 16             |
| maxInEachMint    | 单次最大 Mint  | 1              |
| urlLength        | url的长度       | 2             |
| url         | url        | url的长度             |

#### ▶ 用户自定义地址 Token

| 字段名           | 说明             | 长度（bytes） |
|------------------|------------------|----------------|
| operation        | 操作码（1）      | 1              |
| type             | 类型码（3）      | 1              |
| tokenAddress     | 合约地址         | 20             |

---

### ✅ Mint

#### ▶ ERC20Capped

| 字段名           | 说明             | 长度（bytes） |
|------------------|------------------|----------------|
| operation        | 操作码（2）      | 1              |
| type             | 类型码（1）      | 1              |
| name             | Token 名称       | 8             |
| to               | 目标地址         | 20             |
| amount           | Mint 数量        | 1             |

#### ▶ ERC721Capped

| 字段名           | 说明             | 长度（bytes） |
|------------------|------------------|----------------|
| operation        | 操作码（2）      | 1              |
| type             | 类型码（2）      | 1              |
| name             | Token 名称       | 8              |
| to               | 目标地址         | 20             |
| amount           | Mint 数量        | 1              |

#### ▶ 用户自定义地址 Token

| 字段名           | 说明             | 长度（bytes） |
|------------------|------------------|----------------|
| operation        | 操作码（2）      | 1              |
| type             | 类型码（3）      | 1              |
| tokenAddress     | 合约地址         | 20             |
| userSpecifiedData | 用户数据        | xx            |


---

### ✅ Transfer

#### ▶ ERC20Capped

| 字段名           | 说明             | 长度（bytes） |
|------------------|------------------|----------------|
| operation        | 操作码（3）      | 1              |
| type             | 类型码（1）      | 1              |
| name             | Token 名称       | 8             |
| to               | 目标地址         | 20             |
| amount           | 转账数量         | 32             |
| signature        | 签名             | 64             |

#### ▶ ERC721Capped

| 字段名           | 说明             | 长度（bytes） |
|------------------|------------------|----------------|
| operation        | 操作码（3）      | 1              |
| type             | 类型码（2）      | 1              |
| name             | Token 名称       | 8              |
| to               | 目标地址         | 20             |
| tokenId          | Token ID         | 32             |
| signature        | 签名             | 64             |

#### ▶ 用户自定义地址 Token

| 字段名           | 说明             | 长度（bytes） |
|------------------|------------------|----------------|
| operation        | 操作码（3）      | 1              |
| type             | 类型码（3）      | 1              |
| tokenAddress     | 合约地址         | 20             |
| userSpecifiedData | 用户数据        | xx             |

---

### ✅ Burn

#### ▶ ERC20Capped

| 字段名           | 说明             | 长度（bytes） |
|------------------|------------------|----------------|
| operation        | 操作码（4）      | 1              |
| type             | 类型码（1）      | 1              |
| name             | Token 名称       | 8              |
| amount           | 销毁数量         | 32             |
| signature        | 签名             | 64             |

#### ▶ ERC721Capped

| 字段名           | 说明             | 长度（bytes） |
|------------------|------------------|----------------|
| operation        | 操作码（4）      | 1              |
| type             | 类型码（2）      | 1              |
| name             | Token 名称       | 8              |
| tokenId          | Token ID         | 32             |
| signature        | 签名             | 64             |

#### ▶ 用户自定义地址 Token

| 字段名           | 说明             | 长度（bytes） |
|------------------|------------------|----------------|
| operation        | 操作码（4）      | 1              |
| type             | 类型码（3）      | 1              |
| tokenAddress     | 合约地址         | 20             |
| userSpecifiedData | 用户数据        | xx             |

```sh
/* appData
fields:               appData
length(bytes):         operation    type
                        1 byte     1 byte
operation:
1- Deploy
2- Mint
3- Transfer
4- Burn

type:
1 - ERC20Capped
2 - ERC721Capped
3 - User Specified

deploy a zkBRC20 Capped:
operation    type       len(name)   name     len(symbol)   symbol     decimal   maxInEachMint  totalSupply 
  (1)          (1)       1         xx bytes    1         xx bytes      1 bytes    1 bytes        8 bytes 

deploy a zkBRC721 Capped:
operation    type       len(name)   name     len(symbol)   symbol     totalSupply    len(url)     url
  (1)         (2)           1       xx        1           xx            8              2           xx

deploy a User Specified:
operation    type        tokenAddress
  (1)         (3)          20 bytes


mint:
mint a zkBRC20:
operation     type     name       to      amount
  (2)         (1)     8bytes   20bytes    1bytes


mint a zkBRC721:
operation  type       name       to         amount
  (2)       (2)       8bytes   20bytes      1bytes


mint a User Specified:
operation    type       tokenAddress    user-specified data 
(2)          (3)        20 bytes          xxx bytes


transfer a ERC20:
operation    type     name     nonce   to             amount    signature
  (3)          (1)     8bytes    ??   20 bytes       16 bytes    64 bytes


transfer a ERC721:
operation    type       name   nonce    to            tokenId    signature
  (3)          (2)       8bytes  ??  20 bytes       16 bytes    64 bytes


transfer a User Specified:
operation    type      tokenAddress    user-specified data 
  (3)          (3)        20 bytes         xxx


burn a ERC20:
operation    type     name      amount      signature
  4          (1)     8bytes     16 bytes     64 bytes


burn a ERC721:
operation    type       name      tokenId      signature
  4          (2)       8bytes     16 bytes     64 bytes


burn a User Specified:
operation    type       tokenAddress     user-specified data 
4            (3)         20 bytes           xxx bytes
*/
```

## 操作流程
### Deploy
<img width="704" height="556" alt="image" src="https://github.com/user-attachments/assets/d8d2307b-3deb-4e8d-aab6-2c4772144673" />



### Mint

<img width="508" height="698" alt="image" src="https://github.com/user-attachments/assets/c501a224-517d-4f18-aa68-fcc2b52f40b0" />

### Transfer
<img width="398" height="682" alt="image" src="https://github.com/user-attachments/assets/016a6262-92ba-4ed8-94f2-09bf338a3214" />


### Burn
<img width="423" height="685" alt="image" src="https://github.com/user-attachments/assets/17efb9b5-bd06-4172-93d4-ec9667c7ff60" />




