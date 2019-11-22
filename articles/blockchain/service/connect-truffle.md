---
title: Usar o Truffle para se conectar ao serviço Blockchain do Azure
description: Conectar-se a uma rede do serviço Blockchain do Azure usando o Truffle
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/20/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: f5d752c99331d454e7f9f98c06b9ba0209de8cc7
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285383"
---
# <a name="quickstart-use-truffle-to-connect-to-azure-blockchain-service"></a>Início rápido: usar o Truffle para se conectar ao serviço Blockchain do Azure

Neste guia de início rápido, você usa Truffle conectar-se a um nó de transação do serviço Blockchain do Azure. Em seguida, você usa o console interativo do Truffle para chamar os métodos **Web3** para interagir com sua rede blockchain.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Conclua [o início rápido: criar um membro do blockchain usando o portal do Azure](create-member.md) ou [início rápido: criar um membro Blockchain do serviço blockchain do Azure usando o CLI do Azure](create-member-cli.md)
* Instale o [Truffle](https://github.com/trufflesuite/truffle). O Truffle exige que várias ferramentas sejam instaladas, incluindo [node. js](https://nodejs.org), [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Instale o [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). O Python é necessário para o Web3.

## <a name="create-truffle-project"></a>Criar projeto Truffle

1. Abra um prompt de comando ou shell do node. js.
1. Altere o diretório para onde você deseja criar o diretório do projeto Truffle.
1. Crie um diretório para o projeto e altere seu caminho para o novo diretório. Por exemplo,

    ``` bash
    mkdir truffledemo
    cd truffledemo
    ```

1. Inicialize o projeto Truffle.

    ``` bash
    truffle init
    ```

1. Instale a API Web3 do Ethereum JavaScript na pasta do projeto. Atualmente, a versão Web3 versão 1.0.0-beta. 37 é necessária.

    ``` bash
    npm install web3@1.0.0-beta.37
    ```

    Você pode receber avisos do NPM durante a instalação.
    
## <a name="configure-truffle-project"></a>Configurar o projeto Truffle

Para configurar o projeto Truffle, você precisa de algumas informações de nó de transação do portal do Azure.

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
1. Vá para o membro do serviço Blockchain do Azure. Selecione **nós de transação** e o link do nó de transação padrão.

    ![Selecionar nó de transação padrão](./media/connect-truffle/transaction-nodes.png)

1. Selecione **cadeias de conexão**.
1. Copie a cadeia de conexão do **https (tecla de acesso 1)** . Você precisa da cadeia de caracteres para a próxima seção.

    ![Cadeia de ligação](./media/connect-truffle/connection-string.png)

### <a name="edit-configuration-file"></a>Editar arquivo de configuração

Em seguida, você precisa atualizar o arquivo de configuração Truffle com o ponto de extremidade do nó de transação.

1. Na pasta do projeto **truffledemo** , abra o arquivo de configuração Truffle `truffle-config.js` em um editor.
1. Substitua o conteúdo do arquivo pelas informações de configuração a seguir. Adicione uma variável que contém o endereço do ponto de extremidade. Substitua o colchete angular por valores que você coletou da seção anterior.

    ``` javascript
    var defaultnode = "<default transaction node connection string>";   
    var Web3 = require("web3");
    
    module.exports = {
      networks: {
        defaultnode: {
          provider: new Web3.providers.HttpProvider(defaultnode),
          network_id: "*"
        }
      }
    }
    ```

1. Salve as alterações em `truffle-config.js`.

## <a name="connect-to-transaction-node"></a>Ligar ao nó de transação

Use *Web3* para se conectar ao nó de transação.

1. Use o console do Truffle para se conectar ao nó de transação padrão. Em um prompt de comando ou Shell, execute o seguinte comando:

    ``` bash
    truffle console --network defaultnode
    ```

    Truffle conecta-se ao nó de transação padrão e fornece um console interativo.

    Você pode chamar métodos no objeto **Web3** para interagir com sua rede blockchain.

1. Chame o método **getBlockNumber** para retornar o número do bloco atual.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Exemplo de saída:

    ```bash
    truffle(defaultnode)> web3.eth.getBlockNumber();
    18567
    ```
1. Saia do console do Truffle.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você usou Truffle conectar a um nó de transação padrão do serviço Blockchain do Azure e usou o console interativo para retornar o número do bloco de Blockchain atual.

Experimente o próximo tutorial para usar o kit de desenvolvimento do Azure Blockchain para Ethereum para criar, compilar, implantar e executar uma função de contrato inteligente por meio de uma transação.

> [!div class="nextstepaction"]
> [Use Visual Studio Code para criar, compilar e implantar contratos inteligentes](send-transaction.md)
