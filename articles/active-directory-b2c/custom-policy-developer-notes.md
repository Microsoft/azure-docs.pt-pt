---
title: Notas de desenvolvedor para políticas personalizadas
titleSuffix: Azure AD B2C
description: Notas para desenvolvedores sobre configuração e manutenção Azure AD B2C com políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403ca480bcf0743d81e375c122c888db96bbf543
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80408703"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Notas de desenvolvedor espreitapara políticas personalizadas no Diretório Ativo Azure B2C

A configuração de política personalizada no Diretório Ativo Azure B2C está agora geralmente disponível. Este método de configuração é direcionado para desenvolvedores de identidade avançadas construindo soluções de identidade complexas. As políticas personalizadas disponibilizam o poder do Quadro de Experiência de Identidade nos inquilinos DoAzure AD B2C.
Os desenvolvedores de identidade avançados que usam políticas personalizadas devem planear investir algum tempo completando walk-throughs e lendo documentos de referência.

Embora a maioria das opções de política personalizadas disponíveis estejam agora geralmente disponíveis, existem capacidades subjacentes, tais como tipos de perfil técnico e APIs de definição de conteúdo que estão em diferentes fases do ciclo de vida do software. Muitos mais estão vindo. O quadro abaixo especifica o nível de disponibilidade a um nível mais granular.

## <a name="features-that-are-generally-available"></a>Características que estão geralmente disponíveis

- Autor e carregue as viagens de utilizador de autenticação personalizada utilizando políticas personalizadas.
    - Descreva as viagens de utilizador passo a passo como trocas entre fornecedores de sinistros.
    - Defina a ramificação condicional nas viagens do utilizador.
- Interopera com serviços com API REST nas suas viagens de utilizador de autenticação personalizada.
- Federate com fornecedores de identidade que estejam em conformidade com o protocolo OpenIDConnect.
- Federate com fornecedores de identidade que aderem ao protocolo SAML 2.0.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilidades dos desenvolvedores de funcionalidades de política personalizada

A configuração da política manual permite um acesso de nível inferior à plataforma subjacente do Azure AD B2C e resulta na criação de um quadro único e de confiança. As muitas permutações possíveis de fornecedores de identidade personalizadas, relações de confiança, integrações com serviços externos e fluxos de trabalho passo a passo requerem uma abordagem metódica ao design e configuração.

Os desenvolvedores que consomem o conjunto de funcionalidades de política personalizada devem aderir às seguintes orientações:

- Familiarize-se com a linguagem de configuração das políticas personalizadas e gestão de chaves/segredos. Para mais informações, consulte [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Apropriar-se de cenários e integrações personalizadas. Documente o seu trabalho e informe a organização do seu site ao vivo.
- Realizar testes de cenário metódico.
- Siga o desenvolvimento de software e encenando as melhores práticas com um mínimo de um ambiente de desenvolvimento e teste e um ambiente de produção.
- Mantenha-se informado sobre novos desenvolvimentos dos fornecedores de identidade e serviços com os quais se integra. Por exemplo, acompanhe as mudanças nos segredos e das alterações programadas e não programadas no serviço.
- Instale uma monitorização ativa e monitorize a capacidade de resposta dos ambientes de produção. Para obter mais informações sobre a integração com os Insights de Aplicação, consulte [o Diretório Ativo Azure B2C: Recolha de Registos](analytics-with-application-insights.md).
- Mantenha os endereços de e-mail de contacto atuais na subscrição do Azure e mantenha-se atento aos e-mails da equipa de sites ao vivo da Microsoft.
- Tome medidas oportunas quando aconselhado a fazê-lo pela equipa de sites ao vivo da Microsoft.

## <a name="terms-for-features-in-public-preview"></a>Termos para funcionalidades na pré-visualização pública

- Encorajamo-lo a utilizar as funcionalidades de pré-visualização pública apenas para fins de avaliação.
- Os acordos de nível de serviço (SLAs) não se aplicam às funcionalidades de pré-visualização pública.
- Os pedidos de apoio para funcionalidades de pré-visualização pública podem ser apresentados através de canais de suporte regulares.

## <a name="features-by-stage-and-known-issues"></a>Características por palco e questões conhecidas

Política personalizada/Experiência de Identidade As capacidades de enquadramento estão em constante e rápido desenvolvimento. A tabela seguinte é um índice de funcionalidades e disponibilidade de componentes.


### <a name="protocols-and-authorization-flows"></a>Protocolos e fluxos de autorização

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
|-------- | :-----------: | :-------: | :--: | ----- |
| [Código de autorização OAuth2](authorization-code-flow.md) |  |  | X |  |
| Código de autorização OAuth2 com PKCE |  |  | X | Aplicações móveis apenas  |
| [Fluxo implícito OAuth2](implicit-flow-single-page-application.md) |  |  | X |  |
| [Credenciais de senha do proprietário de recursos OAuth2](ropc-custom.md) |  | X |  |  |
| [Ligação OIDC](openid-connect.md) |  |  | X |  |
| [SAML2](connect-with-saml-service-providers.md)  |  |X  |  | ENCADEr e Redirecionar encadernações. |
| OAuth1 |  |  |  | Não suportado. |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>Identificar a federação de fornecedores 

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | Por exemplo, google+.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | Por exemplo, Facebook.  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | Por exemplo, No Twitter. |
| [SAML2](saml-technical-profile.md) |  |   | X | Por exemplo, Salesforce, ADFS. |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>Integração da API REST

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
|-------- | :-----------: | :-------: | :--: | ----- |
| [REST API com auth básico](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [REST API com certificado de cliente auth](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [REST API com o portador oAuth2](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>Suporte de componentes

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Autenticação do fator telefone](phone-factor-technical-profile.md) |  |  | X |  |
| [Autenticação Azure MFA](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [Palavra-passe monouso](one-time-password-technical-profile.md) |  | X |  |  |
| [Diretório Azure Ative](active-directory-technical-profile.md) como diretório local |  |  | X |  |
| Subsistema de e-mail Azure para verificação de email |  |  | X |  |
| [Fornecedores de serviços de e-mail de terceiros](custom-email.md) |  |X  |  |  |
| [Suporte multilingues](localization.md)|  |  | X |  |
| [Validações predicadas](predicates.md) |  |  | X | Por exemplo, complexidade de senhas. |
| [Controlos de exibição](display-controls.md) |  |X  |  |  |


### <a name="page-layout-versions"></a>Versões do esquema de página

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | :-----------: | :-------: | :--: | ----- |
| [2.0.0](page-layout.md#200) |  | X |  |  |
| [1.2.0](page-layout.md#120) |  | X |  |  |
| [1.1.0](page-layout.md#110) |  |  | X |  |
| [1.0.0](page-layout.md#100) |  |  | X |  |
| [Suporte JavaScript](javascript-samples.md) |  | X |  |  |

### <a name="app-ief-integration"></a>Integração App-IEF

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parâmetro de corda de consulta`domain_hint` |  |  | X | Disponível como reclamação, pode ser passado para IDP. |
| Parâmetro de corda de consulta`login_hint` |  |  | X | Disponível como reclamação, pode ser passado para IDP. |
| Insira a JSON na viagem do utilizador via`client_assertion` | X |  |  | Será depreciado. |
| Insira a JSON na viagem do utilizador como`id_token_hint` |  | X |  | Abordagem para a frente para passar jSON. |
| [Passe o fornecedor de identidade ficha para o pedido](idp-pass-through-custom.md) |  | X |  | Por exemplo, do Facebook à app. |

### <a name="session-management"></a>Gestão de Sessões

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Provedor de sessão SSO padrão](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [Fornecedor externo de sessão de login](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [Provedor de sessão SAML SSO](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |


### <a name="security"></a>Segurança

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
|-------- | :-----------: | :-------: | :--: | ----- |
| Chaves de política- Gerar, Manual, Carregar |  |  | X |  |
| Chaves políticas- RSA/Cert, Segredos |  |  | X |  |


### <a name="developer-interface"></a>Interface de desenvolvedor

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | :-----------: | :-------: | :--: | ----- |
| Ux do Portal-IEF |  |  | X |  |
| Upload de política |  |  | X |  |
| [Registos de viagem de utilizador insights de aplicação](troubleshoot-with-application-insights.md) |  | X |  | Usado para resolução de problemas durante o desenvolvimento.  |
| [Registos de eventos de Insights de Aplicação](application-insights-technical-profile.md) |  | X |  | Usado para monitorizar os fluxos de utilizadores na produção. |


## <a name="next-steps"></a>Passos seguintes

- Consulte as operações do [Microsoft Graph disponíveis para O Azure AD B2C](microsoft-graph-operations.md)
- Saiba mais sobre [as políticas personalizadas e as diferenças com os fluxos de utilizadores.](custom-policy-overview.md)
