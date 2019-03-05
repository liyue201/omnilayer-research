

### 什么是onmilayer
omnilayer是基于比特币协议之上的一层协议，主要用于发行基于比特币网络的token，类似于以太坊的ERC20。[omnicore](https://github.com/OmniLayer/omnicore)是onmilayer协议的客户端节点，可以看到其代码是从[bitcoin](https://github.com/bitcoin/bitcoin) fork过来再修改的。omnicore没有自己的主网，与比特币使用同一个网络。omnilayer有自己的区块链浏览器[omniexplorer](https://omniexplorer.info/)。omnilayer上可以发行很多token，通过property_id来唯一标识。

### 什么是USDT
USDT是Tether公司发行的基于omnilayer协议稳定币，它的property_id是35。

### omnilayer原理
比特币网络之所以能发行token，是因为比特币的交易输出中可以附带一些信息，（网上也有很多资料，这里省略500字）。omnilayer上的token也包括发行和转账两个功能。发行token其实就是在某一个区块高度发起一比无中生有的转账，然后后面的节点共同承认这笔转账就ok了。后面的普通转账就是从一个地址的余额流转到另一个地址。omnilayer的账本信息不记录在区块上，区块上只有转账记录，账本信息由omnicore客户端维护。当然，这些账本信息是可以从区块上推导和验证的。
### omnilayer 转账
既然omnilayer是基于比特币网络的，那么他的交易数据结构也和比特币一样，也有交易输入和输出。下面是omniexplorer浏览器中一笔USDT的转账（https://omniexplorer.info/search/dd2158cf27bfc8fa18ed93e2a62e82e5d4acfa862bade9468db60381bbb6aebd）

![](https://raw.githubusercontent.com/liyue201/omnilayer-research/master/11111.png)

在比特币浏览器中是这样的

（https://btc.com/dd2158cf27bfc8fa18ed93e2a62e82e5d4acfa862bade9468db60381bbb6aebd)

![](https://raw.githubusercontent.com/liyue201/omnilayer-research/master/2222.png)

可以看到它有一个输入和三个输出，还付了0.00012800 BTC的矿工费。其中第一个输出在比特币浏览器中解析失败了，这里是就是omnilayer协议中的转账信息，第二个输出是找零，第三个输出是转账对象的地址，一般是0.00000546 BTC，但不是绝对的。
