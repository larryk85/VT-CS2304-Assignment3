# VT-CS2304-Assignment3

To stop the running nodeos instance run the command:
  pkill nodeos
  
To start/restart nodeos:
  nodeos --config-dir ~/eosio/chain/config --data-dir ~/eosio/chain/data -e -p eosio --plugin eosio::chain_api_plugin --contracts-console
  
To reset the blockchain:
  rm -rf ~/eosio/chain/data
