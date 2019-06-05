# Possible attacks for our project
Note 1: we have global facts files: use, value, op and so on. If some extra global facts files are needed, they will be noted.
Note 2: Re means redundant.
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
| Securify | (Re) TOD | ... | No | ...|
| Securify | (Re) Reentrancy | ... | Yes | ... |
| Securify | (Re) Handled exception | ... | Yes | ... |
| Securify | Restricted transfer | No one can transfer ether. (Ponzi) | No | Our tool is based on only one transaction. We have no idea of all the possible transactions to the smart contract. |

Note 1: Securify has some vulnerable patterns for the smart contract. Its paper does not clearly tell the attacks they are doing, but it has a graph that displays attacks compared with other tools.

Note 2: Mishandled exceptions in Oyente, handled exception in Securify, unchecked return value in Mythril and unchecked send are same.

## Vandal
| Paper | Attack | Description | OKay? | If no, why? If yes, what facts? |
| ------|--------|------------ |-------| ---- |
| Vandal | (Re) Unchecked send |  | Yes | |
| Vandal | (Re) Reentrancy |  | Yes | |
| Vandal | Unsecured balance |  | Yes | |
| Vandal | (Re) Destroyable contract | This kind of vulnerability belongs to unprotected functions in the Mythril. | Yes | |
| Vandal | (Re) Use of origin |  | Yes | |

## teEther
teEther is not based on specific attacks/vulnerabilities.
