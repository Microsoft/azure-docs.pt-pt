---
title: Início rápido do Android para plataforma de identidade da Microsoft | Azure
description: Saiba como os aplicativos Android podem chamar uma API que exige tokens de acesso pelo ponto de extremidade da plataforma de identidade da Microsoft.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/15/2019
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Android
ms.openlocfilehash: bbaaf4b26beec56cd8608abc8a2f9cdd3a4cda3f
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084537"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Início Rápido: Iniciar sessão dos utilizadores e chamar a Microsoft Graph API a partir de uma aplicação Android

Este guia de início rápido usa um exemplo de código para demonstrar como um aplicativo Android pode entrar em contas pessoais, corporativas ou de estudante usando a plataforma de identidade da Microsoft e, em seguida, obter um token de acesso e chamar a API de Microsoft Graph.

Os aplicativos devem ser representados por um objeto de aplicativo no Azure Active Directory para que a plataforma de identidade da Microsoft possa compartilhar tokens com seu aplicativo.

> [!div renderon="docs"]
> Como conveniência, a amostra de código vem com um `redirect_uri` pré-configurado no ficheiro `AndroidManifest.xml` para que não tenha primeiro de registar o seu próprio objeto de aplicação. Um `redirect_uri` baseia-se em parte na chave de assinatura da sua aplicação. O projeto da amostra é reconfigurado com uma chave de assinatura para que o `redirect_uri` fornecido funcione. Para saber mais sobre o registo de um objeto de aplicação e a sua integração com a sua aplicação, consulte o Sign in users e ligue para o Microsoft Graph a partir de um tutorial [de aplicações Android.](tutorial-v2-android.md)

![Captura de tela do aplicativo de exemplo](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Pré-requisitos**
> * Android Studio 
> * Android 16 +

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configure a sua aplicação no portal Azure 
>  Para que o exemplo de código para este guia de início rápido funcione, você precisa adicionar um URI de redirecionamento compatível com o agente de autenticação.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas mudanças para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-android/green-check.png) A sua aplicação está configurada com estes atributos
>
> ### <a name="step-2-download-the-project"></a>Passo 2: Transferir o projeto 
> * [Descarregue a amostra de código](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> ### <a name="step-3-configure-your-project"></a>Passo 3: Configurar o projeto
> 1. Extraia e abra o Projeto no Android Studio.
> 2. Dentro da **aplicação** > **sRC** > **principais res** >  ** > ** **cru,** abra **auth_config_multiple_account.json** e substitua-a pelo seguinte código:
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
> 3. Dentro da **aplicação** > **src** > **principais** ** > res** > **cru,** abra **auth_config_single_account.json** e substitua-o pelo seguinte código:
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
> 4. Dentro da **aplicação** > **src** > **principal**, abra **AndroidManifest.xml**.
> 5. No nó **manifesto\application,** substitua a **atividade android:name="com.microsoft.identity.client.BrowserTabActivity"** com o seguinte:    
> ```xml
> <!--Intent filter to catch Microsoft's callback after Sign In-->
> <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
>     <intent-filter>
>         <action android:name="android.intent.action.VIEW" />
>         <category android:name="android.intent.category.DEFAULT" />
>         <category android:name="android.intent.category.BROWSABLE" />
>         <!--
>             Add in your scheme/host from registered redirect URI 
>             note that the leading "/" is required for android:path
>         -->
>         <data 
>             android:host="Enter_the_Package_Name"
>             android:path="/Enter_the_Signature_Hash"
>             android:scheme= "msauth" />
>     </intent-filter>
> </activity>
> ```
> 6. Execute o aplicativo!   
> A aplicação da amostra começa no ecrã modo **de conta única.** Um âmbito predefinido, **user.read**, é fornecido por predefinição, que é usado ao ler os dados do seu próprio perfil durante a chamada DaPI do Microsoft Graph. A URL para a chamada à API de Microsoft Graph é fornecida por padrão. Você pode alterar ambos se desejar.
>
> ![Aplicativo de exemplo MSAL mostrando o uso de conta única e múltipla](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Use o menu do aplicativo para alterar entre os modos de conta única e múltipla.
>
> No modo de conta única, entre usando uma conta corporativa ou doméstica:
>
> 1. Selecione **Obter dados de gráfico interactivamente** para solicitar ao utilizador as suas credenciais. Você verá a saída da chamada para a API de Microsoft Graph na parte inferior da tela.
> 2. Uma vez inscrito, selecione **Obter dados de gráfico silenciosamente** para fazer uma chamada para a Microsoft Graph API sem pedir ao utilizador novamente credenciais. Você verá a saída da chamada para a API de Microsoft Graph na parte inferior da tela.
>
> No modo de conta múltipla, você pode repetir as mesmas etapas.  Além disso, você pode remover a conta conectada, que também remove os tokens armazenados em cache para essa conta.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Este arranque rápido apoia Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> ## <a name="step-1-get-the-sample-app"></a>Etapa 1: obter o aplicativo de exemplo
>
> [Descarregue o código.](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> ## <a name="step-2-run-the-sample-app"></a>Etapa 2: executar o aplicativo de exemplo
>
> Selecione o seu emulador, ou dispositivo físico, a partir do drop down dos **dispositivos disponíveis** do Android Studio e execute a aplicação.
>
> A aplicação da amostra começa no ecrã modo **de conta única.** Um âmbito predefinido, **user.read**, é fornecido por predefinição, que é usado ao ler os dados do seu próprio perfil durante a chamada DaPI do Microsoft Graph. A URL para a chamada à API de Microsoft Graph é fornecida por padrão. Você pode alterar ambos se desejar.
>
> ![Aplicativo de exemplo MSAL mostrando o uso de conta única e múltipla](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Use o menu do aplicativo para alterar entre os modos de conta única e múltipla.
>
> No modo de conta única, entre usando uma conta corporativa ou doméstica:
>
> 1. Selecione **Obter dados de gráfico interactivamente** para solicitar ao utilizador as suas credenciais. Você verá a saída da chamada para a API de Microsoft Graph na parte inferior da tela.
> 2. Uma vez inscrito, selecione **Obter dados de gráfico silenciosamente** para fazer uma chamada para a Microsoft Graph API sem pedir ao utilizador novamente credenciais. Você verá a saída da chamada para a API de Microsoft Graph na parte inferior da tela.
>
> No modo de conta múltipla, você pode repetir as mesmas etapas.  Além disso, você pode remover a conta conectada, que também remove os tokens armazenados em cache para essa conta.

## <a name="how-the-sample-works"></a>Como funciona o exemplo

O código é organizado em fragmentos que mostram como escrever um aplicativo MSAL único e de várias contas. Os arquivos de código são organizados da seguinte maneira:

| Ficheiro  | Demonstra  |
|---------|---------|
| MainActivity | Gerencia a interface do usuário |
| MSGraphRequestWrapper  | Chama a API de Microsoft Graph usando o token fornecido por MSAL |
| MultipleAccountModeFragment  | Inicializa um aplicativo de várias contas, carrega uma conta de usuário e Obtém um token para chamar a API de Microsoft Graph |
| SingleAccountModeFragment | Inicializa um aplicativo de conta única, carrega uma conta de usuário e Obtém um token para chamar a API de Microsoft Graph |
| res/auth_config_multiple_account.json  | O arquivo de configuração de várias contas |
| res/auth_config_single_account.json  | O arquivo de configuração de conta única |
| Scripts gradle/Build. grau (módulo: aplicativo) | As dependências da biblioteca MSAL são adicionadas aqui |

Agora vamos examinar esses arquivos mais detalhadamente e chamar o código específico do MSAL em cada um.

### <a name="adding-msal-to-the-app"></a>Adicionando MSAL ao aplicativo

MSAL[(com.microsoft.identity.client)](https://javadoc.io/doc/com.microsoft.identity.client/msal)é a biblioteca usada para assinar utilizadores e solicitar fichas usadas para aceder a uma API protegida pela plataforma de identidade microsoft. Gradle 3.0+ instala a biblioteca quando adiciona o seguinte aos **Scripts Gradle** > **build.gradle (Módulo: app)** em **Dependências:**

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

As importações relevantes para a biblioteca MSAL são `com.microsoft.identity.client.*`.  Por exemplo, você verá `import com.microsoft.identity.client.PublicClientApplication;` qual é o espaço de nome para a classe `PublicClientApplication`, que representa a sua aplicação de cliente público.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment. java

Este arquivo demonstra como criar um aplicativo MSAL de conta única e chamar uma API Microsoft Graph.

Os aplicativos de conta única são usados apenas por um único usuário.  Por exemplo, você pode ter apenas uma conta na qual você entra no seu aplicativo de mapeamento.

#### <a name="single-account-msal-initialization"></a>Inicialização de MSAL de conta única

Em `auth_config_single_account.json`, em `onCreateView()`, é criada uma única conta `PublicClientApplication` utilizando as informações de config armazenadas no ficheiro `auth_config_single_account.json`.  É assim que você inicializa a biblioteca MSAL para uso em um aplicativo MSAL de conta única:

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

No `SingleAccountModeFragment.java`, o código para iniciar sessão num utilizador está em `initializeUI()`, no manipulador de cliques `signInButton`.

Ligue `signIn()` antes de tentar adquirir fichas. `signIn()` se comporta como se `acquireToken()` fosse chamado, resultando numa solicitação interativa para o utilizador iniciar o seu contrato.

A entrada de um usuário é uma operação assíncrona. É passado um retorno de chamada que chama a API de Microsoft Graph e atualiza a interface do usuário quando ele faz logon:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Desconectar um usuário

Em `SingleAccountModeFragment.java`, o código para assinar um utilizador está em `initializeUI()`, no manipulador de cliques `signOutButton`.  A inscrição de um usuário é uma operação assíncrona. A inscrição do usuário também limpa o cache de token para essa conta. Um retorno de chamada é criado para atualizar a interface do usuário depois que a conta de usuários é desconectada:

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

Para apresentar o menor número de prompts para o usuário, você normalmente obterá um token silenciosamente. Em seguida, se houver um erro, tente obter o token interativamente. A primeira vez que a aplicação chama `signIn()`, funciona efetivamente como uma chamada para `acquireToken()`, o que irá levar o utilizador a credenciais.

Algumas situações em que o usuário pode ser solicitado a selecionar sua conta, inserir suas credenciais ou consentir as permissões solicitadas pelo seu aplicativo são:

* Na primeira vez que o usuário entra no aplicativo
* Se um usuário redefinir sua senha, ele precisará inserir suas credenciais
* Se o consentimento for revogado
* Se seu aplicativo requer explicitamente consentimento
* Quando seu aplicativo está solicitando acesso a um recurso pela primeira vez
* Quando MFA ou outras políticas de acesso condicional são necessárias

O código para obter um símbolo interactivamente, isto é, com UI que envolverá o utilizador, está em `SingleAccountModeFragment.java`, em `initializeUI()`, no manipulador de cliques `callGraphApiInteractiveButton`:

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Se o utilizador já se inscreveu, `acquireTokenSilentAsync()` permite que as aplicações solicitem tokens silenciosamente, como mostra `initializeUI()`, no manipulador de cliques `callGraphApiSilentButton`:

```java
/**
 * Once you've signed the user in,
 * you can perform acquireTokenSilent to obtain resources without interrupting the user.
 **/
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Carregar uma conta

O código para carregar uma conta está em `SingleAccountModeFragment.java` em `loadAccount()`.  Carregar a conta do usuário é uma operação assíncrona, portanto, os retornos de chamada para manipular quando a conta é carregada, as alterações ou um erro é passado para MSAL.  O código que se segue também lida com `onAccountChanged()`, que ocorre quando uma conta é removida, o utilizador muda para outra conta, e assim por diante.

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

Quando um utilizador é inscrito, a chamada para o Microsoft Graph é feita através de um pedido HTTP por `callGraphAPI()` que é definido em `SingleAccountModeFragment.java`. Esta função é um invólucro que simplifica a amostra fazendo algumas tarefas, tais como obter o sinal de acesso do `authenticationResult` e embalar a chamada para o MSGraphRequestWrapper, e exibir os resultados da chamada.

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

### <a name="auth_config_single_accountjson"></a>auth_config_single_account.json

Esse é o arquivo de configuração para um aplicativo MSAL que usa uma única conta.

Consulte o ficheiro de [configuração Android MSAL](msal-configuration.md) para obter uma explicação destes campos.

Note a presença de `"account_mode" : "SINGLE"`, que configura esta aplicação para usar uma única conta.

`"client_id"` é configurado para usar um registo de objetos de aplicação que a Microsoft mantém.
`"redirect_uri"`é configurado para utilizar a chave de assinatura fornecida com a amostra de código.

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

No ficheiro `MultipleAccountModeFragment.java`, em `onCreateView()`, é criado um objeto de aplicação de conta múltipla (`IMultipleAccountPublicClientApplication`) utilizando as informações de config armazenadas no `auth_config_multiple_account.json file`:

```java
// Creates a PublicClientApplication object with res/raw/auth_config_multiple_account.json
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

O objeto criado `MultipleAccountPublicClientApplication` é armazenado numa variável de membro da classe para que possa ser usado para interagir com a biblioteca MSAL para adquirir fichas e carregar e remover a conta do utilizador.

#### <a name="load-an-account"></a>Carregar uma conta

Várias aplicações de conta geralmente chamam `getAccounts()` para selecionar a conta para usar para operações MSAL. O código para carregar uma conta está no ficheiro `MultipleAccountModeFragment.java`, em `loadAccounts()`.  O carregamento da conta do usuário é uma operação assíncrona. Portanto, um retorno de chamada manipula as situações em que a conta é carregada, muda ou ocorre um erro.

```java
/**
 * Load currently signed-in accounts, if there's any.
 **/
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

As aplicações de conta múltiplas devem normalmente adquirir tokens interativamente, isto é, com UI que envolve o utilizador, com uma chamada para `acquireToken()`.  O código para obter um token interativamente está no ficheiro `MultipleAccountModeFragment.java` em `initializeUI()`, no manipulador de cliques `callGraphApiInteractiveButton`:

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
 **/
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Os aplicativos não devem exigir que o usuário entre sempre que solicitar um token. Se o utilizador já tiver assinado, `acquireTokenSilentAsync()` permite que as aplicações solicitem fichas sem avisar o utilizador, como mostra o ficheiro `MultipleAccountModeFragment.java`, em`initializeUI()` no manipulador de cliques `callGraphApiSilentButton`:

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

O código para remover uma conta, e quaisquer fichas em cache para a conta, está no ficheiro `MultipleAccountModeFragment.java` em `initializeUI()` no manipulador para o botão de conta de remoção. Antes de poder remover uma conta, precisa de um objeto de conta, que obtém de métodos MSAL como `getAccounts()` e `acquireToken()`. Como a remoção de uma conta é uma operação assíncrona, o `onRemoved` chamada é fornecida para atualizar o UI.

```java
/**
 * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
 **/
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

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.json

Esse é o arquivo de configuração para um aplicativo MSAL que usa várias contas.

Consulte o ficheiro de [configuração Android MSAL](msal-configuration.md) para obter uma explicação dos vários campos.

Ao contrário do ficheiro de configuração [auth_config_single_account.json,](#auth_config_single_accountjson) este ficheiro config tem `"account_mode" : "MULTIPLE"` em vez de `"account_mode" : "SINGLE"` porque esta é uma aplicação de conta múltipla.

`"client_id"` é configurado para usar um registo de objetos de aplicação que a Microsoft mantém.
`"redirect_uri"`é configurado para utilizar a chave de assinatura fornecida com a amostra de código.

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

Experimente o [Sign in users e ligue para o Microsoft Graph a partir de um](tutorial-v2-android.md) tutorial de aplicações Android para um guia passo a passo para a construção de uma aplicação Android que obtenha um sinal de acesso e o utilize para ligar para o Microsoft Graph API.

> [!div class="nextstepaction"]
> [Tutorial Chamar a Graph API Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Wiki da biblioteca MSAL para Android

Leia mais informações sobre a biblioteca MSAL para Android:

> [!div class="nextstepaction"]
> [Wiki da biblioteca MSAL para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Ajude-nos a melhorar a plataforma de identidade da Microsoft. Diga-nos o que pensa ao concluir um pequeno inquérito de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa da plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
