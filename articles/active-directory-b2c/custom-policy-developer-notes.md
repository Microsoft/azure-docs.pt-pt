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
ms.date: 02/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ee3b5bd3278412949074b77f9d1c53d63a467280
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189400"
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

### <a name="identity-providers-tokens-protocols"></a>Fornecedores de Identidade, Tokens, Protocolos

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-OpenIDConnect |  |  | X | Por exemplo, google+.  |
| IDP-OAUTH2 |  |  | X | Por exemplo, Facebook.  |
| IDP-OAUTH1 (twitter) |  | X |  | Por exemplo, No Twitter. |
| IDP-OAUTH1 (ex-twitter) |  |  |  | Não suportado |
| IDP-SAML |  |   | X | Por exemplo, Salesforce, ADFS. |
| IDP-WSFED | X |  |  |  |
| Partido Dependente OAUTH1 |  |  |  | Não suportado. |
| Partido Dependente OAUTH2 |  |  | X |  |
| Partido Dependente OIDC |  |  | X |  |
| Partido Dependente SAML |  |X  |  |  |
| Partido De Base WSFED | X |  |  |  |
| Rest API com auth básico e certificado |  |  | X | Por exemplo, Aplicativos Azure Logic. |

### <a name="component-support"></a>Suporte de Componentes

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | :-----------: | :-------: | :--: | ----- |
| Autenticação de fator multi-factor azure |  |  | X |  |
| Diretório Azure Ative como diretório local |  |  | X |  |
| Subsistema de e-mail Azure para verificação de email |  |  | X |  |
| Suporte multilingues|  |  | X |  |
| Validações Predicadas |  |  | X | Por exemplo, complexidade de senhas. |
| Utilização de fornecedores de serviços de e-mail de terceiros |  |X  |  |  |

### <a name="content-definition"></a>Definição de Conteúdo

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | :-----------: | :-------: | :--: | ----- |
| Página de erro, api.error |  |  | X |  |
| Página de seleção idp, api.idpselections |  |  | X |  |
| Seleção IDP para inscrição, api.idpselections.signup |  |  | X |  |
| Palavra-passe esquecida, api.localpasswordre |  |  | X |  |
| Conta Local Signin, api.localaccountsignin |  |  | X |  |
| Inscrição de Conta Local, api.localaccountsignup |  |  | X |  |
| Página de MFA, api.phonefactor |  |  | X |  |
| Autoafirmada inscrição na conta social, api.self-afirmou |  |  | X |  |
| Atualização de perfil autoafirmada, api.selfasserted.profileupdate |  |  | X |  |
| Página unificada de inscrição ou inscrição, api.signuporsignin, com parâmetro "desactivaçãoSignup" |  |  | X |  |
| Layout JavaScript / Página |  | X |  |  |

### <a name="app-ief-integration"></a>Integração App-IEF

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parâmetro de corda de consulta domain_hint |  |  | X | Disponível como reclamação, pode ser passado para IDP. |
| Parâmetro de corda de consulta login_hint |  |  | X | Disponível como reclamação, pode ser passado para IDP. |
| Insira a JSON na UserJourney via client_assertion | X |  |  | Será depreciado. |
| Insira a JSON na UserJourney como id_token_hint |  | X |  | Abordagem para a frente para passar jSON. |
| Passe IDP TOKEN para a aplicação |  | X |  | Por exemplo, do Facebook à app. |

### <a name="session-management"></a>Gestão de Sessões

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | :-----------: | :-------: | :--: | ----- |
| Provedor de SSO Sessão |  |  | X |  |
| Fornecedor externo de sessão de login |  |  | X |  |
| Provedor de Sessão SAML SSO |  |  | X |  |
| Provedor de Sessão SSO Padrão |  |  | X |  |

### <a name="security"></a>Segurança

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
|-------- | :-----------: | :-------: | :--: | ----- |
| Chaves de política- Gerar, Manual, Carregar |  |  | X |  |
| Chaves políticas- RSA/Cert, Segredos |  |  | X |  |
| Upload de política |  |  | X |  |

### <a name="developer-interface"></a>Interface de desenvolvedor

| Funcionalidade | Desenvolvimento | Pré-visualização | GA | Notas |
| ------- | :-----------: | :-------: | :--: | ----- |
| Ux do Portal-IEF |  |  | X |  |
| Informações de aplicação UserJourney Logs |  | X |  | Usado para resolução de problemas durante o desenvolvimento.  |
| Registos de eventos de Insights de Aplicação (através de passos de orquestração) |  | X |  | Usado para monitorizar os fluxos de utilizadores na produção. |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [as políticas personalizadas e as diferenças com os fluxos de utilizadores.](custom-policy-overview.md)
