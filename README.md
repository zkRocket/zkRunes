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


## 操作流程
### Deploy
<img width="822" height="640" alt="image" src="https://github.com/user-attachments/assets/d7ad01cc-fc16-40fa-a94c-39f194b33f3a" />

### Mint

<img width="508" height="698" alt="image" src="https://github.com/user-attachments/assets/c501a224-517d-4f18-aa68-fcc2b52f40b0" />

### Transfer
<img width="398" height="682" alt="image" src="https://github.com/user-attachments/assets/016a6262-92ba-4ed8-94f2-09bf338a3214" />


### Burn



