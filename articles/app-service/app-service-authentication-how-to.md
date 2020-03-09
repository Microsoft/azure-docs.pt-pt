---
title: Utilização avançada da AuthN/AuthO
description: Aprenda a personalizar a funcionalidade de autenticação e autorização no App Service para diferentes cenários e obtenha reclamações de utilizadores e fichas diferentes.
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: d57b196bf95ebdf31bc459ad4b9d718fd32ca495
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358116"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Utilização avançada de autenticação e autorização no Serviço de Aplicações Azure

Este artigo mostra-lhe como personalizar a [autenticação e autorização](overview-authentication-authorization.md)incorporadas no Serviço de Aplicações e gerir a identidade da sua aplicação. 

Para começar rapidamente, consulte um dos seguintes tutoriais:

* [Tutorial: Autenticar e autorizar utilizadores de ponta a ponta no Serviço de Aplicações Azure (Windows)](app-service-web-tutorial-auth-aad.md)
* [Tutorial: Autenticar e autorizar utilizadores de ponta a ponta no Serviço de Aplicações Azure para linux](containers/tutorial-auth-aad.md)
* [Como configurar a sua aplicação para utilizar o início de sessão do Azure Active Directory](configure-authentication-provider-aad.md)
* [Como configurar a sua aplicação para utilizar o início de sessão do Facebook](configure-authentication-provider-facebook.md)
* [Como configurar a sua aplicação para utilizar o início de sessão do Google](configure-authentication-provider-google.md)
* [Como configurar a sua aplicação para utilizar o início de sessão da conta Microsoft](configure-authentication-provider-microsoft.md)
* [Como configurar a sua aplicação para utilizar o início de sessão do Twitter](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Utilize vários fornecedores de sessão

A configuração do portal não oferece uma forma chave-de-mão de apresentar vários fornecedores de entrada aos seus utilizadores (como facebook e Twitter). No entanto, não é difícil adicionar a funcionalidade à sua aplicação. Os passos são delineados da seguinte forma:

Primeiro, na página de **Autenticação/Autorização** no portal Azure, configure cada um dos fornecedores de identidade que pretende ativar.

Em Ação a tomar quando o **pedido não é autenticado,** selecione **Permitir pedidos Anónimos (sem ação)** .

Na página de sessão, ou na barra de navegação, ou em qualquer outra localização da sua aplicação, adicione um link de sessão a cada um dos fornecedores que ativou (`/.auth/login/<provider>`). Por exemplo:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Quando o utilizador clica num dos links, a respetiva página de sessão abre-se para iniciar sessão no utilizador.

Para redirecionar o pós-login do utilizador para um URL personalizado, utilize o parâmetro de cadeia de consulta `post_login_redirect_url` (não confundir com o Redirect URI na configuração do seu fornecedor de identidade). Por exemplo, para navegar o utilizador para `/Home/Index` após o iniciar o sessão, utilize o seguinte código HTML:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Validar fichas de fornecedores

Num login dirigido pelo cliente, a aplicação assina manualmente no utilizador ao fornecedor e, em seguida, submete o símbolo de autenticação ao Serviço de Aplicações para validação (ver fluxo de [autenticação).](overview-authentication-authorization.md#authentication-flow) Esta validação em si não lhe dá acesso aos recursos de aplicações desejados, mas uma validação bem sucedida lhe dará um sinal de sessão que pode usar para aceder aos recursos da aplicação. 

Para validar o token do fornecedor, a aplicação App Service deve primeiro ser configurada com o fornecedor pretendido. No tempo de execução, depois de recuperar o símbolo de autenticação do seu fornecedor, poste o símbolo para `/.auth/login/<provider>` para validação. Por exemplo: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

O formato simbólico varia ligeiramente de acordo com o fornecedor. Consulte a tabela seguinte para mais detalhes:

| Valor do fornecedor | Obrigatório no órgão de pedido | Comentários |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | A propriedade `expires_in` é opcional. <br/>Ao solicitar o sinal dos serviços ao vivo, solicite sempre o âmbito `wl.basic`. |
| `google` | `{"id_token":"<id_token>"}` | A propriedade `authorization_code` é opcional. Quando especificado, também pode ser acompanhado opcionalmente pela propriedade `redirect_uri`. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Utilize um sinal de acesso válido [ao utilizador](https://developers.facebook.com/docs/facebook-login/access-tokens) do Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Se o token do fornecedor for validado com sucesso, a API regressa com um `authenticationToken` no corpo de resposta, que é o seu símbolo de sessão. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Assim que tiver esta ficha de sessão, pode aceder aos recursos de aplicações protegidos adicionando o cabeçalho `X-ZUMO-AUTH` aos seus pedidos HTTP. Por exemplo: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Assine fora de uma sessão

Os utilizadores podem iniciar uma inscrição enviando um pedido de `GET` para o `/.auth/logout` ponto final da app. O pedido de `GET` faz o seguinte:

- Limpa os cookies de autenticação da sessão atual.
- Elimina as fichas do utilizador atual da loja de fichas.
- Para o Azure Ative Directory e google, realiza uma inscrição do lado do servidor no fornecedor de identidade.

Aqui está um simples link de inscrição numa página web:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Por predefinição, um sinal de saída bem sucedido redireciona o cliente para o URL `/.auth/logout/done`. Pode alterar a página de redirecionamento pós-inscrição adicionando o parâmetro de consulta `post_logout_redirect_uri`. Por exemplo:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Recomenda-se que [codifique](https://wikipedia.org/wiki/Percent-encoding) o valor da `post_logout_redirect_uri`.

Ao utilizar URLs totalmente qualificados, o URL deve ser hospedado no mesmo domínio ou configurado como um URL externo de redirecionamento permitido para a sua aplicação. No exemplo seguinte, redirecionar para `https://myexternalurl.com` que não está hospedado no mesmo domínio:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Executar o seguinte comando na [Casca de Nuvem Azure:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Preservar fragmentos de URL

Depois de os utilizadores iniciarem sessão na sua aplicação, normalmente querem ser redirecionados para a mesma secção da mesma página, como `/wiki/Main_Page#SectionZ`. No entanto, uma vez que os [fragmentos](https://wikipedia.org/wiki/Fragment_identifier) de URL (por exemplo, `#SectionZ`) nunca são enviados para o servidor, não são preservados por padrão após o login OAuth completar e redirecionar de volta para a sua aplicação. Os utilizadores obtêm então uma experiência sub-óptima quando precisam de navegar novamente para a âncora desejada. Esta limitação aplica-se a todas as soluções de autenticação do lado do servidor.

Na autenticação do Serviço de Aplicações, pode preservar fragmentos de URL através do login OAuth. Para tal, defina uma definição de aplicação chamada `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` para `true`. Pode fazê-lo no [portal Azure,](https://portal.azure.com)ou simplesmente executar o seguinte comando na [Casca de Nuvem Azure:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Aceder às reclamações dos utilizadores

O Serviço de Aplicações transmite as alegações dos utilizadores à sua aplicação utilizando cabeçalhos especiais. Os pedidos externos não são permitidos definir estes cabeçalhos, por isso só estão presentes se definidos pelo Serviço de Aplicações. Alguns cabeçalhos de exemplo incluem:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

O código que está escrito em qualquer língua ou enquadramento pode obter a informação de que necessita destes cabeçalhos. Para ASP.NET 4.6 aplicações, o **ClaimsPrincipal** é automaticamente definido com os valores apropriados. ASP.NET Core, no entanto, não fornece um middleware de autenticação que se integre com as alegações dos utilizadores do App Service. Para uma suposição, consulte [MaximeRouiller.Azure.AppService.EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

A sua aplicação também pode obter detalhes adicionais sobre o utilizador autenticado, ligando para `/.auth/me`. Os SDKs do servidor de aplicativos móveis fornecem métodos de ajuda para trabalhar com estes dados. Para mais informações, consulte [Como utilizar as aplicações móveis Azure Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)e [Trabalhe com o servidor de backend .NET SDK para Aplicações Móveis Azure](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Recuperar fichas no código da aplicação

A partir do código do servidor, as fichas específicas do fornecedor são injetadas no cabeçalho do pedido, para que possa aceder facilmente aos mesmos. A tabela que se segue mostra possíveis nomes de cabeçalho simbólico:

| Fornecedor | Nomes de cabeçalho |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Ficha do Facebook | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Conta Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

A partir do seu código de cliente (como uma aplicação móvel ou javaScript no navegador), envie um pedido http `GET` para `/.auth/me`. A JSON devolvida tem as fichas específicas do fornecedor.

> [!NOTE]
> Os tokens de acesso são para aceder aos recursos do fornecedor, pelo que só estão presentes se configurar o seu fornecedor com um segredo de cliente. Para ver como obter fichas refrescantes, consulte fichas de acesso Refresh.

## <a name="refresh-identity-provider-tokens"></a>Atualizar fichas de fornecedor de identidade

Quando o sinal de acesso do seu fornecedor (não o token da [sessão)](#extend-session-token-expiration-grace-period)expirar, tem de reautenticar o utilizador antes de voltar a utilizar esse símbolo. Pode evitar a expiração do token fazendo uma chamada `GET` para o ponto final `/.auth/refresh` da sua aplicação. Quando chamado, o Serviço de Aplicações atualiza automaticamente as fichas de acesso na loja token para o utilizador autenticado. Os pedidos subsequentes de fichas pelo código da aplicação obtêm os tokens renovados. No entanto, para que a atualização token funcione, a loja de fichas deve conter [fichas de atualização](https://auth0.com/learn/refresh-tokens/) para o seu fornecedor. A forma de obter fichas de atualização é documentada por cada fornecedor, mas a seguinte lista é um breve resumo:

- **Google**: Anexar um parâmetro de corda de consulta `access_type=offline` à sua chamada `/.auth/login/google` API. Se utilizar o SDK de Aplicações Móveis, pode adicionar o parâmetro a uma das sobrecargas `LogicAsync` (ver [Google Refresh Tokens](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: Não fornece fichas de atualização. As fichas de longa duração expiram em 60 dias (ver [Facebook Expiração e Extensão de Fichas de Acesso).](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)
- **Twitter**: As fichas de acesso não expiram (ver [Twitter OAuth FAQ](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Conta Microsoft**: Ao [configurar as definições](configure-authentication-provider-microsoft.md)de autenticação da conta microsoft, selecione o âmbito `wl.offline_access`.
- **Diretório Ativo Azure**: Em [https://resources.azure.com, ](https://resources.azure.com)faça os seguintes passos:
    1. No topo da página, selecione **Ler/Escrever**.
    2. No navegador esquerdo, navegue para **subscrições** >  **_\<nome\__** de **recursos > os grupos** >  **_\<\_\_nome>_** **fornecedores** de >  > **Microsoft.Web** > **sites** > \<app **_\_nome>_**  > **config** > **authsettings.** 
    3. Clique em **Editar**.
    4. Modificar a seguinte propriedade. Substitua _\<app\_id>_ com o ID de aplicação do Diretório Ativo Azure do serviço a que pretende aceder.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Clique em **Colocar**. 

Uma vez configurado o seu fornecedor, pode encontrar o token de [atualização e o tempo de validade para o token](#retrieve-tokens-in-app-code) de acesso na loja de fichas. 

Para refrescar o seu sinal de acesso a qualquer momento, basta ligar para `/.auth/refresh` em qualquer idioma. O seguinte snippet usa jQuery para refrescar as suas fichas de acesso de um cliente JavaScript.

```JavaScript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Se um utilizador revogar as permissões concedidas à sua aplicação, a sua chamada para `/.auth/me` pode falhar com uma resposta `403 Forbidden`. Para diagnosticar erros, verifique os registos da sua aplicação para obter detalhes.

## <a name="extend-session-token-expiration-grace-period"></a>Prolongar período de carência de validade da sessão

A sessão autenticada expira após 8 horas. Após o termo de uma sessão autenticada, existe um período de carência de 72 horas por defeito. Dentro deste período de carência, é permitido refrescar a sessão com o Serviço de Aplicações sem reautenticar o utilizador. Pode saqueá`/.auth/refresh` quando o seu token de sessão se tornar inválido, e não precisa de rastrear a expiração do símbolo. Uma vez que o período de carência de 72 horas é caducado, o utilizador deve iniciar sessão novamente para obter um token de sessão válido.

Se 72 horas não forem tempo suficiente para si, pode estender esta janela de validade. O alargamento da expiração por um longo período pode ter implicações significativas na segurança (como quando um token de autenticação é vazado ou roubado). Por isso, deve deixá-lo no padrão 72 horas ou definir o período de extensão para o menor valor.

Para estender a janela de validade predefinida, execute o seguinte comando na [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> O período de carência aplica-se apenas à sessão autenticada do Serviço de Aplicações, e não aos símbolos dos fornecedores de identidade. Não existe um período de carência para os tokens do fornecedor expirado. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Limitar o domínio das contas de inscrição

Tanto a Microsoft Account como o Azure Ative Directory permitem-lhe iniciar sessão a partir de vários domínios. Por exemplo, a Conta Microsoft permite _contas outlook.com_, _live.com_e _hotmail.com._ A Azure AD permite qualquer número de domínios personalizados para as contas de entrada. No entanto, pode querer acelerar os seus utilizadores diretamente para a sua própria página de entrada de anúncios Azure AD (como `contoso.com`). Para sugerir o nome de domínio das contas de inscrição, siga estes passos.

Em [https://resources.azure.com, ](https://resources.azure.com)navegue para **subscrições** >  **_\<nome\_ > _** **recursosGroup** > \<\_grupo\_ **_nome>_** **fornecedores** de >  > **Microsoft.Web** > **sites** > app\< **_\_nome>_**  > **config** > **authsettings.** 

Clique em **Editar,** modifique a seguinte propriedade e, em seguida, clique em **Colocar**. Certifique-se de substituir _\<domínio\_nome>_ pelo domínio que deseja.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Esta definição adere o parâmetro de corda de consulta `domain_hint` ao URL de redirecionamento de login. 

> [!IMPORTANT]
> É possível que o cliente remova o parâmetro `domain_hint` depois de receber o URL de redirecionamento e, em seguida, iniciar sessão com um domínio diferente. Por isso, embora esta função seja conveniente, não é uma funcionalidade de segurança.
>

## <a name="authorize-or-deny-users"></a>Autorizar ou negar utilizadores

Embora o Serviço de Aplicações cuide do caso de autorização mais simples (ou seja, rejeitar pedidos não autenticados), a sua aplicação pode necessitar de um comportamento de autorização mais fino, como limitar o acesso a apenas um determinado grupo de utilizadores. Em certos casos, é necessário escrever um código de aplicação personalizado para permitir ou negar o acesso ao utilizador inscrito. Noutros casos, o Serviço de Aplicações ou o seu fornecedor de identidade poderão ajudar sem exigir alterações de código.

- [Nível de servidor](#server-level-windows-apps-only)
- [Nível de fornecedor de identidade](#identity-provider-level)
- [Nível de candidatura](#application-level)

### <a name="server-level-windows-apps-only"></a>Nível de servidor (apenas aplicações do Windows)

Para qualquer aplicação do Windows, pode definir o comportamento de autorização do servidor web IIS, editando o ficheiro *Web.config.* As aplicações Linux não usam o IIS e não podem ser configuradas através do *Web.config*.

1. Navegue para `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. No navegador explorador dos seus ficheiros do Serviço de Aplicações, navegue para *site/wwwroot*. Se um *Web.config* não existir, crie-o selecionando **+**  > **Novo Ficheiro**. 

1. Selecione o lápis para *Web.config* para editá-lo. Adicione o seguinte código de configuração e clique em **Guardar**. Se *web.config* já existir, basta adicionar o elemento `<authorization>` com tudo o que estiver nele. Adicione as contas que pretende permitir no elemento `<allow>`.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Nível de fornecedor de identidade

O fornecedor de identidade pode fornecer uma certa autorização chave-na-mão. Por exemplo:

- Para o [Azure App Service,](configure-authentication-provider-aad.md)pode gerir o acesso a [nível empresarial](../active-directory/manage-apps/what-is-access-management.md) diretamente em Azure AD. Para obter instruções, consulte Como remover o [acesso de um utilizador a uma aplicação](../active-directory/manage-apps/methods-for-removing-user-access.md).
- Para o [Google,](configure-authentication-provider-google.md)os projetos google API que pertencem a uma [organização](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) podem ser configurados para permitir o acesso apenas aos utilizadores da sua organização (ver página de [suporte o **OAuth 2.0** da Google).](https://support.google.com/cloud/answer/6158849?hl=en)

### <a name="application-level"></a>Nível de candidatura

Se algum dos outros níveis não fornecer a autorização de que necessita, ou se a sua plataforma ou fornecedor de identidade não for suportado, deve escrever código personalizado para autorizar utilizadores com base nas [alegações](#access-user-claims)do utilizador .

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Autenticar e autorizar utilizadores de ponta a ponta (Windows)](app-service-web-tutorial-auth-aad.md)
> [Tutorial: Autenticar e autorizar utilizadores de ponta a ponta (Linux)](containers/tutorial-auth-aad.md)
