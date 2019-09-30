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
ms.date: 09/16/2019
ms.author: twhitney
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: e11e47952f70ce0cd212ca93eff1c38f2b3993a8
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678058"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Início rápido: Conectar usuários e chamar a API de Microsoft Graph de um aplicativo Android

Este guia de início rápido usa um exemplo de código para demonstrar como um aplicativo Android pode entrar em contas pessoais, corporativas ou de estudante e, em seguida, obter um token de acesso e chamar a API de Microsoft Graph.

![Screenshoft do aplicativo de exemplo](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Pré-requisitos**
> * Android Studio 
> * O Android 16 + é necessário

## <a name="step-1-get-the-sample-app"></a>Passo 1: Obter a aplicação de exemplo

[Clone o código](https://github.com/Azure-Samples/ms-identity-android-java.git).

## <a name="step-2-register-your-application"></a>Passo 2: Registar a sua aplicação

Para registrar um objeto de aplicativo e adicionar as informações de registro do objeto de aplicativo ao projeto de exemplo manualmente, siga estas etapas:

1. Aceda ao [Portal do Azure](https://aka.ms/MobileAppReg).
1. Abra a [folha registros de aplicativo](https://portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) e clique em **+ novo registro**.
1. Insira um **nome** para o registro do aplicativo e, em seguida, sem definir um URI de redirecionamento, clique em **registrar**.
1. Na seção **gerenciar** , selecione **autenticação** >  **+ Adicionar uma plataforma** > **Android**. (Você pode precisar selecionar **experimentar a nova experiência** perto da parte superior da folha para ver essa tela)
1. Insira o nome do **pacote**do seu projeto, que é `com.azuresamples.msalandroidapp`.
1. Na seção de **hash de assinatura** da página **configurar seu aplicativo Android** , clique em **gerando um hash de assinatura de desenvolvimento**e copie o comando keytool a ser usado para a plataforma que você está usando para desenvolver seu aplicativo Android.

   > [!Note]
   > Keytool. exe é instalado como parte do Java Development Kit (JDK). Você também deve instalar a ferramenta OpenSSL para executar o comando keytool.  Você precisará do keytool e do diretório OpenSSL\bin em seu caminho.

1. Execute o comando keytool copiado do portal em uma janela de terminal.
1. Insira o hash de assinatura gerado no portal em **hash de assinatura**.
1. Clique em `Configure` e faça uma cópia da **configuração MSAL**. Você copiará e colará isso em um arquivo de configuração na próxima etapa. Clique em **Concluído**.

## <a name="step-3-add-your-app-registration"></a>Passo 3: Adicionar o registro do aplicativo

1. Abra o projeto de exemplo no Android Studio.
1. Dentro do **aplicativo** > **res** > **RAW**, abra **auth_config_multiple_account. JSON**.  Cole o conteúdo da configuração MSAL. Isso adiciona a ID do cliente, a ID do locatário e o redirect_uri do Portal. Ele terá uma aparência semelhante a esta, mas com os valores preenchidos para a ID do cliente, ID do locatário e redirect_uri:

    ```json
    {
      "client_id" : "<your_client_id_here>",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "<your_redirect_uri_here>",
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

1. Abra o **aplicativo** > **res** > **RAW**, abra **auth_config_single_account. JSON**e cole o conteúdo da configuração do MSAL. Ele se parecerá com o arquivo **auth_config_multiple_account. JSON** acima.
1. Nos**manifestos**do **aplicativo** >   > **AndroidManifest. xml**, localize a atividade `BrowserTabActivity`. Essa entrada permite que a Microsoft chame de volta para seu aplicativo depois de concluir a autenticação:

    ```xml
    ...
    <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
                <intent-filter>
                    <action android:name="android.intent.action.VIEW" />
    
                    <category android:name="android.intent.category.DEFAULT" />
                    <category android:name="android.intent.category.BROWSABLE" />
    
                    <!--
                        Add in your scheme/host from registered redirect URI
                        note that the leading "/" is required for android:path
                        For Example:
                        <data
                            android:host="com.azuresamples.msalandroidapp"
                            android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
                            android:scheme="msauth" />
                    -->
    
                    <data
                        android:host="YOUR_PACKAGE_NAME - must be registered at https://aka.ms/MobileAppReg"
                        android:path="/YOUR_DECODED_SIGNATURE_HASH - must be registered at https://aka.ms/MobileAppReg"
                        android:scheme="msauth" />
                </intent-filter>
            </activity>
    ```
    
1. Substitua o nome do pacote pelo que você registrou na portal do Azure para o valor de `android:host=`.  Nesse caso, será: `com.azuresamples.msalandroidapp`.

    > [!IMPORTANT]
    > O valor de **caminho Android:** **deve** ter um caractere "/" à esquerda, ou você receberá uma linha vermelha abaixo do valor e o aplicativo de exemplo não será executado.
     
1. Substitua o hash de chave que você obteve ao executar o keytool anteriormente e inserido na portal do Azure, para o valor de `android:path=`. O hash de assinatura não deve ser codificado em URL.

## <a name="step-4-run-the-sample-app"></a>Passo 4: Execute a aplicação de exemplo

Selecione seu emulador ou dispositivo na lista suspensa de **dispositivos disponíveis** do Android Studio e execute o aplicativo.

O aplicativo de exemplo inicia na tela de **modo de conta única** . Um escopo padrão, **User. Read**, é fornecido por padrão, que é usado ao ler seus próprios dados de perfil durante a chamada à API de Microsoft Graph. A URL para a chamada à API de Microsoft Graph é fornecida por padrão. Você pode alterar ambos se desejar.

![Aplicativo de exemplo MSAL mostrando o uso de conta única e múltipla](./media/quickstart-v2-android/quickstart-sample-app.png)

Use o menu do aplicativo para alterar entre os modos de conta única e múltipla.

No modo de conta única, entre usando uma conta corporativa ou doméstica:

1. Selecione **obter dados de grafo interativamente** para solicitar ao usuário suas credenciais. Você verá a saída da chamada para a API de Microsoft Graph na parte inferior da tela.
2. Depois de conectado, selecione **obter dados do grafo silenciosamente** para fazer uma chamada para a API de Microsoft Graph sem solicitar as credenciais do usuário novamente. Você verá a saída da chamada para a API de Microsoft Graph na parte inferior da tela.

No modo de conta múltipla, você pode repetir as mesmas etapas.  Além disso, você pode remover a conta conectada, que também remove os tokens armazenados em cache para essa conta.

## <a name="how-the-sample-works"></a>Como funciona o exemplo

O código é organizado em fragmentos que mostram como escrever um aplicativo MSAL único e de várias contas. Os arquivos de código são organizados da seguinte maneira:

| Ficheiro  | Demonstrar  |
|---------|---------|
| MainActivity | Gerencia a interface do usuário |
| MSGraphRequestWrapper  | Chama a API de Microsoft Graph usando o token fornecido por MSAL |
| MultipleAccountModeFragment  | Inicializa um aplicativo de várias contas, carrega uma conta de usuário e Obtém um token para chamar a API de Microsoft Graph |
| SingleAccountModeFragment | Inicializa um aplicativo de conta única, carrega uma conta de usuário e Obtém um token para chamar a API de Microsoft Graph |
| res/auth_config_multiple_account. JSON  | O arquivo de configuração de várias contas |
| res/auth_config_single_account. JSON  | O arquivo de configuração de conta única |
| Scripts gradle/Build. grau (módulo: aplicativo) | As dependências da biblioteca MSAL são adicionadas aqui |

Agora vamos examinar esses arquivos mais detalhadamente e chamar o código específico do MSAL em cada um.

### <a name="add-msal-to-the-app"></a>Adicionar MSAL ao aplicativo

MSAL ([com. Microsoft. Identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft. O gradle 3.0 + instala a biblioteca quando você adiciona o seguinte a **scripts Gradle** > **Build. gradle (módulo: aplicativo)** em **dependências**:

```gradle  
implementation 'com.microsoft.identity.client:msal:1.0.0'
```

Você pode ver isso no projeto de exemplo em Build. gradle (módulo: aplicativo):

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.0.0-RC7'
    ...
}
```

Isso instrui o gradle a baixar e compilar o MSAL da central do Maven.

### <a name="msal-imports"></a>Importações do MSAL

As importações que são relevantes para a biblioteca MSAL são `com.microsoft.identity.client.*`.  Por exemplo, você verá `import com.microsoft.identity.client.PublicClientApplication;`, que é o namespace para a classe `PublicClientApplication`, que representa seu aplicativo cliente público.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment. java

Este arquivo demonstra como criar um aplicativo MSAL de conta única e chamar uma API Microsoft Graph.

Os aplicativos de conta única são usados apenas por um único usuário.  Por exemplo, você pode ter apenas uma conta na qual você entra no seu aplicativo de mapeamento.

#### <a name="single-account-msal-initialization"></a>Inicialização de MSAL de conta única

No `onCreateView()`, uma única conta `PublicClientApplication` é criada usando as informações de configuração armazenadas no arquivo `auth_config_single_account.json`.  É assim que você inicializa a biblioteca MSAL para uso em um aplicativo MSAL de conta única:

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

O código para entrar em um usuário está em `initializeUI()`, no manipulador de cliques `signInButton`.

Chame `signIn()` antes de tentar adquirir tokens. `signIn()` se comporta como se `acquireToken()` for chamado, resultando em um prompt interativo para o usuário entrar.

A entrada de um usuário é uma operação assíncrona. É passado um retorno de chamada que chama a API de Microsoft Graph e atualiza a interface do usuário quando ele faz logon:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Desconectar um usuário

O código para sair de um usuário está em `initializeUI()`, no manipulador de cliques `signOutButton`.  A inscrição de um usuário é uma operação assíncrona. A inscrição do usuário também limpa o cache de token para essa conta. Um retorno de chamada é criado para atualizar a interface do usuário depois que a conta de usuários é desconectada:

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

Para apresentar o menor número de prompts para o usuário, você normalmente obterá um token silenciosamente. Em seguida, se houver um erro, tente obter o token interativamente. Na primeira vez que o aplicativo chama `signIn()`, ele age efetivamente como uma chamada para `acquireToken()`, o que solicitará ao usuário as credenciais.

Algumas situações em que o usuário pode ser solicitado a selecionar sua conta, inserir suas credenciais ou consentir as permissões solicitadas pelo seu aplicativo são:

* Na primeira vez que o usuário entra no aplicativo
* Se um usuário redefinir sua senha, ele precisará inserir suas credenciais
* Se o consentimento for revogado
* Se seu aplicativo requer explicitamente consentimento
* Quando seu aplicativo está solicitando acesso a um recurso pela primeira vez
* Quando MFA ou outras políticas de acesso condicional são necessárias

O código para obter um token interativamente, ou seja, com a interface do usuário que envolve o User, está em `initializeUI()`, no manipulador de cliques `callGraphApiInteractiveButton`:

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

Se o usuário já tiver entrado, `acquireTokenSilentAsync()` permitirá que os aplicativos solicitem tokens silenciosamente, conforme mostrado em `initializeUI()`, no manipulador de cliques `callGraphApiSilentButton`:

```java
/**
  * Once you've signed the user in,
  * you can perform acquireTokenSilent to obtain resources without interrupting the user.
  */
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Carregar uma conta

O código para carregar uma conta está em `loadAccount()`.  Carregar a conta do usuário é uma operação assíncrona, portanto, os retornos de chamada para manipular quando a conta é carregada, as alterações ou um erro é passado para MSAL.  O código a seguir também manipula `onAccountChanged()`, que ocorre quando uma conta é removida, o usuário muda para outra conta e assim por diante.

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

Quando um usuário é conectado, a chamada para Microsoft Graph é feita por meio de uma solicitação HTTP por `callGraphAPI()`. Essa função é um wrapper que simplifica o exemplo fazendo algumas tarefas, como obter o token de acesso do `authenticationResult` e empacotar a chamada para o MSGraphRequestWrapper e exibir os resultados da chamada.

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

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
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

No `onCreateView()`, um objeto de aplicativo de várias contas (`IMultipleAccountPublicClientApplication`) é criado usando as informações de configuração armazenadas no `auth_config_multiple_account.json file`:

```java
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

O objeto `MultipleAccountPublicClientApplication` criado é armazenado em uma variável de membro de classe para que possa ser usado para interagir com a biblioteca MSAL para adquirir tokens e carregar e remover a conta de usuário.

#### <a name="load-an-account"></a>Carregar uma conta

Aplicativos de conta múltipla geralmente chamam `GetAccounts()` para selecionar a conta a ser usada para operações de MSAL. O código para carregar uma conta está em `loadAccount()`.  O carregamento da conta do usuário é uma operação assíncrona. Portanto, um retorno de chamada manipula as situações em que a conta é carregada, muda ou ocorre um erro.

```java
/**
    * Load the currently signed-in account, if there's any.
    * In the shared device mode, if the user is signed out from the device, the app can also perform the clean-up work in onAccountChanged().
    */
private void loadAccount() {
    ...
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

Os aplicativos de conta múltipla normalmente devem adquirir tokens interativamente, ou seja, com a interface do usuário que envolve a usuária, com uma chamada para `acquireToken()`.  O código para obter um token interativamente está em `initializeUI()`, no manipulador de cliques `callGraphApiInteractiveButton`:

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

Os aplicativos não devem exigir que o usuário entre sempre que solicitar um token. Se o usuário já tiver entrado, `acquireTokenSilentAsync()` permitirá que os aplicativos solicitem tokens sem avisar o usuário, conforme mostrado em `initializeUI()` no manipulador de cliques `callGraphApiSilentButton`:

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

O código para remover uma conta e todos os tokens em cache para a conta está em `initializeUI()` no manipulador para o botão Remover conta. Antes de remover uma conta, você precisa de um objeto de conta, que você obtém de funções MSAL como `getAccounts()` e `acquireToken()`. Como a remoção de uma conta é uma operação assíncrona, o retorno de chamada `onRemoved` é fornecido para atualizar a interface do usuário.

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
                loadAccount();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account. JSON

Esse é o arquivo de configuração para um aplicativo MSAL que usa várias contas.

Consulte [entender o arquivo de configuração do Android MSAL](msal-configuration.md) para obter uma explicação desses campos.

Ao contrário do arquivo de configuração [auth_config_single_account. JSON](#auth_config_single_accountjson) , esse arquivo de configuração tem `"account_mode" : "MULTIPLE"` em vez de `"account_mode" : "SINGLE"` porque este é um aplicativo de várias contas.

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
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
