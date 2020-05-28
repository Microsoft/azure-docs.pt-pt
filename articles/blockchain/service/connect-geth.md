---
title: Use o Geth para anexar ao Serviço Blockchain Azure
description: Anexar a uma instância geth no nó de transação do Serviço Azure Blockchain
ms.date: 05/26/2020
ms.topic: quickstart
ms.reviewer: maheshna
ms.openlocfilehash: 2c5b6a56d96ac132949052e9879c8f61d41ffb06
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83994794"
---
# <a name="quickstart-use-geth-to-attach-to-an-azure-blockchain-service-transaction-node"></a>Quickstart: Use Geth para anexar a um nó de transações do Serviço Blockchain Azure

Neste arranque rápido, você usa o cliente Geth para anexar a uma instância Geth em um nó de transação azure Blockchain Service. Uma vez anexado, utiliza-se a consola Geth para chamar a API Ethereum JavaScript.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Instalar [o Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* Complete [Quickstart: Criar um membro blockchain usando o portal Azure](create-member.md) ou [Quickstart: Criar um membro blockchain Azure Blockchain Service usando o Azure CLI](create-member-cli.md)

## <a name="get-geth-connection-string"></a>Obter cadeia de ligação Geth

Você pode obter a cadeia de conexão Geth para um nó de transação do Serviço Azure Blockchain no portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Vá ao seu membro do Serviço Azure Blockchain. Selecione **os nódosos de transação** e o link de nó de transação predefinido.

    ![Selecione o nó de transações por defeito](./media/connect-geth/transaction-nodes.png)

1. Selecione **as cordas de ligação**.
1. Copie a cadeia de ligação de HTTPS (Tecla de **acesso 1)**. Precisa da corda para a próxima secção.

    ![Cadeia de ligação](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Ligue-se a Geth

1. Abra um pedido de comando ou concha.
1. Utilize o subcomando geth para anexar à instância Geth em execução no seu nó de transações. Colar a corda de ligação como argumento para o subcomando anexado. Por exemplo:

    ``` bash
    geth attach <connection string>
    ```

1. Uma vez ligado à consola Ethereum do nó de transações, pode utilizar a API Ethereum JavaScript.

    Por exemplo, utilize a Seguinte API para descobrir a cadeia Id.

    ``` bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    Neste exemplo, a cadeia Id é de 661.

    ![Opção De serviço Azure Blockchain](./media/connect-geth/geth-attach.png)

1. Para desligar da consola, escreva `exit` .

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, usou o cliente Geth para anexar a uma instância geth num nó de transações do Azure Blockchain Service. Experimente o próximo tutorial para usar o Kit de Desenvolvimento Azure Blockchain para o Ethereum para criar, construir, implementar e executar uma função de contrato inteligente através de uma transação.

> [!div class="nextstepaction"]
> [Criar, construir e implementar contratos inteligentes no Serviço Azure Blockchain](send-transaction.md)
