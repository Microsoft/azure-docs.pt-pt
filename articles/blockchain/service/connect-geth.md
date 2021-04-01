---
title: Use Geth para anexar ao Serviço Azure Blockchain
description: Anexar a uma instância geth no nó de transação do Serviço Azure Blockchain
ms.date: 05/26/2020
ms.topic: quickstart
ms.reviewer: maheshna
ms.openlocfilehash: 2c5b6a56d96ac132949052e9879c8f61d41ffb06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "83994794"
---
# <a name="quickstart-use-geth-to-attach-to-an-azure-blockchain-service-transaction-node"></a>Quickstart: Use Geth para anexar um nó de transação do Serviço Azure Blockchain

Neste arranque rápido, você usa o cliente Geth para anexar a uma instância Geth em um nó de transação do Serviço Azure Blockchain. Uma vez anexado, utiliza a consola Geth para chamar uma API Ethereum JavaScript.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Instalar [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* [Quickstart Completo: Criar um membro blockchain utilizando o portal Azure](create-member.md) ou [Quickstart: Criar um membro blockchain do Azure Blockchain Service utilizando o Azure CLI](create-member-cli.md)

## <a name="get-geth-connection-string"></a>Obter cadeia de conexão Geth

Pode obter o string de conexão Geth para um nó de transação do Serviço Azure Blockchain no portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Vá ao seu membro do Serviço Azure Blockchain. Selecione **os nós de transação** e o link de nó de transação padrão.

    ![Selecione o nó de transação padrão](./media/connect-geth/transaction-nodes.png)

1. Selecione **as cordas de ligação**.
1. Copie o fio de ligação a partir de **HTTPS (Tecla de acesso 1)**. Precisa da corda para a próxima secção.

    ![Cadeia de ligação](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Conecte-se a Geth

1. Abra uma solicitação de comando ou uma concha.
1. Utilize o subcomeçador Geth para anexar à instância Geth em execução no seu nó de transação. Cole a cadeia de ligação como argumento para a subcomefão do anexo. Por exemplo:

    ``` bash
    geth attach <connection string>
    ```

1. Uma vez ligado à consola Ethereum do nó de transação, pode utilizar a API Ethereum JavaScript.

    Por exemplo, utilize a API seguinte para descobrir o chainId.

    ``` bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    Neste exemplo, o chainId é 661.

    ![Opção de Serviço Azure Blockchain](./media/connect-geth/geth-attach.png)

1. Para desligar da consola, escreva `exit` .

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você usou o cliente Geth para anexar a uma instância Geth em um nó de transação do Serviço Azure Blockchain. Experimente o próximo tutorial para usar o Azure Blockchain Development Kit para o Ethereum para criar, construir, implementar e executar uma função de contrato inteligente através de uma transação.

> [!div class="nextstepaction"]
> [Criar, construir e implementar contratos inteligentes no Azure Blockchain Service](send-transaction.md)
