# **Bitcoin Optech Newsletter #145**

Apr 21, 2021

本周的新闻简报将总结激活主根的最新进展，LN为解决滞纳金进行的更新进展，总结LND锚定输出的反馈请求，并宣布Sapio智能合约开发工具包的公开发布。还包括我们的常规板块，即总结描述了最近流行的客户端和服务，新发行版和发行候选版本以及比特币基础软件重要更新。

 

新闻

**l** ***\*Taproot激活版本候选者\****：自我们在上周新闻简报（https://bitcoinops.org/en/newsletters/2021/04/14/#taproot-activation-discussion）中更新关于taproot（https://bitcoinops.org/en/topics/taproot/）激活相关内容以来，比特币核心项目已经合并了一个估计快速试验（https://bitcoinops.org/en/newsletters/2021/03/10/#a-short-duration-attempt-at-miner-activation）激活机制的拉动请求（https://github.com/bitcoin/bitcoin/pull/21377）和第二个包含激活参数的PR（https://github.com/bitcoin/bitcoin/pull/21686）。

 

这些PR和之前其他一些PR是Bitcoin Core 0.21.1第一个发布候选版本的一部分。测试和其他质量保证任务预计将在本新闻发布后几天继续。有关更多详细信息，请参见下面的RC和合并摘要部分。

 

使用LN优惠可解决滞纳金问题：在某些情况下，尝试支付LN发票可能导致付款长期滞留。在解决故障之前，请求第二张发票进行第二次付款尝试可能导致付款两次。

 

**l** *****本周，Rusty Russell在Lightning-Dev发表（https://lists.linuxfoundation.org/pipermail/lightning-dev/2021-April/002992.html）了他对提出报价(https://bitcoinops.org/en/topics/offers/)的规范（https://bitcoinops.org/en/topics/offers/）**，**允许收款人可以提交新的发票来代替以前的发票。如果付款人支付第二份发票，仍然存在支付两次的风险，但是如果要约收款人的签字加上LN固有的付款证明，则支出者可以证明接受这两项付款时，接收者的行为是欺骗性的。 当向声誉良好的收款人付款(如热门业务)时，就能避免滞留金问题。

 

 

 

要约规范的更新还使得接收方能够证明他们是否收到付款。在这种情况下，支出者和接收者的资金都是完全安全的，唯一的结果是，支出者需要等待一段时间才能重新使用其特定的支付时段（[HTLC](https://bitcoinops.org/en/topics/htlc/)（https://bitcoinops.org/en/topics/htlc/）时段）。与普通发票相比，这种交互式交流的能力是要约的明显优势。

 

 

 

 

**l** ***\*默认情况下在LND中使用锚定输出\****： Olaoluwa Osuntokun 在LND engineering中表示他希望LND的下一个主要版本默认使用[锚定输出](https://bitcoinops.org/en/topics/anchor-outputs/)（https://bitcoinops.org/en/topics/anchor-outputs/）。锚输出将允许关闭通道的未确认的LN承诺交易能够取消CPFP费用（https://bitcoinops.org/en/topics/cpfp/）。但是与此同时LN模型中CPFP费用将会面临一些挑战：

 

并非始终是可选的：对于常规的链上交易，许多用户可以等待长时间来确认交易，以避免费用上调。但是对于LN来说，有时无法等待——需要在数小时内提交上调的费用完成交易，否则可能会造成资金损失。

 

 

时间锁定输出: 对于大多数定期的链上支付，想要CPFP增加的用户可以使用存储在交易输出中的资金来支付增加的费用。但对LN而言，这些资金只有在链上完全结算完渠道关闭后才能使用。这意味着用户需要使用单独的UTXO来支付费用。

 

 

为了解决以上两个问题，LND要求锚定输出的用户在通道打开时随时在其钱包中保留至少一个已确认的合理价值的UTXO。这样可以确保他们可以在必要时提高CPFP的费用，但这会对用户造成一定的不便，例如，在用户至少有一个开放的渠道开放时，阻止其花费最后的链上资金（甚至是开设新渠道）。

 

Osuntokun的使用要求基于LND的钱包或服务，目的是使开发团队知道上述任何问题或与锚输出有关的任何其他问题是否会引起严重后果。尽管该问题特定于LND，但其造成的影响可能会波及所有LN节点。

 

 

**l** **Sapio公开发布：**Jeremy Rubin在Bitcoin- dev中发表声明（https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2021-April/018759.html）道：他已经发布了Sapio智能合约开发工具包，这是一个基于rust的库和相关工具，可以用来创建使用Bitcoin Script进行表达的智能合约。该语言最初设计是为了利用Rubin提出的OP_CHECKTEMPLATEVERIFY（https://bitcoinops.org/en/topics/op_checktemplateverify/）操作码(OP_CTV)，但它可以模拟该操作码的存在，以及其他潜在的比特币功能，如使用可信的签名oracle。除了Sapio库之外，该版本还包含了大量的文档和一个实验性前端。

 

 

## 服务和客户端软件的更新

 

在本月度功能中，我们重点介绍了比特币钱包和服务的更新。

 

l ***\*发布Spectre v1.3.0\****： [Spectre v1.3.0](https://github.com/cryptoadvance/specter-desktop/releases/tag/v1.3.0)（https://github.com/cryptoadvance/specter-desktop/releases/tag/v1.3.0）包括附加的RBF支持，应用程序内部的Bitcoin Core设置，HWI 2支持，以及将[mempool.space](#mempool-v2-0-0-released)（https://bitcoinops.org/en/newsletters/2021/01/20/#mempool-v2-0-0-released）用作[块浏览器](https://bitcoinops.org/en/topics/block-explorers/)（https://bitcoinops.org/en/topics/block-explorers/）和进行费用估算的选项。

 

**l** [ ](#specter-diy-v1-5-0)***\*Spectre-DIY v1.5.0： 硬件钱包固件Spectre-DIY\****[***\*发布\*******\*（https://github.com/cryptoadvance/specter-diy/releases）\*******\*了\****](https://github.com/cryptoadvance/specter-diy/releases)***\*v1.5.0\****，其中添加了自定义SIGHASH标志和包括[miniscript](https://bitcoinops.org/en/topics/miniscript/)（https://bitcoinops.org/en/topics/miniscript/）在内的完整[描述符](https://bitcoinops.org/en/topics/output-script-descriptors/)（https://bitcoinops.org/en/topics/output-script-descriptors/）的功能。

 

**l** ***\*BlueWallet v6.0.7添加了消息签名\****：BlueWallet v6.0.7（https://github.com/BlueWallet/BlueWallet/releases/tag/v6.0.7）允许用户使用比特币地址对消息进行签名和验证，以及其他功能和修复。

 

**l** ***\*Azteco宣布支持Lightning Network\****： 比特币凭证公司Azteco[宣布](https://medium.com/@Azteco_/at-azteco-weve-been-experimenting-with-lightning-for-over-a-year-refining-our-thinking-and-user-b9d112cff13c)支持通过Lightning Network兑换购买的比特币。

 

## **发布和发布候选**

新版本和流行的比特币基础设施项目的候选版本。请考虑升级到新版本或帮助测试候选版本。

 

l Bitcoin Core 0.21.1rc1（https://bitcoincore.org/bin/bitcoin-core-0.21.1/）是比特币核心版本的候选发布版本，如果激活，它将强制执行建议的taproot软叉的规则，该规则使用schnorr签名并允许使用tapscript。这些分别由BIP 341(https://github.com/bitcoin/bips/blob/master/bip-0341.mediawiki)、340(https://github.com/bitcoin/bips/blob/master/bip-0340.mediawiki)和342(https://github.com/bitcoin/bips/blob/master/bip-0342.mediawiki)指定。还包括支付由BIP350(https://github.com/bitcoin/bips/blob/master/bip-0350.mediawiki)指定的bech32m地址的能力，尽管在主网上使用这些地址的比特币并不安全的，直到激活使用这些地址的软分叉，如taroot。该版本还包含了bug修复和一些小改进。

 

 

## 重要的代码和文档更新

本周 [Bitcoin Core](https://github.com/bitcoin/bitcoin)(https://github.com/bitcoin/bitcoin)， [C-Lightning](https://github.com/ElementsProject/lightning)(https://github.com/ElementsProject/lightning)，[Eclair](https://github.com/ACINQ/eclair)(https://github.com/ACINQ/eclair)，[LND](https://github.com/lightningnetwork/lnd/)（https://github.com/lightningnetwork/lnd/）， [Rust-Lightning](https://github.com/rust-bitcoin/rust-lightning)（https://github.com/rust-bitcoin/rust-lightning），[libsecp256k1](https://github.com/bitcoin-core/secp256k1)（https://github.com/bitcoin-core/secp256k1）， [Hardware Wallet Interface (HWI)](https://github.com/bitcoin-core/HWI) （https://github.com/bitcoin-core/HWI）, [Rust Bitcoin](https://github.com/rust-bitcoin/rust-bitcoin)（https://github.com/rust-bitcoin/rust-bitcoin）, [BTCPay Server](https://github.com/btcpayserver/btcpayserver/)（https://github.com/btcpayserver/btcpayserver/）, [Bitcoin Improvement Proposals (BIPs)](https://github.com/bitcoin/bips/) （https://github.com/bitcoin/bips/）, 和 [Lightning BOLTs](https://github.com/lightningnetwork/lightning-rfc/)（https://github.com/lightningnetwork/lightning-rfc/）.的各项更新

 

l Bitcoin Core#21377（https://github.com/bitcoin/bitcoin/pull/21377）添加了激活机制，#21686（https://github.com/bitcoin/bitcoin/pull/21686）添加了taroot软分叉的激活参数。从4月24日之后的第一次难度调整开始，矿工将能够在比特2上发出Taproot激活准备信号。如果2016年一个困难期的1815(90%)阻塞了信号窗口的信号准备，则软叉激活锁定。信号窗口随着8月11日之后的第一次困难调整而结束。如果锁定，taroot将在区块高度709632被激活，预计在11月12日左右达到。

 

l [Bitcoin Core #21602](https://github.com/bitcoin/bitcoin/issues/21602) updates the listbanned RPC with two additional fields:ban_duration and time_remaining.

 

l Bitcoin Core#21602（https://github.com/bitcoin/bitcoin/pull/21602）更新列表禁止RPC两个额外字段:ban_duration和time_remaining。

 

 

l C-Lightning #4444（https://github.com/ElementsProject/lightning/pull/4444）添加了lnprototest（https://github.com/rustyrussell/lnprototest） (LN协议测试)到C-Lightning的持续集成(CI)测试的默认目标，也使开发人员更容易从C-Lightning的通常构建系统运行测试。LN协议测试使实现可以很容易地测试它们是否遵循LN协议规范。

 

l LND #4588在更改量很小的情况下跳过创建更改输出，其价值小于花费它的成本。

 

l LND #5193默认使用Neutrino客户端禁用LND实例的通道验证(它实现了紧凑的块过滤器（https://bitcoinops.org/en/topics/compact-block-filters/）协议)。此选项假定从对等端接收到的频道广告是正确的，从而使客户端不必下载验证这些广告所需的旧块。这有一个缺点，即使用虚假广告渠道进行的支付尝试将失败，浪费时间但不会造成金钱损失——对于那些已经选择使用轻量级客户端的人来说，这是一个合理的权衡。这个新的默认行为可以通过使用新的配置选项——neutrino. validatchannels =true来禁用。

 

l LND #5154增加了使用LND的基本支持与一个修剪完整的节点，允许LND外部请求其他比特币节点的块已经被本地节点删除。然后，LND可以从块中提取它需要的任何信息，而无需经过剪枝节点。因为用户自己的完整节点先前验证了块，所以这不会改变安全模型。

 

l LND #5187增加了新的channel-commit-interval和channel-commit-batch-size参数，可用于配置LND在发送通道状态更新之前等待多长时间，以及在一次更新中发送的最大更改数。这些值越高，繁忙的LND节点的效率就越高，尽管这种效率是以延迟稍高为代价的。

 

l Rust-Lightning #858增加了与电子风格区块链数据源互操作的内部支持。

 

l Rust-Lightning #856更新了它如何处理融资交易。此前，该钱包被要求创建融资交易，打开一个新渠道，并只给Rust Lightning交易的txid。现在锈闪电公司接受了全额融资交易。类似于C-Lightning最近的一个变化(见通讯#141（https://bitcoinops.org/en/newsletters/2021/03/24/#c-lightning-4428）)，这允许LN软件在播放前检查资金交易，并确保它是正确的。

 

l HWI #498增加了使用BitBox02硬件钱包签名任意比特币风格消息的支持。

 

l BTCPay Server #2425增加了对钱包接收payjoin（https://bitcoinops.org/en/topics/payjoin/）支付的支持，即使地址与BTCPay发票没有关联。

 