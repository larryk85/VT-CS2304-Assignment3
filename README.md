# VT-CS2304-Assignment3

To stop the running nodeos instance run the command:
  ```sh 
  pkill nodeos
  ```
  
To start/restart nodeos:
  ```sh
  nodeos --config-dir ~/eosio/chain/config --data-dir ~/eosio/chain/data -e -p eosio --plugin eosio::chain_api_plugin --contracts-console
  ```
  
To reset the blockchain:
  ```sh
  rm -rf ~/eosio/chain/data
  ```

## Account contract
The contract to be used for each of your test accounts should only contain one action `resolve(name acnt)`.  You can add more if you'd like for debugging purposes.  This contract should also have notification handlers for `deposit`, `transfer`, `createcontract`, and `release`.  For this project simply print the arguments of the handlers with a message signifying what handler we are in and what action was executed successfully or unsuccessfully.  The `resolve` action should require authorization of the account that the contract is deployed on and that `acnt` is the same as the account which the contract is deployed on.  It should notify the escrow account.

## Escrow contract
For the first part of this assignment you should create a smart contract that fulfills the role of an escrow account.  You can you the simple token contract from the example token.hpp|cpp from Canvas.  This contract should be able to hold balances for users and release/transfer the tokens when it has received notification that both parties have successfully completed whatever their contract obligations were.

### Escrow invariants
1) The contract should only allow assets to have a symbol of "SYS" with a precision of 4 and "VT" with a precision of 2.

2) This contract should have the following actions:
    - deposit(name acnt, asset amt)
    - transfer(name from, name to, asset amt)
    - createcontract(name acnt1, name acnt2, asset amt)
    - release(name acnt)

3) This contract should have the following tables:
    - holdings
    - contracts
    - metrics

4) This contract should have the following notification handler:
   - resolve(name acnt)
 
## Actions
### deposit
This action should validate that `acnt` is a valid on chain account.  The asset is valid based on invariant 1.  This should notify `acnt` that his deposit was made.  This should require the authorization of `acnt`

### transfer
This action should validate that `from` and `to` are valid on chain accounts.  That `from` and `to` are in an active contract together.  This should then transfer the funds between the two parties deposited balances and delete/inactive the current contract together.  This action should also ensure that enough funds are available to complete the transfer, this assertion message should signify that something bad went wrong.  This should notify both parties that the transfer was successful. This should require the authorization of the account that the escrow contract is deployed to.

### createcontract
This action should validate that `acnt1` and `acnt2` are valid on chain accounts.  These should then create a new "contract" and create an active row in the `contracts` table.  This should validate that both parties have enough deposited funds to fulfill the future transfer.  This should notify both parties that the contract was successfully created.  This should require the authorization of both `acnt1` and `acnt2`.

### release
This action should validate that `acnt` is a valid on chain account.  This should validate that no active contracts are open with this account.  If any active contracts exist, then invalidate all currently open contracts and notify all parties of these contracts. Else, notify `acnt` that their release of funds has succeeded.  For abstraction, this should simply take the balance of the account to zero and remove the row from the table. This should require the authorization of `acnt`.

## Notification Handlers
### resolve
This should be handling a notification from one of the active parties that they have resolved their side of the contract.  This should require the authorization of `acnt`.  This should send an inline action to itself to do the transfer, once it has validated that the other account has already "resolved".

## Tables
### holdings
This table should keep records of what each account that deposited tokens and how much they have.

### contracts
This table should keep records of which parties are in a current contract with each other and how much the escrow is. This should also keep track of whether each party has fulfilled their side of the contract.

### metrics
This table should also keep records per account of how many successful contracts have been completed.  It should also keep records of how many total contracts have been created with that account. And lastly keep records of how many releases this account has initiated themselves.

## Assignment
1) Create 3 accounts `escrow`, `testa`, `testb`.  These will house either the escrow contract or the account contract.
2) Just follow the invariants mentioned above when writing your smart contracts.
3) Submit the source for your escrow.hpp|cpp and account.hpp|cpp.
