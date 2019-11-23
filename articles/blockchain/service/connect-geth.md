---
title: Use Geth to attach to Azure Blockchain Service
description: Attach to a Geth instance on Azure Blockchain Service transaction node
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 1da38ebd3a264ea173626cc0858d82cd1fc3f30e
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325314"
---
# <a name="quickstart-use-geth-to-attach-to-an-azure-blockchain-service-transaction-node"></a>Quickstart: Use Geth to attach to an Azure Blockchain Service transaction node

In this quickstart, you use the Geth client to attach to a Geth instance on an Azure Blockchain Service transaction node. Once attached, you use the Geth JavaScript console to call a web3 JavaScript Dapp API.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Install [Geth](https://github.com/ethereum/go-ethereum/wiki/geth)
* Complete [Quickstart: Create a blockchain member using the Azure portal](create-member.md) or [Quickstart: Create an Azure Blockchain Service blockchain member using Azure CLI](create-member-cli.md)

## <a name="get-geth-connection-string"></a>Get Geth connection string

You can get the Geth connection string for an Azure Blockchain Service transaction node in the Azure portal.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Go to your Azure Blockchain Service member. Select **Transaction nodes** and the default transaction node link.

    ![Select default transaction node](./media/connect-geth/transaction-nodes.png)

1. Select **Connection strings**.
1. Copy the connection string from **HTTPS (Access key 1)** . You need the string for the next section.

    ![Cadeia de ligação](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Connect to Geth

1. Open a command prompt or shell.
1. Use the Geth attach subcommand to attach to the running Geth instance on your transaction node. Paste the connection string as an argument for the attach subcommand. Por exemplo:

    ``` bash
    geth attach <connection string>
    ```

1. Once connected to the transaction node's Ethereum console, you can call the web3 JavaScript Dapp API or the admin API.

    For example, use the following API to find out the chainId.

    ``` bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    In this example, the chainId is 661.

    ![Azure Blockchain Service option](./media/connect-geth/geth-attach.png)

1. To disconnect from the console, type `exit`.

## <a name="next-steps"></a>Passos seguintes

In this quickstart, you used the Geth client to attach to a Geth instance on an Azure Blockchain Service transaction node. Try the next tutorial to use Azure Blockchain Development Kit for Ethereum to create, build, deploy, and execute a smart contract function via a transaction.

> [!div class="nextstepaction"]
> [Use Visual Studio Code to create, build, and deploy smart contracts](send-transaction.md)
