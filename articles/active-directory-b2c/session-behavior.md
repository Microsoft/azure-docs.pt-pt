---
title: Configure comportamento de sessão - Diretório Ativo Azure B2C / Microsoft Docs
description: Configure o comportamento da sessão no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c1d39fdbca9484f47ce0c8537c82247b75b2e3db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78186816"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Configure comportamento de sessão no Diretório Ativo Azure B2C

Esta funcionalidade confere-lhe um controlo fino, numa [base de fluxo por utilizador,](user-flow-overview.md)de:

- Vida útil de sessões de aplicação web geridas por Azure AD B2C.
- Comportamento de entrada única (SSO) em várias aplicações e fluxos de utilizadores no seu inquilino Azure AD B2C.

Estas definições não estão disponíveis para redefinir os fluxos de utilizador da palavra-passe.

O Azure AD B2C suporta o protocolo de [autenticação OpenID Connect](openid-connect.md) para permitir o acesso seguro às aplicações web. Pode utilizar as seguintes propriedades para gerir sessões de aplicação web:

## <a name="session-behavior-properties"></a>Propriedades do comportamento da sessão

- Vida útil da sessão da **aplicação web (minutos)** - A vida útil do cookie de sessão do Azure AD B2C armazenado no navegador do utilizador após a autenticação bem sucedida.
    - Predefinição = 1440 minutos.
    - Mínimo (inclusivo) = 15 minutos.
    - Máximo (inclusivo) = 1440 minutos.
- Tempo limite de sessão de **aplicações web** - Se este interruptor estiver definido para **Absolute,** o utilizador é obrigado a autenticar novamente após o período de tempo especificado pela sessão de **aplicação web (minutos)** decorrido. Se este interruptor estiver definido para **A Rolling** (a definição predefinida), o utilizador permanece inscrito enquanto o utilizador estiver continuamente ativo na sua aplicação web.
- **Configuração de inscrição única** Se tiver várias aplicações e fluxos de utilizadores no seu inquilino B2C, pode gerir as interações do utilizador através delas utilizando a propriedade **de configuração de entrada individual.** Pode definir a propriedade para uma das seguintes definições:
    - **Inquilino** - Esta definição é o padrão. A utilização desta definição permite que várias aplicações e fluxos de utilizadores no seu inquilino B2C partilhem a mesma sessão de utilizador. Por exemplo, uma vez que um utilizador assina numa aplicação, o utilizador também pode iniciar sem problemas outra, a Farmácia Contoso, ao aceder à sua.
    - **Aplicação** - Esta definição permite manter uma sessão de utilizador exclusivamente para uma aplicação, independente de outras aplicações. Por exemplo, se quiser que o utilizador instituna a Farmácia Contoso (com as mesmas credenciais), mesmo que o utilizador já esteja inscrito no Contoso Shopping, outra aplicação no mesmo inquilino B2C.
    - **Política** - Esta definição permite manter uma sessão de utilizador exclusivamente para um fluxo de utilizador, independentemente das aplicações que a utilizam. Por exemplo, se o utilizador já tiver assinado e concluído uma etapa de autenticação multifactor (MFA), o utilizador pode ter acesso a partes de maior segurança de várias aplicações, desde que a sessão ligada ao fluxo do utilizador não expire.
    - **Desativado** - Esta definição obriga o utilizador a executar todo o fluxo do utilizador em cada execução da apólice.

Os seguintes casos de utilização são ativados utilizando estas propriedades:

- Cumpra os requisitos de segurança e conformidade da sua indústria, definindo as vidas de sessão de aplicação web apropriadas.
- Forçar a autenticação após um período de tempo definido durante a interação de um utilizador com uma parte de alta segurança da sua aplicação web.

## <a name="configure-the-properties"></a>Configure as propriedades

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino Azure AD B2C.
3. Escolha **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
4. Selecione **fluxos de utilizador (políticas)**.
5. Abra o fluxo de utilizador que criou anteriormente.
6. Selecione **Propriedades**.
7. Configure o tempo de vida da sessão da **web (minutos)**, tempo limite de sessão de **aplicações web**, **configuração de início de sessão individual**e exija ID Token em pedidos de **logout** conforme necessário.

    ![Configurações de propriedade de comportamento de sessão no portal Azure](./media/session-behavior/session-behavior.png)

8. Clique em **Guardar**.
