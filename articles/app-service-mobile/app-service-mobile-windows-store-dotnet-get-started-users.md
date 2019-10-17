---
title: Adicionar autenticação ao seu aplicativo de Plataforma Universal do Windows (UWP) | Microsoft Docs
description: 'Saiba como usar os aplicativos móveis do serviço Azure App para autenticar usuários de seu aplicativo Plataforma Universal do Windows (UWP) usando uma variedade de provedores de identidade, incluindo: AAD, Google, Facebook, Twitter e Microsoft.'
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
ms.openlocfilehash: d5012ccc503e48785e23ff00564bbc9f6735eea8
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388542"
---
# <a name="add-authentication-to-your-windows-app"></a>Adicionar autenticação ao seu aplicativo do Windows
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão geral
Este tópico mostra como adicionar autenticação baseada em nuvem ao seu aplicativo móvel. Neste tutorial, você adicionará autenticação ao projeto de início rápido do Plataforma Universal do Windows (UWP) para aplicativos móveis usando um provedor de identidade com suporte do serviço Azure App. Depois de ser autenticado e autorizado com êxito pelo back-end do aplicativo móvel, o valor da ID de usuário será exibido.

Este tutorial se baseia no início rápido dos aplicativos móveis. Primeiro, você deve concluir o tutorial [introdução aos aplicativos móveis](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register"></a>Registrar seu aplicativo para autenticação e configurar o serviço de aplicativo
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar seu aplicativo às URLs de redirecionamento externo permitidas

A autenticação segura exige que você defina um novo esquema de URL para seu aplicativo. Isso permite que o sistema de autenticação Redirecione para seu aplicativo após a conclusão do processo de autenticação. Neste tutorial, usamos o esquema de URL _AppName_ em todo o. No entanto, você pode usar qualquer esquema de URL que escolher. Ele deve ser exclusivo para seu aplicativo móvel. Para habilitar o redirecionamento no lado do servidor:

1. No [portal do Azure](https://ms.portal.azure.com), selecione o serviço de aplicativo.

2. Clique na opção de menu **autenticação/autorização** .

3. Nas **URLs de redirecionamento externo permitidas**, insira `url_scheme_of_your_app://easyauth.callback`.  O **url_scheme_of_your_app** nesta cadeia de caracteres é o esquema de URL para seu aplicativo móvel.  Ele deve seguir a especificação de URL normal para um protocolo (Use somente letras e números e comece com uma letra).  Você deve anotar a cadeia de caracteres que escolher, pois será necessário ajustar o código do aplicativo móvel com o esquema de URL em vários locais.

4. Clique em **Guardar**.

## <a name="permissions"></a>Restringir permissões a usuários autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Agora, você pode verificar se o acesso anônimo ao seu back-end foi desabilitado. Com o projeto de aplicativo UWP definido como o projeto de inicialização, implante e execute o aplicativo; Verifique se uma exceção sem tratamento com um código de status 401 (não autorizado) é gerada depois que o aplicativo é iniciado. Isso acontece porque o aplicativo tenta acessar o código do aplicativo móvel como um usuário não autenticado, mas a tabela *TodoItem* agora requer autenticação.

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do seu serviço de aplicativo.

## <a name="add-authentication"></a>Adicionar autenticação ao aplicativo
1. No arquivo de projeto de aplicativo UWP MainPage.xaml.cs e adicione o seguinte trecho de código:
   
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
   
    Esse código autentica o usuário com um logon do Facebook. Se você estiver usando um provedor de identidade diferente do Facebook, altere o valor de **MobileServiceAuthenticationProvider** acima para o valor de seu provedor.
2. Substitua o método **OnNavigatedTo ()** em MainPage.XAML.cs. Em seguida, você adicionará um botão **entrar** ao aplicativo que dispara a autenticação.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. Adicione o seguinte trecho de código ao MainPage.xaml.cs:
   
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
4. Abra o arquivo de projeto MainPage. XAML, localize o elemento que define o botão **salvar** e substitua-o pelo código a seguir:
   
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
5. Adicione o seguinte trecho de código ao App.xaml.cs:

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
6. Abra o arquivo Package. appxmanifest, navegue até **declarações**, na lista suspensa **declarações disponíveis** , selecione **protocolo** e clique no botão **Adicionar** . Agora, configure as **Propriedades** da declaração de **protocolo** . Em **nome de exibição**, adicione o nome que você deseja exibir aos usuários do seu aplicativo. Em **nome**, adicione seu {url_scheme_of_your_app}.
7. Pressione a tecla F5 para executar o aplicativo, clique no botão **entrar** e entre no aplicativo com o provedor de identidade escolhido. Depois que sua entrada for bem-sucedida, o aplicativo será executado sem erros e você poderá consultar seu back-end e fazer atualizações nos dados.

## <a name="tokens"></a>Armazenar o token de autenticação no cliente
O exemplo anterior mostrou uma entrada padrão, que exige que o cliente contate o provedor de identidade e o serviço de aplicativo toda vez que o aplicativo for iniciado. Não só esse método é ineficiente, você pode se deparar com problemas relacionados ao uso, caso muitos clientes tentem iniciar seu aplicativo ao mesmo tempo. Uma abordagem melhor é armazenar em cache o token de autorização retornado pelo serviço de aplicativo e tentar usá-lo primeiro antes de usar um logon baseado em provedor.

> [!NOTE]
> Você pode armazenar em cache o token emitido por serviços de aplicativos, independentemente de estar usando a autenticação gerenciada pelo cliente ou de serviço. Este tutorial usa a autenticação gerenciada por serviço.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Passos seguintes
Agora que você concluiu este tutorial de autenticação básica, considere continuar com um dos seguintes tutoriais:

* [Adicionar notificações push à aplicação](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Saiba como adicionar suporte de notificações push à aplicação e configurar o back-end da Aplicação Móvel para utilizar Notification Hubs do Azure para enviar notificações push.
* [Permitir sincronização offline para a sua aplicação](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Saiba como adicionar suporte offline à aplicação utilizando um back-end de Aplicação Móvel. A sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel &mdash; visualizar, adicionar ou modificar dados &mdash;, mesmo quando não existe qualquer ligação de rede.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
