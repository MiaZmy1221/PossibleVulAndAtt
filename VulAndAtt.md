# Possible attacks for our project
Note 1: we have global facts files: use, value, op and so on. If some extra global facts files are needed, they will be noted.
## Oyente
| Paper | Attack | Description | OKay? | If no, why? If yes, what facts? |
| ------|--------|------------ |-------| ---- |
| Oyente | Transaction order dependence (TOD) detection | Transaction order makes a difference on the result of executing. | No | It involve multiple transactions and we cannot replay transactions in different orders to see their difference. |
| Oyente | Timestamp dependence detection | The smart contract depends on the timestamp to send out money. (Similar attack: Block number dependence.) | Yes | "CALL", "CALLCODE", "DELEGATECALL", "STATICCALL", "TIMESTAMP", "NUMBER" |
| Oyente | Mishandled execeptions | The result of the exception from callee is not used, which is similar as the unchecked send.| Yes | "CALL", "CALLCODE", "DELEGATECALL", "STATICCALL" |
| Oyente | Reentrancy detection | Attackers make use of fallback functions to steal more money from the victim smart contracts. | Yes | "SLOAD", "JUMPI", "SSTORE"  |

Note 1: We cannot check whether the TOD exists or not since the order of the transactions has been determined.

Note 2: From what are described in the Oyente paper, the timestamp dependence attack is divided into two types: [theRun](https://etherscan.io/address/0xcac337492149bdb66b088bf5914bedfbf78ccc18#code) smart contract uses the timestamp as a state variable (We cannot detect this kind of attack since we don't know the meaning of the state variable in real trace.); [PonziGovernMental](https://etherscan.io/address/0xf45717552f12ef7cb65e95476f217ea008167ae3#code) smart contract use block timestamp to perform some computation. We can tell this kind of attack, but we need to connect the timestamp with money.  
Our oracle is based on another tool named [contractfuzzer](https://arxiv.org/ftp/arxiv/papers/1807/1807.03932.pdf).

## Securify
| Paper | Attack | Description | OKay? | If no, why? If yes, what facts? |
| ------|--------|------------ |-------| ---- |
| Securify | (Re) TOD | ... | No | |
| Securify | (Re) Reentrancy | ... | Yes | |
| Securify | (Re) Handled exception | Unchecked call return value. | Yes | |
| Securify | Restricted transfer | No one can transfer ether. (Ponzi) | No | Our tool is based on only one transaction. We have no idea of all the possible transactions to the smart contract. |

Note 1: Securify has some vulnerable patterns for the smart contract. Its paper does not clearly tell the attacks they are doing, but it has a graph that displays attacks compared with other tools.


## Mythril
| Paper | Attack | Description | OKay? | If no, why? If yes, what facts? |
| ------|--------|------------ |-------| ---- |
| Mythril | Unprotected functions | a) critical functions can be called by anyone. <br> b) msg.sender is compared against an address in storage that can be written to. E.g. Parity wallet bugs. | a) Yes <br> b) Yes |  |
| Mythril | (Re) Unchecked return value |  | Yes | |
| Mythril | (Re) Reentrancy	|  | Yes | |
| Mythril | Multiple sends in a single transaction	| External calls can fail accidentally or deliberately. Avoid combining multiple send() calls in a single transaction. | Yes |"CALL", "CALLCODE", "DELEGATECALL", "STATICCALL" |
| Mythril | External call to untrusted contract		| The callee address can be set by the caller. | Yes | |
| Mythril | Delegatecall or callcode to untrusted contract | Similar as the above vulnerability. But this kind of vulnerability focuses DELEGATECALL and CALLCODE. | Yes | |
| Mythril | Integer overflow/underflow	| For Mythril, integer overflow or underflow has nothing to do with the input data.  | Yes | |
| Mythril | (Re) Timestamp dependence		|  | Yes | |
| Mythril | Payable transaction does not revert in case of failure ??? (Do not understand)	|  | TBD | |
| Mythril | Use of tx.origin	|  | Yes | |
| Mythril | Type confusion ??? (Do not understand) |  | TBD | |
| Mythril | (Re) Predictable RNG	 | Similar as timestamp dependence, except that this vulnerability contains other opcodes: timestamp, block number, coinbase and gaslimit | Yes | |
| Mythril | (Re) TOD |  | No | |
| Mythril | (Re) TOD |  | No | |
| Mythril | (Re) TOD |  | No | |
| Mythril | (Re) TOD |  | No | |
| Mythril | (Re) TOD |  | No | |
| Mythril | (Re) TOD |  | No | |
| Mythril | (Re) TOD |  | No | |


## Vandal
| Paper | Attack | Description | OKay? | If no, why? If yes, what facts? |
| ------|--------|------------ |-------| ---- |
| Vandal | (Re) Unchecked send | part a) of mishandled exceptions in Oyente, handled exception in Securify, unchecked return value in Mythril and unchecked send are same. | Yes | |
| Vandal | (Re) Reentrancy |  | Yes | |
| Vandal | Unsecured balance |  | Yes | |
| Vandal | (Re) Destroyable contract | This kind of vulnerability belongs to unprotected functions in the Mythril. | Yes | |
| Vandal | (Re) Use of origin |  | Yes | |
