---
title: Começar com autenticação no Xamarin Android
description: Saiba como usar aplicações móveis para autenticar utilizadores da sua aplicação Xamarin Android com fornecedores de identidade como AAD, Google, Facebook, Twitter e Microsoft.
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: fa70b7419e1877ab2daba49ad154cdfd5a8d2cba
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77458958"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Adicione a autenticação à sua aplicação Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Descrição geral
Este tópico mostra como autenticar os utilizadores de uma Aplicação Móvel a partir da sua aplicação cliente. Neste tutorial, adiciona-se autenticação ao projeto quickstart utilizando um fornecedor de identidade que é apoiado pela Azure Mobile Apps. Depois de ter sido autenticado e autorizado com sucesso na Aplicação Móvel, o valor de ID do utilizador é apresentado.

Este tutorial baseia-se no arranque rápido da Mobile App. Você também deve completar primeiro o tutorial [Crie uma aplicação Xamarin.Android]. Se não utilizar o projeto de servidor de arranque rápido descarregado, tem de adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre os pacotes de extensão do servidor, consulte [Trabalhar com o servidor de backend .NET SDK para Aplicações Móveis Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register"></a>Registe a sua aplicação para autenticação e configure serviços de aplicações
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicione a sua aplicação aos URLs De Redirecionamento Externo Permitidos

A autenticação segura requer que defina um novo esquema de URL para a sua aplicação. Isto permite que o sistema de autenticação redirecione para a sua aplicação uma vez concluído o processo de autenticação. Neste tutorial, usamos o _nome_ de aplicativo do esquema URL em toda a parte. No entanto, pode utilizar qualquer esquema de URL que escolha. Deve ser exclusivo da sua aplicação móvel. Para ativar a reorientação do lado do servidor:

1. No portal [Azure], selecione o seu Serviço de Aplicações.

2. Clique na opção de **menu Autenticação/Autorização.**

3. Nos **URLs de Redirecionamento Externo Permitidos,** introduza `url_scheme_of_your_app://easyauth.callback`.  O **url_scheme_of_your_app** nesta cadeia é o URL Scheme para a sua aplicação móvel.  Deve seguir a especificação normal do URL para um protocolo (utilize apenas letras e números, e comece com uma letra).  Deve tomar nota da corda que escolher, pois terá de ajustar o seu código de aplicação móvel com o URL Scheme em vários locais.

4. Clique em **OK**.

5. Clique em **Guardar**.

## <a name="permissions"></a>Restringir permissões a utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

No Visual Studio ou no Xamarin Studio, execute o projeto do cliente num dispositivo ou emulador. Verifique se uma exceção não tratada com um código de estado de 401 (Não autorizado) é levantada após o início da aplicação. Isto acontece porque a aplicação tenta aceder ao seu backend da Aplicação Móvel como um utilizador não autenticado. A tabela *TodoItem* requer agora a autenticação.

Em seguida, irá atualizar a app do cliente para solicitar recursos do backend da Aplicação Móvel com um utilizador autenticado.

## <a name="add-authentication"></a>Adicionar autenticação à app
A aplicação é atualizada para exigir que os utilizadores toquem no botão **'Sinal'** e autenticam antes de os dados forem apresentados.

1. Adicione o seguinte código à classe **TodoActividade:**
   
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
   
    Isto cria um novo método para autenticar um utilizador e um manipulador de métodos para um novo botão **Sign in.** O utilizador no código de exemplo acima é autenticado utilizando um login no Facebook. É utilizado um diálogo para exibir o ID do utilizador uma vez autenticado.
   
   > [!NOTE]
   > Se estiver a utilizar um fornecedor de identidade que não seja o Facebook, altere o valor passado para **O LoginAsync** acima para um dos seguintes: *MicrosoftAccount,* *Twitter,* *Google*ou *WindowsAzureActiveDirectory*.
   > 
   > 
2. No método **OnCreate,** elimine ou comente a seguinte linha de código:
   
        OnRefreshItemsSelected ();
3. No ficheiro Activity_To_Do.axml, adicione a seguinte definição de botão *LoginUser* antes do botão *AddItem* existente:
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Adicione o seguinte elemento ao ficheiro de recursos Strings.xml:
   
        <string name="login_button_text">Sign in</string>
5. Abra o ficheiro AndroidManifest.xml, adicione o seguinte código dentro `<application>` elemento XML:

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. No Visual Studio ou no Xamarin Studio, execute o projeto do cliente num dispositivo ou emulador e inscreva-se com o seu fornecedor de identidade escolhido. Quando estiver a iniciar sessão com sucesso, a aplicação apresentará o seu ID de login e a lista de itens de todo o todo, podendo fazer atualizações para os dados.

## <a name="troubleshooting"></a>Resolução de problemas

**A aplicação despenhou-se com `Java.Lang.NoSuchMethodError: No static method startActivity`**

Em alguns casos, os conflitos nos pacotes de suporte apresentados como apenas um aviso no estúdio Visual, mas a aplicação falha com esta exceção no tempo de execução. Neste caso, tem de se certificar de que todos os pacotes de suporte referenciados no seu projeto têm a mesma versão. O [pacote NuGet de Aplicações Móveis do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) tem dependência `Xamarin.Android.Support.CustomTabs` para a plataforma Android. Portanto, se o seu projeto utilizar os pacotes de suporte mais recentes, precisará de instalar diretamente este pacote com a versão necessária para evitar conflitos.

<!-- URLs. -->
[Crie uma aplicação Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
