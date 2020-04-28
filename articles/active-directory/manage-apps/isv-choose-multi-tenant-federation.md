---
title: Escolha o protocolo certo da federação para aplicação de multi-inquilinos
description: Orientação para fornecedores independentes de software sobre integração com o Diretório Ativo Azure
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
ms.openlocfilehash: 3b3edbbe037c3874d639476e516b3732b7573d9b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75443382"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Escolha o protocolo da federação certa para a sua aplicação multi-inquilino

Quando desenvolve o seu software como uma aplicação de serviço (SaaS), deve selecionar o protocolo da federação que melhor satisfaz as necessidades dos seus clientes e dos seus clientes. Esta decisão baseia-se na sua plataforma de desenvolvimento e no seu desejo de integrar os dados disponíveis no âmbito do Office 365 dos seus clientes e do ecossistema Azure AD.

Consulte a lista completa de [protocolos disponíveis para integrações SSO](what-is-single-sign-on.md) com o Diretório Ativo Azure.
A tabela seguinte compara 
* Autenticação Aberta 2.0 (OAuth 2.0)
* Open ID Connect (OIDC)
* Linguagem de marcação de afirmação de segurança (SAML)
* Federação de Serviços Web (WSFed)

| Capacidade| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| Inscrição única baseada na Web| √| √ |
| Inscrição única baseada na Web| √| √ |
| Inscrição single baseada em dispositivos móveis| √| √* |
| Inscrição única baseada em dispositivos móveis| √| √* |
| Políticas de acesso condicional para aplicações móveis| √| X |
| Experiência MFA sem emenda para aplicações móveis| √| X |
| Aceder ao Microsoft Graph| √| X |

*Possível, mas a Microsoft não fornece amostras ou orientação.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 e Open ID Connect

O AUth 2.0 é um protocolo [padrão para](https://oauth.net/2/) a indústria de autorização. OIDC (OpenID Connect) é uma camada de autenticação de identidade padrão da [indústria](https://openid.net/connect/) construída em cima do protocolo OAuth 2.0.

### <a name="benefits"></a>Benefícios

A Microsoft recomenda a utilização do OIDC/OAuth 2.0, uma vez que têm autenticação e autorização incorporadas nos protocolos. Com a SAML, deve implementar ainda a autorização.

A autorização inerente a estes protocolos permite que a sua aplicação aceda e integre com dados de utilizadores e organizacionais ricos através da API do Microsoft Graph.

A utilização da OAuth 2.0 e da OIDC simplifica a experiência do utilizador final dos seus clientes ao adotar o SSO para a sua aplicação. Pode definir facilmente os conjuntos de permissão necessários, que são automaticamente representados para o administrador ou o consentimento do utilizador final.

Além disso, a utilização destes protocolos permite aos seus clientes utilizar em condições de Acesso Condicional e MFA para controlar o acesso às aplicações. A Microsoft fornece bibliotecas e amostras de [código em várias plataformas tecnológicas](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) para ajudar no seu desenvolvimento.  

### <a name="implementation"></a>Implementação

Regista a sua aplicação com a Microsoft Identity, que é um fornecedor OAuth 2.0. Em seguida, poderá também registar a sua aplicação baseada em OAuth 2.0 com qualquer outro Fornecedor de Identidade com o que deseje integrar. 

Para obter informações sobre como registar a sua aplicação e implementar estes protocolos para SSO para aplicações web, consulte [Autorizar o acesso a aplicações web utilizando o OpenID Connect e o Azure Ative Directory](../develop/sample-v2-code.md).  Para obter informações sobre como implementar estes protocolos para SSO em aplicações móveis, consulte o seguinte: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Plataforma Universal do Windows](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 e WSFed

A linguagem de marcação de afirmação de segurança (SAML) é normalmente utilizada para aplicações web. Veja [como o Azure utiliza o protocolo SAML](../develop/active-directory-saml-protocol-reference.md) para uma visão geral. 

Web Services Federation (WSFed) é um padrão da [indústria](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) geralmente usado para aplicações web que são desenvolvidos usando a plataforma .Net.

### <a name="benefits"></a>Benefícios

O SAML 2.0 é um padrão maduro e a maioria das plataformas tecnológicas suportam bibliotecas de código aberto para o SAML 2.0. Pode fornecer aos seus clientes uma interface de administração para configurar o SAML SSO. Podem configurar saml SSO para a AD Microsoft Azure, e qualquer outro fornecedor de identidade que suporte o SAML 2

### <a name="trade-offs"></a>Compensações

Ao utilizar protocolos SAML 2.0 ou WSFed para aplicações móveis, certas políticas de Acesso Condicional, incluindo a Autenticação Multifactor (MFA), terão uma experiência degradada. Além disso, se quiser aceder ao Microsoft Graph, terá de implementar a autorização através da OAuth 2.0 para gerar tokens necessários. 

### <a name="implementation"></a>Implementação

A Microsoft não fornece bibliotecas para implementação do SAML nem recomenda bibliotecas específicas. Existem muitas bibliotecas de código aberto disponíveis.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO e usando a Microsoft Graph Rest API 

O Microsoft Graph é o tecido de dados em toda a Microsoft 365, incluindo office 365, Windows 10 e Enterprise Mobility and Security, e produtos adicionais como dynamics 365. Isto inclui os esquemas centrais das entidades como Utilizadores, Grupos, Calendário, Correio, Ficheiros e muito mais, que impulsionam a produtividade dos utilizadores. O Microsoft Graph oferece três interfaces para desenvolvedores com base em API, Microsoft Graph e Conectores que permitem aos desenvolvedores adicionar os seus próprios dados no Microsoft Graph.  

A utilização de qualquer um dos protocolos acima referidos para o SSO permite o acesso da sua aplicação aos dados ricos disponíveis através da API do Microsoft Graph REST. Isto permite que os seus clientes obtenhamais valor do seu investimento na Microsoft 365. Por exemplo, a sua aplicação pode ligar para a Microsoft Graph API para integrar com os itens do Microsoft Office 365 dos seus clientes e dos utilizadores de superfície Microsoft Office e SharePoint dentro da sua aplicação. 

Se estiver a usar o Open ID Connect para autenticar, então a sua experiência de desenvolvimento é perfeita porque utilizará o OAuth2, a fundação do Open ID Connect, para adquirir fichas pode ser usada para invocar APIs do Microsoft Graph. Se a sua aplicação estiver a utilizar o SAML ou o WSFed, tem de adicionar código adicional dentro da sua aplicação para que estes OAuth2 adquiram os tokens necessários para invocar apis do Microsoft Graph. 

## <a name="next-steps"></a>Passos Seguintes

[Ativar o SSO para a sua aplicação multi-inquilino](isv-sso-content.md)

[Crie documentação para a sua aplicação multi-inquilino](isv-create-sso-documentation.md)
