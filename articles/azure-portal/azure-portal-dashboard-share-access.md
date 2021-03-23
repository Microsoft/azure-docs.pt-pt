---
title: Partilhe os dashboards do portal Azure utilizando o controlo de acesso baseado em funções Azure
description: Este artigo explica como partilhar um dashboard no portal Azure utilizando o controlo de acesso baseado em funções Azure.
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 336bfe9792c5ba4246458368e008a8c50833ed03
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771550"
---
# <a name="share-azure-dashboards-by-using-azure-role-based-access-control"></a>Partilhe tabliers Azure utilizando o controlo de acesso baseado em funções Azure

Depois de configurar um dashboard, pode publicá-lo e partilhá-lo com outros utilizadores da sua organização. Permite que outros vejam o seu dashboard utilizando o [controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md). Atribua um único utilizador ou um grupo de utilizadores a uma função. Esta função define se esses utilizadores podem ver ou modificar o dashboard publicado.

Todos os dashboards publicados são implementados como recursos Azure. Existem como itens manejáveis dentro da sua subscrição e estão contidos num grupo de recursos. Do ponto de vista do controlo de acessos, os dashboards não são diferentes dos outros recursos, como uma máquina virtual ou uma conta de armazenamento. Os azulejos individuais no tablier impõem os seus próprios requisitos de controlo de acesso com base nos recursos que exibem. Pode partilhar um dashboard amplamente enquanto protege os dados em azulejos individuais.

## <a name="understanding-access-control-for-dashboards"></a>Compreender o controlo de acessos para tabliers

Com o controlo de acesso baseado em funções Azure (Azure RBAC), pode atribuir os utilizadores a funções em três níveis diferentes de âmbito:

* subscrição
* grupo de recursos
* recurso

As permissões que atribui herdam da subscrição até ao recurso. O painel de instrumentos publicado é um recurso. Pode já ter utilizadores atribuídos a funções para a subscrição que se candidatam ao dashboard publicado.

Digamos que tem uma subscrição do Azure e vários membros da sua equipa foram designados para as funções de *proprietário,* *colaborador* ou *leitor* para a subscrição. Os utilizadores que são proprietários ou contribuintes podem listar, visualizar, criar, modificar ou eliminar dashboards dentro da subscrição. Os utilizadores que são leitores podem listar e ver dashboards, mas não podem modificá-los ou eliminá-los. Os utilizadores com acesso ao leitor podem fazer edições locais para um dashboard publicado, como quando se solucionam um problema, mas não podem publicar essas alterações de volta para o servidor. Podem fazer uma cópia privada do painel para si mesmos.

Pode atribuir permissões ao grupo de recursos que contém vários dashboards ou a um dashboard individual. Por exemplo, pode decidir que um grupo de utilizadores deve ter permissões limitadas em toda a subscrição, mas um maior acesso a um determinado dashboard. Atribua esses utilizadores a um papel para o painel de instrumentos.

## <a name="publish-a-dashboard"></a>Publicar um dashboard

Suponhamos que configura um dashboard que pretende partilhar com um grupo de utilizadores na sua subscrição. Os passos a seguir mostram como partilhar um dashboard com um grupo chamado Storage Managers. Pode nomear o seu grupo o que quiser. Para obter mais informações, consulte [os grupos de gestão no Diretório Ativo Azure.](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

Antes de atribuir o acesso, tem de publicar o painel de instrumentos.

1. No painel de instrumentos, **selecione Partilhar**.

    ![selecione partilha para o seu dashboard](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. No **controlo de partilha + acesso**, selecione **Publicar.**

    ![publicar o seu dashboard](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Por predefinição, a partilha publica o seu dashboard a um grupo de recursos chamado **dashboards**. Para selecionar um grupo de recursos diferente, limpe a caixa de verificação.

O seu painel de instrumentos foi agora publicado. Se as permissões herdadas da subscrição forem adequadas, não precisa de fazer mais nada. Outros utilizadores da sua organização podem aceder e modificar o dashboard com base no seu nível de subscrição.

## <a name="assign-access-to-a-dashboard"></a>Atribuir acesso a um dashboard

Pode atribuir um grupo de utilizadores a uma função para esse painel de instrumentos.

1. Depois de publicar o painel de instrumentos, **selecione Gerir a partilha**.

1. No **Access Control** seleciona **atribuições de funções** para ver os utilizadores existentes que já estão atribuídos a uma função para este dashboard.

1. Para adicionar um novo utilizador ou grupo, **selecione Adicionar** e adicione a atribuição **de função**.

    ![adicionar um utilizador para acesso ao painel de instrumentos](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Selecione o papel que representa as permissões a conceder, como **o Contribuinte.**

1. Selecione o utilizador ou grupo para atribuir à função. Se não vir o utilizador ou grupo que procura na lista, utilize a caixa de pesquisa. A sua lista de grupos disponíveis depende dos grupos que criou no Ative Directory.

1. Quando terminar de adicionar utilizadores ou grupos, **selecione Save**.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma lista de papéis, consulte [as funções incorporadas do Azure.](../role-based-access-control/built-in-roles.md)
* Para conhecer a gestão de recursos, consulte [gerir os recursos do Azure utilizando o portal Azure.](../azure-resource-manager/management/manage-resources-portal.md)