---
title: Modificar as contas suportadas por uma aplicação registada na plataforma de identidade da Microsoft | Azure
description: Configure uma aplicação registada na plataforma de identidade da Microsoft para alterar quem ou que contas podem aceder à aplicação.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a2c68d607e7afc2e3eac675511734c8d054c427
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60299537"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application-preview"></a>Início rápido: Modificar as contas suportadas por uma aplicação (pré-visualização)

Quando registar uma aplicação na plataforma de identidade da Microsoft, poderá querer que a mesma seja acedida apenas pelos utilizadores da sua organização. Em alternativa, também pode querer que a aplicação seja acedida pelos utilizadores em organizações externas, ou por utilizadores em organizações externas e utilizadores que não fazem necessariamente parte de uma organização (contas pessoais).

Neste início rápido, vai aprender a modificar a configuração da sua aplicação para alterar quem ou que contas podem aceder à mesma.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, certifique-se de que cumpre estes pré-requisitos:

* Saiba mais sobre as [permissões e consentimentos](v2-permissions-and-consent.md) suportados, que é importante compreender se estiver a criar aplicações que têm de ser utilizadas por outros utilizadores ou aplicações.
* Ter um inquilino com aplicações registadas.
  * Se não tiver aplicações registadas, [saiba como registar aplicações na plataforma de identidade da Microsoft](quickstart-register-app.md).
* Inscreva-na experiência de Pré-visualização para registos de aplicações no portal do Azure. Os passos neste início rápido correspondem à nova IU e apenas funcionam se tiver optado pela experiência de Pré-visualização.

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Iniciar sessão no portal do Azure e selecionar a aplicação

Antes de poder configurar a aplicação, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
1. No painel de navegação do lado esquerdo, selecione o serviço **Azure Active Directory** e, em seguida, selecione **Registos de aplicações (Pré-visualização)**.
1. Encontre e selecione a aplicação que quer configurar. Depois de selecionar a aplicação, verá a página **Descrição geral** da aplicação ou a página de registo principal.
1. Siga os passos para [alterar o registo de aplicação para suportar diferentes contas](#change-the-application-registration-to-support-different-accounts).
1. Se tiver uma aplicação de página única, [ative a concessão implícita de OAuth 2.0](#enable-oauth-20-implicit-grant-for-single-page-applications).

## <a name="change-the-application-registration-to-support-different-accounts"></a>Alterar o registo de aplicação para suportar diferentes contas

Se estiver a escrever uma aplicação e pretende disponibilizá-la aos seus clientes ou parceiros fora da sua organização, tem de atualizar a definição da mesma no portal do Azure.

> [!IMPORTANT]
> O Azure AD precisa que o URI do ID da Aplicação das aplicações multi-inquilino seja globalmente exclusivo. O URI do ID da Aplicação é uma das formas através das quais as aplicações são identificadas nas mensagens de protocolo. Relativamente às aplicações de inquilino único, basta que o URI do ID da Aplicação seja exclusivo nesse inquilino. Nas aplicações multi-inquilinos, tem de ser globalmente exclusivo, para que o Azure AD consiga encontrar a aplicação em todos os inquilinos. Para aplicar a exclusividade global, o URI do ID da App tem de ter um nome de anfitrião que corresponda a um domínio verificado do inquilino do Azure AD. Por exemplo, se o nome do inquilino for contoso.onmicrosoft.com, https://contoso.onmicrosoft.com/myapp seria um URI de ID da Aplicação válido. Se o seu inquilino tiver o domínio verificado contoso.com, https://contoso.com/myapp também seria um URI de ID da Aplicação válido. Se o URI não seguir este padrão, a definição da aplicação como multi-inquilinos falha.

### <a name="to-change-who-can-access-your-application"></a>Para alterar quem pode aceder à sua aplicação

1. Na página **Descrição geral** da aplicação, selecione a secção **Autenticação** e altere o valor selecionado em **Tipos de conta suportados**.
    * Selecione **Contas apenas neste diretório** se estiver a criar uma aplicação de linha de negócio (LOB). Esta opção não está disponível se a aplicação não estiver registada num diretório.
    * Selecione **Contas em qualquer diretório organizacional** se quiser visar todos os clientes comerciais ou pedagógicos.
    * Selecione **Contas em qualquer diretório organizacional e contas Microsoft pessoais** para visar o maior conjunto de clientes.
1. Selecione **Guardar**.

## <a name="enable-oauth-20-implicit-grant-for-single-page-applications"></a>Ativar a concessão implícita de OAuth 2.0 para aplicações de página única

Geralmente, as aplicações de página única (SPAs) são estruturadas com um front-end altamente baseado em JavaScript que é executado no browser e que chama o back-end da API Web da aplicação para realizar a respetiva lógica de negócio. Relativamente às SPAs alojadas no Azure AD, é utilizada a Concessão Implícita de OAuth 2.0 para autenticar o utilizador no Azure AD e obter um token que pode ser utilizado para proteger as chamadas do cliente JavaScript da aplicação para a respetiva API Web de back-end.

Depois de o utilizador conceder o consentimento, este mesmo protocolo de autenticação pode ser utilizado para obter tokens para proteger as chamadas entre o cliente e os outros recursos da API Web configurados para a aplicação. Para saber mais sobre a concessão de autorização implícita e decidir se é adequada para o cenário da sua aplicação, obtenha informações sobre o fluxo de concessão implícita de OAuth 2.0 no Azure AD [v1.0](v1-oauth2-implicit-grant-flow.md) e [v2.0](v2-oauth2-implicit-grant-flow.md).

Por predefinição, a concessão implícita de OAuth 2.0 está desativada nas aplicações. Pode ativar a concessão implícita de OAuth 2.0 para a sua aplicação ao seguir os passos descritos abaixo.

### <a name="to-enable-oauth-20-implicit-grant"></a>Para permitir a concessão implícita de OAuth 2.0

1. Na página **Descrição Geral** da aplicação, selecione a secção **Autenticação**.
1. Em **Definições avançadas**, localize a secção **Concessão implícita**.
1. Selecione **Tokens de ID**, **Tokens de acesso** ou ambos.
1. Selecione **Guardar**.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre outros inícios rápidos de gestão de aplicações relacionados:

* [Registar uma aplicação na plataforma de identidade da Microsoft](quickstart-register-app.md)
* [Configurar uma aplicação cliente para aceder a APIs Web](quickstart-configure-app-access-web-apis.md)
* [Configurar uma aplicação para expor APIs Web](quickstart-configure-app-expose-web-apis.md)
* [Remover uma aplicação registada na plataforma de identidade da Microsoft](quickstart-remove-app.md)

Para saber mais sobre os dois objetos do Azure AD que representam uma aplicação registada e o relacionamento entre os mesmos, veja [Application objects and service principal objects](app-objects-and-service-principals.md) (Objetos da aplicação e objetos do principal de serviço).

Para saber mais sobre as diretrizes de imagem corporativa que deve seguir quando desenvolver aplicações com o Azure Active Directory, veja [Diretrizes de imagem corporativa para aplicações](howto-add-branding-in-azure-ad-apps.md).
