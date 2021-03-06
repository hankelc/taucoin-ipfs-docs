# Block
 No              |  Key           | Size-Byte        |  Notes
 ----------------|----------------|------------------|----------------------
 1   |version        | 1          |  "0x1"
 2   |option         | 1          |  "0x1"
 3   |chainid        | 32         |  "0x0"
 4   |blockno        | 8          |  "0x1"
 5   |basetarget     | 8          |  used for POT calculation
 6   |cumulativedifficulty    | 8       | fork judgement
 7   |generationsignature     | 32      | used for POT calculation
 8   |tforger      | 20       | block forger address in TAU system, for IPLD index and display
 9   |iforger      | 46       | block forger address in IPFS system, for forging connection
 10  |relayma      | 72       | IPFS TAU relay nodes in multiaddress format
 11  |timestamp    | 4        | unix timestamp
 12  |previoushash | 32       | necessary in blockchain
 13  |stateroot    | 32       | root cid hash of state database
 14  |txroot       | 32       | TXsCID, IPLD format to index transactions
 15  |signature    | 65       | r: 32 bytes, s: 32 bytes, v: 1 byte

9, 10为可选字段。

# Transaction
 No              |  Key           | Size-Byte        |  Notes
 ----------------|----------------|------------------|----------------------
1   | version       | 1        |  "0x1"
2   | option        | 1        |  "0x1"
3   | chainid       | 32       |  "0x0"
4   | blockhash     | 32       |  "0x0"
5   | nounce        | 8        |  "0x1"
6   | timestamp     | 4        | tx timestamp
7   | tsender       | 20       | tx sender address in TAU system, for IPLD index and display
8   | isender       | 46       | tx sender address in IPFS system, for locating tx file in IPFS
9   | relayma       | 72       | relay multiaddress in IPFS system, for adding relay nodes
10  | receiver      | 20       | tx receiver
11  | amount        | 5        | transfer amount
12  | fee           | 1        | transaction fee
13  | signature     | 65       | r: 32 bytes, s: 32 bytes, v: 1 byte

4, 8, 9为可选字段。

# Modified Notes
## Block
### 20191118
- 考虑IPLD和区块数据信息统一以及效率问题，设计字段都有效保留；
- nodeid改名为bootstrapid
- 考虑多链结构下的区块信息流，加入chainid-genesis transaction hash字段以做区分；
---
### 20191125
- 考虑非矿工用户，只关心内容，进而减少其同步数据的流量；
- 根据上述需求，加入两个区分交易类型的merkle root：mmerkleroot, cmerkleroot;  
   - mmerkleroot: 信息交易(New chain transaction, persnonal info transaction, new message transaction)的MR
   - cmerkleroot: 代币转账交易的MR
---
### 20191126
- 区块中的交易merkle root，改为单层交易root, ![如图](https://github.com/Tau-Coin/taucoin-ipfs-docs/blob/master/imgfile/txrootcid.jpg)
- Merkle root对于本应用而言，无优势，单层root结构可以无缝对接IPLD数据格式，效率也高；
---
### 20191204
- 考虑以太坊的设计，实现快速同步账户状态数据，增加stateroot字段；
```
stateroot是Merkle Patricia Trie的根哈希.
```
- MPT树的Value记录了账户状态(余额，Power-Nounce, 信息交易)，根据MPT来获取账户信息交易；
- 信息交易的获取根源从stateroot出发，因而将mtxroot和ctxroot合并为txroot；


##### 问题讨论：
```
Shared MPT的实现方案？
```

### 20200213
- 考虑IPFS效率问题，block header中加入MultiAddress信息：IP Info+ NodeId Info
- 交易中在以后版本中考虑中继节点信息

---
### 20200214
- 经过讨论，IPFS 可以利用dnt findpeer找到NodeId Info对应的IP Info, 只保留NodeId。

---
### 20200218
- 还原之前的讨论内容，block header中加入中继节点信息，该信息以MultiAddress形式加入。[详见说明](https://github.com/Tau-Coin/taucoin-ipfs-docs/blob/master/doc/p2p-network-relay-solution.md)

---
### 20200221
- block header中加入矿工节点信息，该信息仅表现为IPFS cid形式。[详见说明](https://github.com/Tau-Coin/taucoin-ipfs-docs/blob/master/doc/Tau-address-instructions.md)


## Transaction
### 20191118
- 考虑forum应用以及交易过期问题，去除expiretime字段；
- 考虑IPLD和区块数据信息统一以及效率问题，其他设计字段有效保留；
- 考虑多链结构下的交易信息流，加入chainid-genesis transaction hash字段以做区分；  
---
### 20191125
- New name transaction中加入profile字段，扩展为个人简介交易；
- new chain, message transaction中的abstract字段名修改为title；
- 信息类型由纯文字类型扩展到文字、图片、链接、视频四种类型，信息类型的由IPFS Cid区分；

##### 问题讨论：
```
IPFS Cid区分信息类型的方案：可以类似与CID version实现，message type+ cidv0
```
- message type:
  - 0 -> 纯文字
  - 1 -> 包含链接
  - 2 -> 包含图片
  - 3 -> 包含视频
  - ...
---
### 20200205
- new chain, message transaction中的title字段名修改为intro；
- intro由三部分組成：title+ 內容摘要(前144個bytes)+ IPFS Cid hash(图片hash, voice Hash等)
- IPFS Cid区分信息类型的方案暂定为：message type+ cidv0
- message type:
  - 0 -> 纯文字
  - 1 -> 包含链接
  - 2 -> 包含图片
  - 3 -> 包含视频
  - ...
---
### 20200214
- 交易中加入主链BlockHash, 以达到'TaPOS'。 [TaPOS说明](https://github.com/Tau-Coin/taucoin-ipfs-docs/blob/master/doc/TaPoS.md)

---
### 20200218
- 上链的CID对应的文件大小，矿工要做一次验证以防攻击. [CID对应文件说明](https://github.com/Tau-Coin/taucoin-ipfs-docs/blob/master/doc/Taucoin-ipfs-cid-instructions-cn.md)

---
### 20200221
- 交易中加入可选字段：Sender节点信息，该信息仅表现为IPFS cid形式。
- 交易中加入可选字段：中继节点信息，该信息以MultiAddress的形式加入，为可选字段。
- 上述两个可选字段的加入，[详见说明](https://github.com/Tau-Coin/taucoin-ipfs-docs/blob/master/doc/Tau-address-instructions.md)。

---

### 20200226
- 账户信息中只存在balance, nounce<=>power.
- 在现有的框架下，可以适应于多链系统，Shared MPT不再适用。
---

#### 多链应用下的交易类型，目前草稿：

### General info
 No              |  Key           | Size-Byte        |  Notes
 -------------------|----------------|------------------|----------------------
1   | version           | 1        | "0x1"
2   | option            | 1        | "0x1"
3   | chainid           | 32       | "0x0"
4   | blockhash         | 32       | "0x0"
5   | nounce            | 8        | "0x1"
6   | timestamp         | 4        | tx timestamp
7   | tsender           | 20       | tx sender address in TAU system, for IPLD index and display
8   | isender           | 46       | tx sender address in IPFS system, for locating tx file in IPFS
9   | relayma           | 72       | relay multiaddress in IPFS system, for adding relay nodes
10  | fee               | 1        | transaction fee
11  | signature         | 65       | r: 32 bytes, s: 32 bytes, v: 1 byte

4, 8, 9为可选字段。

### New chain transaction
 No              |  Key           | Size-Byte        |  Notes
 ----------------|----------------|------------------|----------------------
12  | name             | 20       | 论坛版块名字
13  | contact          | 32       | 版块管理员联系方式，暂定为telegram id
14  | intro            | 256      | 论坛版块的标题，内容摘要，抽象hash等
15  | description      | 33       | 论坛版块的描述细节，message type+ IPFS Cid形式存在


### Personal info transaction
 No              |  Key           | Size-Byte        |  Notes
 ----------------------|----------------|------------------|----------------------
12  | contactname      | 32         | 论坛板块中可用于直接联系的方式，暂定为telegram id
13  | name             | 20         | 论坛板块中的昵称
14  | profile          | 32         | 论坛板块中的个人资料，以IPFS Cid形式存在；


### New message transaction
 No              |  Key           | Size-Byte        |  Notes
 ----------------|----------------|------------------|----------------------
12  | referid      | 32           | 被回复帖子的交易哈希
13  | intro        | 256          | 回复贴的标题，内容摘要，抽象hash等
14  | content      | 33           | 回复贴的具体内容，message type+ IPFS Cid形式存在
