# 🚀 Starknet: De cero a mainnet

## 📋 Agenda

🛠️ **Instalación y Configuración**
🚀 **Primeros Pasos**
💻 **Desarrollo Local**
👤 **Gestión de Cuentas**
🧪 **Deploy en Sepolia (Testnet)**
🌐 **Deploy en Mainnet**
⚡ **Usar scaffold-stark-2**
🤖 **Usar Cairo Coder**

## Starkup - Gestor de herramientas de Starknet

Starkup es el gestor oficial de herramientas para el desarrollo en Starknet. Instala y gestiona automáticamente las versiones de Scarb, snforge, sncast y starknet-devnet.

**Repositorio oficial**: https://github.com/software-mansion/starkup

### Instalación automática

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.starkup.sh | sh
```

### Verificar instalación

Después de la instalación, verifica que todas las herramientas estén disponibles:

```bash
scarb --version
snforge --version && sncast --version
starknet-devnet --version
```

```
scarb 2.11.4 (c0ef5ec6a 2025-04-09)
cairo: 2.11.4 (https://crates.io/crates/cairo-lang-compiler/2.11.4)
sierra: 1.7.0

snforge 0.45.0
sncast 0.45.0
starknet-devnet 0.4.3
```

### Herramientas instaladas

- **Scarb**: Gestor de paquetes y compilador para Cairo
- **snforge**: Framework de testing para contratos de Starknet
- **sncast**: CLI para interactuar con contratos de Starknet
- **starknet-devnet**: Red local para desarrollo y testing

## Crear un nuevo proyecto

Para comenzar con Starknet, crea un nuevo proyecto usando Scarb:

```bash
scarb new hello_starknet
```

Este comando creará una nueva carpeta `hello_starknet` con la estructura básica de un proyecto de Cairo/Starknet.

## Compresión del contrato

Un contrato simple para gestionar el saldo. En concreto:

#### Estructura del contrato

El contrato se define encapsulando el estado y la lógica dentro de un módulo anotado con el `#[starknet::contract]` atributo.

#### Interfaz del contrato

La lógica que el contrato expone al mundo exterior está representada por su rasgo de interfaz, anotado con el `#[starknet::interface]` atributo. Aquí, nuestro contrato define y expone públicamente las funciones `increase_balance` y `get_balance`.

#### Estado del contrato

El estado se define dentro de la estructura `Storage`, que siempre se inicializa vacía. Aquí, nuestra estructura contiene un único campo llamado `balance` de tipo `felt252`.

#### Implementación de la lógica

La lógica se define en el bloque de implementación y se anota con el `#[abi(embed_v0)]` atributo para exponer las implementaciones al mundo exterior. Aquí, `increase_balance` se usa el método `write` para incrementar `balance` en `amount` y `get_balance` se usa el método `read` para devolver el valor de `balance`.

### Ejemplos avanzados

Para ejemplos más avanzados, visita: https://starknet-by-example.voyager.online/

## Compilar el contrato

```bash
scarb build
```

# Declarar, desplegar e interactuar con el contrato HelloStarknet localmente

## Iniciar una instancia local de Starknet

```bash
starknet-devnet --seed 0
```

Incluye un par de cuentas que ya estan deployadas.

```bash
sncast account import \
    --url http://127.0.0.1:5050 \
    --name test_local \
    --address 0x064b48806902a367c8598f4f95c305e8c1a1acba5f082d294a43793113115691 \
    --private-key 0x0000000000000000000000000000000071d7bb07b9a64f6f78ac4c816aff4da9 \
    --type oz
```

## Declarar el contrato

Antes de que un contrato pueda ser deployado en Starknet, su código compilado debe enviarse a la red (también conocido como declararlo).

```bash
sncast --account test_local declare \
--url http://127.0.0.1:5050 \
--contract-name HelloStarknet
```

El class hash del contrato puede ser usado para deployar otra instancia del mismo

## Deployar el contrato

```bash
sncast --account test_local \
deploy \
--url http://127.0.0.1:5050 \
--class-hash0x051e0d3b26fb79035afdc64d2214eb18291629b4f2ef132e79c3f3fbe1ba57c4 \
--salt 0
```

## Interactuar con el contrato

**Llamar función (solo lectura)**

```bash
sncast --account test_local \
call \
--url http://127.0.0.1:5050 \
--contract-address 0x04035c4db7822523478996bfc2b80d925e671279bb99ed1fb4e4fcc222e344e6 \
--function get_balance
```

**Invocar función (cambiar estado)**

```bash
sncast --account test_local \
invoke \
--contract-address 0x04035c4db7822523478996bfc2b80d925e671279bb99ed1fb4e4fcc222e344e6 \
--url http://127.0.0.1:5050 \
--function increase_balance \
--arguments 42
```

*Tip: Para debuggear agregar prints en el contrato y los veras reflejados en la terminal.*

# Gestion de cuentas

### Opción 1: Crear una cuenta con sncast

```bash
sncast account create \
--url https://starknet-sepolia.public.blastapi.io/rpc/v0_8 \
--name account_sepolia
```

Prefinanciamos la cuenta con la red de Sepolia.

```bash
sncast account deploy \
--url https://starknet-sepolia.public.blastapi.io/rpc/v0_8 \
--name account_sepolia
```

### Opción 2: Importar una cuenta (OZ, Argent, Braavos)

**OpenZeppelin**

```bash
sncast \
    account import \
    --url https://starknet-sepolia.public.blastapi.io/rpc/v0_8 \
    --name my_imported_account \
    --address 0x1 \
    --private-key 0x2 \
    --type oz
```

**Argent**

```bash
sncast \
    account import \
    --url https://starknet-sepolia.public.blastapi.io/rpc/v0_8 \
    --name account_argent \
    --address 0x1 \
    --private-key 0x2 \
    --type argent
```

**Braavos**

```bash
sncast \
    account import \
    --url https://starknet-sepolia.public.blastapi.io/rpc/v0_8 \
    --name account_braavos \
    --address 0x1 \
    --private-key 0x2 \
    --type braavos
```

# Deploy en Sepolia

## Crear una cuenta de la red de Sepolia

```bash
sncast account create \
--url https://starknet-sepolia.public.blastapi.io/rpc/v0_8 \
--name dojo_sepolia
```

Prefinanciamos la cuenta con la red de Sepolia.

```bash
sncast account deploy \
--url https://starknet-sepolia.public.blastapi.io/rpc/v0_8 \
--name dojo_sepolia
```

## Declarar el contrato

```bash
sncast --account dojo_sepolia \
declare \
--url https://starknet-sepolia.public.blastapi.io/rpc/v0_8 \
--contract-name HelloStarknet
```

## Deployar el contrato

```bash
sncast --account dojo_sepolia \
deploy \
--url https://starknet-sepolia.public.blastapi.io/rpc/v0_8 \
--class-hash 0x051e0d3b26fb79035afdc64d2214eb18291629b4f2ef132e79c3f3fbe1ba57c4 \
```

## Interactuar con el contrato

**Llamar función (solo lectura)**

```bash
sncast --account dojo_sepolia \
call \
--url https://starknet-sepolia.public.blastapi.io/rpc/v0_8 \
--contract-address 0x02397df2a299a5b6c3a429578ac7dc26aa49117e4ceca7b53d15e177c15facf1 \
--function get_balance
```

**Invocar función (cambiar estado)**

```bash
sncast --account dojo_sepolia \
invoke \
--url https://starknet-sepolia.public.blastapi.io/rpc/v0_8 \
--contract-address 0x02397df2a299a5b6c3a429578ac7dc26aa49117e4ceca7b53d15e177c15facf1 \
--function increase_balance \
--arguments 42
```

# Deploy en Mainnet

## Importar una cuenta de la red de Mainnet

```bash
sncast \
account import \
--url https://starknet-mainnet.public.blastapi.io/rpc/v0_8 \
--name dojo_mainnet_braavos \
--address 0x1 \
--private-key 0x1 \
--type braavos
```


## Declarar el contrato

```bash
sncast --account dojo_mainnet_braavos \
declare \
--url https://starknet-mainnet.public.blastapi.io/rpc/v0_8 \
--contract-name HelloStarknet
```

## Deployar el contrato

```bash
sncast --account dojo_mainnet_braavos \
deploy \
--url https://starknet-mainnet.public.blastapi.io/rpc/v0_8 \
--class-hash 0x051e0d3b26fb79035afdc64d2214eb18291629b4f2ef132e79c3f3fbe1ba57c4 
```

## Interactuar con el contrato

**Llamar función (solo lectura)**

```bash
sncast --account dojo_mainnet_braavos \
call \
--url https://starknet-mainnet.public.blastapi.io/rpc/v0_8 \
--contract-address 0x06b3af50d1e8e4bf3e66efc38568f6c25d5bcc64d413afd5c53779ae79edfc2b \
--function get_balance
```

**Invocar función (cambiar estado)**

```bash
sncast --account dojo_mainnet_braavos \
invoke \
--url https://starknet-mainnet.public.blastapi.io/rpc/v0_8 \
--contract-address 0x06b3af50d1e8e4bf3e66efc38568f6c25d5bcc64d413afd5c53779ae79edfc2b \
--function increase_balance \
--arguments 42
```

# Recursos adicionales

## Faucets para obtener ETH de prueba

- https://starknet-faucet.vercel.app/
- https://www.alchemy.com/faucets/starknet-sepolia

## Endpoints RPC

- **Sepolia**: https://starknet-sepolia.public.blastapi.io/rpc/v0_8
- **Mainnet**: https://starknet-mainnet.public.blastapi.io/rpc/v0_8
- **Local**: http://127.0.0.1:5050

## Herramientas y utilidades

- **Documentación oficial**: https://docs.starknet.io/tools/overview/
- **Convertidor de unidades**: https://www.stark-utils.xyz/converter
- **Explorador de bloques**: https://starkscan.co/

## Recursos de aprendizaje

- **Recursos de Dojo Coding**: https://dojocoding.notion.site/Dojo-Coding-Startup-House-Resources-231b467dc20c80fa8b8de86d086f2ca4
- **Inicio rápido de Starknet**: https://docs.starknet.io/guides/quickstart/overview/
- **Starknet por ejemplos**: https://starknet-by-example.voyager.online/
- **BlastAPI**: https://blastapi.io/public-api/starknet
