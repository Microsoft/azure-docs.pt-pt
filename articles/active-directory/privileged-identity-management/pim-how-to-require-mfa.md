---
title: MFA ou 2FA e Gestão de Identidade Privilegiada - Azure AD Microsoft Docs
description: Saiba como a Azure AD Privileged Identity Management (PIM) valida a autenticação de vários fatores (MFA).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6df593909c3ae5962e413eb84e64196fade0326b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022145"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Autenticação multifactor e Gestão de Identidade Privilegiada

Recomendamos que necessite de autenticação multifactor (MFA) para todos os seus administradores. Isto reduz o risco de um ataque devido a uma senha comprometida.

Pode exigir que os utilizadores completem um desafio de autenticação de vários fatores quando iniciarem a sua inscrição. Também pode exigir que os utilizadores completem um desafio de autenticação multifactor quando ativam uma função no Azure Ative Directory (Azure AD) Privileged Identity Management (PIM). Desta forma, se o utilizador não tiver concluído um desafio de autenticação multifactor quando assinou a sua assinatura, será solicitado a fazê-lo pela Privileged Identity Management.

> [!IMPORTANT]
> Neste momento, a Autenticação Multi-Factor Azure só funciona com contas de trabalho ou de escola, e não com contas pessoais da Microsoft (normalmente uma conta pessoal que é usada para iniciar sessão em serviços da Microsoft como Skype, Xbox ou Outlook.com). Por causa disso, qualquer pessoa que use uma conta pessoal não pode ser um administrador elegível porque não pode usar a autenticação de vários fatores para ativar as suas funções. Se estes utilizadores precisarem de continuar a gerir as cargas de trabalho utilizando uma conta Microsoft, eleve-as a administradores permanentes por enquanto.

## <a name="how-pim-validates-mfa"></a>Como a PIM valida o MFA

Existem duas opções para validar a autenticação de vários fatores quando um utilizador ativa uma função.

A opção mais simples é confiar na Autenticação Multi-Factor Azure para os utilizadores que estão a ativar um papel privilegiado. Para tal, verifique primeiro se esses utilizadores estão licenciados, se necessário, e registaram-se para autenticação multi-factor Azure. Para obter mais informações sobre como implementar a autenticação azure multi-factor, consulte [A autenticação azure multi-factor baseada em nuvem.](../authentication/howto-mfa-getstarted.md) Recomenda-se, mas não é necessário, que configure o Azure AD para impor a autenticação de vários fatores para estes utilizadores quando iniciar a sua inscrição. Isto porque os controlos de autenticação de vários fatores serão feitos pela própria Gestão de Identidade Privilegiada.

Em alternativa, se os utilizadores autenticarem no local, pode fazer com que o seu fornecedor de identidade seja responsável pela autenticação de vários fatores. Por exemplo, se configurar os Serviços da Federação AD para exigir a autenticação baseada em smartcard antes de aceder ao Azure AD, a segurança dos recursos na nuvem com a [Autenticação Multi-Factor Azure e a AD FS](../authentication/howto-mfa-adfs.md) inclui instruções para configurar a AD FS para enviar reclamações para a AD Azure. Quando um utilizador tenta ativar uma função, a Privileged Identity Management aceitará que a autenticação multifactor já tenha sido validada para o utilizador assim que receber as reclamações apropriadas.

## <a name="next-steps"></a>Passos seguintes

- [Configure definições de funções de AD Azure na Gestão de Identidade Privilegiada](pim-how-to-change-default-settings.md)
- [Configure definições de funções de recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
