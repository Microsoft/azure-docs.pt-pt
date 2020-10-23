---
title: Ativar o papel personalizado Azure AD - Gestão de Identidade Privilegiada (PIM)
description: Como ativar um papel personalizado Azure AD para a atribuição gestão de identidade privilegiada (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c0d98641f8e2040de8350b7dd0231c2e7c889c9
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371618"
---
# <a name="activate-an-azure-ad-custom-role-in-privileged-identity-management"></a>Ativar um papel personalizado da AD AD em Gestão de Identidade Privilegiada

A Gestão privilegiada de Identidade em Diretório Ativo Azure (Azure AD) suporta agora a atribuição just-in-time e time-bound a funções personalizadas criadas para Gestão de Aplicações na experiência administrativa de Gestão de Identidade e Acesso. Para obter mais informações sobre a criação de funções personalizadas para delegar a gestão de aplicações em Azure AD, consulte [as funções de administrador personalizado no Azure Ative Directory (pré-visualização)](../roles/custom-overview.md).

> [!NOTE]
> As funções personalizadas AZURE AD não são integradas com as funções de diretório incorporado durante a pré-visualização. Uma vez que a capacidade esteja geralmente disponível, a gestão de funções terá lugar na experiência de funções incorporadas. Se vir o seguinte banner, estas funções devem ser geridas [na experiência de funções incorporadas](pim-how-to-activate-role.md) e este artigo não se aplica:
>
> :::image type="content" source="media/pim-how-to-add-role-to-user/pim-new-version.png" alt-text="Selecione Gestão de Identidade Privilegiada em Azure AD." lightbox="media/pim-how-to-add-role-to-user/pim-new-version.png":::

## <a name="activate-a-role"></a>Ativar um papel

Quando precisar de ativar uma função personalizada Azure AD, solicite a ativação selecionando a opção de navegação das minhas funções na Gestão de Identidade Privilegiada.

1. Inscreva-se [no portal Azure](https://portal.azure.com).
1. Open Azure AD [Gestão de Identidade Privilegiada.](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart)

1. Selecione **as funções personalizadas Azure AD** para ver uma lista das suas atribuições de funções personalizadas Azure AD elegíveis.

   ![Consulte a lista de atribuições de funções personalizadas elegíveis da Azure AD](./media/azure-ad-custom-roles-activate/view-preview-roles.png)

> [!Note] 
> Antes de atribuir um papel, deve criar/configurar um papel. Para obter mais informações sobre a configuração de funções personalizadas da AAD, consulte [as funções personalizadas Configure Azure AD na Gestão de Identidade Privilegiada.](azure-ad-custom-roles-configure.md)

1. Na página **de funções personalizadas Azure AD (Pré-visualização),** encontre a atribuição de que necessita.
1. **Selecione Ative a sua função** para abrir a página **Ativar.**
1. Se a sua função necessitar de autenticação multi-factor, **selecione Verifique a sua identidade antes de prosseguir**. É-lhe exigido que autente apenas uma vez por sessão.
1. **Selecione Verifique a minha identidade** e siga as instruções para fornecer qualquer verificação adicional de segurança.
1. Para especificar um âmbito de aplicação personalizado, selecione **Scope** para abrir o painel de filtros. Deve solicitar o acesso a uma função no âmbito mínimo necessário. Se a sua atribuição estiver no âmbito da aplicação, só pode ser ativada nesse âmbito.

   ![Atribuir um âmbito de recurso AD AZure à atribuição de funções](./media/azure-ad-custom-roles-activate/assign-scope.png)

1. Se necessário, especifique uma hora de início de ativação personalizada. Quando utilizado, o membro da função é ativado no momento especificado.
1. Na caixa **Reason,** insira o motivo do pedido de ativação. Estes podem ser necessários ou não na definição de função.
1. **Selecione Ativar**.

Se a função não necessitar de aprovação, é ativada de acordo com as suas definições e é adicionada à lista de funções ativas. Se pretender utilizar a função ativada, comece com os passos na [Atribuição de um papel personalizado AZURE AD na Gestão de Identidade Privilegiada.](azure-ad-custom-roles-assign.md)

Se a função necessitar de aprovação para ser ativada, receberá uma notificação do Azure informando-o de que o pedido está pendente de aprovação.

## <a name="next-steps"></a>Passos seguintes

- [Atribuir um papel personalizado AZure AD](azure-ad-custom-roles-assign.md)
- [Remova ou atualize uma atribuição de função personalizada Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configure uma atribuição de função personalizada Azure AD](azure-ad-custom-roles-configure.md)
- [Definições de função em Azure AD](../roles/permissions-reference.md)
