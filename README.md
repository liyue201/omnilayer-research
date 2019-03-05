### 什么是omnilayer
omnilayer是基于比特币协议之上的一层协议，主要用于发行基于比特币网络的token，类似于以太坊的ERC20。[omnicore](https://github.com/OmniLayer/omnicore)是onmilayer协议的客户端节点，可以看到其代码是从[bitcoin](https://github.com/bitcoin/bitcoin) fork过来的。omnicore并没有自己的主网，与比特币使用同一个网络。omnilayer有自己的区块链浏览器[omniexplorer](https://omniexplorer.info/)。omnilayer上可以发行很多token，通过property_id来唯一标识。

### 什么是USDT
USDT是Tether公司发行的基于omnilayer协议的稳定币，它的property_id是31。

### omnilayer原理
比特币网络之所以能发行token，是因为比特币的交易输出中可以附带一些信息（关于具体细节网上有很多资料，这里省略500字）。omnilayer上的token也包括发行和转账两个功能。发行token其实就是在某个区块高度发起一笔无中生有的转账，然后后面的节点共同承认这笔转账就ok了。后面的普通转账就是从一个地址的余额流转到另一个地址。omnilayer的账本信息不记录在区块上，区块上只有转账记录，账本信息由omnicore客户端维护。当然，这些账本信息是可以从区块上推导和验证的。

### omnilayer 转账
既然omnilayer是基于比特币网络的，那么他的交易数据结构也和比特币一样，也有交易输入和输出。下面是omniexplorer浏览器中一笔USDT的转账（https://omniexplorer.info/search/dd2158cf27bfc8fa18ed93e2a62e82e5d4acfa862bade9468db60381bbb6aebd）

![](https://raw.githubusercontent.com/liyue201/omnilayer-research/master/11111.png)

在比特币浏览器中是这样的

（https://btc.com/dd2158cf27bfc8fa18ed93e2a62e82e5d4acfa862bade9468db60381bbb6aebd)

![](https://raw.githubusercontent.com/liyue201/omnilayer-research/master/2222.png)

![](https://raw.githubusercontent.com/liyue201/omnilayer-research/master/3333.png)

可以看到它有一个输入和三个输出，还付了0.00012800 BTC的矿工费。其中第一个输出在比特币浏览器中解析失败了，它输出脚本的是NULL_DATA类型，OP_RETURN指令后面跟的是一些数据就是omnilayer协议中的转账信息。数据结构是这样的

| 字节数      | 数据        | 描述         |
| ---------- | ----------- | ----------------- |
| 4      | 6f6d6e69 | omni             |
| 2    | 0000      |  版本： 0 |
| 2    | 0000      |    0 =>  交易类型：Simple Send      |
| 4  | 0000001f|  31 => 货币：USDT |
| 8  | 000000018a3052b0 |  	66.1339  => 转账金额 |

第二个输出是找零，第三个输出是转账目标的地址，一般是0.00000546 BTC，但不是绝对的。

### 如何对接
既然omnilayer是比特币基于协议的，那么omnilayer的地址也是比特币的地址。omnicore的命令行客户端叫omnicore-cli，完全支持比特币的命令，同时还扩展了一些命令，[omnicore的JSON RPC](https://github.com/OmniLayer/omnicore/blob/master/src/omnicore/doc/rpc-api.md)也是一样。对于普通转账使用omni_send，如果想指定出矿工费的地址可以使用omni_funded_send。需要注意的是即便指定了付矿工费的地址，发送token的地址还是需要预留一定数量的BTC的。当然这个问题一般无须担心，看到前面的交易了吗，别人给你转账的时候给了多给了0.00000546 BTC，估计这么设计就是为了防止你后面没btc转账吧。矿工费的费率可以在节点的配置文件里面指定。这些RPC接口不仅提供了查询转账等基本功能，你还可以自己组装UTXO构成交易，这种方式更灵活，弊端就是需要多次调用RPC，这里有个教程：[Use the raw transaction API to create a Simple Send transaction](https://github.com/OmniLayer/omnicore/wiki/Use-the-raw-transaction-API-to-create-a-Simple-Send-transaction)。如果你的项目是C++写的，有些接口通过库的形式可以集成到代码中，比如签名。但是要是别的语言就比较麻烦了。



