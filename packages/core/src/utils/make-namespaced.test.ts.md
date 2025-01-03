# Snapshot report for `src/utils/make-namespaced.test.ts`

The actual snapshot is saved in `make-namespaced.test.ts.snap`.

Generated by [AVA](https://avajs.dev).

## make namespaced input

> Snapshot 1

    {
      language: 'Solidity',
      settings: {
        evmVersion: 'paris',
        optimizer: {
          enabled: true,
          runs: 200,
        },
        outputSelection: {
          '*': {
            '': [
              'ast',
            ],
            '*': [
              'storageLayout',
            ],
          },
        },
      },
      sources: {
        'contracts/test/NamespacedToModify.sol': {
          content: `// SPDX-License-Identifier: MIT␊
          pragma solidity ^0.8.20;␊
          ␊
          contract Example {␊
              /// @custom:storage-location erc7201:example.main␊
              struct MainStorage {␊
                  uint256 x;␊
                  uint256 y;␊
              } MainStorage $MainStorage_random;␊
          ␊
              /// @custom:storage-location erc7201:example.secondary␊
              struct SecondaryStorage {␊
                  uint256 a;␊
                  uint256 b;␊
              } SecondaryStorage $SecondaryStorage_random;␊
          ␊
              /// @notice some natspec␊
              function foo() public {}␊
          ␊
              /// @param a docs␊
              function foo1(uint a) public {}␊
          ␊
              /// @param a docs␊
              function foo2(uint a) internal {}␊
              struct MyStruct { uint b; }␊
          ␊
              // keccak256(abi.encode(uint256(keccak256("example.main")) - 1)) & ~bytes32(uint256(0xff));␊
              bytes32 private constant MAIN_STORAGE_LOCATION =␊
                  0x183a6125c38840424c4a85fa12bab2ab606c4b6d0e7cc73c0c06ba5300eab500;␊
          ␊
              function _getMainStorage() private pure returns (bool) {}␊
          ␊
              function _getXTimesY() internal view returns (bool) {}␊
          ␊
              /// @notice standlone natspec␊
          ␊
              /// @notice natspec for var␊
              enum $astId_id_random { dummy }␊
          ␊
              // standalone doc␊
          ␊
              /**␊
               * standlone doc block␊
               */␊
          ␊
              /**␊
               * doc block without natspec␊
               */␊
              function foo3() public {}␊
          ␊
              /**␊
               * doc block with natspec␊
               *␊
               * @notice some natspec␊
               */␊
              function foo4() public {}␊
          ␊
              /**␊
               * @dev a custom error inside a contract␊
               */␊
              enum $astId_id_random { dummy }␊
          ␊
              enum $astId_id_random { dummy }␊
              enum $astId_id_random { dummy }␊
              enum $astId_id_random { dummy }␊
              enum $astId_id_random { dummy }␊
              enum $astId_id_random { dummy }␊
          }␊
          ␊
          contract HasFunction {␊
            ␊
            function foo() pure public returns (bool) {}␊
          }␊
          ␊
          contract UsingFunction is HasFunction {␊
            enum $astId_id_random { dummy }␊
          }␊
          ␊
          function FreeFunctionUsingSelector() pure returns (bool) {}␊
          ␊
          bytes4 constant CONSTANT_USING_SELECTOR = HasFunction.foo.selector;␊
          ␊
          library Lib {␊
            function usingSelector() pure public returns (bool) {}␊
          ␊
            function plusOne(uint x) pure public returns (bool) {}␊
          }␊
          ␊
          contract Consumer {␊
            enum $astId_id_random { dummy }␊
          ␊
            function usingFreeFunction() pure public returns (bool) {}␊
          ␊
            function usingConstant() pure public returns (bool) {}␊
          ␊
            function usingLibraryFunction() pure public returns (bool) {}␊
          }␊
          ␊
          contract HasConstantWithSelector {␊
            bytes4 constant CONTRACT_CONSTANT_USING_SELECTOR = HasFunction.foo.selector;␊
          }␊
          ␊
          function plusTwo(uint x) pure returns (bool) {}␊
          ␊
          /**␊
           * @notice originally orphaned natspec␊
           */␊
          ␊
          /**␊
           * @dev plusThree␊
           * @param x x␊
           */␊
          function plusThree(uint x) pure returns (bool) {}␊
          ␊
          /// @notice originally orphaned natspec 2␊
          ␊
          /**␊
           * @dev plusThree overloaded␊
           * @param x x␊
           * @param y y␊
           */␊
          function plusThree(uint x, uint y) pure returns (bool) {}␊
          ␊
          function originallyNoDocumentation() pure {}␊
          ␊
          /**␊
           * @param foo foo␊
           */␊
          enum $astId_id_random { dummy }␊
          ␊
          contract UsingForDirectives {␊
            enum $astId_id_random { dummy }␊
          ␊
            function usingFor(uint x) pure public returns (bool) {}␊
          }␊
          ␊
          /**␊
           * @title a␊
           * @author a␊
           * @inheritdoc Example␊
           * @dev a␊
           * @custom:a a␊
           * @notice a␊
           * @param a a␊
           * @return a a␊
           */␊
          enum FreeEnum { MyEnum }␊
          ␊
          /**␊
           * @dev a custom error outside a contract␊
           * @param example example parameter␊
           */␊
          enum CustomErrorOutsideContract { dummy }␊
          ␊
          int8 constant MAX_SIZE_C = 2;␊
          ␊
          contract StructArrayUsesConstant {␊
            uint16 private constant MAX_SIZE = 10;␊
          ␊
            struct NotNamespaced {␊
              uint16 a;␊
              uint256[MAX_SIZE] b;␊
              uint256[MAX_SIZE_C] c;␊
            }␊
          ␊
            /// @custom:storage-location erc7201:uses.constant␊
            struct MainStorage {␊
              uint256 x;␊
              uint256[MAX_SIZE] y;␊
              uint256[MAX_SIZE_C] c;␊
            } MainStorage $MainStorage_random;␊
          }␊
          ␊
          address constant MY_ADDRESS = address(0);␊
          uint constant CONVERTED_ADDRESS = uint160(MY_ADDRESS);␊
          ␊
          interface IDummy {␊
          }␊
          ␊
          contract UsesAddress {␊
            IDummy public constant MY_CONTRACT = IDummy(MY_ADDRESS);␊
          }␊
          ␊
          contract HasFunctionWithRequiredReturn {␊
              struct S { uint x; }␊
              function foo(S calldata s) internal pure returns (bool) {}␊
          }␊
          ␊
          /**␊
           * @return uint 1␊
           * @return uint 2␊
           */␊
          function hasMultipleReturns() pure returns (bool,bool) {}␊
          ␊
          /**␊
           * @return a first␊
           * @return b second␊
           */␊
          function hasMultipleNamedReturns() pure returns (bool,bool) {}␊
          ␊
          contract HasNatSpecWithMultipleReturns {␊
              /**␊
               * @return uint 1␊
               * @return uint 2␊
               */␊
              function hasMultipleReturnsInContract() public pure returns (bool,bool) {}␊
          ␊
              /**␊
               * @return a first␊
               * @return b second␊
               */␊
              function hasMultipleNamedReturnsInContract() public pure returns (bool,bool) {}␊
          }␊
          `,
        },
        'contracts/test/NamespacedToModifyImported.sol': {
          content: `// SPDX-License-Identifier: MIT␊
          pragma solidity ^0.8.20;␊
          ␊
          import {CONSTANT_USING_SELECTOR, plusTwo, plusThree, CustomErrorOutsideContract} from "./NamespacedToModify.sol";␊
          ␊
          contract Example {}␊
          `,
        },
      },
    }

## make namespaced input - solc 0.7

> Snapshot 1

    {
      language: 'Solidity',
      settings: {
        optimizer: {
          enabled: true,
          runs: 200,
        },
        outputSelection: {
          '*': {
            '': [
              'ast',
            ],
            '*': [
              'storageLayout',
            ],
          },
        },
      },
      sources: {
        'contracts/test/NamespacedToModify07.sol': {
          content: `// SPDX-License-Identifier: MIT␊
          pragma solidity 0.7.6;␊
           ␊
          contract HasFunction {␊
            ␊
            function foo() pure public returns (bool) {}␊
          }␊
          ␊
          contract UsingFunction is HasFunction {␊
            enum $astId_id_random { dummy }␊
          }␊
          `,
        },
      },
    }
