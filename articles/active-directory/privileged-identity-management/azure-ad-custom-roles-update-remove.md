---
title: Atualizar ou remover uma atribuição de função personalizada do Azure AD no Privileged Identity Management (PIM) | Microsoft Docs
description: Como atualizar ou remover um PIM (Privileged Identity Management de atribuição de função personalizada) do Azure AD
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccbc9fbd763b9393a64d6cfc29f6b5a9d021da88
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756345"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Atualizar ou remover uma função personalizada do Azure AD atribuída no Privileged Identity Management

Este artigo mostra como usar o Privileged Identity Management (PIM) para atualizar ou remover atribuição just-in-time e limite de tempo para funções personalizadas criadas para gerenciamento de aplicativos na experiência administrativa do Azure Active Directory (AD do Azure). 

- Para obter mais informações sobre como criar funções personalizadas para delegar o gerenciamento de aplicativos no Azure AD, consulte [funções de administrador personalizadas no Azure Active Directory (versão prévia)](../users-groups-roles/roles-custom-overview.md). 
- Se você ainda não usou o Privileged Identity Management, obtenha mais informações em [começar a usar o Privileged Identity Management](pim-getting-started.md).

> [!NOTE]
> As funções personalizadas do Azure AD não são integradas às funções de diretório internas durante a visualização. Assim que o recurso estiver disponível, o gerenciamento de função de gerenciamento de função ocorrerá na experiência de funções internas.

## <a name="update-or-remove-an-assignment"></a>Atualizar ou remover uma atribuição

Siga estas etapas para atualizar ou remover uma atribuição de função personalizada existente.

1. Entre em [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) no portal do Azure com uma conta de usuário atribuída à função de administrador de função com privilégios.
1. Selecione **funções personalizadas do Azure AD (versão prévia)** .

    ![Selecione a visualização de funções personalizadas do Azure AD para ver as atribuições de função qualificadas](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Selecione **funções** para ver a lista de **atribuições** de funções personalizadas para aplicativos do Azure AD.

    ![Selecione funções ver a lista de atribuições de função qualificadas](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. Selecione a função que você deseja atualizar ou remover.
1. Localize a atribuição de função nas guias **funções qualificadas** ou **funções ativas** .
1. Selecione **Atualizar** ou **remover** para atualizar ou remover a atribuição de função.

    ![Selecione remover ou atualizar na atribuição de função qualificada](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>Passos seguintes

- [Ativar uma função personalizada do Azure AD](azure-ad-custom-roles-assign.md)
- [Atribuir uma função personalizada do Azure AD](azure-ad-custom-roles-assign.md)
- [Configurar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-configure.md)
