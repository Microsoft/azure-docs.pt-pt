---
title: Introdução à autenticação no Xamarin iOS
description: Saiba como usar aplicativos móveis para autenticar usuários de seu aplicativo Xamarin iOS com provedores de identidade como AAD, Google, Facebook, Twitter e Microsoft.
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 0f2c78c3d4b18e7c662c4f7345938ddab377229b
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668270"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Adicionar autenticação à sua aplicação Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se quiser integrar serviços cloud na sua aplicação móvel, inscreva-se no [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão geral

Este tópico mostra como autenticar usuários de um aplicativo móvel do serviço de aplicativo do seu aplicativo cliente. Neste tutorial, você adiciona autenticação ao projeto de início rápido do Xamarin. iOS usando um provedor de identidade com suporte do serviço de aplicativo. Depois de ser autenticado e autorizado com êxito pelo seu aplicativo móvel, o valor da ID de usuário será exibido e você poderá acessar os dados da tabela restrita.

Primeiro, você deve concluir o tutorial [criar um aplicativo Xamarin. Ios]. Se você não usar o projeto baixado do servidor de início rápido, deverá adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão de servidor, consulte [trabalhar com o SDK do servidor de back-end do .net para aplicativos móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrar seu aplicativo para autenticação e configurar os serviços de aplicativos
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Adicionar seu aplicativo às URLs de redirecionamento externo permitidas

A autenticação segura exige que você defina um novo esquema de URL para seu aplicativo. Isso permite que o sistema de autenticação Redirecione para seu aplicativo após a conclusão do processo de autenticação. Neste tutorial, usamos o esquema de URL _AppName_ em todo o. No entanto, você pode usar qualquer esquema de URL que escolher. Ele deve ser exclusivo para seu aplicativo móvel. Para habilitar o redirecionamento no lado do servidor:

1. No [portal do Azure](https://portal.azure.com/), selecione o serviço de aplicativo.

2. Clique na opção de menu **autenticação/autorização** .

3. Nas **URLs de redirecionamento externo permitidas**, insira `url_scheme_of_your_app://easyauth.callback`.  O **url_scheme_of_your_app** nesta cadeia de caracteres é o esquema de URL para seu aplicativo móvel.  Ele deve seguir a especificação de URL normal para um protocolo (Use somente letras e números e comece com uma letra).  Você deve anotar a cadeia de caracteres que escolher, pois será necessário ajustar o código do aplicativo móvel com o esquema de URL em vários locais.

4. Clique em **OK**.

5. Clique em **Guardar**.

## <a name="restrict-permissions-to-authenticated-users"></a>Restringir permissões a usuários autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* No Visual Studio ou Xamarin Studio, execute o projeto do cliente em um dispositivo ou emulador. Verifique se uma exceção sem tratamento com um código de status 401 (não autorizado) é gerada depois que o aplicativo é iniciado. A falha é registrada no console do depurador. Portanto, no Visual Studio, você deve ver a falha na janela de saída.

    Essa falha não autorizada ocorre porque o aplicativo tenta acessar o back-end do aplicativo móvel como um usuário não autenticado. A tabela *TodoItem* agora requer autenticação.

Em seguida, você atualizará o aplicativo cliente para solicitar recursos do back-end do aplicativo móvel com um usuário autenticado.

## <a name="add-authentication-to-the-app"></a>Adicionar autenticação ao aplicativo
Nesta seção, você modificará o aplicativo para exibir uma tela de logon antes de exibir os dados. Quando o aplicativo for iniciado, ele não se conectará ao serviço de aplicativo e não exibirá nenhum dado. Após a primeira vez que o usuário executar o gesto de atualização, a tela de logon será exibida; após o logon bem-sucedido, a lista de itens de tarefas será exibida.

1. No projeto cliente, abra o arquivo **QSTodoService.cs** e adicione a instrução using a seguir e `MobileServiceUser` com acessador à classe QSTodoService:

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. Adicione um novo método chamado **Authenticate** ao **QSTodoService** com a seguinte definição:

    ```csharp
    public async Task Authenticate(UIViewController view)
    {
        try
        {
            AppDelegate.ResumeWithURL = url => url.Scheme == "{url_scheme_of_your_app}" && client.ResumeWithURL(url);
            user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }
    ```

    > [!NOTE]
    > Se você estiver usando um provedor de identidade diferente de um Facebook, altere o valor passado para **LoginAsync** acima para um dos seguintes: _MicrosoftAccount_, _Twitter_, _Google_ou _WindowsAzureActiveDirectory_.

3. Abra **QSTodoListViewController.cs**. Modifique a definição do método de **ViewDidLoad** removendo a chamada para **RefreshAsync ()** próximo ao final:

    ```csharp
    public override async void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        todoService = QSTodoService.DefaultService;
        await todoService.InitializeStoreAsync();

        RefreshControl.ValueChanged += async (sender, e) => {
            await RefreshAsync();
        }

        // Comment out the call to RefreshAsync
        // await RefreshAsync();
    }
    ```

4. Modifique o método **RefreshAsync** para autenticar se a propriedade de **usuário** for nula. Adicione o seguinte código na parte superior da definição do método:

    ```csharp
    // start of RefreshAsync method
    if (todoService.User == null) {
        await QSTodoService.DefaultService.Authenticate(this);
        if (todoService.User == null) {
            Console.WriteLine("couldn't login!!");
            return;
        }
    }
    // rest of RefreshAsync method
    ```

5. Abra **AppDelegate.cs**, adicione o seguinte método:

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. Abra o arquivo **info. plist** , navegue até **tipos de URL** na seção **avançado** . Agora, configure o **identificador** e os **esquemas de URL** do tipo de URL e clique em **Adicionar tipo de URL**. Os **esquemas de URL** devem ser os mesmos que o {url_scheme_of_your_app}.
7. No Visual Studio, conectado ao seu host ou Visual Studio para Mac do Mac, execute o projeto do cliente direcionando um dispositivo ou emulador. Verifique se o aplicativo não exibe dados.

    Execute o gesto de atualização puxando a lista de itens para baixo, o que fará com que a tela de logon seja exibida. Depois de inserir credenciais válidas com êxito, o aplicativo exibirá a lista de itens de tarefas pendentes e você poderá fazer atualizações nos dados.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Criar um aplicativo Xamarin. iOS]: app-service-mobile-xamarin-ios-get-started.md
