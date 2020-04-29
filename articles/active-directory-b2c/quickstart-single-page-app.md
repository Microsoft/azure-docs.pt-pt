---
title: 'Quickstart: Configurar o início para uma aplicação de uma única página (SPA)'
titleSuffix: Azure AD B2C
description: Neste Quickstart, execute uma aplicação de uma página única que utilize o Azure Ative Directory B2C para fornecer o início de sessão de conta.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0a754873aeafe8d4e7b48d49647469874ff40f7e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80875892"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Início Rápido: Configurar o início de sessão para uma aplicação de uma página através do Azure Active Directory B2C

O Azure Ative Directory B2C (Azure AD B2C) fornece gestão de identidade na nuvem para manter a sua aplicação, negócio e clientes protegidos. O Azure AD B2C permite às aplicações fazer a autenticação em contas de redes sociais e contas empresariais, através de protocolos padrão abertos. Neste início rápido, vai utilizar uma aplicação de página única para iniciar sessão com um fornecedor de identidade de redes sociais e chamar uma API Web protegida pelo Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio Code](https://code.visualstudio.com/)
- [Node.js](https://nodejs.org/en/download/)
- Conta social do Facebook, Google ou Microsoft
- Amostra de código do GitHub: [active-directy-b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

    Pode [baixar o arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clonar o repositório:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>Executar a aplicação

1. Inicie o servidor ao executar os seguintes comandos na linha de comandos do Node.js:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    npm start
    ```

    O servidor iniciado pelo *servidor.js* exibe a porta em que está a ouvir:

    ```console
    Listening on port 6420...
    ```

1. Navegue para o URL da aplicação. Por exemplo, `http://localhost:6420`.

    ![App de amostra de aplicação de uma página mostrada no navegador](./media/quickstart-single-page-app/sample-app-spa.png)

## <a name="sign-in-using-your-account"></a>Iniciar sessão com a sua conta

1. Selecione Iniciar o **'Iniciar'** a viagem do utilizador.
1. O Azure AD B2C apresenta uma página de entrada para uma empresa fictícia chamada Fabrikam para a aplicação web da amostra. Para se inscrever usando um fornecedor de identidade social, selecione o botão do fornecedor de identidade que pretende utilizar.

    ![Inscreva-se ou inscreva-se na página mostrando botões de fornecedor de identidade](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    Autentica (iniciar sessão) usando as credenciais da sua conta social e autorizar o pedido de leitura de informação na sua conta social. Ao conceder acesso, a aplicação pode obter as informações do perfil da conta de rede social, como o nome e a localidade.

1. Conclua o processo de início de sessão para o fornecedor de identidade.

## <a name="access-a-protected-api-resource"></a>Aceder a um recurso protegido da API

Selecione **Call API** para que o seu nome de exibição seja devolvido da Web API como um objeto JSON.

![Aplicação de amostra no navegador mostrando a resposta web API](./media/quickstart-single-page-app/call-api-spa.png)

A aplicação de página única de exemplo inclui um token de acesso no pedido ao recurso protegido da API Web.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, utilizou uma aplicação de uma página única para:

- Inscreva-se com um fornecedor de identidade social
- Criar uma conta de utilizador Azure AD B2C (criada automaticamente no início do sessão)
- Ligue para uma Web API protegida por Azure AD B2C

Comece a criar o seu próprio inquilino do Azure AD B2C.

> [!div class="nextstepaction"]
> [Criar um inquilino do Azure Active Directory B2C no portal do Azure](tutorial-create-tenant.md)
