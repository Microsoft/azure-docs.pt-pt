---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: eded2d6a9f2c270a2b3ccca296277b0a016733fd
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119769"
---
1. Abra o projeto no Android Studio.

2. Na **Explorador de projeto** no Android Studio, abra o `ToDoActivity.java` de ficheiros e adicione as seguintes declarações de importação:

    ```java
    import java.util.concurrent.ExecutionException;
    import java.util.concurrent.atomic.AtomicBoolean;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;

    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
    ```

3. Adicione o seguinte método para o **ToDoActivity** classe:

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

    Este código cria um método para manipular o processo de autenticação do Google. Uma caixa de diálogo apresenta o ID do usuário autenticado. Só pode continuar numa autenticação com êxito.

    > [!NOTE]
    > Se estiver a utilizar um fornecedor de identidade que não seja o Google, altere o valor transmitido para o **início de sessão** método para um dos seguintes valores: _MicrosoftAccount_, _Facebook_, _Twitter_, ou _windowsazureactivedirectory_.

4. Na **onCreate** método, adicione a seguinte linha de código após o código que instancia o `MobileServiceClient` objeto.

    ```java
    authenticate();
    ```

    Esta chamada inicia o processo de autenticação.

5. Mover o código restante após `authenticate();` no **onCreate** método para um novo **createTable** método:

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

6. Para garantir que o redirecionamento funciona conforme esperado, adicione o seguinte fragmento de `RedirectUrlActivity` para `AndroidManifest.xml`:

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

7. Adicione `redirectUriScheme` para `build.gradle` da sua aplicação Android.

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

8. Adicione `com.android.support:customtabs:23.0.1` às dependências em seu `build.gradle`:

    ```gradle
    dependencies {
        // ...
        compile 'com.android.support:customtabs:23.0.1'
    }
    ```

9. Do **execute** menu, clique em **executar aplicação** para iniciar a aplicação e inicie sessão com o fornecedor de identidade escolhido.

> [!WARNING]
> O esquema de URL mencionado diferencia maiúsculas de minúsculas. Certifique-se de que todas as ocorrências de `{url_scheme_of_you_app}` utilizar as maiúsculas.

Quando tiver entrado com êxito, a aplicação deve ser executado sem erros e deve ser capaz de consultar o serviço de back-end e fazer atualizações aos dados.
