---
title: Notas do desenvolvedor para políticas personalizadas
titleSuffix: Azure AD B2C
description: Notas para desenvolvedores sobre configurar e manter Azure AD B2C com políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c175a6d225be268f27854b9ab63886892cf029fb
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557280"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Notas do desenvolvedor para políticas personalizadas no Azure Ative Directory B2C

A configuração de política personalizada no Azure Ative Directory B2C está agora disponível geralmente. Este método de configuração é direcionado para desenvolvedores de identidade avançados construindo soluções de identidade complexas. As políticas personalizadas disponibilizam o poder do Quadro de Experiência de Identidade nos inquilinos Azure AD B2C.
Os desenvolvedores de identidade avançados que usam políticas personalizadas devem planear investir algum tempo completando documentos de referência de walk-throughs e leitura.

Embora a maioria das opções de política personalizadas disponíveis estejam agora geralmente disponíveis, existem capacidades subjacentes, tais como tipos de perfis técnicos e APIs de definição de conteúdo que estão em diferentes fases do ciclo de vida do software. Muitos mais estão vindo. O quadro abaixo especifica o nível de disponibilidade a um nível mais granular.

## <a name="features-that-are-generally-available"></a>Características que estão geralmente disponíveis

- Autor e faça upload das viagens personalizadas do utilizador da autenticação utilizando políticas personalizadas.
    - Descreva as viagens dos utilizadores passo a passo como trocas entre os fornecedores de sinistros.
    - Defina a ramificação condicional nas viagens do utilizador.
- Interoperar com serviços com API ativados pela REST nas suas viagens personalizadas de utilizador de autenticação.
- Federate com fornecedores de identidade que estejam em conformidade com o protocolo OpenIDConnect.
- Federate com fornecedores de identidade que aderem ao protocolo SAML 2.0.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilidades dos desenvolvedores de recursos de política personalizada

A configuração manual da política concede acesso de nível inferior à plataforma subjacente do Azure AD B2C e resulta na criação de um quadro único e fidedificador. As muitas permutações possíveis de fornecedores de identidade personalizados, relações de confiança, integrações com serviços externos, e fluxos de trabalho passo a passo requerem uma abordagem metódica ao design e configuração.

Os desenvolvedores que consomem o conjunto de recursos de política personalizado devem aderir às seguintes orientações:

- Familiarize-se com a linguagem de configuração das políticas personalizadas e gestão de chaves/segredos. Para mais informações, consulte [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Tome posse de cenários e integrações personalizadas. Documente o seu trabalho e informe a sua organização do site ao vivo.
- Realizar testes de cenário metódico.
- Acompanhe o desenvolvimento de software e as melhores práticas. Recomenda-se um mínimo de um ambiente de desenvolvimento e teste.
- Mantenha-se informado sobre novos desenvolvimentos dos fornecedores de identidade e serviços com os quais integra. Por exemplo, acompanhe as mudanças de segredos e as alterações programadas e não programadas do serviço.
- Crie uma monitorização ativa e monitorize a capacidade de resposta dos ambientes de produção. Para obter mais informações sobre a integração com a Application Insights, consulte [o Azure Ative Directory B2C: Collecting Logs](analytics-with-application-insights.md).
- Mantenha os endereços de e-mail de contato atuais na subscrição do Azure e mantenha-se atento aos e-mails da equipa do site da Microsoft.
- Tome medidas oportunas quando aconselhado a fazê-lo pela equipa do site da Microsoft.

## <a name="terms-for-features-in-public-preview"></a>Termos para funcionalidades na pré-visualização pública

- Encorajamo-lo a usar as funcionalidades de pré-visualização do público apenas para fins de avaliação.
- Os contratos de nível de serviço (SLAs) não se aplicam às funcionalidades de pré-visualização do público.
- Os pedidos de apoio para funcionalidades de pré-visualização públicas podem ser arquivados através de canais de suporte regulares.

## <a name="features-by-stage-and-known-issues"></a>Características por fase e questões conhecidas

As capacidades de política personalizada estão em constante desenvolvimento. O quadro a seguir é um índice de funcionalidades e disponibilidade de componentes.


### <a name="protocols-and-authorization-flows"></a>Protocolos e fluxos de autorização

| Funcionalidade | Desenvolvimento | Pré-visualizar | GA | Notas |
|-------- | :-----------: | :-------: | :--: | ----- |
| [Código de autorização OAuth2](authorization-code-flow.md) |  |  | X |  |
| Código de autorização OAuth2 com PKCE |  |  | X | [Clientes públicos e aplicações de página única](authorization-code-flow.md)  |
| [Fluxo implícito OAuth2](implicit-flow-single-page-application.md) |  |  | X |  |
| [Credenciais de senha do proprietário de recursos OAuth2](add-ropc-policy.md) |  | X |  |  |
| [OIDC Connect](openid-connect.md) |  |  | X |  |
| [SAML2](saml-service-provider.md)  |  |  |X  | ENCADERAMENTO E Redireccionamento de encadernações. |
| OAuth1 |  |  |  | Não suportado. |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>Identificar federação de fornecedores 

| Funcionalidade | Desenvolvimento | Pré-visualizar | GA | Notas |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | Por exemplo, o Google+.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | Por exemplo, Facebook.  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | Por exemplo, Twitter. |
| [SAML2](identity-provider-generic-saml.md) |  |   | X | Por exemplo, Salesforce, ADFS. |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>REST Integração API

| Funcionalidade | Desenvolvimento | Pré-visualizar | GA | Notas |
|-------- | :-----------: | :-------: | :--: | ----- |
| [REST API com auth básico](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [REST API com certificado de cliente auth](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [REST API com Auth2 portador de OAuth2](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>Suporte de componentes

| Funcionalidade | Desenvolvimento | Pré-visualizar | GA | Notas |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Autenticação de fator telefone](phone-factor-technical-profile.md) |  |  | X |  |
| [Autenticação Azure AD MFA](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [Palavra-passe monouso](one-time-password-technical-profile.md) |  | X |  |  |
| [Diretório Ativo Azure](active-directory-technical-profile.md) como diretório local |  |  | X |  |
| Subsistema de e-mail Azure para verificação de e-mail |  |  | X |  |
| [Fornecedores de serviços de e-mail de terceiros](custom-email-mailjet.md) |  |X  |  |  |
| [Suporte multi-linguístico](localization.md)|  |  | X |  |
| [Validações predicados](predicates.md) |  |  | X | Por exemplo, complexidade da palavra-passe. |
| [Controlos de exibição](display-controls.md) |  |X  |  |  |


### <a name="app-ief-integration"></a>Integração app-IEF

| Funcionalidade | Desenvolvimento | Pré-visualizar | GA | Notas |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parâmetro de cadeia de consulta `domain_hint` |  |  | X | Disponível como reclamação, pode ser passado para IDP. |
| Parâmetro de cadeia de consulta `login_hint` |  |  | X | Disponível como reclamação, pode ser passado para IDP. |
| Insira o JSON na viagem do utilizador através `client_assertion` | X |  |  | Será depreciado. |
| Insira o JSON na jornada do utilizador como `id_token_hint` |  | X |  | Avance para passar JSON. |
| [Passe o token do fornecedor de identidade para a aplicação](idp-pass-through-user-flow.md) |  | X |  | Por exemplo, do Facebook à app. |


### <a name="session-management"></a>Gestão de Sessão

| Funcionalidade | Desenvolvimento | Pré-visualizar | GA | Notas |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Fornecedor de sso predefinido](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [Fornecedor externo de sessão de login](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [Provedor de sessão SAML SSO](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |
| [OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider)  |  | X |  |  |
| [Fim de sessão único](session-behavior.md#sign-out)  |  | X |  |  |

### <a name="security"></a>Segurança

| Funcionalidade | Desenvolvimento | Pré-visualizar | GA | Notas |
|-------- | :-----------: | :-------: | :--: | ----- |
| Chaves de política - Gerar, Manual, Carregar |  |  | X |  |
| Chaves de Política- RSA/Cert, Segredos |  |  | X |  |


### <a name="developer-interface"></a>Interface do desenvolvedor

| Funcionalidade | Desenvolvimento | Pré-visualizar | GA | Notas |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Portal-IEF UX |  |  | X |  |
| Upload de política |  |  | X |  |
| [Registos de viagem do utilizador Do Application Insights](troubleshoot-with-application-insights.md) |  | X |  | Usado para resolução de problemas durante o desenvolvimento.  |
| [Registos de eventos do Application Insights](analytics-with-application-insights.md) |  | X |  | Usado para monitorizar os fluxos de produção dos utilizadores. |


## <a name="next-steps"></a>Passos seguintes

- Consulte as operações do [Microsoft Graph disponíveis para Azure AD B2C](microsoft-graph-operations.md)
- Saiba mais sobre [as políticas personalizadas e as diferenças com os fluxos dos utilizadores.](custom-policy-overview.md)
