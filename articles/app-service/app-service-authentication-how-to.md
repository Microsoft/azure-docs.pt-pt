---
title: Utilização avançada da AuthN/AuthO
description: Aprenda a personalizar a funcionalidade de autenticação e autorização no App Service para diferentes cenários e obtenha reclamações de utilizadores e fichas diferentes.
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: d57b196bf95ebdf31bc459ad4b9d718fd32ca495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280836"
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

Em Ação a tomar quando o **pedido não é autenticado,** selecione **Permitir pedidos Anónimos (sem ação)**.

Na página de sessão, ou na barra de navegação, ou em qualquer outra localização da sua`/.auth/login/<provider>`aplicação, adicione um link de sessão a cada um dos fornecedores que ativou (). Por exemplo:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Quando o utilizador clica num dos links, a respetiva página de sessão abre-se para iniciar sessão no utilizador.

Para redirecionar o utilizador pós-login para um `post_login_redirect_url` URL personalizado, utilize o parâmetro de corda de consulta (não confundir com o Redirect URI na configuração do seu fornecedor de identidade). Por exemplo, para navegar `/Home/Index` o utilizador para depois do iniciar sessão, utilize o seguinte código HTML:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Validar fichas de fornecedores

Num login dirigido pelo cliente, a aplicação assina manualmente no utilizador ao fornecedor e, em seguida, submete o símbolo de autenticação ao Serviço de Aplicações para validação (ver fluxo de [autenticação).](overview-authentication-authorization.md#authentication-flow) Esta validação em si não lhe dá acesso aos recursos de aplicações desejados, mas uma validação bem sucedida lhe dará um sinal de sessão que pode usar para aceder aos recursos da aplicação. 

Para validar o token do fornecedor, a aplicação App Service deve primeiro ser configurada com o fornecedor pretendido. No tempo de execução, depois de recuperar o símbolo de `/.auth/login/<provider>` autenticação do seu fornecedor, poste o token para validação. Por exemplo: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

O formato simbólico varia ligeiramente de acordo com o fornecedor. Consulte a tabela seguinte para mais detalhes:

| Valor do fornecedor | Obrigatório no órgão de pedido | Comentários |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | A `expires_in` propriedade é opcional. <br/>Ao solicitar o sinal dos serviços `wl.basic` ao vivo, solicite sempre o âmbito. |
| `google` | `{"id_token":"<id_token>"}` | A `authorization_code` propriedade é opcional. Quando especificado, também pode ser acompanhado `redirect_uri` opcionalmente pela propriedade. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Utilize um sinal de acesso válido [ao utilizador](https://developers.facebook.com/docs/facebook-login/access-tokens) do Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Se o token do fornecedor for validado com `authenticationToken` sucesso, a API devolve com um no corpo de resposta, que é o seu símbolo de sessão. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Assim que tiver esta ficha de sessão, pode `X-ZUMO-AUTH` aceder aos recursos de aplicações protegidos adicionando o cabeçalho aos seus pedidos HTTP. Por exemplo: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Assine fora de uma sessão

Os utilizadores podem iniciar uma `GET` inscrição enviando `/.auth/logout` um pedido para o ponto final da aplicação. O `GET` pedido faz o seguinte:

- Limpa os cookies de autenticação da sessão atual.
- Elimina as fichas do utilizador atual da loja de fichas.
- Para o Azure Ative Directory e google, realiza uma inscrição do lado do servidor no fornecedor de identidade.

Esta é uma ligação simples de fim de sessão numa página Web:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Por predefinição, um sinal de saída bem `/.auth/logout/done`sucedido redireciona o cliente para o URL . Pode alterar a página de redirecionamento `post_logout_redirect_uri` pós-inscrição adicionando o parâmetro de consulta. Por exemplo:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Recomenda-se que [codifique](https://wikipedia.org/wiki/Percent-encoding) o `post_logout_redirect_uri`valor de.

Ao utilizar URLs totalmente qualificados, o URL deve ser hospedado no mesmo domínio ou configurado como um URL externo de redirecionamento permitido para a sua aplicação. No exemplo seguinte, redirecionar `https://myexternalurl.com` para que não esteja hospedado no mesmo domínio:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Executar o seguinte comando na [Casca de Nuvem Azure:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Preservar fragmentos de URL

Depois de os utilizadores iniciarem sessão na sua aplicação, normalmente querem `/wiki/Main_Page#SectionZ`ser redirecionados para a mesma secção da mesma página, como . No entanto, uma vez `#SectionZ`que os [fragmentos](https://wikipedia.org/wiki/Fragment_identifier) de URL (por exemplo, ) nunca são enviados para o servidor, não são preservados por padrão após o login OAuth completar e redirecionar de volta para a sua aplicação. Os utilizadores obtêm então uma experiência sub-óptima quando precisam de navegar novamente para a âncora desejada. Esta limitação aplica-se a todas as soluções de autenticação do lado do servidor.

Na autenticação do Serviço de Aplicações, pode preservar fragmentos de URL através do login OAuth. Para isso, defina uma `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` `true`definição de aplicação chamada para . Pode fazê-lo no [portal Azure,](https://portal.azure.com)ou simplesmente executar o seguinte comando na [Casca de Nuvem Azure:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Aceder às reclamações dos utilizadores

O Serviço de Aplicações transmite as alegações dos utilizadores à sua aplicação utilizando cabeçalhos especiais. Os pedidos externos não são permitidos definir estes cabeçalhos, por isso só estão presentes se definidos pelo Serviço de Aplicações. Alguns cabeçalhos de exemplo incluem:

* X-MS-CLIENTE-PRINCIPAL-NAME
* X-MS-CLIENTE-PRINCIPAL-ID

O código que está escrito em qualquer língua ou enquadramento pode obter a informação de que necessita destes cabeçalhos. Para ASP.NET 4.6 aplicações, o **ClaimsPrincipal** é automaticamente definido com os valores apropriados. ASP.NET Core, no entanto, não fornece um middleware de autenticação que se integre com as alegações dos utilizadores do App Service. Para uma suposição, consulte [MaximeRouiller.Azure.AppService.EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

A sua aplicação também pode obter detalhes `/.auth/me`adicionais sobre o utilizador autenticado através da chamada . Os SDKs do servidor de aplicativos móveis fornecem métodos de ajuda para trabalhar com estes dados. Para mais informações, consulte [Como utilizar as aplicações móveis Azure Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)e [Trabalhe com o servidor de backend .NET SDK para Aplicações Móveis Azure](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

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

A partir do seu código de cliente (como uma aplicação móvel ou javaScript no navegador), envie um pedido HTTP `GET` para `/.auth/me`. A JSON devolvida tem as fichas específicas do fornecedor.

> [!NOTE]
> Os tokens de acesso são para aceder aos recursos do fornecedor, pelo que só estão presentes se configurar o seu fornecedor com um segredo de cliente. Para ver como obter fichas refrescantes, consulte fichas de acesso Refresh.

## <a name="refresh-identity-provider-tokens"></a>Atualizar fichas de fornecedor de identidade

Quando o sinal de acesso do seu fornecedor (não o token da [sessão)](#extend-session-token-expiration-grace-period)expirar, tem de reautenticar o utilizador antes de voltar a utilizar esse símbolo. Pode evitar a expiração do `GET` símbolo `/.auth/refresh` fazendo uma chamada para o ponto final da sua aplicação. Quando chamado, o Serviço de Aplicações atualiza automaticamente as fichas de acesso na loja token para o utilizador autenticado. Os pedidos subsequentes de fichas pelo código da aplicação obtêm os tokens renovados. No entanto, para que a atualização token funcione, a loja de fichas deve conter [fichas de atualização](https://auth0.com/learn/refresh-tokens/) para o seu fornecedor. A forma de obter fichas de atualização é documentada por cada fornecedor, mas a seguinte lista é um breve resumo:

- **Google**: Anexar um `access_type=offline` parâmetro de `/.auth/login/google` corda de consulta à sua chamada API. Se utilizar o SDK de Aplicações Móveis, `LogicAsync` pode adicionar o parâmetro a uma das sobrecargas (ver [Google Refresh Tokens](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: Não fornece fichas de atualização. As fichas de longa duração expiram em 60 dias (ver [Facebook Expiração e Extensão de Fichas de Acesso).](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)
- **Twitter**: As fichas de acesso não expiram (ver [Twitter OAuth FAQ](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Conta Microsoft**: Ao [configurar as definições](configure-authentication-provider-microsoft.md)de autenticação da conta microsoft, selecione o `wl.offline_access` âmbito.
- **Diretório Ativo Azure**: In, [https://resources.azure.com](https://resources.azure.com)faça os seguintes passos:
    1. No topo da página, selecione **Ler/Escrever**.
    2. No navegador esquerdo, navegue para **subscrições** > **_\<\_nome_** de grupo >  > de**recursos Os** > **_\<recursos\_de recursos\_>_**  >  **fornecedores****Microsoft.Web** > **sites** > **_\<nome de aplicação\_>_**  > **authsettings** **config** > . 
    3. Clique em **Editar**.
    4. Modificar a seguinte propriedade. Substitua _ \<\__ o id da aplicação>com o ID de aplicação azure Ative Directory do serviço a que pretende aceder.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Clique em **Colocar**. 

Uma vez configurado o seu fornecedor, pode encontrar o token de [atualização e o tempo de validade para o token](#retrieve-tokens-in-app-code) de acesso na loja de fichas. 

Para refrescar o seu sinal de `/.auth/refresh` acesso a qualquer momento, basta ligar para qualquer idioma. O seguinte snippet usa jQuery para refrescar as suas fichas de acesso de um cliente JavaScript.

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

Se um utilizador revogar as permissões concedidas `/.auth/me` à sua `403 Forbidden` aplicação, a sua chamada poderá falhar com uma resposta. Para diagnosticar erros, verifique os registos da sua aplicação para obter detalhes.

## <a name="extend-session-token-expiration-grace-period"></a>Prolongar período de carência de validade da sessão

A sessão autenticada expira após 8 horas. Após o termo de uma sessão autenticada, existe um período de carência de 72 horas por defeito. Dentro deste período de carência, é permitido refrescar a sessão com o Serviço de Aplicações sem reautenticar o utilizador. Pode ligar `/.auth/refresh` quando o seu token da sessão se tornar inválido, e não precisa de rastrear a expiração do símbolo. Uma vez que o período de carência de 72 horas é caducado, o utilizador deve iniciar sessão novamente para obter um token de sessão válido.

Se 72 horas não forem tempo suficiente para si, pode estender esta janela de validade. O alargamento da expiração por um longo período pode ter implicações significativas na segurança (como quando um token de autenticação é vazado ou roubado). Por isso, deve deixá-lo no padrão 72 horas ou definir o período de extensão para o menor valor.

Para estender a janela de validade predefinida, execute o seguinte comando na [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> O período de carência aplica-se apenas à sessão autenticada do Serviço de Aplicações, e não aos símbolos dos fornecedores de identidade. Não existe um período de carência para os tokens do fornecedor expirado. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Limitar o domínio das contas de inscrição

Tanto a Microsoft Account como o Azure Ative Directory permitem-lhe iniciar sessão a partir de vários domínios. Por exemplo, a Conta Microsoft permite _contas outlook.com_, _live.com_e _hotmail.com._ A Azure AD permite qualquer número de domínios personalizados para as contas de entrada. No entanto, pode querer acelerar os seus utilizadores diretamente para a `contoso.com`sua própria página de entrada de anúncios Azure AD (por exemplo). Para sugerir o nome de domínio das contas de inscrição, siga estes passos.

Em [https://resources.azure.com](https://resources.azure.com), navegue para **subscrições** > **_\<\_nome_** de recurso > **Recursos Grupos** > **_\<de recursos\_\_nome>_**  >  **fornecedores** > **Microsoft.Web** > **sites** > **_\<nome\_>_**  > **authsettings** **config** > . 

Clique em **Editar,** modifique a seguinte propriedade e, em seguida, clique em **Colocar**. Certifique-se _ \<de\__ substituir o nome de domínio>pelo domínio que deseja.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Esta definição adere o parâmetro de `domain_hint` corda de consulta ao URL de redirecionamento de login. 

> [!IMPORTANT]
> É possível que o cliente `domain_hint` remova o parâmetro depois de receber o URL de redirecionamento e, em seguida, iniciar sessão com um domínio diferente. Por isso, embora esta função seja conveniente, não é uma funcionalidade de segurança.
>

## <a name="authorize-or-deny-users"></a>Autorizar ou negar utilizadores

Embora o Serviço de Aplicações cuide do caso de autorização mais simples (ou seja, rejeitar pedidos não autenticados), a sua aplicação pode necessitar de um comportamento de autorização mais fino, como limitar o acesso a apenas um determinado grupo de utilizadores. Em certos casos, é necessário escrever um código de aplicação personalizado para permitir ou negar o acesso ao utilizador inscrito. Noutros casos, o Serviço de Aplicações ou o seu fornecedor de identidade poderão ajudar sem exigir alterações de código.

- [Nível de servidor](#server-level-windows-apps-only)
- [Nível de fornecedor de identidade](#identity-provider-level)
- [Nível de candidatura](#application-level)

### <a name="server-level-windows-apps-only"></a>Nível de servidor (apenas aplicações do Windows)

Para qualquer aplicação do Windows, pode definir o comportamento de autorização do servidor web IIS, editando o ficheiro *Web.config.* As aplicações Linux não usam o IIS e não podem ser configuradas através do *Web.config*.

1. Navegar para`https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. No navegador explorador dos seus ficheiros do Serviço de Aplicações, navegue para *site/wwwroot*. Se um *Web.config* não existir, crie-o selecionando **+**  >  **New File**. 

1. Selecione o lápis para *Web.config* para editá-lo. Adicione o seguinte código de configuração e clique em **Guardar**. Se *web.config* já existir, `<authorization>` basta adicionar o elemento com tudo o que estiver nele. Adicione as contas que pretende `<allow>` permitir no elemento.

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
