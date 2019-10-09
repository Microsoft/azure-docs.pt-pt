---
title: Introdução à autenticação para aplicativos móveis no Xamarin Android
description: Saiba como usar aplicativos móveis para autenticar usuários de seu aplicativo Xamarin Android por meio de uma variedade de provedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: panarasi
editor: ''
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 44a6261819ee8e880d9a5763e92678bc359f5eff
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025089"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Adicionar autenticação ao seu aplicativo Xamarin. Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar o pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para manter e sincronizar dados de aplicativos na nuvem.
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje.

## <a name="overview"></a>Descrição geral
Este tópico mostra como autenticar usuários de um aplicativo móvel do seu aplicativo cliente. Neste tutorial, você adicionará autenticação ao projeto de início rápido usando um provedor de identidade com suporte dos aplicativos móveis do Azure. Depois de ser autenticado e autorizado com êxito no aplicativo móvel, o valor da ID de usuário é exibido.

Este tutorial se baseia no guia de início rápido do aplicativo móvel. Você também deve primeiro concluir o tutorial [criar um aplicativo Xamarin. Android]. Se você não usar o projeto baixado do servidor de início rápido, deverá adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão de servidor, consulte [trabalhar com o SDK do servidor de back-end do .net para aplicativos móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Registrar seu aplicativo para autenticação e configurar os serviços de aplicativos
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar seu aplicativo às URLs de redirecionamento externo permitidas

A autenticação segura exige que você defina um novo esquema de URL para seu aplicativo. Isso permite que o sistema de autenticação Redirecione para seu aplicativo após a conclusão do processo de autenticação. Neste tutorial, usamos o esquema de URL _AppName_ em todo o. No entanto, você pode usar qualquer esquema de URL que escolher. Ele deve ser exclusivo para seu aplicativo móvel. Para habilitar o redirecionamento no lado do servidor:

1. No [portal do Azure], selecione o serviço de aplicativo.

2. Clique na opção de menu **autenticação/autorização** .

3. Nas **URLs de redirecionamento externo permitidas**, insira `url_scheme_of_your_app://easyauth.callback`.  O **url_scheme_of_your_app** nesta cadeia de caracteres é o esquema de URL para seu aplicativo móvel.  Ele deve seguir a especificação de URL normal para um protocolo (Use somente letras e números e comece com uma letra).  Você deve anotar a cadeia de caracteres que escolher, pois será necessário ajustar o código do aplicativo móvel com o esquema de URL em vários locais.

4. Clique em **OK**.

5. Clique em **Guardar**.

## <a name="permissions"></a>Restringir permissões a usuários autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

No Visual Studio ou Xamarin Studio, execute o projeto do cliente em um dispositivo ou emulador. Verifique se uma exceção sem tratamento com um código de status 401 (não autorizado) é gerada depois que o aplicativo é iniciado. Isso acontece porque o aplicativo tenta acessar o back-end do aplicativo móvel como um usuário não autenticado. A tabela *TodoItem* agora requer autenticação.

Em seguida, você atualizará o aplicativo cliente para solicitar recursos do back-end do aplicativo móvel com um usuário autenticado.

## <a name="add-authentication"></a>Adicionar autenticação ao aplicativo
O aplicativo é atualizado para exigir que os usuários toquem no botão **entrar e se** autentiquem antes de os dados serem exibidos.

1. Adicione o seguinte código à classe **TodoActivity** :
   
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
   
    Isso cria um novo método para autenticar um usuário e um manipulador de método para um novo botão **entrar** . O usuário no código de exemplo acima é autenticado usando um logon do Facebook. Uma caixa de diálogo é usada para exibir a ID de usuário depois de autenticada.
   
   > [!NOTE]
   > Se você estiver usando um provedor de identidade diferente do Facebook, altere o valor passado para **LoginAsync** acima para um dos seguintes: *MicrosoftAccount*, *Twitter*, *Google*ou *WindowsAzureActiveDirectory*.
   > 
   > 
2. No método **OnCreate** , exclua ou comente a seguinte linha de código:
   
        OnRefreshItemsSelected ();
3. No arquivo Activity_To_Do. axml, adicione a seguinte definição de botão de *LoginUser* antes do botão *AddItem* existente:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Adicione o seguinte elemento ao arquivo de recursos Strings. xml:
   
        <string name="login_button_text">Sign in</string>
5. Abra o arquivo AndroidManifest. xml e adicione o seguinte código dentro do elemento XML `<application>`:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. No Visual Studio ou Xamarin Studio, execute o projeto do cliente em um dispositivo ou emulador e entre com seu provedor de identidade escolhido. Quando você tiver feito logon com êxito, o aplicativo exibirá sua ID de logon e a lista de itens de tarefas e você poderá fazer atualizações nos dados.

## <a name="troubleshooting"></a>Resolução de problemas

**O aplicativo falhou com `Java.Lang.NoSuchMethodError: No static method startActivity`**

Em alguns casos, os conflitos nos pacotes de suporte são exibidos como apenas um aviso no Visual Studio, mas o aplicativo falha com essa exceção em tempo de execução. Nesse caso, você precisa certificar-se de que todos os pacotes de suporte referenciados no seu projeto tenham a mesma versão. O [pacote NuGet de Aplicações Móveis do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) tem dependência `Xamarin.Android.Support.CustomTabs` para a plataforma Android. Portanto, se o seu projeto utilizar os pacotes de suporte mais recentes, precisará de instalar diretamente este pacote com a versão necessária para evitar conflitos.

<!-- URLs. -->
[Criar um aplicativo Xamarin. Android]: app-service-mobile-xamarin-android-get-started.md
