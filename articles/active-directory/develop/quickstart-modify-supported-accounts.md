---
title: 'Quickstart: Modificar as contas de aplicações da plataforma da Microsoft Rio Azure'
description: Neste arranque rápido, configura uma aplicação registada na plataforma de identidade da Microsoft para alterar quem, ou que contas, pode aceder à aplicação.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: d143bde9c22bc726f00b5c209d1b7fbc131905b0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258018"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application"></a>Quickstart: Modificar as contas suportadas por uma aplicação

Quando registar uma aplicação na plataforma de identidade da Microsoft, poderá querer que a mesma seja acedida apenas pelos utilizadores da sua organização. Em alternativa, também pode querer que a aplicação seja acedida pelos utilizadores em organizações externas, ou por utilizadores em organizações externas e utilizadores que não fazem necessariamente parte de uma organização (contas pessoais).

Neste início rápido, vai aprender a modificar a configuração da sua aplicação para alterar quem ou que contas podem aceder à mesma.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do [Quickstart: Registar uma aplicação com a plataforma de identidade microsoft](quickstart-register-app.md)

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Iniciar sessão no portal do Azure e selecionar a aplicação

Antes de poder configurar a aplicação, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure** e, em seguida, selecione **registos de Aplicações**.
1. Encontre e selecione a aplicação que quer configurar. Depois de selecionar a aplicação, verá a página **Descrição Geral** da aplicação ou a página de registo principal.
1. Siga os passos para [alterar o registo de aplicação para suportar diferentes contas](#change-the-application-registration-to-support-different-accounts).
1. Se tiver uma aplicação de página única, [ative a concessão implícita de OAuth 2.0](#enable-oauth-20-implicit-grant-for-single-page-applications).

## <a name="change-the-application-registration-to-support-different-accounts"></a>Alterar o registo de aplicação para suportar diferentes contas

Se estiver a escrever uma aplicação e pretende disponibilizá-la aos seus clientes ou parceiros fora da sua organização, tem de atualizar a definição da mesma no portal do Azure.

> [!IMPORTANT]
> O Azure AD precisa que o URI do ID da Aplicação das aplicações multi-inquilino seja globalmente exclusivo. O URI do ID da Aplicação é uma das formas através das quais as aplicações são identificadas nas mensagens de protocolo. Relativamente às aplicações de inquilino único, basta que o URI do ID da Aplicação seja exclusivo nesse inquilino. Nas aplicações multi-inquilinos, tem de ser globalmente exclusivo, para que o Azure AD consiga encontrar a aplicação em todos os inquilinos. Para aplicar a exclusividade global, o URI do ID da App tem de ter um nome de anfitrião que corresponda a um domínio verificado do inquilino do Azure AD. Por exemplo, se o nome do inquilino for contoso.onmicrosoft.com, `https://contoso.onmicrosoft.com/myapp` seria um URI de ID da Aplicação válido. Se o seu inquilino tiver o domínio verificado contoso.com, `https://contoso.com/myapp` também seria um URI de ID da Aplicação válido. Se o URI não seguir este padrão, a definição da aplicação como multi-inquilinos falha.

### <a name="to-change-who-can-access-your-application"></a>Para alterar quem pode aceder à sua aplicação

1. Na página **Descrição geral** da aplicação, selecione a secção **Autenticação** e altere o valor selecionado em **Tipos de conta suportados**.
    * Selecione **Contas apenas neste diretório** se estiver a criar uma aplicação de linha de negócio (LOB). Esta opção não está disponível se a aplicação não estiver registada num diretório.
    * Selecione **Contas em qualquer diretório organizacional** se quiser visar todos os clientes comerciais ou pedagógicos.
    * Selecione **Contas em qualquer diretório organizacional e contas Microsoft pessoais** para visar o maior conjunto de clientes.
1. Selecione **Guardar**.

## <a name="enable-oauth-20-implicit-grant-for-single-page-applications"></a>Ativar a concessão implícita de OAuth 2.0 para aplicações de página única

Geralmente, as aplicações de página única (SPAs) são estruturadas com um front-end altamente baseado em JavaScript que é executado no browser e que chama o back-end da API Web da aplicação para realizar a respetiva lógica de negócio. Relativamente às SPAs alojadas no Azure AD, é utilizada a Concessão Implícita de OAuth 2.0 para autenticar o utilizador no Azure AD e obter um token que pode ser utilizado para proteger as chamadas do cliente JavaScript da aplicação para a respetiva API Web de back-end.

Depois de o utilizador conceder o consentimento, este mesmo protocolo de autenticação pode ser utilizado para obter tokens para proteger as chamadas entre o cliente e os outros recursos da API Web configurados para a aplicação. Para saber mais sobre a concessão de autorização implícita e decidir se é adequada para o cenário da sua aplicação, obtenha informações sobre o fluxo de concessão implícita de OAuth 2.0 no Azure AD [v1.0](../azuread-dev/v1-oauth2-implicit-grant-flow.md) e [v2.0](v2-oauth2-implicit-grant-flow.md).

Por predefinição, a concessão implícita de OAuth 2.0 está desativada nas aplicações. Pode ativar a concessão implícita de OAuth 2.0 para a sua aplicação ao seguir os passos descritos abaixo.

### <a name="to-enable-oauth-20-implicit-grant"></a>Para permitir a concessão implícita de OAuth 2.0

1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure** e, em seguida, selecione **registos de Aplicações**.
1. Encontre e selecione a aplicação que quer configurar. Depois de selecionar a aplicação, verá a página **Descrição Geral** da aplicação ou a página de registo principal.
1. Na página **Descrição geral** da aplicação, selecione a secção **Autenticação**.
1. Em **Definições avançadas**, localize a secção **Concessão implícita**.
1. Selecione **Tokens de ID**, **Tokens de acesso** ou ambos.
1. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Diretrizes de imagem corporativa para aplicações](howto-add-branding-in-azure-ad-apps.md)
