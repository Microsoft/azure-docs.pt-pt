---
title: Usar o Geth para se conectar ao serviço Blockchain do Azure
description: Conectar-se a uma rede do serviço Blockchain do Azure usando o Geth
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: a26899e291c4f44d3c9d91032b2ee191ba03133a
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931782"
---
# <a name="quickstart-use-geth-to-connect-to-a-transaction-node"></a>Início rápido: Utilizar o Geth para ligar a um nó de transação

Geth é um cliente Ethereum go que você pode usar para anexar a uma instância do Geth em um nó de transação do serviço Blockchain do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Instalar o [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* Início [rápido completo: Crie um membro do blockchain usando o](create-member.md) portal do Azure [ou o início rápido: Criar um membro Blockchain do serviço Blockchain do Azure usando CLI do Azure](create-member-cli.md)

## <a name="get-the-geth-connection-string"></a>Obter a cadeia de conexão Geth

Você pode encontrar a cadeia de conexão Geth no portal do Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Navegue até o membro do serviço Blockchain do Azure. Selecione **nós de transação** e o link do nó de transação padrão.

    ![Selecionar nó de transação padrão](./media/connect-geth/transaction-nodes.png)

1. Selecione **cadeias de conexão**.
1. Copie a cadeia de conexão do **https (tecla de acesso 1)** . Você precisa do comando para a próxima seção.

    ![Cadeia de ligação](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Conectar-se ao Geth

1. Abra uma linha de comandos ou shell.
1. Use o subcomando Geth attach para anexar à instância do Geth em execução em seu nó de transação. Cole a cadeia de conexão como um argumento para o subcomando Attach. Por exemplo,

    ```
    geth attach <connection string>
    ```

1. Uma vez conectado ao console do Ethereum do nó de transação, você pode chamar a API do Web3 JavaScript Dapp ou a API de administração.

    Por exemplo, use a API a seguir para descobrir a chainid.

    ```bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    Neste exemplo, o chainid é 297.

    ![Opção do serviço Blockchain do Azure](./media/connect-geth/geth-attach.png)

1. Para se desconectar do console do `exit`, digite.

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você usou o cliente Geth para anexar a uma instância do Geth em um nó de transação do serviço Blockchain do Azure. Experimente o próximo tutorial para usar o kit de desenvolvimento do Azure Blockchain para Ethereum e Truffle para criar, compilar, implantar e executar uma função de contrato inteligente por meio de uma transação.

> [!div class="nextstepaction"]
> [Use Visual Studio Code para criar, compilar e implantar contratos inteligentes](send-transaction.md)