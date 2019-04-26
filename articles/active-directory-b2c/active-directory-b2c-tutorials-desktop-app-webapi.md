---
title: Tutorial - conceder acesso a uma API da web de node. js a partir de um aplicativo de desktop - Azure Active Directory B2C | Documentos da Microsoft
description: Tutorial sobre como utilizar o Active Directory B2C para proteger uma API Web Node.js e chamá-la a partir de uma aplicação de ambiente de trabalho .NET.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 90a6a88ff0dc5aab1163e471b24cd1d00e548a1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60315937"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Tutorial: Conceder acesso a uma API da web de node. js a partir de uma aplicação de ambiente de trabalho com o Azure Active Directory B2C

Este tutorial mostra-lhe como chamar um recurso de API Web Node.js protegida do Azure Active Directory (Azure AD) B2C a partir de uma aplicação de ambiente de trabalho do Windows Presentation Foundation (WPF).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar âmbitos para uma API web
> * Conceder permissões para a API web
> * O exemplo a utilizar a aplicação de atualização

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Conclua os passos e a pré-requisitos no [Tutorial: Ativar a autenticação de aplicação de ambiente de trabalho com contas através do Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md).

## <a name="add-a-web-api-application"></a>Adicionar uma aplicação de API Web

Recursos da API Web têm de ser registados no seu inquilino antes de poderem aceitar e responder a pedidos de recursos protegidos por aplicações cliente que apresentam um token de acesso. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Add**.
5. Introduza um nome para a aplicação. Por exemplo, *webapi1*.
6. Para **incluir a aplicação web / web API** e **permitir fluxo implícito**, selecione **Sim**.
7. Para **URL de resposta**, introduza um ponto de extremidade em que o Azure AD B2C deverá devolver quaisquer tokens que a aplicação solicite. Neste tutorial, o exemplo é executada localmente e escuta na `https://localhost:5000`.
8. Para **URI de ID de aplicação**, introduza o identificador utilizado para a API web. O URI identificador completo, incluindo o domínio, é gerado por si. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
9. Clique em **Criar**.
10. Na página de propriedades, registe o ID da aplicação que irá utilizar quando configurar a aplicação web.

## <a name="configure-scopes"></a>Configurar âmbitos

Âmbitos proporcionam uma forma de reger o acesso a recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, alguns utilizadores podem ter o acesso de leitura e de escrita, ao passo que outros podem ter apenas permissões só de leitura. Neste tutorial, vai definir as permissões de leitura para a API Web.

1. Selecione **aplicativos**e, em seguida, selecione *webapi1*.
2. Selecione **âmbitos publicados**.
3. Para **âmbito**, introduza `Hello.Read`e para a descrição, insira `Read access to hello`.
4. Para **âmbito**, introduza `Hello.Write`e para a descrição, insira `Write access to hello`.
5. Clique em **Guardar**.

Os âmbitos publicados podem ser utilizados para conceder permissão a uma aplicação cliente à API Web.

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API web protegida a partir de uma aplicação, tem de conceder as permissões de aplicação para a API. O tutorial de pré-requisitos, criou uma aplicação web no Azure AD B2C com o nome *app1*. Utilizar esta aplicação para chamar a API web.

1. Selecione **aplicativos**e, em seguida, selecione *nativeapp1*.
2. Selecione **acesso à API**e, em seguida, selecione **Add**.
3. Na **selecionar API** menu pendente, selecione *webapi1*.
4. Na **selecionar âmbitos** menu pendente, selecione a **Hello.Read** e **Hello.Write** âmbitos que definiu anteriormente.
5. Clique em **OK**.

Um utilizador é autenticado com o Azure AD B2C para utilizar a aplicação de ambiente de trabalho do WPF. O aplicativo de área de trabalho obtém uma concessão de autorização do Azure AD B2C para aceder à API web protegida.

## <a name="configure-the-sample"></a>Configurar o exemplo

Agora que a API web está registada e já estão definidos âmbitos, configura a web, código de API para utilizar o inquilino do Azure AD B2C. Neste tutorial, irá configurar uma aplicação web do node. js de exemplo que pode transferir a partir do GitHub. 

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

### <a name="run-the-desktop-application"></a>Executar a aplicação de ambiente de trabalho

1. Abra o **active-directory-b2c-wpf** solução no Visual Studio.
2. Prima **F5** para executar a aplicação de ambiente de trabalho.
3. Inicie sessão com o endereço de e-mail e a palavra-passe utilizados no tutorial [Autenticar utilizadores com o Azure Active Directory B2C numa aplicação de ambiente de trabalho](active-directory-b2c-tutorials-desktop-app.md).
4. Clique no botão **Chamar API**. 

O aplicativo de área de trabalho faz um pedido para a API web para e obtém uma resposta com o nome de exibição de logon do usuário. Protegida aplicativo de desktop é chamar a API de web protegida no seu inquilino do Azure AD B2C.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar âmbitos para uma API web
> * Conceder permissões para a API web
> * O exemplo a utilizar a aplicação de atualização

> [!div class="nextstepaction"]
> [Tutorial: Adicionar fornecedores de identidade às suas aplicações no Azure Active Directory B2C](tutorial-add-identity-providers.md)
