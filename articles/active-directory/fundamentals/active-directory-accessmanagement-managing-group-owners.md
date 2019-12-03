---
title: Adicionar ou remover proprietários de grupo-Azure Active Directory | Microsoft Docs
description: Instruções sobre como adicionar ou remover proprietários de grupo usando Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ee4b452d087bfef0e9f5f7c820870da0df8dc3e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74683942"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Adicionar ou remover proprietários de grupo no Azure Active Directory
Os grupos do Azure Active Directory (AD do Azure) são de propriedade e são gerenciados por proprietários do grupo. Os proprietários do grupo podem ser usuários ou entidades de serviço e podem gerenciar o grupo, incluindo a associação. Somente proprietários de grupo existentes ou administradores de gerenciamento de grupo podem atribuir proprietários de grupo. Os proprietários do grupo não precisam ser membros do grupo.

Quando um grupo não tem proprietário, administradores de grupo de gerenciamento ainda podem gerenciar o grupo.

## <a name="add-an-owner-to-a-group"></a>Adicionar um proprietário a um grupo
Abaixo estão as instruções para adicionar um usuário como um proprietário a um grupo usando o portal do AD do Azure. Para adicionar uma entidade de serviço como um proprietário de um grupo, siga as instruções para fazer isso usando o [PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

### <a name="to-add-a-group-owner"></a>Para adicionar um proprietário de grupo
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **Azure Active Directory**, selecione **grupos**e, em seguida, selecione o grupo ao qual você deseja adicionar um proprietário (para este exemplo, *política de MDM-oeste*).

3. Na página **política de MDM – visão geral do oeste** , selecione **proprietários**.

    ![Política de MDM-página Visão geral do oeste com a opção proprietários realçada](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Na página **política de MDM-oeste-proprietários** , selecione **Adicionar proprietários**e, em seguida, procure e selecione o usuário que será o novo proprietário do grupo e escolha **selecionar**.

    ![Política de MDM – página proprietários do oeste com a opção Adicionar proprietários realçada](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Depois de selecionar o novo proprietário, você pode atualizar a página **proprietários** e ver o nome adicionado à lista de proprietários.

## <a name="remove-an-owner-from-a-group"></a>Remover um proprietário de um grupo
Remova um proprietário de um grupo usando o Azure AD.

### <a name="to-remove-an-owner"></a>Para remover um proprietário
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **Azure Active Directory**, selecione **grupos**e, em seguida, selecione o grupo para o qual você deseja remover um proprietário (para este exemplo, *política de MDM-oeste*).

3. Na página **política de MDM – visão geral do oeste** , selecione **proprietários**.

    ![Política de MDM-página Visão geral do oeste com a opção proprietários realçada](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Na página **política de MDM-oeste-proprietários** , selecione o usuário que você deseja remover como proprietário do grupo, escolha **remover** na página informações do usuário e selecione **Sim** para confirmar sua decisão.

    ![Página informações do usuário com a opção remover realçada](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Depois de remover o proprietário, você pode retornar à página **proprietários** e ver se o nome foi removido da lista de proprietários.

## <a name="next-steps"></a>Passos seguintes
- [Gerir o acesso aos recursos com grupos do Azure Active Directory](active-directory-manage-groups.md)

- [Cmdlets do Azure Active Directory para configurar definições de grupo](../users-groups-roles/groups-settings-cmdlets.md)

- [Usar grupos para atribuir acesso a um aplicativo SaaS integrado](../users-groups-roles/groups-saasapps.md)

- [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Cmdlets do Azure Active Directory para configurar definições de grupo](../users-groups-roles/groups-settings-v2-cmdlets.md)
