# **Level 2 -- Fal1out**
## **Contract to hack**
``` ts
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import 'openzeppelin-contracts-06/math/SafeMath.sol';

contract Fallout {
  
  using SafeMath for uint256;
  mapping (address => uint) allocations;
  address payable public owner;


  /* constructor */
  function Fal1out() public payable {
    owner = msg.sender;
    allocations[owner] = msg.value;
  }

  modifier onlyOwner {
	        require(
	            msg.sender == owner,
	            "caller is not the owner"
	        );
	        _;
	    }

  function allocate() public payable {
    allocations[msg.sender] = allocations[msg.sender].add(msg.value);
  }

  function sendAllocation(address payable allocator) public {
    require(allocations[allocator] > 0);
    allocator.transfer(allocations[allocator]);
  }

  function collectAllocations() public onlyOwner {
    msg.sender.transfer(address(this).balance);
  }

  function allocatorBalance(address allocator) public view returns (uint) {
    return allocations[allocator];
  }
}
`````` 
---
## ***Objectives***
* Claim ownership of the contract
---
## ***Thought Process***
``` ts
/* constructor */
  function Fal1out() public payable {
    owner = msg.sender;
    allocations[owner] = msg.value;
  }
```
If we check the whole contract, there is no any other function can change the ownership except ```Fal1out()```.
However, according to the note description, ```Fal1out()``` is the constructor function. Constructor function can only be called once when the contract deployed.

Actually this level is not suitable for current version of solidity. If you go through the solidity doc, you can find that before v0.6.0, contract's ```constructor``` function needed to be writted as ```function sameNameWithContract() public{}```. In the v0.6.0 and later, contracts's ```constructor``` function has been replaced by using ```constructor``` keyword.

Therefore, there are two mistakes in the ```Fal1out()``` function.
The first is a stupid spell mistake. The deployer spelled 'l' as '1'. By the way, because of the function's name is same as the contract's name, the contract can still be compiled with error even if the developer spelled `Fallout()` correctly.
The second is as the described in the second paragraph. `Fal1out()` function in this contract is just a normal function that can be called by everyone.

---
## ***Solution***
Just call the fal1out() function
``` js
> await contract.Fal1Out();
```



