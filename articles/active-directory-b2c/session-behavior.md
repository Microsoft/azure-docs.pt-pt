---
title: Configurar o comportamento de sessão - Azure Active Directory B2C | Documentos da Microsoft
description: Configure o comportamento da sessão no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 1140a3cbb43e86bf222c73c95a03b11871f7a2d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60360443"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Configurar o comportamento da sessão no Azure Active Directory B2C

Esta funcionalidade dá-lhe um controlo detalhado, por um [base de fluxo de por utilizador](active-directory-b2c-reference-policies.md), de:

- Durações das sessões do aplicativo web geridos pelo Azure AD B2C.
- Single sign-on (SSO) do comportamento em várias aplicações e fluxos de utilizador no seu inquilino do Azure AD B2C.

Estas definições não estão disponíveis para fluxos de utilizador de reposição de palavra-passe.

O Azure AD B2C suporta o [protocolo de autenticação OpenID Connect](active-directory-b2c-reference-oidc.md) para ativar a sessão seguro às aplicações web. Pode utilizar as seguintes propriedades para gerir sessões do aplicativo web:

## <a name="session-behavior-properties"></a>Propriedades de comportamento de sessão

- **Aplicação Web duração da sessão (minutos)** - a vida útil do cookie de sessão do Azure AD B2C armazenado no navegador do usuário após a autenticação com êxito.
    - Predefinição = 1440 minutos.
    - Mínimo (inclusive) = 15 minutos.
    - Máximo (inclusive) = 1440 minutos.
- **Tempo limite de sessão de aplicação Web** – se este parâmetro estiver definido como **absoluto**, o utilizador é forçado a autenticar novamente após o período de tempo especificado pelo **aplicação Web duração da sessão (minutos)** decorrida. Se este parâmetro estiver definido como **Rolling** (a predefinição), o usuário permanece com sessão iniciada, desde que o utilizador está continuamente ativo na sua aplicação web.
- **Configuração de início de sessão único** se tiver várias aplicações e fluxos de utilizador no seu inquilino do B2C, pode gerir interações de utilizador por elas com o **configuração de início de sessão único** propriedade. Pode definir a propriedade a uma das seguintes definições:
    - **Inquilino** -esta é a predefinição. Com esta definição permite que vários aplicativos e de utilizador fluxos no seu inquilino de B2C para partilhar a mesma sessão do utilizador. Por exemplo, uma vez que um utilizador inicia sessão numa aplicação, o utilizador pode iniciar diretamente em outro farmácia Contoso um, após a aceder ao mesmo.
    - **Aplicação** -esta definição permite-lhe manter uma sessão de utilizador exclusivamente para um aplicativo, independentemente de outras aplicações. Por exemplo, se pretender que o utilizador iniciar sessão na Contoso farmácia (com as mesmas credenciais), mesmo que o usuário já está conectado a compras de Contoso, outra aplicação no mesmo B2C inquilino. 
    - **Política** -esta definição permite-lhe manter uma sessão de utilizador exclusivamente para um fluxo de utilizador, independente das aplicações que utilizam ele. Por exemplo, se o utilizador já iniciou sessão e concluir uma etapa de authentication (MFA) do fator de transmissões, o utilizador pode ser dado acesso a partes de uma maior segurança de vários aplicativos, desde que a sessão associada ao fluxo de utilizador não expire.
    - **Desativado** -esta definição força o usuário a executar o fluxo de utilizador completo em cada execução da política.

Os seguintes casos de utilização estão ativados com estas propriedades:

- Cumprir os requisitos de segurança e conformidade da sua indústria ao definir a sessão de aplicativo da web apropriados tempos de vida.
- Forçar autenticação após um período de tempo durante a interação do usuário com uma parte de alta segurança da sua aplicação web. 

## <a name="configure-the-properties"></a>Configurar as propriedades

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino do Azure AD B2C.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Selecione **fluxos de utilizador (diretivas)**.
5. Abra o fluxo de utilizador que criou anteriormente. 
6. Selecione **propriedades**.
7. Configurar **aplicação Web duração da sessão (minutos)**, **tempo limite de sessão de aplicação Web**, **configuração de início de sessão único**, e **necessitam de ID de Token em pedidos de fim de sessão**  conforme necessário.

    ![Configurar o comportamento da sessão](./media/session-behavior/session-behavior.png)
    
8. Clique em **Guardar**.



