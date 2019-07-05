---
title: Adicionar autenticação à sua aplicação plataforma Universal do Windows (UWP) | Documentos da Microsoft
description: 'Saiba como utilizar aplicações de Mobile do serviço de aplicações do Azure para autenticar os utilizadores da sua aplicação plataforma Universal do Windows (UWP) utilizando uma variedade de fornecedores de identidade, incluindo: AAD, Google, Facebook, Twitter e Microsoft.'
services: app-service\mobile
documentationcenter: windows
author: elamalani
manager: panarasi
editor: ''
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 9a3a6a8d063836094c1285a5ab4c17ee403fc347
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440241"
---
# <a name="add-authentication-to-your-windows-app"></a>Adicionar autenticação à sua aplicação do Windows
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center está a investir em serviços de novo e integrados essenciais para o desenvolvimento de aplicações móveis. Os desenvolvedores podem usar **crie**, **teste** e **distribuir** serviços para configurar os pipelines de integração e entrega contínuas. Assim que a aplicação é implementada, os programadores podem monitorizar o estado e a utilização da sua aplicação com o **Analytics** e **diagnóstico** serviços e interaja com os utilizadores que utilizam o **Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados de aplicações na cloud. Confira [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-windows-store-dotnet-get-started-users) hoje mesmo.
>

## <a name="overview"></a>Descrição geral
Este tópico mostra-lhe como adicionar a autenticação baseada na cloud à sua aplicação móvel. Neste tutorial, vai adicionar autenticação ao projeto de início rápido da plataforma Universal do Windows (UWP) para aplicações móveis através de um fornecedor de identidade que é suportado pelo App Service do Azure. Após com êxito a ser autenticadas e autorizadas pela sua back-end de aplicação móvel, é apresentado o valor de ID de utilizador.

Este tutorial baseia-se no manual de início rápido das aplicações móveis. Primeiro tem de concluir o tutorial [introdução às Mobile Apps](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register"></a>Registar a sua aplicação para autenticação e configurar o serviço de aplicações
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar a sua aplicação para os URLs de redirecionamento externo permitidos

Autenticação segura requer que defina um novo esquema de URL para a sua aplicação. Isso permite que o sistema de autenticação redirecionar para a sua aplicação, uma vez concluído o processo de autenticação. Neste tutorial, utilizamos o esquema de URL _appname_ em todo. No entanto, pode utilizar qualquer esquema de URL que escolher. Deve ser exclusivo para a sua aplicação móvel. Para ativar o redirecionamento no lado do servidor:

1. Na [portal do Azure](https://ms.portal.azure.com), selecione o serviço de aplicações.

2. Clique nas **autenticação / autorização** opção de menu.

3. Na **permitido URLs de redirecionamento externo**, introduza `url_scheme_of_your_app://easyauth.callback`.  O **url_scheme_of_your_app** nessa cadeia é o esquema de URL para a sua aplicação móvel.  Deve seguir normal especificação de URL para um protocolo (utilize letras e números apenas e começar com uma letra).  Deve tome nota da cadeia de caracteres que escolha, como precisará ajustar o código da aplicação móvel com o esquema de URL em vários locais.

4. Clique em **Guardar**.

## <a name="permissions"></a>Restringir as permissões para utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Agora, pode verificar que o acesso anônimo ao seu back-end foi desabilitado. Com o projeto de aplicação UWP definir como projeto de arranque, implementar e executar a aplicação; Certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é gerada depois da aplicação for iniciada. Isto acontece porque a aplicação tenta aceder ao seu código da aplicação móvel como um utilizador não autenticado, mas a *TodoItem* tabela agora requer autenticação.

Em seguida, irá atualizar a aplicação para autenticar os utilizadores antes de solicitar recursos a partir do seu serviço de aplicações.

## <a name="add-authentication"></a>Adicionar autenticação à aplicação
1. O UWP aplicação arquivo MainPage.xaml.cs do projeto e adicione o seguinte fragmento de código:
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    Esse código autentica o utilizador com um início de sessão do Facebook. Se estiver a utilizar um fornecedor de identidade que não seja o Facebook, altere o valor de **MobileServiceAuthenticationProvider** acima para o valor para o seu fornecedor.
2. Substitua a **OnNavigatedTo()** método em MainPage.xaml.cs. Em seguida, adicionará uma **iniciar sessão** botão para a aplicação que aciona a autenticação.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. Adicione o seguinte fragmento de código para o MainPage.xaml.cs:
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. Abra o ficheiro de projeto de mainpage. XAML, localize o elemento que define a **guardar** botão e substituí-lo com o código a seguir:
   
        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>
5. Adicione o seguinte fragmento de código para o App.xaml.cs:

        protected override void OnActivated(IActivatedEventArgs args)
        {
            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                Frame content = Window.Current.Content as Frame;
                if (content.Content.GetType() == typeof(MainPage))
                {
                    content.Navigate(typeof(MainPage), protocolArgs.Uri);
                }
            }
            Window.Current.Activate();
            base.OnActivated(args);
        }
6. Abra o arquivo Package appxmanifest, navegue para **declarações**, na **declarações disponíveis** lista pendente, selecione **protocolo** e clique em **adicionar** botão. Agora configurar o **propriedades** da **protocolo** declaração. Na **nome a apresentar**, adicione o nome que pretende apresentar aos utilizadores da sua aplicação. Na **nome**, adicione {url_scheme_of_your_app}.
7. Prima a tecla F5 para executar a aplicação, clique nas **iniciar sessão** botão e inicie sessão na aplicação com o fornecedor de identidade escolhido. Depois do início de sessão for bem-sucedida, a aplicação é executada sem erros e pode consultar o back-end e fazer atualizações aos dados.

## <a name="tokens"></a>Store o token de autenticação do cliente
O exemplo anterior mostrava um padrão de início de sessão, que exige que o cliente contactar o fornecedor de identidade e o serviço de aplicações sempre que a aplicação for iniciada. Não só é ineficiente, pode executar este método para utilização-relacionada com problemas de muitos clientes devem tentar iniciar o aplicativo ao mesmo tempo. Uma abordagem melhor é armazenar em cache o token de autorização devolvido pelo seu serviço de aplicações e tente utilizar nesta primeira antes de utilizar um fornecedor com base no início de sessão.

> [!NOTE]
> Pode colocar em cache o token emitido por serviços de aplicações, independentemente de se está a utilizar a autenticação gerida pelo cliente ou pelo serviço. Este tutorial utiliza a autenticação gerida pelo serviço.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Passos Seguintes
Agora que concluiu este tutorial de autenticação básica, considere continuar em uma das seguintes tutoriais:

* [Adicionar notificações push à aplicação](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Saiba como adicionar suporte de notificações push à aplicação e configurar o back-end da Aplicação Móvel para utilizar Notification Hubs do Azure para enviar notificações push.
* [Permitir sincronização offline para a sua aplicação](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Saiba como adicionar suporte offline à aplicação utilizando um back-end de Aplicação Móvel. A sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel &mdash; visualizar, adicionar ou modificar dados &mdash;, mesmo quando não existe qualquer ligação de rede.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
