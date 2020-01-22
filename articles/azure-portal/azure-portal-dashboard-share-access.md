---
title: Compartilhar portal do Azure dashboards usando o RBAC | Microsoft Docs
description: Este artigo explica como compartilhar um painel no portal do Azure usando o controle de acesso baseado em função.
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
ms.date: 08/01/2016
ms.author: mblythe
ms.openlocfilehash: ce94a35ebcbf5d8cf3d176f05ac75ea5da5d8d99
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310752"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Partilhar dashboards do Azure mediante a utilização do Controlo de Acesso Baseado em Funções

Depois de configurar um painel, você pode publicá-lo e compartilhá-lo com outros usuários em sua organização. Você permite que outras pessoas exibam seu painel usando o [controle de acesso baseado em função](../role-based-access-control/role-assignments-portal.md)do Azure. Você atribui um usuário ou grupo de usuários a uma função, e essa função define se esses usuários podem exibir ou modificar o painel publicado. 

Todos os painéis publicados são implementados como recursos do Azure, o que significa que eles existem como itens gerenciáveis em sua assinatura e estão contidos em um grupo de recursos.  De uma perspectiva de controle de acesso, os painéis não são diferentes de outros recursos, como uma máquina virtual ou uma conta de armazenamento.

> [!TIP]
> Blocos individuais no painel impõem seus próprios requisitos de controle de acesso com base nos recursos que eles exibem.  Portanto, você pode criar um dashboard que é compartilhado amplamente enquanto ainda protege os dados em blocos individuais.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Entendendo o controle de acesso para dashboards
Com o RBAC (controle de acesso baseado em função), você pode atribuir usuários a funções em três níveis diferentes de escopo:

* subscrição
* grupo de recursos
* resource

As permissões que você atribui são herdadas da assinatura para o recurso. O painel publicado é um recurso. Portanto, talvez você já tenha usuários atribuídos a funções para a assinatura que também funcionam para o painel publicado. 

Eis um exemplo.  Digamos que você tenha uma assinatura do Azure e vários membros de sua equipe tenham recebido as funções de **proprietário**, **colaborador**ou **leitor** para a assinatura. Os usuários que são proprietários ou colaboradores são capazes de listar, exibir, criar, modificar ou excluir painéis dentro da assinatura.  Os usuários que são leitores podem listar e exibir painéis, mas não podem modificá-los ou excluí-los.  Os usuários com acesso de leitor podem fazer edições locais em um painel publicado (como, ao solucionar um problema), mas não podem publicar essas alterações de volta no servidor.  Eles terão a opção de fazer uma cópia privada do painel para si mesmos

No entanto, você também pode atribuir permissões ao grupo de recursos que contém vários dashboards ou a um Dashboard individual. Por exemplo, você pode decidir que um grupo de usuários deve ter permissões limitadas em toda a assinatura, mas maior acesso a um Dashboard específico. Atribua esses usuários a uma função para esse painel. 

## <a name="publish-dashboard"></a>Publicar o dashboard
Vamos supor que você tenha concluído a configuração de um dashboard que deseja compartilhar com um grupo de usuários em sua assinatura. As etapas a seguir descrevem um grupo personalizado chamado gerenciadores de armazenamento, mas você pode nomear o grupo como desejar. Para obter informações sobre como criar um grupo de Active Directory e adicionar usuários a esse grupo, consulte [Managing groups in Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

1. No painel, selecione **compartilhar**.
   
     ![selecionar compartilhamento](./media/azure-portal-dashboard-share-access/select-share.png)
2. Antes de atribuir acesso, você deve publicar o painel. Por padrão, o painel será publicado em um grupo de recursos chamado **dashboards**. Selecione **Publicar**.
   
     ![publicar](./media/azure-portal-dashboard-share-access/publish.png)

Seu painel agora está publicado. Se as permissões herdadas da assinatura forem adequadas, você não precisará fazer mais nada. Outros usuários em sua organização poderão acessar e modificar o Dashboard com base em sua função de nível de assinatura. No entanto, para este tutorial, vamos atribuir um grupo de usuários a uma função para esse painel.

## <a name="assign-access-to-a-dashboard"></a>Atribuir acesso a um painel
1. Depois de publicar o painel, selecione **gerenciar usuários**.
   
     ![gerir utilizadores](./media/azure-portal-dashboard-share-access/manage-users.png)
2. Você verá uma lista de usuários existentes que já atribuiram uma função para esse painel. Sua lista de usuários existentes será diferente da imagem abaixo. Provavelmente, as atribuições são herdadas da assinatura. Para adicionar um novo usuário ou grupo, selecione **Adicionar**.
   
     ![Adicionar usuário](./media/azure-portal-dashboard-share-access/existing-users.png)
3. Selecione a função que representa as permissões que você gostaria de conceder. Para este exemplo, selecione **colaborador**.
   
     ![selecionar função](./media/azure-portal-dashboard-share-access/select-role.png)
4. Selecione o usuário ou grupo que você deseja atribuir à função. Se você não vir o usuário ou grupo que está procurando na lista, use a caixa de pesquisa. Sua lista de grupos disponíveis dependerá dos grupos que você criou em seu Active Directory.
   
     ![Selecionar usuário](./media/azure-portal-dashboard-share-access/select-user.png) 
5. Quando terminar de adicionar usuários ou grupos, selecione **OK**. 
6. A nova atribuição é adicionada à lista de usuários. Observe que seu **acesso** está listado como **atribuído** , em vez de **herdado**.
   
     ![funções atribuídas](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Passos seguintes
* Para obter uma lista de funções, consulte [RBAC: funções internas](../role-based-access-control/built-in-roles.md).
* Para saber mais sobre como gerenciar recursos, consulte [gerenciar recursos do Azure por meio do portal](resource-group-portal.md).

