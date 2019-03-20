---
title: Introdução à autenticação para aplicações móveis no Xamarin Android
description: Saiba como utilizar aplicações móveis para autenticar os utilizadores da sua aplicação Xamarin Android através de uma variedade de fornecedores de identidade, incluindo o AAD, Google, Facebook, Twitter e Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: panarasi
editor: ''
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: panarasi
ms.openlocfilehash: 0a2d964d60d13f0e71de5776112a4edbe3cdcc45
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993917"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Adicionar autenticação à sua aplicação xamarin. Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Este tópico mostra-lhe como autenticar os utilizadores de uma aplicação móvel da sua aplicação de cliente. Neste tutorial, vai adicionar autenticação ao projeto de início rápido, utilizando um fornecedor de identidade que é suportado pelo Azure Mobile Apps. Depois de com êxito a ser autenticado e autorizado na aplicação móvel, é apresentado o valor de ID de utilizador.

Este tutorial baseia-se o guia de introdução da aplicação móvel. Também primeiro tem de concluir o tutorial [criar uma aplicação xamarin. Android]. Se não utilizar o projeto de servidor de início rápido transferido, tem de adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre os pacotes de extensão de servidor, consulte [trabalhar com o SDK do servidor de back-end de .NET para aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Registar a sua aplicação para autenticação e configurar os serviços de aplicação
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar a sua aplicação para os URLs de redirecionamento externo permitidos

Autenticação segura requer que defina um novo esquema de URL para a sua aplicação. Isso permite que o sistema de autenticação redirecionar para a sua aplicação, uma vez concluído o processo de autenticação. Neste tutorial, utilizamos o esquema de URL _appname_ em todo. No entanto, pode utilizar qualquer esquema de URL que escolher. Deve ser exclusivo para a sua aplicação móvel. Para ativar o redirecionamento no lado do servidor:

1. No [portal do Azure], selecione o serviço de aplicações.

2. Clique nas **autenticação / autorização** opção de menu.

3. Na **permitido URLs de redirecionamento externo**, introduza `url_scheme_of_your_app://easyauth.callback`.  O **url_scheme_of_your_app** nessa cadeia é o esquema de URL para a sua aplicação móvel.  Deve seguir normal especificação de URL para um protocolo (utilize letras e números apenas e começar com uma letra).  Deve tome nota da cadeia de caracteres que escolha, como precisará ajustar o código da aplicação móvel com o esquema de URL em vários locais.

4. Clique em **OK**.

5. Clique em **Guardar**.

## <a name="permissions"></a>Restringir as permissões para utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

No Visual Studio ou no Xamarin Studio, execute o projeto de cliente num dispositivo ou emulador. Certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é gerada depois da aplicação for iniciada. Isso acontece porque a aplicação tenta aceder ao seu back-end de aplicação móvel como um usuário não autenticado. O *TodoItem* tabela agora requer autenticação.

Em seguida, atualizar a aplicação de cliente para solicitar recursos de back-end da aplicação móvel com um usuário autenticado.

## <a name="add-authentication"></a>Adicionar autenticação à aplicação
A aplicação é atualizada para exigir que os utilizadores tocar a **iniciar sessão** botão e autenticar antes dos dados são exibidos.

1. Adicione o seguinte código para o **TodoActivity** classe:
   
        // Define an authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");
   
                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }
   
        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;
   
                // Load the data.
                OnRefreshItemsSelected();
            }
        }
   
    Esta ação cria um novo método para autenticar um usuário e um manipulador de método para um novo **iniciar sessão** botão. O utilizador no código de exemplo acima é autenticado com um início de sessão do Facebook. Uma caixa de diálogo é utilizada para apresentar o ID de utilizador após a autenticação.
   
   > [!NOTE]
   > Se estiver a utilizar um fornecedor de identidade que não seja o Facebook, altere o valor transmitido ao **LoginAsync** acima para um dos seguintes: *MicrosoftAccount*, *Twitter*, *Google*, ou *WindowsAzureActiveDirectory*.
   > 
   > 
2. Na **OnCreate** método, eliminar ou comente a seguinte linha de código:
   
        OnRefreshItemsSelected ();
3. No ficheiro Activity_To_Do.axml, adicione as seguintes *LoginUser* botão definição antes do existente *AddItem* botão:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Adicione o seguinte elemento no arquivo de recursos de strings:
   
        <string name="login_button_text">Sign in</string>
5. Abra o ficheiro Androidmanifest XML, adicione o seguinte código dentro `<application>` elemento XML:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. No Visual Studio ou no Xamarin Studio, execute o projeto de cliente num dispositivo ou emulador e inicie sessão com o fornecedor de identidade escolhido. Quando estiver registado com êxito, a aplicação irá apresentar o ID de início de sessão e a lista de itens de lista de tarefas e que pode efetuar atualizações para os dados.

## <a name="troubleshooting"></a>Resolução de problemas

**A aplicação falhada com o `Java.Lang.NoSuchMethodError: No static method startActivity`**

Em alguns casos, entra em conflito nos pacotes de suporte exibidos como apenas um aviso no Visual studio, mas as falhas da aplicação com esta exceção em tempo de execução. Neste caso, precisa certificar-se de que todos os pacotes de suporte referenciados no seu projeto têm a mesma versão. O [pacote NuGet de Aplicações Móveis do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) tem dependência `Xamarin.Android.Support.CustomTabs` para a plataforma Android. Portanto, se o seu projeto utilizar os pacotes de suporte mais recentes, precisará de instalar diretamente este pacote com a versão necessária para evitar conflitos.

<!-- URLs. -->
[Criar uma aplicação xamarin. Android]: app-service-mobile-xamarin-android-get-started.md
