---
title: Introdução à autenticação para aplicações móveis no Xamarin iOS
description: Saiba como utilizar aplicações móveis para autenticar os utilizadores da sua aplicação Xamarin iOS através de uma variedade de fornecedores de identidade, incluindo o AAD, Google, Facebook, Twitter e Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: crdun
ms.openlocfilehash: 132909931291daf3aefddd5e1a44273050d98e06
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62120914"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Adicionar autenticação à sua aplicação Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Este tópico mostra-lhe como autenticar os utilizadores de uma aplicação de Mobile do serviço de aplicações da sua aplicação de cliente. Neste tutorial, vai adicionar autenticação ao projeto de início rápido do xamarin. IOS utilizando um fornecedor de identidade que é suportado pelo serviço de aplicações. Depois de com êxito a ser autenticadas e autorizadas pela sua aplicação móvel, o valor de ID de utilizador é apresentado e poderá aceder aos dados de tabela restrito.

Primeiro tem de concluir o tutorial [criar uma aplicação xamarin. IOS]. Se não utilizar o projeto de servidor de início rápido transferido, tem de adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre os pacotes de extensão de servidor, consulte [trabalhar com o SDK do servidor de back-end de .NET para aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registar a sua aplicação para autenticação e configurar os serviços de aplicação
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Adicionar a sua aplicação para os URLs de redirecionamento externo permitidos

Autenticação segura requer que defina um novo esquema de URL para a sua aplicação. Isso permite que o sistema de autenticação redirecionar para a sua aplicação, uma vez concluído o processo de autenticação. Neste tutorial, utilizamos o esquema de URL _appname_ em todo. No entanto, pode utilizar qualquer esquema de URL que escolher. Deve ser exclusivo para a sua aplicação móvel. Para ativar o redirecionamento no lado do servidor:

1. Na [portal do Azure](https://portal.azure.com/), selecione o serviço de aplicações.

2. Clique nas **autenticação / autorização** opção de menu.

3. Na **permitido URLs de redirecionamento externo**, introduza `url_scheme_of_your_app://easyauth.callback`.  O **url_scheme_of_your_app** nessa cadeia é o esquema de URL para a sua aplicação móvel.  Deve seguir normal especificação de URL para um protocolo (utilize letras e números apenas e começar com uma letra).  Deve tome nota da cadeia de caracteres que escolha, como precisará ajustar o código da aplicação móvel com o esquema de URL em vários locais.

4. Clique em **OK**.

5. Clique em **Guardar**.

## <a name="restrict-permissions-to-authenticated-users"></a>Restringir as permissões para utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* No Visual Studio ou no Xamarin Studio, execute o projeto de cliente num dispositivo ou emulador. Certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é gerada depois da aplicação for iniciada. A falha é registada para a consola do depurador. Portanto, no Visual Studio, deverá ver a falha na janela de saída.

    Esta falha não autorizada acontece porque a aplicação tenta aceder ao seu back-end de aplicação móvel como um usuário não autenticado. O *TodoItem* tabela agora requer autenticação.

Em seguida, atualizar a aplicação de cliente para solicitar recursos de back-end da aplicação móvel com um usuário autenticado.

## <a name="add-authentication-to-the-app"></a>Adicionar autenticação à aplicação
Nesta secção, irá modificar a aplicação para exibir uma tela de início de sessão antes de exibir dados. Quando o aplicativo é iniciado, ele não irá ligar ao seu serviço de aplicações e não apresentará quaisquer dados. Após a primeira vez que o utilizador executa o gesto de atualização, será apresentado o ecrã de início de sessão; após o logon bem-sucedido será apresentada a lista de itens de afazeres.

1. No projeto do cliente, abra o ficheiro **QSTodoService.cs** e adicione a seguinte instrução using e `MobileServiceUser` com acessador à classe QSTodoService:

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. Adicionar o novo método chamado **Authenticate** ao **QSTodoService** com a seguinte definição:

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
    > Se estiver a utilizar um fornecedor de identidade que não seja um Facebook, altere o valor transmitido ao **LoginAsync** acima para um dos seguintes: _MicrosoftAccount_, _Twitter_, _Google_, ou _WindowsAzureActiveDirectory_.

3. Open **QSTodoListViewController.cs**. Modificar a definição de método da **ViewDidLoad** removendo a chamada para **RefreshAsync()** perto do fim:

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

4. Modifique o método **RefreshAsync** para se autenticar caso a **utilizador** propriedade é nula. Adicione o seguinte código na parte superior da definição do método:

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

5. Open **AppDelegate.cs**, adicione o seguinte método:

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. Open **Info. plist** de ficheiros, navegue até **tipos de URL** no **avançadas** secção. Agora configurar o **identificador** e o **esquemas de URL** do seu tipo de URL e clique em **Adicionar tipo de URL**. **Esquemas de URL** deve ser o mesmo que sua {url_scheme_of_your_app}.
7. No Visual Studio, ligada ao seu anfitrião Mac ou o Visual Studio para Mac, execute o projeto de cliente direcionado um dispositivo ou emulador. Certifique-se de que a aplicação não apresenta nenhum dado.

    Execute o gesto de atualização ao extrair a lista de itens, o que fará com que o aparecimento do ecrã de início de sessão. Depois de inserir credenciais válidas, a aplicação irá apresentar a lista de itens de lista de tarefas e que pode efetuar atualizações para os dados.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Criar uma aplicação xamarin. IOS]: app-service-mobile-xamarin-ios-get-started.md
