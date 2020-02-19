---
title: Começar com autenticação no Xamarin iOS
description: Saiba como usar aplicações móveis para autenticar utilizadores da sua aplicação Xamarin iOS com fornecedores de identidade como AAD, Google, Facebook, Twitter e Microsoft.
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 05e936accbcd5c6fa2760c4f8682d907557f23b5
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461322"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Adicione a autenticação à sua aplicação Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Descrição geral

Este tópico mostra como autenticar os utilizadores de uma aplicação móvel do Serviço de Aplicações a partir da sua aplicação cliente. Neste tutorial, adiciona-se autenticação ao projeto de quickstart Xamarin.iOS utilizando um fornecedor de identidade que é apoiado pelo App Service. Depois de ter sido autenticado e autorizado com sucesso pela sua Aplicação Móvel, o valor de ID do utilizador é apresentado e poderá aceder a dados de tabela restritos.

Primeiro deve completar o tutorial [Crie uma app Xamarin.iOS]. Se não utilizar o projeto de servidor de arranque rápido descarregado, tem de adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre os pacotes de extensão do servidor, consulte [Trabalhar com o servidor de backend .NET SDK para Aplicações Móveis Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registe a sua aplicação para autenticação e configure serviços de aplicações
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Adicione a sua aplicação aos URLs De Redirecionamento Externo Permitidos

A autenticação segura requer que defina um novo esquema de URL para a sua aplicação. Isto permite que o sistema de autenticação redirecione para a sua aplicação uma vez concluído o processo de autenticação. Neste tutorial, usamos o _nome_ de aplicativo do esquema URL em toda a parte. No entanto, pode utilizar qualquer esquema de URL que escolha. Deve ser exclusivo da sua aplicação móvel. Para ativar a reorientação do lado do servidor:

1. No [portal Azure,](https://portal.azure.com/)selecione o seu Serviço de Aplicações.

2. Clique na opção de **menu Autenticação/Autorização.**

3. Nos **URLs de Redirecionamento Externo Permitidos,** introduza `url_scheme_of_your_app://easyauth.callback`.  O **url_scheme_of_your_app** nesta cadeia é o URL Scheme para a sua aplicação móvel.  Deve seguir a especificação normal do URL para um protocolo (utilize apenas letras e números, e comece com uma letra).  Deve tomar nota da corda que escolher, pois terá de ajustar o seu código de aplicação móvel com o URL Scheme em vários locais.

4. Clique em **OK**.

5. Clique em **Guardar**.

## <a name="restrict-permissions-to-authenticated-users"></a>Restringir permissões a utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* No Visual Studio ou no Xamarin Studio, execute o projeto do cliente num dispositivo ou emulador. Verifique se uma exceção não tratada com um código de estado de 401 (Não autorizado) é levantada após o início da aplicação. A falha está ligada à consola do desgraçado. Então, no Estúdio Visual, deve ver a falha na janela de saída.

    Esta falha não autorizada acontece porque a aplicação tenta aceder ao seu backend da Aplicação Móvel como um utilizador não autenticado. A tabela *TodoItem* requer agora a autenticação.

Em seguida, irá atualizar a app do cliente para solicitar recursos do backend da Aplicação Móvel com um utilizador autenticado.

## <a name="add-authentication-to-the-app"></a>Adicionar autenticação à app
Nesta secção, irá modificar a aplicação para exibir um ecrã de login antes de apresentar dados. Quando a aplicação começar, não se ligará ao seu Serviço de Aplicações e não apresentará quaisquer dados. Após a primeira vez que o utilizador executa o gesto de atualização, aparecerá o ecrã de login; após o login bem sucedido, a lista de itens todo-o-dia será exibida.

1. No projeto do cliente, abra o ficheiro **QSTodoService.cs** e adicione o seguinte usando a declaração e `MobileServiceUser` com o acessório para a classe QSTodoService:

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
    > Se estiver a utilizar um fornecedor de identidade que não seja um Facebook, altere o valor passado para **O LoginAsync** acima para um dos seguintes: _MicrosoftAccount,_ _Twitter,_ _Google_ou _WindowsAzureActiveDirectory_.

3. Aberto **QSTodoListViewController.cs.** Modificar a definição de método de **ViewDidLoad** removendo a chamada para **RefreshAsync()** perto do fim:

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

4. Modifique o método **RefreshAsync** para autenticar se a propriedade **do Utilizador** for nula. Adicione o seguinte código no topo da definição de método:

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

5. Abrir **AppDelegate.cs,** adicione o seguinte método:

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. Abra o ficheiro **Info.plist,** navegue para **tipos de URL** na secção **Avançada.** Agora configure o **Identificador** e os **esquemas de URL** do seu tipo URL e clique em **Adicionar Tipo URL**. **Os esquemas de URL** devem ser os mesmos que o seu {url_scheme_of_your_app}.
7. No Visual Studio, ligado ao seu Mac Host ou Visual Studio para Mac, executa o projeto do cliente direcionado para um dispositivo ou emulador. Verifique se a aplicação não apresenta dados.

    Execute o gesto de atualização puxando para baixo a lista de itens, o que fará com que o ecrã de login apareça. Uma vez introduzido com sucesso credenciais válidas, a aplicação apresentará a lista de itens todo-o-dia e poderá fazer atualizações para os dados.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Crie uma app Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
