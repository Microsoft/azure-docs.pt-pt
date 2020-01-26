---
title: Conectar usuários de entrada/saída & Microsoft Graph (Android)-plataforma de identidade da Microsoft | Azure
description: Obtenha um sinal de acesso e ligue para o Microsoft Graph ou APIs que requerem fichas de acesso da plataforma de identidade da Microsoft (Android)
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: d851e23e8f6915c7d52565f18eff4a73bd96c9c0
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758840"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-application"></a>Tutorial: conectar usuários e chamar o Microsoft Graph de um aplicativo Android 

>[!NOTE]
>Este tutorial demonstra exemplos simplificados de como trabalhar com o MSAL para Android. Para simplificar, este tutorial usa apenas o modo de conta única. Você também pode exibir o repositório e clonar [o aplicativo de exemplo pré-configurado](https://github.com/Azure-Samples/ms-identity-android-java/) para explorar cenários mais complexos. Veja o guia de [início rápido](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-android) para saber mais sobre o aplicativo de exemplo, a configuração e o registro. 

Neste tutorial, você aprenderá a integrar seu aplicativo Android com a plataforma de identidade da Microsoft usando a biblioteca de autenticação da Microsoft para Android. Você aprenderá como entrar e sair de um usuário, obter um token de acesso para chamar a API de Microsoft Graph e fazer uma solicitação para o API do Graph. 

> [!div class="checklist"]
> * Integre seu aplicativo Android com a plataforma de identidade da Microsoft 
> * Conectar um usuário 
> * Obtenha um sinal de acesso para ligar para a Microsoft Graph API 
> * Ligue para a Microsoft Graph API 
> * Desconectar um usuário 

Quando tiver concluído este tutorial, a sua aplicação aceitará inscrições de contas pessoais da Microsoft (incluindo outlook.com, live.com e outras) bem como contas de trabalho ou escola de qualquer empresa ou organização que utilize o Diretório Ativo Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="how-this-tutorial-works"></a>Como funciona este tutorial

![Mostra como o aplicativo de exemplo gerado por este tutorial funciona](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

A aplicação neste tutorial irá inscrever os utilizadores e obter dados em seu nome. Estes dados serão acedidos através de uma API protegida (Microsoft Graph API) que requer autorização e está protegida pela plataforma de identidade da Microsoft.

Mais especificamente:

* A sua aplicação irá iniciar sessão de atividade através de um navegador ou do Microsoft Authenticator e do Portal da Empresa Intune.
* O utilizador final aceitará as permissões que a sua aplicação solicitou.
* A sua aplicação será emitida um sinal de acesso para a API do Microsoft Graph.
* O token de acesso será incluído no pedido http para a Web API.
* Processe a resposta do Microsoft Graph.

Esta amostra utiliza a biblioteca de autenticação da Microsoft para Android (MSAL) para implementar a Autenticação: [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 A MSAL renovará automaticamente as fichas, entregará um único sinal (SSO) entre outras aplicações no dispositivo e gerirá a conta(s).

### <a name="prerequisites"></a>Pré-requisitos

* Este tutorial requer Android Studio versão 3.5 +

## <a name="create-a-project"></a>Criar um projeto
Se você ainda não tiver um aplicativo Android, siga estas etapas para configurar um novo projeto. 

1. Abra o Android Studio e selecione **Iniciar um novo projeto Android Studio.**
2. Selecione **Atividade Básica** e selecione **Next**.
3. Dê um nome à aplicação.
4. Guarde o nome do pacote. Entrará mais tarde no portal Azure.
5. Mude a linguagem de **Kotlin** para **Java.**
6. Detete o **nível mínimo de API** para **API 19** ou superior e clique em **Terminar**.
7. Na visão do projeto, escolha **Project** no dropdown para exibir ficheiros de projetos de origem e não-fonte, **abra app/build.gradle** e coloque `targetSdkVersion` para `28`.

## <a name="integrate-with-microsoft-authentication-library"></a>Integrar com a biblioteca de autenticação da Microsoft 

### <a name="register-your-application"></a>Registar a sua aplicação

1. Aceda ao [Portal do Azure](https://aka.ms/MobileAppReg).
2. Abra a lâmina de [registos](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) da App e clique **em +Nova inscrição.**
3. Insira um **nome** para seu aplicativo e, em seguida, **sem** definir um URI de redirecionamento, clique em **registrar**.
4. Na secção **Gerir** do painel que aparece, selecione **Autenticação** >  **+ Adicione uma plataforma** > **Android**. (Pode ter de selecionar "Mudar para a nova experiência" perto da parte superior da lâmina para ver esta secção)
5. Insira o Nome pacote do seu projeto. Se descarregou o código, este valor é `com.azuresamples.msalandroidapp`.
6. Na secção signature **hash** da página de **aplicações Configure a sua aplicação Android,** clique em **Gerar um desenvolvimento Signature Hash.** e copiar o comando KeyTool para usar para a sua plataforma.

   > [!Note]
   > KeyTool.exe é instalado como parte do Kit de Desenvolvimento Java (JDK). Também deve instalar a ferramenta OpenSSL para executar o comando KeyTool. Consulte a [documentação do Android sobre a geração de uma chave](https://developer.android.com/studio/publish/app-signing#generate-key) para mais informações. 

7. Introduza o **hash Signature** gerado pelo KeyTool.
8. Clique em `Configure` e guarde a **Configuração MSAL** que aparece na página de **configuração do Android** para que possa inseri-la quando configurar a sua aplicação mais tarde.  Clique em **Concluído**.

### <a name="configure-your-application"></a>Configurar a aplicação 

1. No painel de projetos do Android Studio, navegue para **app\src\main\res**.
2. Clique no **res** do clique direito e escolha **New** > **Diretório**. Introduza `raw` como o novo nome do diretório e clique **OK**.
3. Na **aplicação** > **sRC** > **principal** ** > res** > **crus**, crie um novo ficheiro JSON chamado `auth_configbn_single_account.json` e colhe a Configuração MSAL que guardou anteriormente. 

    Abaixo do URI de redirecionamento, Cole: 
    ```json
      "account_mode" : "SINGLE",
    ```
    O arquivo de configuração deve ser semelhante a este exemplo: 
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
   >Este tutorial demonstra apenas como configurar um aplicativo no modo de conta única. Exiba a documentação para obter mais informações sobre o [modo de conta única versus várias](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account) e [configurar seu aplicativo](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration)
   
4. Na **aplicação** > **sRC** > **principal** > **AndroidManifest.xml,** adicione a atividade `BrowserTabActivity` abaixo ao organismo de aplicação. Esta entrada permite que a Microsoft volte a ligar para a sua aplicação depois de concluída a autenticação:

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

    Substitua o nome do pacote registado no portal Azure pelo valor `android:host=`.
    Substitua o hash chave que registou no portal Azure pelo valor `android:path=`. O hash de assinatura **não** deve ser codificado em URL. Verifique se há um `/` inicial no início do hash de assinatura. 
    >[!NOTE]
    >O "nome do pacote" substituirá o valor de `android:host` por deve ser semelhante a: "com. azuresamples. msalandroidapp" o "hash de assinatura" que substituirá seu valor de `android:path` deve ser semelhante a: "/1wIqXSqBj7w + h11ZifsnqwgyKrY =" você também poderá encontrar esses valores na folha de autenticação do registro do aplicativo. Note que o seu URI redirecionado será semelhante ao: "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D". Enquanto o hash de assinatura é codificado na URL no final desse valor, o hash de assinatura **não** deve ser codificado de URL no valor `android:path`. 

## <a name="use-msal"></a>Use MSAL 

### <a name="add-msal-to-your-project"></a>Adicione MSAL ao seu projeto

1. Na janela Android Studio projeto, navegue até o **aplicativo** > **src** > **Build. gradle** e adicione o seguinte: 

    ```gradle
    repositories{
        jcenter()
    }  
    dependencies{
        implementation 'com.microsoft.identity.client:msal:1.2.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version") 
    }
    ```
    [Mais sobre o SDK do Microsoft Graph](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>Importações obrigatórias 

Adicione o seguinte à parte superior do **aplicativo** > **src** > **principal**> **Java** > **com. example (yourapp)**  > **MainActivity. java** 

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

## <a name="instantiate-publicclientapplication"></a>Criar instância de PublicClientApplication
#### <a name="initialize-variables"></a>Inicializar variáveis 
```java
private final static String[] SCOPES = {"File.Read"};
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
Dentro da classe `MainActivity`, consulte o seguinte método onCreate () para instanciar MSAL usando o `SingleAccountPublicClientApplication`.

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

### <a name="loadaccount"></a>LoadAccount 

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

### <a name="initializeui"></a>initializeUI
Ouça os botões e chame os métodos ou erros de log de acordo. 
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
> A assinatura com a MSAL remove todas as informações conhecidas sobre um utilizador da aplicação, mas o utilizador ainda terá uma sessão ativa no seu dispositivo. Se o utilizador tentar voltar a iniciar o contrato, poderá ver uI de entrada, mas pode não precisar de reintroduzir as suas credenciais porque a sessão do dispositivo ainda está ativa. 

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
Retorno de chamada usado para solicitações interativas.

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

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
Retorno de chamada usado para solicitações silenciosas 
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

O código a seguir demonstra como chamar o GraphAPI usando o Graph SDK. 

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

## <a name="add-ui"></a>Adicionar interface do usuário
### <a name="activity"></a>Atividade 
Se você quiser modelar a interface do usuário deste tutorial, os métodos a seguir fornecerão um guia para atualizar o texto e ouvir os botões.

#### <a name="updateui"></a>updateUI
Habilitar/Desabilitar botões com base no estado de entrada e definir texto.  
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
Método para atualizar o texto na interface do usuário para refletir a saída. 

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Esquema 

Experimente `activity_main.xml` ficheiro para visualizar botões e caixas de texto. 

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

Construa e implemente a aplicação para um dispositivo de teste ou emulador. Deverá poder iniciar sessão e obter fichas para contas Azure AD ou pessoais da Microsoft.

Depois de iniciar sessão, a aplicação apresentará os dados devolvidos do Microsoft Graph `/me` ponto final.

### <a name="consent"></a>Consentimento

A primeira vez que qualquer utilizador entrar na sua aplicação, será solicitado pela identidade da Microsoft para consentir com as permissões solicitadas. Alguns locatários do Azure AD desabilitaram o consentimento do usuário, o que exige que os administradores consentissem em nome de todos os usuários. Para dar suporte a esse cenário, você precisará criar seu próprio locatário ou receber o consentimento do administrador. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o objeto de aplicação que criou no registo da sua etapa de [aplicação.](#register-your-application)

## <a name="get-help"></a>Obter ajuda

Visite [ajuda e suporte](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) se tiver problemas com este tutorial ou com a plataforma de identidade da Microsoft.

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que você imagina ao concluir uma pesquisa curta de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
