---
title: Autenticação multifator (MFA) e PIM-Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: 686c39c9fb1f1ff7c0ecf068a6612f530620d5dc
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804310"
---
# <a name="multi-factor-authentication-mfa-and-pim"></a>Autenticação multifator (MFA) e PIM

Recomendamos que você exija a autenticação multifator (MFA) para todos os seus administradores. Isso reduz o risco de um ataque devido a uma senha comprometida.

Você pode exigir que os usuários concluam um desafio de MFA ao entrarem. Você também pode exigir que os usuários concluam um desafio de MFA quando ativam uma função no Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Dessa forma, se o usuário não concluiu um desafio de MFA quando se conectasse, ele será solicitado a fazê-lo pelo PIM.

> [!IMPORTANT]
> No momento, o Azure MFA funciona apenas com contas corporativas ou de estudante, não com contas da Microsoft (geralmente uma conta pessoal que é usada para entrar nos serviços da Microsoft, como Skype, Xbox, Outlook.com, etc.). Por isso, qualquer pessoa que usa uma conta Microsoft não pode ser um administrador qualificado porque não pode usar a MFA para ativar suas funções. Se esses usuários precisarem continuar Gerenciando cargas de trabalho usando um conta Microsoft, eleve-os aos administradores permanentes por enquanto.

## <a name="how-pim-validates-mfa"></a>Como o PIM valida a MFA

Há duas opções para validar o MFA quando um usuário ativa uma função.

A opção mais simples é contar com o Azure MFA para usuários que estão ativando uma função privilegiada. Para fazer isso, primeiro verifique se os usuários estão licenciados, se necessário, e se foram registrados para o Azure MFA. Para obter mais informações sobre como implantar o Azure MFA, consulte [implantar a autenticação multifator do Azure baseada em nuvem](../authentication/howto-mfa-getstarted.md). É recomendável, mas não obrigatório, que você configure o Azure AD para impor a MFA para esses usuários quando eles entram. Isso ocorre porque as verificações de MFA serão feitas pelo PIM em si.

Como alternativa, se os usuários autenticarem no local, você poderá fazer com que seu provedor de identidade seja responsável pela MFA. Por exemplo, se você configurou os serviços de Federação do AD para exigir autenticação baseada em cartão inteligente antes de acessar o Azure AD, [proteger os recursos de nuvem com a autenticação multifator do Azure e AD FS](../authentication/howto-mfa-adfs.md) inclui instruções para configurar AD FS para enviar declarações para o Azure AD. Quando um usuário tentar ativar uma função, o PIM aceitará que a MFA já foi validada para o usuário depois de receber as declarações apropriadas.

## <a name="next-steps"></a>Passos seguintes

- [Definir as configurações de função do Azure AD no PIM](pim-how-to-change-default-settings.md)
- [Definir configurações de função de recurso do Azure no PIM](pim-resource-roles-configure-role-settings.md)
