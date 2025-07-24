# Vault

## Prompt

Implementa un contrato Vault sencillo en Cairo utilizando OpenZeppelin para StarkNet 2.0.0.
El contrato debe permitir al owner depositar y retirar tokens STRK (un ERC20 StarkNet).

Requisitos:

Nombre del contrato: Vault

El contrato setea la dirección del token STRK(0x04718f5a0Fc34cC1AF16A1cdee98fFB20C31f5cD61D6Ab07201858f4287c938D) en el constructor.

Implementa las funciones deposit y withdraw.

Solo el owner puede ejecutar estas funciones.

La lógica debe usar la interfaz IERC20 de OpenZeppelin 2.0.0 para transferencias.

No incluyas pruebas ni mocks, solo el contrato Cairo completo.


Aca tienes un ejemplo de como se implementa en Solidity:
---

### 🔐 Contrato Vault en Solidity 

```solidity
pragma solidity ^0.5.0;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/math/SafeMath.sol";

contract Vault {
    using SafeMath for uint256;

    IERC20 public strkToken;
    address public owner;

    mapping(address => uint256) public balances;
    address public strkToken;

    constructor() public {
        strkToken = 0x0000000000000000000000000000000000000000000000000000000000000000;
        owner = msg.sender;
    }

    function deposit(uint256 _amount) external {
        require(_amount > 0, "Amount must be greater than 0");
        require(msg.sender == owner, "Only owner can deposit");
        require(strkToken.transferFrom(msg.sender, address(this), _amount), "Transfer failed");

        balances[msg.sender] = balances[msg.sender].add(_amount);
    }

    function withdraw() external {
        require(msg.sender == owner, "Only owner can withdraw");
        require(balances[msg.sender] > 0, "Insufficient balance");

        uint256 balance = balances[msg.sender];
        balances[msg.sender] = 0;
        require(strkToken.transfer(msg.sender, balance), "Withdraw transfer failed");
    }
}
```

## Deploy

### Declarar el contrato

```bash
sncast --account dojo_mainnet_braavos \
declare \
--url https://starknet-mainnet.public.blastapi.io/rpc/v0_8 \
--contract-name Vault
```

### Deployar el contrato

```bash
sncast --account dojo_mainnet_braavos \
deploy \
--url https://starknet-mainnet.public.blastapi.io/rpc/v0_8 \
--class-hash 0x00de0bb07e63ed68f4f9180266ab391b680c6385d35d6c8e3a4a8abb57004434 \
--arguments '0x016337481939d86cd684e4bfacca4d37453c3a5ec69f19b77148d372b527e7be'
```
