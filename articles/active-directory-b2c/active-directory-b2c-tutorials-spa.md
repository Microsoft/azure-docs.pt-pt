---
title: Tutorial - ativar a autenticação num aplicativo de página única - Azure Active Directory B2C | Documentos da Microsoft
description: Tutorial sobre como utilizar o Azure Active Directory B2C para fornecer início de sessão do utilizador para uma aplicação de página única (JavaScript).
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3773efdbe47c045dccf74ace3e1b53936c9400fe
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190310"
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

* [Criar fluxos de utilizador](tutorial-create-user-flows.md) para permitir experiências de utilizador na sua aplicação. 
* Instale [Visual Studio 2019](https://www.visualstudio.com/downloads/) com o **desenvolvimento na web e ASP.NET** carga de trabalho.
* Instalar o [.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) ou posterior
* Instalar o [Node. js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Atualizar a aplicação

Tutorial que concluídas como parte dos pré-requisitos, adicionou uma aplicação web no Azure AD B2C. Para ativar a comunicação com o exemplo no tutorial, terá de adicionar um URI de redirecionamento para a aplicação no Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Selecione **aplicativos**e, em seguida, selecione a *webapp1* aplicação.
5. Sob **URL de resposta**, adicione `http://localhost:6420`.
6. Selecione **Guardar**.
7. Na página de propriedades, registe o ID da aplicação que irá utilizar quando configurar a aplicação web.
8. Selecione **chaves**, selecione **gerar chave**e selecione **guardar**. Registe a chave que irá utilizar quando configurar a aplicação web.

## <a name="configure-the-sample"></a>Configurar o exemplo

Neste tutorial, configurou um exemplo que pode transferir a partir do GitHub. O exemplo demonstra como uma aplicação de página única pode utilizar o Azure AD B2C para inscrição e início de sessão do utilizador e chamar uma API web protegida.

[Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clone o exemplo a partir do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

Para alterar as definições:

1. Abra o `index.html` ficheiro no exemplo.
2. Configure o exemplo com o ID da aplicação e a chave que registou anteriormente. Altere as seguintes linhas de código, substituindo os valores com os nomes do seu diretório e APIs:

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID>',
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
    };
    ```

    O nome do fluxo de utilizador utilizado neste tutorial é **B2C_1_signupsignin1**. Se estiver a utilizar um nome de fluxo de utilizador diferente, utilize o seu nome de fluxo de utilizador no `authority` valor.

## <a name="run-the-sample"></a>Executar o exemplo

1. Inicie uma linha de comandos Node.js.
2. Mude para o diretório que contém o exemplo de Node.js. Por exemplo, `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Execute os seguintes comandos:

    ```
    npm install && npm update
    node server.js
    ```

    A janela da consola mostra o número de porta onde está alojada a aplicação.
    
    ```
    Listening on port 6420...
    ```

4. Utilize um browser para navegar para o endereço `http://localhost:6420` para ver a aplicação.

O exemplo suporta inscrição, início de sessão, edição de perfil e reposição de palavra-passe. Este tutorial destaca como um utilizador inicia sessão utilizando um endereço de e-mail.

### <a name="sign-up-using-an-email-address"></a>Inscrever-se com um endereço de e-mail

1. Clique em **início de sessão** para se inscrever como um utilizador da aplicação. Esta opção utiliza a **B2C_1_signupsignin1** fluxo de utilizador definida no passo anterior.
2. O Azure AD B2C apresenta uma página de início de sessão com uma ligação de inscrição. Uma vez que ainda não tem uma conta, clique na ligação **Inscrever-se agora**. 
3. O fluxo de trabalho de inscrição apresenta uma página para recolher e verificar a identidade do utilizador através de um endereço de e-mail. O fluxo de trabalho de inscrição também recolhe a senha do usuário e os atributos solicitados definidos no fluxo de utilizador.

    Utilize um endereço de e-mail válido e valide com o código de verificação. Defina uma palavra-passe. Introduza os valores para os atributos solicitados. 

    ![Fluxo de trabalho de inscrição](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Clique em **Criar** para criar uma conta local no diretório do Azure AD B2C.

Agora, o utilizador pode utilizar um endereço de e-mail para iniciar sessão e utilizar a aplicação SPA.

> [!NOTE]
> Após iniciar sessão, a aplicação mostra um erro de "permissões insuficientes". Recebe este erro porque está a tentar aceder a um recurso a partir do diretório de demonstração. Como o seu token de acesso só é válido para o seu diretório do Azure AD, a chamada à API não é autorizada. Avance para o próximo tutorial para criar uma API Web protegida para o seu diretório.

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como:

> [!div class="checklist"]
> * Atualizar a aplicação no Azure AD B2C
> * Configurar o exemplo a utilizar a aplicação
> * Inscreva-se com o fluxo de utilizador

> [!div class="nextstepaction"]
> [Tutorial: Conceder acesso a uma API web ASP.NET Core a partir de uma aplicação de página única com o Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md)
