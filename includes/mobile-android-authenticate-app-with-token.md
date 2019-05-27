---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: deb94cab97bd9a402676cdc5c0239da8d07ed8b2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66141023"
---
O exemplo anterior mostrava um padrão de início de sessão, que exige que o cliente contactar o fornecedor de identidade e o serviço do Azure de back-end, sempre que a aplicação for iniciada. Este método é ineficaz e pode ter problemas relacionados com a utilização se muitos clientes tentarem iniciar a sua aplicação em simultâneo. Uma abordagem melhor é armazenar em cache o token de autorização retornado pelo serviço do Azure, e tente utilizar nesta primeira antes de utilizar um fornecedor com base no início de sessão.

> [!NOTE]
> Pode colocar em cache o token emitido pelo serviço do Azure, independentemente de se está a utilizar a autenticação gerida pelo cliente ou pelo serviço de back-end. Este tutorial utiliza a autenticação gerida pelo serviço.
>
>

1. Abra o ficheiro Todoactivity e adicione as seguintes declarações de importação:

    ```java
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    ```

2. Adicionar os seguintes membros para o `ToDoActivity` classe.

    ```java
    public static final String SHAREDPREFFILE = "temp";
    public static final String USERIDPREF = "uid";
    public static final String TOKENPREF = "tkn";
    ```

3. No ficheiro Todoactivity, adicione a seguinte definição para o `cacheUserToken` método.

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

    Este método armazena o ID de utilizador e o token num arquivo de preferência marcado como privado. Isso deve proteger o acesso à cache para que outras aplicações no dispositivo não tem acesso ao token. A preferência é restrita para a aplicação. No entanto, se alguém obtiver acesso ao dispositivo, é possível que eles obter acesso para a cache de tokens por outros meios.

   > [!NOTE]
   > Pode proteger ainda mais o token com a encriptação, se o token acesso aos seus dados é considerada como altamente confidencial e alguém pode ganhar acesso ao dispositivo. Uma solução completamente segura está além do escopo deste tutorial, no entanto e depende dos requisitos de segurança.
   >
   >

4. No ficheiro Todoactivity, adicione a seguinte definição para o `loadUserTokenCache` método.

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

5. Na *Todoactivity* do ficheiro, substitua a `authenticate` e `onActivityResult` métodos com os seguintes, que utiliza uma cache de tokens. Altere o fornecedor de início de sessão se pretender utilizar uma conta diferente da Google.

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

6. Crie a autenticação de aplicação e de teste usando uma conta válida. Execute-a, pelo menos, duas vezes. Durante a primeira execução, deve receber um pedido para iniciar sessão e criar a cache de token. Depois disso, cada execução tenta carregar o cache de token para autenticação. Não deve ser pedido para iniciar sessão.
