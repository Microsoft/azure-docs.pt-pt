---
title: Autenticação multifator (MFA) e Azure Active Directory de Privileged Identity Management | Microsoft Docs
description: Saiba como Azure AD Privileged Identity Management (PIM) valida a autenticação multifator (MFA).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/31/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aa58c354198e648227b9b0b43d1f60546f87d32
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063065"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Autenticação multifator e Privileged Identity Management

Recomendamos que você exija a autenticação multifator (MFA) para todos os seus administradores. Isso reduz o risco de um ataque devido a uma senha comprometida.

Você pode exigir que os usuários concluam um desafio de autenticação multifator ao entrarem. Você também pode exigir que os usuários concluam um desafio de autenticação multifator quando ativam uma função no Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Dessa forma, se o usuário não concluiu um desafio de autenticação multifator quando se conectasse, ele será solicitado a fazer isso por Privileged Identity Management.

> [!IMPORTANT]
> Agora, a autenticação multifator do Azure funciona apenas com contas corporativas ou de estudante, não com contas pessoais da Microsoft (geralmente uma conta pessoal que é usada para entrar nos serviços da Microsoft, como Skype, Xbox ou Outlook.com). Por isso, qualquer pessoa que usa uma conta pessoal não pode ser um administrador qualificado porque não pode usar a autenticação multifator para ativar suas funções. Se esses usuários precisarem continuar Gerenciando cargas de trabalho usando um conta Microsoft, eleve-os aos administradores permanentes por enquanto.

## <a name="how-pim-validates-mfa"></a>Como o PIM valida a MFA

Há duas opções para validar a autenticação multifator quando um usuário ativa uma função.

A opção mais simples é contar com a autenticação multifator do Azure para usuários que estão ativando uma função privilegiada. Para fazer isso, primeiro verifique se os usuários estão licenciados, se necessário, e se foram registrados para a autenticação multifator do Azure. Para obter mais informações sobre como implantar a autenticação multifator do Azure, consulte [implantar a autenticação multifator do Azure baseada em nuvem](../authentication/howto-mfa-getstarted.md). É recomendável, mas não obrigatório, que você configure o Azure AD para impor a autenticação multifator para esses usuários quando eles entram. Isso ocorre porque as verificações de autenticação multifator serão feitas Privileged Identity Management si mesma.

Como alternativa, se os usuários autenticarem no local, você poderá fazer com que seu provedor de identidade seja responsável pela autenticação multifator. Por exemplo, se você configurou os serviços de Federação do AD para exigir autenticação baseada em cartão inteligente antes de acessar o Azure AD, [proteger os recursos de nuvem com a autenticação multifator do Azure e AD FS](../authentication/howto-mfa-adfs.md) inclui instruções para configurar AD FS para enviar declarações para o Azure AD. Quando um usuário tenta ativar uma função, Privileged Identity Management aceitará que a autenticação multifator já tenha sido validada para o usuário depois de receber as declarações apropriadas.

## <a name="next-steps"></a>Passos seguintes

- [Definir as configurações de função do Azure AD no Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Definir configurações de função de recurso do Azure no Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
