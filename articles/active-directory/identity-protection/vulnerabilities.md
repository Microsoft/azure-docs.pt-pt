---
title: Vulnerabilidades detetadas pelo Azure Active Directory Identity Protection
description: Descrição geral das vulnerabilidades detetadas pelo Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 04/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e792551f4cac857f56454c67d527e01cb9c4281
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113128"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Vulnerabilidades detetadas pelo Azure Active Directory Identity Protection

Vulnerabilidades são pontos fracos num ambiente que pode ser explorada por um atacante. Recomendamos que os administradores resolver estas vulnerabilidades para melhorar a postura de segurança da sua organização.

![Vulnerabilidades comunicadas pelo Identity Protection](./media/vulnerabilities/identity-protection-vulnerabilities.png)

As secções seguintes fornecem uma visão geral das vulnerabilidades comunicado pelo Identity Protection.

## <a name="multi-factor-authentication-registration-not-configured"></a>Registo de autenticação Multifator não configurado

Essa vulnerabilidade ajuda a avaliar a implantação do multi-factor Authentication do Azure na sua organização.

O Azure multi-factor Authentication fornece uma segunda camada de segurança para autenticação de utilizador. Ele ajuda a salvaguardar o acesso aos dados e aplicações, satisfazendo um processo de início de sessão simples. O Azure multi-factor Authentication fornece opções de verificação fácil de usar, como:

* Chamada telefónica
* Mensagem SMS
* Notificação de aplicação móvel
* Código de verificação de OTP

Recomendamos que exigem o multi-factor Authentication para inícios de sessão do utilizador. Autenticação multifator desempenha um papel fundamental nas políticas de acesso condicional baseado no risco disponíveis através da proteção de identidade.

Para obter mais informações, veja [What is Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md) (O que é o Multi-Factor Authentication do Azure?)

## <a name="unmanaged-cloud-apps"></a>Aplicações cloud não geridas

Essa vulnerabilidade ajuda a identificar as aplicações de cloud não geridas na sua organização.

A equipe de TI, muitas vezes, não está cientes de todos os aplicativos de nuvem na sua organização. É fácil ver por que os administradores teriam preocupações sobre acesso não autorizado a dados da empresa, a fuga de dados e outros riscos de segurança.

Recomendamos a implementação da Cloud Discovery para detetar aplicações na cloud não geridas e para gerir estas aplicações com o Azure Active Directory.

Para obter mais informações, consulte [da Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>Alertas de Segurança do Privileged Identity Management

Essa vulnerabilidade ajuda-o a detetar e resolver alertas sobre identidades privilegiadas da sua organização.  

Para permitir que os utilizadores realizar operações privilegiadas, as organizações precisam conceder o acesso privilegiado utilizadores temporária ou permanente no Azure AD, os recursos do Azure ou do Office 365 ou outras aplicações SaaS. Cada um destes utilizadores com privilégios aumenta a superfície de ataque da sua organização. Essa vulnerabilidade ajuda-o a identificar os utilizadores com acesso privilegiado desnecessário e tomar as medidas adequadas para reduzir ou eliminar o risco de que eles representam.

Recomendamos a utilização de organizações do Azure AD Privileged Identity Management, para gerir, controlar e monitorizar identidades privilegiadas no Azure AD, bem como outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

Para obter mais informações, consulte [do Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md).

## <a name="see-also"></a>Consulte também

[O Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
