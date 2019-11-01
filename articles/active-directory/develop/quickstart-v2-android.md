---
title: Início rápido do Android para plataforma de identidade da Microsoft | Azure
description: Saiba como os aplicativos Android podem chamar uma API que exige tokens de acesso pelo ponto de extremidade da plataforma de identidade da Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/15/2019
ms.author: twhitney
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Android
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a1a86965eb6a50fa87c63f5713f21d6a467dedb
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242239"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Início Rápido: Iniciar sessão dos utilizadores e chamar a Microsoft Graph API a partir de uma aplicação Android

Este guia de início rápido usa um exemplo de código para demonstrar como um aplicativo Android pode entrar em contas pessoais, corporativas ou de estudante usando a plataforma de identidade da Microsoft e, em seguida, obter um token de acesso e chamar a API de Microsoft Graph.

Os aplicativos devem ser representados por um objeto de aplicativo no Azure Active Directory para que a plataforma de identidade da Microsoft possa compartilhar tokens com seu aplicativo.

> [!div renderon="docs"]
> Como uma conveniência, o exemplo de código vem com um `redirect_uri` padrão pré-configurado no arquivo de `AndroidManifest.xml` para que você não precise primeiro registrar seu próprio objeto de aplicativo. Uma `redirect_uri` é parcialmente baseada na chave de assinatura do seu aplicativo. O projeto de exemplo é pré-configurado com uma chave de assinatura para que o `redirect_uri` fornecido funcione. Para saber mais sobre como registrar um objeto de aplicativo e integrá-lo ao seu aplicativo, consulte os [usuários de conexão e chame o Microsoft Graph de um tutorial de aplicativo do Android](tutorial-v2-android.md) .

![Captura de tela do aplicativo de exemplo](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Pré-requisitos**
> * Android Studio 
> * Android 16 +

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Etapa 1: configurar seu aplicativo no portal do Azure 
>  Para que o exemplo de código para este guia de início rápido funcione, você precisa adicionar um URI de redirecionamento compatível com o agente de autenticação.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça essas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-android/green-check.png) A sua aplicação está configurada com estes atributos
>
> ### <a name="step-2-download-the-project"></a>Passo 2: Transferir o projeto 
> * [Baixar o exemplo de código](https://github.com/Azure-Samples/ms-identity-android-java)
>
> ### <a name="step-3-configure-your-project"></a>Passo 3: Configurar o projeto
> 1. Extraia e abra o Projeto no Android Studio.
> 2. Dentro do aplicativo > src > Principal > res > RAW, abra auth_config_multiple_account. JSON e substitua-o pelo código a seguir:
> ```javascript 
> {
>   "client_id" : "Enter_the_Application_Id_Here",
>   "authorization_user_agent" : "DEFAULT",
>   "redirect_uri" : "Enter_the_Redirect_Uri_Here",
>   "account_mode" : "MULTIPLE",
>   "broker_redirect_uri_registered": true,
>   "authorities" : [
>     {
>       "type": "AAD",
>       "audience": {
>         "type": "Enter_the_Audience_Info_Here",
>         "tenant_id": "Enter_the_Tenant_Info_Here"
>       }
>     }
>   ]
> }
> ```

> [!div class="sxs-lookup" renderon="portal"]
> 3. Dentro do aplicativo > src > Principal > res > RAW, abra auth_config_single_account. JSON e substitua-o pelo código a seguir:
> ```javascript 
> {
>   "client_id" : "Enter_the_Application_Id_Here",
>   "authorization_user_agent" : "DEFAULT",
>   "redirect_uri" : "Enter_the_Redirect_Uri_Here",
>   "account_mode" : "SINGLE",
>   "broker_redirect_uri_registered": true,
>   "authorities" : [
>     {
>       "type": "AAD",
>       "audience": {
>         "type": "Enter_the_Audience_Info_Here",
>         "tenant_id": "Enter_the_Tenant_Info_Here"
>       }
>     }
>   ]
> }
> ```

> [!div class="sxs-lookup" renderon="portal"]
> 4. Dentro do **aplicativo** > **src** > **principal**, abra **AndroidManifest. xml**.
> 5. No nó **manifest\application** , substitua o nó **<activity android:name="com.microsoft.identity.client.BrowserTabActivity">** pelo seguinte:  
> ```xml
> <!--Intent filter to catch Microsoft's callback after Sign In-->
> <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
>     <intent-filter>
>     <action android:name="android.intent.action.VIEW" />
>     <category android:name="android.intent.category.DEFAULT" />
>     <category android:name="android.intent.category.BROWSABLE" />
>         <!--
>             Add in your scheme/host from registered redirect URI 
>             note that the leading "/" is required for android:path
>         -->
>         <data android:scheme="msauth"
>             android:host="Enter_the_Package_Name_here"
>             android:path="Enter_the_Signature_Hash_here"
>             android:scheme = "msauth" />
>     </intent-filter>
> </activity>
> ```
> 6. Execute o aplicativo!
> O aplicativo de exemplo inicia na tela de **modo de conta única** . Um escopo padrão, **User. Read**, é fornecido por padrão, que é usado ao ler seus próprios dados de perfil durante a chamada à API de Microsoft Graph. A URL para a chamada à API de Microsoft Graph é fornecida por padrão. Você pode alterar ambos se desejar.
>
> ![Aplicativo de exemplo MSAL mostrando o uso de conta única e múltipla](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Use o menu do aplicativo para alterar entre os modos de conta única e múltipla.
>
> No modo de conta única, entre usando uma conta corporativa ou doméstica:
>
> 1. Selecione **obter dados de grafo interativamente** para solicitar ao usuário suas credenciais. Você verá a saída da chamada para a API de Microsoft Graph na parte inferior da tela.
> 2. Depois de conectado, selecione **obter dados do grafo silenciosamente** para fazer uma chamada para a API de Microsoft Graph sem solicitar as credenciais do usuário novamente. Você verá a saída da chamada para a API de Microsoft Graph na parte inferior da tela.
>
> No modo de conta múltipla, você pode repetir as mesmas etapas.  Além disso, você pode remover a conta conectada, que também remove os tokens armazenados em cache para essa conta.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Este guia de início rápido dá suporte a Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> ## <a name="step-1-get-the-sample-app"></a>Etapa 1: obter o aplicativo de exemplo
>
> [Clone o código](https://github.com/Azure-Samples/ms-identity-android-java.git).
>
> ## <a name="step-2-run-the-sample-app"></a>Etapa 2: executar o aplicativo de exemplo
>
> Selecione seu emulador ou dispositivo físico no menu suspenso de **dispositivos disponíveis** do Android Studio e execute o aplicativo.
>
> O aplicativo de exemplo inicia na tela de **modo de conta única** . Um escopo padrão, **User. Read**, é fornecido por padrão, que é usado ao ler seus próprios dados de perfil durante a chamada à API de Microsoft Graph. A URL para a chamada à API de Microsoft Graph é fornecida por padrão. Você pode alterar ambos se desejar.
>
> ![Aplicativo de exemplo MSAL mostrando o uso de conta única e múltipla](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Use o menu do aplicativo para alterar entre os modos de conta única e múltipla.
>
> No modo de conta única, entre usando uma conta corporativa ou doméstica:
>
> 1. Selecione **obter dados de grafo interativamente** para solicitar ao usuário suas credenciais. Você verá a saída da chamada para a API de Microsoft Graph na parte inferior da tela.
> 2. Depois de conectado, selecione **obter dados do grafo silenciosamente** para fazer uma chamada para a API de Microsoft Graph sem solicitar as credenciais do usuário novamente. Você verá a saída da chamada para a API de Microsoft Graph na parte inferior da tela.
>
> No modo de conta múltipla, você pode repetir as mesmas etapas.  Além disso, você pode remover a conta conectada, que também remove os tokens armazenados em cache para essa conta.

## <a name="how-the-sample-works"></a>Como funciona o exemplo

O código é organizado em fragmentos que mostram como escrever um aplicativo MSAL único e de várias contas. Os arquivos de código são organizados da seguinte maneira:

| Ficheiros  | Demonstrar  |
|---------|---------|
| MainActivity | Gerencia a interface do usuário |
| MSGraphRequestWrapper  | Chama a API de Microsoft Graph usando o token fornecido por MSAL |
| MultipleAccountModeFragment  | Inicializa um aplicativo de várias contas, carrega uma conta de usuário e Obtém um token para chamar a API de Microsoft Graph |
| SingleAccountModeFragment | Inicializa um aplicativo de conta única, carrega uma conta de usuário e Obtém um token para chamar a API de Microsoft Graph |
| res/auth_config_multiple_account. JSON  | O arquivo de configuração de várias contas |
| res/auth_config_single_account. JSON  | O arquivo de configuração de conta única |
| Scripts gradle/Build. grau (módulo: aplicativo) | As dependências da biblioteca MSAL são adicionadas aqui |

Agora vamos examinar esses arquivos mais detalhadamente e chamar o código específico do MSAL em cada um.

### <a name="adding-msal-to-the-app"></a>Adicionando MSAL ao aplicativo

MSAL ([com. Microsoft. Identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft. O gradle 3.0 + instala a biblioteca quando você adiciona o seguinte a **scripts Gradle** > **Build. gradle (módulo: aplicativo)** em **dependências**:

```gradle  
implementation 'com.microsoft.identity.client:msal:1.0.0'
```

Você pode ver isso no projeto de exemplo em Build. gradle (módulo: aplicativo):

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.0.+'
    ...
}
```

Isso instrui o gradle a baixar e compilar o MSAL da central do Maven.

### <a name="msal-imports"></a>Importações do MSAL

As importações que são relevantes para a biblioteca MSAL são `com.microsoft.identity.client.*`.  Por exemplo, você verá `import com.microsoft.identity.client.PublicClientApplication;` qual é o namespace para a classe `PublicClientApplication`, que representa seu aplicativo cliente público.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment. java

Este arquivo demonstra como criar um aplicativo MSAL de conta única e chamar uma API Microsoft Graph.

Os aplicativos de conta única são usados apenas por um único usuário.  Por exemplo, você pode ter apenas uma conta na qual você entra no seu aplicativo de mapeamento.

#### <a name="single-account-msal-initialization"></a>Inicialização de MSAL de conta única

No `auth_config_single_account.json`, no `onCreateView()`, uma única conta `PublicClientApplication` é criada usando as informações de configuração armazenadas no arquivo `auth_config_single_account.json`.  É assim que você inicializa a biblioteca MSAL para uso em um aplicativo MSAL de conta única:

```java
...
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createSingleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_single_account,
        new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(ISingleAccountPublicClientApplication application) {
                /**
                    * This test app assumes that the app is only going to support one account.
                    * This requires "account_mode" : "SINGLE" in the config json file.
                    **/
                mSingleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                displayError(exception);
            }
        });
```

#### <a name="sign-in-a-user"></a>Conectar um usuário

No `SingleAccountModeFragment.java`, o código para entrar em um usuário está em `initializeUI()`, no manipulador de `signInButton` clique.

Chame `signIn()` antes de tentar adquirir tokens. `signIn()` se comporta como se `acquireToken()` for chamado, resultando em um prompt interativo para o usuário entrar.

A entrada de um usuário é uma operação assíncrona. É passado um retorno de chamada que chama a API de Microsoft Graph e atualiza a interface do usuário quando ele faz logon:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Desconectar um usuário

No `SingleAccountModeFragment.java`, o código para sair de um usuário está em `initializeUI()`, no manipulador de `signOutButton` clique.  A inscrição de um usuário é uma operação assíncrona. A inscrição do usuário também limpa o cache de token para essa conta. Um retorno de chamada é criado para atualizar a interface do usuário depois que a conta de usuários é desconectada:

```java
mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
    @Override
    public void onSignOut() {
        updateUI(null);
        performOperationOnSignOut();
    }

    @Override
    public void onError(@NonNull MsalException exception) {
        displayError(exception);
    }
});
```

#### <a name="get-a-token-interactively-or-silently"></a>Obter um token de forma interativa ou silenciosa

Para apresentar o menor número de prompts para o usuário, você normalmente obterá um token silenciosamente. Em seguida, se houver um erro, tente obter o token interativamente. Na primeira vez que o aplicativo chama `signIn()`, ele age efetivamente como uma chamada para `acquireToken()`, que solicitará as credenciais ao usuário.

Algumas situações em que o usuário pode ser solicitado a selecionar sua conta, inserir suas credenciais ou consentir as permissões solicitadas pelo seu aplicativo são:

* Na primeira vez que o usuário entra no aplicativo
* Se um usuário redefinir sua senha, ele precisará inserir suas credenciais
* Se o consentimento for revogado
* Se seu aplicativo requer explicitamente consentimento
* Quando seu aplicativo está solicitando acesso a um recurso pela primeira vez
* Quando MFA ou outras políticas de acesso condicional são necessárias

O código para obter um token interativamente, ou seja, com a interface do usuário que envolverá o usuários, está em `SingleAccountModeFragment.java`, em `initializeUI()`, no manipulador de cliques `callGraphApiInteractiveButton`:

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Se o usuário já tiver entrado, `acquireTokenSilentAsync()` permite que os aplicativos solicitem tokens silenciosamente, conforme mostrado na `initializeUI()`, no manipulador de `callGraphApiSilentButton` clique:

```java
/**
  * Once you've signed the user in,
  * you can perform acquireTokenSilent to obtain resources without interrupting the user.
  */
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Carregar uma conta

O código para carregar uma conta está em `SingleAccountModeFragment.java` em `loadAccount()`.  Carregar a conta do usuário é uma operação assíncrona, portanto, os retornos de chamada para manipular quando a conta é carregada, as alterações ou um erro é passado para MSAL.  O código a seguir também manipula `onAccountChanged()`, que ocorre quando uma conta é removida, o usuário muda para outra conta e assim por diante.

```java
private void loadAccount() {
    ...

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
```

#### <a name="call-microsoft-graph"></a>Chamar Microsoft Graph

Quando um usuário é conectado, a chamada para Microsoft Graph é feita por meio de uma solicitação HTTP pelo `callGraphAPI()` que é definido em `SingleAccountModeFragment.java`. Essa função é um wrapper que simplifica o exemplo fazendo algumas tarefas, como obter o token de acesso do `authenticationResult` e empacotar a chamada para o MSGraphRequestWrapper e exibir os resultados da chamada.

```java
private void callGraphAPI(final IAuthenticationResult authenticationResult) {
    MSGraphRequestWrapper.callGraphAPIUsingVolley(
            getContext(),
            graphResourceTextView.getText().toString(),
            authenticationResult.getAccessToken(),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                    /* Successfully called graph, process data and send to UI */
                    ...
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    ...
                }
            });
}
```

### <a name="auth_config_single_accountjson"></a>auth_config_single_account. JSON

Esse é o arquivo de configuração para um aplicativo MSAL que usa uma única conta.

Consulte [entender o arquivo de configuração do Android MSAL](msal-configuration.md) para obter uma explicação desses campos.

Observe a presença de `"account_mode" : "SINGLE"`, que configura esse aplicativo para usar uma única conta.

`"client_id"` é pré-configurado para usar um registro de objeto de aplicativo que a Microsoft mantém.
`"redirect_uri"`é pré-configurado para usar a chave de assinatura fornecida com o exemplo de código.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "SINGLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment. java

Este arquivo demonstra como criar um aplicativo MSAL de várias contas e chamar uma API Microsoft Graph.

Um exemplo de um aplicativo de várias contas é um aplicativo de email que permite que você trabalhe com várias contas de usuário, como uma conta corporativa e uma conta pessoal.

#### <a name="multiple-account-msal-initialization"></a>Inicialização de MSAL de várias contas

No arquivo de `MultipleAccountModeFragment.java`, no `onCreateView()`, um objeto de aplicativo de várias contas (`IMultipleAccountPublicClientApplication`) é criado usando as informações de configuração armazenadas no `auth_config_multiple_account.json file`:

```java
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccounts();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

O objeto `MultipleAccountPublicClientApplication` criado é armazenado em uma variável de membro de classe para que possa ser usado para interagir com a biblioteca MSAL para adquirir tokens e carregar e remover a conta de usuário.

#### <a name="load-an-account"></a>Carregar uma conta

Aplicativos de conta múltipla geralmente chamam `getAccounts()` para selecionar a conta a ser usada para operações de MSAL. O código para carregar uma conta está no arquivo de `MultipleAccountModeFragment.java`, em `loadAccounts()`.  O carregamento da conta do usuário é uma operação assíncrona. Portanto, um retorno de chamada manipula as situações em que a conta é carregada, muda ou ocorre um erro.

```java
/**
     * Load currently signed-in accounts, if there's any.
    */
private void loadAccounts() {
    if (mMultipleAccountApp == null) {
        return;
    }

    mMultipleAccountApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            // You can use the account data to update your UI or your app database.
            accountList = result;
            updateUI(accountList);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

#### <a name="get-a-token-interactively-or-silently"></a>Obter um token de forma interativa ou silenciosa

Algumas situações em que o usuário pode ser solicitado a selecionar sua conta, inserir suas credenciais ou consentir as permissões solicitadas pelo seu aplicativo são:

* A primeira vez que os utilizadores iniciam sessão na aplicação
* Se um usuário redefinir sua senha, ele precisará inserir suas credenciais 
* Se o consentimento for revogado 
* Se seu aplicativo requer explicitamente consentimento 
* Quando seu aplicativo está solicitando acesso a um recurso pela primeira vez
* Quando MFA ou outras políticas de acesso condicional são necessárias

Aplicativos de conta múltipla normalmente devem adquirir tokens interativamente, ou seja, com a interface do usuário, com uma chamada para `acquireToken()`.  O código para obter um token interativamente está no arquivo de `MultipleAccountModeFragment.java` em `initializeUI()`, no manipulador de `callGraphApiInteractiveButton` clique:

```java
/**
 * Acquire token interactively. It will also create an account object for the silent call as a result (to be obtained by getAccount()).
 *
 * If acquireTokenSilent() returns an error that requires an interaction,
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your SSO refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Os aplicativos não devem exigir que o usuário entre sempre que solicitar um token. Se o usuário já tiver entrado, `acquireTokenSilentAsync()` permitirá que os aplicativos solicitem tokens sem avisar o usuário, conforme mostrado no arquivo `MultipleAccountModeFragment.java`, em`initializeUI()` no manipulador de `callGraphApiSilentButton` clique:

```java
/**
 * Performs acquireToken without interrupting the user.
 *
 * This requires an account object of the account you're obtaining a token for.
 * (can be obtained via getAccount()).
 */
mMultipleAccountApp.acquireTokenSilentAsync(getScopes(),
    accountList.get(accountListSpinner.getSelectedItemPosition()),
    AUTHORITY,
    getAuthSilentCallback());
```

#### <a name="remove-an-account"></a>Remover uma conta

O código para remover uma conta e todos os tokens em cache para a conta está no arquivo `MultipleAccountModeFragment.java` em `initializeUI()` no manipulador para o botão Remover conta. Antes de remover uma conta, você precisa de um objeto de conta, que você obtém de funções MSAL como `getAccounts()` e `acquireToken()`. Como a remoção de uma conta é uma operação assíncrona, o `onRemoved` retorno de chamada é fornecido para atualizar a interface do usuário.

```java
/**
  * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
  */
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccounts();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account. JSON

Esse é o arquivo de configuração para um aplicativo MSAL que usa várias contas.

Consulte [entender o arquivo de configuração do Android MSAL](msal-configuration.md) para obter uma explicação dos vários campos.

Ao contrário do arquivo de configuração [auth_config_single_account. JSON](#auth_config_single_accountjson) , esse arquivo de configuração tem `"account_mode" : "MULTIPLE"` em vez de `"account_mode" : "SINGLE"` porque este é um aplicativo de várias contas.

`"client_id"` é pré-configurado para usar um registro de objeto de aplicativo que a Microsoft mantém.
`"redirect_uri"`é pré-configurado para usar a chave de assinatura fornecida com o exemplo de código.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "MULTIPLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Passos seguintes

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Conheça os passos para criar a aplicação utilizada neste início rápido

Experimente os [usuários de entrada e chame o Microsoft Graph de um tutorial de aplicativo Android](tutorial-v2-android.md) para obter um guia passo a passo para criar um aplicativo Android que obtém um token de acesso e o usa para chamar a API Microsoft Graph.

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
