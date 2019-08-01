---
title: Ligar através do Truffle
description: Conectar-se a uma rede do serviço Blockchain do Azure usando o Truffle
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 9154bc749f7db337de67f501d5e5049dfd466156
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698475"
---
# <a name="quickstart-use-truffle-to-connect-to-an-azure-blockchain-service-network"></a>Início rápido: Usar o Truffle para se conectar a uma rede do serviço Blockchain do Azure

Truffle é um ambiente de desenvolvimento blockchain que você pode usar para se conectar a um nó do serviço Blockchain do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Criar um membro Blockchain do Azure](create-member.md)
* Instale o [Truffle](https://github.com/trufflesuite/truffle). O Truffle exige que várias ferramentas sejam instaladas, incluindo [node. js](https://nodejs.org), [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Instale o [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). O Python é necessário para o Web3.
* Instale o [Visual Studio Code](https://code.visualstudio.com/download).
* Instale [Visual Studio Code extensão de solidez](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity).

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

### <a name="transaction-node-endpoint-addresses"></a>Endereços de ponto de extremidade do nó de transação

1. No portal do Azure, navegue até cada nó de transação e selecione **nós de transação > cadeias de conexão**.
1. Copie e salve a URL do ponto de extremidade do **https (chave de acesso 1)** para cada nó de transação. Você precisará dos endereços de ponto de extremidade para o arquivo de configuração do contrato inteligente posteriormente no tutorial.

    ![Endereço do ponto de extremidade da transação](./media/send-transaction/endpoint.png)

### <a name="edit-configuration-file"></a>Editar arquivo de configuração

1. Inicie o Visual Studio Code e abra a pasta Truffle do diretório do projeto usando o menu **arquivo > abrir pasta** .
1. Abra o arquivo `truffle-config.js`de configuração Truffle.
1. Substitua o conteúdo do arquivo pelas informações de configuração a seguir. Adicione uma variável que contém o endereço do ponto de extremidade. Substitua o colchete angular por valores que você coletou das seções anteriores.

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

1. Use o console do Truffle para se conectar ao nó de transação padrão.

    ``` bash
    truffle console --network defaultnode
    ```

    Truffle conecta-se ao nó de transação padrão e fornece um console interativo.

    Você pode chamar métodos no objeto **Web3** para interagir com seu nó de transação.

1. Chame o método **getBlockNumber** para retornar o número do bloco atual.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Exemplo de saída:

    ```bash
    truffle(defaultnode)> web3.eth.getBlockNumber();
    18567
    ```
1. Saia do console de desenvolvimento do Truffle.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você criou um projeto Truffle para se conectar ao nó de transação padrão do serviço Blockchain do Azure.

Experimente o próximo tutorial para usar o Truffle para enviar uma transação à sua rede do consórcio blockchain.

> [!div class="nextstepaction"]
> [Enviar uma transação](send-transaction.md)
