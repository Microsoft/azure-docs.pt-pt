---
title: Microsoft identity plataforma Android guia de introdução | Azure
description: Saiba como Android aplicativos pode chamar uma API que precisam de tokens de acesso ao ponto final de plataforma de identidade Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 547eafac8cc1acf2b60416f93804e819a1c549b0
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702748"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Início rápido: Iniciar sessão dos utilizadores e chamar a API do Microsoft Graph a partir de uma aplicação Android

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Este início rápido contém um exemplo de código que demonstra como uma aplicação Android pode iniciar sessão em contas pessoais ou contas profissionais e escolares, obter um token de acesso e chamar a Microsoft Graph API.

![Mostra como funciona a aplicação de exemplo gerada por este início rápido](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Pré-requisitos**
> * Android Studio 
> * Android 16 + é necessário 


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> Tem duas opções para iniciar a aplicação de início rápido:
> * [Express] [Opção 1: Registre-se e automática configurar a sua aplicação e, em seguida, transferir o exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: Registar e configurar manualmente o seu aplicativo e o código de exemplo](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registre-se e automática configurar a sua aplicação e, em seguida, transferir o exemplo de código
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação
> Para registar a sua aplicação
> 1. Vá para a nova [portal do Azure – registos de aplicações](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs) painel.
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para transferir e configurar automaticamente a sua nova aplicação com um só clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registar e configurar manualmente o seu aplicativo e o código de exemplo
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a sua aplicação
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Navegue para a plataforma de identidade da Microsoft para desenvolvedores [registos das aplicações](https://aka.ms/MobileAppReg) página.
> 1. Selecione **novo registo**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>      - Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `AndroidQuickstart`.
>      - Pode ignorar outras configurações desta página. 
>      - Pressionar o `Register` botão.
> 1. Clique na nova aplicação > aceda a `Authentication`  >  `Add Platform`  >  `Android`.    
>      - Introduza o nome do pacote do seu projeto do Android studio. 
>      - Gere um Hash de assinatura. Consulte o portal para obter instruções.
> 1. Selecione `Configure` e guarde o ***configuração de MSAL*** JSON para utilizar mais tarde. 

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Passo 1: Configurar a aplicação
> Para o código de exemplo para este início rápido funcionar, terá de adicionar um compatível com o Mediador de autenticação URI de redirecionamento. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-android/green-check.png) A sua aplicação está configurada com estes atributos

#### <a name="step-2-download-the-project"></a>Passo 2: Transfira o projeto

* [Baixar o código de exemplo](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Passo 3: Configurar o seu projeto

> [!div renderon="docs"]
> Se tiver selecionado a opção 1 acima, pode ignorar estes passos. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extraia e abra o Projeto no Android Studio.
> 1. Dentro **app** > **src** > **principal** > **res**  >   **não processados**, abra **auth_config.json**.
> 1. Editar **auth_config.json** e substituí-lo com o JSON do portal do Azure. Se em vez disso, quiser fazer manualmente as alterações:
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
> 1. Inside **app** > **manifests**, open  **AndroidManifest.xml**.
> 1. Paste the following activity to the **manifest\application** node: 
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
> > 1. Execute a aplicação! 

> [!div renderon="docs"]
> 1. Extraia e abra o Projeto no Android Studio.
> 1. Dentro **app** > **res** > **brutos**, abra **auth_config.json**.
> 1. Editar **auth_config.json** e substituí-lo com o JSON do portal do Azure. Se em vez disso, pretende efetuar manualmente estas alterações:
>    ```javascript
>    "client_id" : "ENTER_YOUR_APPLICATION_ID",
>    "redirect_uri": "ENTER_YOUR_REDIRECT_URI", 
>     ```
> 1. Dentro **app** > **manifestos**, abra **androidmanifest. XML**.
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
> 1. Substitua `Enter_the_Package_Name` e `Enter_the_Signature_Hash` com os valores que registou no portal do Azure. 
> 1. Execute a aplicação! 

## <a name="more-information"></a>Mais Informações

Leia as secções seguintes para obter mais informações sobre este início rápido.

### <a name="getting-msal"></a>Introdução a MSAL

A MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) é a biblioteca utilizada para iniciar sessão dos utilizadores e solicitar tokens utilizados para aceder a uma API protegida pela plataforma de identidade da Microsoft. Pode utilizar Gradle 3.0 + para instalá-lo ao adicionar o seguinte na **Scripts de Gradle** > **gradle (módulo: aplicação)** sob **dependências**:

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
> |`R.raw.auth_config` | Este ficheiro contém as configurações para a sua aplicação, incluindo o seu ID de aplicação/cliente, início de sessão público-alvo, URI de redirecionamento e várias outras opções de personalização. |

### <a name="requesting-tokens"></a>Pedir tokens

A MSAL tem dois métodos utilizados para adquirir tokens: `acquireToken` e `acquireTokenSilentAsync`

#### <a name="acquiretoken-getting-a-token-interactively"></a>acquireToken: Obter um token de forma interativa

Algumas situações exijam os utilizadores interajam com a plataforma de identidade da Microsoft. Nestes casos, o utilizador final pode ser necessário para selecionar a conta, introduza as credenciais ou dar consentimento a permissões que solicitou a sua aplicação. Por exemplo, 

* A primeira vez que os utilizadores iniciam sessão na aplicação
* Se um utilizador repõe a palavra-passe, terão de introduzir as respetivas credenciais 
* Se o consentimento é revogado 
* Se a sua aplicação necessita de consentimento explicitamente. 
* Quando a aplicação está a pedir o acesso a um recurso pela primeira vez
* Quando são necessárias o MFA ou outras políticas de acesso condicional

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Em que:||
> |---------|---------|
> | `SCOPES` | Contém os âmbitos que estão a ser solicitados (ou seja, `{ "user.read" }` para o Microsoft Graph ou `{ "<Application ID URL>/scope" }` para as APIs Web personalizadas (ou seja, `api://<Application ID>/access_as_user`) |
> | `getAuthInteractiveCallback` | Chamada de retorno executada quando o controlo é devolvido à aplicação após a autenticação |

#### <a name="acquiretokensilent-getting-a-user-token-silently"></a>acquireTokenSilent: Obter um token de utilizador automaticamente

As aplicações não devem exigir aos utilizadores iniciar sessão sempre que eles solicitar um token. Se o utilizador já tiver iniciado sessão, este método permite que as aplicações pedir tokens silenciosamente.

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
> | `getAccounts(...)` | Contém a conta que está a tentar obter os tokens do silenciosamente |
> | `getAuthSilentCallback()` | Chamada de retorno executada quando o controlo é devolvido à aplicação após a autenticação |

## <a name="next-steps"></a>Passos Seguintes

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Conheça os passos para criar a aplicação utilizada neste início rápido

Experimente o tutorial para Android para obter um guia passo a passo completo sobre a criação de aplicações e novas funcionalidades, incluindo uma explicação completa deste início rápido.

> [!div class="nextstepaction"]
> [Tutorial Chamar a Graph API Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Wiki da biblioteca MSAL para Android

Leia mais informações sobre a biblioteca MSAL para Android:

> [!div class="nextstepaction"]
> [Wiki da biblioteca MSAL para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
