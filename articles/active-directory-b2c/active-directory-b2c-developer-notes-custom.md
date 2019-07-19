---
title: Notas do desenvolvedor para políticas personalizadas – Azure Active Directory B2C | Microsoft Docs
description: Observações para os desenvolvedores sobre como configurar e manter Azure AD B2C com políticas personalizadas.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f8d1ac217647ee292338da875671ef8bd3f79db
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227201"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Notas do desenvolvedor para políticas personalizadas no Azure Active Directory B2C

A configuração de política personalizada no Azure Active Directory B2C agora está disponível para o público geral. Esse método de configuração é direcionado a desenvolvedores de identidade avançada que criam soluções de identidade complexas. As políticas personalizadas tornam o poder da estrutura de experiência de identidade disponível em locatários Azure AD B2C.
Os desenvolvedores de identidade avançada usando políticas personalizadas devem planejar investir algum tempo concluindo orientações e lendo documentos de referência.

Embora a maioria das opções de política personalizada disponíveis agora esteja disponível para o público geral, há recursos subjacentes, como tipos de perfil técnico e APIs de definição de conteúdo que estão em diferentes estágios no ciclo de vida do software. Muito mais estão chegando. A tabela abaixo especifica o nível de disponibilidade em um nível mais granular.

## <a name="features-that-are-generally-available"></a>Recursos que estão geralmente disponíveis

- Crie e carregue percursos do usuário de autenticação personalizada usando políticas personalizadas.
    - Descrever as viagens passo a passo do usuário como trocas entre provedores de declarações.
    - Defina a ramificação condicional em percursos do usuário.
- Interopere com serviços habilitados para API REST em suas jornadas do usuário de autenticação personalizada.
- Federar com provedores de identidade que são compatíveis com o protocolo OpenIDConnect.
- Federar com provedores de identidade que aderem ao protocolo SAML 2,0.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilidades do conjunto de recursos de política personalizada – desenvolvedores

A configuração de política manual concede acesso de nível inferior à plataforma subjacente do Azure AD B2C e resulta na criação de uma estrutura exclusiva de confiança. As muitas permutas possíveis de provedores de identidade personalizados, relações de confiança, integrações com serviços externos e fluxos de trabalho passo a passo exigem uma abordagem unidirecional para design e configuração.

Os desenvolvedores que consomem o conjunto de recursos de política personalizada devem aderir às seguintes diretrizes:

- Familiarize-se com o idioma de configuração das políticas personalizadas e do gerenciamento de chaves/segredos. Para obter mais informações, consulte [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Aproprie-se de cenários e integrações personalizadas. Documente seu trabalho e informe sua organização do site ativo.
- Execute testes de cenário método.
- Siga as práticas recomendadas de desenvolvimento e preparo de software com um mínimo de um ambiente de desenvolvimento e teste e um ambiente de produção.
- Mantenha-se informado sobre novos desenvolvimentos dos provedores de identidade e dos serviços com os quais você se integra. Por exemplo, mantenha o controle das alterações nos segredos e das alterações agendadas e não agendadas para o serviço.
- Configure o monitoramento ativo e monitore a capacidade de resposta dos ambientes de produção. Para obter mais informações sobre como integrar com o [Application insights, consulte Azure Active Directory B2C: Coletando](active-directory-b2c-custom-guide-eventlogger-appins.md)logs.
- Mantenha os endereços de email de contato atuais na assinatura do Azure e Continue respondendo aos emails da equipe de site ativo da Microsoft.
- Tome uma ação oportuna quando for aconselhável fazer isso pela equipe de site do Microsoft Live.

## <a name="terms-for-features-in-public-preview"></a>Termos para recursos na visualização pública

- Incentivamos você a usar os recursos de visualização pública apenas para fins de avaliação.
- Os SLAs (contratos de nível de serviço) não se aplicam aos recursos de visualização pública.
- As solicitações de suporte para recursos de visualização pública podem ser arquivadas por meio de canais de suporte regular.

## <a name="features-by-stage-and-known-issues"></a>Recursos por estágio e problemas conhecidos

Recursos personalizados de estrutura de experiência de identidade/política estão em constante e desenvolvimento rápido. A tabela a seguir é um índice de recursos e disponibilidade de componentes.

### <a name="identity-providers-tokens-protocols"></a>Provedores de identidade, tokens, protocolos

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-OpenIDConnect |  |  | X | Por exemplo, Google +.  |
| IDP-OAUTH2 |  |  | X | Por exemplo, Facebook.  |
| IDP-OAUTH1 (Twitter) |  | X |  | Por exemplo, Twitter. |
| IDP-OAUTH1 (ex-Twitter) |  |  |  | Não suportado |
| IDP-SAML |  |   | X | Por exemplo, Salesforce, ADFS. |
| IDP-WSFED | X |  |  |  |
| OAUTH1 de terceira parte confiável |  |  |  | Não suportado. |
| OAUTH2 de terceira parte confiável |  |  | X |  |
| OIDC de terceira parte confiável |  |  | X |  |
| SAML de terceira parte confiável | X |  |  |  |
| WSFED de terceira parte confiável | X |  |  |  |
| API REST com autenticação básica e de certificado |  |  | X | Por exemplo, aplicativos lógicos do Azure. |

### <a name="component-support"></a>Suporte a componentes

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | :-----------: | :-------: | :--: | ----- |
| Multi-Factor Authentication do Azure |  |  | X |  |
| Azure Active Directory como diretório local |  |  | X |  |
| Subsistema de email do Azure para verificação de email |  |  | X |  |
| Suporte a vários idiomas|  |  | X |  |
| Validações de predicado |  |  | X | Por exemplo, complexidade de senha. |
| Usando provedores de serviço de email de terceiros | X |  |  |  |

### <a name="content-definition"></a>Definição de conteúdo

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | :-----------: | :-------: | :--: | ----- |
| Página de erro, API. Error |  |  | X |  |
| Página de seleção de IDP, API. idpselections |  |  | X |  |
| Seleção de IDP para inscrição, API. idpselections. signup |  |  | X |  |
| Esqueceu a senha, API. localaccountpasswordreset |  |  | X |  |
| Entrada da conta local, API. localaccountsignin |  |  | X |  |
| Inscrição na conta local, API. localaccountsignup |  |  | X |  |
| Página MFA, API. PhoneFactor |  |  | X |  |
| Inscrição de conta social autodeclarada, API. selfasserted |  |  | X |  |
| Atualização de perfil autodeclarado, API. selfasserted. profileUpdate |  |  | X |  |
| Inscrição unificada ou página de entrada, API. signuporsignin, com o parâmetro "disableSignup" |  |  | X |  |
| JavaScript/layout da página |  | X |  |  |

### <a name="app-ief-integration"></a>Integração do App-IEF

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parâmetro de cadeia de caracteres de consulta domain_hint |  |  | X | Disponível como declaração, pode ser passado para IDP. |
| Parâmetro de cadeia de caracteres de consulta login_hint |  |  | X | Disponível como declaração, pode ser passado para IDP. |
| Inserir JSON em userjornada via client_assertion | X |  |  | Será preterido. |
| Inserir JSON em userjornada como id_token_hint |  | X |  | Abordagem de avanço para passar JSON. |
| Passar o TOKEN IDP para o aplicativo |  | X |  | Por exemplo, do Facebook para o aplicativo. |

### <a name="session-management"></a>Gerenciamento de sessão

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | :-----------: | :-------: | :--: | ----- |
| Provedor de sessão de SSO |  |  | X |  |
| Provedor de sessão de logon externo |  |  | X |  |
| Provedor de sessão de SSO do SAML |  |  | X |  |
| Provedor de sessão de SSO padrão |  |  | X |  |

### <a name="security"></a>Segurança

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
|-------- | :-----------: | :-------: | :--: | ----- |
| Chaves de política – gerar, manual, carregar |  |  | X |  |
| Chaves de política-RSA/certificado, segredos |  |  | X |  |
| Upload de política |  |  | X |  |

### <a name="developer-interface"></a>Interface do desenvolvedor

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | :-----------: | :-------: | :--: | ----- |
| Portal do Azure-IEF UX |  |  | X |  |
| Application Insights logs do userjornada |  | X |  | Usado para solução de problemas durante o desenvolvimento.  |
| Application Insights logs de eventos (por meio de etapas de orquestração) |  | X |  | Usado para monitorar fluxos de usuário na produção. |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [políticas personalizadas e as diferenças com fluxos de usuário](active-directory-b2c-overview-custom.md).
