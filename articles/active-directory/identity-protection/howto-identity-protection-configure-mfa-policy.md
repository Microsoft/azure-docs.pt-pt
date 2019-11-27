---
title: Configurar a política de registro de MFA-Azure Active Directory Identity Protection
description: Saiba como configurar a política de registro de autenticação multifator do Azure AD Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fa6a4cf184b426355f62117ea51642127eee529
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382132"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Como configurar a política de registro da autenticação multifator do Azure

Azure AD Identity Protection ajuda a gerenciar a distribuição do registro de autenticação multifator do Azure (MFA) configurando uma política de acesso condicional para exigir o registro de MFA, independentemente do aplicativo de autenticação moderno no qual você está entrando.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>O que é a política de registro da autenticação multifator do Azure?

A autenticação multifator do Azure fornece um meio de verificar quem você está usando mais do que apenas um nome de usuário e senha. Ele fornece uma segunda camada de segurança para entradas de usuário. Para que os usuários possam responder aos prompts do MFA, eles devem primeiro se registrar para a autenticação multifator do Azure.

Recomendamos que você exija a autenticação multifator do Azure para entradas de usuário porque:

- Fornece autenticação forte por meio de uma variedade de opções de verificação.
- Desempenha um papel fundamental na preparação de sua organização para a remediação automática de detecções de risco no Identity Protection.

Para obter mais informações sobre a autenticação multifator do Azure, consulte [o que é a autenticação multifator do Azure?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Configuração da política

1. Navegue para o [portal do Azure](https://portal.azure.com).
1. Navegue até **Azure Active Directory** > **Security** > **identidade Protection** > **política de registro do MFA**.
   1. Em **atribuições**
      1. **Usuários** – escolha **todos os usuários** ou **selecione indivíduos e grupos** se limitar a distribuição.
         1. Opcionalmente, você pode optar por excluir usuários da política.
   1. Em **controles**
      1. Certifique-se de que a caixa de seleção **exigir registro de MFA do Azure** esteja marcada e escolha **selecionar**.
   1. **Impor** - **de política em**
   1. **Guarde**

## <a name="user-experience"></a>Experiência do usuário

Azure Active Directory Identity Protection solicitará que os usuários se registrem na próxima vez que entrarem de forma interativa e terão 14 dias para concluir o registro. Durante esse período de 14 dias, eles podem ignorar o registro, mas no final do período eles deverão ser registrados para que possam concluir o processo de entrada.

Para obter uma visão geral da experiência do usuário relacionada, consulte:

- [Experiências de entrada com Azure ad Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Passos Seguintes

- [Habilitar as políticas de entrada e de risco do usuário](howto-identity-protection-configure-risk-policies.md)

- [Habilitar redefinição de senha de autoatendimento do Azure AD](../authentication/howto-sspr-deployment.md)

- [Habilitar a autenticação multifator do Azure](../authentication/howto-mfa-getstarted.md)
