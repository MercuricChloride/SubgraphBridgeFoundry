# SoulboundLabs Subgraph Bridge 📊🌉

### THIS CONTRACT IS NOT FINAL. I AM STILL MAKING SOME FINISHING DESIGN DECISIONS AND CLEANING THE CODE A BIT MORE.

A smart contract enabling for optimistic subgraph query results to be used on chain with configurable security params and query values. This allows for some interesting things, such as the graph as an L2 for offloading computation (this get's fun when substreams become a thing), as well as this technically enables composable subgraphs. As you can bridge a subgraph result on chain, and create another subgraph that tracks this contract, allowing a subgraph to use the data from another subgraph. Snazzy!

There are some more detailed docs in the `docs/SubgraphBridgeDocs.md` file.

---

# Subgraph Bridge Speedrun 🏃💨

1. Create a subgraph query to use that queries a subgraph at a specific blockhash. (IF YOU DON'T MAKE IT FOR A BLOCKHASH IT WILL NOT WORK, and we use blockhash rather than blocknumber to protect against reorgs)
2. Format it in a very specific way (we are making an interface and sdk to make this super easy)
3. Trim the blockhash from the formatted query string and split the query into two parts (before blockhash, after blockhash) and convert to hex. (This allows us to verify what new requests should be when they are posted on chain);
4. Configure your params for creation of a subgraph bridge and call the function `createSubgraphBridge()` passing in your bridge config!
5. After you create a new bridge, query your subgraph with the same query swapping in a different block hash. Pass the results of the query into `postSubgraphResponse()` to add it to the list of proposed results for that CID.
6. After the configured subgraph bridge dispute period ends, call `certifySubgraphResponse()` for your data to add it to the on chain subgraphBridgeData if it hasn't been disputed.
7. Use your subgraph bridge data on chain from a smart contract by the function `subgraphBridgeData()`

---

# Tests 🧪

Requirements: Foundry [Link to foundry, it's really great](https://book.getfoundry.sh/)

The tests are run with queries to the HOP mainnet subgraph. [Link to subgraph](https://thegraph.com/explorer/subgraphs/Cjv3tykF4wnd6m9TRmQV7weiLjizDnhyt6x2tTJB42Cy?view=Playground)

Because of the $GRT balance checks we use for the subgraph attestations, it is easiest to just fork mainnet. You technically don't need to fork from a block number, but just in case an indexer stops indexing the subgraph, you should just run the command below to test.

command for testing: `forge test --fork-url https://eth-mainnet.g.alchemy.com/v2/<YOUR API KEY> --fork-block-number 15984600`

---

An open source public good made with ❤️
