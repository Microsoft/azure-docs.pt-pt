---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: eded2d6a9f2c270a2b3ccca296277b0a016733fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184946"
---
1. Abra o projeto no Android Studio.

2. No **Project Explorer** no Android `ToDoActivity.java` Studio, abra o ficheiro e adicione as seguintes declarações de importação:

    ```java
    import java.util.concurrent.ExecutionException;
    import java.util.concurrent.atomic.AtomicBoolean;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;

    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
    ```

3. Adicione o seguinte método à classe **ToDoActivity:**

    ```java
    // You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
    public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

    private void authenticate() {
        // Sign in using the Google provider.
        mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the login request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // sign-in succeeded
                    createAndShowDialog(String.format("You are now signed in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    createTable();
                } else {
                    // sign-in failed, check the error message
                    String errorMessage = result.getErrorMessage();
                    createAndShowDialog(errorMessage, "Error");
                }
            }
        }
    }
    ```

    Este código cria um método para lidar com o processo de autenticação da Google. Um diálogo exibe a identificação do utilizador autenticado. Só pode proceder a uma autenticação bem sucedida.

    > [!NOTE]
    > Se estiver a utilizar um fornecedor de identidade que não o Google, altere o valor passado para o método de **login** para um dos seguintes valores: _MicrosoftAccount,_ _Facebook,_ _Twitter_ou _windowsazureactivedirectory_.

4. No método **onCreate,** adicione a seguinte linha de código `MobileServiceClient` após o código que instantaneamente o objeto.

    ```java
    authenticate();
    ```

    Esta chamada inicia o processo de autenticação.

5. Mova o código `authenticate();` restante depois no método **onCreate** para um novo método **createTable:**

    ```java
    private void createTable() {

        // Get the table instance to use.
        mToDoTable = mClient.getTable(ToDoItem.class);

        mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

        // Create an adapter to bind the items with the view.
        mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
        ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
        listViewToDo.setAdapter(mAdapter);

        // Load the items from Azure.
        refreshItemsFromTable();
    }
    ```

6. Para garantir que a reorientação funcione como `RedirectUrlActivity` `AndroidManifest.xml`esperado, adicione o seguinte corte de :

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}"
                android:host="easyauth.callback"/>
        </intent-filter>
    </activity>
    ```

7. Adicione `redirectUriScheme` `build.gradle` à sua aplicação Android.

    ```gradle
    android {
        buildTypes {
            release {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
            debug {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
        }
    }
    ```

8. Adicione `com.android.support:customtabs:23.0.1` às dependências `build.gradle`do seu:

    ```gradle
    dependencies {
        // ...
        compile 'com.android.support:customtabs:23.0.1'
    }
    ```

9. A partir do menu **Executar,** clique na **aplicação Run** para iniciar a aplicação e iniciar o comia com o seu fornecedor de identidade escolhido.

> [!WARNING]
> O sistema de URL mencionado é sensível a casos. Certifique-se de `{url_scheme_of_you_app}` que todas as ocorrências de utilização são iguais.

Quando estiver inscrito com sucesso, a aplicação deve ser executada sem erros, e deverá ser capaz de consultar o serviço back-end e fazer atualizações para os dados.
