---
title: MFA ou 2FA e Gestão de Identidade Privilegiada - Azure AD | Microsoft Docs
description: Saiba como a Azure AD Privileged Identity Management (PIM) valida a autenticação de vários fatores (MFA).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4344e769cc8466287dab1e98e95cc3fbe705ffbd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94835004"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Autenticação multi-factor e Gestão de Identidade Privilegiada

Recomendamos que necessite de autenticação multi-factor (MFA) para todos os seus administradores. Isto reduz o risco de um ataque devido a uma senha comprometida.

Pode exigir que os utilizadores completem um desafio de autenticação de vários fatores quando iniciarem sação. Também pode exigir que os utilizadores completem um desafio de autenticação multi-factor quando ativam uma função no Azure Ative Directory (Azure AD) Gestão de Identidade Privilegiada (PIM). Desta forma, se o utilizador não completar um desafio de autenticação multi-factor quando se inscreveu, será solicitado pela Gestão de Identidade Privilegiada.

> [!IMPORTANT]
> Neste momento, a autenticação multi-factor Ad Ad apenas funciona com contas de trabalho ou escola, e não contas pessoais da Microsoft (normalmente uma conta pessoal que é usada para iniciar sedutação em serviços da Microsoft como Skype, Xbox ou Outlook.com). Por isso, qualquer pessoa que use uma conta pessoal não pode ser um administrador elegível porque não pode usar a autenticação de vários fatores para ativar as suas funções. Se estes utilizadores precisarem de continuar a gerir cargas de trabalho utilizando uma conta Microsoft, eleve-os a administradores permanentes por enquanto.

## <a name="how-pim-validates-mfa"></a>Como a PIM valida o MFA

Existem duas opções para validar a autenticação de vários fatores quando um utilizador ativa uma função.

A opção mais simples é confiar na Autenticação Multi-Factor AD Azure para os utilizadores que estejam a ativar uma função privilegiada. Para tal, verifique primeiro se esses utilizadores estão licenciados, se necessário, e que se registaram para autenticação multi-factor Azure AD. Para obter mais informações sobre como implementar a autenticação multi-factor Azure AD, consulte [implementar a autenticação multi-factor Azure AD baseada na nuvem](../authentication/howto-mfa-getstarted.md). Recomenda-se, mas não é necessário, que configuure a AD Azure para impor a autenticação de vários fatores para estes utilizadores quando estes se inscrevem. Isto porque os controlos de autenticação de vários fatores serão feitos pela própria Gestão de Identidade Privilegiada.

Em alternativa, se os utilizadores autenticarem no local, pode ter o seu fornecedor de identidade responsável pela autenticação de vários fatores. Por exemplo, se tiver configurado os Serviços da Federação AD para exigir a autenticação baseada em smartcard antes de aceder ao Azure AD, [a garantia de recursos em nuvem com autenticação multi-factor AD Azure e AD FS](../authentication/howto-mfa-adfs.md) inclui instruções para configurar o AD FS para enviar reclamações para a Azure AD. Quando um utilizador tenta ativar uma função, a Gestão de Identidade Privilegiada aceitará que a autenticação multi-factor já tenha sido validada para o utilizador assim que receber as alegações apropriadas.

## <a name="next-steps"></a>Passos seguintes

- [Configurar definições de papel de AD Azure na Gestão de Identidade Privilegiada](pim-how-to-change-default-settings.md)
- [Configurar configurações de funções de recursos Azure na Gestão de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
