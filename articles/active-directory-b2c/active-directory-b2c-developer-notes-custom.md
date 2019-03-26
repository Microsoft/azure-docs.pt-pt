---
title: Notas do programador para as políticas personalizadas - Azure Active Directory B2C | Documentos da Microsoft
description: Notas para os desenvolvedores de configuração e manutenção do Azure AD B2C com as políticas personalizadas.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: cf9c6f6a54c38f00e477e2a9d62e72ab5faccdef
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418929"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Notas do programador para as políticas personalizadas no Azure Active Directory B2C

Configuração de política personalizada no Azure Active Directory B2C está agora em disponibilidade geral. Este método de configuração é direcionado para programadores de identidades avançada criação de soluções de identidade complexos. As políticas personalizadas, disponibilize o poder do Framework de experiência de identidade em inquilinos do Azure AD B2C. Avançada de identidade, os desenvolvedores que usam as políticas personalizadas devem planejar para investir algum tempo a concluir as instruções passo a passo e ler os documentos de referência.

Embora a maioria das opções de política personalizada disponíveis está agora em disponibilidade geral, há capacidades subjacentes, como tipos de perfil técnico e de APIs que estão em diferentes fases do ciclo de vida do software de definição de conteúdo. Muitos mais estão a chegar. A tabela a seguir especifica o nível de disponibilidade num nível mais granular.  

## <a name="features-that-are-generally-available"></a>Funcionalidades que estão disponíveis em geral

- Autor e carregamento de autenticação personalizado utilizador Jornadas ao utilizar políticas personalizadas.  
    - Descreva jornadas de usuário passo a passo como trocas entre fornecedores de afirmações.  
    - Defina a ramificação condicional no jornadas de utilizador.  
- Interopere com os serviços de ativada para API do REST de mensagens em fila no seu jornadas de utilizador de autenticação personalizado.  
- Federar com fornecedores de identidade que estão em conformidade com o protocolo de OpenIDConnect.  
- Federar com fornecedores de identidade que sigam para o protocolo SAML 2.0.   

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilidades de desenvolvedores do conjunto de funcionalidades de política personalizada

Configuração da política manual concede acesso de nível inferior para a plataforma subjacente do Azure AD B2C e os resultados na criação de uma estrutura de confiança único. As combinações possíveis muitos dos fornecedores de identidade personalizada, relações de confiança, integrações com serviços externos e fluxos de trabalho passo a passo exigem uma abordagem metódica para o design e a configuração. 

Os desenvolvedores consumindo o conjunto de recursos de política personalizada devem seguir as diretrizes seguintes:

- Familiarize-se com a linguagem de configuração de gestão de chaves/segredos e políticas personalizado. Para obter mais informações, consulte [TrustFrameworkPolicy](trustframeworkpolicy.md). 
- Assumir a propriedade dos cenários e integrações personalizadas. Documentar seu trabalho e informar a sua organização de site em direto.  
- Realize os testes de cenário metódico. 
- Siga o desenvolvimento de software e melhores práticas com um mínimo de um ambiente de teste e desenvolvimento e um ambiente de produção de teste. 
- Mantenha-se informado sobre novos desenvolvimentos do fornecedores de identidade e integrar com os serviços. Por exemplo, mantenha um registo de alterações no segredos e das alterações programadas e não programadas para o serviço. 
- Configurar a monitorização de Active Directory e monitorize a capacidade de resposta de ambientes de produção. Para obter mais informações sobre a integração com o Application Insights, consulte [Azure Active Directory B2C: Recolher registos](active-directory-b2c-custom-guide-eventlogger-appins.md). 
- Manter os endereços de correio eletrónico de contacto atualizados na subscrição do Azure e permaneça responsivo ao mensagens de e-mail de equipa do site em direto da Microsoft. 
- Tome medidas atempada somente quando aconselhado para fazê-lo pela equipe de sites ativos da Microsoft.

## <a name="terms-for-features-in-public-preview"></a>Termos de funcionalidades em pré-visualização pública

- Nós o encorajamos a utilizar as funcionalidades de pré-visualização pública para fins de avaliação apenas. 
- Contratos de nível de serviço (SLAs) não se aplicam às funcionalidades de pré-visualização pública.
- Pedidos de suporte para funcionalidades de pré-visualização pública podem ser arquivados por meio de canais de suporte regular. 

## <a name="features-by-stage-and-known-issues"></a>Funcionalidades ao estágio e problemas conhecidos

Capacidades de arquitetura de experiências de identidade/política personalizada estão em desenvolvimento de constante e rápido. A tabela seguinte é um índice de funcionalidades e a disponibilidade de componente.

### <a name="identity-providers-tokens-protocols"></a>Protocolos de fornecedores, Tokens, de identidade

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
|-------- | ----------- | ------- | -- | ----- |
| IDP-OpenIDConnect |  |  | X | Por exemplo, Google +.  |
| IDP OAUTH2 |  |  | X | Por exemplo, o Facebook.  |
| IDP-OAUTH1 (twitter) |  | X |  | Por exemplo, o Twitter. |
| (Por exemplo-twitter) de OAUTH1 IDP |  |  |  | Não suportado |
| IDP-SAML |  |   | X | Por exemplo, Salesforce, ADFS. |
| IDP-WSFED | X |  |  |  |
| OAUTH1 da entidade confiadora de terceiros |  |  |  | Não suportado. |
| OAUTH2 da entidade confiadora de terceiros |  |  | X |  |
| Da entidade confiadora de terceiros OIDC |  |  | X |  |
| Da entidade confiadora de Party SAML | X |  |  |  |
| Entidade confiadora Party WSFED | X |  |  |  |
| API de REST com basic e autenticação de certificados |  |  | X | Por exemplo, o Azure Logic Apps. |

### <a name="component-support"></a>Suporte de componente

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | ----------- | ------- | -- | ----- |
| Multi-Factor Authentication do Azure |  |  | X |  |
| O Azure Active Directory como o diretório local |  |  | X |  |
| Subsistema de E-Mail do Azure para a verificação de e-mail |  |  | X |  |
| Suporte a vários idiomas|  |  | X |  |
| Predicado validações |  |  | X | Por exemplo, a complexidade de palavra-passe. |
| Utilizar fornecedores de serviços de e-mail de terceiros | X |  |  |  |

### <a name="content-definition"></a>Definição de conteúdo

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | ----------- | ------- | -- | ----- |
| Página de erro, api.error |  |  | X |  |
| Página de seleção de IDP, api.idpselections |  |  | X |  |
| Seleção do IDP para a inscrição, api.idpselections.signup |  |  | X |  |
| Esqueci-me da palavra-passe, api.localaccountpasswordreset |  |  | X |  |
| Conta local. o início de sessão, api.localaccountsignin |  |  | X |  |
| Api.localaccountsignup inscrição, da conta local |  |  | X |  |
| Página MFA, api.phonefactor |  |  | X |  |
| Api.selfasserted de inscrição, de declaração própria conta de redes sociais |  |  | X |  |
| Autodeclarativas atualização de perfil, api.selfasserted.profileupdate |  |  | X |  |
| Unificada de inscrição ou início de sessão na página, api.signuporsignin, com o parâmetro "disableSignup" |  |  | X |  |
| JavaScript / página de contrato |  | X |  |  |

### <a name="app-ief-integration"></a>Integração de aplicações-IEF

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | ----------- | ------- | -- | ----- |
| Query string parameter domain_hint |  |  | X | Disponível como afirmações, pode ser passado para o IDP. |
| Login_hint de parâmetro de cadeia de caracteres de consulta |  |  | X | Disponível como afirmações, pode ser passado para o IDP. |
| Inserir JSON UserJourney via client_assertion | X |  |  | Vão ser preteridos. |
| Inserir JSON UserJourney como id_token_hint |  | X |  | Abordagem de go-forward passar JSON. |
| Passar o TOKEN do IDP para a aplicação |  | X |  | Por exemplo, do Facebook para a aplicação. |

### <a name="session-management"></a>Gerenciamento de sessões

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | ----------- | ------- | -- | ----- |
| Fornecedor de sessões SSO |  |  | X |  |
| Fornecedor de sessão de início de sessão externo |  |  | X |  |
| Fornecedor de sessão de SAML SSO |  |  | X |  |
| Fornecedor de sessão SSO predefinido |  |  | X |  |

### <a name="security"></a>Segurança

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
|-------- | ----------- | ------- | -- | ----- |
| Chaves de política - gerar, Manual, carregamento |  |  | X |  |
| Política de chaves - RSA/Cert, segredos |  |  | X |  |
| Carregamento da política |  |  | X |  |

### <a name="developer-interface"></a>Interface do desenvolvedor

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | ----------- | ------- | -- | ----- |
| Portal do Azure-IEF UX |  |  | X |  |
| Registos de UserJourney do Application Insights |  | X |  | Utilizado para resolução de problemas durante o desenvolvimento.  |
| Registos de eventos do Application Insights (por meio de etapas da orquestração) |  | X |  | Utilizado para monitorizar os fluxos de utilizador na produção. |

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre as políticas personalizadas e as diferenças com fluxos de utilizador](active-directory-b2c-overview-custom.md).
