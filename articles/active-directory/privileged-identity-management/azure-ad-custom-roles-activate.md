---
title: Ativar um papel personalizado da AD Azure na Gestão de Identidade Privilegiada (PIM) Microsoft Docs
description: Como ativar uma função personalizada da AD Azure para a atribuição de Gestão de Identidade Privilegiada (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06ceb2d2c98c3f9b6b184b98ddaadea610d525d2
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896569"
---
# <a name="activate-an-azure-ad-custom-role-in-privileged-identity-management"></a>Ativar um papel personalizado da AD Azure na Gestão de Identidade Privilegiada

A Gestão privilegiada de Identidade no Diretório Ativo azure (Azure AD) apoia agora a atribuição a tempo e tempo limitados a funções personalizadas criadas para gestão de aplicações na experiência administrativa de Gestão de Identidade e Acesso. Para obter mais informações sobre a criação de papéis personalizados para delegar a gestão de aplicações em Azure AD, consulte as funções de [administrador personalizado no Azure Ative Directory (pré-visualização)](../users-groups-roles/roles-custom-overview.md).

> [!NOTE]
> As funções personalizadas da Azure AD não estão integradas com as funções de diretório incorporada durante a pré-visualização. Uma vez que a capacidade esteja geralmente disponível, a gestão de papéis terá lugar na experiência de papéis incorporados.

## <a name="activate-a-role"></a>Ativar um papel

Quando necessitar de ativar uma função personalizada azure AD, solicite a ativação selecionando a opção de navegação das Minhas funções na Gestão de Identidade Privilegiada.

1. Inicie sessão no [do portal do Azure](https://portal.azure.com).
1. Open Azure AD [Privileged Identity Management.](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart)

1. Selecione **funções personalizadas azure ad** para ver uma lista das suas atribuições personalizadas de Azure AD elegíveis.

   ![Consulte a lista de atribuições personalizadas da Azure AD elegíveis](./media/azure-ad-custom-roles-activate/view-preview-roles.png)

> [!Note] 
>  Antes de atribuir um papel, deve criar/configurar um papel. Para mais informações sobre a configuração de Papéis Personalizados AAD consulte [aqui] (https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-ad-custom-roles-configure)

1. Na página de **funções personalizadas azure AD (Preview),** encontre a atribuição de que necessita.
1. Selecione **Ativar a sua função** para abrir a página **Ativar.**
1. Se o seu papel necessitar de autenticação multifactor, selecione **Verifique a sua identidade antes de prosseguir**. É obrigado a autenticar apenas uma vez por sessão.
1. Selecione Verificar a **minha identidade** e siga as instruções para fornecer qualquer verificação de segurança adicional.
1. Para especificar um âmbito de aplicação personalizado, selecione **Scope** para abrir o painel de filtro. Deve solicitar o acesso a um papel no âmbito mínimo necessário. Se a sua atribuição estiver num âmbito de aplicação, só pode ser ativada nesse âmbito.

   ![Atribuir um âmbito de recurso Azure AD à atribuição de funções](./media/azure-ad-custom-roles-activate/assign-scope.png)

1. Se necessário, especifique uma hora de início de ativação personalizada. Quando utilizado, o membro do papel é ativado no momento especificado.
1. Na caixa **Reason,** introduza o motivo do pedido de ativação. Estes podem ser necessários ou não na definição de funções.
1. Selecione **Ativar**.

Se a função não necessitar de aprovação, é ativada de acordo com as suas definições e é adicionada à lista de funções ativas. Se pretender utilizar a função ativada, comece com os passos em [Atribuir uma função personalizada de AD Azure na Gestão](azure-ad-custom-roles-assign.md)de Identidade Privilegiada .

Se a função necessitar de aprovação para ser ativada, receberá uma notificação azure informando-o de que o pedido está pendente de aprovação.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir uma função personalizada do Azure AD](azure-ad-custom-roles-assign.md)
- [Remover ou atualizar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configurar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-configure.md)
- [Definições de função no Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
