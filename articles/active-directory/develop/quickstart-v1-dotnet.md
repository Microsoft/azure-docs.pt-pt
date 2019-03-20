---
title: Iniciar sessão dos utilizadores e chamar a API do Microsoft Graph a partir de uma aplicação de ambiente de trabalho .NET (WPF) | Documentos da Microsoft
description: Saiba como criar uma aplicação de ambiente de trabalho do .NET Windows que se integra com o Azure AD para início de sessão e chamadas do Azure AD protegidos APIs com o OAuth 2.0.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 292fa61ae87d87f27b988d250b694e05f6fa1d3d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58119433"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>Início rápido: Iniciar sessão dos utilizadores e chamar a API do Microsoft Graph a partir de uma aplicação de ambiente de trabalho .NET (WPF)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Para .NET clientes nativos que precisam de aceder a recursos protegidos, o Azure Active Directory (Azure AD) fornece o Active Directory Authentication Library (ADAL). ADAL torna mais fácil para a sua aplicação obter os tokens de acesso. 

Neste início rápido, irá aprender como criar uma aplicação de lista de tarefas de WPF do .NET que:

* Obtém acesso tokens para chamar a API do Azure AD Graph utilizando o protocolo de autenticação OAuth 2.0.
* Procura um diretório para os utilizadores com um alias de determinado.
* Utilizadores de sinais horizontalmente.

Para criar a aplicação funcional e completa, precisa de:

1. Registar a aplicação com o Azure AD.
2. Instalar e configurar a ADAL.
3. Utilizar a ADAL para obter os tokens do Azure AD.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, conclua estes pré-requisitos:

* [Transferir a estrutura de aplicação](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) ou [transferir o exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip)
* Ter um inquilino do Azure AD, onde pode criar utilizadores e registar uma aplicação. Se ainda não tiver um inquilino, [saiba como obter um](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Passo 1: Registar a aplicação de DirectorySearcher

Para ativar a sua aplicação obter os tokens, registar a sua aplicação no seu inquilino do Azure AD e conceder permissão para aceder à API do Azure AD Graph:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na barra superior, selecione a sua conta e, no **Directory** lista, escolha o inquilino do Active Directory em que deseja registar a sua aplicação.
3. Selecione no **todos os serviços** na barra de navegação esquerda e escolha **Azure Active Directory**.
4. No **registos de aplicações**, escolha **Add**.
5. Siga as instruções e crie um novo **nativo** aplicação cliente.
    * O **nome** da aplicação irá descrever seu aplicativo para os utilizadores finais
    * O **Uri de redirecionamento** é uma combinação de esquema e a cadeia de caracteres que o Azure AD irá utilizar para devolver respostas token. Introduza um valor específico para seu aplicativo, por exemplo, `http://DirectorySearcher`.

6. Depois de concluir o registo, AAD vai atribuir a aplicação um ID de aplicação exclusivo. Precisará desse valor nas próximas seções, por isso, copie-o partir da página de aplicativo.
7. Do **configurações** página, selecione **permissões obrigatórias** e escolha **Add**. Selecione **Microsoft Graph** como a API e, em **permissões delegadas** adicionar o **ler dados do diretório** permissão. Definir esta permissão permite que seu aplicativo consultar a Graph API para os utilizadores.

## <a name="step-2-install-and-configure-adal"></a>Passo 2: Instale e configure a ADAL

Agora que já tem uma aplicação no Azure AD, pode instalar a ADAL e escrever o seu código relacionado com identidade. Para que a ADAL comunique com o Azure AD, terá de fornecer algumas informações sobre o registo da sua aplicação.

1. Comece por adicionar a ADAL para o `DirectorySearcher` utilizando a consola do Gestor de pacotes do projeto.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

1. Na `DirectorySearcher` projeto aberto `app.config`.
1. Substitua os valores dos elementos no `<appSettings>` secção para refletir os valores de entrada no portal do Azure. O seu código vai fazer referência a estes valores sempre que utilizar a ADAL.
   * O `ida:Tenant` é o domínio de inquilino do Azure AD, por exemplo, contoso.onmicrosoft.com
   * O `ida:ClientId` é o ID de cliente da sua aplicação que copiou do portal.
   * O `ida:RedirectUri` é o URL de redirecionamento registados no portal.

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>Passo 3: Utilizar a ADAL para obter os tokens do Azure AD

O princípio básico por trás da ADAL é que sempre que a sua aplicação precisa de um token de acesso, seu aplicativo simplesmente chama `authContext.AcquireTokenAsync(...)`, e a ADAL faz o resto.

1. Na `DirectorySearcher` projeto aberto `MainWindow.xaml.cs`.
1. Localize o `MainWindow()` método. 
1. Inicializar a sua aplicação `AuthenticationContext` -da ADAL da classe principal. `AuthenticationContext` é onde passar a ADAL as coordenadas que precisa para comunicar com o Azure AD e dizer a ele como colocar em cache tokens.

    ```csharp
    public MainWindow()
    {
        InitializeComponent();

        authContext = new AuthenticationContext(authority, new FileCache());

        CheckForCachedToken();
    }
    ```

1. Localize a `Search(...)` método, que será chamado quando o usuário seleciona o **pesquisa** botão na IU da aplicação. Este método faz um pedido GET à Graph API do Azure AD para consultar utilizadores cujo UPN comece pelo termo de pesquisa especificado.
1. Para consultar a Graph API, incluem um access_token no `Authorization` cabeçalho do pedido, que é aí que entra ADAL.

    ```csharp
    private async void Search(object sender, RoutedEventArgs e)
    {
        // Validate the Input String
        if (string.IsNullOrEmpty(SearchText.Text))
        {
            MessageBox.Show("Please enter a value for the To Do item name");
            return;
        }

        // Get an Access Token for the Graph API
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
            UserNameLabel.Content = result.UserInfo.DisplayableId;
            SignOutButton.Visibility = Visibility.Visible;
        }
        catch (AdalException ex)
        {
            // An unexpected error occurred, or user canceled the sign in.
            if (ex.ErrorCode != "access_denied")
                MessageBox.Show(ex.Message);

            return;
        }

        ...
    }
    ```

    Quando a aplicação solicita um token ao chamar `AcquireTokenAsync(...)`, ADAL irá tentar devolver um token sem pedir ao utilizador as credenciais.
    * Se ADAL determina que o utilizador tem de iniciar sessão obter um token, ele irá apresentar uma caixa de diálogo de início de sessão, recolher as credenciais do usuário e devolver um token de autenticação com êxito. 
    * Se a ADAL não consegue devolver um token por qualquer motivo, ele irá gerar um `AdalException`.

1. Tenha em atenção que o `AuthenticationResult` objeto contém um `UserInfo` objeto que pode ser usado para recolher informações de seu aplicativo pode necessitar. No DirectorySearcher, `UserInfo` é usado para personalizar a IU da aplicação com o ID. do utilizador
1. Quando o utilizador seleciona o **termine** botão, certifique-se de que a próxima chamada para `AcquireTokenAsync(...)` pedirá ao utilizador para iniciar sessão. Ao limpar a cache de token que pode facilmente fazer isso com ADAL:

    ```csharp
    private void SignOut(object sender = null, RoutedEventArgs args = null)
    {
        // Clear the token cache
        authContext.TokenCache.Clear();

        ...
    }
    ```

    Se o utilizador não clicar o **terminar sessão** botão, terá de manter a sessão do utilizador para a próxima vez que o DirectorySearcher é executada. Quando a aplicação é iniciada, pode verificar o cache de token da ADAL para um token e atualizar a interface do Usuário de maneira adequada.

1. Na `CheckForCachedToken()` método, fazer outra chamada para `AcquireTokenAsync(...)`, desta vez passando a `PromptBehavior.Never` parâmetro. `PromptBehavior.Never` informará ADAL que não deve ser pedido ao utilizador para início de sessão e ADAL em vez disso, deve lançar uma exceção se não conseguir devolver um token.

    ```csharp
    public async void CheckForCachedToken() 
    {
        // As the application starts, try to get an access token without prompting the user. If one exists, show the user as signed in.
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "user_interaction_required")
            {
                // An unexpected error occurred.
                MessageBox.Show(ex.Message);
            }

            // If user interaction is required, proceed to main page without singing the user in.
            return;
        }

        // A valid token is in the cache
        SignOutButton.Visibility = Visibility.Visible;
        UserNameLabel.Content = result.UserInfo.DisplayableId;
    }
    ```

Parabéns! Agora tem um aplicativo WPF de .NET que pode autenticar utilizadores, com segurança chamar as APIs da Web com o OAuth 2.0 e obter informações básicas sobre o utilizador em funcionamento. Caso ainda não o tenha feito, está na altura de preencher o seu inquilino com alguns utilizadores. Executar a aplicação de DirectorySearcher e inicie sessão com um desses usuários. Procure outros utilizadores com base no respetivo UPN. Feche a aplicação e executá-lo novamente. Observe como a sessão do utilizador permanece intacta. Termine sessão e volte a iniciar sessão como outro utilizador.

ADAL torna mais fácil incorporar essas funcionalidades de identidade comum em seu aplicativo. Ele cuida o trabalho sujo para si, incluindo gestão de cache, o suporte de protocolo de OAuth, apresentando ao usuário um início de sessão da interface do Usuário, atualizando tokens expirados e muito mais. Tudo o que precisa realmente de fazer é uma única chamada à API, `authContext.AcquireTokenAsync(...)`.

Para referência, consulte o exemplo concluído (sem os valores de configuração) [no GitHub](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Passos Seguintes

Saiba como proteger uma API web utilizando os tokens de acesso de portador do OAuth 2.0.
> [!div class="nextstepaction"]
> [Proteger uma API com o Azure AD de Web do .NET >>](quickstart-v1-dotnet-webapi.md)
