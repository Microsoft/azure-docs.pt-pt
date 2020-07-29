---
title: Escolha o protocolo da federação certa para aplicação de vários inquilinos
description: Orientação para fornecedores independentes de software na integração com o Azure Ative Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e7dc40d0e4736b4fef8012b08737cbd07bc36bf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763300"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>Escolha o protocolo da federação certa para a sua aplicação multi-inquilino

Ao desenvolver o seu software como uma aplicação de serviço (SaaS), deve selecionar o protocolo da federação que melhor satisfaça as suas necessidades e as dos seus clientes. Esta decisão baseia-se na sua plataforma de desenvolvimento e no seu desejo de integração com os dados disponíveis no sistema 365 e AD AD dos seus clientes.

Consulte a lista completa de [protocolos disponíveis para integrações SSO](what-is-single-sign-on.md) com o Azure Ative Directory.
A tabela a seguir compara 
* Autenticação Aberta 2.0 (OAuth 2.0)
* Ligação ID aberta (OIDC)
* Linguagem de marcação de afirmação de segurança (SAML)
* Federação de Serviços Web (WSFed)

| Funcionalidade| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| Sign-on único baseado na Web| √| √ |
| Sign-out único baseado na Web| √| √ |
| Sign-on único baseado em dispositivos móveis| √| √* |
| Sign-out único baseado em dispositivos móveis| √| √* |
| Políticas de acesso condicional para aplicações móveis| √| X |
| Experiência de MFA sem emenda para aplicações móveis| √| X |
| Aceder ao Gráfico da Microsoft| √| X |

*Possível, mas a Microsoft não fornece amostras ou orientação.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 e Open ID Connect

OAuth 2.0 é um protocolo [padrão da indústria](https://oauth.net/2/) para autorização. OIDC (OpenID Connect) é uma camada de autenticação de identidade [padrão](https://openid.net/connect/) da indústria construída em cima do protocolo OAuth 2.0.

### <a name="benefits"></a>Benefícios

A Microsoft recomenda a utilização do OIDC/OAuth 2.0, uma vez que têm autenticação e autorização incorporadas nos protocolos. Com a SAML, deve ainda implementar a autorização.

A autorização inerente a estes protocolos permite que a sua aplicação aceda e integre com dados de utilizador e organizacionais ricos através da API do Gráfico da Microsoft.

A utilização da OAuth 2.0 e da OIDC simplifica a experiência do utilizador final dos seus clientes ao adotar sSO para a sua aplicação. Pode definir facilmente os conjuntos de permissão necessários, que são então automaticamente representados ao administrador ou ao consentimento do utilizador final.

Além disso, a utilização destes protocolos permite que os seus clientes utilizem políticas de Acesso Condicional e MFA para controlar o acesso às aplicações. A Microsoft fornece bibliotecas e [amostras de código em várias plataformas tecnológicas](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) para ajudar no seu desenvolvimento.  

### <a name="implementation"></a>Implementação

Regista a sua aplicação com o Microsoft Identity, que é um fornecedor OAuth 2.0. Poderá também registar a sua aplicação baseada em OAuth 2.0 com qualquer outro Fornecedor de Identidade com o quais pretenda integrar.. 

Para obter informações sobre como registar a sua aplicação e implementar estes protocolos para SSO para aplicações web, consulte [o acesso autorizado a aplicações web utilizando o OpenID Connect e o Azure Ative Directory](../develop/sample-v2-code.md).  Para obter informações sobre como implementar estes protocolos para SSO em aplicações móveis, consulte o seguinte: 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [Plataforma Universal do Windows](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 e WSFed

O Language markup de afirmação de segurança (SAML) é geralmente utilizado para aplicações web. Veja [como o Azure utiliza o protocolo SAML](../develop/active-directory-saml-protocol-reference.md) para uma visão geral. 

Web Services Federation (WSFed) é um [padrão da indústria](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) geralmente usado para aplicações web que são desenvolvidos usando a plataforma .Net.

### <a name="benefits"></a>Benefícios

SAML 2.0 é um padrão maduro e a maioria das plataformas tecnológicas suportam bibliotecas de código aberto para SAML 2.0. Pode fornecer aos seus clientes uma interface de administração para configurar a SAML SSO. Podem configurar o SAML SSO para o Microsoft Azure AD e qualquer outro fornecedor de identidade que suporte o SAML 2

### <a name="trade-offs"></a>Compensações

Ao utilizar protocolos SAML 2.0 ou WSFed para aplicações móveis, certas políticas de Acesso Condicional, incluindo a Autenticação Multi-factor (MFA) terão uma experiência degradada. Além disso, se pretender aceder ao Microsoft Graph, terá de implementar a autorização através da OAuth 2.0 para gerar fichas necessárias. 

### <a name="implementation"></a>Implementação

A Microsoft não fornece bibliotecas para implementação DE SAML nem recomenda bibliotecas específicas. Há muitas bibliotecas de código aberto disponíveis.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO e usando Microsoft Graph Rest API 

O Microsoft Graph é o tecido de dados em toda a Microsoft 365, incluindo o Office 365, Windows 10 e Enterprise Mobility and Security, e produtos adicionais como o Dynamics 365. Isto inclui os esquemas centrais das entidades como Utilizadores, Grupos, Calendário, Correio, Ficheiros, entre outros, que impulsionam a produtividade dos utilizadores. O Microsoft Graph oferece três interfaces para programadores de uma API baseada em REST, ligação de dados do Microsoft Graph e Conectores que permitem aos desenvolvedores adicionar os seus próprios dados no Gráfico do Microsoft.  

A utilização de qualquer um dos protocolos acima referidos para SSO permite o acesso da sua aplicação aos dados ricos disponíveis através da API do Microsoft Graph REST. Isto permite que os seus clientes obtenham mais valor com o seu investimento na Microsoft 365. Por exemplo, a sua aplicação pode ligar para a Microsoft Graph API para integrar-se com os itens Microsoft Office e SharePoint dos seus clientes no Microsoft Office e sharePoint dentro da sua aplicação. 

Se estiver a utilizar o Open ID Connect para autenticar, então a sua experiência de desenvolvimento é perfeita porque utilizará o OAuth2, a fundação do Open ID Connect, para adquirir fichas pode ser usada para invocar APIs do Microsoft Graph. Se a sua aplicação estiver a utilizar SAML ou WSFed, tem de adicionar código adicional dentro da sua aplicação para obter estes OAuth2 para adquirir os tokens necessários para invocar as APIs do Gráfico da Microsoft. 

## <a name="next-steps"></a>Passos Seguintes

[Ativar o SSO para a sua aplicação multi-inquilino](isv-sso-content.md)

[Crie documentação para a sua aplicação multi-inquilino](isv-create-sso-documentation.md)
