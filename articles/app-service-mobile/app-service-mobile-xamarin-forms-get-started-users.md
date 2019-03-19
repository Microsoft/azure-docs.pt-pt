---
title: Introdução à autenticação para aplicações móveis na aplicação de formulários Xamarin | Documentos da Microsoft
description: Saiba como utilizar aplicações móveis para autenticar os utilizadores da sua aplicação Xamarin Forms por meio de diversos fornecedores de identidade, incluindo o AAD, Google, Facebook, Twitter e Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: panarasi
manager: crdun
editor: ''
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: panarasi
ms.openlocfilehash: 1bbd481218128c482769cd6a28910e135c1ce16d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58001046"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Adicionar autenticação à sua aplicação de formulários Xamarin
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Descrição geral
Este tópico mostra-lhe como autenticar os utilizadores de uma aplicação de Mobile do serviço de aplicações da sua aplicação de cliente. Neste tutorial, vai adicionar autenticação ao projeto de início rápido do Xamarin Forms usando um provedor de identidade que é suportado pelo serviço de aplicações. Após a com êxito a ser autenticadas e autorizadas pela sua aplicação móvel, o valor de ID de utilizador é apresentado e poderá aceder aos dados de tabela restrito.

## <a name="prerequisites"></a>Pré-requisitos
Para o melhor resultado com este tutorial, recomendamos que conclua primeiro o [criar uma aplicação Xamarin Forms] [ 1] tutorial. Depois de concluir este tutorial, terá um projeto de formulários Xamarin é uma aplicação de TodoList de várias plataforma.

Se não utilizar o projeto de servidor de início rápido transferido, tem de adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre os pacotes de extensão de servidor, consulte [trabalhar com o SDK do servidor de back-end de .NET para aplicações móveis do Azure][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Registar a sua aplicação para autenticação e configurar os serviços de aplicação
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar a sua aplicação para os URLs de redirecionamento externo permitidos

Autenticação segura requer que defina um novo esquema de URL para a sua aplicação. Isso permite que o sistema de autenticação redirecionar para a sua aplicação, uma vez concluído o processo de autenticação. Neste tutorial, utilizamos o esquema de URL _appname_ em todo. No entanto, pode utilizar qualquer esquema de URL que escolher. Deve ser exclusivo para a sua aplicação móvel. Para ativar o redirecionamento no lado do servidor:

1. Na [portal do Azure][8], selecione o serviço de aplicações.

2. Clique nas **autenticação / autorização** opção de menu.

3. Na **permitido URLs de redirecionamento externo**, introduza `url_scheme_of_your_app://easyauth.callback`.  O **url_scheme_of_your_app** nessa cadeia é o esquema de URL para a sua aplicação móvel.  Deve seguir normal especificação de URL para um protocolo (utilize letras e números apenas e começar com uma letra).  Deve tome nota da cadeia de caracteres que escolha, como precisará ajustar o código da aplicação móvel com o esquema de URL em vários locais.

4. Clique em **OK**.

5. Clique em **Guardar**.

## <a name="restrict-permissions-to-authenticated-users"></a>Restringir as permissões para utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Adicionar autenticação à biblioteca de classes portátil
Aplicações móveis utiliza a [LoginAsync] [ 3] método de extensão no [MobileServiceClient] [ 4] para iniciar sessão de um utilizador com o serviço de aplicações autenticação. Este exemplo utiliza um fluxo de autenticação de servidor gerido que exibe a interface de início de sessão do fornecedor na aplicação. Para obter mais informações, consulte [autenticação de servidor gerido][5]. Para proporcionar uma melhor experiência de utilizador na sua aplicação de produção, deve considerar utilizar em vez disso [autenticação de cliente gerido][6].

Para autenticar com um projeto de formulários Xamarin, definir uma **IAuthenticate** interface na biblioteca de classes portáteis para a aplicação. Em seguida, adicione uma **início de sessão** botão para a interface de utilizador definidos na biblioteca de classe portáteis, o que clique para começar a autenticação. Dados são carregados a partir do back-end de aplicação móvel, após a autenticação com êxito.

Implementar o **IAuthenticate** interface para cada plataforma suportada pela sua aplicação.

1. No Visual Studio ou no Xamarin Studio, abra App.cs do projeto com **portátil** o nome, o que é o projeto de biblioteca de classes portáteis, em seguida, adicione o seguinte `using` instrução:

        using System.Threading.Tasks;
2. No App.cs, adicione as seguintes `IAuthenticate` imediatamente antes de definição de interface a `App` definição de classe.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Para inicializar a interface com uma implementação específica da plataforma, adicione os seguintes membros estáticos para o **aplicação** classe.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Abra TodoList.xaml do projeto de biblioteca de classes portáteis, adicione as seguintes **botão** elemento no *buttonsPanel* elemento de esquema, após o botão existente:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Este botão aciona a autenticação de servidor gerido com o seu back-end de aplicação móvel.
5. Abrir TodoList.xaml.cs do projeto de biblioteca de classes portáteis, em seguida, adicione o seguinte campo para o `TodoList` classe:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. Substitua a **OnAppearing** método com o código a seguir:

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

    Esse código certifica-se de que os dados são atualizados apenas do serviço depois de ter sido autenticado.
7. Adicionar o manipulador a seguir para o **Clicked** evento para o **TodoList** classe:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Guardar as alterações e reconstruir o projeto de biblioteca de classes portáteis verificar sem erros.

## <a name="add-authentication-to-the-android-app"></a>Adicionar autenticação à aplicação Android
Esta secção mostra como implementar o **IAuthenticate** interface no projeto de aplicação Android. Ignore esta secção se não estiver sustentando os dispositivos Android.

1. No Visual Studio ou no Xamarin Studio, clique com botão direito a **droid** , em seguida, do projeto **Set as StartUp Project**.
2. Pressione F5 para iniciar o projeto no depurador, em seguida, certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é gerada depois da aplicação for iniciada. O código 401 é produzido porque o acesso de back-end está limitado a apenas os utilizadores autorizados.
3. Abrir MainActivity.cs no projeto Android e adicione o seguinte `using` instruções:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Atualização do **MainActivity** classe para implementar a **IAuthenticate** interface, da seguinte forma:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Atualização a **MainActivity** classe adicionando um **MobileServiceUser** campo e um **Authenticate** método, que é necessário para o **IAuthenticate** interface, da seguinte forma:

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

    Se estiver a utilizar um fornecedor de identidade que não seja o Facebook, escolha um valor diferente para [MobileServiceAuthenticationProvider][7].

6. Atualização do **androidmanifest. XML** ficheiro adicionando o seguinte XML dentro do `<application>` elemento:

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
    Substitua `{url_scheme_of_your_app}` com o esquema de URL.
7. Adicione o seguinte código para o **OnCreate** método o **MainActivity** classe antes da chamada para `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Este código assegura que o autenticador é inicializado antes das cargas de aplicação.
8. Reconstruir a aplicação, executá-lo e, em seguida, inicie sessão com o fornecedor de autenticação que escolheu e certifique-se de que é capazes de acessar dados como um usuário autenticado.

### <a name="troubleshooting"></a>Resolução de problemas

**A aplicação falhada com o `Java.Lang.NoSuchMethodError: No static method startActivity`**

Em alguns casos, entra em conflito nos pacotes de suporte exibidos como apenas um aviso no Visual studio, mas as falhas da aplicação com esta exceção em tempo de execução. Neste caso, precisa certificar-se de que todos os pacotes de suporte referenciados no seu projeto têm a mesma versão. O [pacote NuGet de Aplicações Móveis do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) tem dependência `Xamarin.Android.Support.CustomTabs` para a plataforma Android. Portanto, se o seu projeto utilizar os pacotes de suporte mais recentes, precisará de instalar diretamente este pacote com a versão necessária para evitar conflitos.

## <a name="add-authentication-to-the-ios-app"></a>Adicionar autenticação à aplicação iOS
Esta secção mostra como implementar o **IAuthenticate** interface no projeto de aplicação iOS. Ignore esta secção se não estiver sustentando os dispositivos iOS.

1. No Visual Studio ou no Xamarin Studio, clique com botão direito a **iOS** , em seguida, do projeto **Set as StartUp Project**.
2. Pressione F5 para iniciar o projeto no depurador, em seguida, certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é gerada depois da aplicação for iniciada. A resposta 401 é produzido porque o acesso de back-end está limitado a apenas os utilizadores autorizados.
3. Abrir AppDelegate.cs no projeto iOS e adicione o seguinte `using` instruções:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Atualização do **AppDelegate** classe para implementar a **IAuthenticate** interface, da seguinte forma:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Atualização a **AppDelegate** classe adicionando um **MobileServiceUser** campo e um **Authenticate** método, que é necessário para o **IAuthenticate** interface, da seguinte forma:

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
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();

            return success;
        }

    Se estiver a utilizar um fornecedor de identidade que não seja o Facebook, escolha um valor diferente para [MobileServiceAuthenticationProvider].
    
6. Atualização do **AppDelegate** classe adicionando a **OpenUrl** sobrecarga de método, da seguinte forma:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. Adicione a seguinte linha de código para o **FinishedLaunching** método antes da chamada para `LoadApplication()`:

        App.Init(this);

    Este código assegura que o autenticador é inicializado antes da aplicação for carregada.

8. Abra o ficheiro info. plist e adicione um **tipo de URL**. Definir o **identificador** para um nome à sua escolha, o **esquemas de URL** para o esquema de URL para a sua aplicação e o **função** como None.

9. Reconstruir a aplicação, executá-lo e, em seguida, inicie sessão com o fornecedor de autenticação que escolheu e certifique-se de que é capazes de acessar dados como um usuário autenticado.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Adicionar autenticação para projetos de aplicativos do Windows 10 (incluindo o telemóvel)
Esta secção mostra como implementar o **IAuthenticate** interface em projetos de aplicativos do Windows 10. Os mesmos passos aplicam-se a projetos de plataforma Universal do Windows (UWP), mas utilizar o **UWP** projeto (com as alterações indicadas). Ignore esta secção se não estiver sustentando dispositivos do Windows.

1. No Visual Studio, clique com botão direito a **UWP** , em seguida, do projeto **Set as StartUp Project**.
2. Pressione F5 para iniciar o projeto no depurador, em seguida, certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é gerada depois da aplicação for iniciada. A resposta 401 acontece porque o acesso de back-end está limitado a apenas os utilizadores autorizados.
3. Abra o MainPage.xaml.cs para o projeto de aplicação do Windows e adicione o seguinte `using` instruções:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Substitua `<your_Portable_Class_Library_namespace>` com o espaço de nomes para a sua biblioteca de classes portátil.
4. Atualização do **MainPage** classe para implementar a **IAuthenticate** interface, da seguinte forma:

        public sealed partial class MainPage : IAuthenticate
5. Atualização a **MainPage** classe adicionando um **MobileServiceUser** campo e um **Authenticate** método, que é necessário para o **IAuthenticate**interface, da seguinte forma:

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

    Se estiver a utilizar um fornecedor de identidade que não seja o Facebook, escolha um valor diferente para [MobileServiceAuthenticationProvider][7].

1. Adicione a seguinte linha de código no construtor para o **MainPage** classe antes da chamada para `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    Substitua `<your_Portable_Class_Library_namespace>` com o espaço de nomes para a sua biblioteca de classes portátil.

3. Se estiver a utilizar **UWP**, adicione as seguintes **OnActivated** método de substituição para o **aplicação** classe:

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(protocolArgs.Uri);
            }
       }

3. Abra o Package. appxmanifest e adicione um **protocolo** declaração. Definir o **nome a apresentar** para um nome à sua escolha e o **nome** para o esquema de URL da sua aplicação.

4. Reconstruir a aplicação, executá-lo e, em seguida, inicie sessão com o fornecedor de autenticação que escolheu e certifique-se de que é capazes de acessar dados como um usuário autenticado.

## <a name="next-steps"></a>Passos Seguintes
Agora que concluiu este tutorial de autenticação básica, considere continuar em uma das seguintes tutoriais:

* [Adicionar notificações push à aplicação](app-service-mobile-xamarin-forms-get-started-push.md)

  Saiba como adicionar suporte de notificações push à aplicação e configurar o back-end da Aplicação Móvel para utilizar Notification Hubs do Azure para enviar notificações push.
* [Permitir sincronização offline para a sua aplicação](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Saiba como adicionar suporte offline à aplicação utilizando um back-end de Aplicação Móvel. Sincronização offline permite que os utilizadores finais interagir com uma aplicação móvel - visualizar, adicionar ou modificar dados, mesmo quando não existe nenhuma ligação de rede.

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
