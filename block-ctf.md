# 区块链官方CTF题 write-up

官方网站：https://capturetheether.com/challenges/

做题基本步骤：先下载好metamask插件并装载在浏览器上。登录metamask后点击题目左边的 `Begin Challenge` ，metamask会弹出是否发送消息的对话框，点击确定，之后会自动在Ropsten测试网上加载好ctf环境，之后就可以开始做题。完成题目后点击 `Check Solution` ，同样metamask会弹出是否发送消息的对话框，点击确定，完成验证。

## Warmup

### [Deploy a contract](https://capturetheether.com/challenges/warmup/deploy/)

* Write-Up

```c
  直接点击 Begin Challenge ，metamask会弹出是否发送消息的对话框，点击确定。
  Begin Challenge 的圈圈转好之后，点击 Check Solution ，同样metamask会弹出是否发送消息的对话框，点击确定。
```

### [Call me](https://capturetheether.com/challenges/warmup/call-me/)

* Write-Up

  总结：要想调用一个已经在链上部署的合约的函数，一个比较简单的方法是把源代码复制在remix上，再在deploy中的at address 框中输入已部署的合约地址。必须要这样，remix才可以找到已部署的合约并使用。

```
  打开 remix.ethereum.org 这个网址，新建一个合约文件，把solidity代码源码复制粘贴在合约文件中。
  在网址左侧找到 compile 栏，并单击右侧的compile按钮，编译该文件。
  在网址左侧找到 deploy 栏，environment下拉菜单中选择Injected Web3。
  在 deploy 栏中找到 at address ，在右侧框内输入题目中给的合约地址，点击 at address，下方会显示合约和合约函数列表，点击 callme 函数。
  完成
```

### [Choose a nickname](https://capturetheether.com/challenges/warmup/nickname/)

## Lotteries

### [Guess the number](https://capturetheether.com/challenges/lotteries/guess-the-number/)

### [Guess the secret number](https://capturetheether.com/challenges/lotteries/guess-the-secret-number/)

###  [Guess the random number](https://capturetheether.com/challenges/lotteries/guess-the-random-number/)

* 题目：

  ```c
  pragma solidity ^0.4.21;
  
  contract GuessTheRandomNumberChallenge {
      uint8 answer;
  
      function GuessTheRandomNumberChallenge() public payable {
          require(msg.value == 1 ether);
          answer = uint8(keccak256(block.blockhash(block.number - 1), now));
      }
  
      function isComplete() public view returns (bool) {
          return address(this).balance == 0;
      }
  
      function guess(uint8 n) public payable {
          require(msg.value == 1 ether);
  
          if (n == answer) {
              msg.sender.transfer(2 ether);
          }
      }
  }
  ```

* Write-up

  虽然uint8只有255种可能，但是`isComplete()`要求`address(this).balance == 0`。那么一次环境只能`guess`一次。所以我们要在`guess`前，先计算出来answer的值。

  在`etherscan.io`上搜索合约，以`0xe2d8265D24A4e1045008AEAD2175C46dFA55b834`这个合约地址为例。

  ![](https://github.com/Karry-Almond/blog.io/raw/gh-pages/pic/blockchain-ctf-guessrandom1.png)

  ![](https://github.com/Karry-Almond/blog.io/raw/gh-pages/pic/blockchain-ctf-guessrandom2.png)