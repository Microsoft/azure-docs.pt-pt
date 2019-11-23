---
title: Using applications in Azure Blockchain Workbench
description: Tutorial on how to use application contracts in Azure Blockchain Workbench Preview.
ms.date: 10/14/2019
ms.topic: tutorial
ms.reviewer: brendal
ms.openlocfilehash: 5761bf1294691c2d50e0e389fe69ec286df4a06c
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324047"
---
# <a name="tutorial-using-applications-in-azure-blockchain-workbench"></a>Tutorial: Using applications in Azure Blockchain Workbench

You can use Blockchain Workbench to create and take actions on contracts. You can also view contract details such as status and transaction history.

Vai aprender a:

> [!div class="checklist"]
> * Create a new contract
> * Take an action on a contract

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* A Blockchain Workbench deployment. For more information, see [Azure Blockchain Workbench deployment](deploy.md) for details on deployment
* A deployed blockchain application in Blockchain Workbench. See [Create a blockchain application in Azure Blockchain Workbench](create-app.md)

[Open the Blockchain Workbench](deploy.md#blockchain-workbench-web-url) in your browser.

![Blockchain Workbench](./media/use/workbench.png)

You need to sign in as a member of the Blockchain Workbench. If there are no applications listed, you are a member of Blockchain Workbench but not a member of any applications. The Blockchain Workbench administrator can assign members to applications.

## <a name="create-new-contract"></a>Create new contract

To create a new contract, you need to be a member specified as a contract **initiator**. For information defining application roles and initiators for the contract, see [workflows in the configuration overview](configuration.md#workflows). For information on assigning members to application roles, see [add a member to application](manage-users.md#add-member-to-application).

1. In Blockchain Workbench application section, select the application tile that contains the contract you want to create. A list of active contracts is displayed.

2. To create a new contract, select **New contract**.

    ![New contract button](./media/use/contract-list.png)

3. The **New contract** pane is displayed. Specify the initial parameters values. Selecione **Criar**.

    ![New contract pane](./media/use/new-contract.png)

    The newly created contract is displayed in the list with the other active contracts.

    ![Active contracts list](./media/use/active-contracts.png)

## <a name="take-action-on-contract"></a>Take action on contract

Depending on the state the contract is in, members can take actions to transition to the next state of the contract. Actions are defined as [transitions](configuration.md#transitions) within a [state](configuration.md#states). Members belonging to an allowed application or instance role for the transition can take the action. 

1. In Blockchain Workbench application section, select the application tile that contains the contract to take the action.
2. Select the contract in the list. Details about the contract are displayed in different sections. 

    ![Contract details](./media/use/contract-details.png)

    | Section  | Descrição  |
    |---------|---------|
    | Estado | Lists the current progress within the contract stages |
    | Detalhes | The current values of the contract |
    | Ação | Details about the last action |
    | Atividade | Transaction history of the contract |
    
3. In the **Action** section, select **Take action**.

4. The details about the current state of the contract are displayed in a pane. Choose the action you want to take in the drop-down. 

    ![Choose action](./media/use/choose-action.png)

5. Select **Take action** to initiate the action.
6. If parameters are required for the action, specify the values for the action.

    ![Take action](./media/use/take-action.png)

7. Select **Take action** to execute the action.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench application versioning](version-app.md)
