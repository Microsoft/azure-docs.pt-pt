---
title: 'Quickstart: Adicione o início de sôs-in com a Microsoft a uma aplicação Android Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, saiba como as aplicações android podem chamar uma API que requer acesso a tokens emitidos pela plataforma de identidade da Microsoft.
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
ms.openlocfilehash: e0f3cd034838d5a9ccb1a5ccdc2567933b2e1dd6
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562207"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Início Rápido: Iniciar sessão dos utilizadores e chamar a Microsoft Graph API a partir de uma aplicação Android

Neste quickstart, você descarrega e execute uma amostra de código que demonstra como uma aplicação Android pode assinar nos utilizadores e obter um token de acesso para ligar para a Microsoft Graph API. 

Veja [como funciona a amostra](#how-the-sample-works) para uma ilustração.

As aplicações devem ser representadas por um objeto de aplicação no Azure Ative Directory para que a plataforma de identidade da Microsoft possa fornecer fichas à sua aplicação.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Android Studio
* Android 16+

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configurar a sua candidatura no portal Azure
>  Para que a amostra de código para este arranque rápido funcione, é necessário adicionar um URI de redirecionamento compatível com o corretor Auth.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas alterações para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-android/green-check.png) A sua aplicação está configurada com estes atributos
>
> ### <a name="step-2-download-the-project"></a>Passo 2: Transferir o projeto
> [!div class="sxs-lookup" renderon="portal"]
> Executar o projeto usando o Android Studio.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passo 3: A sua aplicação está configurada e pronta para correr
> Configurámos o seu projeto com valores das propriedades da sua aplicação e está pronto para funcionar.
> A aplicação de amostra começa no ecrã **do Modo Conta Única.** Um âmbito predefinido, **user.read,** é fornecido por padrão, que é usado ao ler os seus próprios dados de perfil durante a chamada API do Microsoft Graph. O URL para a chamada API do Microsoft Graph é fornecido por padrão. Pode mudar as duas coisas, se quiser.
>
> ![App de amostra MSAL mostrando o uso de conta única e múltipla](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Utilize o menu da aplicação para alterar entre os modos de conta simples e múltiplo.
>
> No modo conta única, inscreva-se utilizando uma conta de trabalho ou de casa:
>
> 1. **Selecione Obter dados gráficos interativamente** para solicitar ao utilizador as suas credenciais. Verá a saída da chamada para a API do Gráfico microsoft na parte inferior do ecrã.
> 2. Uma vez assinado, **selecione Obter os dados do gráfico em silêncio** para fazer uma chamada para a API do Gráfico da Microsoft sem voltar a solicitar ao utilizador credenciais. Verá a saída da chamada para a API do Gráfico microsoft na parte inferior do ecrã.
>
> No modo de conta múltipla, pode repetir os mesmos passos.  Além disso, pode remover a conta de assinatura, que também remove os tokens em cache para essa conta.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> ## <a name="step-1-get-the-sample-app"></a>Passo 1: Obtenha a aplicação da amostra
>
> [Descarregue o código.](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> ## <a name="step-2-run-the-sample-app"></a>Passo 2: Executar a aplicação de amostra
>
> Selecione o seu emulador, ou dispositivo físico, a partir dos **dispositivos disponíveis** do Android Studio dropdown e executar a aplicação.
>
> A aplicação de amostra começa no ecrã **do Modo Conta Única.** Um âmbito predefinido, **user.read,** é fornecido por padrão, que é usado ao ler os seus próprios dados de perfil durante a chamada API do Microsoft Graph. O URL para a chamada API do Microsoft Graph é fornecido por padrão. Pode mudar as duas coisas, se quiser.
>
> ![App de amostra MSAL mostrando o uso de conta única e múltipla](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Utilize o menu da aplicação para alterar entre os modos de conta simples e múltiplo.
>
> No modo conta única, inscreva-se utilizando uma conta de trabalho ou de casa:
>
> 1. **Selecione Obter dados gráficos interativamente** para solicitar ao utilizador as suas credenciais. Verá a saída da chamada para a API do Gráfico microsoft na parte inferior do ecrã.
> 2. Uma vez assinado, **selecione Obter os dados do gráfico em silêncio** para fazer uma chamada para a API do Gráfico da Microsoft sem voltar a solicitar ao utilizador credenciais. Verá a saída da chamada para a API do Gráfico microsoft na parte inferior do ecrã.
>
> No modo de conta múltipla, pode repetir os mesmos passos.  Além disso, pode remover a conta de assinatura, que também remove os tokens em cache para essa conta.

## <a name="how-the-sample-works"></a>Como funciona a amostra
![Screenshot da aplicação da amostra](media/quickstart-v2-android/android-intro.svg)


O código é organizado em fragmentos que mostram como escrever uma única e múltiplas contas app MSAL. Os ficheiros de código são organizados da seguinte forma:

| Ficheiro  | Demonstra  |
|---------|---------|
| MainActivity | Gere a UI |
| MSGraphRequestWrapper  | Chama a API do Gráfico microsoft usando o token fornecido pela MSAL |
| MultipleAccountModeFragment  | Inicializa uma aplicação multi-conta, carrega uma conta de utilizador e recebe um símbolo para ligar para a Microsoft Graph API |
| SingleAccountModeFragment | Inicializa uma aplicação de uma única conta, carrega uma conta de utilizador e recebe um símbolo para ligar para a API do Gráfico microsoft |
| res/auth_config_multiple_account.jsem  | O ficheiro de configuração de várias contas |
| res/auth_config_single_account.jsem  | O ficheiro de configuração de conta única |
| Gradle Scripts/build.grade (Módulo:app) | As dependências da biblioteca MSAL são adicionadas aqui |

Vamos agora analisar estes ficheiros com mais detalhes e chamar o código específico da MSAL em cada um.

### <a name="adding-msal-to-the-app"></a>Adicionar MSAL à app

MSAL[(com.microsoft.identity.client)](https://javadoc.io/doc/com.microsoft.identity.client/msal)é a biblioteca utilizada para assinar nos utilizadores e solicitar fichas usadas para aceder a uma API protegida pela plataforma de identidade Microsoft. Gradle 3.0+ instala a biblioteca quando adiciona o seguinte a **Gradle Scripts**  >  **build.gradle (Módulo: app)** em **Dependências** :

```gradle
implementation 'com.microsoft.identity.client:msal:2.+'
```

Pode ver isso no projeto de amostra em build.gradle (Módulo: app):

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:2.+'
    ...
}
```

Isto instrui Gradle a descarregar e construir MSAL a partir de Maven Central.

### <a name="msal-imports"></a>Importações de MSAL

As importações relevantes para a biblioteca MSAL são `com.microsoft.identity.client.*` .  Por exemplo, você verá `import com.microsoft.identity.client.PublicClientApplication;` qual é o espaço de nome para a `PublicClientApplication` classe, que representa a sua aplicação de cliente público.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment.java

Este ficheiro demonstra como criar uma única aplicação MSAL de conta e chamar a aPI do Microsoft Graph.

As aplicações de conta única são utilizadas apenas por um único utilizador.  Por exemplo, pode ter apenas uma conta com a qual assina na sua aplicação de mapeamento.

#### <a name="single-account-msal-initialization"></a>Inicialização MSAL de conta única

Em `auth_config_single_account.json` , em , é criada uma única conta utilizando a `onCreateView()` `PublicClientApplication` informação config armazenada no `auth_config_single_account.json` ficheiro.  É assim que se inicia a biblioteca MSAL para utilização numa aplicação MSAL de uma única conta:

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

#### <a name="sign-in-a-user"></a>Inscreva-se num utilizador

Em `SingleAccountModeFragment.java` , o código para iniciar a s inscrição num utilizador está em , no manipulador de `initializeUI()` `signInButton` cliques.

Ligue `signIn()` antes de tentar adquirir fichas. `signIn()` comporta-se como se `acquireToken()` fosse chamado, resultando numa solicitação interativa para o utilizador iniciar siva.

A inscrição num utilizador é uma operação assíncronea. É passada uma chamada de retorno que chama a API do Gráfico microsoft e atualiza o UI assim que o utilizador assina:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Assine um utilizador

Em `SingleAccountModeFragment.java` , o código para assinar um utilizador está em , no manipulador de `initializeUI()` `signOutButton` cliques.  A assinatura de um utilizador é uma operação assíncronea. A assinatura do utilizador também limpa a cache simbólica para essa conta. Uma chamada é criada para atualizar a UI assim que a conta de utilizador é assinada:

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

#### <a name="get-a-token-interactively-or-silently"></a>Obtenha um símbolo interativo ou silenciosamente

Para apresentar o menor número de solicitações ao utilizador, normalmente obtém um símbolo em silêncio. Então, se houver um erro, tente chegar a token interativamente. A primeira vez que a aplicação chama `signIn()` , atua efetivamente como uma chamada para `acquireToken()` , o que irá levar o utilizador a obter credenciais.

Algumas situações em que o utilizador pode ser solicitado a selecionar a sua conta, introduzir as suas credenciais ou consentir com as permissões que a sua app solicitou são:

* A primeira vez que o utilizador assina a aplicação
* Se um utilizador redefinir a sua palavra-passe, terá de introduzir as suas credenciais
* Se o consentimento for revogado
* Se a sua aplicação exigir explicitamente consentimento
* Quando a sua aplicação está a solicitar acesso a um recurso pela primeira vez
* Quando são necessárias políticas de acesso condicional ou MFA ou outras

O código para obter um símbolo interativamente, isto é, com uI que envolverá o utilizador, está  `SingleAccountModeFragment.java` em, `initializeUI()` em, no manipulador de `callGraphApiInteractiveButton` cliques:

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

Se o utilizador já se `acquireTokenSilentAsync()` inscreveu, permite que as aplicações solicitem tokens silenciosamente como mostrado `initializeUI()` no , no manipulador de `callGraphApiSilentButton` cliques:

```java
/**
 * Once you've signed the user in,
 * you can perform acquireTokenSilent to obtain resources without interrupting the user.
 **/
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Carregar uma conta

O código para carregar uma conta está `SingleAccountModeFragment.java` em `loadAccount()` .  Carregar a conta do utilizador é uma operação assíncronea, pelo que as chamadas para lidar quando a conta carrega, alterações ou ocorre um erro para o MSAL.  O seguinte código também é tratado `onAccountChanged()` , o que ocorre quando uma conta é removida, o utilizador muda para outra conta, e assim por diante.

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

#### <a name="call-microsoft-graph"></a>Ligue para o Microsoft Graph

Quando um utilizador é assinado, a chamada para o Microsoft Graph é feita através de um pedido HTTP pelo `callGraphAPI()` qual é definido em `SingleAccountModeFragment.java` . Esta função é um invólucro que simplifica a amostra fazendo algumas tarefas, tais como obter o token de acesso do `authenticationResult` e embalar a chamada para o MSGraphRequestWrapper, e exibir os resultados da chamada.

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

### <a name="auth_config_single_accountjson"></a>auth_config_single_account.jsem

Este é o ficheiro de configuração de uma aplicação MSAL que utiliza uma única conta.

Consulte [o ficheiro de configuração DORrso Android ](msal-configuration.md) para obter uma explicação destes campos.

Note a presença de `"account_mode" : "SINGLE"` , que configura esta app para usar uma única conta.

`"client_id"` está pré-configurado para usar um registo de objeto de aplicação que a Microsoft mantém.
`"redirect_uri"`está pré-configurado para utilizar a chave de assinatura fornecida com a amostra de código.

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

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment.java

Este ficheiro demonstra como criar uma app MSAL de várias contas e chamar uma API do Microsoft Graph.

Um exemplo de uma aplicação de várias contas é uma aplicação de correio que permite trabalhar com várias contas de utilizador, como uma conta de trabalho e uma conta pessoal.

#### <a name="multiple-account-msal-initialization"></a>Inicialização múltiplo de MSAL de conta múltipla

No `MultipleAccountModeFragment.java` ficheiro, em `onCreateView()` , um objeto de aplicação de conta múltipla ( - é criado `IMultipleAccountPublicClientApplication` usando a informação config armazenada no `auth_config_multiple_account.json file` :

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

As aplicações de várias contas costumam ligar `getAccounts()` para selecionar a conta a utilizar para operações DESMS. O código para carregar uma conta está no `MultipleAccountModeFragment.java` ficheiro, em  `loadAccounts()` .  Carregar a conta do utilizador é uma operação assíncronea. Assim, uma chamada trata das situações quando a conta é carregada, altera ou ocorre um erro.

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

#### <a name="get-a-token-interactively-or-silently"></a>Obtenha um símbolo interativo ou silenciosamente

Algumas situações em que o utilizador pode ser solicitado a selecionar a sua conta, introduzir as suas credenciais ou consentir com as permissões que a sua app solicitou são:

* A primeira vez que os utilizadores iniciam sessão na aplicação
* Se um utilizador redefinir a sua palavra-passe, terá de introduzir as suas credenciais
* Se o consentimento for revogado
* Se a sua aplicação exigir explicitamente consentimento
* Quando a sua aplicação está a solicitar acesso a um recurso pela primeira vez
* Quando são necessárias políticas de acesso condicional ou MFA ou outras

Várias aplicações de conta devem normalmente adquirir tokens interativamente, isto é, com uI que envolve o utilizador, com uma chamada para `acquireToken()` .  O código para obter um símbolo interativamente está `MultipleAccountModeFragment.java` no ficheiro, `initializeUI()` no manipulador de `callGraphApiInteractiveButton` cliques:

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

As aplicações não devem exigir que o utilizador assine sempre que solicita um token. Se o utilizador já se `acquireTokenSilentAsync()` inscreveu, permite que as aplicações solicitem fichas sem solicitar ao utilizador, como mostra o `MultipleAccountModeFragment.java` ficheiro, `initializeUI()` no manipulador de `callGraphApiSilentButton` cliques:

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

O código para remover uma conta, e quaisquer fichas em cache para a conta, está `MultipleAccountModeFragment.java` no ficheiro no manipulador para o `initializeUI()` botão de conta remover. Antes de poder remover uma conta, precisa de um objeto de conta, que obtém a partir de métodos MSAL como `getAccounts()` e `acquireToken()` . Como remover uma conta é uma operação assíncronea, a `onRemoved` chamada é fornecida para atualizar a UI.

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

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.jsem

Este é o ficheiro de configuração de uma aplicação MSAL que utiliza várias contas.

Consulte [o ficheiro de configuração DORrso Android ](msal-configuration.md) para obter uma explicação dos vários campos.

Ao contrário do [auth_config_single_account.jsno](#auth_config_single_accountjson) ficheiro de configuração, este ficheiro config tem em vez de ser porque esta é `"account_mode" : "MULTIPLE"` uma `"account_mode" : "SINGLE"` aplicação de várias contas.

`"client_id"` está pré-configurado para usar um registo de objeto de aplicação que a Microsoft mantém.
`"redirect_uri"`está pré-configurado para utilizar a chave de assinatura fornecida com a amostra de código.

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

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Passe para o tutorial android no qual constrói uma aplicação Android que obtém um token de acesso a partir da plataforma de identidade da Microsoft e a usa para ligar para a Microsoft Graph API.

> [!div class="nextstepaction"]
> [Tutorial: Inscreva-se nos utilizadores e ligue para o Microsoft Graph a partir de uma aplicação Android](tutorial-v2-android.md)
