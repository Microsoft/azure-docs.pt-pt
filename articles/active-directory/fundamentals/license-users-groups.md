---
title: Atribuir ou remover licenças - Azure Active Directory | Documentos da Microsoft
description: Instruções sobre como atribuir ou remover licenças do Azure Active Directory, os usuários ou grupos.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 869713967a6a89611ab2ea9ca7b7516f090a5f92
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034260"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Atribuir ou remover licenças no portal de Azure Active Directory

Muitos serviços do Azure Active Directory (AD do Azure) exigem que você licenciasse cada um de seus usuários ou grupos (e membros associados) para esse serviço. Somente os usuários com licenças ativas poderão acessar e usar os serviços do Azure AD licenciados para os quais isso é verdade.

## <a name="available-license-plans"></a>Planos de licença disponíveis

Há vários planos de licença disponíveis para o serviço do Azure AD, incluindo:

- Azure AD Gratuito

- Azure AD Premium P1

- Azure AD Premium P2

Para obter informações específicas sobre cada plano de licença e os detalhes de licenciamento associados, consulte [qual licença eu preciso?](https://azure.microsoft.com/pricing/details/active-directory/).

Nem todos os serviços da Microsoft estão disponíveis em todas as localizações. Antes de uma licença pode ser atribuída a um grupo, tem de especificar o **localização de utilização** para todos os membros. Pode definir este valor **do Azure Active Directory &gt; usuários &gt; perfil &gt; definições** área no Azure AD. Qualquer usuário cujo local de uso não seja especificado herda o local da organização do Azure AD.

## <a name="view-license-plans-and-plan-details"></a>Exibir planos de licença e detalhes do plano

Você pode exibir os planos de serviço disponíveis, incluindo as licenças individuais, verificar datas de vencimento pendentes e exibir o número de atribuições disponíveis.

### <a name="to-find-your-service-plan-and-plan-details"></a>Para localizar o plano de serviço e os detalhes do plano

1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador de licenças na sua organização do Azure AD.

1. Selecione **do Azure Active Directory**e, em seguida, selecione **licenças**.

    ![Página licenças, com número de serviços comprados e licenças atribuídas](media/license-users-groups/license-details-blade.png)

1. Selecione o link **comprado** para exibir a **página produtos** e ver os números em breve **atribuídos**, **disponíveis**e **expirando** para seus planos de licença.

    ![página de serviços, com planos de licença de serviço e informações de licença associadas](media/license-users-groups/license-products-blade-with-products.png)

1. Selecione um nome de plano para ver seus usuários e grupos licenciados.

## <a name="assign-licenses-to-users-or-groups"></a>Atribuir licenças a utilizadores ou grupos

Certifique-se de que alguém precisar usar um licenciada serviço do Azure AD tem a licença adequada. Você pode adicionar os direitos de licenciamento a usuários ou a um grupo inteiro.

### <a name="to-assign-a-license-to-a-user"></a>Para atribuir uma licença a um usuário

1. Na página **produtos** , selecione o nome do plano de licença que você deseja atribuir ao usuário.

    ![página de serviços, com plano de licença de serviço realçado](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Na página Visão geral do plano de licença, selecione **atribuir**.

    ![página serviços, com a opção atribuir realçada](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Sobre o **atribuir** página, selecione **utilizadores e grupos**e, em seguida, procure e selecione o utilizador está a atribuir a licença.

    ![Atribuir a página de licença, com pesquisa destacada e selecione as opções](media/license-users-groups/assign-license-blade-with-highlight.png)

1. Selecione **opções de atribuição**, certifique-se de que tem o adequado licenciar opções está ativada e, em seguida, selecione **OK**.

    ![Página de opção de licença, com todas as opções disponíveis no plano de licença](media/license-users-groups/license-option-blade-assignments.png)

    O **atribuir licenças** página atualizações para mostrar que um usuário está selecionado e que as atribuições estão configuradas.

    > [!NOTE]
    > Nem todos os serviços da Microsoft estão disponíveis em todas as localizações. Antes de uma licença pode ser atribuída a um utilizador, tem de especificar o **localização de utilização**. Pode definir este valor **do Azure Active Directory &gt; usuários &gt; perfil &gt; definições** área no Azure AD. Qualquer usuário cujo local de uso não seja especificado herda o local da organização do Azure AD.

1. Selecione **Atribuir**.

    O utilizador é adicionado à lista de utilizadores licenciados e tem acesso a incluído para serviços do Azure AD.

### <a name="to-assign-a-license-to-a-group"></a>Para atribuir uma licença a um grupo

1. Na página **produtos** , selecione o nome do plano de licença que você deseja atribuir ao usuário.

    ![Folha produtos, com plano de licença do produto realçado](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Sobre o **do Azure Active Directory Premium plano 2** página, selecione **atribuir**.

    ![Página de produtos, com a opção de atribuir realçada](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Sobre o **atribuir** página, selecione **utilizadores e grupos**e, em seguida, procure e selecione o grupo está a atribuir a licença.

    ![Atribuir a página de licença, com pesquisa destacada e selecione as opções](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. Selecione **opções de atribuição**, certifique-se de que tem o adequado licenciar opções está ativada e, em seguida, selecione **OK**.

    ![Página de opção de licença, com todas as opções disponíveis no plano de licença](media/license-users-groups/license-option-blade-group-assignments.png)

    O **atribuir licenças** página atualizações para mostrar que um usuário está selecionado e que as atribuições estão configuradas.

1. Selecione **Atribuir**.

    O grupo é adicionado à lista de grupos licenciados e todos os membros têm acesso ao incluído serviços do Azure AD.

## <a name="remove-a-license"></a>Remover uma licença

Você pode remover uma licença da página de usuário do Azure AD de um usuário, na página Visão geral do grupo de uma atribuição de grupo ou a partir da página **licenças** do Azure ad para ver os usuários e grupos de uma licença.

### <a name="to-remove-a-license-from-a-user"></a>Para remover uma licença de um usuário

1. Na página **usuários licenciados** para o plano de serviço, selecione o usuário que não deve mais ter a licença. Por exemplo, _Alain Charon_.

1. Selecione **remover licença**.

    ![Página dos usuários licenciados com a opção de licença de remover realçada](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> As licenças que um usuário herda de um grupo não podem ser removidas diretamente. Em vez disso, terá de remover o utilizador do grupo do qual eles estão a herdar a licença.

### <a name="to-remove-a-license-from-a-group"></a>Para remover uma licença a partir de um grupo

1. Na página **grupos licenciados** do plano de licença, selecione o grupo que não deve mais ter a licença.

1. Selecione **remover licença**.

    ![Página de grupos licenciados com a opção de licença de remover realçada](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)

## <a name="next-steps"></a>Passos seguintes

Depois de atribuir as licenças, pode efetuar os seguintes processos:

- [Identificar e resolver problemas de atribuição de licença](../users-groups-roles/licensing-groups-resolve-problems.md)

- [Adicionar os utilizadores licenciados a um grupo para licenciamento](../users-groups-roles/licensing-groups-migrate-users.md)

- [Cenários, limitações e problemas conhecidos, utilizar grupos para gerir o licenciamento no Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)

- [Adicionar ou alterar as informações de perfil](active-directory-users-profile-azure-portal.md)
