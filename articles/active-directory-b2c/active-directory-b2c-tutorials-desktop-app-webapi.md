---
title: Tutorial-conceder acesso a uma API Web node. js de um aplicativo de área de trabalho-Azure Active Directory B2C | Microsoft Docs
description: Tutorial sobre como utilizar o Active Directory B2C para proteger uma API Web Node.js e chamá-la a partir de uma aplicação de ambiente de trabalho .NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 35d850dc6439173c83730375c576855f6920b450
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475239"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Tutorial: Conceder acesso a uma API Web Node.js a partir de uma aplicação de ambiente de trabalho com o Azure Active Directory B2C

Este tutorial mostra como chamar uma API Web node. js protegida por Azure Active Directory B2C (Azure AD B2C) de um aplicativo de área de trabalho Windows Presentation Foundation (WPF), também protegido pelo Azure AD B2C.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar escopos para uma API Web
> * Conceder permissões para a API da Web
> * Atualizar o exemplo para usar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas e os pré-requisitos no [tutorial: autenticar usuários em um cliente de área de trabalho nativa](active-directory-b2c-tutorials-desktop-app.md).

## <a name="add-a-web-api-application"></a>Adicionar uma aplicação de API Web

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Configurar escopos

Os escopos fornecem uma maneira de controlar o acesso a recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, alguns utilizadores podem ter o acesso de leitura e de escrita, ao passo que outros podem ter apenas permissões só de leitura. Neste tutorial, vai definir as permissões de leitura e escrita para a API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Registre o valor em **escopos** para o escopo de `demo.read` a ser usado em uma etapa posterior ao configurar o aplicativo da área de trabalho. O valor de escopo completo é semelhante a `https://contosob2c.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API Web protegida de um aplicativo cliente nativo, você precisa conceder permissões de aplicativo cliente nativo registradas para a API da Web que você registrou na Azure AD B2C.

No tutorial de pré-requisito, você registrou um aplicativo cliente nativo chamado *nativeapp1*. As etapas a seguir configuram esse registro de aplicativo nativo com os escopos de API que você expôs para *webapi1* na seção anterior. Isso permite que o aplicativo da área de trabalho obtenha um token de acesso de Azure AD B2C que a API da Web pode usar para verificar e fornecer acesso com escopo a seus recursos. Você configura e executa o aplicativo da área de trabalho e os exemplos de código da API Web posteriormente no tutorial.

#### <a name="applicationstabapplications"></a>[Aplicações](#tab/applications/)

1. Selecione **aplicativos**e, em seguida, selecione *nativeapp1*.
1. Selecione **acesso à API**e, em seguida, selecione **Adicionar**.
1. Na lista suspensa **selecionar API** , selecione *webapi1*.
1. Na lista suspensa **selecionar escopos** , selecione os escopos que você definiu anteriormente. Por exemplo, *demo. Read* e *demo. Write*.
1. Selecione **OK**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Selecione **registros de aplicativo (versão prévia)** e, em seguida, selecione o aplicativo cliente nativo que deve ter acesso à API. Por exemplo, *nativeapp1*.
1. Em **gerenciar**, selecione **permissões de API**.
1. Em **permissões configuradas**, selecione **Adicionar uma permissão**.
1. Selecione a guia **minhas APIs** .
1. Selecione a API à qual o aplicativo cliente nativo deve receber acesso. Por exemplo, *webapi1*.
1. Em **permissão**, expanda **demonstração**e selecione os escopos que você definiu anteriormente. Por exemplo, *demo. Read* e *demo. Write*.
1. Selecione **adicionar permissões**. Conforme indicado, aguarde alguns minutos antes de prosseguir para a próxima etapa.
1. Selecione **conceder consentimento de administrador para (seu nome de locatário)** .
1. Selecione sua conta de administrador conectada no momento ou entre com uma conta em seu locatário Azure AD B2C que tenha sido atribuído pelo menos à função *administrador de aplicativos de nuvem* .
1. Selecione **Aceitar**.
1. Selecione **Atualizar**e, em seguida, verifique se "concedido para..." aparece em **status** para ambos os escopos. Pode levar alguns minutos para que as permissões se propaguem.

* * *

Um usuário é autenticado com Azure AD B2C para usar o aplicativo de área de trabalho do WPF. O aplicativo da área de trabalho obtém uma concessão de autorização de Azure AD B2C para acessar a API Web protegida.

## <a name="configure-the-samples"></a>Configurar os exemplos

Agora que a API da Web está registrada e se você tem escopos e permissões configuradas, configure o aplicativo de área de trabalho e amostras de API Web para usar seu locatário Azure AD B2C.

### <a name="update-the-desktop-application"></a>Atualizar o aplicativo da área de trabalho

Em um pré-requisito para este artigo, você modificou um [aplicativo de área de trabalho do WPF](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) para habilitar a entrada com um fluxo de usuário em seu locatário de Azure ad B2C. Nesta seção, você atualiza esse mesmo aplicativo para fazer referência à API Web que você registrou anteriormente, *webapi1*.

1. Abra a solução **Active-Directory-B2C-WPF** (`active-directory-b2c-wpf.sln`) no Visual Studio.
1. No projeto **Active-Directory-B2C-WPF** , abra o arquivo *app.XAML.cs* e localize as definições de variável a seguir.
    1. Substitua o valor da variável `ApiScopes` pelo valor que você registrou anteriormente ao definir o escopo **demo. Read** .
    1. Substitua o valor da variável `ApiEndpoint` pelo URI de **redirecionamento** que você registrou anteriormente quando registrou a API Web (por exemplo, *webapi1*) em seu locatário.

    Segue-se um exemplo:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Obter e atualizar o exemplo de API do node. js

Em seguida, obtenha o exemplo de código da API Web node. js do GitHub e configure-o para usar a API Web que você registrou em seu locatário Azure AD B2C.

[Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) ou clone a aplicação Web de exemplo a partir do GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

O exemplo de API Web Node.js utiliza a biblioteca Passport.js para ativar o Azure AD B2C no sentido de proteger as chamadas à API.

1. Abra o ficheiro `index.js`.
1. Atualize essas definições de variável com os valores a seguir. Altere `<web-API-application-ID>` para a **ID do aplicativo (cliente)** da API Web que você registrou anteriormente (*webapi1*). Altere `<your-b2c-tenant>` para o nome do seu locatário Azure AD B2C.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. Como você está executando a API localmente, atualize o caminho na rota para o método GET para `/` em vez do local do aplicativo de demonstração de `/hello`:

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>Executar os exemplos

### <a name="run-the-nodejs-web-api"></a>Executar a API Web do node. js

1. Inicie uma linha de comandos Node.js.
2. Mude para o diretório que contém o exemplo de Node.js. Por exemplo, `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Execute os seguintes comandos:
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Executar o aplicativo de desktop

1. Abra a solução **Active-Directory-B2C-WPF** no Visual Studio.
2. Prima **F5** para executar a aplicação de ambiente de trabalho.
3. Inicie sessão com o endereço de e-mail e a palavra-passe utilizados no tutorial [Autenticar utilizadores com o Azure Active Directory B2C numa aplicação de ambiente de trabalho](active-directory-b2c-tutorials-desktop-app.md).
4. Selecione o botão **chamar API** .

O aplicativo de desktop faz uma solicitação para a API Web em execução local e, após a verificação de um token de acesso válido, mostra o nome de exibição do usuário conectado.

![Nome de exibição mostrado no painel superior do aplicativo de área de trabalho do WPF](media/active-directory-b2c-tutorials-desktop-app-webapi/desktop-app-01-post-api-call.png)

Seu aplicativo de área de trabalho, protegido pelo Azure AD B2C, está chamando a API da Web em execução localmente que também é protegida pelo Azure AD B2C.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar escopos para uma API Web
> * Conceder permissões para a API da Web
> * Atualizar o exemplo para usar o aplicativo

> [!div class="nextstepaction"]
> [Tutorial: adicionar provedores de identidade a seus aplicativos no Azure Active Directory B2C](tutorial-add-identity-providers.md)
