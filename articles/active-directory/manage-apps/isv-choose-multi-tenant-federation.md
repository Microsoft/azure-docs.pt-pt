---
title: Escolha o protocolo de Federação correto para seu aplicativo multilocatário
description: Diretrizes para fornecedores independentes de software na integração com o Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: ede458e7d4c1cb1a8d7e3f2e2c9df54d5925d6d8
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175981"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Escolha o protocolo de Federação correto para seu aplicativo multilocatário

Ao desenvolver seu aplicativo SaaS (software como serviço), você deve selecionar o protocolo de Federação que melhor atenda às suas necessidades de clientes. Essa decisão se baseia na sua plataforma de desenvolvimento e no seu desejo de se integrar com os dados disponíveis no Microsoft Office 365 e no ecossistema do Azure AD.

Consulte a lista completa de [protocolos disponíveis para integrações de SSO](what-is-single-sign-on.md) com Azure Active Directory.
A tabela a seguir compara 
* Autenticação aberta 2,0 (OAuth 2,0)
* Open ID Connect (OIDC)
* Security Assertion Markup Language (SAML)
* Especificação Web Services Federation (WSFed)

| Capacidade| OAuth/OIDC| SAML/WSFed |
| - |-|-|
| Logon único baseado na Web| √| √ |
| Logout único baseado na Web| √| √ |
| Logon único baseado em móvel| √| √ |
| Logout único baseado em celular| √| √ |
| Políticas de acesso condicional para aplicativos móveis| √| X |
| Experiência de MFA direta para aplicativos móveis| √| X |
| Microsoft Graph de acesso| √| X |

\* Possível, mas a Microsoft não fornece exemplos ou diretrizes.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2,0 e Open ID Connect

O OAuth 2,0 é um protocolo [padrão da indústria](https://oauth.net/2/) para autorização. OIDC (OpenID Connect) é uma camada de autenticação de identidade [padrão do setor](https://openid.net/connect/) criada com base no protocolo Oath 2,0.

### <a name="benefits"></a>Vantagens

A Microsoft recomenda o uso do OIDC/OAuth 2,0, pois eles têm autenticação e autorização interna aos protocolos. Com o SAML, você também deve implementar a autorização.

A autorização inerente a esses protocolos permite que seu aplicativo acesse e integre com dados avançados de usuário e organizacionais por meio da API de Microsoft Graph.

Usar o OAuth 2,0 e o OIDC simplifica a experiência do usuário final de seus clientes ao adotar o SSO para seu aplicativo. Você pode definir facilmente os conjuntos de permissões necessários, que são representados automaticamente para o administrador ou o usuário final que está consentido.

Além disso, o uso desses protocolos permite que seus clientes usem políticas de acesso condicional e MFA para controlar o acesso aos aplicativos. A Microsoft fornece bibliotecas e [exemplos de código em várias plataformas de tecnologia](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) para ajudar seu desenvolvimento.  

### <a name="implementation"></a>Implementação

Registre seu aplicativo com a identidade da Microsoft, que é um provedor OAuth 2,0. Você também pode registrar seu aplicativo baseado em OAuth 2,0 com qualquer outro provedor de identidade com o qual deseja integrar. 

Para obter informações sobre como registrar seu aplicativo e implementar esses protocolos para o SSO para aplicativos Web, consulte [autorizar o acesso a aplicativos Web usando OpenID Connect e Azure Active Directory](../develop/sample-v2-code.md).  Para obter informações sobre como implementar esses protocolos para SSO em aplicativos móveis, consulte o seguinte: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Plataforma Universal do Windows](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2,0 e WSFed

O Security Assertion Markup Language (SAML) geralmente é usado para aplicativos Web. Veja [como o Azure usa o protocolo SAML](../develop/active-directory-saml-protocol-reference.md) para obter uma visão geral. 

O especificação Web Services Federation (WSFed) é um [padrão do setor](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) geralmente usado para aplicativos Web desenvolvidos usando a plataforma .net.

### <a name="benefits"></a>Vantagens

O SAML 2,0 é um padrão maduro e a maioria das plataformas de tecnologia dá suporte a bibliotecas de software livre para SAML 2,0. Você pode fornecer aos seus clientes uma interface de administração para configurar o SSO do SAML. Eles podem configurar o SSO do SAML para Microsoft Azure AD e qualquer outro provedor de identidade que ofereça suporte a SAML 2

### <a name="trade-offs"></a>Compensações

Ao usar os protocolos SAML 2,0 ou WSFed para aplicativos móveis, determinadas políticas de acesso condicional, incluindo a MFA (autenticação multifator), terão uma experiência degradada. Além disso, se você quiser acessar o Microsoft Graph, será necessário implementar a autorização por meio do OAuth 2,0 para gerar os tokens necessários. 

### <a name="implementation"></a>Implementação

A Microsoft não fornece bibliotecas para implementação SAML ou recomenda bibliotecas específicas. Há muitas bibliotecas de código-fonte aberto disponíveis.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO e usando Microsoft Graph API REST 

Microsoft Graph é a malha de dados em todas as Microsoft 365, incluindo o Office 365, o Windows 10 e o Enterprise Mobility e a segurança, além de produtos adicionais, como o Dynamics 365. Isso inclui os esquemas principais das entidades, como usuários, grupos, calendário, emails, arquivos e muito mais, que impulsionam a produtividade do usuário. O Microsoft Graph oferece três interfaces para desenvolvedores de uma API baseada em REST, Microsoft Graph conexão de dados e conectores que permitem aos desenvolvedores adicionar seus próprios dados ao Microsoft Graph.  

O uso de qualquer um dos protocolos acima para SSO permite que o acesso do seu aplicativo aos dados avançados disponíveis por meio da API REST do Microsoft Graph. Isso permite que seus clientes obtenham mais valor de seus investimentos em Microsoft 365. Por exemplo, seu aplicativo pode chamar a API de Microsoft Graph para integrar com a instância do Office 365 de seus clientes e os itens Microsoft Office e SharePoint de superfície do seu aplicativo. 

Se você estiver usando o Open ID Connect para autenticar, sua experiência de desenvolvimento será simples porque você usará o OAuth2, a base do Open ID Connect, para adquirir tokens que podem ser usados para invocar Microsoft Graph APIs. Se seu aplicativo estiver usando SAML ou WSFed, você deverá adicionar código adicional em seu aplicativo para obter essas OAuth2 para adquirir os tokens necessários para invocar Microsoft Graph APIs. 

## <a name="next-steps"></a>Próximos Passos

[Habilitar o SSO para seu aplicativo multilocatário](isv-sso-content.md)

[Criar documentação para seu aplicativo multilocatário](isv-create-sso-documentation.md)
