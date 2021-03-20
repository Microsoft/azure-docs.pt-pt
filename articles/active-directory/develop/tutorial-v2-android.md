---
title: 'Tutorial: Criar uma aplicação Android que utilize a plataforma de identidade da Microsoft para autenticação | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, você constrói uma aplicação Android que utiliza a plataforma de identidade da Microsoft para iniciar sípido nos utilizadores e obter um token de acesso para ligar para a Microsoft Graph API em seu nome.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 7d297d96ba764c812a3d4db6d9383122c73cfe31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100103146"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-application"></a>Tutorial: Inscreva-se nos utilizadores e ligue para a Microsoft Graph API a partir de uma aplicação Android

Neste tutorial, você constrói uma aplicação Android que se integra com a plataforma de identidade da Microsoft para iniciar sismo nos utilizadores e obter um token de acesso para ligar para a Microsoft Graph API.

Quando tiver concluído este tutorial, a sua aplicação aceitará inscrições de contas pessoais da Microsoft (incluindo outlook.com, live.com e outras) bem como contas de trabalho ou escola de qualquer empresa ou organização que utilize o Azure Ative Directory.

Neste tutorial: 

> [!div class="checklist"]
> * Criar um projeto de aplicativo Android no *Android Studio*
> * Registe a app no portal Azure
> * Adicione código para suportar a inscrição do utilizador e a s inscrição
> * Adicione código para ligar para a Microsoft Graph API
> * Testar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

* Android Studio 3.5+

## <a name="how-this-tutorial-works"></a>Como funciona este tutorial

![Mostra como funciona a app de amostras gerada por este tutorial](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

A aplicação neste tutorial irá iniciar snuários nos utilizadores e obter dados em seu nome. Estes dados serão acedidos através de uma API protegida (Microsoft Graph API) que requer autorização e está protegida pela plataforma de identidade da Microsoft.

Mais especificamente:

* A sua aplicação irá iniciar súbs no utilizador através de um browser ou do Microsoft Authenticator e do Portal da Empresa Intune.
* O utilizador final aceitará as permissões que a sua aplicação solicitou.
* A sua aplicação será emitida um token de acesso para a API do Gráfico da Microsoft.
* O token de acesso será incluído no pedido HTTP para a API web.
* Processe a resposta do Microsoft Graph.

Esta amostra utiliza a Biblioteca de Autenticação do Microsoft para Android (MSAL) para implementar a autenticação: [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

A MSAL renovará automaticamente os tokens, entregará um único sign-on (SSO) entre outras aplicações no dispositivo e gerirá a Conta(s).

Este tutorial demonstra exemplos simplificados de trabalhar com a MSAL para Android. Para simplificar, utiliza apenas o Modo Conta Única. Para explorar cenários mais complexos, consulte uma amostra de [código de trabalho](https://github.com/Azure-Samples/ms-identity-android-java/) completa no GitHub.

## <a name="create-a-project"></a>Criar um projeto
Se ainda não tem uma aplicação Android, siga estes passos para criar um novo projeto.

1. Abra o Android Studio e selecione **Iniciar um novo projeto Android Studio**.
2. Selecione **A Atividade Básica** e selecione **Seguinte**.
3. Dê um nome à aplicação.
4. Guarde o nome do pacote. Vai inseri-lo mais tarde no portal Azure.
5. Mude a língua de **Kotlin** para **Java.**
6. Desa estatua o **nível mínimo de API** para **API 19** ou superior, e clique em **Terminar**.
7. Na visão do projeto, **escolha** Project no dropdown para exibir ficheiros de projetos de origem e não fonte, abrir **app/build.gradle** e definir `targetSdkVersion` para `28` .

## <a name="integrate-with-the-microsoft-authentication-library"></a>Integre-se na Biblioteca de Autenticação da Microsoft

### <a name="register-your-application"></a>Registar a aplicação

1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
1. Procure e selecione **Azure Active Directory**.
1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
1. Insira um **Nome** para a sua inscrição. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
1. Selecione **Registar**.
1. Em **Gestão**, **selecione Autenticação**  >  **Adicione uma plataforma**  >  **Android**.
1. Insira o nome do pacote do seu projeto. Se descarregou o código, este valor é `com.azuresamples.msalandroidapp` .
1. Na secção **de hash Signature** da página de **aplicações Configure o Android,** selecione **Gerar um Hash assinatura de desenvolvimento.** e copiar o comando KeyTool para usar para a sua plataforma.


     KeyTool.exe é instalado como parte do Kit de Desenvolvimento de Java (JDK). Também tem de instalar a ferramenta OpenSSL para executar o comando KeyTool. Consulte a documentação do [Android sobre a geração de uma chave](https://developer.android.com/studio/publish/app-signing#generate-key) para mais informações.

1. Introduza o **hash Signature** gerado pelo KeyTool.
1. Selecione **Configurar** e guarde a **Configuração MSAL** que aparece na página **de configuração** do Android para que possa introduzi-la quando configurar a sua aplicação mais tarde.  
1. Selecione **Concluído**.

### <a name="configure-your-application"></a>Configurar a aplicação

1. No painel de projeto do Android Studio, navegue para **app\src\main\res**.
1. **Clique** à direita e escolha **Novo**  >  **Diretório**. Insira `raw` como o novo nome do diretório e clique em **OK**.
1. Na **app**  >  **src**  >  **main**  >  **res**  >  **raw**, crie um novo ficheiro JSON chamado `auth_config_single_account.json` e cole a Configuração MSAL que guardou anteriormente.

    Abaixo do redirecionamento URI, pasta:
    ```json
      "account_mode" : "SINGLE",
    ```
    O seu ficheiro config deve assemelhar-se a este exemplo:
    ```json
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "broker_redirect_uri_registered" : true,
      "account_mode" : "SINGLE",
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

     Este tutorial apenas demonstra como configurar uma aplicação no modo Conta Única. Consulte a documentação para obter mais informações sobre [o modo de conta única vs. múltipla](./single-multi-account.md) e [configurar a sua app](./msal-configuration.md)

4. Na **app**  >  **src**  >  **main**  >  **AndroidManifest.xml**, adicione a `BrowserTabActivity` atividade abaixo ao corpo de aplicação. Esta entrada permite que a Microsoft volte a ligar para a sua aplicação depois de completar a autenticação:

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    Substitua o nome do pacote que registou no portal Azure pelo `android:host=` valor.
    Substitua o haxixe chave que registou no portal Azure pelo `android:path=` valor. O Hash assinatura **não** deve ser codificado url. Certifique-se de que existe uma liderança `/` no início do seu Signature Hash.
    
    O "Nome do Pacote" por que substituirá o `android:host` valor deve ser semelhante a: "com.azuresamples.msalandroidapp".
    O "Signature Hash" por quem substituirá o seu `android:path` valor deve ser semelhante a: "/1wIqXSqBj7w+h11ZifsnqwgyKrY=".
    
    Poderá também encontrar estes valores na lâmina de autenticação do registo da sua aplicação. Note que o seu reencaminhador URI será semelhante a: "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D". Enquanto o Hash assinatura é URL codificado no final deste valor, o Hash assinatura **não** deve ser codificado no seu `android:path` valor.

## <a name="use-msal"></a>Utilizar MSAL

### <a name="add-msal-to-your-project"></a>Adicione MSAL ao seu projeto

1. Na janela do projeto Android Studio, navegue para **app**  >  **src**  >  **build.gradle** e adicione o seguinte:

    ```gradle
    repositories{
        jcenter()
    }
    dependencies{
        implementation 'com.microsoft.identity.client:msal:2.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version")
    }
    ```
    [Mais sobre o Microsoft Graph SDK](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>Importações Exigidas

Adicione o seguinte ao topo da **app**  >  **src**  >  **main** >  **java**  >  **com.exemplo (yourapp)**  >  **MainActivity.java**

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>Instantiate PublicClientApplication
#### <a name="initialize-variables"></a>Inicializar Variáveis
```java
private final static String[] SCOPES = {"Files.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>onCreate
No interior da `MainActivity` classe, consulte o seguinte método onCreate() para instantaneaizar o MSAL utilizando o `SingleAccountPublicClientApplication` .

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount

```java
//When app comes to the foreground, load existing account to determine if user is signed in
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

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
}
```

### <a name="initializeui"></a>inicializeUI
Ouça os botões e os métodos de chamada ou faça login em conformidade.
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);

        //Sign in user
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });

        //Interactive
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> A assinatura com o MSAL remove todas as informações conhecidas sobre um utilizador da aplicação, mas o utilizador ainda terá uma sessão ativa no seu dispositivo. Se o utilizador tentar entrar novamente, poderá ver uI de inscrição, mas pode não precisar de reentrar nas suas credenciais porque a sessão do dispositivo ainda está ativa.

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
Chamada usada para pedidos interativos.

```java
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>obterAuthSilentCallback
Callback usado para pedidos silenciosos
```java
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>Chamar o Microsoft Graph API

O código a seguir demonstra como ligar para o GraphAPI utilizando o Gráfico SDK.

### <a name="callgraphapi"></a>callGraphAPI

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>Adicionar UI
### <a name="activity"></a>Atividade
Se quiser modelar a sua UI fora deste tutorial, os seguintes métodos fornecem um guia para atualizar texto e ouvir botões.

#### <a name="updateui"></a>updateUI
Ative/desative os botões com base no estado de inscrição e no texto definido.
```java
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>displayError
```java
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
Método para atualizar texto em UI para refletir a assinatura.

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Layout

Ficheiro de amostra `activity_main.xml` para exibir botões e caixas de texto.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>Testar a aplicação

### <a name="run-locally"></a>Executar localmente

Construa e implemente a aplicação num dispositivo de teste ou emulador. Você deve ser capaz de iniciar seduca e obter fichas para Azure AD ou contas pessoais da Microsoft.

Depois de iniciar sposição, a aplicação apresentará os dados devolvidos a partir do ponto final do Microsoft `/me` Graph.
PR 4
### <a name="consent"></a>Consent (Consentimento)

A primeira vez que qualquer utilizador entrar na sua aplicação, será solicitado pela identidade da Microsoft para consentir com as permissões solicitadas. Alguns inquilinos da AD Azure têm o consentimento do utilizador desativado, o que requer que os administradores consintam em nome de todos os utilizadores. Para apoiar este cenário, você precisará criar o seu próprio inquilino ou receber o consentimento administrativo.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o objeto da aplicação que criou no Passo de Inscrição da [sua aplicação.](#register-your-application)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a construção de aplicações móveis que chamem APIs web protegidas na nossa série de cenários multi-partes.

> [!div class="nextstepaction"]
> [Cenário: Aplicação móvel que chama APIs web](scenario-mobile-overview.md)
