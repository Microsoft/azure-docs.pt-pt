---
title: List Azure fun assignments using the Azure portal - Azure RBAC
description: Saiba como determinar quais os recursos que os utilizadores, grupos, diretores de serviços ou identidades geridas têm acesso ao uso do portal Azure e do controlo de acesso baseado em funções Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 10/16/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 280e5012f7c41a906b5b2e266840ea3c0cc315ba
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151711"
---
# <a name="list-azure-role-assignments-using-the-azure-portal"></a>Liste atribuições de funções Azure usando o portal Azure

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] Este artigo descreve como listar atribuições de funções usando o portal Azure.

> [!NOTE]
> Se a sua organização tiver funções de gestão subcontratadas a um prestador de serviços que utilize [a Azure delegada em gestão de recursos,](../lighthouse/concepts/azure-delegated-resource-management.md)as atribuições de funções autorizadas por esse prestador de serviços não serão mostradas aqui.

## <a name="list-role-assignments-for-a-user-or-group"></a>Listar atribuições de funções para um utilizador ou grupo

A maneira mais fácil de ver as funções atribuídas a um utilizador ou grupo numa subscrição é utilizar o painel **de atribuições de funções Azure.**

1. No portal Azure, selecione **Todos os serviços** do menu do portal Azure.

1. Selecione **O Diretório Ativo Azure** e, em seguida, selecione **Utilizadores** ou **Grupos**.

1. Clique no utilizador ou grupo para o que pretende listar as atribuições de funções para.

1. Clique **em atribuições de funções Azure**.

    Você vê uma lista de funções atribuídas ao utilizador ou grupo selecionado em vários âmbitos, tais como grupo de gestão, subscrição, grupo de recursos ou recursos. Esta lista inclui todas as atribuições de funções que tem permissão para ler.

    ![Atribuições de funções para um utilizador](./media/role-assignments-list-portal/azure-role-assignments-user.png)    

1. Para alterar a subscrição, clique na lista **de Assinaturas.**

## <a name="list-owners-of-a-subscription"></a>Proprietários de listas de uma subscrição

Os utilizadores que tenham sido atribuídos o papel [de Proprietário](built-in-roles.md#owner) para uma subscrição podem gerir tudo na subscrição. Siga estes passos para listar os proprietários de uma subscrição.

1. No portal Azure, clique em **Todos os serviços** e, em seguida, **Em Assinaturas**.

1. Clique na subscrição de que pretende listar os proprietários de.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no separador **Atribuições de funções** para ver todas as atribuições de funções para esta subscrição.

1. Percorra a secção **Proprietários** para ver todos os utilizadores que foram designados para a função Proprietário para esta subscrição.

   ![Controlo de acesso a subscrição - Separador de atribuições de funções](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Listar atribuições de funções num âmbito

1. No portal Azure, clique em **Todos os serviços** e, em seguida, selecione o âmbito. Por exemplo, pode selecionar **grupos de Gestão,** **Assinaturas, Grupos** **de Recursos**ou um recurso.

1. Clique no recurso específico.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no **separador atribuições de funções** para ver todas as atribuições de funções neste âmbito.

   ![Controlo de acesso - Separador de atribuições de funções](./media/role-assignments-list-portal/access-control-role-assignments.png)

   No separador Fun assignments, pode ver quem tem acesso neste âmbito. Tenha em conta que algumas funções têm o âmbito **Este recurso**, ao passo que outras são **(Herdadas)** de outro âmbito. O acesso é atribuído especificamente a este recurso ou herdado de uma atribuição ao âmbito dos pais.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Listar atribuições de funções para um utilizador num âmbito

Para listar o acesso a um utilizador, grupo, principal de serviço ou identidade gerida, enumera as suas atribuições de funções. Siga estes passos para listar as atribuições de funções para um único utilizador, grupo, principal de serviço ou identidade gerida num determinado âmbito.

1. No portal Azure, clique em **Todos os serviços** e, em seguida, selecione o âmbito. Por exemplo, pode selecionar **grupos de Gestão,** **Assinaturas, Grupos** **de Recursos**ou um recurso.

1. Clique no recurso específico.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no separador **De acesso a Verificação.**

    ![Controlo de acesso - Verifique o separador de acesso](./media/role-assignments-list-portal/access-control-check-access.png)

1. Na lista **Localizar,** selecione o tipo de princípio de segurança para o qual pretende verificar o acesso.

1. Na caixa de pesquisa, introduza uma cadeia para pesquisar nomes de exibição, endereços de e-mail ou identificadores de objetos.

    ![Verifique a lista de seleção de acessos](./media/role-assignments-list-portal/check-access-select.png)

1. Clique no principal de segurança para abrir o painel **de atribuições.**

    ![pane atribuições](./media/role-assignments-list-portal/check-access-assignments.png)

    Neste painel, pode ver as funções atribuídas ao principal de segurança selecionado e o âmbito. Se houver alguma atribuição de negação neste âmbito ou herdada a este âmbito, serão listadas.

## <a name="list-role-assignments-for-a-managed-identity"></a>Listar atribuições de funções para uma identidade gerida

Pode listar atribuições de funções para identidades geridas atribuídas pelo sistema e atribuídas pelo utilizador num determinado âmbito, utilizando a lâmina de controlo de **acesso (IAM),** conforme descrito anteriormente. Esta secção descreve como listar atribuições de funções apenas para a identidade gerida.

### <a name="system-assigned-managed-identity"></a>Identidade gerida atribuída pelo sistema

1. No portal Azure, abra uma identidade gerida atribuída pelo sistema.

1. No menu esquerdo, clique em **Identidade.**

    ![Identidade gerida atribuída pelo sistema](./media/shared/identity-system-assigned.png)

1. Em **Permissões,** clique nas **atribuições de funções Azure**.

    Você vê uma lista de funções atribuídas à identidade gerida atribuída ao sistema selecionado em vários âmbitos tais como grupo de gestão, subscrição, grupo de recursos ou recursos. Esta lista inclui todas as atribuições de funções que tem permissão para ler.

    ![Atribuições de funções para uma identidade gerida atribuída pelo sistema](./media/shared/role-assignments-system-assigned.png)

1. Para alterar a subscrição, clique na lista **de Assinaturas.**

### <a name="user-assigned-managed-identity"></a>Identidade gerida atribuída pelo utilizador

1. No portal Azure, abra uma identidade gerida atribuída ao utilizador.

1. Clique **em atribuições de funções Azure**.

    Você vê uma lista de funções atribuídas à identidade gerida atribuída pelo utilizador selecionado em vários âmbitos, tais como grupo de gestão, subscrição, grupo de recursos ou recursos. Esta lista inclui todas as atribuições de funções que tem permissão para ler.

    ![Atribuições de funções para uma identidade gerida atribuída pelo sistema](./media/shared/role-assignments-user-assigned.png)

1. Para alterar a subscrição, clique na lista **de Assinaturas.**

## <a name="list-number-of-role-assignments"></a>Número de atribuições de funções

Pode ter até **2000** atribuições de funções em cada subscrição. Este limite inclui atribuições de funções nos âmbitos de subscrição, grupo de recursos e recursos. Para ajudá-lo a acompanhar este limite, o separador **atribuições de Função** inclui um gráfico que lista o número de atribuições de funções para a subscrição atual.

![Controlo de acesso - Número de atribuições de funções](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Se estiver a aproximar-se do número máximo e tentar adicionar mais atribuições de funções, verá um aviso no painel de atribuição de **funções Add.** Para formas de reduzir o número de atribuições de funções, consulte [Troubleshoot Azure RBAC](troubleshooting.md#azure-role-assignments-limit).

![Controlo de acesso - Adicionar aviso de atribuição de funções](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="download-role-assignments"></a>Descarregue atribuições de funções

Você pode baixar atribuições de funções em um âmbito em formato CSV ou JSON. Isto pode ser útil se precisar de inspecionar a lista numa folha de cálculo ou fazer um inventário ao migrar uma subscrição.

Ao descarregar atribuições de funções, deve ter em mente os seguintes critérios:

- Se não tiver permissões para ler o diretório, como o papel de Leitores de Diretório, o Nome de Exibição, o Nome do SignInName e as colunas ObjectType ficarão em branco.
- As atribuições de funções cujo principal de segurança foi eliminado não estão incluídas.
- O acesso concedido aos administradores clássicos não está incluído.

Siga estes passos para descarregar atribuições de funções num âmbito.

1. No portal Azure, clique em **Todos os serviços** e, em seguida, selecione o âmbito onde pretende descarregar as atribuições de funções. Por exemplo, pode selecionar **grupos de Gestão,** **Assinaturas, Grupos** **de Recursos**ou um recurso.

1. Clique no recurso específico.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique **em Baixar as atribuições de funções** para abrir o painel de atribuições de funções de descarregamento.

    ![Controlo de acessos - Descarregue atribuições de funções](./media/role-assignments-list-portal/download-role-assignments.png)

1. Utilize as caixas de verificação para selecionar as atribuições de funções que pretende incluir no ficheiro descarregado.

    - **Herdado** - Inclua atribuições de funções herdadas para o âmbito atual.
    - **No âmbito atual** - Incluir atribuições de funções para o âmbito atual.
    - **Crianças** - Incluir atribuições de funções em níveis inferiores ao âmbito atual. Esta caixa de verificação é desativada para o âmbito do grupo de gestão.

1. Selecione o formato de ficheiro, que pode ser valores separados por vírgula (CSV) ou Notação de Objetos JavaScript (JSON).

1. Especifique o nome do ficheiro.

1. Clique **em Iniciar** o download.

    Os exemplos de exibição de cada formato de ficheiro.

    ![Descarregue atribuições de funções como CSV](./media/role-assignments-list-portal/download-role-assignments-csv.png)

    ![Descarregue atribuições de funções como CSV](./media/role-assignments-list-portal/download-role-assignments-json.png)

## <a name="next-steps"></a>Passos seguintes

- [Adicionar ou remover atribuições de funções do Azure com o portal do Azure](role-assignments-portal.md)
- [Resolução de problemas Azure RBAC](troubleshooting.md)
