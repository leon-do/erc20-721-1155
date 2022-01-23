## Instructions
- Deploy 1155   -> _my1155
- Deploy 20     -> _my20
- Deploy Main(_my1155, _my20) -> _myMain
- Call 1155.addController(_myMain)
- Call 20.addController(_myMain)

```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >0.8.0;

import "@openzeppelin/contracts/token/ERC1155/ERC1155.sol";
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/utils/Strings.sol";

contract Main {

    My1155 my1155;
    My20 my20;

    constructor(address _my1155, address _my20) {
        my1155 = My1155(_my1155);
        my20 = My20(_my20);
    }

    function mint1155(address _to, uint256 _id, uint256 _amount) external {
        my1155.mint(_to, _id, _amount);
    }

    function burn1155(address _from, uint256 _id, uint256 _amount) external {
        my1155.burn(_from, _id, _amount);
    }

    function mint20(address _to, uint256 _amount) external {
        my20.mint(_to, _amount);
    }

    function burn20(address _from, uint256 _amount) external {
        my20.burn(_from, _amount);
    }


}


contract My1155 is ERC1155 {

    // a mapping from an address to whether or not it can mint / burn
    mapping(address => bool) controllers;

    constructor() ERC1155("") {
        controllers[msg.sender] = true;
    }

    function uri(uint256 _tokenID) override public pure returns (string memory) {
        return string(abi.encodePacked("https://MYWEBSITE.io/", Strings.toString(_tokenID), ".json"));
    }

    function mint(address _to, uint256 _id, uint256 _amount) external {
        require(controllers[msg.sender], "Only controllers can mint");
        _mint(_to, _id, _amount, "");
    }

    function burn(address _from, uint256 _id, uint256 _amount) external {
        require(controllers[msg.sender], "Only controllers can burn");
        _burn(_from, _id, _amount);
    }

    function addController(address _controller) external {
        require(controllers[msg.sender], "Only controllers can add");
        controllers[_controller] = true;
    }

    function removeController(address _controller) external {
        require(controllers[msg.sender], "Only controllers can remove");
        controllers[_controller] = false;
    }


}

contract My20 is ERC20 {

    // a mapping from an address to whether or not it can mint / burn
    mapping(address => bool) controllers;
  
    constructor() ERC20("SuperToken", "STN") {
        controllers[msg.sender] = true;
    }

    function mint(address _to, uint256 _amount) external {
        require(controllers[msg.sender], "Only controllers can mint");
        _mint(_to, _amount);
    }

    function burn(address _from, uint256 _amount) external {
        require(controllers[msg.sender], "Only controllers can burn");
        _burn(_from, _amount);
    }

    function addController(address _controller) external {
        require(controllers[msg.sender], "Only controllers can add");
        controllers[_controller] = true;
    }

    function removeController(address _controller) external {
        require(controllers[msg.sender], "Only controllers can remove");
        controllers[_controller] = false;
    }
}
```


```solidity
// SPDX-License-Identifier: GPL-3.0
pragma solidity >0.8.0;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/token/ERC20/ERC20.sol";


contract My721 is ERC721 {

    My20 my20;

    constructor(address _erc20Addr) ERC721("MyCollectible", "MCO") {
        my20 = My20(_erc20Addr);
        // _mint(msg.sender, 1); // owner, tokenId
    }

    function burn20(address _address, uint256 _amount) external {
        my20.burn(_address, _amount);
    }

    function mint20(address _address, uint256 _amount) external {
        my20.mint(_address, _amount);
    }
}

contract My20 is ERC20 {

    // a mapping from an address to whether or not it can mint / burn
    mapping(address => bool) controllers;
  
    constructor() ERC20("SuperToken", "STN") {
        controllers[msg.sender] = true;
        // _mint(msg.sender, 99); // owner, amount
    }

    function mint(address _to, uint256 _amount) external {
        require(controllers[msg.sender], "Only controllers can mint");
        _mint(_to, _amount);
    }

    function burn(address _from, uint256 _amount) external {
        require(controllers[msg.sender], "Only controllers can burn");
        _burn(_from, _amount);
    }

    function addController(address _controller) external {
        controllers[_controller] = true;
    }

    function removeController(address _controller) external {
        controllers[_controller] = false;
    }
}
```
