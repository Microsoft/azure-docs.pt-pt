---
title: Plataforma de identidade Microsoft Android quickstart [ Azure
description: Saiba como as aplicações Android podem chamar um API que requer acesso a fichas por parte da plataforma de identidade da Microsoft.
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
ms.openlocfilehash: a184b035e3296f82ecdacf74a99ea7148d99bd49
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78271104"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Início Rápido: Iniciar sessão dos utilizadores e chamar a Microsoft Graph API a partir de uma aplicação Android

Este quickstart usa uma amostra de código para demonstrar como uma aplicação Android pode assinar em contas pessoais, de trabalho ou escolares usando a plataforma de identidade da Microsoft, e depois obter um sinal de acesso e ligar para a Microsoft Graph API. (Ver [como funciona a amostra](#how-the-sample-works) para uma ilustração.)

As aplicações devem ser representadas por um objeto de aplicação no Azure Ative Directory para que a plataforma de identidade da Microsoft possa fornecer tokens à sua aplicação.

> [!div renderon="docs"]
> Como conveniência, a amostra de `redirect_uri` código vem com `AndroidManifest.xml` um predefinido prereconfigurado no ficheiro para que não tenha primeiro de registar o seu próprio objeto de aplicação. A `redirect_uri` baseia-se em parte na chave de assinatura da sua aplicação. O projeto da amostra é reconfigurado com `redirect_uri` uma chave de assinatura para que o fornecido funcione. Para saber mais sobre o registo de um objeto de aplicação e a sua integração com a sua aplicação, consulte o Sign in users e ligue para o Microsoft Graph a partir de um tutorial [de aplicações Android.](tutorial-v2-android.md)


> [!NOTE]
> **Pré-requisitos**
> * Android Studio 
> * Android 16+

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configure a sua aplicação no portal Azure 
>  Para a amostra de código para este arranque rápido funcionar, você precisa adicionar um URI redireccionador compatível com o corretor Auth.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas mudanças para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-android/green-check.png) A sua aplicação está configurada com estes atributos
>
> ### <a name="step-2-download-the-project"></a>Passo 2: Transferir o projeto 
> [!div class="sxs-lookup" renderon="portal"]
> Executar o projeto usando o Android Studio.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Passo 3: A sua aplicação está configurada e pronta para ser executada
> Configurámos o seu projeto com valores das propriedades da sua aplicação e está pronto para ser executado. 
> A aplicação da amostra começa no ecrã modo **de conta única.** Um âmbito predefinido, **user.read**, é fornecido por predefinição, que é usado ao ler os dados do seu próprio perfil durante a chamada DaPI do Microsoft Graph. O URL da chamada Microsoft Graph API é fornecido por padrão. Pode mudar os dois, se quiser.
>
> ![Aplicação de amostra MSAL mostrando uso de conta única e múltipla](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Utilize o menu da aplicação para alterar entre modos de conta simples e múltiplos.
>
> No modo de conta única, inscreva-se utilizando uma conta de trabalho ou de casa:
>
> 1. Selecione **Obter dados de gráfico interactivamente** para solicitar ao utilizador as suas credenciais. Verá a saída da chamada para a Microsoft Graph API na parte inferior do ecrã.
> 2. Uma vez inscrito, selecione **Obter dados de gráfico silenciosamente** para fazer uma chamada para a Microsoft Graph API sem pedir ao utilizador novamente credenciais. Verá a saída da chamada para a Microsoft Graph API na parte inferior do ecrã.
>
> Em vários modos de conta, pode repetir os mesmos passos.  Além disso, pode remover a conta de entrada assinada, que também remove as fichas em cache para essa conta.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here

> [!div renderon="docs"]
> ## <a name="step-1-get-the-sample-app"></a>Passo 1: Obtenha a aplicação da amostra
>
> [Descarregue o código.](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> ## <a name="step-2-run-the-sample-app"></a>Passo 2: Executar a aplicação da amostra
>
> Selecione o seu emulador, ou dispositivo físico, a partir do drop down dos **dispositivos disponíveis** do Android Studio e execute a aplicação.
>
> A aplicação da amostra começa no ecrã modo **de conta única.** Um âmbito predefinido, **user.read**, é fornecido por predefinição, que é usado ao ler os dados do seu próprio perfil durante a chamada DaPI do Microsoft Graph. O URL da chamada Microsoft Graph API é fornecido por padrão. Pode mudar os dois, se quiser.
>
> ![Aplicação de amostra MSAL mostrando uso de conta única e múltipla](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Utilize o menu da aplicação para alterar entre modos de conta simples e múltiplos.
>
> No modo de conta única, inscreva-se utilizando uma conta de trabalho ou de casa:
>
> 1. Selecione **Obter dados de gráfico interactivamente** para solicitar ao utilizador as suas credenciais. Verá a saída da chamada para a Microsoft Graph API na parte inferior do ecrã.
> 2. Uma vez inscrito, selecione **Obter dados de gráfico silenciosamente** para fazer uma chamada para a Microsoft Graph API sem pedir ao utilizador novamente credenciais. Verá a saída da chamada para a Microsoft Graph API na parte inferior do ecrã.
>
> Em vários modos de conta, pode repetir os mesmos passos.  Além disso, pode remover a conta de entrada assinada, que também remove as fichas em cache para essa conta.

## <a name="how-the-sample-works"></a>Como funciona a amostra
![Screenshot da aplicação da amostra](media/quickstart-v2-android/android-intro.svg)


O código é organizado em fragmentos que mostram como escrever uma aplicação MSAL de contas únicas e múltiplas. Os ficheiros de código são organizados da seguinte forma:

| Ficheiro  | Demonstra  |
|---------|---------|
| Atividade Principal | Gere a UI |
| Invólucro mSGraphRequest  | Chama o Microsoft Graph API usando o símbolo fornecido pela MSAL |
| MultiAccountModeFragment  | Inicializa uma aplicação multi-conta, carrega uma conta de utilizador e recebe um sinal para ligar para a Microsoft Graph API |
| SingleAccountModeFragment | Inicializa uma aplicação de uma única conta, carrega uma conta de utilizador e recebe um sinal para ligar para a Microsoft Graph API |
| res/auth_config_multiple_account.json  | O ficheiro de configuração de conta múltipla |
| res/auth_config_single_account.json  | O ficheiro de configuração de conta única |
| Gradle Scripts/build.grade (Módulo:app) | As dependências da biblioteca MSAL são adicionadas aqui |

Vamos agora analisar estes ficheiros com mais detalhes e chamar o código específico da MSAL em cada um.

### <a name="adding-msal-to-the-app"></a>Adicionar MSAL à app

MSAL[(com.microsoft.identity.client)](https://javadoc.io/doc/com.microsoft.identity.client/msal)é a biblioteca usada para assinar utilizadores e solicitar fichas usadas para aceder a uma API protegida pela plataforma de identidade microsoft. Gradle 3.0+ instala a biblioteca quando adiciona o seguinte a **Gradle Scripts** > **build.gradle (Módulo: app)** em **Dependências:**

```gradle  
implementation 'com.microsoft.identity.client:msal:1.+'
```

Pode ver isso no projeto de amostra em build.gradle (Módulo: app):

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.+'
    ...
}
```

Isto instrui Gradle a descarregar e construir MSAL a partir da central maven.

### <a name="msal-imports"></a>Importações de MSAL

As importações relevantes para a biblioteca `com.microsoft.identity.client.*`MSAL são.  Por exemplo, você `import com.microsoft.identity.client.PublicClientApplication;` verá qual é o `PublicClientApplication` espaço de nome para a classe, que representa a sua aplicação de cliente público.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment.java

Este ficheiro demonstra como criar uma única aplicação MSAL de conta e chamar um Microsoft Graph API.

As aplicações de conta única são usadas apenas por um único utilizador.  Por exemplo, pode ter apenas uma conta com a qual assina na sua aplicação de mapeamento.

#### <a name="single-account-msal-initialization"></a>Inicialização de MSAL de conta única

Em, `auth_config_single_account.json` `onCreateView()`in , `PublicClientApplication` é criada uma única conta utilizando `auth_config_single_account.json` as informações de config armazenadas no ficheiro.  É assim que se inicia a biblioteca MSAL para utilização numa aplicação MSAL de uma única conta:

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

No `SingleAccountModeFragment.java`manual, o código para `initializeUI()`iniciar sessão num utilizador está no manipulador de `signInButton` cliques.

Ligue `signIn()` antes de tentar adquirir fichas. `signIn()`comporta-se `acquireToken()` como se fosse chamado, resultando num pedido interativo para o utilizador iniciar sessão.

A assinatura num utilizador é uma operação assíncrona. É passado um backback que liga para o Microsoft Graph API e atualiza o UI assim que o utilizador assinar:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Assine um utilizador

No `SingleAccountModeFragment.java`manual, o código para `initializeUI()`assinar um `signOutButton` utilizador está no manipulador de cliques.  Assinar um utilizador é uma operação assíncrona. A assinatura do utilizador também limpa a cache simbólica para essa conta. É criado um backback para atualizar o UI assim que a conta de utilizador for assinada:

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

#### <a name="get-a-token-interactively-or-silently"></a>Obtenha um símbolo interativamente ou silenciosamente

Para apresentar o menor número de solicitações ao utilizador, você normalmente receberá um token silenciosamente. Então, se houver um erro, tente chegar a um símbolo interactivamente. A primeira vez `signIn()`que a aplicação chama `acquireToken()`, funciona efetivamente como uma chamada para , o que irá levar o utilizador a credenciais.

Algumas situações em que o utilizador pode ser solicitado a selecionar a sua conta, introduzir as suas credenciais ou consentir com as permissões que a sua app solicitou são:

* A primeira vez que o utilizador entra na aplicação
* Se um utilizador redefinir a sua palavra-passe, terá de introduzir as suas credenciais
* Se o consentimento for revogado
* Se a sua aplicação necessitar explicitamente de consentimento
* Quando a sua aplicação está a solicitar acesso a um recurso pela primeira vez
* Quando mFA ou outras políticas de acesso condicional são necessárias

O código para obter um token interativamente, isto é, com `SingleAccountModeFragment.java`UI que envolverá o utilizador, está `initializeUI()`dentro, dentro , no manipulador de `callGraphApiInteractiveButton` cliques:

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

Se o utilizador já `acquireTokenSilentAsync()` se inscreveu, permite que as aplicações solicitem fichas silenciosamente como mostrado no `initializeUI()`manipulador de `callGraphApiSilentButton` cliques:

```java
/**
 * Once you've signed the user in,
 * you can perform acquireTokenSilent to obtain resources without interrupting the user.
 **/
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Carregar uma conta

O código para carregar `SingleAccountModeFragment.java` uma `loadAccount()`conta está dentro .  Carregar a conta do utilizador é uma operação assíncrona, pelo que as chamadas para manusear quando a conta carrega, alterações ou um erro ocorre são passadas para MSAL.  O código que `onAccountChanged()`se segue também manuseia , o que ocorre quando uma conta é removida, o utilizador muda para outra conta, e assim por diante.

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

Quando um utilizador é inscrito, a chamada para o `callGraphAPI()` Microsoft Graph `SingleAccountModeFragment.java`é feita através de um pedido HTTP pelo qual é definido em . Esta função é um invólucro que simplifica a amostra fazendo algumas `authenticationResult` tarefas, tais como obter o token de acesso do e embalar a chamada para o MSGraphRequestWrapper, e exibir os resultados da chamada.

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

Este é o ficheiro de configuração de uma aplicação MSAL que utiliza uma única conta.

Consulte o ficheiro de [configuração Android MSAL](msal-configuration.md) para obter uma explicação destes campos.

Note a `"account_mode" : "SINGLE"`presença de , que configura esta app para usar uma única conta.

`"client_id"`é configurado para utilizar um registo de objetos de aplicação que a Microsoft mantém.
`"redirect_uri"`é reconfigurada para utilizar a chave de assinatura fornecida com a amostra de código.

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

Este ficheiro demonstra como criar uma aplicação MSAL de várias contas e chamar um Microsoft Graph API.

Um exemplo de uma aplicação de várias contas é uma aplicação de correio que lhe permite trabalhar com várias contas de utilizador, como uma conta de trabalho e uma conta pessoal.

#### <a name="multiple-account-msal-initialization"></a>Ininicialização msal de conta múltipla

No `MultipleAccountModeFragment.java` ficheiro, `onCreateView()`em, é criado`IMultipleAccountPublicClientApplication`um objeto de aplicação de conta `auth_config_multiple_account.json file`múltipla ( ) utilizando as informações de config armazenadas no :

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

O `MultipleAccountPublicClientApplication` objeto criado é armazenado numa variável de membro da classe para que possa ser usado para interagir com a biblioteca MSAL para adquirir fichas e carregar e remover a conta de utilizador.

#### <a name="load-an-account"></a>Carregar uma conta

Várias aplicações `getAccounts()` de conta geralmente ligam para selecionar a conta para usar para operações MSAL. O código para carregar uma `MultipleAccountModeFragment.java` conta `loadAccounts()`está no ficheiro, em .  Carregar a conta do utilizador é uma operação assíncrona. Assim, uma chamada lida com as situações quando a conta é carregada, altera ou ocorre um erro.

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

#### <a name="get-a-token-interactively-or-silently"></a>Obtenha um símbolo interativamente ou silenciosamente

Algumas situações em que o utilizador pode ser solicitado a selecionar a sua conta, introduzir as suas credenciais ou consentir com as permissões que a sua app solicitou são:

* A primeira vez que os utilizadores iniciam sessão na aplicação
* Se um utilizador redefinir a sua palavra-passe, terá de introduzir as suas credenciais 
* Se o consentimento for revogado 
* Se a sua aplicação necessitar explicitamente de consentimento 
* Quando a sua aplicação está a solicitar acesso a um recurso pela primeira vez
* Quando mFA ou outras políticas de acesso condicional são necessárias

Várias aplicações de conta devem normalmente adquirir fichas interativamente, isto é, `acquireToken()`com UI que envolve o utilizador, com uma chamada para .  O código para obter um token `MultipleAccountModeFragment.java` interativamente está no ficheiro no `initializeUI()`manipulador de `callGraphApiInteractiveButton` cliques:

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

As aplicações não devem exigir que o utilizador assine cada vez que pede um sinal. Se o utilizador já `acquireTokenSilentAsync()` tiver assinado, permite que as aplicações solicitem `MultipleAccountModeFragment.java` fichas`initializeUI()` sem `callGraphApiSilentButton` solicitar ao utilizador, como mostra o ficheiro, no manipulador de cliques:

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

O código para remover uma conta, e quaisquer fichas `MultipleAccountModeFragment.java` em `initializeUI()` cache para a conta, está no ficheiro no manipulador para o botão de remoção da conta. Antes de poder remover uma conta, precisa de um objeto de `getAccounts()` `acquireToken()`conta, que obtém de métodos MSAL como e . Como a remoção de uma conta é uma `onRemoved` operação assíncrona, o backback é fornecido para atualizar o UI.

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

Este é o ficheiro de configuração de uma aplicação MSAL que utiliza várias contas.

Consulte o ficheiro de [configuração Android MSAL](msal-configuration.md) para obter uma explicação dos vários campos.

Ao contrário do ficheiro de configuração [auth_config_single_account.json,](#auth_config_single_accountjson) este ficheiro config tem `"account_mode" : "MULTIPLE"` em vez de porque `"account_mode" : "SINGLE"` esta é uma aplicação de conta múltipla.

`"client_id"`é configurado para utilizar um registo de objetos de aplicação que a Microsoft mantém.
`"redirect_uri"`é reconfigurada para utilizar a chave de assinatura fornecida com a amostra de código.

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
