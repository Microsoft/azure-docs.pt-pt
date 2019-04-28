---
title: Utilizar aplicações no Azure Blockchain Workbench
description: Tutorial sobre como utilizar a aplicação de contratos no Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 04/15/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 89c83ed6d02a60978bd54fb97d37063e34f6c0c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61128340"
---
# <a name="tutorial-using-applications-in-azure-blockchain-workbench"></a>Tutorial: Utilizar aplicações no Azure Blockchain Workbench

Pode utilizar o Blockchain Workbench para criar e tome ações sobre contratos. Também pode ver detalhes como o histórico de estado e transações de contrato.

Vai aprender a:

> [!div class="checklist"]
> * Criar um novo contrato
> * Execute uma ação num contrato

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma implementação de Blockchain Workbench. Para obter mais informações, consulte [implementação do Azure Blockchain Workbench](deploy.md) para obter detalhes sobre a implementação
* Uma aplicação de blockchain implementado na bancada de trabalho de Blockchain. Consulte [criar uma aplicação de blockchain no Azure Blockchain Workbench](create-app.md)

[Abra o Blockchain Workbench](deploy.md#blockchain-workbench-web-url) no seu browser.

![Blockchain Workbench](./media/use/workbench.png)

Tem de iniciar sessão como membro da bancada de trabalho de Blockchain. Se não há aplicações listadas, é um membro do Blockchain Workbench, mas não é membro de quaisquer aplicações. O administrador de Blockchain Workbench pode atribuir membros a aplicativos.

## <a name="create-new-contract"></a>Criar novo contrato

Para criar um novo contrato, tem de ser um membro especificado como um contrato **iniciador**. Para definir funções de aplicação e os iniciadores para o contrato de informações, consulte [fluxos de trabalho na descrição geral do configuração](configuration.md#workflows). Para informações sobre como atribuir membros a funções de aplicação, consulte [Adicionar membro à aplicação](manage-users.md#add-member-to-application).

1. Na secção de aplicações de Blockchain Workbench, selecione o mosaico de aplicação que contém o contrato que pretende criar. É apresentada uma lista de contratos de Active Directory.

2. Para criar um novo contrato, selecione **novo contrato**.

    ![Novo botão de contrato](./media/use/contract-list.png)

3. O **novo contrato** é apresentado o painel. Especifique os valores de parâmetros inicial. Selecione **Criar**.

    ![Novo painel de contrato](./media/use/new-contract.png)

    O contrato recentemente criado é apresentado na lista com os outros contratos de Active Directory.

    ![Lista de contratos de Active Directory](./media/use/active-contracts.png)

## <a name="take-action-on-contract"></a>Tomar medidas em contrato

Dependendo do Estado o contrato é, os membros podem tomar medidas para transição para o próximo estado do contrato. Ações são definidas como [transições](configuration.md#transitions) dentro de um [estado](configuration.md#states). Os membros que pertencem a uma função de aplicação ou instância permitida para a transição podem executar a ação. 

1. Na secção de aplicações de Blockchain Workbench, selecione o mosaico de aplicação que contém o contrato para executar a ação.
2. Na lista, selecione o contrato. Detalhes sobre o contrato são apresentadas nas seções diferentes. 

    ![Detalhes do contrato ](./media/use/contract-details.png)

    | Section  | Descrição  |
    |---------|---------|
    | Estado | Apresenta o progresso atual dentro os estágios de contrato |
    | Detalhes | Os valores atuais do contrato |
    | Ação | Detalhes sobre a última ação |
    | Atividade | Histórico de transações do contrato |
    
3. Na **ação** secção, selecione **agir**.

4. Os detalhes sobre o estado atual do contrato são apresentados num painel. Escolha a ação que pretende colocar na lista pendente. 

    ![Escolha a ação](./media/use/choose-action.png)

5. Selecione **agir** para iniciar a ação.
6. Se os parâmetros são necessários para a ação, especifique os valores para a ação.

    ![Tomar medidas](./media/use/take-action.png)

7. Selecione **agir** para executar a ação.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Controlo de versões de aplicações do Azure Blockchain Workbench](version-app.md)
