# How to deploy router swap

## 0. compile

```shell
make all
```
run the above command, it will generate `./build/bin/swaprouter` binary.

## 1. deploy `AnyswapRouter`
Binance testnet
deploy a `AnyswapRouter` contract for each supported blockchain
https://etherscan.io/address/0xe95fd76cf16008c12ff3b3a937cb16cd9cc20284#code
factory - адрес создателя 
wNative - wrapped coin 
https://etherscan.io/address/0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2#code
https://testnet.bscscan.com/address/0x1350d3f31d9f805626980fc37e6163ed872f2e45#code

mcp - адрес ноды (кто работает с контрактом и делает свапы)

testnet https://testnet.bscscan.com/address/0x69558d860103e420013fadde75f81b54a06d728f#code

======================================================

xDai testnet
Xdait testnet - Wrapped POI
https://blockscout.com/poa/sokol/address/0x7C8F2b4D30A98A9Abcdeac3cf49d51f92a6dd81d/contracts

AnyswapRouter
https://blockscout.com/poa/sokol/address/0x67AAf7aB10d4273023ddEEad302Bc9a7E7951eC2/contracts


Polygon testnet
Wrapped Matic (wNative)
https://mumbai.polygonscan.com/address/0xb0ca6c34cfad6dbb5b0423f421584faf718f3a7e
Router
https://mumbai.polygonscan.com/address/0x20aabbc7752457e980c53ac7bc8c8a72df2aa4eb

## 2. deploy `AnyswapERC20`
Binance testnet
deploy a `AnyswapERC20` contract for each token on each blockchain
constructor(string memory _name, string memory _symbol, uint8 _decimals, address _underlying, address _vault) {
underlying - адрес токена ERC20
vault - адресс AnyswapRouter`
minter - адресс AnyswapRouter`

https://testnet.bscscan.com/address/0xd71a1bbabb389f3af78633e040bd994a99210c59#code ( исправлен minter - указывается в контракте, в оригинале нужно отдельно вызывать функцию и ждать два дня для подтверждения добавления нового минтера)

https://testnet.bscscan.com/address/0xc0c3394781c23faa538a506b3c96fb59c050bed8 (новый ваулт)
AnySwapTether
основной токен https://testnet.bscscan.com/address/0x6e9c98a8a481bf038ba7e1d669a0086547dd144e


xDai testnet

AnySwapTether
https://blockscout.com/poa/sokol/address/0x52C72781aB6B5FE83E0B5c6B0F890D5B08aff6E4/contracts

https://blockscout.com/poa/sokol/address/0x821FCbC717acD26B1Cc582D29Cc3CDcc696C3e2d/contracts (новый ваулт)
основной токен
https://blockscout.com/poa/sokol/address/0x35bE4cA2f4C66861b6B3B9E6F35A7d8FDaD48dea/contracts
=====================================================

Polygon testnet
основной токен
https://mumbai.polygonscan.com/address/0xd2774939f988ee4545822333105ef4554f44496c

AnySwapTether
https://mumbai.polygonscan.com/address/0xdf46b199612dfcc4617e7ce627d9464aa914b173
## 3. deploy `RouterConfig`

deploy a `RouterConfig` contract to store router bridge configs
https://bscscan.com/address/0xbccff1c6c1ca650f533d3dcc5f4722aae6718337#code

тестовый https://testnet.bscscan.com/address/0xf37f50d6bcff79e7f4f45ab87de136e05559c838#code



## 4. set router config on chain

call `RouterConfig` contract to set configs on blcokchain.

The following is the most used functions, please ref. the abi for more info.

### 4.1 set chain config

call the following contract function:

```solidity
setChainConfig(uint256 chainID, ChainConfig config)
```

input data can be generated by the following method.

```shell
./build/bin/swaprouter config genSetChainConfigData --c.ChainID 4 --c.BlockChain eth --c.RouterContract 0x3302f922b24420f3a3048dddc4e2761ce37ea098 --c.Confirmations 3 --c.InitialHeight 0

Output:

chain config struct is {
  "ChainID": "4",
  "BlockChain": "eth",
  "RouterContract": "0x3302f922b24420f3a3048dddc4e2761ce37ea098",
  "Confirmations": 3,
  "InitialHeight": 0
}
set chain config input data is 0xdefb3a0d0000000000000000000000000000000000000000000000000000000000000004000000000000000000000000000000000000000000000000000000000000004000000000000000000000000000000000000000000000000000000000000000800000000000000000000000003302f922b24420f3a3048dddc4e2761ce37ea0980000000000000000000000000000000000000000000000000000000000000003000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000036574680000000000000000000000000000000000000000000000000000000000


solidy/web3 call args
bnb testnet
97,["tbnb", "0x69558d860103e420013fadde75f81b54a06d728f", 3, 0]
https://testnet.bscscan.com/tx/0x9eb04455c49d5bb9507fee0dcfcace7981066773a5f8124270d33b9ac5852b90

xdai testnet
77,["txdai", "0x67AAf7aB10d4273023ddEEad302Bc9a7E7951eC2", 3, 0]
https://testnet.bscscan.com/tx/0x2879aa65033d43f0968c3b487dc142940a3245a64113476f3368b1409ed2864e

```

### 4.2 set token config

call the following contract function:

```solidity
setTokenConfig(string tokenID, uint256 chainID, TokenConfig config)



```

input data can be generated by the following method.

```shell
./build/bin/swaprouter config genSetTokenConfigData --c.ChainID 46688 --c.TokenID test --c.Decimals 18 --c.ContractAddress 0xb302f922b24420f3a3048dddc4e2761ce37ea098 --c.ContractVersion 4

Output:

tokenID is test
chainID is 46688
token config struct is {
  "TokenID": "test",
  "Decimals": 18,
  "ContractAddress": "0xb302f922b24420f3a3048dddc4e2761ce37ea098", - AnyswapERC20
  "ContractVersion": 4
}
set token config input data is 0xba6e0d0f00000000000000000000000000000000000000000000000000000000000000a0000000000000000000000000000000000000000000000000000000000000b6600000000000000000000000000000000000000000000000000000000000000012000000000000000000000000b302f922b24420f3a3048dddc4e2761ce37ea098000000000000000000000000000000000000000000000000000000000000000400000000000000000000000000000000000000000000000000000000000000047465737400000000000000000000000000000000000000000000000000000000


dolidy/web3 call args
bnb testnet
"TETHER", 97, [8,"0xc0c3394781c23faa538a506b3c96fb59c050bed8",6]
https://testnet.bscscan.com/tx/0x5aba55ec09696605f05f4ccceb1682c2145c3ef2ff58a6814c7503eac04c88c1

xdai testnet
"TETHER", 77, [8,"0x821FCbC717acD26B1Cc582D29Cc3CDcc696C3e2d",6]
https://testnet.bscscan.com/tx/0xc7c91f839a5c6a1ed646c3a7b3a784c32075d75b61f0a36f37349592c39c56ea
```

### 4.3 set swap config

call the following contract function:

```solidity
setSwapConfig(string tokenID, uint256 toChainID, SwapConfig config)
```

**Notice: you should set swap config for all tokenIDs and all toChainIDs.**

input data can be generated by the following method.

```shell
./build/bin/swaprouter config genSetSwapConfigData --c.ToChainID 46688 --c.TokenID test --c.MaximumSwap 1000000 --c.MinimumSwap 100 --c.BigValueThreshold 100000 --c.SwapFeeRate 0.001 --c.MaximumSwapFee 10 --c.MinimumSwapFee 1.5

Output:

tokenID is test
toChainID is 46688
swap config struct is {
  "MaximumSwap": 1000000000000000000000000,
  "MinimumSwap": 100000000000000000000,
  "BigValueThreshold": 100000000000000000000000,
  "SwapFeeRatePerMillion": 1000,
  "MaximumSwapFee": 10000000000000000000,
  "MinimumSwapFee": 1500000000000000000
}
set swap config input data is 0xca29ee960000000000000000000000000000000000000000000000000000000000000100000000000000000000000000000000000000000000000000000000000000b66000000000000000000000000000000000000000000000d3c21bcecceda10000000000000000000000000000000000000000000000000000056bc75e2d6310000000000000000000000000000000000000000000000000152d02c7e14af680000000000000000000000000000000000000000000000000000000000000000003e80000000000000000000000000000000000000000000000008ac7230489e8000000000000000000000000000000000000000000000000000014d1120d7b16000000000000000000000000000000000000000000000000000000000000000000047465737400000000000000000000000000000000000000000000000000000000



solidity/web3 call args
test bnb
"TETHER", 97, ["1000000000000000000000000", "100000000000000000000", "100000000000000000000000", "1000","10000000000000000000","1500000000000000000"]

https://testnet.bscscan.com/tx/0x5a6b68b5c8c18d7c7f71aa93a4657835686081724bd88bc8dc071b22c0b15a27

test xdai
"TETHER", 77, ["1000000000000000000000000", "100000000000000000000", "100000000000000000000000", "1000","10000000000000000000","1500000000000000000"]
https://testnet.bscscan.com/tx/0xfb6a35600b7cbf042d5bd50f43ea3cdafef1e738059c4e24e471c6cdba58bb9d
```

### 4.4 set mpc address's public key

call the following contract function:

```solidity
setMPCPubkey(address addr, string pubkey)


"0x7594E0bC1EB3592E773E29F5d9ec64e8a7F35789", "0x021954cd587cf07b6e94662e28b1ac749424b9b0df974c61a9b41b16a117ca0306"

https://testnet.bscscan.com/tx/0x1ad298b772a0d682711bfcd7bc89b2481184f3a959fb0df99b313d892a298059
```

## 5. add local config file

please ref. [config-example.toml](https://github.com/anyswap/CrossChain-Router/blob/main/params/config-example.toml)

### 5.1 Config MPC key

https://github.com/ethereum/wiki/wiki/Web3-Secret-Storage-Definition

## 6. run swaprouter

```shell
# for server run (add '--runserver' option)
setsid ./build/bin/swaprouter --config config.toml --log logs/routerswap.log --runserver

# for oracle run
setsid ./build/bin/swaprouter --config config.toml --log logs/routerswap.log
```

## 7. sub commands

get all sub command list and help info, run

```shell
./build/bin/swaprouter -h
```

sub commands:

`admin` is admin tool

`config` is tool to process and query config data

## 8. RPC api

please ref. [server rpc api](https://github.com/anyswap/CrossChain-Router/blob/main/rpc/README.md)