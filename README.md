# Cart 流动性挖矿
一个质押挖矿项目，两个池子，一个单币池子（cart），一个lp池子（cart+usdt），
分有锁无锁质押，每个池子奖励权重是28开

## Factory.sol
- 属性
```
cartPerBlock
totalWeight
endBlock
mapping(address => address) public pools
```
- 方法
```
registerPool()
changePoolWeight()
setNFTWeight()
mintYieldTo()
setCartPerBlock()
setEndBlock()
emergencyWithdraw()
```

 - 作用

1. 管理池子，可以注册和移除池子(通过池子权重降为0)
设置奖励的额度和周期
2. 奖励派发：按照出块奖励，按权重分给每个池子，池子用户瓜分


## pool.sol
单币池子、LP池子

 - 属性     
 ```             
poolToken
weight
weightMultiplier
poolTokenReserve
weightMultiplier

struct User {
    uint256 tokenAmount;
    uint256 rewardAmount;
    uint256 totalWeight;
    uint256 subYieldRewards;
    Deposit[] deposits;
}
```

 - 方法
 ```
getOriginDeposit()
getDepositsLength()
tokensReceived()
stake()
unstake()
sync()
processRewards()
emergencyWithdraw()
```

 - 作用
池子分有锁质押和无锁质押，用户存款放在这里
权重：根据质押金额、锁仓时间(1-2)、有无NFT(0-0.2)来计算用户权重
1. 奖励领取情况：用户领取奖励，结算掉之前的奖励
2. 奖励派发情况：单币池子的奖励直接派发给用户，LP池子奖励锁仓(有锁)一年
质押和取款时：先sync同步，再将当前的奖励结算掉，最后进行存款取款，
存款取款时，token是到在池子里处理的，奖励才会从factory中mint
存款类型：用户存款数组会标注类型，是用户的本金还是奖励


## Helper.sol
- 方法
```
getDeposit()
getDepositsByIsYield()
getPredictedRewards()
pendingYieldRewards()
lptoTokenAmount()
```

 - 作用
1. 给前端用的，查询奖励、存款情况、
2. 可重复部署、减少pool合约的gas费用