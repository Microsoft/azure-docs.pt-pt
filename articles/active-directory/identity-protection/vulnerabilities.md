---
title: Vulnerabilidades detetadas pelo Azure Active Directory Identity Protection | Documentos da Microsoft
description: Descrição geral das vulnerabilidades detetadas pelo Azure Active Directory Identity Protection.
services: active-directory
keywords: proteção de identidade do Azure Active Directory, da cloud discovery, gerenciamento de aplicativos, a segurança, a risco, a nível de risco, a vulnerabilidade, a política de segurança
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 92233a5b-cb34-4d28-88cc-d5d29c0f3256
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fec9693641ff5918f622ecceee3fb94828b508e
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517900"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Vulnerabilidades detetadas pelo Azure Active Directory Identity Protection
Vulnerabilidades são pontos fracos do seu ambiente que pode ser explorada por um atacante. Recomendamos que resolver estas vulnerabilidades para melhorar a postura de segurança da sua organização e impede que os atacantes explorando-los.


![vulnerabilidades](./media/vulnerabilities/101.png "vulnerabilidades")



As secções seguintes fornecem uma visão geral das vulnerabilidades comunicado pelo Identity Protection.

## <a name="multi-factor-authentication-registration-not-configured"></a>Registo de autenticação multifator não configurado
Essa vulnerabilidade ajuda-o a controlar a implantação do multi-factor Authentication do Azure na sua organização. 

Multi-factor authentication fornece uma segunda camada de segurança para autenticação de utilizador. Ele ajuda a salvaguardar o acesso aos dados e aplicações, satisfazendo um processo de início de sessão simples. Proporciona uma autenticação segura através de um conjunto de opções de verificação simples — telefonema, mensagem de texto ou aplicação móvel de notificação ou verificação de código e de terceiros tokens OATH.

Recomendamos que exigem o multi-factor Authentication para inícios de sessão do utilizador. Autenticação multifator desempenha um papel fundamental nas políticas de acesso condicional baseado no risco disponíveis através da proteção de identidade.

Para obter mais informações, veja [What is Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md) (O que é o Multi-Factor Authentication do Azure?)

## <a name="unmanaged-cloud-apps"></a>Aplicações cloud não geridas
Essa vulnerabilidade ajuda a identificar as aplicações de cloud não geridas na sua organização.

Nas empresas modernas, os departamentos de TI, muitas vezes, estão cientes de todos os aplicativos de nuvem que os utilizadores na sua organização estiver a utilizar para trabalhar. É fácil ver por que os administradores teriam preocupações sobre acesso não autorizado a dados da empresa, a fuga de dados e outros riscos de segurança. 

É recomendável para implementar a Cloud Discovery para detetar aplicações na cloud não geridas e para gerir estas aplicações com o Azure Active Directory.

Para obter mais informações, consulte [da Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>Alertas de Segurança do Privileged Identity Management
Essa vulnerabilidade ajuda-o a detetar e resolver alertas sobre identidades privilegiadas da sua organização.  

Para permitir que os utilizadores realizar operações privilegiadas, as organizações precisam conceder o acesso privilegiado utilizadores temporária ou permanente no Azure AD, os recursos do Azure ou do Office 365 ou outras aplicações SaaS. Cada um destes utilizadores com privilégios aumenta a superfície de ataque da sua organização. Essa vulnerabilidade ajuda-o a identificar os utilizadores com acesso privilegiado desnecessário e tomar as medidas adequadas para reduzir ou eliminar o risco de que eles representam. 

Recomendamos que a sua organização utiliza o Azure AD Privileged Identity Management para gerir, controlar e monitorizar privilegiado identidades e o respetivo acesso a recursos no Azure AD, bem como outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

Para obter mais informações, consulte [do Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md). 

## <a name="see-also"></a>Consulte também

[O Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

