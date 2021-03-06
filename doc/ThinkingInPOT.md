对比POT/POW/POS的一些思考（持续补充）：

1. 链下资产与算力关系：POW生成算力需要消耗链下的资产，从而通过生成的算力来换取币，它是以资产消耗来保证链的安全；而POS生成算力需要把链下资产兑换为币，有币才能出块，它是以当前的资产抵押来保证链的安全；POT的算力与币分离，出块需要的算力是需要持续积累，有算力代表历史上发生的交易比较多，POT某种意义上是一种历史POS，代表过往在链上持有过币，现在出块既不需要消耗资产，也不需要抵押资产；
2. POT算力需要在链上积累，是天生的激励刷交易模型，POW算力在链下生成，而POS算力也是在链上生成，但是生成效率高，如果把非算力积累类交易占比看作链上有效交易占比来比较的话，POW ~> POS >> POT；
3. 算力的永久垄断导致的阶层固化问题：POW没有算力永久垄断门槛，只要愿意投资，再高的算力都存在被另一个更高的算力取代的可能，并且即使短暂垄断也需要很大的代价；POS存在算力永久垄断门槛，只要持币量占有社区绝对优势，就能持续出块，同时收取新发币或者收交易费带来持币量增长，进而进入继续保持出块优势并增加持币量的正循环，虽然POS算力垄断代价也很大，但是一旦形成便难以打破；同样，POT也存在算力永久垄断门槛，只要某个时期交易量占有社区绝对优势，形成垄断，便能在以后垄断出块的时候，将区块的一半以上的空间用来积累算力，剩余空间用来挣交易费，最终形成算力的永久垄断，坐收交易费，这在链诞生的初期可能很容易发生；
4. 算力与地址的关系：POW挖矿算力与地址无关，鼓励使用新地址来保证安全；POS挖矿算力也与地址无关，只有持有币，转移到新地址依然可以挖矿；POT算力与地址锁定，算力无法移动，必须小心保管好私钥；
5. 关于折旧：POW挖矿需要挖矿设备，而设备无时无刻不在折旧，也即是算力折旧，存在出块的代价；有些POS币，如NXT，有币龄的概念，出块之后币龄重新计算，相当于发生了折旧，出块也有代价；POT目前并无折旧概念，不管什么时期积累的power，都是等价的，可以恒久使用，永不折旧，对刷交易激励很大，因此可以考虑出块算力折旧；
6. double voting导致的short range fork问题：POS/POT由于nothing at stake，因此double voting近乎零成本，在double voting投机时会带来short range fork问题，导致链随着高度的增长是发散的，而不是收敛的，因此需要一个选链策略在不同的链之间做出选择；POW没有这个问题。
