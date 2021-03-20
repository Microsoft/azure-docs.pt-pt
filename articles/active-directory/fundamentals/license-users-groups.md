---
title: Atribuir ou remover licenças - Azure Ative Directory | Microsoft Docs
description: Instruções sobre como atribuir ou remover licenças do Azure Ative Directory dos seus utilizadores ou grupos.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6900647acf7182529f34c8cc065dbb039de38be
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97504411"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Atribuir ou remover licenças no portal Azure Ative Directory

Muitos serviços do Azure Ative Directory (Azure AD) exigem que você licencie cada um dos seus utilizadores ou grupos (e membros associados) para esse serviço. Apenas os utilizadores com licenças ativas poderão aceder e utilizar os serviços licenciados Azure AD para os quais isso é verdade. As licenças são aplicadas por arrendatário e não são transferidas para outros inquilinos. 

## <a name="available-license-plans"></a>Planos de licença disponíveis

Existem vários planos de licença disponíveis para o serviço Azure AD, incluindo:

- Azure AD Gratuito

- Azure AD Premium P1

- Azure AD Premium P2

Para obter informações específicas sobre cada plano de licença e os detalhes de licenciamento associados, veja [que licença preciso?](https://azure.microsoft.com/pricing/details/active-directory/) Para se inscrever nos planos de licença premium Azure AD veja [aqui.](./active-directory-get-started-premium.md)

Nem todos os serviços da Microsoft estão disponíveis em todos os locais. Antes de uma licença poder ser atribuída a um grupo, deve especificar a **localização de utilização** para todos os membros. Pode definir este valor na área de **&gt; &gt; &gt; Definições de Perfil dos Utilizadores do Diretório Ativo Azure** em Azure AD. Qualquer utilizador cuja localização de utilização não seja especificada herda a localização da organização Azure AD.

## <a name="view-license-plans-and-plan-details"></a>Ver planos de licença e detalhes do plano

Pode consultar os seus planos de serviço disponíveis, incluindo as licenças individuais, verificar as datas de validade pendentes e ver o número de atribuições disponíveis.

### <a name="to-find-your-service-plan-and-plan-details"></a>Para encontrar o seu plano de serviço e planear detalhes

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando uma conta de administrador de licença na sua organização Azure AD.

1. Selecione **Azure Ative Directory** e, em seguida, selecione **Licenças**.

    :::image type="content" source="media/license-users-groups/license-details-blade.png" alt-text="Página de licenças, com número de serviços adquiridos e licenças atribuídas":::

1. Selecione **Todos os produtos** para ver a página Todos os Produtos e para ver os números **Total,** **Atribuídos,** **Disponíveis** e **Expirando em breve** para os seus planos de licença.

    :::image type="content" source="media/license-users-groups/license-products-blade-with-products.png" alt-text="página de serviços - com planos de licença de serviço - informações de licença associadas":::

    > [!NOTE]
    > Os números são definidos como: 
    > - Total: Número total de licenças adquiridas
    > - Atribuído: Número de licenças atribuídas aos utilizadores
    > - Disponível: Número de licenças disponíveis para atribuição, incluindo a expiração em breve
    > - Expiração em breve: Número de licenças expirando em breve

1. Selecione um nome de plano para ver os seus utilizadores e grupos licenciados.

## <a name="assign-licenses-to-users-or-groups"></a>Atribuir licenças a utilizadores ou grupos

Certifique-se de que qualquer pessoa que precise de usar um serviço AD Azure licenciado tem a licença apropriada. Pode adicionar os direitos de licenciamento aos utilizadores ou a um grupo inteiro.

### <a name="to-assign-a-license-to-a-user"></a>Atribuir uma licença a um utilizador

1. Na página **Produtos,** selecione o nome do plano de licença que pretende atribuir ao utilizador.

    ![página de serviços, com plano de licença de serviço destacado](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Na página geral do plano de licença, selecione **Atribuir**.

    ![página de serviços, com opção de atribuição realçada](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Na página **'Atribuir',** selecione **Utilizadores e grupos**, e, em seguida, procure e selecione o utilizador que está a atribuir a licença.

    ![Atribuir página de licença, com pesquisa destacada e Selecionar opções](media/license-users-groups/assign-license-blade-with-highlight.png)

1. Selecione **opções de atribuição**, certifique-se de que tem as opções de licença apropriadas ligadas e, em seguida, selecione **OK**.

    ![Página de opção de licença, com todas as opções disponíveis no plano de licença](media/license-users-groups/license-option-blade-assignments.png)

    As atualizações da página **da licença atribuir** para mostrar que um utilizador é selecionado e que as atribuições estão configuradas.

    > [!NOTE]
    > Nem todos os serviços da Microsoft estão disponíveis em todos os locais. Antes de uma licença poder ser atribuída a um utilizador, tem de especificar a **localização de Utilização**. Pode definir este valor na área de **&gt; &gt; &gt; Definições de Perfil dos Utilizadores do Diretório Ativo Azure** em Azure AD. Qualquer utilizador cuja localização de utilização não seja especificada herda a localização da organização Azure AD.

1. Selecione **Atribuir**.

    O utilizador é adicionado à lista de utilizadores licenciados e tem acesso aos serviços AD AD incluídos.
    > [!NOTE]
    > As licenças também podem ser atribuídas diretamente a um utilizador a partir da página **licenças** do utilizador. Se um utilizador tiver uma licença atribuída através de uma associação de grupo e pretender atribuir a mesma licença diretamente ao utilizador, só pode ser feita a partir da página **Produtos** mencionada apenas no passo 1.

### <a name="to-assign-a-license-to-a-group"></a>Atribuir uma licença a um grupo

1. Na página **Produtos,** selecione o nome do plano de licença que pretende atribuir ao utilizador.

    ![Lâmina de produtos, com plano de licença de produto em destaque](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Na página **Azure Ative Directory Premium Plan 2,** selecione **Atribuir**.

    ![Página de produtos, com opção de atribuição realçada](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Na página **'Atribuir',** selecione **Utilizadores e grupos** e, em seguida, procure e selecione o grupo que está a atribuir a licença.

    ![Atribuir página de licença, com pesquisa destacada e Selecionar opções 2](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. Selecione **opções de atribuição**, certifique-se de que tem as opções de licença apropriadas ligadas e, em seguida, selecione **OK**.

    ![Página de opção de licença, com todas as opções disponíveis no plano de licença 2](media/license-users-groups/license-option-blade-group-assignments.png)

    As atualizações da página **da licença atribuir** para mostrar que um utilizador é selecionado e que as atribuições estão configuradas.

1. Selecione **Atribuir**.

    O grupo é adicionado à lista de grupos licenciados e todos os membros têm acesso aos serviços AD AD incluídos.

## <a name="remove-a-license"></a>Remover uma licença

Pode remover uma licença da página de utilizador Azure AD de um utilizador, da página de visão geral do grupo para uma atribuição de grupo, ou a partir da página Azure AD **Licenses** para ver os utilizadores e grupos para obter uma licença.

### <a name="to-remove-a-license-from-a-user"></a>Para remover uma licença de um utilizador

1. Na página **de utilizadores licenciados** para o plano de serviço, selecione o utilizador que já não deve ter a licença. Por exemplo, _Alain Caronte._

1. Selecione **Remover a licença**.

    ![Página de utilizadores licenciados com opção de licença remover em destaque](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> As licenças que um utilizador herda de um grupo não podem ser removidas diretamente. Em vez disso, tem de remover o utilizador do grupo do qual herda a licença.

### <a name="to-remove-a-license-from-a-group"></a>Para remover uma licença de um grupo

1. Na página de **grupos licenciados** para o plano de licença, selecione o grupo que já não deve ter a licença.

1. Selecione **Remover a licença**.

    ![Página de grupos licenciados com opção de licença Remover destacada 2](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)
    
    > [!NOTE]
    > Quando uma conta de utilizador no local sincronizada com a Azure AD cai fora do alcance da sincronização ou quando a sincronização é removida, o utilizador é eliminado em Azure AD. Quando isto ocorrer, as licenças atribuídas ao utilizador diretamente ou através de licenças baseadas em grupo serão marcadas como **suspensas** em vez de **eliminadas**.

## <a name="next-steps"></a>Passos seguintes

Depois de ter atribuído as suas licenças, pode realizar os seguintes processos:

- [Identificar e resolver problemas de atribuição de licenças](../enterprise-users/licensing-groups-resolve-problems.md)

- [Adicionar utilizadores licenciados a um grupo para licenciamento](../enterprise-users/licensing-groups-migrate-users.md)

- [Cenários, limitações e questões conhecidas usando grupos para gerir o licenciamento no Azure Ative Directory](../enterprise-users/licensing-group-advanced.md)

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)
