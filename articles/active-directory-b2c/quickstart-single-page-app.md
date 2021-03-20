---
title: 'Quickstart: Configurar o início para uma aplicação de uma página (SPA)'
titleSuffix: Azure AD B2C
description: Neste Quickstart, execute uma aplicação de uma página única que utiliza o Azure Ative Directory B2C para fornecer o início de sedutar da conta.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6471d1b5a5ad2b8ba34080ae1220872fa0e2e232
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421061"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Quickstart: Inicie o súbbréste para uma aplicação de uma página usando o Azure Ative Directory B2C

O Azure Ative Directory B2C (Azure AD B2C) fornece gestão de identidade em nuvem para manter a sua aplicação, negócios e clientes protegidos. O Azure AD B2C permite às aplicações fazer a autenticação em contas de redes sociais e contas empresariais, através de protocolos padrão abertos. Neste início rápido, vai utilizar uma aplicação de página única para iniciar sessão com um fornecedor de identidade de redes sociais e chamar uma API Web protegida pelo Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio Code](https://code.visualstudio.com/)
- [Node.js](https://nodejs.org/en/download/)
- Conta social do Facebook, Google ou Microsoft
- Amostra de código do GitHub: [ms-identidade-b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa)

    Pode [descarregar o arquivo zip](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip) ou clonar o repositório:

    ```console
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```

## <a name="run-the-application"></a>Executar a aplicação

1. Inicie o servidor ao executar os seguintes comandos na linha de comandos do Node.js:

    ```console
    npm install && npm update
    npm start
    ```

    O servidor iniciado por *server.js* exibe a porta que está a ouvir:

    ```console
    Listening on port 6420...
    ```

1. Navegue para o URL da aplicação. Por exemplo, `http://localhost:6420`.

    ![App de amostra de aplicação de uma página mostrada no navegador](./media/quickstart-single-page-app/sample-app-spa.png)

## <a name="sign-in-using-your-account"></a>Iniciar sessão com a sua conta

1. Selecione **Iniciar s-se-in** para iniciar a jornada do utilizador.
1. A Azure AD B2C apresenta uma página de inscrição para uma empresa fictícia chamada "Fabrikam" para a aplicação web de amostras. Para se inscrever usando um fornecedor de identidade social, selecione o botão do fornecedor de identidade que pretende utilizar.

    ![Iniciar sposição ou iniciar sposição na página que mostra os botões do fornecedor de identidade](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    Autentica (iniciar sação) utilizando as suas credenciais de conta social e autoriza o pedido de leitura de informação a partir da sua conta social. Ao conceder acesso, a aplicação pode obter as informações do perfil da conta de rede social, como o nome e a localidade.

1. Conclua o processo de início de sessão para o fornecedor de identidade.

## <a name="access-a-protected-api-resource"></a>Aceder a um recurso protegido da API

Selecione **Call API** para ter o seu nome de exibição devolvido da API web como um objeto JSON.

![Aplicação de amostra no navegador mostrando a resposta da API web](./media/quickstart-single-page-app/call-api-spa.png)

A aplicação de página única de exemplo inclui um token de acesso no pedido ao recurso protegido da API Web.

## <a name="clean-up-resources"></a>Limpar os recursos

Pode utilizar o inquilino do Azure AD B2C se planeia experimentar outros inícios rápidos ou tutoriais do Azure AD B2C. Quando já não for necessário, pode [eliminar o inquilino do Azure AD B2C](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, utilizou uma aplicação de uma página única para:

- Inscreva-se com um provedor de identidade social
- Criar uma conta de utilizador AZURE AD B2C (criada automaticamente no início)
- Chame uma API web protegida por Azure AD B2C

Comece a criar o seu próprio inquilino do Azure AD B2C.

> [!div class="nextstepaction"]
> [Criar um inquilino do Azure Active Directory B2C no portal do Azure](tutorial-create-tenant.md)
