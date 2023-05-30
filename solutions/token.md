# **Level 5 -- Token**
## **Contract to hack**
``` solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Token {

  mapping(address => uint) balances;
  uint public totalSupply;

  constructor(uint _initialSupply) public {
    balances[msg.sender] = totalSupply = _initialSupply;
  }

  function transfer(address _to, uint _value) public returns (bool) {
    require(balances[msg.sender] - _value >= 0);
    balances[msg.sender] -= _value;
    balances[_to] += _value;
    return true;
  }

  function balanceOf(address _owner) public view returns (uint balance) {
    return balances[_owner];
  }
}

```
---
## ***Objectives***
* Increase our balance
---
## ***Thought Process***
This is a simple contract. We can see from the compiler version, which is 0.6.0, that to perform mathematical operation without the risk of overflow, we should use the `SafeMath` library. In earlier version, uint256 underflow/overflow means:
```
0 - 1 = 2^256 - 1(max value of uint256)

2^256 - 1 + 1 = 0

```
However in the `transfer()` function, ```require(balances[msg.sender] - _value >= 0);``` the developer used the normal operation symbol `-` instead of the `sub()` function from the `SafeMath` library, and this is what the problem lies. To hack this contract, we just need to cause an underflow of ```balances[msg.sender] - _value ```.

---
## ***Solution***
we have 20 tokens and we need to let ```20 - _value = 2^256 - 1```, so `_value` = 21
``` ts
> await contract.transfer('0x0000000000000000000000000000000000000000', 21)

```



