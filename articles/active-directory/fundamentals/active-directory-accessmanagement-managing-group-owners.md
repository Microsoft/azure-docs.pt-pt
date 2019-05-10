---
title: Adicionar ou remover os proprietários do grupo - Azure Active Directory | Documentos da Microsoft
description: Instruções sobre como adicionar ou remover do grupo proprietários com o Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd684e1bd48f877a74280b33b4df65d7baaa0fe7
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507177"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Adicionar ou remover os proprietários do grupo no Azure Active Directory
Os grupos do Active Directory (Azure AD) do Azure são detidos e geridos pelo proprietários do grupo. Os proprietários do grupo podem ser utilizadores ou principais de serviço e são capazes de gerir o grupo, incluindo a associação. Apenas os proprietários do grupo existente ou gestão de grupo de administradores, podem atribuir onwers de grupo. Os proprietários do grupo não têm de ser membros do grupo.

Quando um grupo não tem proprietários, os administradores de gestão de grupo ainda conseguem gerir o grupo.

## <a name="add-an-owner-to-a-group"></a>Adicionar proprietário a um grupo
A seguir as instruções para adicionar um utilizador como proprietário a um grupo estão usando o portal do Azure AD. Para adicionar um principal de serviço como um proprietário de um grupo, siga as instruções para fazer isso usando [PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

### <a name="to-add-a-group-owner"></a>Para adicionar um proprietário do grupo
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **do Azure Active Directory**, selecione **grupos**e, em seguida, selecione o grupo para o qual pretende adicionar um proprietário (neste exemplo, *política da MDM - oeste*).

3. Sobre o **política MDM - descrição geral do Oeste** página, selecione **proprietários**.

    ![Política MDM - página de descrição geral do oeste com a opção de proprietários realçada](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Na **MDM política - oeste - proprietários** página, selecione **adicionar proprietários**e, em seguida, procure e selecione o utilizador que será o novo proprietário de grupo e, em seguida, escolha **selecionar**.

    ![Página de proprietários de - oeste - política MDM com a opção de proprietários de adicionar realçada](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Depois de selecionar o novo proprietário, pode atualizar o **proprietários** página e ver o nome adicionado à lista de proprietários.

## <a name="remove-an-owner-from-a-group"></a>Remover um proprietário de um grupo
Remova um proprietário de um grupo com o Azure AD.

### <a name="to-remove-an-owner"></a>Para remover um proprietário
1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta de Administrador global do diretório.

2. Selecione **do Azure Active Directory**, selecione **grupos**e, em seguida, selecione o grupo para o qual pretende remover um proprietário (neste exemplo, *política da MDM - oeste*).

3. Sobre o **política MDM - descrição geral do Oeste** página, selecione **proprietários**.

    ![Política MDM - página de descrição geral do oeste com a opção de proprietários realçada](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Na **MDM política - oeste - proprietários** página, selecione o utilizador que pretende remover como proprietário de um grupo, escolha **remover** na página de informações do usuário e selecione **Sim** para confirmar sua decisão.

    ![Página de informações do utilizador com a opção de remover realçada](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Depois de remover o proprietário, pode retornar para o **proprietários** página e ver o nome foi removido da lista de proprietários.

## <a name="next-steps"></a>Passos Seguintes
- [Gerir o acesso aos recursos com grupos do Azure Active Directory](active-directory-manage-groups.md)

- [Cmdlets do Azure Active Directory para configurar definições de grupo](../users-groups-roles/groups-settings-cmdlets.md)

- [Utilizar grupos para atribuir acesso a uma aplicação SaaS integrada](../users-groups-roles/groups-saasapps.md)

- [Integrar as identidades no local ao Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Cmdlets do Azure Active Directory para configurar definições de grupo](../users-groups-roles/groups-settings-v2-cmdlets.md)
