---
title: Gerenciar o acesso aos recursos do Azure usando o RBAC e o portal do Azure | Microsoft Docs
description: Saiba como gerenciar o acesso aos recursos do Azure para usuários, grupos, entidades de serviço e identidades gerenciadas usando o RBAC (controle de acesso baseado em função) e o portal do Azure. Isto inclui como listar, conceder e remover acesso.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1e9a53c41535c17de2d56227012160c7f6eb25c6
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71337611"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Gerenciar o acesso aos recursos do Azure usando o RBAC e o portal do Azure

O [RBAC (controle de acesso baseado em função)](overview.md) é a maneira como você gerencia o acesso aos recursos do Azure. Este artigo descreve como gerenciar o acesso usando o portal do Azure. Se você precisar gerenciar o acesso a Azure Active Directory, consulte [Exibir e atribuir funções de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar e remover atribuições de função, você deve ter:

- permissões `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, como [administrador de acesso do usuário](built-in-roles.md#user-access-administrator) ou [proprietário](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>Visão geral do controle de acesso (IAM)

O **controle de acesso (iam)** é a folha que você usa para gerenciar o acesso aos recursos do Azure. Ele também é conhecido como gerenciamento de identidade e acesso e aparece em vários locais na portal do Azure. Veja a seguir um exemplo da folha controle de acesso (IAM) para uma assinatura.

![Folha de controle de acesso (IAM) para uma assinatura](./media/role-assignments-portal/access-control-numbers.png)

A tabela a seguir descreve o que alguns dos elementos são usados para:

| # | Elemento | O que você usa para |
| --- | --- | --- |
| 1 | Recurso em que o IAM (controle de acesso) é aberto | Identificar o escopo (assinatura neste exemplo) |
| 2 | Botão **Adicionar** | Adicionar atribuições de função |
| 3 | Guia **verificar acesso** | Exibir as atribuições de função para um único usuário |
| 4 | Guia **atribuições de função** | Exibir as atribuições de função no escopo atual |
| 5 | Guia **funções** | Exibir todas as funções e permissões |

Para ser o mais eficaz com a folha de controle de acesso (IAM), ele ajuda se você pode responder às três perguntas a seguir ao tentar gerenciar o acesso:

1. **Quem precisa de acesso?**

    Quem se refere a um usuário, grupo, entidade de serviço ou identidade gerenciada. Isso também é chamado de *entidade de segurança*.

1. **Quais permissões são necessárias?**

    As permissões são agrupadas em funções. Você pode selecionar em uma lista de várias funções internas.

1. **Onde eles precisam de acesso?**

    Em que se refere ao conjunto de recursos ao qual o acesso se aplica. Onde pode ser um grupo de gerenciamento, uma assinatura, um grupo de recursos ou um único recurso, como uma conta de armazenamento. Isso é chamado de *escopo*.

## <a name="open-access-control-iam"></a>Controle de acesso aberto (IAM)

A primeira coisa que você precisa decidir é onde abrir a folha de controle de acesso (IAM). Depende de quais recursos você deseja gerenciar o acesso. Você deseja gerenciar o acesso para tudo em um grupo de gerenciamento, tudo em uma assinatura, tudo em um grupo de recursos ou um único recurso?

1. No portal do Azure, clique em **todos os serviços** e, em seguida, selecione o escopo. Por exemplo, você pode selecionar **grupos de gerenciamento**, **assinaturas**, **grupos de recursos**ou um recurso.

1. Clique no recurso específico.

1. Clique em **controlo de acesso (IAM)** .

    Veja a seguir um exemplo da folha controle de acesso (IAM) para uma assinatura. Se você fizer alterações de controle de acesso aqui, elas se aplicarão à assinatura inteira.

    ![Folha de controle de acesso (IAM) para uma assinatura](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>Ver funções e permissões

Uma definição de função é uma coleção de permissões que é utilizada para atribuições de funções. O Azure tem mais [de 70 funções internas para recursos do Azure](built-in-roles.md). Siga estas etapas para exibir as funções e permissões disponíveis.

1. **Controle de acesso aberto (iam)** em qualquer escopo.

1. Clique na guia **funções** para ver uma lista de todas as funções internas e personalizadas.

   Você pode ver o número de usuários e grupos atribuídos a cada função no escopo atual.

   ![Lista de funções](./media/role-assignments-portal/roles-list.png)

1. Clique em uma função individual para ver quem recebeu essa função e também exibir as permissões para a função.

   ![Atribuições de funções](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>Ver atribuições de funções

Ao gerenciar o acesso, você deseja saber quem tem acesso, quais são suas permissões e em qual escopo. Para listar o acesso de um usuário, grupo, entidade de serviço ou identidade gerenciada, você vê suas atribuições de função.

### <a name="view-role-assignments-for-a-single-user"></a>Exibir atribuições de função para um único usuário

Siga estas etapas para exibir o acesso de um único usuário, grupo, entidade de serviço ou identidade gerenciada em um escopo específico.

1. **Controle de acesso aberto (iam)** em um escopo, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso, no qual você deseja exibir o acesso.

1. Clique na guia **verificar acesso** .

    ![Controle de acesso – guia acesso de verificação](./media/role-assignments-portal/access-control-check-access.png)

1. Na lista **Localizar** , selecione o tipo de entidade de segurança para a qual você deseja verificar o acesso.

1. Na caixa de pesquisa, insira uma cadeia de caracteres para pesquisar o diretório em busca de nomes de exibição, endereços de email ou identificadores de objeto.

    ![Marcar lista de seleção de acesso](./media/role-assignments-portal/check-access-select.png)

1. Clique na entidade de segurança para abrir o painel **atribuições** .

    ![painel atribuições](./media/role-assignments-portal/check-access-assignments.png)

    Nesse painel, você pode ver as funções atribuídas à entidade de segurança selecionada e ao escopo. Se houver quaisquer atribuições de negação neste escopo ou herdadas para esse escopo, elas serão listadas.

### <a name="view-all-role-assignments-at-a-scope"></a>Exibir todas as atribuições de função em um escopo

1. **Controle de acesso aberto (iam)** em um escopo, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso, no qual você deseja exibir o acesso.

1. Clique na guia **atribuições de função** para exibir todas as atribuições de função neste escopo.

   ![Controle de acesso – guia atribuições de função](./media/role-assignments-portal/access-control-role-assignments.png)

   Na guia atribuições de função, você pode ver quem tem acesso nesse escopo. Tenha em conta que algumas funções têm o âmbito **Este recurso**, ao passo que outras são **(Herdadas)** de outro âmbito. O acesso é atribuído especificamente a esse recurso ou herdado de uma atribuição para o escopo pai.

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

No RBAC, para conceder acesso, você atribui uma função a um usuário, grupo, entidade de serviço ou identidade gerenciada. Siga estas etapas para conceder acesso em escopos diferentes.

### <a name="assign-a-role-at-a-scope"></a>Atribuir uma função a um escopo

1. **Controle de acesso aberto (iam)** em um escopo, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso, no qual você deseja conceder acesso.

1. Clique na guia **atribuições de função** para exibir todas as atribuições de função neste escopo.

1. Clique em **Adicionar** > **Adicionar atribuição de função** para abrir o painel Adicionar atribuição de função.

   Se você não tiver permissões para atribuir funções, a opção Adicionar atribuição de função será desabilitada.

   ![Adicionar menu](./media/role-assignments-portal/add-menu.png)

   ![Adicionar painel de atribuição de função](./media/role-assignments-portal/add-role-assignment.png)

1. Na lista pendente **Função**, selecione uma função, como **Contribuidor de Máquina Virtual**.

1. Na lista **selecionar** , selecione um usuário, grupo, entidade de serviço ou identidade gerenciada. Se não vir o principal de segurança na lista, pode escrever na caixa **Selecionar** para procurar no diretório os nomes a apresentar, endereços de e-mail e identificadores de objetos.

1. Clique em **Guardar** para atribuir a função.

   Depois de alguns instantes, a entidade de segurança recebe a função no escopo selecionado.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Atribuir um utilizador como administrador de uma subscrição

Para tornar um usuário um administrador de uma assinatura do Azure, atribua a ele a função de [proprietário](built-in-roles.md#owner) no escopo da assinatura. A função Proprietário dá ao utilizador acesso total a todos os recursos na subscrição, incluindo o direito de delegar o acesso a outras pessoas. Estes passos são iguais a qualquer outra atribuição de função.

1. No portal do Azure, clique em **todos os serviços** e em **assinaturas**.

1. Clique na subscrição na qual pretende conceder acesso.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no separador **Atribuições de funções** para ver todas as atribuições de funções para esta subscrição.

1. Clique em **Adicionar** > **Adicionar atribuição de função** para abrir o painel Adicionar atribuição de função.

   Se você não tiver permissões para atribuir funções, a opção Adicionar atribuição de função será desabilitada.

   ![Adicionar menu](./media/role-assignments-portal/add-menu.png)

   ![Adicionar painel de atribuição de função](./media/role-assignments-portal/add-role-assignment.png)

1. Na lista pendente **Função**, selecione a função **Proprietário**.

1. Na lista **Selecionar**, selecione um utilizador. Se não vir o utilizador na lista, pode escrever na caixa **Selecionar** para procurar no diretório os nomes a apresentar e endereços de e-mail.

1. Clique em **Guardar** para atribuir a função.

   Após alguns instantes, é atribuída ao utilizador a função Proprietário no âmbito da subscrição.

## <a name="remove-role-assignments"></a>Remover atribuições de funções

No RBAC, para remover o acesso, remova uma atribuição de função. Siga estas etapas para remover o acesso.

1. **Controle de acesso aberto (iam)** em um escopo, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso, no qual você deseja remover o acesso.

1. Clique no separador **Atribuições de funções** para ver todas as atribuições de funções para esta subscrição.

1. Na lista de atribuições de funções, adicione uma marca de verificação junto ao principal de segurança com a atribuição de função que pretende remover.

   ![Mensagem Remover atribuição de função](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Clique em **remover**.

   ![Mensagem Remover atribuição de função](./media/role-assignments-portal/remove-role-assignment.png)

1. Na mensagem remover atribuição de função exibida, clique em **Sim**.

    As atribuições de funções herdadas não podem ser removidas. Se precisar de remover uma atribuição de função herdada, tem de o fazer no âmbito em que a atribuição da função foi criada. Na coluna **escopo** , ao lado de **(Herdado)** , há um link que leva você até o escopo em que essa função foi atribuída. Vá para o âmbito aí listado para remover a atribuição de função.

   ![Mensagem Remover atribuição de função](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Conceder a um usuário acesso aos recursos do Azure usando o RBAC e o portal do Azure @ no__t-0
* [Tutorial: Conceder a um usuário acesso aos recursos do Azure usando RBAC e Azure PowerShell @ no__t-0
* [Solucionar problemas de RBAC para recursos do Azure](troubleshooting.md)
* [Organizar os recursos com grupos de gestão do Azure](../governance/management-groups/overview.md)
