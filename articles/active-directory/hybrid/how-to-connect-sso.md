---
title: 'Azure AD Connect: Logon único contínuo | Microsoft Docs'
description: Este tópico descreve o logon único contínuo do Azure Active Directory (Azure AD) e como ele permite que você forneça o verdadeiro logon único para usuários da área de trabalho corporativa dentro de sua rede corporativa.
services: active-directory
keywords: o que é Azure AD Connect, instalar Active Directory, componentes necessários para o Azure AD, SSO, logon único
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
ms.openlocfilehash: 7791e7b50a963d2f92a2cbc460e36f9e83bb1b52
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025696"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory logon único contínuo

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>O que é Azure Active Directory logon único contínuo?

O Início de Sessão Único Totalmente Integrado do Azure Active Directory (SSO Totalmente Integrado do Azure AD) inicia sessão automaticamente pelos utilizadores quando estão a utilizar os dispositivos da empresa ligados à sua rede empresarial. Quando habilitado, os usuários não precisam digitar suas senhas para entrar no Azure AD e, em geral, digitar seus nomes de usuário. Esta funcionalidade dá aos utilizadores acesso fácil às aplicações baseadas na cloud sem serem precisos componentes no local adicionais.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

O SSO contínuo pode ser combinado com os métodos de entrada de [sincronização de hash de senha](how-to-connect-password-hash-synchronization.md) ou de autenticação de [passagem](how-to-connect-pta.md) . O SSO contínuo _não_ é aplicável a serviços de Federação do Active Directory (AD FS) (ADFS).

![Logon único contínuo](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>O SSO contínuo precisa que o dispositivo do usuário seja **ingressado no domínio**, mas não precisa que o dispositivo seja [ingressado no Azure ad](../active-directory-azureadjoin-overview.md).

## <a name="key-benefits"></a>Principais vantagens

- *Experiência de utilizador excecional*
  - Os usuários são automaticamente conectados a aplicativos locais e baseados em nuvem.
  - Os usuários não precisam inserir suas senhas repetidamente.
- *Fácil de implantar & administrar*
  - Nenhum componente adicional é necessário no local para fazer esse trabalho.
  - Funciona com qualquer método de autenticação de nuvem- [sincronização de hash de senha](how-to-connect-password-hash-synchronization.md) ou [autenticação de passagem](how-to-connect-pta.md).
  - Pode ser distribuído para alguns ou todos os seus usuários usando Política de Grupo.
  - Registre dispositivos que não sejam do Windows 10 com o Azure AD sem a necessidade de qualquer infraestrutura de AD FS. Esse recurso precisa que você use a versão 2,1 ou posterior do [cliente de ingresso no local de trabalho](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Destaques de recursos

- O nome de usuário de entrada pode ser o nome de usuário padrão local (`userPrincipalName`) ou outro atributo configurado em Azure AD Connect (`Alternate ID`). Ambos os casos de uso funcionam porque o SSO contínuo usa a declaração `securityIdentifier` no tíquete Kerberos para pesquisar o objeto de usuário correspondente no Azure AD.
- O SSO contínuo é um recurso oportunista. Se ele falhar por algum motivo, a experiência de entrada do usuário voltará ao seu comportamento regular, ou seja, o usuário precisará inserir sua senha na página de entrada.
- Se um aplicativo (por exemplo, `https://myapps.microsoft.com/contoso.com`) encaminha um parâmetro `domain_hint` (OpenID Connect) ou `whr` (SAML)-identificando seu locatário ou @no__t parâmetro-3-identificando o usuário, em sua solicitação de entrada do Azure AD, os usuários são conectados automaticamente sem eles inserindo nomes de acessadores ou senhas.
- Os usuários também terão uma experiência de logon silenciosa se um aplicativo (por exemplo, `https://contoso.sharepoint.com`) enviar solicitações de entrada para pontos de extremidade do Azure AD configurados como locatários, ou seja, `https://login.microsoftonline.com/contoso.com/<..>` ou `https://login.microsoftonline.com/<tenant_ID>/<..>`-em vez do ponto de extremidade comum do Azure AD – ou seja, `https://login.microsoftonline.com/common/<...>`.
- Há suporte para sair. Isso permite que os usuários escolham outra conta do Azure AD para entrar, em vez de serem conectados automaticamente usando o SSO contínuo automaticamente.
- Os clientes do Office 365 Win32 (Outlook, Word, Excel e outros) com versões 16.0.8730. xxxx e superior têm suporte usando um fluxo não interativo. Para o OneDrive, você precisará ativar o [recurso de configuração silenciosa do onedrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) para uma experiência de logon silencioso.
- Ele pode ser habilitado por meio de Azure AD Connect.
- É um recurso gratuito, e você não precisa de nenhuma edição paga do Azure AD para usá-lo.
- Há suporte para clientes baseados em navegador da Web e clientes do Office que dão suporte à [autenticação moderna](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) em plataformas e navegadores com capacidade de autenticação Kerberos:

| OS\Browser |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Sim @ no__t-0|Sim|Sim|Sim @ no__t-0 @ no__t-1 @ no__t-2|N/A
|Windows 8.1|Sim @ no__t-0|N/A|Sim|Sim @ no__t-0 @ no__t-1 @ no__t-2|N/A
|Windows 8|Sim @ no__t-0|N/A|Sim|Sim @ no__t-0 @ no__t-1 @ no__t-2|N/A
|Windows 7|Sim @ no__t-0|N/A|Sim|Sim @ no__t-0 @ no__t-1 @ no__t-2|N/A
|Windows Server 2012 R2 ou superior|Sim @ no__t-0 @ no__t-1|N/A|Sim|Sim @ no__t-0 @ no__t-1 @ no__t-2|N/A
|Mac OS X|N/A|N/A|Sim @ no__t-0 @ no__t-1 @ no__t-2|Sim @ no__t-0 @ no__t-1 @ no__t-2|Sim @ no__t-0 @ no__t-1 @ no__t-2


\*Requires o Internet Explorer versões 10 ou posteriores

\* @ no__t-1Requires Internet Explorer versões 10 ou superior. Desabilitar o modo protegido avançado

\* @ no__t-1 @ no__t-2Requires [configuração adicional](how-to-connect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>Para o Windows 10, a recomendação é usar o [ingresso no Azure ad](../active-directory-azureadjoin-overview.md) para obter a experiência ideal de logon único com o Azure AD.

## <a name="next-steps"></a>Passos seguintes

- [**Início rápido**](how-to-connect-sso-quick-start.md) -obtenha e execute o SSO contínuo do Azure AD.
- [**Plano de implantação**](https://aka.ms/deploymentplans/sso) -plano de implantação passo a passo.
- [**Aprofundamento técnico**](how-to-connect-sso-how-it-works.md) – entenda como esse recurso funciona.
- [**Perguntas**](how-to-connect-sso-faq.md) frequentes-respostas para perguntas frequentes.
- [**Solução de problemas**](tshoot-connect-sso.md) -saiba como resolver problemas comuns com o recurso.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) -para o arquivamento de novas solicitações de recursos.

