---
title: 'Azure AD Connect: Single Sign-On sem emendas Microsoft Docs'
description: Este tópico descreve o Azure Ative Directory (Azure AD) Seamless Single Sign-On e como permite fornecer um verdadeiro sign-on único para utilizadores de desktop corporativos dentro da sua rede corporativa.
services: active-directory
keywords: o que é Azure AD Connect, instalar Ative Directory, componentes necessários para Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7baa851943db3e8c691b50c2cb5446a2adbbccc
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92457996"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Início de Sessão Único Totalmente Integrado do Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>O que é Azure Ative Directory Seamless Single Sign-On?

O Início de Sessão Único Totalmente Integrado do Azure Active Directory (SSO Totalmente Integrado do Azure AD) inicia sessão automaticamente pelos utilizadores quando estão a utilizar os dispositivos da empresa ligados à sua rede empresarial. Quando ativados, os utilizadores não precisam de digitar as suas palavras-passe para iniciar sação no Azure AD e, normalmente, até escrever nos seus nomes de utilizador. Esta funcionalidade dá aos utilizadores acesso fácil às aplicações baseadas na cloud sem serem precisos componentes no local adicionais.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

O SSO sem emenda pode ser combinado com os métodos de [sincronização de hash de palavra-passe](how-to-connect-password-hash-synchronization.md) ou com os métodos [de autenticação pass-through.](how-to-connect-pta.md) O SSO sem emenda _não_ é aplicável aos Serviços da Federação de Diretório Ativo (ADFS).

![Sign-On single sem emenda](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>O Seamless SSO precisa que o dispositivo do utilizador seja **apenas ligado** ao domínio, mas não é utilizado em dispositivos [aderidos a Azure AD ou](../devices/concept-azure-ad-join.md) [híbridos Azure AD.](../devices/concept-azure-ad-join-hybrid.md) SSO on Azure AD juntou-se, Hybrid Azure AD juntou-se, e Azure AD registrou dispositivos com base no [token de atualização primária](../devices/concept-primary-refresh-token.md).

## <a name="key-benefits"></a>Principais vantagens

- *Experiência de utilizador excecional*
  - Os utilizadores são automaticamente inscritos em aplicações no local e na nuvem.
  - Os utilizadores não têm de introduzir as suas palavras-passe repetidamente.
- *Fácil de implantar & administrar*
  - Não são necessários componentes adicionais no local para que isto funcione.
  - Funciona com qualquer método de autenticação em nuvem - [Sincronização de Hash password](how-to-connect-password-hash-synchronization.md) ou [autenticação pass-through](how-to-connect-pta.md).
  - Pode ser lançado para alguns ou todos os seus utilizadores usando a Política de Grupo.
  - Registar dispositivos não-Windows 10 com Azure AD sem a necessidade de qualquer infraestrutura AD FS. Esta capacidade precisa que você use a versão 2.1 ou posterior do [cliente de aderião](https://www.microsoft.com/download/details.aspx?id=53554)no local de trabalho .

## <a name="feature-highlights"></a>Destaques de recursos

- O nome de utilizador de inscrição pode ser o nome de utilizador predefinido no local `userPrincipalName` () ou outro atributo configurado no Azure AD Connect `Alternate ID` (). Ambos os casos funcionam porque o Seamless SSO utiliza a `securityIdentifier` reclamação no bilhete Kerberos para procurar o objeto de utilizador correspondente em Azure AD.
- Seamless SSO é uma característica oportunista. Se falhar por qualquer motivo, a experiência de início de sômbar volta ao seu comportamento regular - ou seja, o utilizador precisa de introduzir a sua palavra-passe na página de início de s.a..
- Se uma aplicação (por exemplo,  `https://myapps.microsoft.com/contoso.com` ) encaminhar um `domain_hint` parâmetro (OpenID Connect) ou `whr` (SAML) - identificando o seu inquilino, ou `login_hint` parâmetro - identificando o utilizador, no seu pedido de entrada AD Azure, os utilizadores são automaticamente inscritos sem que estes introduzam nomes de utilizador ou palavras-passe.
- Os utilizadores também obtêm uma experiência de inscrição silenciosa se uma aplicação (por exemplo, `https://contoso.sharepoint.com` ) enviar pedidos de inscrição para os pontos finais da AZure AD configurados como inquilinos - isto é, `https://login.microsoftonline.com/contoso.com/<..>` ou - em vez do ponto final comum da `https://login.microsoftonline.com/<tenant_ID>/<..>` AD AD - isto é, `https://login.microsoftonline.com/common/<...>` .
- A assinatura é suportada. Isto permite que os utilizadores escolham outra conta Azure AD para iniciar seduca, em vez de serem automaticamente assinados na utilização de SSO sem emenda.
- Os clientes Microsoft 365 Win32 (Outlook, Word, Excel e outros) com as versões 16.0.8730.xxxx e acima são suportados com um fluxo não interativo. Para o OneDrive, terá de ativar a [funcionalidade de config silencioso OneDrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) para uma experiência de sinal-on silenciosa.
- Pode ser ativado através do Azure AD Connect.
- É uma funcionalidade gratuita e não precisa de edições pagas da Azure AD para usá-lo.
- É suportado em clientes baseados no navegador web e clientes do Office que suportam [a autenticação moderna](/office365/enterprise/modern-auth-for-office-2013-and-2016) em plataformas e navegadores capazes de autenticação Kerberos:

| OS\Browser |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Sim\*|Sim|Sim|Sim\*\*\*|N/D
|Windows 8.1|Sim\*|Sim, eu não.\*\*\*|Sim|Sim\*\*\*|N/D
|Windows 8|Sim\*|N/D|Sim|Sim\*\*\*|N/D
|Windows 7|Sim\*|N/D|Sim|Sim\*\*\*|N/D
|Windows Server 2012 R2 ou superior|Sim, é o seu\*\*|N/D|Sim|Sim\*\*\*|N/D
|Mac OS X|N/D|N/D|Sim\*\*\*|Sim\*\*\*|Sim\*\*\*


\*Requer a versão 10 ou posterior do Internet Explorer.

\*\*Requer a versão 10 ou posterior do Internet Explorer. Desative o modo protegido melhorado.

\*\*\*Requer [uma configuração adicional](how-to-connect-sso-quick-start.md#browser-considerations).

\*\*\*\*Requer a versão 77 ou posterior do Microsoft Edge.

>[!NOTE]
>Para o Windows 10, a recomendação é utilizar o [Azure AD Join](../devices/concept-azure-ad-join.md) para a experiência de súmis ideal com a Azure AD.

## <a name="next-steps"></a>Passos seguintes

- [**Quick Start**](how-to-connect-sso-quick-start.md) - Levante-se e execute Azure AD Seamless SSO.
- Plano de [**Implantação**](https://aka.ms/deploymentplans/sso) - Plano de implantação passo a passo.
- [**Mergulho Profundo Técnico**](how-to-connect-sso-how-it-works.md) - Entenda como funciona esta funcionalidade.
- [**Perguntas frequentes**](how-to-connect-sso-faq.md) - Respostas a perguntas frequentes.
- [**Resolução de problemas**](tshoot-connect-sso.md) - Aprenda a resolver problemas comuns com a funcionalidade.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Para arquivar novos pedidos de funcionalidades.