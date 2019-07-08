---
title: Tutorial - ativar a autenticação num aplicativo de página única - Azure Active Directory B2C
description: Saiba como utilizar o Azure Active Directory B2C para fornecer início de sessão do utilizador para uma aplicação de página única (JavaScript).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/08/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6824cc84c24b41fd82afd39ead3029a212173948
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67624784"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-using-azure-active-directory-b2c"></a>Tutorial: Ativar a autenticação num aplicativo de página única com o Azure Active Directory B2C

Este tutorial mostra-lhe como utilizar o Azure Active Directory (Azure AD) B2C para iniciar sessão e inscrição de utilizadores numa aplicação de página única (SPA). O Azure AD B2C permite que seus aplicativos autenticar contas de redes sociais, contas empresariais e contas do Azure Active Directory através de protocolos padrão abertos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Atualizar a aplicação no Azure AD B2C
> * Configurar o exemplo a utilizar a aplicação
> * Inscreva-se com o fluxo de utilizador

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Terá dos seguintes recursos do Azure AD B2C em vigor antes de continuar com os passos neste tutorial:

* [Inquilino do Azure AD B2C](tutorial-create-tenant.md)
* [Aplicação registada](tutorial-register-applications.md) no seu inquilino
* [Fluxos de utilizador criados](tutorial-create-user-flows.md) no seu inquilino

Além disso, terá o seguinte no seu ambiente de desenvolvimento local:

* Por exemplo o editor, de código [Visual Studio Code](https://code.visualstudio.com/) ou [2019 do Visual Studio](https://www.visualstudio.com/downloads/)
* [SDK do .NET core 2.0.0](https://www.microsoft.com/net/core) ou posterior
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Atualizar a aplicação

O segundo tutorial concluída como parte dos pré-requisitos, registou uma aplicação web no Azure AD B2C. Para ativar a comunicação com o exemplo no tutorial, terá de adicionar um URI de redirecionamento para a aplicação no Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
1. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
1. Selecione **aplicativos**e, em seguida, selecione a *webapp1* aplicação.
1. Sob **URL de resposta**, adicione `http://localhost:6420`.
1. Selecione **Guardar**.
1. Na página de propriedades, registe o **ID da aplicação**. Utilize o ID da aplicação num passo posterior ao atualizar o código na aplicação web de página única.

## <a name="get-the-sample-code"></a>Obter o código de exemplo

Neste tutorial, configurou um exemplo de código que transferir a partir do GitHub. O exemplo demonstra como uma aplicação de página única pode utilizar o Azure AD B2C para inscrições de utilizador e início de sessão e chamar uma API web protegida.

[Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clone o exemplo a partir do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

## <a name="update-the-sample"></a>Atualização do exemplo

Agora que obtém o exemplo, atualize o código com o nome do seu inquilino do Azure AD B2C e o ID da aplicação que registou no passo anterior.

1. Abra o `index.html` ficheiro na raiz do diretório de exemplo.
1. Na `msalConfig` definição, modificar os **clientId** valor com o ID da aplicação que registou no passo anterior. Em seguida, atualize o **autoridade** valor do URI com o nome do seu inquilino do Azure AD B2C. Atualize também o URI com o nome do fluxo de sessão-inscrição/início de sessão do utilizador que criou em um dos pré-requisitos (por exemplo, *B2C_1_signupsignin1*).

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

    O nome do fluxo de utilizador utilizado neste tutorial é **B2C_1_signupsignin1**. Se estiver a utilizar um nome de fluxo de utilizador diferente, especifique esse nome no `authority` valor.

## <a name="run-the-sample"></a>Executar o exemplo

1. Abra uma janela de consola e altere o diretório que contém o exemplo. Por exemplo:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```
1. Execute os seguintes comandos:

    ```
    npm install && npm update
    node server.js
    ```

    A janela da consola mostra o número de porta do servidor node. js localmente em execução:

    ```
    Listening on port 6420...
    ```

1. Navegue para `http://localhost:6420` no seu browser para ver a aplicação.

O exemplo suporta inscrição, início de sessão, edição de perfil e reposição de palavra-passe. Este tutorial destaca como um utilizador inicia sessão utilizando um endereço de e-mail.

### <a name="sign-up-using-an-email-address"></a>Inscrever-se com um endereço de e-mail

1. Clique em **início de sessão** para se inscrever como um utilizador da aplicação. Esta opção utiliza a **B2C_1_signupsignin1** fluxo de utilizador que especificou no passo anterior.
1. O Azure AD B2C apresenta uma página de início de sessão com uma ligação de inscrição. Uma vez que ainda não tem uma conta, clique na ligação **Inscrever-se agora**.
1. O fluxo de trabalho de inscrição apresenta uma página para recolher e verificar a identidade do utilizador através de um endereço de e-mail. O fluxo de trabalho de inscrição também recolhe a senha do usuário e os atributos solicitados definidos no fluxo de utilizador.

    Utilize um endereço de e-mail válido e valide com o código de verificação. Defina uma palavra-passe. Introduza os valores para os atributos solicitados.

    ![Fluxo de trabalho de inscrição](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

1. Clique em **Criar** para criar uma conta local no diretório do Azure AD B2C.

Quando clica em **criar**, fecha a página de inscrição e o página de início de sessão será exibida novamente.

Agora, pode utilizar o seu endereço de e-mail e palavra-passe para iniciar sessão na aplicação.

### <a name="error-insufficient-permissions"></a>Erro: permissões insuficientes

Depois de iniciar sessão, a aplicação apresenta um erro de permissões insuficientes – Este é **esperado**:

`ServerError: AADB2C90205: This application does not have sufficient permissions against this web resource to perform the operation.`

Recebe este erro porque está a tentar aceder a um recurso do diretório de demonstração, mas o token de acesso só é válido para o diretório do Azure AD. A chamada de API, portanto, não está autorizada.

Avance para o próximo tutorial da série (consulte [passos seguintes](#next-steps)) para criar uma API de web protegido para o seu diretório.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como:

> [!div class="checklist"]
> * Atualizar a aplicação no Azure AD B2C
> * Configurar o exemplo a utilizar a aplicação
> * Inscreva-se com o fluxo de utilizador

Agora avance para o próximo tutorial da série para conceder acesso a uma API web protegida a partir do SPA:

> [!div class="nextstepaction"]
> [Tutorial: Conceder acesso a uma API web ASP.NET Core a partir de uma aplicação de página única com o Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
