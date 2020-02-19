---
title: Adicione a autenticação à sua aplicação UWP
description: Saiba como utilizar as Aplicações Móveis do Serviço de Aplicações Azure para autenticar os utilizadores da sua aplicação Universal Windows Platform (UWP) com fornecedores de identidade como AAD, Google, Facebook, Twitter e Microsoft.
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 32d4313b345964a2db13d68e83f81756a4acf0d9
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77458941"
---
# <a name="add-authentication-to-your-windows-app"></a>Adicione autenticação à sua aplicação Windows
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Descrição geral
Este tópico mostra como adicionar autenticação baseada na nuvem à sua aplicação móvel. Neste tutorial, adiciona-se autenticação ao projeto quickstart da Plataforma Universal Windows (UWP) para Aplicações Móveis utilizando um fornecedor de identidade que é suportado pelo Azure App Service. Depois de ter sido autenticado e autorizado com sucesso pelo seu backend da Aplicação Móvel, o valor de ID do utilizador é apresentado.

Este tutorial baseia-se no arranque rápido das Aplicações Móveis. Primeiro tem de completar o tutorial Iniciar com [Aplicações Móveis.](app-service-mobile-windows-store-dotnet-get-started.md)

## <a name="register"></a>Registe a sua aplicação para autenticação e configure o Serviço de Aplicações
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicione a sua aplicação aos URLs De Redirecionamento Externo Permitidos

A autenticação segura requer que defina um novo esquema de URL para a sua aplicação. Isto permite que o sistema de autenticação redirecione para a sua aplicação uma vez concluído o processo de autenticação. Neste tutorial, usamos o _nome_ de aplicativo do esquema URL em toda a parte. No entanto, pode utilizar qualquer esquema de URL que escolha. Deve ser exclusivo da sua aplicação móvel. Para ativar a reorientação do lado do servidor:

1. No [portal Azure,](https://ms.portal.azure.com)selecione o seu Serviço de Aplicações.

2. Clique na opção de **menu Autenticação/Autorização.**

3. Nos **URLs de Redirecionamento Externo Permitidos,** introduza `url_scheme_of_your_app://easyauth.callback`.  O **url_scheme_of_your_app** nesta cadeia é o URL Scheme para a sua aplicação móvel.  Deve seguir a especificação normal do URL para um protocolo (utilize apenas letras e números, e comece com uma letra).  Deve tomar nota da corda que escolher, pois terá de ajustar o seu código de aplicação móvel com o URL Scheme em vários locais.

4. Clique em **Guardar**.

## <a name="permissions"></a>Restringir permissões a utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Agora, pode verificar se o acesso anónimo ao seu backend foi desativado. Com o projeto de aplicação UWP definido como o projeto de arranque, implemente e execute a app; verificar se uma exceção não tratada com um código de estado de 401 (Não autorizado) é levantada após o início da aplicação. Isto acontece porque a aplicação tenta aceder ao seu Código de Aplicações Móveis como um utilizador não autenticado, mas a tabela *TodoItem* agora requer autenticação.

Em seguida, irá atualizar a aplicação para autenticar os utilizadores antes de solicitar recursos do seu Serviço de Aplicações.

## <a name="add-authentication"></a>Adicionar autenticação à app
1. No ficheiro do projeto da aplicação UWP MainPage.xaml.cs e adicione o seguinte código:
   
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
   
    Este código autentica o utilizador com um login no Facebook. Se estiver a utilizar um fornecedor de identidade que não seja o Facebook, altere o valor do **MobileServiceAuthenticationProvider** acima do valor para o seu fornecedor.
2. Substitua o método **OnNavigatedTo()** em MainPage.xaml.cs. Em seguida, irá adicionar um botão **'Sinal'** à aplicação que aciona a autenticação.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. Adicione o seguinte código de corte ao MainPage.xaml.cs:
   
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
4. Abra o ficheiro do projeto MainPage.xaml, localize o elemento que define o botão **Guardar** e substitua-o pelo seguinte código:
   
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
5. Adicione o seguinte fragmento de código ao App.xaml.cs:

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
6. Abra o pacote.appxmanifest, navegue para **Declarações,** na lista de desaposição de **Declarações Disponíveis,** selecione **Protocolo** e clique em **Adicionar** botão. Agora configure as **propriedades** da declaração do **Protocolo.** No **nome Display,** adicione o nome que pretende exibir aos utilizadores da sua aplicação. Em **Nome,** adicione o seu {url_scheme_of_your_app}.
7. Prima a tecla F5 para executar a aplicação, clique no botão **'Sinal'** e assine na aplicação com o seu fornecedor de identidade escolhido. Depois de o seu sessão ter sucesso, a aplicação corre sem erros e é capaz de consultar o seu backend e fazer atualizações para os dados.

## <a name="tokens"></a>Guarde o símbolo de autenticação no cliente
O exemplo anterior mostrou um login padrão, que requer que o cliente contacte o fornecedor de identidade e o Serviço de Aplicações sempre que a app começa. Não só este método é ineficiente, como pode encontrar problemas relacionados com o uso caso muitos clientes tentem iniciar a sua aplicação ao mesmo tempo. Uma melhor abordagem é cache o token de autorização devolvido pelo seu Serviço de Aplicações e tentar usá-lo primeiro antes de usar um sign-in baseado no fornecedor.

> [!NOTE]
> Pode cache o símbolo emitido pela App Services independentemente de estar a usar a autenticação gerida pelo cliente ou gerida pelo serviço. Este tutorial utiliza a autenticação gerida pelo serviço.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Passos seguintes
Agora que completou este tutorial básico de autenticação, considere continuar a um dos seguintes tutoriais:

* [Adicionar notificações push à aplicação](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Saiba como adicionar suporte de notificações push à aplicação e configurar o back-end da Aplicação Móvel para utilizar Notification Hubs do Azure para enviar notificações push.
* [Permitir sincronização offline para a sua aplicação](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Saiba como adicionar suporte offline à aplicação utilizando um back-end de Aplicação Móvel. A sincronização offline permite que os utilizadores finais interajam com uma aplicação móvel &mdash; visualizar, adicionar ou modificar dados &mdash;, mesmo quando não existe qualquer ligação de rede.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
