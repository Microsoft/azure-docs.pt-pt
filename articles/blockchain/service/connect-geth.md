---
title: Usar Geth para anexar ao serviço Blockchain do Azure
description: Anexar a uma instância do Geth no nó de transação do serviço Blockchain do Azure
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 9da78eac1dc429bcc0ad52bb9cb2f1fb743a90d4
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455824"
---
# <a name="quickstart-use-geth-to-attach-to-an-azure-blockchain-service-transaction-node"></a>Início rápido: usar Geth para anexar a um nó de transação do serviço Blockchain do Azure

Neste guia de início rápido, você usa o cliente Geth para anexar a uma instância do Geth em um nó de transação do serviço Blockchain do Azure. Uma vez anexado, você usa o console do JavaScript Geth para chamar uma API Web3 JavaScript Dapp.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Instalar o [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* Conclua [o início rápido: criar um membro do blockchain usando o portal do Azure](create-member.md) ou [início rápido: criar um membro Blockchain do serviço blockchain do Azure usando o CLI do Azure](create-member-cli.md)

## <a name="get-geth-connection-string"></a>Obter cadeia de conexão Geth

Você pode obter a cadeia de conexão Geth para um nó de transação do serviço Blockchain do Azure no portal do Azure.

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
1. Vá para o membro do serviço Blockchain do Azure. Selecione **nós de transação** e o link do nó de transação padrão.

    ![Selecionar nó de transação padrão](./media/connect-geth/transaction-nodes.png)

1. Selecione **cadeias de conexão**.
1. Copie a cadeia de conexão do **https (tecla de acesso 1)** . Você precisa da cadeia de caracteres para a próxima seção.

    ![Cadeia de ligação](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Conectar-se ao Geth

1. Abra uma linha de comandos ou shell.
1. Use o subcomando Geth attach para anexar à instância do Geth em execução em seu nó de transação. Cole a cadeia de conexão como um argumento para o subcomando Attach. Por exemplo:

    ``` bash
    geth attach <connection string>
    ```

1. Uma vez conectado ao console do Ethereum do nó de transação, você pode chamar a API do Web3 JavaScript Dapp ou a API de administração.

    Por exemplo, use a API a seguir para descobrir a chainid.

    ``` bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    Neste exemplo, o chainid é 661.

    ![Opção do serviço Blockchain do Azure](./media/connect-geth/geth-attach.png)

1. Para desconectar-se do console do, digite `exit`.

## <a name="next-steps"></a>Passos Seguintes

Neste guia de início rápido, você usou o cliente Geth para anexar a uma instância do Geth em um nó de transação do serviço Blockchain do Azure. Experimente o próximo tutorial para usar o kit de desenvolvimento do Azure Blockchain para Ethereum para criar, compilar, implantar e executar uma função de contrato inteligente por meio de uma transação.

> [!div class="nextstepaction"]
> [Criar, compilar e implantar contratos inteligentes no serviço Blockchain do Azure](send-transaction.md)
