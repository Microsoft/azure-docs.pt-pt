---
title: Início rápido do Android para plataforma de identidade da Microsoft | Azure
description: Saiba como os aplicativos Android podem chamar uma API que exige tokens de acesso pelo ponto de extremidade da plataforma de identidade da Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2019
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c22d93d39f086aaa6e2f103d3becbe9376b49b0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324553"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Início rápido: Conectar usuários e chamar a API de Microsoft Graph de um aplicativo Android

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Este início rápido contém um exemplo de código que demonstra como uma aplicação Android pode iniciar sessão em contas pessoais ou contas profissionais e escolares, obter um token de acesso e chamar a Microsoft Graph API.

![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Pré-requisitos**
> * Android Studio 
> * O Android 16 + é necessário 


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> Tem duas opções para iniciar a aplicação de início rápido:
> * Express [Opção 1: Registre e configure automaticamente seu aplicativo e, em seguida, baixe seu exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * Manual [Opção 2: Registrar e configurar manualmente seu aplicativo e exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registre e configure automaticamente seu aplicativo e, em seguida, baixe seu exemplo de código
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação
> Para registrar seu aplicativo,
> 1. Vá para o novo painel de [registros de aplicativo de portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs) .
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação com um só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente seu aplicativo e exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Navegue até a página da plataforma Microsoft Identity para desenvolvedores [registros de aplicativo](https://aka.ms/MobileAppReg) .
> 1. Selecione **novo registro**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>      - Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `AndroidQuickstart`.
>      - Você pode ignorar outras configurações nesta página. 
>      - Pressione o `Register` botão.
> 1. Clique no novo aplicativo > vá para `Authentication`. `Add Platform`  >   >  `Android`    
>      - Insira o nome do pacote do seu projeto do Android Studio. 
>      - Gerar um hash de assinatura. Consulte o portal para obter instruções.
> 1. Selecione `Configure` e salve o JSON de ***configuração MSAL*** para mais tarde. 

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Passo 1: Configurar a aplicação
> Para que o exemplo de código para este guia de início rápido funcione, você precisa adicionar um URI de redirecionamento compatível com o agente de autenticação. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-android/green-check.png) A sua aplicação está configurada com estes atributos

#### <a name="step-2-download-the-project"></a>Passo 2: Baixar o projeto

* [Baixar o exemplo de código](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Passo 3: Configurar seu projeto

> [!div renderon="docs"]
> Se você selecionou a opção 1 acima, poderá ignorar essas etapas. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extraia e abra o Projeto no Android Studio.
> 1. Dentro do **aplicativo** > **src** Main res RAW, abra auth_config. JSON. >  >  > 
> 1. Edite **auth_config. JSON** e substitua-o pelo json do portal do Azure. Se, em vez disso, você quiser fazer as alterações manualmente:
>    ```javascript
>    {
>       "client_id" : "Enter_the_Application_Id_Here",
>       "authorization_user_agent" : "DEFAULT",
>       "redirect_uri" : "Enter_the_Redirect_Uri_Here",
>       "authorities" : [
>          {
>             "type": "AAD",
>             "audience": {
>                "type": "Enter_the_Audience_Info_Here",
>                "tenant_id": "Enter_the_Tenant_Info_Here"
>             }
>          }
>       ]
>    }
>    ```
> 
> 1. Dentro de**manifestos**de **aplicativo** > , abra **AndroidManifest. xml**.
> 1. Cole a seguinte atividade no nó **manifest\application** : 
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
>            <data android:scheme="msauth"
>                android:host="Enter_the_Package_Name"
>                android:path="/Enter_the_Signature_Hash" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Execute o aplicativo! 

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Este guia de início rápido dá suporte a Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> 1. Extraia e abra o Projeto no Android Studio.
> 1. No **app** > **res** RAW, abra auth_config. JSON. > 
> 1. Edite **auth_config. JSON** e substitua-o pelo json do portal do Azure. Se, em vez disso, você quiser fazer essas alterações manualmente:
>    ```javascript
>    "client_id" : "ENTER_YOUR_APPLICATION_ID",
>    "redirect_uri": "ENTER_YOUR_REDIRECT_URI", 
>     ```
> 1. Dentro de**manifestos**de **aplicativo** > , abra **AndroidManifest. xml**.
> 1. Adicione a atividade seguinte ao nó **manifest\application**. Este fragmento de código regista um **BrowserTabActivity** para permitir que o sistema operativo retome a sua aplicação depois de concluir a autenticação:
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
>            <data android:scheme="msauth"
>                android:host="Enter_the_Package_Name"
>                android:path="/Enter_the_Decoded_Signature_Hash" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Substitua `Enter_the_Package_Name` e`Enter_the_Signature_Hash` pelos valores que você registrou na portal do Azure. 
> 1. Execute o aplicativo! 

## <a name="more-information"></a>Mais Informações

Leia as secções seguintes para obter mais informações sobre este início rápido.

### <a name="getting-msal"></a>Obtendo MSAL

MSAL ([com. Microsoft. Identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft. Você pode usar o gradle 3.0 + para instalá-lo adicionando o seguinte em **scripts** > gradle**Build. gradle (módulo: aplicativo)** em **dependências**:

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.3.+'
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Pode adicionar a referência para MSAL, adicionando o código seguinte:

```java
import com.microsoft.identity.client.*;
```

Em seguida, inicialize a MSAL com o código seguinte:

```java
    sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);
```

> |Em que: ||
> |---------|---------|
> |`R.raw.auth_config` | Esse arquivo contém as configurações para seu aplicativo, incluindo a ID do aplicativo/cliente, o público de entrada, o URI de redirecionamento e várias outras opções de personalização. |

### <a name="requesting-tokens"></a>Pedir tokens

A MSAL tem dois métodos utilizados para adquirir tokens: `acquireToken` e `acquireTokenSilentAsync`

#### <a name="acquiretoken-getting-a-token-interactively"></a>AcquireToken Obter um token interativamente

Algumas situações exigem que os usuários interajam com a plataforma de identidade da Microsoft. Nesses casos, o usuário final pode ser solicitado a selecionar sua conta, inserir suas credenciais ou consentir as permissões solicitadas pelo seu aplicativo. Por exemplo, 

* A primeira vez que os utilizadores iniciam sessão na aplicação
* Se um usuário redefinir sua senha, será necessário inserir suas credenciais 
* Se o consentimento for revogado 
* Se seu aplicativo exigir explicitamente o consentimento. 
* Quando seu aplicativo está solicitando acesso a um recurso pela primeira vez
* Quando MFA ou outras políticas de acesso condicional são necessárias

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Em que:||
> |---------|---------|
> | `SCOPES` | Contém os âmbitos que estão a ser solicitados (ou seja, `{ "user.read" }` para o Microsoft Graph ou `{ "<Application ID URL>/scope" }` para as APIs Web personalizadas (ou seja, `api://<Application ID>/access_as_user`) |
> | `getAuthInteractiveCallback` | Chamada de retorno executada quando o controlo é devolvido à aplicação após a autenticação |

#### <a name="acquiretokensilent-getting-a-user-token-silently"></a>AcquireTokenSilent Obter um token de utilizador automaticamente

Os aplicativos não devem exigir que seus usuários façam logon toda vez que solicitarem um token. Se o usuário já tiver entrado, esse método permitirá que os aplicativos solicitem tokens silenciosamente.

```java
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (!accounts.isEmpty()) {
                sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
            } else {
                /* No accounts */
            }
        }
    });
```

> |Em que:||
> |---------|---------|
> | `SCOPES` | Contém os âmbitos que estão a ser solicitados (ou seja, `{ "user.read" }` para o Microsoft Graph ou `{ "<Application ID URL>/scope" }` para as APIs Web personalizadas (ou seja, `api://<Application ID>/access_as_user`) |
> | `getAccounts(...)` | Contém a conta para a qual você está tentando obter tokens silenciosamente |
> | `getAuthSilentCallback()` | Chamada de retorno executada quando o controlo é devolvido à aplicação após a autenticação |

## <a name="next-steps"></a>Passos seguintes

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Conheça os passos para criar a aplicação utilizada neste início rápido

Experimente o tutorial para Android para obter um guia passo a passo completo sobre a criação de aplicações e novas funcionalidades, incluindo uma explicação completa deste início rápido.

> [!div class="nextstepaction"]
> [Tutorial Chamar a Graph API Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Wiki da biblioteca MSAL para Android

Leia mais informações sobre a biblioteca MSAL para Android:

> [!div class="nextstepaction"]
> [Wiki da biblioteca MSAL para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que você imagina ao concluir uma pesquisa curta de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)