---
title: Partilhar dashboards de portais do Azure utilizando o RBAC | Documentos da Microsoft
description: Este artigo explica como partilhar um dashboard no portal do Azure utilizando o controlo de acesso baseado em funções.
services: azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2016
ms.author: tomfitz
ms.openlocfilehash: fbbc8a4f636a95d18baa0dc5de541279ce36789b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551995"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Partilhar dashboards do Azure utilizando o controlo de acesso baseado em funções
Depois de configurar um dashboard, pode publicá-la e partilhá-lo com outros utilizadores na sua organização. Permitir que outros utilizadores para ver o seu dashboard ao utilizar o Azure [controlo de acesso baseado em funções](../role-based-access-control/role-assignments-portal.md). Atribuir um utilizador ou grupo de utilizadores a uma função, e essa função define se esses utilizadores podem ver ou modificar o dashboard publicado. 

Todos os dashboards publicados são implementados como recursos do Azure, o que significa que eles existem como gerenciáveis itens na sua subscrição e estão contidos num grupo de recursos.  Da perspectiva de controlo de acesso, os dashboards são não diferentes de outros recursos, como uma máquina virtual ou uma conta de armazenamento.

> [!TIP]
> Individuais mosaicos no dashboard impõem seus próprios requisitos de controlo de acesso com base nos recursos que serão apresentados.  Por conseguinte, é possível criar um dashboard partilhado amplamente mantendo a proteção de dados nos mosaicos individuais.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Controlo de acesso de compreensão para dashboards
Com baseada em funções controlo de acesso (RBAC), pode atribuir utilizadores a funções em três níveis diferentes de âmbito:

* subscrição
* grupo de recursos
* Recurso

As permissões de que atribuição são herdadas da subscrição para baixo para o recurso. O dashboard publicado é um recurso. Por conseguinte, pode já ter utilizadores atribuídos a funções para a subscrição que também funcionam para o dashboard publicado. 

Eis um exemplo.  Vamos supor que tem uma subscrição do Azure e vários membros de sua equipe foram atribuídos as funções de **proprietário**, **contribuinte**, ou **leitor** para a subscrição. Os utilizadores que são proprietários ou contribuidores são capazes de lista, visualizar, criar, modificar ou eliminar dashboards dentro da subscrição.  Os utilizadores que são os leitores podem listar e ver dashboards, mas não é possível modificar ou eliminá-los.  Os utilizadores com acesso de leitor são capazes de fazer edições locais num dashboard publicado (como, quando um problema de resolução de problemas), mas não é possível publicar as alterações no servidor.  Eles terão a opção para fazer uma cópia privada do dashboard por conta própria

No entanto, também pode atribuir permissões para o grupo de recursos que contém vários dashboards ou para um dashboard individual. Por exemplo, pode decidir que um grupo de usuários deve ter permissões limitadas entre a subscrição, mas maior acesso a um dashboard específico. Atribua os utilizadores a uma função para esse dashboard. 

## <a name="publish-dashboard"></a>Publicar o dashboard
Vamos supor que tiver concluído a configuração de um dashboard que pretende partilhar com um grupo de utilizadores na sua subscrição. Os passos abaixo descrever um grupo personalizado chamado gestores de armazenamento, mas pode nomear o seu grupo de tudo o que desejar. Para obter informações sobre como criar um grupo do Active Directory e adicionar utilizadores a esse grupo, consulte [gerir grupos no Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

1. No dashboard, selecione **partilha**.
   
     ![Selecione a partilha](./media/azure-portal-dashboard-share-access/select-share.png)
2. Antes de atribuir acesso, tem de publicar o dashboard. Por predefinição, o dashboard será publicado num grupo de recursos com o nome **dashboards**. Selecione **Publicar**.
   
     ![publicar](./media/azure-portal-dashboard-share-access/publish.png)

Agora é publicado o seu dashboard. Se adequam-se as permissões herdadas da subscrição, não é necessário fazer mais nada. Outros utilizadores na sua organização poderá aceder e modificar o dashboard com base nas respetivas funções de nível de subscrição. No entanto, para este tutorial, vamos atribuir um grupo de utilizadores a uma função para esse dashboard.

## <a name="assign-access-to-a-dashboard"></a>Atribuir acesso a um dashboard
1. Depois de publicar o dashboard, selecione **gerir utilizadores**.
   
     ![gerir utilizadores](./media/azure-portal-dashboard-share-access/manage-users.png)
2. Verá uma lista de utilizadores existentes que já estão atribuídos uma função para este dashboard. Lista de utilizadores existentes será diferente do que a imagem abaixo. Provavelmente, as atribuições são herdadas da subscrição. Para adicionar um novo utilizador ou grupo, selecione **adicionar**.
   
     ![Adicionar utilizador](./media/azure-portal-dashboard-share-access/existing-users.png)
3. Selecione a função que representa as permissões que pretende conceder. Para este exemplo, selecione **contribuinte**.
   
     ![Selecionar função](./media/azure-portal-dashboard-share-access/select-role.png)
4. Selecione o utilizador ou grupo que pretende atribuir à função. Se não vir o utilizador ou grupo que procura na lista, utilize a caixa de pesquisa. Sua lista de grupos disponíveis dependem os grupos que criou no Active Directory.
   
     ![Selecionar utilizador](./media/azure-portal-dashboard-share-access/select-user.png) 
5. Quando tiver terminado de adicionar utilizadores ou grupos, selecione **OK**. 
6. A nova atribuição é adicionada à lista de utilizadores. Tenha em atenção que seus **acesso** está listado como **atribuído** vez **herdado**.
   
     ![funções atribuídas](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Passos Seguintes
* Para obter uma lista de funções, consulte [RBAC: Funções incorporadas](../role-based-access-control/built-in-roles.md).
* Para saber mais sobre a gestão de recursos, veja [recursos de gerir o Azure através do portal](resource-group-portal.md).

