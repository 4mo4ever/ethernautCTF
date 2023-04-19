# **Level 8 -- Vault**
## **Contract to hack**
``` ts
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Vault {
  bool public locked;
  bytes32 private password;

  constructor(bytes32 _password) {
    locked = true;
    password = _password;
  }

  function unlock(bytes32 _password) public {
    if (password == _password) {
      locked = false;
    }
  }
}

```
---
## ***Objectives***
* Unlock the vault
---
## ***Thought Process***
The `unlock` function is the only way to change the `locked` parameter's status. However, to set `locked = false`, we need to retrieve the private `password` parameter. In fact, there are still ways to get the amount of the parameter with the `private` keyword. Before discussing this, it is important to understand how storage works in Ethereum. Storage on Ethereum consists of 2^256 slots, and each slots is 32 bytes in size. Data is stored sequentially in these slots, in order of declaration. To optimize the storge, adjacent parameters can share the same slot if they do not exclusively occupy a slot exclusively. For example, if we have three data parameters `a`, `b`, `c`, declared as follows:
``` ts
// slot 0
bool public a;
// slot 1
uint256 public b;
// slot 2
bool public c;
```
It would occupy 3 slots, if we change the order of `b` & `c` like this: 
``` ts
// slot 0
bool public a;
bool public c;

// slot 1
uint256 public b;
```
It would only occupy costs 2 slots.
In this contract, there are only 2 parameters in storage
``` ts
bool public locked;
bytes32 private password;
```
Since `bytes32` occupies a separate slot, the data we need is stored in slot 1, and we can use `web3`'s `getStorage` function to retrieve the data.

---
## ***Solution***
``` ts
let password = await web3.eth.getStorage(instance.address, 1);
await contract.unlock(password);
```



