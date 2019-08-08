---
title: Introdução ao Android-plataforma de identidade da Microsoft | Azure
description: Como um aplicativo Android pode obter um token de acesso e chamar Microsoft Graph API ou APIs que exigem tokens de acesso da plataforma Microsoft Identity.
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
ms.date: 07/09/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: a53a0d5ea8405c116d0286d3b67b1640f98ed96d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852448"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Conectar usuários e chamar o Microsoft Graph de um aplicativo Android

Neste tutorial, você aprenderá a integrar um aplicativo Android com a plataforma de identidade da Microsoft. Seu aplicativo entrará em um usuário, obterá um token de acesso para chamar a API de Microsoft Graph e fará uma solicitação para a API de Microsoft Graph.  

Quando você tiver concluído o guia, seu aplicativo aceitará entradas de contas pessoais da Microsoft (incluindo outlook.com, live.com e outros) e contas corporativas ou de estudante de qualquer empresa ou organização que usa Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Como este tutorial funciona

![Mostra como o aplicativo de exemplo gerado por este tutorial funciona](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

O aplicativo neste tutorial conectará os usuários e obterá dados em seu nome.  Esses dados serão acessados por meio de uma API protegida (API de Microsoft Graph) que requer autorização e é protegido pela plataforma de identidade da Microsoft.

Mais especificamente:

* Seu aplicativo conectará o usuário por meio de um navegador ou do Microsoft Authenticator e Portal da Empresa do Intune.
* O usuário final aceitará as permissões solicitadas pelo seu aplicativo.
* Seu aplicativo receberá um token de acesso para a API de Microsoft Graph.
* O token de acesso será incluído na solicitação HTTP para a API da Web.
* Processar a resposta de Microsoft Graph.

Este exemplo usa a biblioteca de autenticação da Microsoft para Android (MSAL) para implementar a autenticação: [com. Microsoft. Identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 O MSAL renovará automaticamente os tokens, entregará o SSO (logon único) entre outros aplicativos no dispositivo e gerenciará as contas.

## <a name="prerequisites"></a>Pré-requisitos

* Este tutorial requer Android Studio versão 16 ou posterior (19 + é recomendado).

## <a name="create-a-project"></a>Criar um projeto

Este tutorial criará um novo projeto. Se você quiser baixar o tutorial concluído, [Baixe o código](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip).

1. Abra Android Studio e selecione **Iniciar um novo projeto de Android Studio**.
2. Selecione **atividade básica** e selecione **Avançar**.
3. Dê um nome à aplicação.
4. Salve o nome do pacote. Você vai inseri-lo mais tarde no portal do Azure.
5. Defina o **nível mínimo da API** como **API 19** ou superior e clique em **concluir**.
6. Na exibição de projeto, escolha **projeto** na lista suspensa para exibir arquivos de projeto de origem e não de origem, abra **app/Build. gradle** `targetSdkVersion` e `27`defina como.

## <a name="register-your-application"></a>Registar a sua aplicação

1. Aceda ao [Portal do Azure](https://aka.ms/MobileAppReg).
2. Abra a [folha registros de aplicativo](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) e clique em **+ novo registro**.
3. Insira um **nome** para seu aplicativo e, em seguida, sem definir um URI de redirecionamento, clique em **registrar**.
4. Na seção **gerenciar** do painel que aparece, selecione **autenticação** >  **+ Adicionar uma plataforma** > **Android**.
5. Insira o nome do pacote do seu projeto. Se você baixou o código, esse valor `com.azuresamples.msalandroidapp`será.
6. Na seção de **hash de assinatura** da página **configurar seu aplicativo Android** , clique em gerando **um hash de assinatura de desenvolvimento.** e copie o comando keytool a ser usado para sua plataforma.

   > [!Note]
   > Keytool. exe é instalado como parte do Java Development Kit (JDK). Você também deve instalar a ferramenta OpenSSL para executar o comando keytool.

7. Insira o **hash de assinatura** gerado pela keytool.
8. Clique `Configure` e salve a **configuração do MSAL** que aparece na página **configuração do Android** para que você possa inseri-la ao configurar o aplicativo mais tarde.  Clique em **Concluído**.

## <a name="build-your-app"></a>Criar a sua aplicação

### <a name="add-your-app-registration"></a>Adicionar o registro do aplicativo

1. No painel de projeto do Android Studio, navegue até **app\src\main\res**.
2. Clique com o botão direito do mouse em **res** e escolha **novo** > **diretório**. Insira `raw` como o novo nome de diretório e clique em **OK**.
3. No **aplicativo** >  `auth_config.json` **src** **res** **RAW**, crie um novo arquivo JSON chamado e cole a configuração MSAL que você salvou anteriormente. >  >  Consulte [configuração do MSAL para obter mais informações](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app).
4. Em **app** > **src** `BrowserTabActivity` Main AndroidManifest. xml, adicione a atividade abaixo. >  >  Essa entrada permite que a Microsoft chame de volta para seu aplicativo depois de concluir a autenticação:

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

    Substitua o nome do pacote que você registrou no portal do Azure `android:host=` para o valor.
    Substitua o hash de chave que você registrou no portal do Azure `android:path=` para o valor. O hash de assinatura não deve ser codificado em URL.

5. Dentro do **AndroidManifest. xml**, logo acima da `<application>` marca, adicione as seguintes permissões:

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

### <a name="create-the-apps-ui"></a>Criar a interface do usuário do aplicativo

1. Na janela Android Studio projeto, navegue até **aplicativo** > **src** > **Main** > **res** > **layout** e abra **activity_main. xml** e abra o **texto** exibição.
2. Altere o layout da atividade, por exemplo `<androidx.coordinatorlayout.widget.CoordinatorLayout` : `<androidx.coordinatorlayout.widget.LinearLayout`para.
3. Adicione a `android:orientation="vertical"` propriedade `LinearLayout` ao nó.
4. Cole o código a seguir no `LinearLayout` nó, substituindo o conteúdo atual:

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```

### <a name="add-msal-to-your-project"></a>Adicionar MSAL ao seu projeto

1. Na janela Android Studio projeto, navegue até **app** > **src** > **Build. gradle**.
2. Em **dependências**, Cole o seguinte:

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>Usar MSAL

Agora, faça alterações `MainActivity.java` dentro para adicionar e usar MSAL em seu aplicativo.
Na janela Android Studio projeto, navegue até **aplicativo** > **src** > **principal** > **Java** > **com. example. MSAL**e abra `MainActivity.java`.

#### <a name="required-imports"></a>Importações necessárias

Adicione as seguintes importações próximo à parte superior `MainActivity.java`de:

```java
import android.app.Activity;
import android.content.Intent;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import com.android.volley.*;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.Volley;
import org.json.JSONObject;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

#### <a name="instantiate-msal"></a>Criar instância de MSAL

Dentro da `MainActivity` classe, precisaremos criar uma instância de MSAL junto com algumas configurações sobre o que o aplicativo fará, incluindo os escopos e a API Web que desejamos acessar.

Copie as seguintes variáveis dentro da `MainActivity` classe:

```java
final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

/* UI & Debugging Variables */
private static final String TAG = MainActivity.class.getSimpleName();
Button callGraphButton;
Button signOutButton;

/* Azure AD Variables */
private PublicClientApplication sampleApp;
private IAuthenticationResult authResult;
```

Substitua o conteúdo de `onCreate()` pelo código a seguir para criar uma instância de MSAL:

```java
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

callGraphButton = (Button) findViewById(R.id.callGraph);
signOutButton = (Button) findViewById(R.id.clearCache);

callGraphButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onCallGraphClicked();
    }
});

signOutButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onSignOutClicked();
    }
});

/* Configure your sample app and save state for this activity */
sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);

/* Attempt to get a user and acquireTokenSilent */
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {
        if (!accounts.isEmpty()) {
            /* This sample doesn't support multi-account scenarios, use the first account */
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts */
        }
    }
});
```

O código acima tenta conectar os usuários silenciosamente quando eles abrem seu aplicativo por `getAccounts()` meio de e, se `acquireTokenSilentAsync()`for bem-sucedido,.  Nas próximas seções, implementaremos o manipulador de retorno de chamada para o caso, não há contas conectadas.

#### <a name="use-msal-to-get-tokens"></a>Usar MSAL para obter tokens

Agora, podemos implementar a lógica de processamento da interface do usuário do aplicativo e obter os tokens interativamente por meio de MSAL.

MSAL expõe dois métodos principais para obter tokens `acquireTokenSilentAsync()` : `acquireToken()`e.  

`acquireTokenSilentAsync()`entra em um usuário e obtém tokens sem qualquer interação do usuário se uma conta estiver presente. Se tiver êxito, o MSAL entregará os tokens para seu aplicativo, se ele falhar, gerará `MsalUiRequiredException`um.  Se essa exceção for gerada ou se você quiser que o usuário tenha uma experiência de entrada interativa (credenciais, MFA ou outras políticas de acesso condicional podem ou não ser necessárias), use `acquireToken()`.  

`acquireToken()`exibe a interface de usuário ao tentar entrar no usuário e obter tokens. No entanto, ele pode usar cookies de sessão no navegador, ou uma conta no Microsoft Authenticator, para fornecer a experiência de SSO interativo.

Crie os três métodos de interface do usuário `MainActivity` a seguir dentro da classe:

```java
/* Set the UI for successful token acquisition data */
private void updateSuccessUI() {
    callGraphButton.setVisibility(View.INVISIBLE);
    signOutButton.setVisibility(View.VISIBLE);
    findViewById(R.id.welcome).setVisibility(View.VISIBLE);
    ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
            authResult.getAccount().getUsername());
    findViewById(R.id.graphData).setVisibility(View.VISIBLE);
}

/* Set the UI for signed out account */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");

    Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
            .show();
}

/* Use MSAL to acquireToken for the end-user
 * Callback will call Graph api w/ access token & update UI
 */
private void onCallGraphClicked() {
    sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
}
```

Adicione os seguintes métodos para obter a atividade atual e o processo silencioso & retornos de chamada interativos:

```java
public Activity getActivity() {
    return this;
}

/* Callback used in for silent acquireToken calls.
 * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
 * else errors that we need to do an interactive request.
 */
private AuthenticationCallback getAuthSilentCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");

            /* Store the authResult */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}

/* Callback used for interactive request.  If succeeds we use the access
 * token to call the Microsoft Graph. Does not check cache
 */
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");
            Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

            /* Store the auth result */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

#### <a name="use-msal-for-sign-out"></a>Usar MSAL para sair

Em seguida, adicione suporte para sair.

> [!Important]
> Sair com o MSAL remove todas as informações conhecidas sobre um usuário do aplicativo, mas o usuário ainda terá uma sessão ativa em seu dispositivo. Se o usuário tentar entrar novamente, poderá ver a interface do usuário de entrada, mas talvez não precise reinserir suas credenciais porque a sessão do dispositivo ainda está ativa.

Para adicionar a funcionalidade de saída, adicione o seguinte método dentro da `MainActivity` classe. Esse método percorre todas as contas e as remove:

```java
/* Clears an account's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 * User will get interactive SSO if trying to sign back-in.
 */
private void onSignOutClicked() {
    /* Attempt to get a user and acquireTokenSilent
     * If this fails we do an interactive request
     */
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (accounts.isEmpty()) {
                /* No accounts to remove */

            } else {
                for (final IAccount account : accounts) {
                    sampleApp.removeAccount(
                            account,
                            new PublicClientApplication.AccountsRemovedCallback() {
                        @Override
                        public void onAccountsRemoved(Boolean isSuccess) {
                            if (isSuccess) {
                                /* successfully removed account */
                            } else {
                                /* failed to remove account */
                            }
                        }
                    });
                }
            }

            updateSignedOutUI();
        }
    });
}
```

#### <a name="call-the-microsoft-graph-api"></a>Chamar a API de Microsoft Graph

Depois de recebermos um token, podemos fazer uma solicitação para a [API de Microsoft Graph](https://graph.microsoft.com) o token de acesso estará dentro do `AuthenticationResult` método do `onSuccess()` retorno de chamada de autenticação. Para construir uma solicitação autorizada, seu aplicativo precisará adicionar o token de acesso ao cabeçalho HTTP:

| chave de cabeçalho    | value                 |
| ------------- | --------------------- |
| Autorização | \<Acesso de portador-> de token |

Adicione os dois métodos a seguir dentro `MainActivity` da classe para chamar o grafo e atualizar a interface do usuário:

```java
/* Use Volley to make an HTTP request to the /me endpoint from MS Graph using an access token */
private void callGraphAPI() {
    Log.d(TAG, "Starting volley request to graph");

    /* Make sure we have a token to send to graph */
    if (authResult.getAccessToken() == null) {return;}

    RequestQueue queue = Volley.newRequestQueue(this);
    JSONObject parameters = new JSONObject();

    try {
        parameters.put("key", "value");
    } catch (Exception e) {
        Log.d(TAG, "Failed to put parameters: " + e.toString());
    }
    JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
            parameters,new Response.Listener<JSONObject>() {
        @Override
        public void onResponse(JSONObject response) {
            /* Successfully called graph, process data and send to UI */
            Log.d(TAG, "Response: " + response.toString());

            updateGraphUI(response);
        }
    }, new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            Log.d(TAG, "Error: " + error.toString());
        }
    }) {
        @Override
        public Map<String, String> getHeaders() {
            Map<String, String> headers = new HashMap<>();
            headers.put("Authorization", "Bearer " + authResult.getAccessToken());
            return headers;
        }
    };

    Log.d(TAG, "Adding HTTP GET to Queue, Request: " + request.toString());

    request.setRetryPolicy(new DefaultRetryPolicy(
            3000,
            DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
            DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
    queue.add(request);
}

/* Sets the graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```

#### <a name="multi-account-applications"></a>Aplicativos de várias contas

Esse aplicativo é criado para um cenário de conta única. O MSAL também dá suporte a cenários de várias contas, mas requer algum trabalho adicional dos aplicativos. Você precisará criar a IU para ajudar o usuário a selecionar qual conta deseja usar para cada ação que requer tokens. Como alternativa, seu aplicativo pode implementar uma heurística para selecionar qual conta usar por meio do `getAccounts()` método.

## <a name="test-your-app"></a>Testar a aplicação

### <a name="run-locally"></a>Executar localmente

Crie e implante o aplicativo em um dispositivo de teste ou emulador. Você deve ser capaz de entrar e obter tokens para contas pessoais ou da Microsoft do Azure AD.

Depois de entrar, o aplicativo exibirá os dados retornados do ponto de extremidade `/me` Microsoft Graph.

### <a name="consent"></a>Consentimento

Na primeira vez que qualquer usuário entrar em seu aplicativo, ele será solicitado pela identidade da Microsoft para consentir as permissões solicitadas.  Embora a maioria dos usuários seja capaz de consentir, alguns locatários do Azure AD desabilitaram o consentimento do usuário, o que exige que os administradores consentissem em nome de todos os usuários. Para dar suporte a esse cenário, registre os escopos do seu aplicativo no portal do Azure.

## <a name="get-help"></a>Obter ajuda

Visite [ajuda e suporte](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) se você tiver problemas com este tutorial ou com a plataforma de identidade da Microsoft.

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que você imagina ao concluir uma pesquisa curta de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)