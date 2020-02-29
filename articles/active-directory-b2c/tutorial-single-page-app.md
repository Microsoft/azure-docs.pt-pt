---
title: 'Tutorial: Ativar a autenticação numa aplicação de uma só página'
titleSuffix: Azure AD B2C
description: Neste tutorial, aprenda a utilizar o Azure Ative Directory B2C para fornecer login ao utilizador para uma aplicação de página única baseada no JavaScript (SPA).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 435800d9c6bfd9131d50681a9808f9836104fac0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183351"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c-azure-ad-b2c"></a>Tutorial: Permitir a autenticação numa aplicação de uma só página utilizando o Diretório Ativo Azure B2C (Azure AD B2C)

Este tutorial mostra-lhe como utilizar o Azure Ative Directory B2C (Azure AD B2C) para iniciar sessão e inscrever os utilizadores numa aplicação de uma única página (SPA). O Azure AD B2C permite que as suas aplicações se autentiquem a contas sociais, contas empresariais e contas de Diretório Ativo Azure utilizando protocolos padrão abertos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Atualizar a aplicação em Azure AD B2C
> * Configure a amostra para utilizar a aplicação
> * Inscreva-se utilizando o fluxo do utilizador

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes recursos Azure AD B2C em vigor antes de continuar com os passos neste tutorial:

* [Inquilino Azure AD B2C](tutorial-create-tenant.md)
* [Pedido registado](tutorial-register-applications.md) no seu inquilino
* [Fluxos de utilizador criados](tutorial-create-user-flows.md) no seu inquilino

Além disso, precisa do seguinte no seu ambiente de desenvolvimento local:

* Editor de código, por exemplo [Visual Studio Code](https://code.visualstudio.com/) ou Visual Studio [2019](https://www.visualstudio.com/downloads/)
* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download) ou posterior
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Atualizar a aplicação

No segundo tutorial que completou como parte dos pré-requisitos, registou uma aplicação web no Azure AD B2C. Para permitir a comunicação com a amostra no tutorial, é necessário adicionar um URI redirecionado à aplicação em Azure AD B2C.

Pode utilizar a experiência atual de **Aplicações** ou a nossa nova experiência unificada de registos de **Aplicações (Pré-visualização)** para atualizar a aplicação. [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a usar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro de **subscrição Do Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Selecione **todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **Aplicações**e, em seguida, selecione a aplicação *webapp1.*
1. No URL **de resposta,** adicione `http://localhost:6420`.
1. Selecione **Guardar**.
1. Na página de propriedades, grave o ID da **Aplicação**. Utiliza o ID da aplicação num passo posterior quando atualiza o código na aplicação web de uma página única.

#### <a name="app-registrations-preview"></a>[Registos de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No menu esquerdo, **selecione Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione registos de **aplicações (Pré-visualização)** - selecione o separador **de aplicações Owned** e, em seguida, selecione a aplicação *webapp1.*
1. **Selecione Autenticação**e, em seguida, selecione **Experimente a nova experiência** (se mostrada).
1. No **Web,** selecione a ligação **Adicionar URI,** introduza `http://localhost:6420`e, em seguida, selecione **Guardar**.
1. Selecione **Descrição geral**.
1. Grave o ID da **Aplicação (cliente)** para utilização num passo posterior quando atualizar o código na aplicação web de página única.

* * *

## <a name="get-the-sample-code"></a>Obter o código de exemplo

Neste tutorial, configura uma amostra de código que descarrega do GitHub. A amostra demonstra como uma aplicação de uma única página pode usar O Azure AD B2C para iniciar sessão e inscrição no utilizador e para chamar uma API web protegida.

[Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clone o exemplo a partir do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Atualizar a amostra

Agora que obteve a amostra, atualize o código com o nome do seu inquilino Azure AD B2C e o ID de aplicação que gravou num passo anterior.

1. Abra o ficheiro `index.html` na raiz do diretório da amostra.
1. Na definição `msalConfig`, modifique o valor **do clienteId** com o ID de aplicação que registou num passo anterior. Em seguida, atualize o valor URI da **autoridade** com o seu nome de inquilino Azure AD B2C. Atualize também o URI com o nome do fluxo de utilizador de inscrição/inscrição que criou num dos pré-requisitos (por exemplo, *B2C_1_signupsignin1*).

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "00000000-0000-0000-0000-000000000000", //This is your client ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    O nome do fluxo de utilizador utilizado neste tutorial é **B2C_1_signupsignin1**. Se estiver a utilizar um nome de fluxo de utilizador diferente, especifique esse nome no valor `authority`.

## <a name="run-the-sample"></a>Executar o exemplo

1. Abra uma janela da consola e mude para o diretório que contém a amostra. Por exemplo:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Execute os seguintes comandos:

    ```
    npm install && npm update
    node server.js
    ```

    A janela da consola apresenta o número de porta do servidor Node.js de execução local:

    ```
    Listening on port 6420...
    ```

1. Vá a `http://localhost:6420` no seu navegador para ver a aplicação.

A amostra suporta o início de inscrição, inscrição, edição de perfil e reset de palavra-passe. Este tutorial destaca como um utilizador se inscreve usando um endereço de e-mail.

### <a name="sign-up-using-an-email-address"></a>Inscrever-se com um endereço de e-mail

> [!WARNING]
> Após a inscrição ou inscrição, poderá ver um [erro de permissões insuficiente](#error-insufficient-permissions). Devido à implementação atual da amostra de código, espera-se este erro. Esta questão será resolvida numa futura versão da amostra de código, altura em que este aviso será removido.

1. Selecione **O Login** para iniciar o fluxo *de utilizador B2C_1_signupsignin1* especificado num passo anterior.
1. O Azure AD B2C apresenta uma página de início de sessão com uma ligação de inscrição. Uma vez que ainda não tem uma conta, selecione o link **'Iniciar' agora.**
1. O fluxo de trabalho de inscrição apresenta uma página para recolher e verificar a identidade do utilizador através de um endereço de e-mail. O fluxo de trabalho de inscrição também recolhe a palavra-passe do utilizador e os atributos solicitados definidos no fluxo do utilizador.

    Utilize um endereço de e-mail válido e valide com o código de verificação. Defina uma palavra-passe. Introduza os valores para os atributos solicitados.

    ![Página de inscrição apresentada pelo fluxo de utilizador de entrada/inscrição](./media/tutorial-single-page-app/azure-ad-b2c-sign-up-workflow.png)

1. Selecione **Criar** para criar uma conta local no diretório Azure AD B2C.

Quando selecionar **Criar,** a página de inscrição fecha e o sinal na página reaparece.

Pode agora utilizar o seu endereço de e-mail e senha para iniciar sessão na aplicação.

### <a name="error-insufficient-permissions"></a>Erro: permissões insuficientes

Depois de iniciar sessão, o pedido pode devolver um erro de permissões insuficiente:

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

Você recebe este erro porque a aplicação web está tentando aceder a uma API web protegida pelo diretório de demonstração, *fabrikamb2c*. Como o seu sinal de acesso é válido apenas para o seu diretório Azure AD, a chamada da API não é autorizada.

Para corrigir este erro, continue para o próximo tutorial da série (ver [Passos Seguintes)](#next-steps)para criar uma API web protegida para o seu diretório.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a:

> [!div class="checklist"]
> * Atualizar a aplicação em Azure AD B2C
> * Configure a amostra para utilizar a aplicação
> * Inscreva-se utilizando o fluxo do utilizador

Agora passe para o próximo tutorial da série para dar acesso a uma API web protegida do SPA:

> [!div class="nextstepaction"]
> [Tutorial: Conceder acesso a um ASP.NET Core web API de um SPA utilizando Azure AD B2C >](tutorial-single-page-app-webapi.md)
