# **Level 6 -- Delegation**
## **Contract to hack**
``` solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Delegate {

  address public owner;

  constructor(address _owner) {
    owner = _owner;
  }

  function pwn() public {
    owner = msg.sender;
  }
}

contract Delegation {

  address public owner;
  Delegate delegate;

  constructor(address _delegateAddress) {
    delegate = Delegate(_delegateAddress);
    owner = msg.sender;
  }

  fallback() external {
    (bool result,) = address(delegate).delegatecall(msg.data);
    if (result) {
      this;
    }
  }
}
```

---
## ***Objectives***
* Claim ownership of the contract
---
## ***Thought Process***
In the `Delegation` contract‘s `fallback()` function, we can see that it uses low-level function `delegatecall()` to call the functions in the contract `Delegate` .

When contract A performs a delegatecall to contract B, A will use B's execution logic, but the storage slot in B will not be modified. Instead, the corresponding storage slot value in A will be modified.
For example, in both the `Delegation()` and `Delegate()` contracts, there are two `address` parameter named `owner` and both of them are the `slot0` in their respective contract. 

If we use the `Delegation` contract to delegatecall the `pwn()` function in the `Delagate` contract, the owner of the `Delagate` contract will not be changed. However, the value at the same slot position in the `Delegation` will be replaced by the value of the `msg.sender`.

---
## ***Solution***
``` ts
> await contract.sendTransaction({data: web3.utils.keccak256("pwn()")})
```



