---
title: Tutorial – habilitar a autenticação em um aplicativo Web-Azure Active Directory B2C
description: Tutorial sobre como utilizar o Azure Active Directory B2C para fornecer início de sessão do utilizador para uma aplicação Web ASP.NET.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 09/19/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b42634aa86f210382adb1ae224c847a92d89109b
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103313"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Tutorial: Habilitar a autenticação em um aplicativo Web usando Azure Active Directory B2C

Este tutorial mostra como usar Azure Active Directory B2C (Azure AD B2C) para entrar e inscrever usuários em um aplicativo Web ASP.NET. O Azure AD B2C permite que seus aplicativos se autentiquem em contas sociais, contas corporativas e Azure Active Directory contas usando protocolos padrão abertos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Atualizar o aplicativo no Azure AD B2C
> * Configurar o exemplo para usar o aplicativo
> * Inscrever-se usando o fluxo do usuário

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Crie fluxos de usuário](tutorial-create-user-flows.md) para habilitar experiências de usuário em seu aplicativo.
* Instale o [Visual Studio 2019](https://www.visualstudio.com/downloads/) com a carga de trabalho de **desenvolvimento de ASP.net e Web** .

## <a name="update-the-application"></a>Atualizar a aplicação

No tutorial que você concluiu como parte dos pré-requisitos, você adicionou um aplicativo Web no Azure AD B2C. Para habilitar a comunicação com o exemplo neste tutorial, você precisa adicionar um URI de redirecionamento ao aplicativo em Azure AD B2C.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém seu locatário.
1. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
1. Selecione **aplicativos**e, em seguida, selecione o aplicativo *webapp1* .
1. Em **URL de resposta**, `https://localhost:44316`adicione.
1. Selecione **Guardar**.
1. Na página Propriedades, registre a ID do aplicativo que você usará ao configurar o aplicativo Web.
1. Selecione **chaves**, selecione **gerar chave**e selecione **salvar**. Registre a chave que você usará ao configurar o aplicativo Web.

## <a name="configure-the-sample"></a>Configurar o exemplo

Neste tutorial, você configura um exemplo que pode ser baixado do GitHub. O exemplo usa ASP.NET para fornecer uma lista de tarefas pendentes simples. O exemplo usa os [componentes de middleware do Microsoft OWIN](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). [Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) ou clone o exemplo a partir do GitHub. Certifique-se de que extrai o ficheiro de exemplo numa pasta em que o comprimento de carateres total do caminho seja inferior a 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Os dois projetos a seguir estão na solução de exemplo:

* **TaskWebApp** -criar e editar uma lista de tarefas. O exemplo usa o fluxo de usuário de inscrição **ou de entrada** para inscrever-se e conectar usuários.
* **TaskService** -dá suporte à funcionalidade criar, ler, atualizar e excluir lista de tarefas. A API é protegida por Azure AD B2C e chamada pelo TaskWebApp.

Você altera o exemplo para usar o aplicativo que está registrado em seu locatário, que inclui a ID do aplicativo e a chave que você registrou anteriormente. Você também configura os fluxos de usuário que criou. O exemplo define os valores de configuração como configurações no arquivo *Web. config* .

Atualize as configurações no arquivo Web. config para trabalhar com seu fluxo de usuário:

1. Abra a solução **B2C-WebAPI-DotNet** no Visual Studio.
1. No projeto **TaskWebApp** , abra o arquivo **Web. config** .
    1. Atualize o valor de `ida:Tenant` e `ida:AadInstance` com o nome do locatário de Azure ad B2C que você criou. Por exemplo, substitua `fabrikamb2c` por `contoso`.
    1. Substitua o valor de `ida:ClientId` pela ID do aplicativo que você registrou.
    1. Substitua o valor de `ida:ClientSecret` pela chave que registou. Você deve codificar o segredo do cliente em XML antes de adicioná-lo ao Web. config.
    1. Substitua o valor de `ida:SignUpSignInPolicyId` por `b2c_1_signupsignin1`.
    1. Substitua o valor de `ida:EditProfilePolicyId` por `b2c_1_profileediting1`.
    1. Substitua o valor de `ida:ResetPasswordPolicyId` por `b2c_1_passwordreset1`.

## <a name="run-the-sample"></a>Executar o exemplo

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **TaskWebApp** e clique em **definir como projeto de inicialização**.
1. Pressione **F5**. É iniciado o browser predefinido para o endereço do site local `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Inscrever-se com um endereço de e-mail

1. Selecione **inscrever-se/entrar** para se inscrever como um usuário do aplicativo. O fluxo de usuário **b2c_1_signupsignin1** é usado.
1. O Azure AD B2C apresenta uma página de início de sessão com uma ligação de inscrição. Como você ainda não tem uma conta, selecione **inscrever-se agora**. O fluxo de trabalho de inscrição apresenta uma página para recolher e verificar a identidade do utilizador através de um endereço de e-mail. O fluxo de trabalho de inscrição também coleta a senha do usuário e os atributos solicitados definidos no fluxo do usuário.
1. Utilize um endereço de e-mail válido e valide com o código de verificação. Defina uma palavra-passe. Introduza os valores para os atributos solicitados.

    ![Página de inscrição mostrada como parte do fluxo de trabalho de entrada/inscrição](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.PNG)

1. Selecione **criar** para criar uma conta local no locatário do Azure ad B2C.

O usuário do aplicativo agora pode usar seu endereço de email para entrar e usar o aplicativo Web.

No entanto, o recurso de **lista de tarefas pendentes** não funcionará até que você conclua [o próximo tutorial da série, tutorial: Use Azure AD B2C para proteger uma API](active-directory-b2c-tutorials-web-api.md)Web ASP.net.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Atualizar o aplicativo no Azure AD B2C
> * Configurar o exemplo para usar o aplicativo
> * Inscrever-se usando o fluxo do usuário

Agora passe para o próximo tutorial para habilitar o recurso de **lista de tarefas pendentes** do aplicativo Web. Nele, você registra um aplicativo de API Web em seu próprio locatário Azure AD B2C e, em seguida, modifica o exemplo de código para usar seu locatário para autenticação de API.

> [!div class="nextstepaction"]
> [Tutorial: Use Azure Active Directory B2C para proteger uma API Web ASP.NET >](active-directory-b2c-tutorials-web-api.md)
