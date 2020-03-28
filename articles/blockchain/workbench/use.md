---
title: Utilização de aplicações na bancada azure blockchain
description: Tutorial sobre como usar contratos de candidatura na Pré-visualização da bancada azure Blockchain.
ms.date: 10/14/2019
ms.topic: tutorial
ms.reviewer: brendal
ms.openlocfilehash: 5761bf1294691c2d50e0e389fe69ec286df4a06c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74324047"
---
# <a name="tutorial-using-applications-in-azure-blockchain-workbench"></a>Tutorial: Utilização de aplicações na bancada azure blockchain

Você pode usar blockchain Workbench para criar e tomar ações em contratos. Também pode ver detalhes do contrato, como o estado e o histórico de transações.

Vai aprender a:

> [!div class="checklist"]
> * Criar um novo contrato
> * Tome uma ação sobre um contrato

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma implantação da bancada blockchain. Para mais informações, consulte a implementação da [bancada de trabalho Azure Blockchain](deploy.md) para obter detalhes sobre a implementação
* Uma aplicação blockchain implantada na Bancada de Trabalho blockchain. Ver [Criar uma aplicação blockchain na bancada azure blockchain](create-app.md)

[Abra a bancada blockchain](deploy.md#blockchain-workbench-web-url) no seu navegador.

![Blockchain Workbench](./media/use/workbench.png)

Tens de te inscrever como membro da bancada blockchain. Se não houver candidaturas listadas, é membro da Blockchain Workbench, mas não membro de quaisquer aplicações. O administrador da bancada blockchain pode atribuir membros a candidaturas.

## <a name="create-new-contract"></a>Criar novo contrato

Para criar um novo contrato, é necessário ser um membro especificado como **iniciador**de contratos. Para obter informações que definam as funções de aplicação e os iniciadores do contrato, consulte [fluxos de trabalho na visão geral da configuração](configuration.md#workflows). Para obter informações sobre a atribuição de membros às funções de inscrição, consulte [adicionar um membro à aplicação](manage-users.md#add-member-to-application).

1. Na secção de aplicação blockchain Workbench, selecione o azulejo de aplicação que contém o contrato que pretende criar. É apresentada uma lista de contratos ativos.

2. Para criar um novo contrato, selecione **Novo contrato.**

    ![Novo botão de contrato](./media/use/contract-list.png)

3. O novo painel de **contrato** sou exibido. Especifique os valores dos parâmetros iniciais. Selecione **Criar**.

    ![Novo painel de contrato](./media/use/new-contract.png)

    O contrato recém-criado é apresentado na lista com os outros contratos ativos.

    ![Lista de contratos ativos](./media/use/active-contracts.png)

## <a name="take-action-on-contract"></a>Tomar medidas sobre contrato

Dependendo do estado em que o contrato está, os membros podem tomar medidas para transitar para o próximo estado do contrato. As ações são definidas como [transições](configuration.md#transitions) dentro de um [estado.](configuration.md#states) Os membros pertencentes a uma aplicação ou papel, por exemplo, autorizados para a transição podem tomar a medida. 

1. Na secção de aplicação blockchain Workbench, selecione o azulejo de aplicação que contém o contrato para tomar a ação.
2. Selecione o contrato na lista. Os detalhes sobre o contrato são apresentados em diferentes secções. 

    ![Detalhes do contrato](./media/use/contract-details.png)

    | Section  | Descrição  |
    |---------|---------|
    | Estado | Enumera os progressos atuais dentro das fases do contrato |
    | Detalhes | Os valores atuais do contrato |
    | Ação | Detalhes sobre a última ação |
    | Atividade | Histórico de transações do contrato |
    
3. Na secção **Ação,** selecione **Tomar medidas**.

4. Os detalhes sobre o estado atual do contrato são exibidos num painel. Escolha a ação que pretende tomar na entrega. 

    ![Escolha ação](./media/use/choose-action.png)

5. Selecione **Tomar medidas** para iniciar a ação.
6. Se forem necessários parâmetros para a ação, especifique os valores da ação.

    ![Tomar medidas](./media/use/take-action.png)

7. Selecione **Tomar medidas** para executar a ação.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Versão da aplicação Azure Blockchain Workbench](version-app.md)
