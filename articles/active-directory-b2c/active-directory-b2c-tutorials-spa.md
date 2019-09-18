---
title: 'Tutorial: Habilitar a autenticação em um aplicativo de página única-Azure Active Directory B2C'
description: Saiba como usar Azure Active Directory B2C para fornecer logon de usuário para um aplicativo de página única (JavaScript).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc, seo-javascript-september2019
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 06bb71b27edf97f9b7e3333aef93af7bd0e0529e
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064791"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>Tutorial: Habilitar a autenticação em um aplicativo de página única usando Azure Active Directory B2C

Este tutorial mostra como usar o Azure Active Directory B2C (Azure AD B2C) para entrar e inscrever usuários em um SPA (aplicativo de página única). O Azure AD B2C permite que seus aplicativos se autentiquem em contas sociais, contas corporativas e Azure Active Directory contas usando protocolos padrão abertos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Atualizar o aplicativo no Azure AD B2C
> * Configurar o exemplo para usar o aplicativo
> * Inscrever-se usando o fluxo do usuário

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes recursos de Azure AD B2C em vigor antes de continuar com as etapas deste tutorial:

* [Azure AD B2C locatário](tutorial-create-tenant.md)
* [Aplicativo registrado](tutorial-register-applications.md) em seu locatário
* [Fluxos de usuário criados](tutorial-create-user-flows.md) em seu locatário

Além disso, você precisa do seguinte em seu ambiente de desenvolvimento local:

* Editor de código, por exemplo [Visual Studio Code](https://code.visualstudio.com/) ou [Visual Studio 2019](https://www.visualstudio.com/downloads/)
* [SDK do .NET Core 2,2](https://dotnet.microsoft.com/download) ou posterior
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Atualizar a aplicação

No segundo tutorial que você concluiu como parte dos pré-requisitos, você registrou um aplicativo Web em Azure AD B2C. Para habilitar a comunicação com o exemplo no tutorial, você precisa adicionar um URI de redirecionamento ao aplicativo em Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
1. Selecione **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
1. Selecione **aplicativos**e, em seguida, selecione o aplicativo *webapp1* .
1. Em **URL de resposta**, `http://localhost:6420`adicione.
1. Selecione **Guardar**.
1. Na página Propriedades, registre a **ID do aplicativo**. Você usará a ID do aplicativo em uma etapa posterior quando atualizar o código no aplicativo Web de página única.

## <a name="get-the-sample-code"></a>Obter o código de exemplo

Neste tutorial, você configura um exemplo de código que você baixa do GitHub. O exemplo demonstra como um aplicativo de página única pode usar Azure AD B2C para inscrição e entrada do usuário e para chamar uma API Web protegida.

[Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clone o exemplo a partir do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Atualizar o exemplo

Agora que você obteve o exemplo, atualize o código com o nome do locatário Azure AD B2C e a ID do aplicativo que você registrou em uma etapa anterior.

1. Abra o `index.html` arquivo na raiz do diretório de exemplo.
1. Na definição, modifique o valor clientId com a ID do aplicativo que você registrou em uma etapa anterior. `msalConfig` Em seguida, atualize o valor do URI de **autoridade** com seu nome de locatário Azure ad B2C. Também atualize o URI com o nome do fluxo de usuário de inscrição/entrada criado em um dos pré-requisitos (por exemplo, *B2C_1_signupsignin1*).

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

    O nome do fluxo de usuário usado neste tutorial é **B2C_1_signupsignin1**. Se você estiver usando um nome de fluxo de usuário diferente, especifique esse nome `authority` no valor.

## <a name="run-the-sample"></a>Executar o exemplo

1. Abra uma janela de console e altere para o diretório que contém o exemplo. Por exemplo:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Execute os seguintes comandos:

    ```
    npm install && npm update
    node server.js
    ```

    A janela do console exibe o número da porta do servidor node. js em execução local:

    ```
    Listening on port 6420...
    ```

1. Vá para `http://localhost:6420` em seu navegador para exibir o aplicativo.

O exemplo dá suporte à inscrição, à entrada, à edição de perfil e à redefinição de senha. Este tutorial destaca como um usuário se inscreve usando um endereço de email.

### <a name="sign-up-using-an-email-address"></a>Inscrever-se com um endereço de e-mail

1. Selecione **logon** para iniciar o fluxo de usuário do *B2C_1_signupsignin1* especificado em uma etapa anterior.
1. O Azure AD B2C apresenta uma página de início de sessão com uma ligação de inscrição. Como você ainda não tem uma conta, selecione o link **inscrever-se agora** .
1. O fluxo de trabalho de inscrição apresenta uma página para recolher e verificar a identidade do utilizador através de um endereço de e-mail. O fluxo de trabalho de inscrição também coleta a senha do usuário e os atributos solicitados definidos no fluxo do usuário.

    Utilize um endereço de e-mail válido e valide com o código de verificação. Defina uma palavra-passe. Introduza os valores para os atributos solicitados.

    ![Página de inscrição apresentada pelo fluxo de usuário de entrada/inscrição](./media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.PNG)

1. Selecione **criar** para criar uma conta local no diretório Azure ad B2C.

Quando você seleciona **criar**, a página de inscrição fecha e a página de entrada é exibida novamente.

Agora você pode usar seu endereço de email e senha para entrar no aplicativo.

### <a name="error-insufficient-permissions"></a>Erro: permissões insuficientes

Depois de entrar, o aplicativo exibe um erro de permissões insuficientes-isso é **esperado**:

```Output
ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.
Correlation ID: ce15bbcc-0000-0000-0000-494a52e95cd7
Timestamp: 2019-07-20 22:17:27Z
```

Você recebe esse erro porque o aplicativo Web está tentando acessar uma API da Web protegida pelo diretório de demonstração, *fabrikamb2c*. Como seu token de acesso é válido somente para seu diretório do Azure AD, a chamada à API é, portanto, não autorizada.

Para corrigir esse erro, continue no próximo tutorial da série (consulte [próximas etapas](#next-steps)) para criar uma API Web protegida para seu diretório.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Atualizar o aplicativo no Azure AD B2C
> * Configurar o exemplo para usar o aplicativo
> * Inscrever-se usando o fluxo do usuário

Agora passe para o próximo tutorial da série para conceder acesso a uma API Web protegida do SPA:

> [!div class="nextstepaction"]
> [Tutorial: Conceder acesso a uma API Web do ASP.NET Core de um SPA usando Azure AD B2C >](active-directory-b2c-tutorials-spa-webapi.md)
