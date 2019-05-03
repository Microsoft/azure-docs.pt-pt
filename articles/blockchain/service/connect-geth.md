---
title: Utilizar Geth para ligar ao serviço do Azure Blockchain
description: Ligar a uma rede de serviço do Azure Blockchain com Geth
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 0716a9326a54ae31d4f355fe5f4c88488339b390
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026922"
---
# <a name="quickstart-use-geth-to-connect-to-a-transaction-node"></a>Início rápido: Utilizar Geth para ligar a um nó de transação

Geth é um cliente de ir Ethereum, que pode utilizar para ligar a uma instância de Geth num nó de transação de serviço do Azure Blockchain.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Instalar [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* [Criar um membro do Azure Blockchain](create-member.md)

## <a name="get-the-geth-connection-string"></a>Obter cadeia de ligação de Geth

Pode encontrar a cadeia de ligação Geth no portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue para o membro de serviço do Azure Blockchain. Selecione **nós de transação** e o link de nó de transação de predefinição.

    ![Selecione o nó de transação de predefinição](./media/connect-geth/transaction-nodes.png)

1. Selecione **cadeias de ligação**.
1. Copie a cadeia de ligação do **HTTPS (chave de acesso 1)**. Terá do comando para a próxima seção.

    ![Cadeia de ligação](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Ligar ao Geth

1. Abra uma linha de comandos ou shell.
1. Utilize o Geth anexar subcomando anexar para a instância de Geth em execução no seu nó de transação. Cole a cadeia de ligação como um argumento para o subcomando attach. Por exemplo,

    ```
    geth attach <connection string>
    ```

1. Quando estiverem ligados a consola de Ethereum o nó de transação, pode chamar a API de Dapp web3 JavaScript ou a API de administração.

    Por exemplo, utilize a seguinte API para descobrir o chainId.

    ```bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    Neste exemplo, o chainId é 297.

    ![Opção de Blockchain serviço do Azure](./media/connect-geth/geth-attach.png)

1. Para desligar a partir da consola, escreva `exit`.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, utilizou o cliente de Geth para anexar a uma instância de Geth num nó de transação de serviço do Azure Blockchain. Experimente o tutorial seguinte para implementar e enviar uma transação usando Truffle.

> [!div class="nextstepaction"]
> [Enviar uma transação](send-transaction.md)