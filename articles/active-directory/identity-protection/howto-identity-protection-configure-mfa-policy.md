---
title: Configure a política de registo do MFA - Azure Ative Directory Identity Protection
description: Saiba como configurar a política de registo de autenticação de vários fatores de proteção de identidade da AD Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382132"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>Como: Configurar a política de registo de autenticação multi-factor Azure

A Azure AD Identity Protection ajuda-o a gerir o lançamento do registo de Autenticação Multifactor (MFA) do Azure, configurando uma política de Acesso Condicional para exigir o registo de MFA independentemente da aplicação de autenticação moderna em que está a assinar.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>O que é a política de registo de autenticação multifactor Azure?

A Autenticação De Vários Fatores Azure fornece um meio para verificar quem está a usar mais do que apenas um nome de utilizador e uma senha. Fornece uma segunda camada de segurança aos insines dos utilizadores. Para que os utilizadores possam responder às solicitações do MFA, devem primeiro registar-se para autenticação multi-factor Azure.

Recomendamos que necessite de autenticação multi-factor Azure para os sessão de inscrição do utilizador porque:

- Oferece autenticação forte através de uma gama de opções de verificação.
- Desempenha um papel fundamental na preparação da sua organização para se auto-remediar das deteções de risco na Proteção de Identidade.

Para obter mais informações sobre a autenticação de multi-factors Azure, consulte o que é a [autenticação multi-factor Azure?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Configuração da política

1. Navegue para o [portal Azure.](https://portal.azure.com)
1. Navegue na política de registo de registo de**MFA**de**Proteção** > de Identidade de**Segurança** > do >  **Diretório Ativo do Azure.**
   1. Em **Atribuições**
      1. **Utilizadores** - Escolha **todos os utilizadores** ou **Selecione indivíduos e grupos** se limitar o seu lançamento.
         1. Opcionalmente pode optar por excluir os utilizadores da apólice.
   1. Sob **controlos**
      1. Certifique-se de que a caixa de verificação Requer que o **registo Azure MFA** seja verificado e escolha **Select**.
   1. **Impor a política** - **On**
   1. **Guardar**

## <a name="user-experience"></a>Experiência de utilizador

A Proteção de Identidade do Diretório Ativo Azure irá levar os seus utilizadores a registarem-se da próxima vez que assinarem o seu registo interactivae e terão 14 dias para completar o registo. Durante este período de 14 dias, podem contornar o registo, mas no final do período serão obrigados a registar-se antes de poderem concluir o processo de inscrição.

Para uma visão geral da experiência do utilizador relacionada, consulte:

- [Experiências de inscrição com a Azure AD Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Passos seguintes

- [Ativar políticas de risco de inscrição e utilizador](howto-identity-protection-configure-risk-policies.md)

- [Ativar o reset da palavra-passe autosserviço Azure AD](../authentication/howto-sspr-deployment.md)

- [Ativar a Multi-Factor Authentication do Azure](../authentication/howto-mfa-getstarted.md)
