```
title: 'Bitcoin Optech Newsletter #144'
permalink: /zh/newsletters/2021/04/14/
name: 2021-04-14-newsletter-zh 
slug: 2021-04-14-newsletter-zh 
type: newsletter
layout: newsletter
lang: zh
```

本周简报将总结关于激活Taproot的代码方面的最新进展 ，还包括我们的常规板块，即最近Bitcoin Core PR Review Club（Bitcoin Core审查俱乐部）会议的介绍，以及比特币基础软件的重要更新。



## 新闻

- **关于激活Taproot 的探讨：**

自我们上次在 [Newsletter #139](https://bitcoinops.org/en/newsletters/2021/03/10/#taproot-activation-discussion)中更新关于探讨Taproot软分叉的激活方式之后， the Speedy Trial （快速试用）方案已成为那些对激活Taproot感兴趣的人所关注的焦点。PRs(项目审计？)为其打开了2个变体：[PR#21377](https://github.com/bitcoin/bitcoin/issues/21377) （使用在[BIP9](https://github.com/bitcoin/bips/blob/master/bip-0009.mediawiki) 中的一个变体）和[PR#21392](https://github.com/bitcoin/bitcoin/issues/21392) （使用已成为[BIP8](https://github.com/bitcoin/bips/blob/master/bip-0008.mediawiki) 一部分的一个变体）。这些PR之间的主要技术差异是如何规定其起点和终点。 PR＃21377使用的是MTP（中位时间过去）； PR＃21392使用的是当前区块的高度。

比特币的主网（mainnet）及其各种测试网（例如testnet，默认[签名](https://bitcoinops.org/en/topics/signet/)和各种独立签名）的MTP（中位时间过去）基本一致。这使得多个网络即使有截然不同的区块高度，它们也可以共享一组激活参数，从而最大程度地减少了保持网络用户与主网的共识更改同步时产生的工作量。

不幸的是，MTP（中位时间过去）既可以轻易地由一小部分矿工以小规模的方式进行操作，也可以轻易地由大部分哈希率以大规模的方式进行操作。即使在区块链重组期间偶然发生，它也可以恢复到更早期。 相比之下，区块高度只会在非正常重组时降低[1](https://bitcoinops.org/en/newsletters/2021/04/14/#fn:height-decreasing)。这让审计人员基本可以得出区块高度只会增加的简化假设，这使得分析基于区块高度的激活机制比MTP机制更容易。

在两项提案之间以及其他顾虑间的这些权衡取舍造成了一种僵局，即一些开发人员认为这使得PR免于额外的审计，最终，其中一方将合并到Bitcoin Core中。当两个PR的作者同意折衷方案时，这一僵局得以解决，使激活讨论中的一些参与者感到满意：

1.用MTP(时间中位过去）表示在节点开始对软分叉的块信令进行计数的时间，并且计数会从开始时间之后的下一个2,016块重定向目标周期的开始处进行。<u>这与[BIP9](https://github.com/bitcoin/bips/blob/master/bip-0009.mediawiki) （versionbits）和 [BIP148](https://github.com/bitcoin/bips/blob/master/bip-0148.mediawiki) （UASF用户激活软分叉）</u>开始计数其帮助激活过的软分叉中的方式相同。

2.也用MTP(时间中位过去）表示在节点终止对尚未被锁定的软分叉的块信令进行计数的时间。但与BIP9不同的是，只有在执行计数的重定向目标周期结束时才会检查 MTP终止时间。这取消了从启动直接变成失败的激活尝试功能，简化了分析过程，并确保至少有一个完整的2,016个区块周期，使得矿工可以在这期间发出激活信号。

3.用块高表示最小激活参数。这进一步简化了分析过程，同时还与允许多个测试网络共享激活参数的目标保持一致。 尽管这些网络上的块高可能不同，它们都可以使用`0`的最小激活高度在MTP定义的窗口中激活。

虽然一些参与讨论的人对折中方案表示不满，但是到目前为止，已经收到了来自十多位Bitcoin Core积极贡献者和其他两个全节点应用（ (btcd and libbitcoin）维护人员对其应用情况的评论或表达支持。我们希望激活Taproot的势头继续保持，也会在后续的新闻简报中同步更多进展。



## Bitcoin Core审查俱乐部

本月的这一部分，我们会总结最近一次的[Bitcoin Core审查俱乐部](https://bitcoincore.reviews/)的会议内容，着重介绍一些重要的问题和答案。点击以下问题查看其答案摘要。

[Introduce deploymentstatus](https://bitcoincore.reviews/19438) <u>（介绍了部署状态）</u>是安东尼·汤斯（Anthony Towns）的PR([#19438](https://github.com/bitcoin/bitcoin/issues/19438))，他提出的3个辅助函数使其可以在不改变检查软分叉激活状态的所有代码路径的情况下，更轻松地嵌入未来的部署<u>（bury future deployments）</u>：`DeploymentEnabled`测试部署是否可以活跃，

`DeploymentActiveAt`检查是否应在给定的区块中强制执行部署，

`DeploymentActiveAfter`了解是否应在后面的区块中强制执行部署，

这3个函数对嵌入式部署和version bits部署都有效。



审查俱乐部的讨论集中在理解这些变更和其潜在的好处。

<u>(表格效果做不出来）</u>

| 与[BIP9](https://github.com/bitcoin/bips/blob/master/bip-0009.mediawiki)version bits部署相比，[BIP90](https://github.com/bitcoin/bips/blob/master/bip-0090.mediawiki)嵌入式部署有哪些优势？ |
| ------------------------------------------------------------ |
| 嵌入式部署通过用简单的块高检查代替控制执行的测试来简化部署的逻辑，从而减小与部署这些共识更改相关的技术负担。 |
| PR([#19438](https://github.com/bitcoin/bitcoin/issues/19438))中列举了多少嵌入式部署？ |
| 5个，分别是coinbase里的块高，CLTV (`CHECKLOCKTIMEVERIFY`)，严格的DER签名，CSV (`OP_CHECKSEQUENCEVERIFY`)，以及segwit.PR在[src/consensus/params.h#L14-22](https://github.com/bitcoin/bitcoin/blob/e72e062e/src/consensus/params.h#L14-L22)中提出的这些都被列在`BuriedDeployment`枚举器中。可以说中本聪时代的软分叉被嵌入了。 |
| 目前所定义的*version bits*部署有多少？                       |
| 2个。testdummy 和schnorr/taproot (BIPs 340-342), 在[src/consensus/params.h#L25-31](https://github.com/bitcoin/bitcoin/blob/e72e062e/src/consensus/params.h#L25-L31)中的代码库枚举。 |
| 如果Taproot软分叉被激活，而且我们之后想要嵌入该激活方式，若此PR合并，我们需要对Bitcoin Core做哪些修改？ |
| 与当前代码相比，主要更改将大大简化：将`DEPLOYMENT_TAPROOT`行从`DeploymentPos`枚举器移动到`BuriedDeployment`。 最重要的是，无需更改验证逻辑。 |



## 重要代码和文档更新

 本周在[Bitcoin Core](https://github.com/bitcoin/bitcoin), [C-Lightning](https://github.com/ElementsProject/lightning), [Eclair](https://github.com/ACINQ/eclair), [LND](https://github.com/lightningnetwork/lnd/), [Rust-Lightning](https://github.com/rust-bitcoin/rust-lightning), [libsecp256k1](https://github.com/bitcoin-core/secp256k1), [Hardware Wallet Interface (HWI)](https://github.com/bitcoin-core/HWI), [Rust Bitcoin](https://github.com/rust-bitcoin/rust-bitcoin), [BTCPay Server](https://github.com/btcpayserver/btcpayserver/), [Bitcoin Improvement Proposals (BIPs)和](https://github.com/bitcoin/bips/) [Lightning BOLTs](https://github.com/lightningnetwork/lightning-rfc/)中的重要更新。

-  [Bitcoin Core #21594](https://github.com/bitcoin/bitcoin/issues/21594)在`getnodeaddresses`PRC(控制暂存器)中增加一个`network`字段，以帮助识别各种网络上的节点（即IPv4, IPv6, I2P, onion）。作者还提出这为将来`getnodeaddresses`的某个补丁打下了基础，该补丁接受特定网络的参数，并仅返回该网络中的地址。

- [Bitcoin Core #21166](https://github.com/bitcoin/bitcoin/issues/21166) 改进了`signrawtransactionwithwallet`PRC(控制暂存器)，使其能够在具有其他不属于钱包的已签名输入的交易中对输入进行签名。[此前](https://github.com/bitcoin/bitcoin/issues/21151)，如果通过了RPC的一个交易，且该交易的已签名输入不属于钱包，那么这些输入的见证人会在返回的交易中被破坏。在各种情况下，用其他已签名的输入在交易中进行输入签名可能很有用，[包括增加输入/输出以提高交易费用。](https://github.com/bitcoin/bitcoin/issues/21151)
- [LND #5108](https://github.com/lightningnetwork/lnd/issues/5108) 增加了对使用低级`sendtoroute`RPC进行自发原子化多路径支付(AMP，也称*Original AMPs*）的支持。因为付款方会选择所有原像，所以原子化多路径支付本质上是非交互的（或自发的）。付款方原像选择也是keysend样式[自发支付](https://bitcoinops.org/en/topics/spontaneous-payments/)的一部分，该方式已被用于单路径自发支付。预计后续的PR将使自发性多路径支付可以用于更高级别的`sendpayment` PRC。
- [LND #5047](https://github.com/lightningnetwork/lnd/issues/5047) 确保钱包导入[BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki)扩展公共密钥（xpubs）并将其用于接收对LND链上钱包的付款。 结合LND最近更新的对[PSBT](https://bitcoinops.org/en/topics/psbt/)（部分签名的比特币交易）的支持（请参阅 [Newsletter #118](https://bitcoinops.org/en/newsletters/2020/10/07/#lnd-4389))，这使LND可以将其非渠道资金<u>用作仅监控钱包。</u> 例如，Alice可以从冷钱包中导入xpub，使用给她的LND地址将资金存入该钱包，请求LND打开一个渠道，用她的零钱包签署PSBT打开该渠道，然后在渠道关闭时让LND自动退还资金到其冷钱包里。最后一部分是将已关闭渠道的资金存回冷钱包中，这可能需要采取额外的步骤，尤其是在非合作渠道关闭的情况下，但是这种变化使LND大部分都可以与兼容PSBT的冷钱包和硬钱包完全互操作。



## 附注：

1. 如果区块链上的每个区块都具有相同的单独工作量证明（PoW），那么有最大汇总PoW的有效链也将是最长的链，即最新区块的高度最大的链。但是，比特币协议每产生2016个区块，就会调整新区块需要包含的PoW数量，从而增加或减少需要证明的工作，以使区块之间的平均时间保持在10分钟左右。 这意味着，具有较少区块的的链可能比具有较多区块的链具有更大的工作量证明。

比特币用户使用PoW最多的链（而不是区块最多）来确定他们是否收到了钱。 当用户看到该链上的有效变体，变体末端的某些区块已被不同的区块替换时，如果该重组链包含的PoW比其当前链更多，则他们将使用该重组链。 尽管重组链有更多累积的PoW，但重组链可能包含较少的区块，从而减小链的高度。

虽然这是一个理论上的顾虑，但通常不是实际应用中的问题。 只有在重组部分越过一组2016个块和另一组2016个块之间的重定目标边界中的至少一个时，才有可能降低高度。 它还需要进行重组，该重组涉及大量区块或最近所需的PoW量发生重大变化（即预示着哈希率最近大幅上升或下降，或矿工可观察到的操纵）。 在[BIP8](https://github.com/bitcoin/bips/blob/master/bip-0008.mediawiki)的语境中，我们不认为在激活时已降低高度的重组链对用户的影响会比更常规的重组链更大。