---
title: 'Tutorial: Ativar a autenticação numa aplicação de uma só página'
titleSuffix: Azure AD B2C
description: Neste tutorial, aprenda a utilizar o Azure Ative Directory B2C para fornecer login ao utilizador para uma aplicação de página única baseada no JavaScript (SPA).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: d7cd437f597fc34fe83904715fc2e459dfe4550f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80875579"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Tutorial: Permitir a autenticação numa aplicação de uma página com o Azure AD B2C

Este tutorial mostra-lhe como utilizar o Azure Ative Directory B2C (Azure AD B2C) para se inscrever e inscrever os utilizadores numa aplicação de uma única página (SPA).

Neste tutorial, o primeiro de uma série de duas partes:

> [!div class="checklist"]
> * Adicione um URL de resposta a uma aplicação registada no seu inquilino Azure AD B2C
> * Descarregue uma amostra de código do GitHub
> * Modificar o código da aplicação da amostra para trabalhar com o seu inquilino
> * Inscreva-se utilizando o seu fluxo de utilizador de inscrição/entrada

O [próximo tutorial](tutorial-single-page-app-webapi.md) da série permite a parte web da API da amostra de código.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes recursos Azure AD B2C em vigor antes de continuar com os passos neste tutorial:

* [Inquilino Azure AD B2C](tutorial-create-tenant.md)
* [Pedido registado](tutorial-register-applications.md) no seu inquilino
* [Fluxos de utilizador criados](tutorial-create-user-flows.md) no seu inquilino

Além disso, precisa do seguinte no seu ambiente de desenvolvimento local:

* [Código de Estúdio Visual](https://code.visualstudio.com/) ou outro editor de código
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Atualizar a aplicação

No segundo tutorial que completou como parte dos pré-requisitos, registou uma aplicação web no Azure AD B2C. Para permitir a comunicação com a amostra de código neste tutorial, adicione um URL de resposta (também chamado de URI redirecionado) ao registo da aplicação.

Pode utilizar a experiência atual de **Aplicações** ou a nossa nova experiência unificada de registos de **Aplicações (Pré-visualização)** para atualizar a aplicação. [Saiba mais sobre a nova experiência.](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Selecione **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **Aplicações**e, em seguida, selecione a aplicação *webapp1.*
1. Under **Answer URL,** add `http://localhost:6420`.
1. Selecione **Guardar**.
1. Na página de propriedades, grave o ID da **Aplicação**. Utiliza o ID da aplicação num passo posterior quando atualiza o código na aplicação web de uma página única.

#### <a name="app-registrations-preview"></a>[Registos de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No menu esquerdo, **selecione Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione registos de **aplicações (Pré-visualização)**- selecione o separador **de aplicações Owned** e, em seguida, selecione a aplicação *webapp1.*
1. **Selecione Autenticação**e, em seguida, selecione **Experimente a nova experiência** (se mostrada).
1. Na **Web,** selecione a `http://localhost:6420`ligação Adicionar **URI,** introduza e, em seguida, selecione **Guardar**.
1. Selecione **Descrição geral**.
1. Grave o ID da **Aplicação (cliente)** para utilização num passo posterior quando atualizar o código na aplicação web de página única.

* * *

## <a name="get-the-sample-code"></a>Obter o código de exemplo

Neste tutorial, configura uma amostra de código que descarrega do GitHub para trabalhar com o seu inquilino B2C. A amostra demonstra como uma aplicação de uma página única pode usar O Azure AD B2C para iniciar sessão e inscrição no utilizador, e para chamar uma API web protegida (ativar a Web API no próximo tutorial da série).

[Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clone o exemplo a partir do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Atualizar a amostra

Agora que obteve a amostra, atualize o código com o nome do seu inquilino Azure AD B2C e o ID de aplicação que gravou num passo anterior.

1. Abra o ficheiro *authConfig.js* dentro da pasta *JavaScriptSPA.*
1. No `msalConfig` objeto, atualização:
    * `clientId`com valor com o ID de **Aplicação (cliente)** que registou num passo anterior
    * `authority`URI com o seu nome de inquilino Azure AD B2C e o nome do fluxo de utilizador de inscrição/inscrição que criou como parte dos pré-requisitos (por exemplo, *B2C_1_signupsignin1)*

    ```javascript
    const msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", // Replace this value with your Application (client) ID
            authority: "https://your-b2c-tenant.b2clogin.com/your-b2c-tenant.onmicrosoft.com/B2C_1_signupsignin1", // Update with your tenant and user flow names
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

## <a name="run-the-sample"></a>Executar o exemplo

1. Abra uma janela da consola e mude para o diretório que contém a amostra. Por exemplo:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Execute os seguintes comandos:

    ```console
    npm install && npm update
    npm start
    ```

    A janela da consola apresenta o número de porta do servidor Node.js de execução local:

    ```console
    Listening on port 6420...
    ```
1. Navegue `http://localhost:6420` para ver a aplicação web em execução na sua máquina local.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="Navegador web mostrando aplicação de uma página única executando localmente":::

### <a name="sign-up-using-an-email-address"></a>Inscrever-se com um endereço de e-mail

Esta aplicação de amostra suporta inscrever-se, iniciar sessão e repor a palavra-passe. Neste tutorial, inscreva-se usando um endereço de e-mail.

1. Selecione **Iniciar** o *B2C_1_signupsignin1* fluxo de utilizador especificado num passo anterior.
1. Azure AD B2C apresenta uma página de entrada que inclui um link de inscrição. Uma vez que ainda não tem uma conta, selecione o link **'Iniciar' agora.**
1. O fluxo de trabalho de inscrição apresenta uma página para recolher e verificar a identidade do utilizador usando um endereço de e-mail. O fluxo de trabalho de inscrição também recolhe a palavra-passe do utilizador e os atributos solicitados definidos no fluxo do utilizador.

    Utilize um endereço de e-mail válido e valide com o código de verificação. Defina uma palavra-passe. Introduza os valores para os atributos solicitados.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Registe a página exibida pelo fluxo de utilizador Do Azure AD B2C":::

1. Selecione **Criar** para criar uma conta local no diretório Azure AD B2C.

Quando seleciona **Criar,** a aplicação mostra o nome do utilizador assinado.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="Navegador web mostrando aplicação de uma página com registrado no utilizador":::

Se quiser testar o 'iniciar' o 'sessão', selecione o botão **'Sign Out',** então selecione **Iniciar** sessão e iniciar sessão com o endereço de e-mail e a palavra-passe que inseriu quando se inscreveu.

### <a name="what-about-calling-the-api"></a>E quanto a chamar a API?

Se selecionar o botão **Call API** após a sessão, é-lhe apresentada a página de fluxo de utilizador de entrada/inscrição em vez dos resultados da chamada API. Isto é esperado porque ainda não configuraa a parte DaPi da aplicação para comunicar com uma aplicação Web API registada no *seu* inquilino Azure AD AD B2C.

Neste momento, o pedido ainda está a tentar comunicar com a API registada na demonstração de inquilino (fabrikamb2c.onmicrosoft.com), e como não foi autenticado com esse inquilino, a página de inscrição/inscrição é exibida.

Passe para o próximo tutorial da série para ativar a API protegida (ver a secção [de passos seguintes).](#next-steps)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou uma aplicação de uma página única para trabalhar com um fluxo de utilizador no seu inquilino Azure AD AD B2C para fornecer inscrição e iniciar sessão. Completou estes passos:

> [!div class="checklist"]
> * Adicionou um URL de resposta a um pedido registado no seu inquilino Azure AD B2C
> * Descarreguei uma amostra de código do GitHub
> * Modificou o código da aplicação da amostra para trabalhar com o seu inquilino
> * Inscreveu-se usando o seu fluxo de utilizador de inscrição/entrada

Agora passe para o próximo tutorial da série para dar acesso a uma API web protegida do SPA:

> [!div class="nextstepaction"]
> [Tutorial: Proteja e conceda acesso à API web a partir de uma aplicação de uma única página >](tutorial-single-page-app-webapi.md)
