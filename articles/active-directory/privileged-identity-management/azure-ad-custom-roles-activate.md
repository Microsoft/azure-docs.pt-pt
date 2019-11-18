---
title: Ativar uma função personalizada do Azure AD no Privileged Identity Management (PIM) | Microsoft Docs
description: Como ativar uma função personalizada do Azure AD para o Privileged Identity Management de atribuição (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
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
ms.openlocfilehash: 92ea116414e12cb231fdbaa5ec9ad375d2c0eea9
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144991"
---
# <a name="activate-an-azure-ad-custom-role-in-privileged-identity-management"></a>Ativar uma função personalizada do Azure AD no Privileged Identity Management

Privileged Identity Management no Azure Active Directory (AD do Azure) agora dá suporte à atribuição just-in-time e de limite de tempo a funções personalizadas criadas para gerenciamento de aplicativos na experiência administrativa de gerenciamento de identidades e acesso. Para obter mais informações sobre como criar funções personalizadas para delegar o gerenciamento de aplicativos no Azure AD, consulte [funções de administrador personalizadas no Azure Active Directory (versão prévia)](../users-groups-roles/roles-custom-overview.md).

> [!NOTE]
> As funções personalizadas do Azure AD não são integradas às funções de diretório internas durante a visualização. Assim que o recurso estiver disponível, o gerenciamento de função ocorrerá na experiência de funções internas.

## <a name="activate-a-role"></a>Ativar uma função

Quando precisar ativar uma função personalizada do Azure AD, solicite a ativação selecionando a opção de navegação minhas funções em Privileged Identity Management.

1. Inicie sessão no [do portal do Azure](https://portal.azure.com).
1. Abra o Azure AD [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart).

1. Selecione **funções personalizadas do Azure ad** para ver uma lista de suas atribuições de função personalizadas do Azure ad qualificadas.

   ![Consulte a lista de atribuições de função personalizada do Azure AD qualificadas](./media/azure-ad-custom-roles-activate/view-preview-roles.png)

> [!Note] 
>  Antes de atribuir uma função, você deve criar/configurar uma função. Para obter mais informações sobre como configurar funções personalizadas do AAD, consulte [aqui] (https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-ad-custom-roles-configure)

1. Na página **funções personalizadas do Azure AD (versão prévia)** , localize a atribuição que você precisa.
1. Selecione **ativar sua função** para abrir a página **Ativar** .
1. Se sua função exigir autenticação multifator, selecione **verificar sua identidade antes de continuar**. Você deve autenticar apenas uma vez por sessão.
1. Selecione **verificar minha identidade** e siga as instruções para fornecer qualquer verificação de segurança adicional.
1. Para especificar um escopo de aplicativo personalizado, selecione **escopo** para abrir o painel de filtro. Você deve solicitar acesso a uma função no escopo mínimo necessário. Se sua atribuição estiver em um escopo de aplicativo, você poderá ativar somente nesse escopo.

   ![Atribuir um escopo de recursos do Azure AD à atribuição de função](./media/azure-ad-custom-roles-activate/assign-scope.png)

1. Se necessário, especifique uma hora de início de ativação personalizada. Quando usado, o membro da função é ativado na hora especificada.
1. Na caixa **motivo** , insira o motivo para a solicitação de ativação. Eles podem ser necessários ou não na configuração da função.
1. Selecione **Ativar**.

Se a função não exigir aprovação, ela será ativada de acordo com suas configurações e adicionada à lista de funções ativas. Se você quiser usar a função ativada, comece com as etapas em [atribuir uma função personalizada do Azure AD no Privileged Identity Management](azure-ad-custom-roles-assign.md).

Se a função exigir aprovação para ativar, você receberá uma notificação do Azure informando que a solicitação está com aprovação pendente.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir uma função personalizada do Azure AD](azure-ad-custom-roles-assign.md)
- [Remover ou atualizar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configurar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-configure.md)
- [Definições de função no Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
