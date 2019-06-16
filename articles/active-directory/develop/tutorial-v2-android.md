---
title: Introdução ao Android - a plataforma de identidade da Microsoft | Azure
description: Como uma aplicação Android pode obter um token de acesso e chamar a API do Microsoft Graph ou as APIs que precisam de tokens de acesso da plataforma de identidade da Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79ffb31dd41e600344d341d17e07ad13d5fcd7f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111085"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Iniciar sessão dos utilizadores e chamar o Microsoft Graph a partir de uma aplicação Android

Neste tutorial, irá aprender como integrar uma aplicação Android na plataforma de identidades da Microsoft. Especificamente, a aplicação irá iniciar sessão de um utilizador, obter um token de acesso para chamar a API do Microsoft Graph e fazer um pedido para o Microsoft Graph API.  

Quando concluir o guia, a aplicação irá aceitar inícios de sessão de contas pessoais da Microsoft (incluindo o outlook.com, live.com e outros) e profissional ou escolar contas a partir de qualquer empresa ou organização que utiliza o Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Como funciona este tutorial

![Mostra como funciona a aplicação de exemplo gerada por este tutorial](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

A aplicação neste exemplo irá iniciar sessão dos utilizadores e obter dados em seu nome.  Estes dados serão acedidos através de uma API (Microsoft Graph API neste caso) protegida que requer autorização.

Mais especificamente:

* A aplicação irá iniciar sessão no usuário, seja por meio de um navegador ou o Microsoft Authenticator e Portal da empresa do Intune.
* O utilizador final irá aceitar as permissões de que seu aplicativo solicitou. 
* A aplicação será emitida um token de acesso para a Microsoft Graph API.
* O token de acesso será incluído na solicitação HTTP para a API web.
* Processe a resposta do Microsoft Graph.

Este exemplo utiliza a biblioteca Microsoft Authentication para Android (MSAL) para implementar a autenticação MSAL automaticamente irá renovar os tokens, fornecer SSO entre outras aplicações no dispositivo e gerir a conta (s).

## <a name="prerequisites"></a>Pré-requisitos

* Esta configuração assistida utiliza o Android Studio.
* É necessário o Android 16 ou posterior (19 + é recomendado).

## <a name="library"></a>Biblioteca

Este guia utiliza a biblioteca de autenticação seguintes:

|Biblioteca|Descrição|
|---|---|
|[com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)|Biblioteca de Autenticação da Microsoft (MSAL)|

## <a name="set-up-your-project"></a>Configurar seu projeto

Neste tutorial, irá criar um novo projeto. Se deseja baixar o tutorial completo em vez disso, [baixar o código](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip).

### <a name="create-a-new-project"></a>Criar um novo projeto

1. Abra o Android Studio e selecione **inicie um novo projeto do Android Studio**.
    - Se o Android Studio estiver aberto, selecione **arquivo** > **New** > **novo projeto**.
2. Deixe **atividade vazia** como é, selecione **próxima**.
3. Nome à aplicação, defina o `Minimum API level` para **API 19 ou mais recente**, acessos **concluir**.
5. No seu `app/build.gradle`, defina o `targetedSdkVersion` para 27. 

## <a name="register-your-application"></a>Registar a sua aplicação

Pode registar a sua aplicação em qualquer uma das duas formas, conforme descrito nas próximas duas secções.

### <a name="register-your-app"></a>Registar a sua aplicação

1. Vá para o [portal do Azure](https://aka.ms/MobileAppReg) > selecione `New registration`. 
2. Introduza um **Name** para a sua aplicação > `Register`. **Não defina um URI de redirecionamento nesta fase**. 
3. Na `Manage` secção, aceda a `Authentication` > `Add a platform` > `Android`
    - Introduza o nome do pacote do seu projeto. Se tiver transferido o código, este valor é `com.azuresamples.msalandroidapp`. 
    - Introduza o hash de assinatura de depuração/desenvolvimento. Utilize o comando de ferramenta de chave no portal para gerar o Hash de assinatura. 
4. Pressionar `Configure` e armazenar o ***configuração de MSAL*** para utilizar mais tarde. 

## <a name="build-your-app"></a>Criar a sua aplicação

### <a name="configure-your-android-app"></a>Configurar a sua aplicação Android

1. Clique com botão direito **res** > **New** > **pasta** > **não processados de recursos de pasta**
2. No **app** > **res** > **não processados**, criar um novo ficheiro JSON denominado `auth_config.json` e cole o ***MSAL configuração***. Ver [MSAL configuração para obter mais informações](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app).
   <!-- Workaround for Docs conversion bug -->
3. No **app** > **manifestos** > **androidmanifest. XML**, adicione o `BrowserTabActivity` atividade abaixo. Esta entrada permite que a Microsoft chamada de retorno para a sua aplicação depois de terminar a autenticação:

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

    Tenha em atenção de que o Hash de assinatura utilizado não deve ser codificada com na URL os **androidmanifest. XML**. 

4. Dentro do **androidmanifest. XML** e, imediatamente acima a `<application>` etiqueta, adicione as seguintes permissões:

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

5. Na `BrowserTabActivity`, substitua o ***nome do pacote*** e ***Hash de assinatura*** com valores registrados no portal do Azure.

### <a name="create-the-apps-ui"></a>Criar a IU da aplicação

1. Aceda a **res** > **layout**e, em seguida, abra **ctivity_main**.
2. Alterar o esquema de atividade de `android.support.constraint.ConstraintLayout` ou outras para `LinearLayout`.
3. Adicionar a `android:orientation="vertical"` propriedade o `LinearLayout` nó.
4. Cole o código seguinte para o `LinearLayout` nó, substituindo o conteúdo atual:

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

1. No Android Studio, selecione **Scripts de Gradle** > **gradle (módulo: aplicação)** .
2. Sob **dependências**, cole o seguinte código:

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>Utilizar a MSAL 

As próximas seções fará alterações dentro do `MainAcitivty.java`. Podemos vai ser percorrendo cada passo necessário para adicionar e utilizar a MSAL na sua aplicação.

#### <a name="required-imports"></a>Importações necessárias

Adicione as seguintes importações ao seu projeto: 

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

#### <a name="instantiating-msal"></a>Criar uma instância de MSAL 

Dentro do `MainActivity` classe, vamos precisar de criar uma instância de MSAL juntamente com algumas configurações sobre o que são aplicações fará incluindo os âmbitos e web API que pretende aceder. 

Copie as variáveis seguintes dentro do `MainActivity`:

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

Agora para criar uma instância de MSAL, copie o seguinte código dentro do `onCreate(...)` método:

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

O bloco acima de código tenta iniciar sessão dos utilizadores silenciosamente quando abrem a sua aplicação através de `getAccounts(...)` e, se tiver êxito, `acquireTokenSilentAsync(...)`.  Nas próximas secções, implementaremos o manipulador de retorno de chamada para o caso de existirem são não contas com sessão iniciada. 

#### <a name="use-msal-to-get-tokens"></a>Utilizar a MSAL para obter os Tokens

Agora, podemos implementar lógica de processamento e obter tokens de forma interativa através de MSAL da interface do Usuário a aplicação. 

A MSAL expõe dois métodos principais para obter os tokens: `acquireTokenSilentAsync` e `acquireToken`.  

`acquireTokenSilentAsync` inicia sessão um utilizador e obter tokens sem qualquer interação do usuário se uma conta estiver presente. Se tiver êxito, MSAL será handoff os tokens para a sua aplicação, caso de falha, irão gerar um `MsalUiRequiredException`.  Se esta exceção é gerada ou pretende que o seu utilizador tem de ter um início de sessão interativo na experiência (as credenciais, mfa ou outros acesso condicional, as políticas podem ou não podem ser necessário), em seguida, pode utilizar `acquireToken`.  

`acquireToken` mostrará sempre da interface do Usuário durante a tentativa de iniciar a sessão do utilizador e obter tokens; No entanto, ele poderá usar cookies de sessão no browser ou uma conta no Microsoft authenticator para lhe proporcionar uma experiência interativa SSO. 

Para começar, crie os seguintes três métodos de interface do Usuário dentro do `MainActivity` classe:

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

Em seguida, adicione um método para obter a atividade atual e processar silenciosas & interaktivního okna retornos de chamada:

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

#### <a name="use-msal-for-sign-out"></a>Utilizar a MSAL para fim de sessão

Em seguida, vamos adicionar suporte para fim de sessão à nossa aplicação. 

É importante observar, a fim de sessão com MSAL remove todas as informações conhecidas sobre um utilizador desta aplicação, mas o utilizador continuará a ter uma sessão ativa no respetivo dispositivo. Se o utilizador tentar iniciar sessão novamente eles podem ver a interação, mas não poderão ter de reintroduzir as respetivas credenciais devido à sessão de dispositivo que está a ser Active Directory. 

Para adicionar a fim de sessão, copie o seguinte método na sua aplicação que percorre todas as contas e remove-os:

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

#### <a name="call-the-microsoft-graph-api"></a>Chamar o Microsoft Graph API

Assim que obtivemos um token com êxito, podemos fazer um pedido para o Microsoft Graph API. O token de acesso será dentro de `AuthenticationResult` dentro do retorno de chamada auth `onSuccess(...)` método. Para construir um pedido de autorizado, a aplicação terá de adicionar o token de acesso para o cabeçalho HTTP:

| Chave do cabeçalho    | value                 |
| ------------- | --------------------- |
| Autorização | Portador < token de acesso > |

Para fazer isso no código, adicione os seguintes dois métodos para a sua aplicação para chamar graph e atualizar a interface do Usuário: 

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

Saiba mais sobre o [Microsoft Graph API](https://graph.microsoft.com)!

#### <a name="multi-account-applications"></a>Aplicações de várias contas

Esta aplicação baseia-se para um cenário de única conta. Cenários de várias contas também oferece suporte a MSAL, mas requer algum trabalho adicional de aplicações. Terá de criar a interface do Usuário para ajudar o usuário selecione a conta que pretendem utilizar para cada ação que requer tokens. Em alternativa, a sua aplicação pode implementar uma heurística para selecionar a conta a utilizar por meio do `getAccounts(...)` método. 

## <a name="test-your-app"></a>Testar a aplicação

### <a name="run-locally"></a>Executar localmente

Se tiver acompanhado o código acima, tente criar e implementar a aplicação para um dispositivo de teste ou emulador. Deve ser capaz de iniciar sessão e obtenha tokens para o Azure AD ou pessoais contas da Microsoft! Depois de um utilizador iniciar sessão, esta aplicação irá apresentar os dados retornados de Microsoft Graph `/me` ponto final. 

Se tiver quaisquer problemas, pode abrir um problema neste documento ou na biblioteca de MSAL e informe-nos. 

### <a name="consent-to-your-app"></a>Consentimento para a sua aplicação

Na primeira vez que qualquer utilizador inicia sessão na sua aplicação, serão solicitados pela identidade da Microsoft para dar consentimento às permissões solicitadas.  Embora a maioria dos usuários são capazes de consentir, alguns inquilinos do Azure AD tem desativado o consentimento do utilizador - exigindo que os administradores consentir em nome de todos os utilizadores.  Para suportar este cenário, certifique-se de que registe os âmbitos da sua aplicação no portal do Azure.

## <a name="help-and-support"></a>Ajuda e suporte

Teve problemas com este tutorial, ou com a plataforma de identidade da Microsoft? Consulte [ajuda e suporte](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)
