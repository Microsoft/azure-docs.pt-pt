---
title: Ligar através do Truffle
description: Ligar a uma rede de serviço do Azure Blockchain com Truffle
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 692bac0e0474ead7a0acd0887c3c10055edf8553
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399980"
---
# <a name="quickstart-use-truffle-to-connect-to-a-an-azure-blockchain-service-network"></a>Início rápido: Utilizar Truffle para ligar a uma rede de um serviço do Azure Blockchain

Truffle é um ambiente de desenvolvimento de blockchain que pode utilizar para ligar a um nó de serviço do Azure Blockchain.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Criar um membro do Azure Blockchain](create-member.md)
* Instale [Truffle](https://github.com/trufflesuite/truffle). Truffle requer várias ferramentas a serem instalados incluindo [node. js](https://nodejs.org), [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Instale [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python é necessária para Web3.

## <a name="create-truffle-project"></a>Criar projeto Truffle

1. Abra uma linha de comandos node. js ou o shell.
1. Altere o diretório para onde pretende criar o diretório de projeto Truffle.
1. Criar um diretório do projeto e altere o seu caminho para o novo diretório. Por exemplo,

    ``` bash
    mkdir truffledemo
    cd truffledemo
    ```

1. Inicialize o projeto de Truffle.

    ``` bash
    truffle init
    ```

1. Instale a API de JavaScript Ethereum web3 na pasta de projeto. Atualmente, a versão web3 versão 1.0.0-beta.37 é necessário.

    ``` bash
    npm install web3@1.0.0-beta.37
    ```

    Poderá receber o npm avisos durante a instalação.

1. Inicie a consola de desenvolvimento interativo do Truffle.

    ``` bash
    truffle develop
    ```

    Truffle cria um blockchain de desenvolvimento local e fornece uma consola interativa.

## <a name="connect-to-transaction-node"></a>Ligar ao nó de transação

Uso *Web3* para ligar ao nó de transação. Pode obter o *Web3* cadeia de ligação do portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para o membro de serviço do Azure Blockchain. Selecione **nós de transação** e o link de nó de transação de predefinição.

    ![Selecione o nó de transação de predefinição](./media/connect-truffle/transaction-nodes.png)

1. Selecione **código de exemplo > Web3**.
1. Copiar a partir de JavaScript **HTTPS (chave de acesso 1)** . Terá do código para a consola de desenvolvimento interativo do Truffle.

    ![Código de Web3](./media/connect-truffle/web3-code.png)

1. Cole o código de JavaScript do passo anterior para a consola de desenvolvimento interativo Truffle. O código cria um objeto de web3 que está ligado ao seu nó de transação de serviço do Azure Blockchain.

    Exemplo de saída:

    ```bash
    truffle(develop)> var Web3 = require("Web3");
    truffle(develop)> var provider = new Web3.providers.HttpProvider("https://myblockchainmember.blockchain.azure.com:3200/hy5FMu5TaPR0Zg8GxiPwned");
    truffle(develop)> var web3 = new Web3(provider);
    ```

    Pode chamar métodos no **web3** objeto para interagir com o nó de transação.

1. Chamar o **getBlockNumber** método para retornar o número atual de bloco.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Exemplo de saída:

    ```bash
    truffle(develop)> web3.eth.getBlockNumber();
    18567
    ```
1. Sair da consola de desenvolvimento Truffle.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou um projeto de Truffle para ligar ao nó de transação do serviço do Azure Blockchain padrão.

Experimente o tutorial seguinte para utilizar Truffle para enviar uma transação para a sua rede de blockchain consortium.

> [!div class="nextstepaction"]
> [Enviar uma transação](send-transaction.md)