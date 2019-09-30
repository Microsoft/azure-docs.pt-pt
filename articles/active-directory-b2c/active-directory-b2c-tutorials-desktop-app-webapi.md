---
title: Tutorial-conceder acesso a uma API Web node. js de um aplicativo de área de trabalho-Azure Active Directory B2C | Microsoft Docs
description: Tutorial sobre como utilizar o Active Directory B2C para proteger uma API Web Node.js e chamá-la a partir de uma aplicação de ambiente de trabalho .NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8c4b2d818549da07faf9ecd28f61b4ed5315f745
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679332"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Tutorial: Conceder acesso a uma API Web node. js de um aplicativo de desktop usando Azure Active Directory B2C

Este tutorial mostra como chamar um recurso de API Web do node. js protegido por Azure Active Directory B2C (Azure AD B2C) de um aplicativo de área de trabalho do Windows Presentation Foundation (WPF).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar escopos para uma API Web
> * Conceder permissões para a API da Web
> * Atualizar o exemplo para usar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas e os pré-requisitos [no tutorial: Habilite a autenticação de aplicativo de área](active-directory-b2c-tutorials-desktop-app.md)de trabalho com contas usando Azure Active Directory B2C.

## <a name="add-a-web-api-application"></a>Adicionar uma aplicação de API Web

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Configurar escopos

Os escopos fornecem uma maneira de controlar o acesso a recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, alguns utilizadores podem ter o acesso de leitura e de escrita, ao passo que outros podem ter apenas permissões só de leitura. Neste tutorial, vai definir as permissões de leitura para a API Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API Web protegida de um aplicativo, você precisa conceder permissões de aplicativo para a API. No tutorial de pré-requisito, você criou um aplicativo Web no Azure AD B2C chamado *App1*. Você usa esse aplicativo para chamar a API da Web.

1. Selecione **aplicativos**e, em seguida, selecione *nativeapp1*.
1. Selecione **acesso à API**e, em seguida, selecione **Adicionar**.
1. Na lista suspensa **selecionar API** , selecione *webapi1*.
1. Na lista suspensa **selecionar escopos** , selecione os escopos que você definiu anteriormente. Por exemplo, *demo. Read* e *demo. Write*.
1. Selecione **OK**.

Um usuário é autenticado com Azure AD B2C para usar o aplicativo de área de trabalho do WPF. O aplicativo da área de trabalho obtém uma concessão de autorização de Azure AD B2C para acessar a API Web protegida.

## <a name="configure-the-sample"></a>Configurar o exemplo

Agora que a API Web está registrada e você tem escopos definidos, configure o código da API Web para usar seu locatário Azure AD B2C. Neste tutorial, você configura um aplicativo Web node. js de exemplo que pode ser baixado do GitHub.

[Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) ou clone a aplicação Web de exemplo a partir do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

O exemplo de API Web Node.js utiliza a biblioteca Passport.js para ativar o Azure AD B2C no sentido de proteger as chamadas à API.

1. Abra o ficheiro `index.js`.
2. Configure o exemplo com as informações de registo de inquilino do Azure AD B2C. Altere as seguintes linhas de código:

    ```nodejs
    var tenantID = "<your-tenant-name>.onmicrosoft.com";
    var clientID = "<application-ID>";
    var policyName = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Executar o exemplo

1. Inicie uma linha de comandos Node.js.
2. Mude para o diretório que contém o exemplo de Node.js. Por exemplo, `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Execute os seguintes comandos:
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Executar o aplicativo de desktop

1. Abra a solução **Active-Directory-B2C-WPF** no Visual Studio.
2. Prima **F5** para executar a aplicação de ambiente de trabalho.
3. Inicie sessão com o endereço de e-mail e a palavra-passe utilizados no tutorial [Autenticar utilizadores com o Azure Active Directory B2C numa aplicação de ambiente de trabalho](active-directory-b2c-tutorials-desktop-app.md).
4. Clique no botão **Chamar API**.

O aplicativo de desktop faz uma solicitação para a API Web e obtém uma resposta com o nome de exibição do usuário conectado. Você está protegido o aplicativo de área de trabalho está chamando a API Web protegida em seu locatário Azure AD B2C.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar escopos para uma API Web
> * Conceder permissões para a API da Web
> * Atualizar o exemplo para usar o aplicativo

> [!div class="nextstepaction"]
> [Tutorial: Adicionar provedores de identidade a seus aplicativos no Azure Active Directory B2C](tutorial-add-identity-providers.md)
