---
title: Use Truffle to connect to Azure Blockchain Service
description: Connect to an Azure Blockchain Service network using Truffle
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 9ea96aa32072775fe6fb9563442f5e3564d37ea5
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455792"
---
# <a name="quickstart-use-truffle-to-connect-to-azure-blockchain-service"></a>Quickstart: Use Truffle to connect to Azure Blockchain Service

In this quickstart, you use Truffle connect to an Azure Blockchain Service transaction node. You then use the Truffle interactive console to call **web3** methods to interact with your blockchain network.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Complete [Quickstart: Create a blockchain member using the Azure portal](create-member.md) or [Quickstart: Create an Azure Blockchain Service blockchain member using Azure CLI](create-member-cli.md)
* Install [Truffle](https://github.com/trufflesuite/truffle). Truffle requires several tools to be installed including [Node.js](https://nodejs.org), [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Install [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Python is needed for Web3.

## <a name="create-truffle-project"></a>Create Truffle project

1. Open a Node.js command prompt or shell.
1. Change directory to where you want to create the Truffle project directory.
1. Create a directory for the project and change your path to the new directory. Por exemplo,

    ``` bash
    mkdir truffledemo
    cd truffledemo
    ```

1. Initialize the Truffle project.

    ``` bash
    truffle init
    ```

1. Install Ethereum JavaScript API web3 in the project folder. Currently, version web3 version 1.0.0-beta.37 is required.

    ``` bash
    npm install web3@1.0.0-beta.37
    ```

    You may receive npm warnings during installation.
    
## <a name="configure-truffle-project"></a>Configure Truffle project

To configure the Truffle project, you need some transaction node information from the Azure portal.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Go to your Azure Blockchain Service member. Select **Transaction nodes** and the default transaction node link.

    ![Select default transaction node](./media/connect-truffle/transaction-nodes.png)

1. Select **Connection strings**.
1. Copy the connection string from **HTTPS (Access key 1)** . You need the string for the next section.

    ![Cadeia de ligação](./media/connect-truffle/connection-string.png)

### <a name="edit-configuration-file"></a>Edit configuration file

Next, you need to update the Truffle configuration file with the transaction node endpoint.

1. In the **truffledemo** project folder, open the Truffle configuration file `truffle-config.js` in an editor.
1. Replace the contents of the file with the following configuration information. Add a variable containing the endpoint address. Replace the angle bracket with values you collected from the previous section.

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

1. Save the changes to `truffle-config.js`.

## <a name="connect-to-transaction-node"></a>Ligar ao nó de transação

Use *Web3* to connect to the transaction node.

1. Use the Truffle console to connect to the default transaction node. At a command prompt or shell, run the following command:

    ``` bash
    truffle console --network defaultnode
    ```

    Truffle connects to the default transaction node and provides an interactive console.

    You can call methods on the **web3** object to interact with your blockchain network.

1. Call the **getBlockNumber** method to return the current block number.

    ```bash
    web3.eth.getBlockNumber();
    ```

    Exemplo de saída:

    ```bash
    truffle(defaultnode)> web3.eth.getBlockNumber();
    18567
    ```
1. Exit the Truffle console.

    ```bash
    .exit
    ```

## <a name="next-steps"></a>Passos seguintes

In this quickstart, you used Truffle connect to an Azure Blockchain Service default transaction node and used the interactive console to return the current blockchain block number.

Try the next tutorial to use Azure Blockchain Development Kit for Ethereum to create, build, deploy, and execute a smart contract function via a transaction.

> [!div class="nextstepaction"]
> [Create, build, and deploy smart contracts on Azure Blockchain Service](send-transaction.md)
