# CDP Project
## Introduction
A "peer-to-peer review" is a proof of concept exercise that shows how a decentralized, blockchain-based system could be used to establish the quality and credibility of academic papers. By allowing a large number of reviewers (i.e. identified peers) to participate in a transparent way, the system aims at incentivizing a thorough and honest behaviour without the need of centralized authorities or intermediaries (e.g journals, peer-review platforms, conference organizers). For more information on promoting transparency, inclusivity, and accountability in the peer review process, you can learn more about [open peer review](https://www.fosteropenscience.eu/learning/open-peer-review/#/id/5a17e150c2af651d1e3b1bce).


#### Assumptions
The goal of this project is to show a viable mechanism for a reviewing process (the green-framed box in the picture), assuming that other elements of a decentralized, permisionless and censorship-resistant academic ecosystem are in place:

![Screenshot 2023-03-06 at 19 50 41](https://user-images.githubusercontent.com/104091627/223203427-68435d17-262c-4a6d-8542-270d197d25a9.png)




#### Objectives 
- <b>Transparency</b> is guaranteed by the Cardano blockchain history, where information like reviewer's final decision, a link to the documents in a  decentralized version control system (e.g. ipfs textile bucket), number of paper revisions and the reviewer's public key hash (or a name handle for a more transparent solution) is stored and retrievable (e.g. by [Blockfrost query](https://docs.blockfrost.io/#tag/Cardano-Scripts/paths/%7E1scripts%7E1datum%7E1%7Bdatum_hash%7D/get)); 

- <b>Inclusivity</b> is fostered by allowing any peer, i.e., accredited subject matter expert, to voluntarily participate in the reviewing process, rather than being limited to a pre-selected number based on internal decisions made by the editors; 

- <b>Accountability</b> is achieved by requiring reviewers to place a predefined stake that they may lose if they fail to meet their reviewing responsibilities and deadlines.

## Reviewing Process

#### Transactions flowchart 
The below state diagram shows the various steps of the reviewing process (endpoints grey area) where <i>Datums</i> and <i>Redeemers</i> are depicted in the state boxes and tranistion lines respectively. Once the reviewing process is over, either 2 or 1 NFT must be returned to the author. The latter case would mean that the other NFT will be locked in the script along with the datum containing the final review info.

```mermaid
stateDiagram-v2
direction TB
state UTXO1 {
state Empty <<fork>>
state Final <<choice>>
[*] --> Submitted  : <i>Created UTXO\n (2 NFTs + Datum)</i>  
Submitted --> Reviewed : <b>Updated At</b> 
Reviewed --> Submitted : <b>Revision </b> 
Submitted --> Final : <b>Claim Author</b> 
Reviewed --> Final : <b>Claim Reviewer</b> 
Reviewed --> Closed : <b>Closed At</b> 
Closed --> Final : <b>Claim Reviewer</b> 
Final --> [*] : <i>Locked UTXO\n (1 NFT + Datum)</i> 
Final --> Empty : <i>Consumed UTXO\n (2 NFTs back to author)</i> 
state Endpoints {
note left of Closed : Author has ended\n review process
note left of Final : Funds are \n redistributed \n according to \n script logic
note left of Reviewed : Reviewer has requested\n minor/major revision
note left of Submitted : Author has (re)submitted paper
}
}
```

#### Concurrent Approach
According to [IOG](https://iohk.io/en/blog/posts/2021/09/10/concurrency-and-all-that-cardano-smart-contracts-and-the-eutxo-model/), enabling concurrency is crucial for facilitating multiple actors to work simultaneously on a given task without causing interference with each other. Therefore, conducting the reviewing process in parallel within the same smart contract can be a more efficient approach.

To enable parallel execution of the reviewing process within the same smart contract, it is necessary to track the UTXO where validation for a specific transactions occurs. This can be achieved by storing a pair of NFTs, whose name and policy are included in the script parameter, for each UTXO created at the script address and including a field with the payment public key hash of the reviewer in the datum of every UTXO. This ensures that the validation activity of both reviewers and authors is accurately recorded and tracked. 

```mermaid
stateDiagram-v2
    state Script_Address {
        state Final <<choice>>
        state Final2 <<choice>>
        state Final3 <<choice>>
        state Empty <<fork>>
        state Empty2 <<fork>>
        state Empty3 <<fork>>
        state ID_01 {
        [*] --> Reviewing_1
        Reviewing_1 --> Reviewing_1: Validator
        Reviewing_1 --> Final
        Final --> Empty : Invalid
        Final --> <b>UTXO1</b> : Valid
        <b>UTXO1</b> --> [*] : Final Datum
        }
        --
        state ID_02 {
        [*] --> Reviewing_2
        Reviewing_2 --> Reviewing_2: Validator
        Reviewing_2 --> Final2
        Final2 --> Empty2 : Invalid
        Final2 --> <b>UTXO2</b> : Valid
        <b>UTXO2</b> --> [*] : Final Datum
        }
        --
        ...
        --
        state ID_n {
        [*] --> Reviewing_n
        Reviewing_n --> Reviewing_n: Validator
        Reviewing_n --> Final3
        Final3 --> Empty3 : Invalid
        Final3 --> <b>UTXOn</b> : Valid
        <b>UTXOn</b> --> [*] : Final Datum
        }
    }
```



## Cardano Professional Developer 
Find Certificate at this link
