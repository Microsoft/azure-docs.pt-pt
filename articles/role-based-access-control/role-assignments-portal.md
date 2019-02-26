---
title: Gerir o acesso aos recursos do Azure através do RBAC e o portal do Azure | Documentos da Microsoft
description: Saiba como gerir o acesso aos recursos do Azure para utilizadores, grupos, os principais de serviço e identidades geridas com o controlo de acesso baseado em funções (RBAC) e o portal do Azure. Isto inclui como listar, conceder e remover acesso.
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
ms.openlocfilehash: bb23cbc275e01eab5361504c547c020b0a29f4c3
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805295"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>Gerir o acesso aos recursos do Azure através do RBAC e o portal do Azure

[Controlo de acesso baseado em funções (RBAC)](overview.md) é a maneira que gerencie o acesso aos recursos do Azure. Este artigo descreve como gerir o acesso no portal do Azure. Se precisar de gerir o acesso ao Azure Active Directory, consulte [modo de exibição e atribuir funções de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar e remover atribuições de função, tem de ter:

- `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete` permissões, tal como [administrador de acesso de utilizador](built-in-roles.md#user-access-administrator) ou [proprietário](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>Descrição geral do controlo de acesso (IAM)

**Controlo de acesso (IAM)** é o painel que utiliza para gerir o acesso aos recursos do Azure. Ele também é conhecido como gestão de identidades e acesso e é exibido em vários locais no portal do Azure. O código a seguir mostra um exemplo de painel de controlo (IAM) de acesso para uma subscrição.

![Painel de controlo (IAM) de acesso para uma subscrição](./media/role-assignments-portal/access-control-numbers.png)

A tabela seguinte descreve o que são alguns dos elementos utilização para:

| # | Elemento | O que usá-lo para |
| --- | --- | --- |
| 1 | Recursos em que o controlo de acesso (IAM) é aberto | Identificar o âmbito (subscrição neste exemplo) |
| 2 | **Adicionar** botão | Adicionar as atribuições de funções |
| 3 | **Verificar acesso** separador | Ver as atribuições de funções para um único utilizador |
| 4 | **Atribuições de funções** separador | Ver as atribuições de funções no âmbito atual |
| 5 | **Funções** separador | Ver todas as funções e permissões |

Para ser mais eficiente com o painel de controlo (IAM) de acesso, ele ajuda a se pode responder as perguntas de três seguintes quando estiver a tentar gerir o acesso:

1. **Quem precisa de acesso?**

    Que se refere a um utilizador, o grupo, o principal de serviço ou a identidade gerida. Isso também é chamado um *entidade de segurança*.

1. **Quais as permissões que precisam?**

    As permissões são agrupadas em funções. Pode selecionar a partir de uma lista de várias funções incorporadas.

1. **Em que precisam de acesso?**

    Em que se refere ao conjunto de recursos que o acesso se aplica a. Em que pode ser um grupo de gestão, subscrição, grupo de recursos ou um único recurso, como uma conta de armazenamento. Isso é chamado do *âmbito*.

## <a name="open-access-control-iam"></a>Abra o controlo de acesso (IAM)

A primeira coisa que precisa decidir é onde abrir o painel de controlo (IAM) de acesso. Depende de quais recursos que pretende gerir o acesso para. Pretende gerir o acesso para tudo num grupo de gestão, tudo numa subscrição, tudo num grupo de recursos ou um único recurso?

1. No portal do Azure, clique em **todos os serviços** e, em seguida, selecione o âmbito. Por exemplo, pode selecionar **grupos de gestão**, **subscrições**, **grupos de recursos**, ou um recurso.

1. Clique no recurso específico.

1. Clique em **controlo de acesso (IAM)**.

    O código a seguir mostra um exemplo de painel de controlo (IAM) de acesso para uma subscrição. Se fizer qualquer alteração de controlo do acesso, seria aplicam-se para a subscrição completa.

    ![Painel de controlo (IAM) de acesso para uma subscrição](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>Ver funções e permissões

Uma definição de função é uma coleção de permissões que é utilizada para atribuições de funções. O Azure tem mais de 70 [funções incorporadas para recursos do Azure](built-in-roles.md). Siga estes passos para ver as funções disponíveis e as permissões.

1. Open **controlo de acesso (IAM)** em qualquer âmbito.

1. Clique nas **funções** separador para ver uma lista de todas as funções incorporadas e personalizadas.

   Pode ver o número de utilizadores e grupos que estão atribuídos a cada função no âmbito atual.

   ![Lista de funções](./media/role-assignments-portal/roles-list.png)

1. Clique numa função individual para ver a quem foi atribuída a esta função e também ver as permissões para a função.

   ![Atribuições de funções](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>Ver atribuições de funções

Ao gerir o acesso, deseja saber quem tem acesso, quais são as respetivas permissões e o âmbito. Para o acesso de lista para um utilizador, grupo, principal de serviço ou uma identidade gerida, exibir suas atribuições de funções.

### <a name="view-role-assignments-for-a-single-user"></a>Atribuições de funções de exibição para um único utilizador

Siga estes passos para ver o acesso a um único utilizador, grupo, principal de serviço ou uma identidade gerida num determinado âmbito.

1. Open **controlo de acesso (IAM)** num âmbito, como o grupo de gestão, subscrição, grupo de recursos ou recurso, em que pretende ver o acesso.

1. Clique nas **verificar acesso** separador.

    ![Controlo de acesso - separador de acesso de verificação](./media/role-assignments-portal/access-control-check-access.png)

1. Na **encontrar** , selecione o tipo de entidade de segurança que pretende verificar o acesso.

1. Na caixa de pesquisa, introduza uma cadeia de caracteres para procurar o diretório para os nomes a apresentar, endereços de e-mail ou identificadores de objetos.

    ![Verifique a lista de seleção de acesso](./media/role-assignments-portal/check-access-select.png)

1. Clique a entidade de segurança para abrir o **atribuições** painel.

    ![Painel de atribuições](./media/role-assignments-portal/check-access-assignments.png)

    Neste painel, pode ver as funções atribuídas para a entidade de segurança selecionado e o escopo. Se existirem quaisquer negar atribuições neste âmbito ou herdadas para este âmbito, estes serão apresentados.

### <a name="view-all-role-assignments-at-a-scope"></a>Ver todas as atribuições de função num âmbito

1. Open **controlo de acesso (IAM)** num âmbito, como o grupo de gestão, subscrição, grupo de recursos ou recurso, em que pretende ver o acesso.

1. Clique nas **atribuições de funções** separador para ver todas as atribuições de função neste âmbito.

   ![Controlo de acesso - separador de atribuições de função](./media/role-assignments-portal/access-control-role-assignments.png)

   No separador de atribuições de função, pode ver quem tem acesso a este âmbito. Tenha em conta que algumas funções têm o âmbito **Este recurso**, ao passo que outras são **(Herdadas)** de outro âmbito. Acesso é atribuído especificamente para este recurso ou herdado de uma atribuição ao âmbito principal.

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

No RBAC, para conceder acesso, atribua uma função a um utilizador, o grupo, o principal de serviço ou a identidade gerida. Siga estes passos para conceder acesso em âmbitos diferentes.

### <a name="assign-a-role-at-a-scope"></a>Atribuir uma função num âmbito

1. Open **controlo de acesso (IAM)** num âmbito, como o grupo de gestão, subscrição, grupo de recursos ou recurso, onde pretende conceder acesso.

1. Clique nas **atribuições de funções** separador para ver todas as atribuições de função neste âmbito.

1. Clique em **Add** > **adicionar atribuição de função** para abrir o painel de atribuição de função de adicionar.

   Se não tiver permissões para atribuir funções, a opção de atribuição de função de adicionar será desativada.

   ![Menu Adicionar](./media/role-assignments-portal/add-menu.png)

   ![Adicionar o painel de atribuição de função](./media/role-assignments-portal/add-role-assignment.png)

1. Na lista pendente **Função**, selecione uma função, como **Contribuidor de Máquina Virtual**.

1. Na **selecione** , selecione um utilizador, o grupo, o principal de serviço ou a identidade gerida. Se não vir o principal de segurança na lista, pode escrever na caixa **Selecionar** para procurar no diretório os nomes a apresentar, endereços de e-mail e identificadores de objetos.

1. Clique em **guardar** para atribuir a função.

   Após alguns instantes, a entidade de segurança é atribuída a função no âmbito selecionado.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Atribuir um utilizador como administrador de uma subscrição

Para tornar um utilizador administrador de uma subscrição do Azure, atribuí-los a [proprietário](built-in-roles.md#owner) função no âmbito da subscrição. A função de proprietário dá ao usuário acesso total a todos os recursos na subscrição, incluindo o direito de delegar o acesso a outras pessoas. Estes passos são os mesmos como qualquer outra atribuição de função.

1. No portal do Azure, clique em **todos os serviços** e, em seguida **subscrições**.

1. Clique a subscrição em que pretende conceder acesso.

1. Clique em **controlo de acesso (IAM)**.

1. Clique nas **atribuições de funções** separador para ver todas as atribuições de função para esta subscrição.

1. Clique em **Add** > **adicionar atribuição de função** para abrir o painel de atribuição de função de adicionar.

   Se não tiver permissões para atribuir funções, a opção de atribuição de função de adicionar será desativada.

   ![Menu Adicionar](./media/role-assignments-portal/add-menu.png)

   ![Adicionar o painel de atribuição de função](./media/role-assignments-portal/add-role-assignment.png)

1. Na **função** na lista pendente, selecione a **proprietário** função.

1. Na **selecione** , selecione um utilizador. Se não vir o utilizador na lista, pode digitar o **selecione** caixa para procurar o diretório para os nomes a apresentar e endereços de e-mail.

1. Clique em **guardar** para atribuir a função.

   Após alguns instantes, o utilizador tem atribuída a função de proprietário no âmbito da subscrição.

## <a name="remove-role-assignments"></a>Remover atribuições de funções

No RBAC, para remover o acesso, remova uma atribuição de função. Siga estes passos para remover o acesso.

1. Open **controlo de acesso (IAM)** num âmbito, como o grupo de gestão, subscrição, grupo de recursos ou recurso, em que pretende remover o acesso.

1. Clique nas **atribuições de funções** separador para ver todas as atribuições de função para esta subscrição.

1. Na lista de atribuições de funções, adicione uma marca de verificação junto ao principal de segurança com a atribuição de função que pretende remover.

   ![Mensagem Remover atribuição de função](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Clique em **remover**.

   ![Mensagem Remover atribuição de função](./media/role-assignments-portal/remove-role-assignment.png)

1. Na mensagem de atribuição de função remove que aparece, clique em **Sim**.

    As atribuições de funções herdadas não podem ser removidas. Se precisar de remover uma atribuição de função herdada, tem de o fazer no âmbito em que a atribuição da função foi criada. Na **âmbito** coluna, junto a **(herdado)** há um link que leva-o para o âmbito em que esta função foi atribuída. Vá para o âmbito aí listado para remover a atribuição de função.

   ![Mensagem Remover atribuição de função](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Passos Seguintes

* [Tutorial: Conceder um acesso de utilizador aos recursos do Azure utilizando o RBAC e o portal do Azure](quickstart-assign-role-user-portal.md)
* [Tutorial: Conceder um acesso de utilizador aos recursos do Azure utilizando o RBAC e o Azure PowerShell](tutorial-role-assignments-user-powershell.md)
* [Resolver problemas relacionados com o RBAC para recursos do Azure](troubleshooting.md)
* [Organizar os recursos com grupos de gestão do Azure](../governance/management-groups/index.md)
