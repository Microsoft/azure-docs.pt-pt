---
title: 'Tutorial: Conceder acesso a uma API web Node.js a partir de uma aplicação de ambiente de trabalho'
description: Tutorial sobre como usar o Ative Directory B2C para proteger uma API web Node.js e chamá-lo de uma aplicação de desktop .NET.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 12951f25feb6f3710b8d35fbc682caeeb480e788
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555614"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Tutorial: Conceder acesso a uma API Web Node.js a partir de uma aplicação de ambiente de trabalho com o Azure Active Directory B2C

Este tutorial mostra-lhe como chamar uma API web Node.js protegida pelo Azure Ative Directory B2C (Azure AD B2C) de uma aplicação de desktop da Windows Presentation Foundation (WPF), também protegida pelo Azure AD B2C.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar âmbitos para uma API web
> * Conceder permissões à Web API
> * Atualizar a amostra para usar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos e pré-requisitos em [Tutorial: Autenticar os utilizadores num cliente de secretária nativo](tutorial-desktop-app.md).

## <a name="add-a-web-api-application"></a>Adicionar uma aplicação de API Web

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Configure âmbitos

Os âmbitos proporcionam uma forma de reger o acesso a recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, alguns utilizadores podem ter o acesso de leitura e de escrita, ao passo que outros podem ter apenas permissões só de leitura. Neste tutorial, vai definir as permissões de leitura e escrita para a API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Grave o valor no **SCOPES** para que o `demo.read` âmbito de utilização seja utilizado posteriormente quando configurar a aplicação para desktop. O valor total do âmbito é semelhante a `https://contosob2c.onmicrosoft.com/api/demo.read` .

## <a name="grant-permissions"></a>Conceder permissões

Para ligar para uma API web protegida a partir de uma aplicação de cliente nativo, você precisa conceder as permissões de pedido de cliente nativo registado para a API web que registrou em Azure AD B2C.

No tutorial pré-requisito, registou uma aplicação de cliente nativo chamada *nativeapp1*. Os seguintes passos configuram o registo de candidaturas nativas com os âmbitos da API que expôs para *o webapi1* na secção anterior. Isto permite que a aplicação de desktop obtenha um token de acesso a partir de Azure AD B2C que a API web pode usar para verificar e fornecer acesso alargado aos seus recursos. Configura e execute tanto a aplicação de ambiente de trabalho como as amostras de código web API mais tarde no tutorial.

Para registar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a nossa nova experiência de registos de **Aplicações unificadas** ou a nossa experiência de **Aplicações (Legacy).** [Saiba mais sobre a nova experiência.](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[Registos de aplicações](#tab/app-reg-ga/)

1. Selecione **as inscrições da App** e, em seguida, selecione a aplicação de cliente nativo que deve ter acesso à API. Por exemplo, *a nativeapp1*.
1. Em **Gestão**, selecione **permissões API**.
1. Sob **permissões configuradas,** **selecione Adicione uma permissão**.
1. Selecione o **separador As Minhas APIs.**
1. Selecione a API à qual deve ser concedido o acesso ao pedido de cliente nativo. Por exemplo, *webapi1*.
1. Em **Permissão,** expanda **a demonstração** e, em seguida, selecione os âmbitos que definiu anteriormente. Por exemplo, *demo.read* e *demo.write*.
1. **Selecione Permissões de adicionar**. Como dirigido, aguarde alguns minutos antes de seguir para o próximo passo.
1. Selecione **o consentimento administrativo do Grant para (o nome do seu inquilino)**.
1. Selecione a sua conta de administrador atualmente assinada ou inscreva-se com uma conta no seu inquilino Azure AD B2C que foi atribuída pelo menos a função de administrador de *aplicação cloud.*
1. Selecione **Aceitar**.
1. Selecione **Refresh**, e, em seguida, verifique que "Concedido para..." aparece em **Estado** para ambos os âmbitos. Pode levar alguns minutos para as permissões se propagarem.

#### <a name="applications-legacy"></a>[Candidaturas (Legado)](#tab/applications-legacy/)

1. Selecione **Aplicações (Legado)** e, em seguida, selecione *nativeapp1*.
1. Selecione **o acesso a API** e, em seguida, selecione **Adicionar**.
1. No **dropdown Select API,** selecione *webapi1*.
1. No **dropdown Select Scopes,** selecione os âmbitos que definiu anteriormente. Por exemplo, *demo.read* e *demo.write*.
1. Selecione **OK**.

* * *

Para utilizar a aplicação de ambiente de trabalho WPF, os utilizadores autenticam-se com o Azure AD B2C. A aplicação de ambiente de trabalho obtém uma concessão de autorização do Azure AD B2C para aceder à API Web protegida.

## <a name="configure-the-samples"></a>Configure as amostras

Agora que a API web está registada e tem âmbitos e permissões configurados, configura a aplicação de desktop e amostras de API web para usar o seu inquilino Azure AD B2C.

### <a name="update-the-desktop-application"></a>Atualizar a aplicação de ambiente de trabalho

Num pré-requisito para este artigo, modificou uma [aplicação de ambiente de trabalho WPF](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) para permitir a entrada com um fluxo de utilizador no seu inquilino Azure AD B2C. Nesta secção, atualiza a mesma aplicação para fazer referência à API web que registou anteriormente, *webapi1*.

1. Abra a solução **active-directório-b2c-wpf** `active-directory-b2c-wpf.sln` () em Visual Studio.
1. No projeto **active-directy-b2c-wpf,** abra o ficheiro *App.xaml.cs* e encontre as seguintes definições variáveis.
    1. Substitua o valor da `ApiScopes` variável pelo valor que registou anteriormente quando definiu o âmbito **de demo.read.**
    1. Substitua o valor da `ApiEndpoint` variável pelo **URI de redirecionamento** que registou anteriormente quando registou a API web (por exemplo, *webapi1)* no seu inquilino.

    Eis um exemplo:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Obter e atualizar a amostra de API Node.js

Em seguida, obtenha a amostra de código API web Node.js do GitHub e configugue-a para usar a API web que registou no seu inquilino Azure AD B2C.

[Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) ou clone a aplicação Web de exemplo a partir do GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

O exemplo de API Web Node.js utiliza a biblioteca Passport.js para ativar o Azure AD B2C no sentido de proteger as chamadas à API.

1. Abra o ficheiro `index.js`.
1. Atualizar estas definições variáveis com os seguintes valores. Alterar `<web-API-application-ID>` para o ID de **Aplicação (cliente)** da API web que registou anteriormente *(webapi1*). Mude `<your-b2c-tenant>` para o nome do seu inquilino Azure AD B2C.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. Uma vez que está a executar a API localmente, atualize o caminho na rota para o método GET para `/` em vez da localização da app de demonstração de `/hello` :

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>Executar os exemplos

### <a name="run-the-nodejs-web-api"></a>Executar a API web Node.js

1. Inicie uma linha de comandos Node.js.
2. Mude para o diretório que contém o exemplo de Node.js. Por exemplo, `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Execute os seguintes comandos:
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Executar a aplicação de ambiente de trabalho

1. Abra a solução **active-directório-b2c-wpf** no Visual Studio.
2. Prima **F5** para executar a aplicação de ambiente de trabalho.
3. Inicie sessão com o endereço de e-mail e a palavra-passe utilizados no tutorial [Autenticar utilizadores com o Azure Active Directory B2C numa aplicação de ambiente de trabalho](tutorial-desktop-app.md).
4. Selecione o botão **Call API.**

A aplicação para desktop faz um pedido para a API web que executa localmente, e após a verificação de um token de acesso válido, mostra o nome de exibição do utilizador inscrito.

![Nome do visor mostrado no painel superior da aplicação de ambiente de trabalho WPF](./media/tutorial-desktop-app-webapi/desktop-app-01-post-api-call.png)

A sua aplicação para desktop, protegida pelo Azure AD B2C, está a chamar a API web que também está protegida pelo Azure AD B2C.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar âmbitos para uma API web
> * Conceder permissões à Web API
> * Atualizar a amostra para usar a aplicação

> [!div class="nextstepaction"]
> [Adicione fornecedores de identidade às suas aplicações no Azure Ative Directory B2C](add-identity-provider.md)