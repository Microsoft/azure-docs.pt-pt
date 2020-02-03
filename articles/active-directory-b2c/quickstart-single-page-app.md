---
title: 'Quickstart: Configurar o início para uma aplicação de uma única página (SPA)'
titleSuffix: Azure AD B2C
description: Neste Quickstart, execute uma aplicação de uma página única que utilize o Azure Ative Directory B2C para fornecer o início de sessão de conta.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 98b4e7e6b64d68d98597c40c6aea3d6cfe104be0
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76850147"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Início Rápido: Configurar o início de sessão para uma aplicação de uma página através do Azure Active Directory B2C

O Azure Active Directory B2C (Azure AD B2C) fornece gerenciamento de identidade de nuvem para manter seus aplicativos, negócios e clientes protegidos. O Azure AD B2C permite às aplicações fazer a autenticação em contas de redes sociais e contas empresariais, através de protocolos padrão abertos. Neste início rápido, vai utilizar uma aplicação de página única para iniciar sessão com um fornecedor de identidade de redes sociais e chamar uma API Web protegida pelo Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) com a **carga de trabalho de ASP.NET e desenvolvimento web**
- [Node.js](https://nodejs.org/en/download/)
- Conta social do Facebook, Google ou Microsoft
- Amostra de código do GitHub: [active-directy-b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)

    Pode [baixar o arquivo zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clonar o repositório:

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>Executar a aplicação

1. Inicie o servidor ao executar os seguintes comandos na linha de comandos do Node.js:

    ```
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    node server.js
    ```

    O Server.js produz o número de porta na qual está a escutar no localhost.

    ```
    Listening on port 6420...
    ```

2. Navegue para o URL da aplicação. Por exemplo, `http://localhost:6420`.

## <a name="sign-in-using-your-account"></a>Inicie sessão com a sua conta

1. Clique em **Iniciar sessão** para iniciar o fluxo de trabalho.

    ![App de amostra de aplicação de uma página mostrada no navegador](./media/quickstart-single-page-app/sample-app-spa.png)

    O exemplo suporta várias opções de inscrição, incluindo através de um fornecedor de identidade de redes sociais ou criando uma conta local com um endereço de e-mail. Para este guia de início rápido, use uma conta de provedor de identidade social do Facebook, do Google ou da Microsoft.

2. Azure AD B2C apresenta uma página de entrada para uma empresa fictícia chamada Fabrikam para o aplicativo Web de exemplo. Para inscrever-se através de um fornecedor de identidade de redes sociais, clique no botão do fornecedor de identidade que pretende utilizar.

    ![Inscreva-se ou inscreva-se na página mostrando botões de fornecedor de identidade](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    Você autentica (entra) usando suas credenciais de conta social e autoriza o aplicativo a ler informações de sua conta social. Ao conceder acesso, a aplicação pode obter as informações do perfil da conta de rede social, como o nome e a localidade.

3. Conclua o processo de início de sessão para o fornecedor de identidade.

## <a name="access-a-protected-api-resource"></a>Aceder a um recurso protegido da API

Clique em **Chamar API Web** para que o nome a apresentar seja devolvido na chamada da API Web como um objeto JSON.

![Aplicação de amostra no navegador mostrando a resposta web API](./media/quickstart-single-page-app/call-api-spa.png)

A aplicação de página única de exemplo inclui um token de acesso no pedido ao recurso protegido da API Web.

## <a name="clean-up-resources"></a>Limpar recursos

Pode utilizar o inquilino do Azure AD B2C se planeia experimentar outros inícios rápidos ou tutoriais do Azure AD B2C. Quando já não for necessário, pode [eliminar o inquilino do Azure AD B2C](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, utilizou uma aplicação de uma página única para:

* Entrar com uma página de logon personalizada
* Entrar com um provedor de identidade social
* Criar uma conta de Azure AD B2C
* Chamar uma API da Web protegida por Azure AD B2C

Comece a criar o seu próprio inquilino do Azure AD B2C.

> [!div class="nextstepaction"]
> [Criar um inquilino do Azure Active Directory B2C no portal do Azure](tutorial-create-tenant.md)
