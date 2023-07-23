Tutorials page - https://docs.substrate.io/tutorials/
https://wiki.polkadot.network/docs/build-index
Concepts and architecture - https://docs.substrate.io/learn/
https://wiki.polkadot.network/docs/learn-index
https://wiki.polkadot.network/docs/build-open-source
https://assets.polkadot.network/Polkadot-whitepaper.pdf
# Running Substrate in Gitpod

0. Install prerequisites : https://docs.substrate.io/install/linux/
    * sudo apt install --assume-yes git clang curl libssl-dev protobuf-compiler
    * Install Rust:
        sudo apt install --assume-yes git clang curl libssl-dev protobuf-compiler  
        curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
        source $HOME/.cargo/env
￼       rustc --version
    * Configure the Rust toolchain to default to the latest stable 
        rustup default stable
        rustup update
    * Add the nightly release and the nightly WebAssembly (wasm) targets to your development environment
￼       rustup update nightly
        rustup target add wasm32-unknown-unknown --toolchain nightly
    * Verify configuration
        rustup show
        rustup +nightly show
    * rustup target add wasm32-unknown-unknown

## Building a local blockchain

1. Compile a substrate node using the Substrate node template
    * git clone https://github.com/substrate-developer-hub/substrate-node-template
    * cd substrate-node-template
    * cargo build --release
    * I got a protoc not found error - apt-get install protobuf-compiler
2. Start the local node
    ./target/release/node-template --dev
3. Install and start the front-end template
    * git clone https://github.com/substrate-developer-hub/substrate-front-end-template
    * cd substrate-front-end-template
    * yarn install
    * yarn start
4. Modifications needed for Gitpod
    * In substrate-front-end-template/src/config/development.json, change the websocket url to the gp url for the ws port.  
    Replace :
        ```
        {
            "PROVIDER_SOCKET": "ws://127.0.0.1:9944"
        }
        ```
        with:

        ```
        {
            "PROVIDER_SOCKET": "wss://9944-deepto98-blockchaininsu-21xewujl06p.ws-us102.gitpod.io/"
        }
        ```


5. Transfer funds from an account
    * Select dave from the list of available accounts to populate the address to which you are transferring funds.
    * Specify at least 1000000000000 as the amount to transfer, then click Submit.
    * Notice the  events in the Events table
￼
## Simulate a network
Now we'll start a private blockchain network with an authority set of private validators. The Substrate node template uses an authority consensus model that limits block production to a rotating list of authorized accounts. The authorized accounts—authorities—are responsible for creating blocks in a round robin fashion.

1. ￼Start the first blockchain node using a predefined network specification called local and running under predefined user accounts.
    * cd substrate-node-template
    * Purge old chain data  :  
    ./target/release/node-template purge-chain --base-path /tmp/alice --chain local
    * Start local blockchain node with the alice account:  
    ./target/release/node-template \
    --base-path /tmp/alice \
    --chain local \
    --alice \
    --port 30333 \
    --ws-port 9945 \
    --rpc-port 9933 \
    --node-key 0000000000000000000000000000000000000000000000000000000000000001 \
    --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \
    --validator  
    We get an error for the polkadot telemtry url, 
    Error while dialing /dns/telemetry.polkadot.io/tcp/443/x-parity-wss/%2Fsubmit%2F: Custom { kind: Other, error: Timeout } but its okay
    I read onine that these flags fix it, though it didn't for me --unsafe-rpc-external  --unsafe-ws-external --no-mdns --rpc-cors=all
    
2. Add second node - bob
    * Purge old chain data 
    ./target/release/node-template purge-chain --base-path /tmp/bob --chain local -y
    * Start second node :
    ./target/release/node-template \
    --base-path /tmp/bob \
    --chain local \
    --bob \
    --port 30334 \
    --ws-port 9946 \
    --rpc-port 9934 \
    --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" \
    --validator \
    --bootnodes /ip4/127.0.0.1/tcp/30333/p2p/12D3KooWEyoppNCUx8Yx66oV9fJnriXwCcXwDDUA2kj6vnc6iDEp

    * Now the nodes should connect to each other as peers and start producing blocks. the `Imported #2` logs represent the number of blocks currently mined

## Add trusted nodes
Start a small, standalone blockchain network with an authority set of private validators. Blockchains require the nodes in the network to agree on the state of data at any specific point in time and this agreement about the state is called consensus. The Substrate node template uses a proof of authority consensus model also referred to as authority round or Aura consensus. The Aura consensus protocol limits block production to a rotating list of authorized accounts. The authorized accounts—authorities—create blocks in a round robin fashion and are generally considered to be trusted participants in the network.

1. Generate your account and keys
    Till now, we used predefined keys, but now we'll generate our own secret keys. Options -  node-template key generate subcommand, subkey utlility, PolkadotJS web app, 3rd Party tools
    * Generate a random secret phrase and keys  
    ./target/release/node-template key generate --scheme Sr25519 --password-interactive  
    We  now have the Sr25519 (SS58) key for producing blocks using aura for one node.
    * The secret phase can be used to derive the keys
    test