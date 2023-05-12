# **Level 15 -- Naught Coin**
## **Contract to hack**
``` ts
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import 'openzeppelin-contracts-08/token/ERC20/ERC20.sol';

 contract NaughtCoin is ERC20 {

  // string public constant name = 'NaughtCoin';
  // string public constant symbol = '0x0';
  // uint public constant decimals = 18;
  uint public timeLock = block.timestamp + 10 * 365 days;
  uint256 public INITIAL_SUPPLY;
  address public player;

  constructor(address _player) 
  ERC20('NaughtCoin', '0x0') {
    player = _player;
    INITIAL_SUPPLY = 1000000 * (10**uint256(decimals()));
    // _totalSupply = INITIAL_SUPPLY;
    // _balances[player] = INITIAL_SUPPLY;
    _mint(player, INITIAL_SUPPLY);
    emit Transfer(address(0), player, INITIAL_SUPPLY);
  }
  
  function transfer(address _to, uint256 _value) override public lockTokens returns(bool) {
    super.transfer(_to, _value);
  }

  // Prevent the initial owner from transferring tokens until the timelock has passed
  modifier lockTokens() {
    if (msg.sender == player) {
      require(block.timestamp > timeLock);
      _;
    } else {
     _;
    }
  } 
} 
`````` 
---
## ***Objectives***
* transfer all the token to others' account. 
---
## ***Thought Process***
It seems we can use the `transfer` function to move token to different addresses. However, this function has a `lockTokens` modifier, indicating we are restricted from transferring token util the lock time ends. At first glance, this contract only has this one `transfer` function, but if we examine the contract in detail, we can notice that this contract inherited `ERC20` standard. This implies that methods defined in the `ERC20` standard are also applicable in this contract. In the `ERC20` standard, token can be transfer by both the `transfer()` function or the `transferFrom()` function. Therefore, to send token to other's address, we can simply to use the `transferFrom()`.

---
## ***Solution***

``` ts
> await contract.transferfrom(myAddress, otherAddress, web3.utils.BN('1000000000000000000000000'));
```



