---
title: 'Azure AD Connect: Sem emenda um único sinal / Microsoft Docs'
description: Este tópico descreve o Azure Ative Directory (Azure AD) Seamless Single Sign-On e como permite fornecer um verdadeiro único sign-on para utilizadores de desktop corporativos dentro da sua rede corporativa.
services: active-directory
keywords: o que é Azure AD Connect, instalar Diretório Ativo, componentes necessários para Azure AD, SSO, Sign-on Único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1b7e4716e731e6b73e3ac60b64baa71043906fc
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77483759"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Ative Directory Seamless Single Sign-On

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>O que é Azure Ative Directory Seamless Single Sign-On?

O Início de Sessão Único Totalmente Integrado do Azure Active Directory (SSO Totalmente Integrado do Azure AD) inicia sessão automaticamente pelos utilizadores quando estão a utilizar os dispositivos da empresa ligados à sua rede empresarial. Quando ativados, os utilizadores não precisam de escrever as suas palavras-passe para iniciar sessão no Azure AD e, normalmente, até escrever nos seus nomes de utilizador. Esta funcionalidade dá aos utilizadores acesso fácil às aplicações baseadas na cloud sem serem precisos componentes no local adicionais.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

O SSO sem emenda pode ser combinado com a Sincronização de [Hash password](how-to-connect-password-hash-synchronization.md) ou com os métodos de inscrição de [autenticação pass-through.](how-to-connect-pta.md) SSO sem emenda _não_ é aplicável aos Serviços da Federação de Diretórios Ativos (ADFS).

![Insígnia única sem emenda](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>O SSO sem emenda necessita que o dispositivo do utilizador seja **apenas unido** ao domínio, mas não é utilizado em dispositivos [adessimos](../devices/concept-azure-ad-join-hybrid.md) [azure](../devices/concept-azure-ad-join.md) ad ou hybrid Azure. SSO on Azure AD juntou-se e Hybrid Azure AD juntou-se a obras com base no [token de atualização primária.](../devices/concept-primary-refresh-token.md)

## <a name="key-benefits"></a>Principais vantagens

- *Experiência de utilizador excecional*
  - Os utilizadores são automaticamente assinados tanto no local como nas aplicações baseadas na nuvem.
  - Os utilizadores não têm de introduzir repetidamente as suas palavras-passe.
- *Fácil de implementar e administrar*
  - Não são necessários componentes adicionais no local para que isto funcione.
  - Funciona com qualquer método de autenticação em nuvem - [Sincronização de Hash password](how-to-connect-password-hash-synchronization.md) ou [autenticação pass-through](how-to-connect-pta.md).
  - Pode ser lançado para alguns ou todos os seus utilizadores usando a Política de Grupo.
  - Registe dispositivos não Windows 10 com AD Azure sem necessidade de qualquer infraestrutura AD FS. Esta capacidade necessita de utilizar a versão 2.1 ou posterior do [cliente de adesão ao local de trabalho.](https://www.microsoft.com/download/details.aspx?id=53554)

## <a name="feature-highlights"></a>Destaques de recurso

- O nome de utilizador de entrada de entrada pode ser o nome de utilizador padrão no local (`userPrincipalName`) ou outro atributo configurado no Azure AD Connect (`Alternate ID`). Ambos os casos funcionam porque o SSO sem emenda utiliza a `securityIdentifier` reivindicação no bilhete Kerberos para procurar o objeto de utilizador correspondente em Azure AD.
- SSO sem emenda é uma característica oportunista. Se falhar por qualquer motivo, a experiência de entrada no utilizador remonta ao seu comportamento regular - ou seja, o utilizador precisa de introduzir a sua palavra-passe na página de entrada.
- Se uma aplicação (por exemplo, `https://myapps.microsoft.com/contoso.com`) reencaminhar um parâmetro `domain_hint` (OpenID Connect) ou `whr` (SAML) - identificando o seu inquilino, ou `login_hint` parâmetro - identificando o utilizador, no seu pedido de entrada em Anúncios Azure, os utilizadores são automaticamente inscritos sem que eles entrem no nome de utilizador ou palavras-passe.
- Os utilizadores também obtêm uma experiência de inscrição silenciosa se uma aplicação (por exemplo, `https://contoso.sharepoint.com`) enviar pedidos de inscrição para os pontos finais da Azure AD criados como inquilinos - isto é, `https://login.microsoftonline.com/contoso.com/<..>` ou `https://login.microsoftonline.com/<tenant_ID>/<..>` - em vez do ponto final comum da Azure AD - isto é, `https://login.microsoftonline.com/common/<...>`.
- A assinatura é apoiada. Isto permite que os utilizadores escolham outra conta Azure AD para iniciar sessão, em vez de serem automaticamente assinados na utilização de SSO SSO sem emenda automaticamente.
- Os clientes do Office 365 Win32 (Outlook, Word, Excel, entre outros) com versões 16.0.8730.xxxx e acima são suportados utilizando um fluxo não interativo. Para o OneDrive, terá de ativar a [função de config silencioso OneDrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) para uma experiência de inscrição silenciosa.
- Pode ser ativado via Azure AD Connect.
- É uma funcionalidade gratuita, e não é preciso edições pagas de Azure AD para usá-lo.
- É suportado em clientes baseados em navegador web e clientes do Office que suportam [a autenticação moderna](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) em plataformas e navegadores capazes de autenticação Kerberos:

| OS\Browser |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Sim,\*|Sim|Sim|Sim\*\* \*|N/A
|Windows 8.1|Sim,\*|N/A|Sim|Sim\*\* \*|N/A
|Windows 8|Sim,\*|N/A|Sim|Sim\*\* \*|N/A
|Windows 7|Sim,\*|N/A|Sim|Sim\*\* \*|N/A
|Windows Server 2012 R2 ou superior|Sim\*\*|N/A|Sim|Sim\*\* \*|N/A
|Mac OS X|N/A|N/A|Sim\*\* \*|Sim\*\* \*|Sim\*\* \*


\*requer versões do Internet Explorer 10 ou superior

\*\*requer versões do Internet Explorer 10 ou superior. Desativar o modo protegido melhorado

\*\*\*requer [configuração adicional](how-to-connect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>Para o Windows 10, a recomendação é utilizar o [Azure AD Join](../devices/concept-azure-ad-join.md) para a melhor experiência de inscrição individual com o Azure AD.

## <a name="next-steps"></a>Passos Seguintes

- [**Quick Start**](how-to-connect-sso-quick-start.md) - Prepare-se e execute Azure AD Seamless SSO.
- Plano de [**Implantação**](https://aka.ms/deploymentplans/sso) - Plano de implantação passo a passo.
- [**Mergulho Técnico Deep**](how-to-connect-sso-how-it-works.md) - Entenda como esta funcionalidade funciona.
- [**Perguntas frequentes**](how-to-connect-sso-faq.md) - Respostas a perguntas frequentes.
- [**Troubleshoot**](tshoot-connect-sso.md) - Aprenda a resolver problemas comuns com a funcionalidade.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Para arquivar novos pedidos de funcionalidades.

