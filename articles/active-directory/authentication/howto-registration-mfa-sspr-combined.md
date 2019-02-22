---
title: Introdução ao registo combinado para SSPR do Azure AD e MFA (pré-visualização)
description: Ativar combinados AD multi-factor Authentication do Azure e o registo (pré-visualização) de reposição de palavra-passe self-service
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ead303257b0b5a4b56803abe57a0101b8f031c0
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56589116"
---
# <a name="enable-combined-security-information-registration-preview"></a>Registo de informações de segurança para ativar combinado (pré-visualização)

Antes de ativar a nova experiência, consulte o artigo [combinado de registo de informações de segurança (pré-visualização)](concept-registration-mfa-sspr-combined.md) para garantir que compreende a funcionalidade e o impacto desse recurso.

![Registo de informações de segurança combinado avançada experiência pedir mais informações em início de sessão. Exemplo mostra a registar a aplicação Microsoft Authenticator como o primeiro método.](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Registo de informações de segurança combinado para a reposição de palavra-passe self-service do Azure multi-factor Authentication e o Azure AD é uma funcionalidade de pré-visualização pública do Azure Active Directory. Para obter mais informações sobre pré-visualizações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="enable-combined-registration"></a>Permitir o registo combinado

Conclua os seguintes passos para ativar o registo combinado:

1. Inicie sessão no portal do Azure como um administrador de utilizador ou administrador global.
2. Navegue até **do Azure Active Directory** > **definições de utilizador** > **gerir as definições para as funcionalidades de pré-visualização do painel de acesso**.
3. Sob **os utilizadores podem utilizar funcionalidades para registar e gerir informações de segurança de pré-visualização - atualizar**, optar por ativar para um **selecionados** grupo de utilizadores ou para **todos os** utilizadores.

![Ativar a experiência de pré-visualização de informações de segurança combinado para todos os utilizadores no portal do Azure AD](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!NOTE]
> Uma vez ativar registo combinado, os utilizadores que registar ou confirmar o que seu número de telefone ou a aplicação móvel através da nova experiência pode utilizá-los para a MFA e o SSPR, se esses métodos estão ativados nas políticas do MFA e o SSPR. Se, em seguida, desativa essa experiência, os utilizadores que vão para o registo SSPR anterior página em [https:/aka.ms/ssprsetup](https:/aka.ms/ssprsetup) terão de efetuar a autenticação multifator para poderem aceder a página.

## <a name="next-steps"></a>Passos Seguintes

[Métodos disponíveis para a MFA e o SSPR](concept-authentication-methods.md)

[Configurar a reposição de palavra-passe self-service](howto-sspr-deployment.md)

[Configurar a autenticação Multifator do Azure](howto-mfa-getstarted.md)

[Resolução de problemas de combinar o registo de informações de segurança](howto-registration-mfa-sspr-combined-troubleshoot.md)