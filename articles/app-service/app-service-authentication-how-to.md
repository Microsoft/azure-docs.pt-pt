---
title: Uso avançado de autenticação e autorização – serviço de Azure App | Microsoft Docs
description: Mostra como personalizar a autenticação e a autorização no serviço de aplicativo e obter declarações de usuário e tokens diferentes.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/08/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ee8d8c54bd618780e00d9975f2fc6950cd795d44
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098547"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Uso avançado de autenticação e autorização no serviço Azure App

Este artigo mostra como personalizar a [autenticação interna e a autorização no serviço de aplicativo](overview-authentication-authorization.md)e gerenciar a identidade do seu aplicativo. 

Para começar rapidamente, consulte um dos seguintes tutoriais:

* [Tutorial: Autenticar e autorizar usuários de ponta a ponta no serviço de Azure App (Windows)](app-service-web-tutorial-auth-aad.md)
* [Tutorial: Autenticar e autorizar usuários de ponta a ponta no serviço de Azure App para Linux](containers/tutorial-auth-aad.md)
* [Como configurar a sua aplicação para utilizar o início de sessão do Azure Active Directory](configure-authentication-provider-aad.md)
* [Como configurar a sua aplicação para utilizar o início de sessão do Facebook](configure-authentication-provider-facebook.md)
* [Como configurar a sua aplicação para utilizar o início de sessão do Google](configure-authentication-provider-google.md)
* [Como configurar a sua aplicação para utilizar o início de sessão da conta Microsoft](configure-authentication-provider-microsoft.md)
* [Como configurar a sua aplicação para utilizar o início de sessão do Twitter](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Usar vários provedores de entrada

A configuração do portal não oferece uma forma de chave para apresentar vários provedores de entrada para seus usuários (como o Facebook e o Twitter). No entanto, não é difícil adicionar a funcionalidade ao seu aplicativo. As etapas são descritas a seguir:

Primeiro, na página **autenticação/autorização** no portal do Azure, configure cada provedor de identidade que você deseja habilitar.

Em **ação a ser tomada quando a solicitação não for autenticada**, selecione **Permitir solicitações anônimas (nenhuma ação)** .

Na página de entrada ou na barra de navegação ou em qualquer outro local do seu aplicativo, adicione um link de entrada para cada um dos provedores habilitados (`/.auth/login/<provider>`). Por exemplo:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Quando o usuário clica em um dos links, a página de entrada respectiva é aberta para entrar no usuário.

Para redirecionar a postagem do usuário para uma URL personalizada, use o `post_login_redirect_url` parâmetro de cadeia de caracteres de consulta (não deve ser confundido com o URI de redirecionamento em sua configuração de provedor de identidade). Por exemplo, para navegar pelo usuário para `/Home/Index` depois de entrar, use o seguinte código HTML:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Validar tokens de provedores

Em uma entrada direcionada ao cliente, o aplicativo conecta o usuário ao provedor manualmente e, em seguida, envia o token de autenticação para o serviço de aplicativo para validação (consulte [fluxo de autenticação](overview-authentication-authorization.md#authentication-flow)). Na verdade, essa validação não concede a você acesso aos recursos de aplicativo desejados, mas uma validação bem-sucedida fornecerá um token de sessão que você pode usar para acessar os recursos do aplicativo. 

Para validar o token do provedor, o aplicativo do serviço de aplicativo deve primeiro ser configurado com o provedor desejado. Em tempo de execução, depois de recuperar o token de autenticação do seu provedor, poste `/.auth/login/<provider>` o token para para validação. Por exemplo: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

O formato do token varia um pouco de acordo com o provedor. Consulte a tabela a seguir para obter detalhes:

| Valor do provedor | Necessário no corpo da solicitação | Comentários |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | A `expires_in` propriedade é opcional. <br/>Ao solicitar o token de serviços dinâmicos, sempre solicite o `wl.basic` escopo. |
| `google` | `{"id_token":"<id_token>"}` | A `authorization_code` propriedade é opcional. Quando especificado, ele também pode ser acompanhado pela `redirect_uri` propriedade. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Use um [token de acesso de usuário](https://developers.facebook.com/docs/facebook-login/access-tokens) válido do Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Se o token do provedor for validado com êxito, a API retornará `authenticationToken` com um no corpo da resposta, que é o seu token de sessão. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Assim que tiver esse token de sessão, você poderá acessar os recursos do aplicativo protegido `X-ZUMO-AUTH` adicionando o cabeçalho às suas solicitações HTTP. Por exemplo: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Sair de uma sessão

Os usuários podem iniciar uma saída enviando uma `GET` solicitação ao ponto de extremidade do `/.auth/logout` aplicativo. A `GET` solicitação faz o seguinte:

- Limpa os cookies de autenticação da sessão atual.
- Exclui os tokens do usuário atual do repositório de token.
- Para Azure Active Directory e Google, o executa uma saída do lado do servidor no provedor de identidade.

Aqui está um link de saída simples em uma página da Web:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Por padrão, uma saída bem-sucedida redireciona o cliente para a URL `/.auth/logout/done`. Você pode alterar a página de redirecionamento pós-sair adicionando o `post_logout_redirect_uri` parâmetro de consulta. Por exemplo:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

É recomendável que você [codifique](https://wikipedia.org/wiki/Percent-encoding) o valor de `post_logout_redirect_uri`.

Ao usar URLs totalmente qualificadas, a URL deve ser hospedada no mesmo domínio ou configurada como uma URL de redirecionamento externa permitida para seu aplicativo. No exemplo a seguir, para redirecionar para `https://myexternalurl.com` isso não está hospedado no mesmo domínio:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Você deve executar o seguinte comando no [Azure cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Preservar fragmentos de URL

Depois que os usuários entram em seu aplicativo, eles geralmente desejam ser redirecionados para a mesma seção da mesma página, como `/wiki/Main_Page#SectionZ`. No entanto, como os [fragmentos](https://wikipedia.org/wiki/Fragment_identifier) de URL `#SectionZ`(por exemplo,) nunca são enviados ao servidor, eles não são preservados por padrão depois que a entrada do OAuth é concluída e redireciona de volta para seu aplicativo. Em seguida, os usuários obtêm uma experiência ideal quando precisam navegar até a âncora desejada novamente. Essa limitação se aplica a todas as soluções de autenticação do lado do servidor.

Na autenticação do serviço de aplicativo, você pode preservar os fragmentos de URL na entrada OAuth. Para fazer isso, defina uma configuração de aplicativo `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` chamada `true`para. Você pode fazer isso na [portal do Azure](https://portal.azure.com)ou simplesmente executar o seguinte comando no [Azure cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Acessar declarações de usuário

O serviço de aplicativo passa declarações do usuário para seu aplicativo usando cabeçalhos especiais. Solicitações externas não têm permissão para definir esses cabeçalhos, portanto, elas estão presentes somente se definidas pelo serviço de aplicativo. Alguns cabeçalhos de exemplo incluem:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

O código escrito em qualquer linguagem ou estrutura pode obter as informações necessárias desses cabeçalhos. Para aplicativos ASP.NET 4,6, o **ClaimsPrincipal** é definido automaticamente com os valores apropriados.

Seu aplicativo também pode obter detalhes adicionais sobre o usuário autenticado chamando `/.auth/me`. Os SDKs do servidor de aplicativos móveis fornecem métodos auxiliares para trabalhar com esses dados. Para obter mais informações, consulte [como usar o SDK do node. js de aplicativos móveis do Azure](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)e [trabalhar com o SDK do servidor de back-end do .net para aplicativos móveis do Azure](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Recuperar tokens no código do aplicativo

No código do servidor, os tokens específicos do provedor são injetados no cabeçalho da solicitação, para que você possa acessá-los facilmente. A tabela a seguir mostra os possíveis nomes de cabeçalho de token:

| Fornecedor | Nomes de cabeçalho |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Token do Facebook | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Conta Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Do seu código de cliente (como um aplicativo móvel ou JavaScript no navegador), envie uma solicitação HTTP `GET` para. `/.auth/me` O JSON retornado tem os tokens específicos do provedor.

> [!NOTE]
> Tokens de acesso são para acessar recursos do provedor, para que eles estejam presentes somente se você configurar seu provedor com um segredo do cliente. Para ver como obter tokens de atualização, consulte atualizar tokens de acesso.

## <a name="refresh-identity-provider-tokens"></a>Atualizar tokens do provedor de identidade

Quando o token de acesso do seu provedor (não o [token de sessão](#extend-session-token-expiration-grace-period)) expirar, você precisará autenticar novamente o usuário antes de usar esse token novamente. Você pode evitar a expiração do token `GET` fazendo uma chamada `/.auth/refresh` para o ponto de extremidade do seu aplicativo. Quando chamado, o serviço de aplicativo atualiza automaticamente os tokens de acesso no repositório de token para o usuário autenticado. Solicitações subsequentes de tokens pelo código do aplicativo obtêm os tokens atualizados. No entanto, para que a atualização de token funcione, o repositório de token deve conter tokens de [atualização](https://auth0.com/learn/refresh-tokens/) para seu provedor. A maneira de obter tokens de atualização é documentada por cada provedor, mas a lista a seguir é um breve resumo:

- **Google**: Acrescente um `access_type=offline` parâmetro de cadeia de caracteres `/.auth/login/google` de consulta à sua chamada à API. Se estiver usando o SDK de aplicativos móveis, você poderá adicionar o parâmetro a uma `LogicAsync` das sobrecargas (consulte tokens de [atualização do Google](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: Não fornece tokens de atualização. Tokens de vida longa expiram em 60 dias (consulte [expiração do Facebook e extensão de tokens de acesso](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: Tokens de acesso não expiram (consulte [perguntas frequentes sobre o Twitter OAuth](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Conta da Microsoft**: Ao [definir as configurações de autenticação da conta](configure-authentication-provider-microsoft.md)da `wl.offline_access` Microsoft, selecione o escopo.
- **Azure Active Directory**: No [https://resources.azure.com](https://resources.azure.com), execute as seguintes etapas:
    1. Na parte superior da página, selecione **leitura/gravação**.
    2.  > No navegador esquerdo, navegue até >  **_\<assinaturas nome da assinatura\__** resourceGroups >  **_grupode\_recursos\< nome\_>_** **provedores Microsoft. Web** **_sitesnomedo\_aplicativo > configuração\<_**  >  >  >  >   >  >  **authsettings**. 
    3. Clique em **Editar**.
    4. Modifique a propriedade a seguir. Substitua a ID do _aplicativo\_> pela ID do aplicativo Azure Active Directory do serviço que você deseja acessar. \<_

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Clique em **Put**. 

Quando o provedor estiver configurado, você poderá [encontrar o token de atualização e a hora de expiração para o token de acesso](#retrieve-tokens-in-app-code) no repositório de tokens. 

Para atualizar seu token de acesso a qualquer momento, `/.auth/refresh` basta chamar em qualquer idioma. O trecho a seguir usa o jQuery para atualizar os tokens de acesso de um cliente JavaScript.

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

Se um usuário revogar as permissões concedidas ao seu aplicativo, sua chamada `/.auth/me` para poderá falhar com `403 Forbidden` uma resposta. Para diagnosticar erros, verifique os logs do aplicativo para obter detalhes.

## <a name="extend-session-token-expiration-grace-period"></a>Estender período de carência de expiração do token de sessão

A sessão autenticada expira após 8 horas. Depois que uma sessão autenticada expira, há um período de carência de 72 horas por padrão. Dentro desse período de carência, você tem permissão para atualizar o token de sessão com o serviço de aplicativo sem reautenticar o usuário. Você pode chamar `/.auth/refresh` apenas quando o token de sessão se tornar inválido e não precisar controlar a expiração do token por conta própria. Uma vez que o período de carência de 72 horas se sobrepõe, o usuário deve entrar novamente para obter um token de sessão válido.

Se 72 horas não for tempo suficiente para você, você poderá estender essa janela de expiração. Estender a expiração por um longo período pode ter implicações de segurança significativas (como quando um token de autenticação é vazado ou roubado). Portanto, você deve deixá-lo no padrão de 72 horas ou definir o período de extensão para o menor valor.

Para estender a janela de expiração padrão, execute o seguinte comando na [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> O período de carência só se aplica à sessão autenticada do serviço de aplicativo, não aos tokens dos provedores de identidade. Não há nenhum período de carência para os tokens de provedor expirados. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Limitar o domínio de contas de entrada

Tanto a conta da Microsoft quanto a Azure Active Directory permitem que você entre em vários domínios. Por exemplo, a conta da Microsoft permite contas _Outlook.com_, _Live.com_e _hotmail.com_ . Azure Active Directory permite qualquer número de domínios personalizados para as contas de entrada. Esse comportamento pode ser indesejável para um aplicativo interno, que você não deseja que ninguém com uma conta do _Outlook.com_ acesse. Para limitar o nome de domínio das contas de entrada, siga estas etapas.

No [https://resources.azure.com](https://resources.azure.com), navegue até > assinaturas nomeda >  **_assinatura\_ resourceGroupsrecurso\<_**  >  **_\<\_ nome\_ do grupo >_**  > **provedores** **Microsoft. Web** **_sitesnome\< doaplicativo>\__**  >  >  >  >  **configuração** do  >  **authsettings**. 

Clique em **Editar**, modifique a seguinte propriedade e, em seguida, clique em **Put**. Certifique-se de  _\<substituir\_o nome de domínio >_ pelo domínio desejado.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Autenticar e autorizar os usuários tutorial de ponta a ponta](app-service-web-tutorial-auth-aad.md)(Windows)
> [: Autenticar e autorizar usuários de ponta a ponta (Linux)](containers/tutorial-auth-aad.md)
