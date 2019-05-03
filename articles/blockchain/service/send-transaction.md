---
title: Enviar transações com o serviço do Azure Blockchain
description: Tutorial sobre como utilizar o serviço do Azure Blockchain para implementar um contrato inteligente e enviar uma transação privada.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: d3ad4cdfe33948c04c278ed3dfef7aa6fda637ab
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026810"
---
# <a name="tutorial-send-transactions-using-azure-blockchain-service"></a>Tutorial: Enviar transações com o serviço do Azure Blockchain

Neste tutorial, irá criar nós de transação para testar a privacidade de contrato e de transação.  Usará Truffle para criar um ambiente de desenvolvimento local e implementar um contrato inteligente e enviar uma transação privada.

Vai aprender a:

> [!div class="checklist"]
> * Adicionar nós de transação
> * Utilizar Truffle para implementar um contrato inteligente
> * Enviar uma transação
> * Validar a privacidade de transação

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Completa [criar um membro de blockchain com o portal do Azure](create-member.md)
* Completa [início rápido: Utilizar Truffle para ligar a uma rede de consórcio](connect-truffle.md)
* Truffle requer várias ferramentas a serem instalados incluindo [node. js](https://nodejs.org), [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git), e [Truffle](https://github.com/trufflesuite/truffle).

    Para configurar rapidamente no Windows 10, instale [Ubuntu no Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6) para um shell Unix Bash terminal, em seguida, instale [Truffle](https://github.com/trufflesuite/truffle). O Ubuntu na distribuição do Windows inclui o node. js e Git.

* Instalar [Visual Studio Code](https://code.visualstudio.com/Download)
* Instalar [extensão solidez de código do Visual Studio](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity)

## <a name="create-transaction-nodes"></a>Criar nós de transação

Por padrão, tem um nó de transação. Vamos adicionar mais dois. Um de nós no qual participam na transação privada. A outra não está incluída na transação privada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para o Azure Blockchain membro e selecione **nós de transação > Adicionar**.
1. Concluir as definições para um novo nó de transação com o nome `alpha`.

    ![Criar o nó de transação](./media/send-transaction/create-node.png)

    | Definição | Valor | Descrição |
    |---------|-------|-------------|
    | Name | `alpha` | Nome de nó da transação. O nome é utilizado para criar o endereço DNS para o ponto de final do nó de transação. Por exemplo, `alpha-mymanagedledger.blockchain.azure.com`. |
    | Palavra-passe | palavra-passe segura | A palavra-passe é utilizada para aceder ao ponto de final de nó de transação com a autenticação básica.

1. Selecione **Criar**.

    Um novo nó de transação o aprovisionamento demora cerca de 10 minutos.

1. Repita os passos 2 a 4 para adicionar um nó de transação com o nome `beta`.

Pode continuar com o tutorial enquanto os nós estão a ser aprovisionados. Quando o aprovisionamento estiver concluído, terá três nós de transação.

## <a name="open-truffle-project"></a>Abrir projeto Truffle

1. Abra um terminal de shell de Bash.
1. Alterar o seu caminho para o diretório de projeto Truffle do pré-requisito [início rápido: Utilizar Truffle para ligar a uma rede de consórcio](connect-truffle.md). Por exemplo,

    ```bash
    cd truffledemo
    ```

1. Inicie a consola de desenvolvimento interativo do Truffle.

    ``` bash
    truffle develop
    ```

    Truffle cria um blockchain de desenvolvimento local e fornece uma consola interativa.

## <a name="connect-to-transaction-node"></a>Ligar ao nó de transação

Utilize Web3 para ligar ao nó de transação de padrão e criar uma conta. Pode obter a cadeia de ligação Web3 do portal do Azure.

1. No portal do Azure, navegue até o nó de transação do padrão e selecione **nós de transação > código de exemplo > Web3**.
1. Copiar a partir de JavaScript **HTTPS (chave de acesso 1)** ![Web3 código de exemplo](./media/send-transaction/web3-code.png)

1. Cole o código de Web3 JavaScript para o nó de transação de predefinição na consola de desenvolvimento interativo Truffle. O código cria um objeto de Web3 que está ligado ao seu nó de transação de serviço do Azure Blockchain.

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    ```

    Pode chamar métodos no objeto Web3 para interagir com o nó de transação.

1. Crie uma nova conta no nó de transação de predefinição. Substitua o parâmetro de palavra-passe a sua própria palavra-passe segura.

    ```bash
    web3.eth.personal.newAccount("1@myStrongPassword");
    ```

    Tomar nota do endereço conta retornado e a palavra-passe utilizada para a próxima seção.

1. O ambiente de desenvolvimento Truffle de saída.

    ```bash
    .exit
    ```

## <a name="configure-truffle-project"></a>Configurar o projeto de Truffle

Para configurar o projeto de Truffle, precisa de algumas informações de nó de transação do portal do Azure.

### <a name="transaction-node-public-key"></a>Chave pública de nó de transação

Cada nó de transação tem uma chave pública. A chave pública permite-lhe enviar uma transação privada ao nó. Para enviar uma transação a partir do nó de transação de padrão para o *alpha* nó da transação, precisa a *alpha* chave pública do nó de transação.

Pode obter a chave pública na lista de nó de transação. Copie a chave pública para o nó alfa e guarde o valor para mais tarde no tutorial.

![Lista de nós de transação](./media/send-transaction/node-list.png)

### <a name="transaction-node-endpoint-addresses"></a>Endereços de ponto final do nó de transação

1. No portal do Azure, navegue para cada nó de transação e selecione **nós de transação > cadeias de ligação**.
1. Copie e guarde o URL de ponto final de * * HTTPS (chave de acesso 1) para cada nó de transação. Terá dos endereços de ponto final para o ficheiro de configuração inteligente contrato mais tarde no tutorial.

    ![Endereço de ponto final da transação](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Editar ficheiro de configuração

1. Inicie o Visual Studio Code e abrir o Truffle projeto directory pasta com o **ficheiro > Abrir pasta** menu.
1. Abra o ficheiro de configuração Truffle `truffle-config.js`.
1. Substitua o conteúdo do ficheiro com as seguintes informações de configuração. Adicione as variáveis que contém os endereços de pontos finais e as informações de conta. Substitua as secções de colchete valores recolhidos a partir de secções anteriores.

``` javascript
var defaultnode = "<default transaction node connection string>";
var alpha = "<alpha transaction node connection string>";
var beta = "<beta transaction node connection string>";

var myAccount = "<account address>";
var myPassword = "<account password>";

var Web3 = require("web3");
```

Adicione o código de configuração para o **module.exports** secção da configuração.

```javascript
module.exports = {
  networks: {
    defaultnode: {
      provider:(() =>  {
      const AzureBlockchainProvider = new Web3.providers.HttpProvider(defaultnode);

      const web3 = new Web3(AzureBlockchainProvider);
      web3.eth.personal.unlockAccount(myAccount, myPassword);

      return AzureBlockchainProvider;
      })(),

      network_id: "*",
      gas: 0,
      gasPrice: 0,
      from: myAccount
    },
    alpha: {
      provider: new Web3.providers.HttpProvider(alpha),
      network_id: "*",
      gas: 0,
      gasPrice: 0
    },
    beta: {
      provider: new Web3.providers.HttpProvider(beta),
      network_id: "*",
      gas: 0,
      gasPrice: 0
    }
  }
}
```

## <a name="create-smart-contract"></a>Criar o contrato inteligente

Na pasta **contratos**, crie um novo ficheiro designado `SimpleStorage.sol`. Adicione o seguinte código.

```solidity
pragma solidity >=0.4.21 <0.6.0;

contract SimpleStorage {
    string public storedData;

    constructor(string memory initVal) public {
        storedData = initVal;
    }

    function set(string memory x) public {
        storedData = x;
    }

    function get() view public returns (string memory retVal) {
        return storedData;
    }
}
```

Na pasta **migrações**, crie um novo ficheiro designado `2_deploy_simplestorage.js`. Adicione o seguinte código.

```solidity
var SimpleStorage = artifacts.require("SimpleStorage.sol");

module.exports = function(deployer) {

  // Pass 42 to the contract as the first constructor parameter
  deployer.deploy(SimpleStorage, "42", {privateFor: ["<alpha node public key>"], from:"<Account address>"})  
};
```

Substitua os valores nos parênteses angulares.

| Value | Descrição
|-------|-------------
| \<chave pública do nó alfa\> | Chave pública do nó alfa
| \<Endereço da conta\> | Endereço da conta criado no nó de transação de predefinição.

Neste exemplo, o valor inicial do **storeData** valor está definido como 42.

**privateFor** define os nós nos quais o contrato está disponível. Neste exemplo, conta do nó de transação de predefinição pode converter privadas transações para o **alpha** nó. Tem de adicionar as chaves públicas para todos os participantes da transação privada. Se não incluir **privateFor:** e **partir:**, as transações de contrato inteligentes sejam públicas e pode ser vistas por todos os membros de consortium.

Guarde todos os ficheiros selecionando **ficheiro > Guardar tudo**.

## <a name="deploy-smart-contract"></a>Implementar o contrato inteligente

Utilizar Truffle para implementar `SimpleStorage.sol` a rede de nó de transação de predefinição.

```bash
truffle migrate --network defaultnode
```

Truffle compila primeiro e, em seguida, implementa a **SimpleStorage** contrato inteligente.

Exemplo de saída:

```
pat@DESKTOP:/mnt/c/truffledemo$ truffle migrate --network defaultnode

2_deploy_simplestorage.js
=========================

   Deploying 'SimpleStorage'
   -------------------------
   > transaction hash:    0x3f695ff225e7d11a0239ffcaaab0d5f72adb545912693a77fbfc11c0dbe7ba72
   > Blocks: 2            Seconds: 12
   > contract address:    0x0b15c15C739c1F3C1e041ef70E0011e641C9D763
   > account:             0x1a0B9683B449A8FcAd294A01E881c90c734735C3
   > balance:             0
   > gas used:            0
   > gas price:           0 gwei
   > value sent:          0 ETH
   > total cost:          0 ETH


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:                   0 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0 ETH
```

## <a name="validate-contract-privacy"></a>Validar a privacidade do contrato

Devido a privacidade de contrato, apenas podem ser consultados valores de contrato de nós que Declaramos na **privateFor**. Neste exemplo, podemos fazer consultar o nó de transação padrão porque a conta existe nesse nó. Utilizar a consola de Truffle, ligue para o nó de transação de predefinição.

```bash
truffle console --network defaultnode
```

Execute um comando que devolve o valor da instância do contrato.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

Se consultar o nó de transação de predefinição for bem-sucedida, é devolvido o valor 42.

Exemplo de saída:

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network defaultnode
truffle(defaultnode)> SimpleStorage.deployed().then(function(instance){return instance.get();})
'42'
```

Sair da consola.

```bash
.exit
```

Uma vez que declarámos **alpha** chave pública do nó no **privateFor**, podemos fazer consultar o **alpha** nó. Utilizando a consola de Truffle, ligar para o **alpha** nó.

```bash
truffle console --network alpha
```

Execute um comando que devolve o valor da instância do contrato.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

Se a consultar o **alpha** nó for bem-sucedida, o valor 42 é devolvido.

Exemplo de saída:

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network alpha
truffle(alpha)> SimpleStorage.deployed().then(function(instance){return instance.get();})
'42'
```

Sair da consola.

```bash
.exit
```

Uma vez que não Declaramos **beta** chave pública do nó no **privateFor**, não conseguiremos consultar o **beta** nó devido a privacidade do contrato. Utilizando a consola de Truffle, ligar para o **beta** nó.

```bash
truffle console --network beta
```

Execute um comando que devolve o valor da instância do contrato.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

Consultar os **beta** nó falhar, uma vez que o contrato é privado.

Exemplo de saída:

```
pat@DESKTOP-J41EP5S:/mnt/c/truffledemo$ truffle console --network beta
truffle(beta)> SimpleStorage.deployed().then(function(instance){return instance.get();})
Thrown:
Error: Returned values aren't valid, did it run Out of Gas?
    at XMLHttpRequest._onHttpResponseEnd (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:345:8)
    at XMLHttpRequest._setReadyState (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request.ts:219:8)
    at XMLHttpRequestEventTarget.dispatchEvent (/mnt/c/truffledemo/node_modules/xhr2-cookies/xml-http-request-event-target.ts:44:13)
    at XMLHttpRequest.request.onreadystatechange (/mnt/c/truffledemo/node_modules/web3-providers-http/src/index.js:96:13)
```

Sair da consola.

```bash
.exit
```

## <a name="send-a-transaction"></a>Enviar uma transação

Crie um ficheiro chamado `sampletx.js`. Guarde-o na raiz do seu projeto.

Este script define o contrato **storedData** valor da variável 65. Adicione o código para o novo ficheiro.

```javascript
var SimpleStorage = artifacts.require("SimpleStorage");

module.exports = function(done) {
  console.log("Getting deployed version of SimpleStorage...")
  SimpleStorage.deployed().then(function(instance) {
    console.log("Setting value to 65...");
    return instance.set("65", {privateFor: ["<alpha node public key>"], from:"<Account address>"});
  }).then(function(result) {
    console.log("Transaction:", result.tx);
    console.log("Finished!");
    done();
  }).catch(function(e) {
    console.log(e);
    done();
  });
};
```

Substitua os valores nos colchetes angulares, em seguida, guarde o ficheiro.

| Value | Descrição
|-------|-------------
| \<chave pública do nó alfa\> | Chave pública do nó alfa
| \<Endereço da conta\> | Endereço da conta criado no nó de transação de predefinição.

**privateFor** define os nós para o qual a transação está disponível. Neste exemplo, conta do nó de transação de predefinição pode converter privadas transações para o **alpha** nó. Tem de adicionar as chaves públicas para todos os participantes da transação privada.

Utilize Truffle para executar o script para o nó de transação de predefinição.

```bash
truffle exec sampletx.js --network defaultnode
```

Execute um comando que devolve o valor da instância do contrato.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

Se a transação foi concluída com êxito, é devolvido o valor de 65.

Exemplo de saída:

```
Getting deployed version of SimpleStorage...
Setting value to 65...
Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
Finished!
```

Sair da consola.

```bash
.exit
```

## <a name="validate-transaction-privacy"></a>Validar a privacidade de transação

Devido a privacidade de transação, transações só podem ser executadas em nós que Declaramos na **privateFor**. Neste exemplo, podemos realizar transações, uma vez que declarámos **alpha** chave pública do nó no **privateFor**. Utilizar Truffle para executar a transação no **alpha** nó.

```bash
truffle exec sampletx.js --network alpha
```

Execute um comando que devolve o valor da instância do contrato.

```bash
SimpleStorage.deployed().then(function(instance){return instance.get();})
```

Se a transação foi concluída com êxito, é devolvido o valor de 65.

Exemplo de saída:

```
Getting deployed version of SimpleStorage...
Setting value to 65...
Transaction: 0x864e67744c2502ce75ef6e5e09d1bfeb5cdfb7b880428fceca84bc8fd44e6ce0
Finished!
```

Sair da consola.

```bash
.exit
```

Neste tutorial, adicionou dois nós de transação para demonstrar a privacidade de contrato e de transação. Utilizou o nó de padrão para implementar um contrato de inteligente privado. Testado privacidade pela consulta de contrato de valores e transações de desempenho a desenvolver o blockchain.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode eliminar os recursos a eliminar o `myResourceGroup` grupo de recursos que criou o serviço do Azure Blockchain.

Para eliminar o grupo de recursos:

1. No portal do Azure, navegue até **grupo de recursos** no painel de navegação esquerda e selecione o grupo de recursos que pretende eliminar.
1. Selecione **Eliminar grupo de recursos**. Verifique a eliminação ao introduzir o nome do grupo de recursos e selecione **eliminar**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Desenvolver aplicações de blockchain com o serviço do Azure Blockchain](develop.md)
