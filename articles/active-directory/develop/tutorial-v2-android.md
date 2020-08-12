---
title: Os utilizadores de iniciar snus in/out & ligar para o Microsoft Graph (Android) - Plataforma de identidade da Microsoft ; Rio Azure
description: Obtenha um token de acesso e ligue para o Microsoft Graph ou APIs que requerem tokens de acesso a partir da plataforma de identidade da Microsoft (Android)
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
ms.openlocfilehash: b4de8a5e96466ea324475030df1f00eb6bb5cf1a
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118293"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-application"></a>Tutorial: Inscreva-se nos utilizadores e ligue para o Microsoft Graph a partir de uma aplicação Android 

>[!NOTE]
>Este tutorial demonstra exemplos simplificados de como trabalhar com o MSAL para Android. Para simplificar, este tutorial utiliza apenas o Modo conta única. Também pode ver o repo e clonar [a aplicação de amostra pré-configurada](https://github.com/Azure-Samples/ms-identity-android-java/) para explorar cenários mais complexos. Veja o [Quickstart](./quickstart-v2-android.md) para mais informações sobre a aplicação, configuração e registo da amostra. 

Neste tutorial, irá aprender a integrar a sua aplicação para Android com a plataforma de identidade da Microsoft utilizando a Microsoft Authentication Library para Android. Você vai aprender a iniciar sômsemuada e assinar um utilizador, obter um token de acesso para ligar para a Microsoft Graph API, e fazer um pedido para a API do gráfico. 

> [!div class="checklist"]
> * Integre a sua Aplicação Android com a Plataforma de Identidade da Microsoft 
> * Inscreva-se num utilizador 
> * Obtenha um token de acesso para ligar para a Microsoft Graph API 
> * Ligue para a Microsoft Graph API 
> * Assine um utilizador 

Quando tiver concluído este tutorial, a sua aplicação aceitará inscrições de contas pessoais da Microsoft (incluindo outlook.com, live.com e outras) bem como contas de trabalho ou escola de qualquer empresa ou organização que utilize o Azure Ative Directory.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="how-this-tutorial-works"></a>Como funciona este tutorial

![Mostra como funciona a app de amostras gerada por este tutorial](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

A aplicação neste tutorial irá iniciar snuários nos utilizadores e obter dados em seu nome. Estes dados serão acedidos através de uma API protegida (Microsoft Graph API) que requer autorização e está protegida pela plataforma de identidade da Microsoft.

Mais especificamente:

* A sua aplicação irá iniciar súbs no utilizador através de um browser ou do Microsoft Authenticator e do Portal da Empresa Intune.
* O utilizador final aceitará as permissões que a sua aplicação solicitou.
* A sua aplicação será emitida um token de acesso para a API do Gráfico da Microsoft.
* O token de acesso será incluído no pedido HTTP para a API web.
* Processe a resposta do Microsoft Graph.

Esta amostra utiliza a biblioteca microsoft authentication para Android (MSAL) para implementar autenticação: [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 A MSAL renovará automaticamente os tokens, entregará um único sign-on (SSO) entre outras aplicações no dispositivo e gerirá a Conta(s).

### <a name="prerequisites"></a>Pré-requisitos

* Este tutorial requer a versão 3.5+ do Android Studio

## <a name="create-a-project"></a>Criar um Projeto
Se ainda não tem uma aplicação Android, siga estes passos para criar um novo projeto. 

1. Abra o Android Studio e selecione **Iniciar um novo projeto Android Studio**.
2. Selecione **A Atividade Básica** e selecione **Seguinte**.
3. Dê um nome à aplicação.
4. Guarde o nome do pacote. Vai inseri-lo mais tarde no portal Azure.
5. Mude a língua de **Kotlin** para **Java.**
6. Desa estatua o **nível mínimo de API** para **API 19** ou superior, e clique em **Terminar**.
7. Na visão do projeto, **escolha** Project no dropdown para exibir ficheiros de projetos de origem e não fonte, abrir **app/build.gradle** e definir `targetSdkVersion` para `28` .

## <a name="integrate-with-microsoft-authentication-library"></a>Integrar-se com a Microsoft Authentication Library 

### <a name="register-your-application"></a>Registar a aplicação

1. Aceda ao [portal do Azure](https://aka.ms/MobileAppReg).
2. Abra a lâmina de [registos](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) da App e clique **em +Novo registo.**
3. Introduza um **Nome** para a sua aplicação e, em seguida, **sem** definir um URI de redirecionamento, clique em **Registar**.
4. Na secção **Gerir** o painel que aparece, selecione **Autenticação**  >  **+ Adicione uma plataforma**  >  **Android**. (Pode ter de selecionar "Mudar para a nova experiência" perto da parte superior da lâmina para ver esta secção)
5. Insira o nome do pacote do seu projeto. Se descarregou o código, este valor é `com.azuresamples.msalandroidapp` .
6. Na secção **de hash Signature** da página de **aplicações Configure o Android,** clique em **Gerar um Hash signature de desenvolvimento.** e copiar o comando KeyTool para usar para a sua plataforma.

   > [!Note]
   > KeyTool.exe é instalado como parte do Kit de Desenvolvimento de Java (JDK). Também tem de instalar a ferramenta OpenSSL para executar o comando KeyTool. Consulte a documentação do [Android sobre a geração de uma chave](https://developer.android.com/studio/publish/app-signing#generate-key) para mais informações. 

7. Introduza o **hash Signature** gerado pelo KeyTool.
8. Clique `Configure` e guarde a **Configuração MSAL** que aparece na página **de configuração** do Android para que possa introduzi-la quando configurar a sua aplicação mais tarde.  Clique em **Concluído**.

### <a name="configure-your-application"></a>Configurar a aplicação 

1. No painel de projeto do Android Studio, navegue para **app\src\main\res**.
2. **Clique** à direita e escolha **Novo**  >  **Diretório**. Insira `raw` como o novo nome do diretório e clique em **OK**.
3. Na **app**  >  **src**  >  **main**  >  **res**  >  **raw**, crie um novo ficheiro JSON chamado `auth_config_single_account.json` e cole a Configuração MSAL que guardou anteriormente. 

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
    
   >[!NOTE]
   >Este tutorial apenas demonstra como configurar uma aplicação no modo Conta Única. Consulte a documentação para obter mais informações sobre [o modo de conta única vs. múltipla](./single-multi-account.md) e [configurar a sua app](./msal-configuration.md)
   
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
    >[!NOTE]
    >O "Nome do Pacote" por que substituirá o `android:host` valor deve ser semelhante a: "com.azuresamples.msalandroidapp" O "Hash de assinatura" por que irá substituir o seu valor deve ser semelhante `android:path` a: "/1wIqXSqBj7w+h11ZifsnqwgyKrY=" Também poderá encontrar estes valores na lâmina de autenticação do seu registo de aplicações. Note que o seu URI redirecionado será semelhante a: "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D". Enquanto o Hash assinatura é URL codificado no final deste valor, o Hash assinatura **não** deve ser codificado no seu `android:path` valor. 

## <a name="use-msal"></a>Utilizar MSAL 

### <a name="add-msal-to-your-project"></a>Adicione MSAL ao seu projeto

1. Na janela do projeto Android Studio, navegue para **app**  >  **src**  >  **build.gradle** e adicione o seguinte: 

    ```gradle
    repositories{
        jcenter()
    }  
    dependencies{
        implementation 'com.microsoft.identity.client:msal:1.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version") 
    }
    ```
    [Mais sobre o Microsoft Graph SDK](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>Importações Exigidas 

Adicione o seguinte ao topo da **app**  >  **src**  >  **main** >  **java**  >  **com.example (yourapp)**  >  **MainActivity.java** 

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

### <a name="consent"></a>Consent (Consentimento)

A primeira vez que qualquer utilizador entrar na sua aplicação, será solicitado pela identidade da Microsoft para consentir com as permissões solicitadas. Alguns inquilinos da AD Azure têm o consentimento do utilizador desativado, o que requer que os administradores consintam em nome de todos os utilizadores. Para apoiar este cenário, você precisará criar o seu próprio inquilino ou receber o consentimento administrativo. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o objeto da aplicação que criou no Passo de Inscrição da [sua aplicação.](#register-your-application)

## <a name="get-help"></a>Obter ajuda

Visite [a Ajuda e suporte](./developer-support-help-options.md) se tiver problemas com este tutorial ou com a plataforma de identidade da Microsoft.