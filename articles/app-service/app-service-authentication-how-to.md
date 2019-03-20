---
title: Advanced a utilização de autenticação e autorização - serviço de aplicações do Azure | Documentos da Microsoft
description: Mostra como personalizar a autenticação e autorização no serviço de aplicações e obter afirmações de utilizador e tokens diferentes.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 97764db40807214e756f119ca95fd640164f0cf2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877314"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Utilização avançada de autenticação e autorização no serviço de aplicações do Azure

Este artigo mostra-lhe como personalizar o incorporado [autenticação e autorização no serviço de aplicações](overview-authentication-authorization.md)e para gerir a identidade da sua aplicação. 

Para começar rapidamente a utilizar, consulte um dos seguintes tutoriais:

* [Tutorial: Autenticar e autorizar utilizadores ponto-a-ponto no serviço de aplicações do Azure (Windows)](app-service-web-tutorial-auth-aad.md)
* [Tutorial: Autenticar e autorizar utilizadores ponto-a-ponto no serviço de aplicações do Azure para Linux](containers/tutorial-auth-aad.md)
* [Como configurar a sua aplicação para utilizar o início de sessão do Azure Active Directory](configure-authentication-provider-aad.md)
* [Como configurar a sua aplicação para utilizar o início de sessão do Facebook](configure-authentication-provider-facebook.md)
* [Como configurar a sua aplicação para utilizar o início de sessão do Google](configure-authentication-provider-google.md)
* [Como configurar a sua aplicação para utilizar o início de sessão da conta Microsoft](configure-authentication-provider-microsoft.md)
* [Como configurar a sua aplicação para utilizar o início de sessão do Twitter](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Utilizar vários fornecedores de início de sessão

A configuração do portal não oferece uma forma chave na mão para apresentar vários fornecedores de início de sessão a seus usuários (por exemplo, Facebook e Twitter). No entanto, não é difícil adicionar a funcionalidade para a sua aplicação. Os passos estão delineados do seguinte modo:

Primeiro, na **autenticação / autorização** página no portal do Azure, configure cada fornecedor de identidade que pretende ativar.

Na **ação a tomar quando o pedido não é autenticado**, selecione **pedidos permitir anónimos (sem ação)**.

Na página de início de sessão, ou a barra de navegação ou qualquer outro local da sua aplicação, adicione uma ligação de início de sessão para cada um dos fornecedores ativou (`/.auth/login/<provider>`). Por exemplo:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Quando o usuário clica em um dos links, é aberta a respectiva página de início de sessão para iniciar a sessão do utilizador.

Para redirecionar o utilizador pós-sessão-para um URL personalizado, utilize o `post_login_redirect_url` (não deve ser confundido com o URI de redirecionamento na sua configuração de fornecedor de identidade) de parâmetro de cadeia de caracteres de consulta. Por exemplo, para direcionar o utilizador para `/Home/Index` após o início de sessão, utilize o seguinte código HTML:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Validar os tokens de fornecedores

Num cliente direcionado início de sessão, o aplicativo inicia a sessão do utilizador para o fornecedor manualmente e, em seguida, envia o token de autenticação no serviço de aplicações para a validação (consulte [fluxo de autenticação](overview-authentication-authorization.md#authentication-flow)). Esta validação em si, na verdade, não concede acesso aos recursos de aplicação pretendida, mas uma validação com êxito irá dar-lhe um token de sessão que pode utilizar para aceder aos recursos da aplicação. 

Para validar o token de fornecedor, aplicação de serviço de aplicações em primeiro lugar tem de ser configurada com o fornecedor pretendido. No tempo de execução, depois de obter o token de autenticação através do seu fornecedor, publicar o token para `/.auth/login/<provider>` para validação. Por exemplo: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

O formato do token varia um pouco, de acordo com o fornecedor. Consulte a tabela seguinte para obter mais detalhes:

| Valor de fornecedor | Necessário no corpo do pedido | Comentários |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | O `expires_in` propriedade é opcional. <br/>Quando pedir o token de serviços do Live, sempre pedir o `wl.basic` âmbito. |
| `google` | `{"id_token":"<id_token>"}` | O `authorization_code` propriedade é opcional. Quando especificado, ele pode opcionalmente, também de ser acompanhado do `redirect_uri` propriedade. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Utilizar válido [token de acesso de utilizador](https://developers.facebook.com/docs/facebook-login/access-tokens) do Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Se o token de fornecedor for validado com êxito, a API devolve com um `authenticationToken` no corpo da resposta, que é o seu token de sessão. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Assim que tiver este token de sessão, pode aceder a recursos de aplicação protegida, adicionando o `X-ZUMO-AUTH` cabeçalho aos seus pedidos HTTP. Por exemplo: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Terminar uma sessão

Os utilizadores podem iniciar um fim de sessão através do envio de um `GET` pedido para a aplicação `/.auth/logout` ponto final. O `GET` pedido procede da seguinte forma:

- Limpa cookies de autenticação da sessão atual.
- Elimina tokens de acesso do utilizador atual do arquivo de tokens.
- Para o Azure Active Directory e o Google, efetua um servidor fim de sessão do fornecedor de identidade.

Este é um link de fim de sessão simples numa página Web:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Por predefinição, um êxito fim de sessão redireciona o cliente para o URL `/.auth/logout/done`. Pode alterar a página de redirecionamento post-sign-out adicionando o `post_logout_redirect_uri` parâmetro de consulta. Por exemplo:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

É recomendado que [codificar](https://wikipedia.org/wiki/Percent-encoding) o valor de `post_logout_redirect_uri`.

Quando utilizar URLs completamente qualificados, o URL deve ser hospedado no mesmo domínio ou configurado como um URL de redirecionamento externo permitido para a sua aplicação. No exemplo a seguir, para redirecionar para `https://myexternalurl.com` que não está alojada no mesmo domínio:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Tem de executar o seguinte comando [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Preservar fragmentos de URL

Depois dos utilizadores iniciam sessão na sua aplicação, normalmente, eles querem ser redirecionado para a mesma seção da mesma página, tal como `/wiki/Main_Page#SectionZ`. No entanto, uma vez [fragmentos de URL](https://wikipedia.org/wiki/Fragment_identifier) (por exemplo, `#SectionZ`) nunca são enviadas para o servidor, eles não são mantidos por predefinição quando o OAuth início de sessão estiver concluída e redireciona para a sua aplicação. Os utilizadores, em seguida, obtém uma experiência de inferior ao ideal quando precisam para navegar novamente para a âncora pretendida. Esta limitação se aplica a todas as soluções de autenticação de servidor.

Na autenticação do serviço de aplicações, pode preservar fragmentos de URL em toda o OAuth início de sessão. Para tal, defina uma aplicação chamada `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` para `true`. Pode fazê-lo [portal do Azure](https://portal.azure.com), ou simplesmente executar o seguinte comando na [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Afirmações de utilizador de acesso

Serviço de aplicações passa afirmações de utilizador à sua aplicação ao utilizar cabeçalhos especiais. Solicitações externas não são permitidas para definir esses cabeçalhos, para que estejam presentes apenas se for definido pelo serviço de aplicações. Alguns cabeçalhos de exemplo incluem:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

O código escrito em qualquer linguagem ou arquitetura pode obter as informações que necessita desses cabeçalhos. Para aplicações ASP.NET 4.6, o **ClaimsPrincipal** é definida automaticamente com os valores apropriados.

Seu aplicativo também pode obter detalhes adicionais sobre o utilizador autenticado ao chamar `/.auth/me`. O servidor de aplicações móveis SDKs fornecem métodos auxiliares para trabalhar com estes dados. Para obter mais informações, consulte [como utilizar o Azure Mobile Apps node. js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity), e [trabalhar com o SDK do servidor de back-end de .NET para aplicações móveis do Azure](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Obter tokens no código da aplicação

A partir do código de servidor, os tokens de específica do fornecedor serão inseridos o cabeçalho do pedido, para que pode acessá-los facilmente. A tabela seguinte mostra os nomes de cabeçalho de token possíveis:

| Fornecedor | Nomes de cabeçalho |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook Token | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Conta Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

A partir do código de cliente (por exemplo, uma aplicação móvel ou JavaScript no browser), enviar um HTTP `GET` pedido para `/.auth/me`. O JSON devolvido tem os tokens de específica do fornecedor.

> [!NOTE]
> São tokens de acesso para aceder a recursos de fornecedor, para que estejam presentes apenas se configurar o seu fornecedor com um segredo do cliente. Para ver como obter tokens de atualização, consulte os tokens de acesso de atualização.

## <a name="refresh-identity-provider-tokens"></a>Tokens de fornecedor de identidade de atualização

Quando o token de acesso do seu fornecedor (não o [token de sessão](#extend-session-token-expiration-grace-period)) expirar, terá de autenticar o utilizador antes de utilizar esse token novamente. Pode evitar a expiração do token, fazendo uma `GET` chamar para o `/.auth/refresh` ponto final do seu aplicativo. Quando chamado, o serviço de aplicações atualiza automaticamente os tokens de acesso no arquivo de tokens para o usuário autenticado. Pedidos subsequentes para tokens pelo seu código de aplicação obtém os tokens de atualização. No entanto, para a atualização de token funcionar, o arquivo de tokens tem de conter [tokens de atualização](https://auth0.com/learn/refresh-tokens/) para o seu fornecedor. A forma de obter tokens de atualização estão documentados pelo cada fornecedor, mas a lista seguinte é um breve resumo:

- **Google**: Acrescentar um `access_type=offline` consultar o parâmetro de cadeia de caracteres para seu `/.auth/login/google` chamada à API. Se utilizar o SDK de aplicações móveis, pode adicionar o parâmetro para um da `LogicAsync` sobrecargas (consulte [Tokens de atualização do Google](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: Não fornece tokens de atualização. Tokens de longa duração expirarem em 60 dias (consulte [expiração do Facebook e extensão de Tokens de acesso](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: Tokens de acesso não expiram (consulte [FAQ de OAuth do Twitter](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Conta Microsoft**: Quando [configurar definições de autenticação de conta do Microsoft](configure-authentication-provider-microsoft.md), selecione o `wl.offline_access` âmbito.
- **Azure Active Directory**: Na [ https://resources.azure.com ](https://resources.azure.com), siga os passos abaixo:
    1. Na parte superior da página, selecione **leitura/escrita**.
    2. No navegador à esquerda, navegue até **subscrições** > **_\<subscrição\_nome_**   >  **resourceGroups** > _**\<recursos\_grupo\_nome >**_   >  **provedores** > **Microsoft. Web** > **sites** > _**\<aplicação \_name >**_ > **config** > **authsettings**. 
    3. Clique em **Editar**.
    4. Modifique a seguinte propriedade. Substitua  _\<aplicação\_id >_ com o ID da aplicação do Azure Active Directory do serviço pretende aceder.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Clique em **colocar**. 

Assim que o fornecedor está configurado, pode [encontrar o token de atualização e a hora de expiração para o token de acesso](#retrieve-tokens-in-app-code) no arquivo de tokens. 

Para atualizar o token de acesso a qualquer altura, basta chamar `/.auth/refresh` em qualquer idioma. O fragmento seguinte utiliza o jQuery para atualizar seus tokens de acesso de um cliente JavaScript.

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

Se um utilizador revoga as permissões concedidas à sua aplicação, a chamada para `/.auth/me` poderá falhar com um `403 Forbidden` resposta. Para diagnosticar erros, verifique os registos da aplicação para obter detalhes.

## <a name="extend-session-token-expiration-grace-period"></a>Estender o período de tolerância de expiração do token de sessão

Sessão autenticada expira após oito horas. Depois de uma sessão autenticada expira, existe um período de tolerância de 72 horas por predefinição. Durante este período de tolerância, tem permissão para atualizar o token de sessão com o serviço de aplicações sem reautenticar o utilizador. Pode simplesmente chamar `/.auth/refresh` quando o token de sessão se torna inválido e não precisa de controlar a expiração do token por conta própria. Assim que o período de tolerância de 72 horas encerraram, o utilizador deve iniciar sessão novamente para obter o token de uma sessão válida.

Se 72 horas não estiver tempo suficiente para, pode estender esta janela de expiração. Estendendo a expiração durante um longo período pode ter implicações de segurança significativos (por exemplo, quando um token de autenticação for perdido ou roubado). Por isso, deve deixá-lo com a predefinição 72 horas ou definir o período de extensão para o valor mais baixo.

Para expandir a janela de expiração predefinida, execute o seguinte comando [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> O período de tolerância só se aplica à sessão do serviço de aplicações que authenticated, não os tokens de fornecedores de identidade. Não há nenhum período de cortesia para os tokens expirados fornecedor. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Limite do domínio de contas de início de sessão

Account da Microsoft e do Azure Active Directory permite-lhe iniciar sessão a partir de vários domínios. Por exemplo, permite que a Microsoft Account _outlook.com_, _live.com_, e _hotmail.com_ contas. O Azure Active Directory permite que qualquer número de domínios personalizados para as contas de início de sessão. Esse comportamento pode ser indesejável para uma aplicação interna, que não pretende qualquer pessoa com uma _outlook.com_ para acesso da conta. Para limitar o nome de domínio das contas de início de sessão, siga estes passos.

Na [ https://resources.azure.com ](https://resources.azure.com), navegue até à **subscrições** > **_\<subscrição\_nome_**   >  **resourceGroups** > _**\<recursos\_grupo\_nome >**_   >  **fornecedores** > **Microsoft. Web** > **sites**  >    _**\<app\_nome >**_ > **config** > **authsettings**. 

Clique em **edite**, modifique a seguinte propriedade e, em seguida, clique em **colocar**. Não se esqueça de substituir  _\<domínio\_name >_ com o domínio que pretende.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Autenticar e autorizar utilizadores ponto-a-ponto (Windows)](app-service-web-tutorial-auth-aad.md)
> [Tutorial: Autenticar e autorizar utilizadores ponto-a-ponto (Linux)](containers/tutorial-auth-aad.md)
