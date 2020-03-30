---
title: Começar com autenticação na aplicação Xamarin Forms
description: Saiba como usar aplicações móveis para autenticar utilizadores da sua aplicação Xamarin Forms com fornecedores de identidade como AAD, Google, Facebook, Twitter e Microsoft.
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 4788aa50074016a34d906353f5b37dbba85ef104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458771"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Adicione a autenticação à sua aplicação Xamarin Forms
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Descrição geral
Este tópico mostra como autenticar os utilizadores de uma aplicação móvel do Serviço de Aplicações a partir da sua aplicação cliente. Neste tutorial, adiciona-se autenticação ao projeto Quickstart Xamarin Forms utilizando um fornecedor de identidade que é apoiado pelo App Service. Depois de ter sido autenticado e autorizado com sucesso pela sua Aplicação Móvel, o valor de ID do utilizador é apresentado e poderá aceder a dados de tabela restritos.

## <a name="prerequisites"></a>Pré-requisitos
Para o melhor resultado com este tutorial, recomendamos que complete primeiro o tutorial da [aplicação Create a Xamarin Forms.][1] Depois de completar este tutorial, terá um projeto Xamarin Forms que é uma aplicação todoList multiplataforma.

Se não utilizar o projeto de servidor de arranque rápido descarregado, tem de adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre os pacotes de extensão do servidor, consulte [Trabalhar com o servidor de backend .NET SDK para Aplicações Móveis Azure][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registe a sua aplicação para autenticação e configure serviços de aplicações
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Adicione a sua aplicação aos URLs De Redirecionamento Externo Permitidos

A autenticação segura requer que defina um novo esquema de URL para a sua aplicação. Isto permite que o sistema de autenticação redirecione para a sua aplicação uma vez concluído o processo de autenticação. Neste tutorial, usamos o _nome_ de aplicativo do esquema URL em toda a parte. No entanto, pode utilizar qualquer esquema de URL que escolha. Deve ser exclusivo da sua aplicação móvel. Para ativar a reorientação do lado do servidor:

1. No [portal Azure,][8]selecione o seu Serviço de Aplicações.

2. Clique na opção de **menu Autenticação/Autorização.**

3. Nos **URLs de Redirecionamento Externo Permitidos,** introduza `url_scheme_of_your_app://easyauth.callback`.  O **url_scheme_of_your_app** nesta cadeia é o URL Scheme para a sua aplicação móvel.  Deve seguir a especificação normal do URL para um protocolo (utilize apenas letras e números, e comece com uma letra).  Deve tomar nota da corda que escolher, pois terá de ajustar o seu código de aplicação móvel com o URL Scheme em vários locais.

4. Clique em **OK**.

5. Clique em **Guardar**.

## <a name="restrict-permissions-to-authenticated-users"></a>Restringir permissões a utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Adicione a autenticação à biblioteca de classes portáteis
As Aplicações Móveis utilizam o método de extensão [LoginAsync][3] no [MobileServiceClient][4] para assinar em um utilizador com autenticação do Serviço de Aplicações. Esta amostra utiliza um fluxo de autenticação gerido pelo servidor que exibe a interface de entrada do fornecedor na aplicação. Para mais informações, consulte a [autenticação gerida pelo Servidor][5]. Para proporcionar uma melhor experiência de utilizador na sua aplicação de produção, deve considerar em vez disso a [autenticação gerida pelo Cliente.][6]

Para autenticar com um projeto Xamarin Forms, defina uma interface **IAuthenticate** na Biblioteca de Classe Portátil para a aplicação. Em seguida, adicione um botão **de entrada** na interface do utilizador definida na Biblioteca de Classe Portátil, que clica para iniciar a autenticação. Os dados são carregados a partir do backend da aplicação móvel após a autenticação bem sucedida.

Implemente a interface **IAuthenticate** para cada plataforma suportada pela sua aplicação.

1. No Estúdio Visual ou no Estúdio Xamarin, abre App.cs do projeto com **a Portable** `using` no nome, que é o projeto da Biblioteca de Classe Portátil, e depois adicione a seguinte declaração:

        using System.Threading.Tasks;
2. Em App.cs, adicione `IAuthenticate` a seguinte definição de interface imediatamente antes da definição de `App` classe.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Para inicializar a interface com uma implementação específica da plataforma, adicione os seguintes membros estáticos à classe **App.**

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Abra todolist.xaml do projeto da Biblioteca de Classes Portáteis, adicione o seguinte elemento **botão** no elemento de layout do painel de *botões,* após o botão existente:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Este botão aciona a autenticação gerida pelo servidor com o backend da sua aplicação móvel.
5. Abra TodoList.xaml.cs do projeto da Biblioteca de Classes `TodoList` Portáteis e, em seguida, adicione o seguinte campo à classe:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Substitua o método **OnAppearing** pelo seguinte código:

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

    Este código garante que os dados só são atualizados a partir do serviço depois de ter sido autenticado.
7. Adicione o seguinte manipulador para o evento **Cliced** à classe **TodoList:**

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Guarde as suas alterações e reconstrua o projeto da Biblioteca de Classes Portáteis, não verificando erros.

## <a name="add-authentication-to-the-android-app"></a>Adicione autenticação à aplicação Android
Esta secção mostra como implementar a interface **IAuthenticate** no projeto de aplicações Android. Ignore esta secção se não estiver a suportar dispositivos Android.

1. No Estúdio Visual ou no Estúdio Xamarin, clique no projeto **dróide** e, em seguida, **set como StartUp Project**.
2. Pressione f5 para iniciar o projeto no debugger, em seguida, verifique se uma exceção não tratada com um código de estado de 401 (Não autorizado) é levantada após o início da app. O código 401 é produzido porque o acesso no backend é restrito apenas aos utilizadores autorizados.
3. Abra MainActivity.cs no projeto Android `using` e adicione as seguintes declarações:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Atualize a classe **MainActivity** para implementar a interface **IAuthenticate,** da seguinte forma:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Atualize a classe **MainActivity** adicionando um campo **MobileServiceUser** e um método **Authenticate,** que é exigido pela interface **IAuthenticate,** da seguinte forma:

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

    Se estiver a utilizar um fornecedor de identidade que não seja o Facebook, escolha um valor diferente para [mobileServiceAuthenticationProvider][7].

6. Atualize o ficheiro **AndroidManifest.xml** adicionando `<application>` o seguinte XML dentro do elemento:

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
    Substitua-o `{url_scheme_of_your_app}` pelo seu esquema de URL.
7. Adicione o seguinte código ao método **OnCreate** da classe `LoadApplication()` **MainActivity** antes da chamada para:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Este código garante que o autenticador é inicializado antes da carga da aplicação.
8. Reconstruir a app, executá-la, em seguida, iniciar sessão com o fornecedor de autenticação que escolheu e verificar se é capaz de aceder a dados como um utilizador autenticado.

### <a name="troubleshooting"></a>Resolução de problemas

**A aplicação bateu com`Java.Lang.NoSuchMethodError: No static method startActivity`**

Em alguns casos, os conflitos nos pacotes de suporte apresentados como apenas um aviso no estúdio Visual, mas a aplicação falha com esta exceção no tempo de execução. Neste caso, tem de se certificar de que todos os pacotes de suporte referenciados no seu projeto têm a mesma versão. O [pacote NuGet de Aplicações Móveis do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) tem dependência `Xamarin.Android.Support.CustomTabs` para a plataforma Android. Portanto, se o seu projeto utilizar os pacotes de suporte mais recentes, precisará de instalar diretamente este pacote com a versão necessária para evitar conflitos.

## <a name="add-authentication-to-the-ios-app"></a>Adicionar autenticação à aplicação iOS
Esta secção mostra como implementar a interface **IAuthenticate** no projeto da aplicação iOS. Ignore esta secção se não estiver a suportar dispositivos iOS.

1. No Estúdio Visual ou no Estúdio Xamarin, clique no projeto do **iOS** e, em seguida, **set como StartUp Project**.
2. Pressione f5 para iniciar o projeto no debugger, em seguida, verifique se uma exceção não tratada com um código de estado de 401 (Não autorizado) é levantada após o início da app. A resposta 401 é produzida porque o acesso no backend é restrito apenas aos utilizadores autorizados.
3. Abrir AppDelegate.cs no projeto iOS `using` e adicionar as seguintes declarações:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Atualize a classe **AppDelegate** para implementar a interface **IAuthenticate,** da seguinte forma:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Atualize a classe **AppDelegate** adicionando um campo **MobileServiceUser** e um método **Authenticate,** que é exigido pela interface **IAuthenticate,** da seguinte forma:

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

    Se estiver a utilizar um fornecedor de identidade que não seja o Facebook, escolha um valor diferente para [MobileServiceAuthenticationProvider].
    
6. Atualize a classe **AppDelegate** adicionando a sobrecarga do método **OpenUrl,** da seguinte forma:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Adicione a seguinte linha de código ao método `LoadApplication()`De lançamento **acabado** antes da chamada para:

        App.Init(this);

    Este código garante que o autenticador é inicializado antes de a aplicação ser carregada.

8. Abra info.plist e adicione um **tipo de URL**. Defino o **Identificador** para um nome à sua escolha, os **Esquemas de URL** para o esquema de URL para a sua aplicação e o **Papel** para Nenhum.

9. Reconstruir a app, executá-la, em seguida, iniciar sessão com o fornecedor de autenticação que escolheu e verificar se é capaz de aceder a dados como um utilizador autenticado.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Adicionar autenticação aos projetos de aplicações do Windows 10 (incluindo telefone)
Esta secção mostra como implementar a interface **IAuthenticate** nos projetos da aplicação Windows 10. Os mesmos passos aplicam-se aos projetos da Universal Windows Platform (UWP), mas utilizando o projeto **UWP** (com alterações notadas). Ignore esta secção se não estiver a suportar dispositivos Windows.

1. No Estúdio Visual, clique no projeto **UWP** e, em seguida, **set as StartUp Project**.
2. Pressione f5 para iniciar o projeto no debugger, em seguida, verifique se uma exceção não tratada com um código de estado de 401 (Não autorizado) é levantada após o início da app. A resposta 401 acontece porque o acesso no backend é restrito apenas aos utilizadores autorizados.
3. Abra MainPage.xaml.cs para o projeto de `using` aplicação Windows e adicione as seguintes declarações:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Substitua-o `<your_Portable_Class_Library_namespace>` pelo espaço de nome para a sua biblioteca de classe portátil.
4. Atualize a classe **MainPage** para implementar a interface **IAuthenticate,** da seguinte forma:

        public sealed partial class MainPage : IAuthenticate
5. Atualize a classe **MainPage** adicionando um campo **MobileServiceUser** e um método **Authenticate,** que é exigido pela interface **IAuthenticate,** da seguinte forma:

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

    Se estiver a utilizar um fornecedor de identidade que não seja o Facebook, escolha um valor diferente para [mobileServiceAuthenticationProvider][7].

1. Adicione a seguinte linha de código no construtor para a `LoadApplication()`classe **MainPage** antes da chamada para:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Substitua-o `<your_Portable_Class_Library_namespace>` pelo espaço de nome para a sua biblioteca de classe portátil.

3. Se estiver a utilizar **o UWP,** adicione o seguinte método **OnActivated** sobreposição à classe **App:**

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                MobileServiceClientExtensions.ResumeWithURL(TodoItemManager.DefaultManager.CurrentClient,protocolArgs.Uri);
            }
       }

3. Open Package.appxmanifest e adicione uma declaração **protocolar.** Delineie o **nome Display** para um nome à sua escolha e o **nome** para o esquema DEURL para a sua aplicação.

4. Reconstruir a app, executá-la, em seguida, iniciar sessão com o fornecedor de autenticação que escolheu e verificar se é capaz de aceder a dados como um utilizador autenticado.

## <a name="next-steps"></a>Passos seguintes
Agora que completou este tutorial básico de autenticação, considere continuar a um dos seguintes tutoriais:

* [Adicionar notificações push à aplicação](app-service-mobile-xamarin-forms-get-started-push.md)

  Saiba como adicionar suporte de notificações push à aplicação e configurar o back-end da Aplicação Móvel para utilizar Notification Hubs do Azure para enviar notificações push.
* [Permitir sincronização offline para a sua aplicação](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Saiba como adicionar suporte offline à aplicação utilizando um back-end de Aplicação Móvel. A sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel - visualização, adição ou modificação de dados - mesmo quando não há ligação de rede.

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
