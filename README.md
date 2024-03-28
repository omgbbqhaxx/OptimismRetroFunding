# Fully onchain random number generator / game

# <img src="https://github.com/omgbbqhaxx/OptimismRetroFunding/blob/main/imgs/optimism.jpeg">

###

[JammySwap Smart Contract is here.](https://basescan.org/address/0x960fB3c7d0188CC56d661726599cC94E8C9b03Ca)

0x960fB3c7d0188CC56d661726599cC94E8C9b03Ca

Just send a 0.001 ETH to this address :

Then you need to check next blocks 'block hash value' if next block hashs last value is 'even, double, twin' you earn %80 yield instantly.

You can send 0.000001 eth again and claim your 0.001 eth + %80 yield instanly. thats mean total 0.0018 eth.

[Example winner.](https://basescan.org/tx/0x8ab4887a4bbbf38e6e40bb44a129b62cea48b90b98b23de1cce891aa8998d25c)

```shell
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.25;
contract JammySwap  {


  uint256 public minbetAM  = 10**10; //0.00000001
  uint256 public maxbetAM = 1*(10**17); // thats means 1ETH
  uint256 public winrate = 90;


  address payable private manager;
  uint256 private lpb = block.number;
  mapping(uint256 => address payable[] ) blockBets;
  mapping (string => uint256 ) private balances;
  mapping (uint256 => bool) private isuserwinner;





  constructor()  {manager = payable(msg.sender);}

   function uintToString(uint v) public pure returns (string memory) {
        uint maxlength = 100;
        bytes memory reversed = new bytes(maxlength);
        uint i = 0;
        while (v != 0) {
            uint remainder = v % 10;
            v = v / 10;
            reversed[i++] = bytes1(uint8(48 + remainder));
        }
        bytes memory s = new bytes(i); // i + 1 is inefficient
        for (uint j = 0; j < i; j++) {
            s[j] = reversed[i - j - 1]; // to avoid the off-by-one error
        }
        string memory str = string(s);  // memory isn't implicitly convertible to storage
        return str;
    }

    function nAddrHash(address _address) internal pure returns (uint) {
        uint i = uint256(uint160(address(_address)));
        return i % 10000000000;
    }

  function append(string memory a, string memory b) internal pure returns (string memory) {
        return string(abi.encodePacked(a,"-",b));
    }

    function MixAddrandSpBlock(uint256 bnum, address _address)  public pure returns(string memory) {
         return append(uintToString(nAddrHash(_address)), uintToString(bnum));
    }



    receive() external payable {
         if(msg.sender != manager) {

            if(msg.value <= maxbetAM && msg.value >= minbetAM) {
              if(lpb != block.number) { for (uint256 i=0; i < blockBets[lpb].length; i++) { cashBack(lpb, blockBets[lpb][i]);}lpb = block.number; }

                blockBets[lpb].push(payable(msg.sender));
                if(balances[MixAddrandSpBlock(lpb, msg.sender)] == 0) { balances[MixAddrandSpBlock(lpb, msg.sender)] = msg.value; }

            } else {revert();}
         }
     }

    function checkReward(uint256 _bnum) private view returns(bool) {
         if(((block.number - 1) - _bnum) < 200) { //this ifblock ckeck 255 block hash problem.
             uint pb = uint256(blockhash(_bnum+1))%2; // future block hash
            if(pb == 0) {
                return true;
            } else {
                return false;
            }
         } else {
             return false;
         }
    }

    function cashBack(uint256 bnum, address payable  _addr) private {
     if(balances[MixAddrandSpBlock(bnum, _addr)] != 0 && checkReward(bnum) == true) {
           uint256 usernewbalance = balances[MixAddrandSpBlock(bnum, _addr)] * 2 ;
           usernewbalance = usernewbalance/ 100;
           _addr.transfer(usernewbalance * winrate);
           balances[MixAddrandSpBlock(bnum, _addr)] = 0;
     }
  }



  function setmaxPrice(uint256 newPrice) public {
    require(msg.sender == manager,"only manager can reach  here");
    maxbetAM = newPrice;
  }

  function setminPrice(uint256 newPrice) public {
    require(msg.sender == manager,"only manager can reach  here");
    minbetAM = newPrice;
  }

  function setrate(uint256 newrate) public {
    require(msg.sender == manager,"only manager can reach  here");
    winrate = newrate;
  }


  function withdraw(uint256 _wamount) public {
     require(msg.sender == manager,"only manager can reach  here");
    payable(msg.sender).transfer(_wamount);
  }


  function changeOwnership(address newManager) public {
    require(msg.sender == manager, "only manager can reach here");
    manager = payable(newManager);
    }

  function getEthBalance() public view returns(uint) {
    return address(this).balance;
 }


}

```

## Contact

[![Gitter](https://img.shields.io/gitter/room/nwjs/nw.js.svg)](https://x.com/vrnouns/)
[![GitHub Issues](https://img.shields.io/badge/open%20issues-0-yellow.svg)](https://github.com/omgbbqhaxx/OptimismRetroFunding/issues)

- Report bugs, issues or feature requests using [GitHub issues](issues/new).

## License

[![License](https://img.shields.io/github/license/ethereum/cpp-ethereum.svg)](LICENSE)

All contributions are made under the [GNU General Public License v3](https://www.gnu.org/licenses/gpl-3.0.en.html). See [LICENSE](LICENSE).

```

```
