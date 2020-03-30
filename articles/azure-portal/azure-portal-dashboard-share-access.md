---
title: Partilhe dashboards de portal Azure utilizando o Controlo de Acesso baseado em funções
description: Este artigo explica como partilhar um dashboard no portal Azure utilizando o Controlo de Acesso baseado em Funções.
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 711e8a7ae31888c9754252d88404d90f24e8030e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131984"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Partilhar dashboards do Azure mediante a utilização do Controlo de Acesso Baseado em Funções

Depois de configurar um dashboard, pode publicá-lo e partilhá-lo com outros utilizadores da sua organização. Permite que outros vejam o seu painel utilizando o [Controlo de Acesso baseado em Funções](../role-based-access-control/role-assignments-portal.md) Azure (RBAC). Atribuir um utilizador ou grupo de utilizadores a uma função. Esta função define se esses utilizadores podem visualizar ou modificar o painel de instrumentos publicado.

Todos os dashboards publicados são implementados como recursos Azure. Existem como itens manejáveis dentro da sua subscrição e estão contidos num grupo de recursos. Do ponto de vista do controlo de acesso, os dashboards não são diferentes dos outros recursos, como uma máquina virtual ou uma conta de armazenamento.

> [!TIP]
> Os azulejos individuais no tablier aplicam os seus próprios requisitos de controlo de acesso com base nos recursos que exibem. Pode partilhar um dashboard amplamente enquanto protege os dados em azulejos individuais.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Compreender o controlo de acesso para dashboards

Com o Controlo de Acesso baseado em Funções (RBAC), pode atribuir aos utilizadores funções em três níveis diferentes de âmbito:

* subscrição
* grupo de recursos
* recurso

As permissões que atribui herdam da subscrição até ao recurso. O painel publicado é um recurso. Pode já ter utilizadores atribuídos a funções para a subscrição que se candidatam ao dashboard publicado.

Digamos que tem uma subscrição Azure e vários membros da sua equipa foram atribuídos as funções de *proprietário,* *colaborador*ou *leitor* para a subscrição. Os utilizadores que sejam proprietários ou colaboradores podem listar, visualizar, criar, modificar ou eliminar dashboards dentro da subscrição. Os utilizadores que são leitores podem listar e ver dashboards, mas não podem modificá-los ou eliminá-los. Os utilizadores com acesso ao leitor podem fazer edições locais para um dashboard publicado, como quando se resolum problemas, mas não podem publicar essas alterações de volta ao servidor. Podem fazer uma cópia privada do painel para si mesmos.

Também pode atribuir permissões ao grupo de recursos que contém vários dashboards ou a um dashboard individual. Por exemplo, pode decidir que um grupo de utilizadores deve ter permissões limitadas em toda a subscrição, mas um maior acesso a um determinado dashboard. Atribuir esses utilizadores a um papel para o painel de instrumentos.

## <a name="publish-dashboard"></a>Publicar o dashboard

Suponhamos que configura um dashboard que pretende partilhar com um grupo de utilizadores na sua subscrição. Os seguintes passos mostram como partilhar um dashboard a um grupo chamado Storage Managers. Pode nomear o seu grupo o que quiser. Para mais informações, consulte [Managing groups no Diretório Ativo Azure](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Antes de atribuir o acesso, tem de publicar o painel de instrumentos.

1. No painel de instrumentos, selecione **Partilhar**.

    ![selecione parte para o seu dashboard](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. Em **Sharing + controlo de acesso,** selecione **Publicar**.

    ![publicar o seu dashboard](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Por padrão, a partilha publica o seu dashboard a um grupo de recursos chamado **dashboards**. Para selecionar um grupo de recursos diferente, limpe a caixa de verificação.

O seu painel de instrumentos está agora publicado. Se as permissões herdadas da subscrição forem adequadas, não precisa de fazer mais nada. Outros utilizadores da sua organização podem aceder e modificar o dashboard com base na sua função de nível de subscrição.

## <a name="assign-access-to-a-dashboard"></a>Atribuir acesso a um painel de instrumentos

Pode atribuir um grupo de utilizadores a um papel nesse dashboard.

1. Depois de publicar o dashboard, selecione a opção **Partilhar** ou **Desfazer** para aceder ao Controlo de Partilha **+ acesso**.

1. No Controlo de Partilha + controlo de **acesso,** selecione **Gerir utilizadores**.

    ![gerir os utilizadores para um dashboard](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

1. Selecione **atribuições** de Role para ver os utilizadores existentes que já estão atribuídos uma função para este dashboard.

1. Para adicionar um novo utilizador ou grupo, selecione **Adicionar** e depois Adicionar a atribuição de **funções**.

    ![adicionar um utilizador para o acesso ao painel de instrumentos](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Selecione o papel que representa as permissões para conceder. Para este exemplo, selecione **Contributor**.

1. Selecione o utilizador ou grupo para atribuir à função. Se não vir o utilizador ou grupo que procura na lista, utilize a caixa de pesquisa. A sua lista de grupos disponíveis depende dos grupos que criou no Diretório Ativo.

1. Quando terminar de adicionar utilizadores ou grupos, selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma lista de papéis, consulte [papéis incorporados para os recursos Azure.](../role-based-access-control/built-in-roles.md)
* Para aprender sobre gestão de recursos, consulte [gerir os recursos do Azure utilizando o portal Azure.](resource-group-portal.md)

