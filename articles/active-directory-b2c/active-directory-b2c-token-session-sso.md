---
title: Sessão e o única início de sessão em configuração - Azure Active Directory B2C | Documentos da Microsoft
description: Sessão e a configuração de início de sessão única no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 674a20fc96cf5b86219222d746525a3559ae9d09
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59681102"
---
# <a name="session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Sessão e a configuração de início de sessão única no Azure Active Directory B2C

Esta funcionalidade dá-lhe um controlo detalhado, por um [base de fluxo de por utilizador](active-directory-b2c-reference-policies.md), de:

- Durações das sessões do aplicativo web geridos pelo Azure AD B2C.
- Single sign-on (SSO) do comportamento em várias aplicações e fluxos de utilizador no seu inquilino do Azure AD B2C.

## <a name="session-behavior"></a>Comportamento da sessão

O Azure AD B2C suporta o [protocolo de autenticação OpenID Connect](active-directory-b2c-reference-oidc.md) para ativar a sessão seguro às aplicações web. Pode utilizar as seguintes propriedades para gerir sessões do aplicativo web:

- **Aplicação Web duração da sessão (minutos)** - a vida útil do cookie de sessão do Azure AD B2C armazenado no navegador do usuário após a autenticação com êxito.
    - Predefinição = 1440 minutos.
    - Mínimo (inclusive) = 15 minutos.
    - Máximo (inclusive) = 1440 minutos.
- **Tempo limite de sessão de aplicação Web** – se este parâmetro estiver definido como **absoluto**, o utilizador é forçado a autenticar novamente após o período de tempo especificado pelo **aplicação Web duração da sessão (minutos)** decorrida. Se este parâmetro estiver definido como **Rolling** (a predefinição), o usuário permanece com sessão iniciada, desde que o utilizador está continuamente ativo na sua aplicação web.

Os seguintes casos de utilização estão ativados com estas propriedades:

- Cumprir os requisitos de segurança e conformidade da sua indústria ao definir a sessão de aplicativo da web apropriados tempos de vida.
- Forçar autenticação após um período de tempo durante a interação do usuário com uma parte de alta segurança da sua aplicação web. 

Estas definições não estão disponíveis para fluxos de utilizador de reposição de palavra-passe.

## <a name="single-sign-on-sso-configuration"></a>Configuração de início de sessão único (SSO)

Se tiver várias aplicações e fluxos de utilizador no seu inquilino do B2C, pode gerir interações de utilizador por elas com o **configuração do início de sessão única** propriedade. Pode definir a propriedade a uma das seguintes definições:

- **Inquilino** -esta é a predefinição. Com esta definição permite que vários aplicativos e de utilizador fluxos no seu inquilino de B2C para partilhar a mesma sessão do utilizador. Por exemplo, uma vez que um utilizador inicia sessão numa aplicação, o utilizador pode iniciar diretamente em outro farmácia Contoso um, após a aceder ao mesmo.
- **Aplicação** -esta definição permite-lhe manter uma sessão de utilizador exclusivamente para um aplicativo, independentemente de outras aplicações. Por exemplo, se pretender que o utilizador iniciar sessão na Contoso farmácia (com as mesmas credenciais), mesmo que o usuário já está conectado a compras de Contoso, outra aplicação no mesmo B2C inquilino. 
- **Política** -esta definição permite-lhe manter uma sessão de utilizador exclusivamente para um fluxo de utilizador, independente das aplicações que utilizam ele. Por exemplo, se o utilizador já iniciou sessão e concluir uma etapa de authentication (MFA) do fator de transmissões, o utilizador pode ser dado acesso a partes de uma maior segurança de vários aplicativos, desde que a sessão associada ao fluxo de utilizador não expire.
- **Desativado** -esta definição força o usuário a executar o fluxo de utilizador completo em cada execução da política.

Estas definições não estão disponíveis para fluxos de utilizador de reposição de palavra-passe. 

