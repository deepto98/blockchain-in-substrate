# Building a local blockchain with Substrate in Gitpod
0. Install prerequisites : https://docs.substrate.io/install/linux/
    * sudo apt install --assume-yes git clang curl libssl-dev protobuf-compiler
    * Install Rust:
        sudo apt install --assume-yes git clang curl libssl-dev protobuf-compiler  
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

1. Compile a substrate node using the Substrate node template
    * git clone https://github.com/substrate-developer-hub/substrate-node-template
    * cd substrate-node-template
    * cargo build --
    * I got a protoc not found error - apt-get install protobuf-compiler
    * rustup target add wasm32-unknown-unknown
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


￼


￼

