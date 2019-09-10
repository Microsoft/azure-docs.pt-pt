---
title: Usando aplicativos na visualização do Azure Blockchain Workbench
description: Tutorial sobre como usar contratos de aplicativo na visualização do Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: e1ba09c4fbba6492e2d5f870a6c31a3d5a6d8956
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845135"
---
# <a name="tutorial-using-applications-in-azure-blockchain-workbench"></a>Tutorial: Usando aplicativos no Azure Blockchain Workbench

Você pode usar o Blockchain Workbench para criar e executar ações em contratos. Você também pode exibir detalhes do contrato, como status e histórico de transações.

Vai aprender a:

> [!div class="checklist"]
> * Criar um novo contrato
> * Executar uma ação em um contrato

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma implantação do Blockchain Workbench. Para obter mais informações, consulte [implantação do Azure Blockchain Workbench](deploy.md) para obter detalhes sobre a implantação
* Um aplicativo blockchain implantado no Blockchain Workbench. Consulte [criar um aplicativo blockchain no Azure Blockchain Workbench](create-app.md)

[Abra o Blockchain Workbench](deploy.md#blockchain-workbench-web-url) em seu navegador.

![Blockchain Workbench](./media/use/workbench.png)

Você precisa entrar como um membro do Blockchain Workbench. Se não houver nenhum aplicativo listado, você será membro do Blockchain Workbench, mas não membro de nenhum aplicativo. O administrador do Blockchain Workbench pode atribuir membros a aplicativos.

## <a name="create-new-contract"></a>Criar novo contrato

Para criar um novo contrato, você precisa ser um membro especificado como um **iniciador**de contrato. Para obter informações sobre como definir funções de aplicativo e iniciadores para o contrato, consulte [fluxos de trabalho na visão geral de configuração](configuration.md#workflows). Para obter informações sobre como atribuir membros a funções de aplicativo, consulte [Adicionar um membro ao aplicativo](manage-users.md#add-member-to-application).

1. Na seção aplicativo do Blockchain Workbench, selecione o bloco do aplicativo que contém o contrato que você deseja criar. É exibida uma lista de contratos ativos.

2. Para criar um novo contrato, selecione **novo contrato**.

    ![Botão novo contrato](./media/use/contract-list.png)

3. O painel **novo contrato** é exibido. Especifique os valores de parâmetros iniciais. Selecione **Criar**.

    ![Novo painel de contrato](./media/use/new-contract.png)

    O contrato recém-criado é exibido na lista com os outros contratos ativos.

    ![Lista de contratos ativos](./media/use/active-contracts.png)

## <a name="take-action-on-contract"></a>Agir no contrato

Dependendo do estado em que o contrato está, os membros podem executar ações para fazer a transição para o próximo estado do contrato. As ações são definidas como [transições](configuration.md#transitions) dentro de um [estado](configuration.md#states). Os membros que pertencem a uma função de aplicativo ou instância permitida para a transição podem executar a ação. 

1. Na seção aplicativo do Blockchain Workbench, selecione o bloco do aplicativo que contém o contrato para executar a ação.
2. Selecione o contrato na lista. Os detalhes sobre o contrato são exibidos em seções diferentes. 

    ![Detalhes do contrato](./media/use/contract-details.png)

    | Section  | Descrição  |
    |---------|---------|
    | State | Lista o progresso atual dentro dos estágios do contrato |
    | Detalhes | Os valores atuais do contrato |
    | Action | Detalhes sobre a última ação |
    | Atividade | Histórico de transações do contrato |
    
3. Na seção **ação** , selecione **executar ação**.

4. Os detalhes sobre o estado atual do contrato são exibidos em um painel. Escolha a ação que você deseja executar na lista suspensa. 

    ![Escolher ação](./media/use/choose-action.png)

5. Selecione **executar ação** para iniciar a ação.
6. Se forem necessários parâmetros para a ação, especifique os valores para a ação.

    ![Tomar medidas](./media/use/take-action.png)

7. Selecione **executar ação** para executar a ação.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Controle de versão do aplicativo Azure Blockchain Workbench](version-app.md)
