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
| Securify | (Re) TOD | SAME AS "Transaction order dependence" IN OYENTE. | No | ...|
| Securify | (Re) Reentrancy | SAME AS "Reentrany detection" IN OYENTE. | Yes | ... |
| Securify | (Re) Handled exception | SAME AS "Mishandled execeptions" IN OYENTE. | Yes | ... |
| Securify | Restricted transfer | No one can transfer ether. (Ponzi) | No | Our tool is based on only one transaction. We have no idea of all the possible transactions to the smart contract. |

Note 1: Securify has some vulnerable patterns for the smart contract. Its paper does not clearly tell the attacks they are doing, but it has a graph that displays attacks compared with other tools.

Note 2: Mishandled exceptions in Oyente, handled exception in Securify, unchecked return value in Mythril and unchecked send are same.

## Vandal
| Paper | Attack | Description | OKay? | If no, why? If yes, what facts? |
| ------|--------|------------ |-------| ---- |
| Vandal | (Re) Unchecked send | SAME AS "Mishandled execeptions" IN OYENTE. | Yes | ... |
| Vandal | (Re) Reentrancy | SAME AS "Reentrany detection" IN OYENTE. | Yes | ... |
| Vandal | Unsecured balance | The balance in the smart contract can be obtained by anyone due to some bugs, such as unmatched constructor. | Yes | "CALL", "SLOAD", "CALLER" |
| Vandal | Destroyable contract | The suicide function can be called by anyone. | Yes | "SELFDESTRUCT", "CALLER" |
| Vandal | Use of origin | Opcode tx.origin contains the address of the account that made the first message call. | Yes | No particular opcode facts but need global facts. |

Note 1: We can follow Vandal's oracle on those vulnerabilities. 

## Zeus
| Paper | Attack | Description | OKay? | If no, why? If yes, what facts? |
| ------|--------|------------ |-------| ---- |
| Incorrect Contract | (Re) Reentrancy attack | SAME AS "Reentrany detection" IN OYENTE. | Yes | ... |
| Incorrect Contract | (Re) Unchecked send | SAME AS "Unchecked send" IN VANDAL. | Yes | ... |
| Incorrect Contract | Failed send | The send always fails due to the heavy code in the receiver smart contract fallback function. | Yes | "CALL" and maybe other call opcodes |
| Incorrect Contract | (Our 3rd direction) Integer overflow and underflow | Integer issues | Yes | "SUB", "ADD", "MUL", "CALLDATALOAD", "SSTORE"  |
| Incorrect Contract | (Re) Transaction state dependence | Also tx.origin bug. <br> SAME AS "Use of origin" IN VANDAL. | Yes | ... |
| Unfair Contract | Absence of logic: Unchecked resources | 1) (Re) Unprotected suicide function. SAME AS "Destroyable contract" IN VANDAL. <br> 2) Some unfair code | 1) Yes <br> 2) No | 1) ... <br>  2) We cannot |
| Unfair Contract | Incorrect Logic: Variable mixup and Logic error (Comparison logic error) | 1) The developer of the smart contract misuses the variables. <br> 2) Balance should be larger than some threshold, rather than less. Then the balance can be transferred. | 1) No <br> 2) No | 1) We cannot get variable name from the bytecode. <br> 2) We cannot tell what the correct logic is. |
| Unfair Contract | Logic correct but unfair | The smart contract has some unfair code.  | No | We cannot know what the unfair code is. |
| Miner's influence | (Re) Block state dependence: such as timestamp, block number and so on. | SAME AS "Timestamp dependence detection" IN OYENTE. | Yes | ... |
| Miner's influence | (Re) TOD. | SAME AS "Transaction order dependence" IN OYENTE. | No | ... |

Note 1: We cannot do most of the vulnerabilities in Unfair contract part. 

## Scale
| Paper | Attack | Description | OKay? | If no, why? If yes, what facts? |
| ------|--------|------------ |-------| ---- |
| Scale | (Re) Suicidal Contracts| SAME AS "Destroyable contract" IN VANDAL. | Yes | ... |
| Scale | (Re) Greedy  Contracts | The ether is frozen and have no way to release Ether after a certain execution state. <br> SAME AS "Restricted transfer" IN SECURIFY. | No | ... |
| Scale | Prodigal  Contracts | Release Ether to arbitrary addresses carelessly. | Yes | "CALL", "CALLDATALOAD" |

Note 1: this scale paper is what Xiaokuan mentioned [Finding The Greedy, Prodigal, and Suicidal Contracts at Scale](https://arxiv.org/pdf/1802.06038.pdf).

Note 2: There is another type of smart contracts named Posthumous Contracts. It is about the killed smart contract. Although killed smart contract does not have code, they can receive ether. Thus, those ethers are wasted. 
