---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: deb94cab97bd9a402676cdc5c0239da8d07ed8b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184947"
---
O exemplo anterior mostrou um login padrão, que requer que o cliente contacte tanto o fornecedor de identidade como o serviço Back-End Azure sempre que a app começa. Este método é ineficiente e pode ter problemas relacionados com o uso se muitos clientes tentarem iniciar a sua aplicação simultaneamente. Uma melhor abordagem é cache o token de autorização devolvido pelo serviço Azure, e tentar usá-lo primeiro antes de usar um sign-in baseado no provedor.

> [!NOTE]
> Pode cache o token emitido pelo serviço Back-end Azure, independentemente de estar a utilizar a autenticação gerida pelo cliente ou gerida pelo serviço. Este tutorial utiliza a autenticação gerida pelo serviço.
>
>

1. Abra o ficheiro ToDoActivity.java e adicione as seguintes declarações de importação:

    ```java
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    ```

2. Adicione os seguintes `ToDoActivity` membros à aula.

    ```java
    public static final String SHAREDPREFFILE = "temp";
    public static final String USERIDPREF = "uid";
    public static final String TOKENPREF = "tkn";
    ```

3. No ficheiro ToDoActivity.java, adicione a `cacheUserToken` seguinte definição para o método.

    ```java
    private void cacheUserToken(MobileServiceUser user)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        Editor editor = prefs.edit();
        editor.putString(USERIDPREF, user.getUserId());
        editor.putString(TOKENPREF, user.getAuthenticationToken());
        editor.commit();
    }
    ```

    Este método armazena o ID e o símbolo do utilizador num ficheiro preferencial que é marcado como privado. Isto deve proteger o acesso à cache para que outras aplicações no dispositivo não tenham acesso ao símbolo. A preferência é de sandbox para a aplicação. No entanto, se alguém tiver acesso ao dispositivo, é possível que possa ter acesso à cache simbólica através de outros meios.

   > [!NOTE]
   > Pode ainda proteger o símbolo com encriptação, se o acesso simbólico aos seus dados for considerado altamente sensível e alguém ter acesso ao dispositivo. Uma solução completamente segura está fora do âmbito deste tutorial, no entanto, e depende dos seus requisitos de segurança.
   >
   >

4. No ficheiro ToDoActivity.java, adicione a `loadUserTokenCache` seguinte definição para o método.

    ```java
    private boolean loadUserTokenCache(MobileServiceClient client)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        String userId = prefs.getString(USERIDPREF, null);
        if (userId == null)
            return false;
        String token = prefs.getString(TOKENPREF, null);
        if (token == null)
            return false;

        MobileServiceUser user = new MobileServiceUser(userId);
        user.setAuthenticationToken(token);
        client.setCurrentUser(user);

        return true;
    }
    ```

5. No ficheiro *ToDoActivity.java,* `authenticate` substitua os métodos e métodos `onActivityResult` com os seguintes, que utilizam uma cache simbólica. Altere o fornecedor de login se quiser utilizar uma conta diferente do Google.

    ```java
    private void authenticate() {
        // We first try to load a token cache if one exists.
        if (loadUserTokenCache(mClient))
        {
            createTable();
        }
        // If we failed to load a token cache, sign in and create a token cache
        else
        {
            // Sign in using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the sign-in request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // sign-in succeeded
                    createAndShowDialog(String.format("You are now signed in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    cacheUserToken(mClient.getCurrentUser());
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

6. Construa a aplicação e a autenticação de teste utilizando uma conta válida. Executa pelo menos duas vezes. Durante a primeira corrida, deve receber uma solicitação para iniciar sessão e criar a cache simbólica. Depois disso, cada corrida tenta carregar a cache simbólica para autenticação. Não deve ser obrigado a assinar.
