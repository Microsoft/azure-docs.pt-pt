---
title: Atribuir ou remover licenças - Diretório Ativo Azure / Microsoft Docs
description: Instruções sobre como atribuir ou remover licenças de Diretório Ativo Azure dos seus utilizadores ou grupos.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20ec1d1909a53a9de29e12be33957acfd1643698
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80128832"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Atribuir ou remover licenças no portal azure Ative Diretório

Muitos serviços de Diretório Ativo Azure (Azure AD) exigem que você licencie cada um dos seus utilizadores ou grupos (e membros associados) para esse serviço. Apenas os utilizadores com licenças ativas poderão aceder e utilizar os serviços AD Azure licenciados para os quais isso é verdade. As licenças são aplicadas por inquilino e não são transferidas para outros inquilinos. 

## <a name="available-license-plans"></a>Planos de licença disponíveis

Existem vários planos de licença disponíveis para o serviço Azure AD, incluindo:

- Azure AD Gratuito

- Azure AD Premium P1

- Azure AD Premium P2

Para obter informações específicas sobre cada plano de licença e os detalhes de licenciamento associados, veja [de que licença preciso?](https://azure.microsoft.com/pricing/details/active-directory/)

Nem todos os serviços da Microsoft estão disponíveis em todos os locais. Antes de uma licença poder ser atribuída a um grupo, deve especificar a localização de **utilização** para todos os membros. Pode definir este valor na área de Definições de **Perfil &gt; &gt; &gt; ** de Utilizadores de Diretório Ativo Azure em Azure AD. Qualquer utilizador cuja localização de utilização não esteja especificada herda a localização da organização Azure AD.

## <a name="view-license-plans-and-plan-details"></a>Ver planos de licença e detalhes do plano

Pode ver os seus planos de serviço disponíveis, incluindo as licenças individuais, verificar as datas de validade pendentes e ver o número de atribuições disponíveis.

### <a name="to-find-your-service-plan-and-plan-details"></a>Para encontrar o seu plano de serviço e detalhes do plano

1. Inscreva-se no [portal Azure](https://portal.azure.com/) utilizando uma conta de administrador de licença na sua organização Azure AD.

1. Selecione **Diretório Ativo Azure**e, em seguida, selecione **Licenças**.

    ![Página de licenças, com número de serviços adquiridos e licenças atribuídas](media/license-users-groups/license-details-blade.png)

1. Selecione o link **Adquirido** para ver a página **produtos** e para ver os números **atribuídos**, **disponíveis**e **expirando em breve** para os seus planos de licença.

    ![página de serviços, com planos de licença de serviço e informações de licença associadas](media/license-users-groups/license-products-blade-with-products.png)

1. Selecione um nome de plano para ver os seus utilizadores e grupos licenciados.

## <a name="assign-licenses-to-users-or-groups"></a>Atribuir licenças a utilizadores ou grupos

Certifique-se de que qualquer pessoa que precise de usar um serviço Azure AD licenciado tem a licença apropriada. Pode adicionar os direitos de licenciamento aos utilizadores ou a todo um grupo.

### <a name="to-assign-a-license-to-a-user"></a>Para atribuir uma licença a um utilizador

1. Na página **Produtos,** selecione o nome do plano de licença que pretende atribuir ao utilizador.

    ![página de serviços, com plano de licença de serviço destacado](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Na página de visão geral do plano de licença, **selecione Atribuir**.

    ![página de serviços, com opção de atribuição destacada](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Na página **Atribuir,** selecione **Utilizadores e grupos,** e depois procure e selecione o utilizador que está a atribuir à licença.

    ![Atribuir página de licença, com opções de pesquisa e select em destaque](media/license-users-groups/assign-license-blade-with-highlight.png)

1. Selecione **opções de Atribuição,** certifique-se de que tem as opções de licença adequadas ligadas e, em seguida, selecione **OK**.

    ![Página de opção de licença, com todas as opções disponíveis no plano de licença](media/license-users-groups/license-option-blade-assignments.png)

    As atualizações da página de atribuição de **licenças** para mostrar que um utilizador é selecionado e que as atribuições estão configuradas.

    > [!NOTE]
    > Nem todos os serviços da Microsoft estão disponíveis em todos os locais. Antes de uma licença poder ser atribuída a um utilizador, deve especificar a localização de **utilização**. Pode definir este valor na área de Definições de **Perfil &gt; &gt; &gt; ** de Utilizadores de Diretório Ativo Azure em Azure AD. Qualquer utilizador cuja localização de utilização não esteja especificada herda a localização da organização Azure AD.

1. Selecione **Atribuir**.

    O utilizador é adicionado à lista de utilizadores licenciados e tem acesso aos serviços de AD Azure incluídos.
    > [!NOTE]
    > As licenças também podem ser atribuídas diretamente a um utilizador a partir da página de **Licenças** do utilizador. Se um utilizador tiver uma licença atribuída através de uma associação de grupo e pretender atribuir a mesma licença diretamente ao utilizador, só pode ser feita a partir da página **produtos** mencionada apenas no passo 1.

### <a name="to-assign-a-license-to-a-group"></a>Para atribuir uma licença a um grupo

1. Na página **Produtos,** selecione o nome do plano de licença que pretende atribuir ao utilizador.

    ![Lâmina de produtos, com plano de licença de produto destacado](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Na página **Azure Ative Directory Premium Plan 2,** selecione **Atribuir**.

    ![Página de produtos, com opção de atribuição destacada](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Na página **Atribuir,** selecione **Utilizadores e grupos,** e depois procure e selecione o grupo que está a atribuir à licença.

    ![Atribuir página de licença, com opções de pesquisa e select em destaque](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. Selecione **opções de Atribuição,** certifique-se de que tem as opções de licença adequadas ligadas e, em seguida, selecione **OK**.

    ![Página de opção de licença, com todas as opções disponíveis no plano de licença](media/license-users-groups/license-option-blade-group-assignments.png)

    As atualizações da página de atribuição de **licenças** para mostrar que um utilizador é selecionado e que as atribuições estão configuradas.

1. Selecione **Atribuir**.

    O grupo é adicionado à lista de grupos licenciados e todos os membros têm acesso aos serviços da AD Azure incluídos.

## <a name="remove-a-license"></a>Remover uma licença

Pode remover uma licença da página de utilizador do AD Azure de um utilizador, da página de visão geral do grupo para uma atribuição de grupo, ou a partir da página de **Licenças** AD Azure para ver os utilizadores e grupos para uma licença.

### <a name="to-remove-a-license-from-a-user"></a>Para remover uma licença de um utilizador

1. Na página de **utilizadores Licenciados** para o plano de serviço, selecione o utilizador que já não deve ter a licença. Por exemplo, _Alain Charon._

1. Selecione **Remover a licença**.

    ![Página de utilizadores licenciados com opção Remover a opção de licença destacada](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> As licenças que um utilizador herda de um grupo não podem ser removidas diretamente. Em vez disso, tem de remover o utilizador do grupo do qual herda a licença.

### <a name="to-remove-a-license-from-a-group"></a>Para remover uma licença de um grupo

1. Na página dos **grupos Licenciados** para o plano de licença, selecione o grupo que já não deve ter a licença.

1. Selecione **Remover a licença**.

    ![Página de grupos licenciados com opção Remover a opção de licença destacada](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)
    
    > [!NOTE]
    > Quando uma conta de utilizador no local sincronizada com a AD Azure se desliga do âmbito para a sincronização ou quando a sincronização é removida, o utilizador é eliminado suavemente em AD Azure. Quando isso ocorrer, as licenças atribuídas ao utilizador diretamente ou através de licenciamento em grupo serão marcadas como **suspensas** e não **eliminadas**.

## <a name="next-steps"></a>Passos seguintes

Depois de atribuir as suas licenças, pode realizar os seguintes processos:

- [Identificar e resolver problemas de atribuição de licenças](../users-groups-roles/licensing-groups-resolve-problems.md)

- [Adicione utilizadores licenciados a um grupo para licenciamento](../users-groups-roles/licensing-groups-migrate-users.md)

- [Cenários, limitações e questões conhecidas usando grupos para gerir licenciamento no Diretório Ativo Azure](../users-groups-roles/licensing-group-advanced.md)

- [Adicionar ou alterar informações sobre o perfil](active-directory-users-profile-azure-portal.md)
