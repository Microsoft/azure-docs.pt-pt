---
title: Introdução à autenticação para aplicativos móveis no aplicativo Xamarin Forms | Microsoft Docs
description: Saiba como usar aplicativos móveis para autenticar usuários de seu aplicativo Xamarin Forms por meio de uma variedade de provedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 643539fb569cdefba8e04d1ac08e73055624d3ae
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025047"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Adicionar autenticação ao aplicativo Xamarin Forms
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem.
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje.

## <a name="overview"></a>Descrição geral
Este tópico mostra como autenticar usuários de um aplicativo móvel do serviço de aplicativo do seu aplicativo cliente. Neste tutorial, você adicionará autenticação ao projeto de início rápido do Xamarin Forms usando um provedor de identidade com suporte do serviço de aplicativo. Depois de ser autenticado e autorizado com êxito pelo seu aplicativo móvel, o valor da ID de usuário é exibido e você poderá acessar os dados da tabela restrita.

## <a name="prerequisites"></a>Pré-requisitos
Para obter o melhor resultado com este tutorial, recomendamos que você conclua primeiro o tutorial [criar um aplicativo Xamarin Forms][1] . Depois de concluir este tutorial, você terá um projeto do Xamarin Forms que é um aplicativo de todas as várias plataformas.

Se você não usar o projeto baixado do servidor de início rápido, deverá adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão de servidor, consulte [trabalhar com o SDK do servidor de back-end do .net para aplicativos móveis do Azure][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrar seu aplicativo para autenticação e configurar os serviços de aplicativos
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar seu aplicativo às URLs de redirecionamento externo permitidas

A autenticação segura exige que você defina um novo esquema de URL para seu aplicativo. Isso permite que o sistema de autenticação Redirecione para seu aplicativo após a conclusão do processo de autenticação. Neste tutorial, usamos o esquema de URL _AppName_ em todo o. No entanto, você pode usar qualquer esquema de URL que escolher. Ele deve ser exclusivo para seu aplicativo móvel. Para habilitar o redirecionamento no lado do servidor:

1. No [portal do Azure][8], selecione o serviço de aplicativo.

2. Clique na opção de menu **autenticação/autorização** .

3. Nas **URLs de redirecionamento externo permitidas**, insira `url_scheme_of_your_app://easyauth.callback`.  O **url_scheme_of_your_app** nesta cadeia de caracteres é o esquema de URL para seu aplicativo móvel.  Ele deve seguir a especificação de URL normal para um protocolo (Use somente letras e números e comece com uma letra).  Você deve anotar a cadeia de caracteres que escolher, pois será necessário ajustar o código do aplicativo móvel com o esquema de URL em vários locais.

4. Clique em **OK**.

5. Clique em **Guardar**.

## <a name="restrict-permissions-to-authenticated-users"></a>Restringir permissões a usuários autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Adicionar autenticação à biblioteca de classes portátil
Os aplicativos móveis usam o método de extensão [LoginAsync][3] no [MobileServiceClient][4] para conectar um usuário com a autenticação do serviço de aplicativo. Este exemplo usa um fluxo de autenticação gerenciado pelo servidor que exibe a interface de entrada do provedor no aplicativo. Para obter mais informações, consulte [autenticação gerenciada por servidor][5]. Para fornecer uma melhor experiência do usuário em seu aplicativo de produção, considere usar a [autenticação gerenciada pelo cliente][6].

Para autenticar com um projeto Xamarin Forms, defina uma interface **iauthenticate** na biblioteca de classes portátil para o aplicativo. Em seguida, adicione um botão de **entrada** à interface do usuário definida na biblioteca de classes portátil, na qual você clica para iniciar a autenticação. Os dados são carregados do back-end do aplicativo móvel após a autenticação bem-sucedida.

Implemente a interface **iauthenticate** para cada plataforma suportada pelo seu aplicativo.

1. No Visual Studio ou Xamarin Studio, abra App.cs do projeto com **portátil** no nome, que é um projeto de biblioteca de classes portátil e, em seguida, adicione a seguinte instrução `using`:

        using System.Threading.Tasks;
2. No App.cs, adicione a seguinte definição de interface `IAuthenticate` imediatamente antes da definição de classe `App`.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Para inicializar a interface com uma implementação específica da plataforma, adicione os seguintes membros estáticos à classe do **aplicativo** .

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Abra ToDoList. XAML no projeto de biblioteca de classes portátil, adicione o seguinte elemento de **botão** no elemento de layout *buttonsPanel* , após o botão existente:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Esse botão dispara a autenticação gerenciada pelo servidor com o back-end do aplicativo móvel.
5. Abra TodoList.xaml.cs no projeto de biblioteca de classes portátil e adicione o seguinte campo à classe `TodoList`:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Substitua o método **onaparecimento** pelo seguinte código:

        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    Esse código garante que os dados sejam atualizados apenas do serviço depois que você tiver sido autenticado.
7. Adicione o seguinte manipulador para o evento **clicado** à classe **ToDoList** :

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Salve as alterações e reconstrua o projeto de biblioteca de classes portátil, verificando se não há erros.

## <a name="add-authentication-to-the-android-app"></a>Adicionar autenticação ao aplicativo Android
Esta seção mostra como implementar a interface **iauthenticate** no projeto de aplicativo do Android. Ignore esta seção se você não estiver dando suporte a dispositivos Android.

1. No Visual Studio ou Xamarin Studio, clique com o botão direito do mouse no projeto **Droid** e **defina como projeto de inicialização**.
2. Pressione F5 para iniciar o projeto no depurador e, em seguida, verifique se uma exceção sem tratamento com um código de status 401 (não autorizado) é gerada depois que o aplicativo é iniciado. O código 401 é produzido porque o acesso no back-end é restrito somente a usuários autorizados.
3. Abra MainActivity.cs no projeto do Android e adicione as seguintes instruções `using`:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Atualize a classe **MainActivity** para implementar a interface **iauthenticate** , da seguinte maneira:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Atualize a classe **MainActivity** adicionando um campo **MobileServiceUser** e um método **Authenticate** , que é exigido pela interface **iauthenticate** , da seguinte maneira:

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this, 
                    MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }

    Se você estiver usando um provedor de identidade diferente do Facebook, escolha um valor diferente para [MobileServiceAuthenticationProvider][7].

6. Atualize o arquivo **AndroidManifest. xml** adicionando o seguinte XML dentro do elemento `<application>`:

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
      </intent-filter>
    </activity>
    ```
    Substitua `{url_scheme_of_your_app}` pelo seu esquema de URL.
7. Adicione o seguinte código ao método **OnCreate** da classe **MainActivity** antes da chamada para `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Esse código garante que o autenticador seja inicializado antes que o aplicativo seja carregado.
8. Recompile o aplicativo, execute-o e, em seguida, entre com o provedor de autenticação escolhido e verifique se você consegue acessar os dados como um usuário autenticado.

### <a name="troubleshooting"></a>Resolução de problemas

**O aplicativo falhou com `Java.Lang.NoSuchMethodError: No static method startActivity`**

Em alguns casos, os conflitos nos pacotes de suporte são exibidos como apenas um aviso no Visual Studio, mas o aplicativo falha com essa exceção em tempo de execução. Nesse caso, você precisa certificar-se de que todos os pacotes de suporte referenciados no seu projeto tenham a mesma versão. O [pacote NuGet de Aplicações Móveis do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) tem dependência `Xamarin.Android.Support.CustomTabs` para a plataforma Android. Portanto, se o seu projeto utilizar os pacotes de suporte mais recentes, precisará de instalar diretamente este pacote com a versão necessária para evitar conflitos.

## <a name="add-authentication-to-the-ios-app"></a>Adicionar autenticação ao aplicativo iOS
Esta seção mostra como implementar a interface **iauthenticate** no projeto de aplicativo do Ios. Ignore esta seção se não estiver suportando dispositivos iOS.

1. No Visual Studio ou Xamarin Studio, clique com o botão direito do mouse no projeto do **Ios** e **defina como projeto de inicialização**.
2. Pressione F5 para iniciar o projeto no depurador e, em seguida, verifique se uma exceção sem tratamento com um código de status 401 (não autorizado) é gerada depois que o aplicativo é iniciado. A resposta 401 é produzida porque o acesso no back-end é restrito somente a usuários autorizados.
3. Abra AppDelegate.cs no projeto do iOS e adicione as seguintes instruções `using`:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Atualize a classe **AppDelegate** para implementar a interface **iauthenticate** , da seguinte maneira:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Atualize a classe **AppDelegate** adicionando um campo **MobileServiceUser** e um método **Authenticate** , que é exigido pela interface **iauthenticate** , da seguinte maneira:

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;
                    }
                }
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertController avAlert = UIAlertController.Create("Sign-in result", message, UIAlertControllerStyle.Alert);
            avAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
            UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(avAlert, true, null);

            return success;
        }

    Se você estiver usando um provedor de identidade diferente do Facebook, escolha um valor diferente para [MobileServiceAuthenticationProvider].
    
6. Atualize a classe **AppDelegate** adicionando a sobrecarga do método **OpenURL** , da seguinte maneira:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Adicione a seguinte linha de código ao método **FinishedLaunching** antes da chamada para `LoadApplication()`:

        App.Init(this);

    Esse código garante que o autenticador seja inicializado antes que o aplicativo seja carregado.

8. Abra o info. plist e adicione um **tipo de URL**. Defina o **identificador** como um nome de sua escolha, os **esquemas de URL** para o esquema de URL para seu aplicativo e a **função** como nenhum.

9. Recompile o aplicativo, execute-o e, em seguida, entre com o provedor de autenticação escolhido e verifique se você consegue acessar os dados como um usuário autenticado.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Adicionar autenticação a projetos de aplicativo do Windows 10 (incluindo telefone)
Esta seção mostra como implementar a interface **iauthenticate** nos projetos de aplicativo do Windows 10. As mesmas etapas se aplicam a projetos de Plataforma Universal do Windows (UWP), mas usando o projeto **UWP** (com alterações anotadas). Ignore esta seção se não estiver dando suporte a dispositivos Windows.

1. No Visual Studio, clique com o botão direito do mouse no projeto **UWP** e **defina como projeto de inicialização**.
2. Pressione F5 para iniciar o projeto no depurador e, em seguida, verifique se uma exceção sem tratamento com um código de status 401 (não autorizado) é gerada depois que o aplicativo é iniciado. A resposta 401 ocorre porque o acesso no back-end é restrito somente a usuários autorizados.
3. Abra MainPage.xaml.cs para o projeto de aplicativo do Windows e adicione as seguintes instruções `using`:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Substitua `<your_Portable_Class_Library_namespace>` pelo namespace para sua biblioteca de classes portátil.
4. Atualize a classe **MainPage** para implementar a interface **iauthenticate** , da seguinte maneira:

        public sealed partial class MainPage : IAuthenticate
5. Atualize a classe **MainPage** adicionando um campo **MobileServiceUser** e um método **Authenticate** , que é exigido pela interface **iauthenticate** , da seguinte maneira:

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }

            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }

    Se você estiver usando um provedor de identidade diferente do Facebook, escolha um valor diferente para [MobileServiceAuthenticationProvider][7].

1. Adicione a seguinte linha de código no construtor para a classe **MainPage** antes da chamada para `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Substitua `<your_Portable_Class_Library_namespace>` pelo namespace para sua biblioteca de classes portátil.

3. Se você estiver usando a **UWP**, adicione a seguinte substituição de método **OnActivated** à classe **app** :

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                MobileServiceClientExtensions.ResumeWithURL(TodoItemManager.DefaultManager.CurrentClient,protocolArgs.Uri);
            }
       }

3. Abra Package. appxmanifest e adicione uma declaração de **protocolo** . Defina o **nome de exibição** como um nome de sua escolha e o **nome** para o esquema de URL para seu aplicativo.

4. Recompile o aplicativo, execute-o e, em seguida, entre com o provedor de autenticação escolhido e verifique se você consegue acessar os dados como um usuário autenticado.

## <a name="next-steps"></a>Passos seguintes
Agora que você concluiu este tutorial de autenticação básica, considere continuar com um dos seguintes tutoriais:

* [Adicionar notificações push à aplicação](app-service-mobile-xamarin-forms-get-started-push.md)

  Saiba como adicionar suporte de notificações push à aplicação e configurar o back-end da Aplicação Móvel para utilizar Notification Hubs do Azure para enviar notificações push.
* [Permitir sincronização offline para a sua aplicação](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Saiba como adicionar suporte offline à aplicação utilizando um back-end de Aplicação Móvel. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel – exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede.

<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-xamarin-forms-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[4]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[5]: app-service-mobile-dotnet-how-to-use-client-library.md#serverflow
[6]: app-service-mobile-dotnet-how-to-use-client-library.md#clientflow
[7]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx
[8]: https://portal.azure.com
