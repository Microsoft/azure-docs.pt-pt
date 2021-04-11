---
title: Utilização avançada da AuthN/AuthZ
description: Aprenda a personalizar a funcionalidade de autenticação e autorização no Serviço de Aplicações para diferentes cenários, e obtenha reclamações de utilizadores e fichas diferentes.
ms.topic: article
ms.date: 07/08/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: fc2916cbccc21262467533b0b497b14f4f4b941c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105034882"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Uso avançado da autenticação e autorização no Serviço de Aplicações Azure

Este artigo mostra-lhe como personalizar a autenticação e autorização incorporada [no Serviço de Aplicações](overview-authentication-authorization.md), e gerir a identidade a partir da sua aplicação. 

Para começar rapidamente, consulte um dos seguintes tutoriais:

* [Tutorial: Autenticar e autorizar utilizadores ponto a ponto no Serviço de Aplicações do Azure](tutorial-auth-aad.md)
* [Como configurar a sua aplicação para utilizar o início de sessão do Azure Active Directory](configure-authentication-provider-aad.md)
* [Como configurar a sua aplicação para utilizar o início de sessão do Facebook](configure-authentication-provider-facebook.md)
* [Como configurar a sua aplicação para utilizar o início de sessão do Google](configure-authentication-provider-google.md)
* [Como configurar a sua aplicação para utilizar o início de sessão da conta Microsoft](configure-authentication-provider-microsoft.md)
* [Como configurar a sua aplicação para utilizar o início de sessão do Twitter](configure-authentication-provider-twitter.md)
* [Como configurar a sua aplicação para iniciar sessão com um fornecedor OpenID Connect (Pré-visualização)](configure-authentication-provider-openid-connect.md)
* [Como configurar a sua app para iniciar sessão usando um Login com a Apple (Pré-visualização)](configure-authentication-provider-apple.md)

## <a name="use-multiple-sign-in-providers"></a>Utilize vários fornecedores de inscrição

A configuração do portal não oferece uma forma chave-na-mão de apresentar vários fornecedores de entrada aos seus utilizadores (como tanto o Facebook como o Twitter). No entanto, não é difícil adicionar a funcionalidade à sua app. Os passos são delineados da seguinte forma:

Em primeiro lugar, na página **Autenticação/Autorização** no portal Azure, configurar cada um dos fornecedores de identidade que pretende ativar.

Em **Ação a tomar quando o pedido não for autenticado,** selecione Permitir **pedidos anónimos (nenhuma ação)**.

Na página de iniciar s indicado, ou na barra de navegação, ou em qualquer outro local da sua aplicação, adicione um link de inscrição a cada um dos fornecedores que ativou `/.auth/login/<provider>` (). Por exemplo:

```html
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
<a href="/.auth/login/apple">Log in with Apple</a>
```

Quando o utilizador clica num dos links, a respetiva página de inscrição abre para iniciar sação no utilizador.

Para redirecionar o utilizador após o sismo para um URL personalizado, utilize o `post_login_redirect_url` parâmetro de cadeia de consulta (não deve ser confundido com o URI de redirecionamento na configuração do seu fornecedor de identidade). Por exemplo, para navegar no utilizador para `/Home/Index` depois de iniciar sindução, utilize o seguinte código HTML:

```html
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Validar fichas de fornecedores

Numa entrada dirigida ao cliente, a aplicação assina manualmente no utilizador e, em seguida, submete o token de autenticação ao Serviço de Aplicações para validação (ver [fluxo de autenticação).](overview-authentication-authorization.md#authentication-flow) Esta validação em si não lhe dá acesso aos recursos de aplicações pretendidos, mas uma validação bem sucedida lhe dará um token de sessão que você pode usar para aceder a recursos de aplicações. 

Para validar o token do fornecedor, a aplicação App Service tem primeiro de ser configurada com o fornecedor pretendido. No tempo de execução, depois de recuperar o sinal de autenticação do seu fornecedor, coloque o token `/.auth/login/<provider>` para validação. Por exemplo: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

O formato token varia ligeiramente de acordo com o fornecedor. Consulte a tabela seguinte para mais detalhes:

| Valor do fornecedor | Requerido no corpo de pedido | Comentários |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | A `expires_in` propriedade é opcional. <br/>Ao solicitar o token dos serviços Live, peça sempre o `wl.basic` âmbito. |
| `google` | `{"id_token":"<id_token>"}` | A `authorization_code` propriedade é opcional. Quando especificado, também pode opcionalmente ser acompanhado pela `redirect_uri` propriedade. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Utilize um [token](https://developers.facebook.com/docs/facebook-login/access-tokens) de acesso ao utilizador válido do Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Se o token do fornecedor for validado com sucesso, a API regressa com um `authenticationToken` no organismo de resposta, que é o seu sinal de sessão. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Assim que tiver este token de sessão, pode aceder aos recursos de aplicações protegidos adicionando o `X-ZUMO-AUTH` cabeçalho aos seus pedidos HTTP. Por exemplo: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Assine fora de uma sessão

Os utilizadores podem iniciar uma sferição enviando um `GET` pedido para o ponto final da aplicação. `/.auth/logout` O `GET` pedido faz o seguinte:

- Limpa cookies de autenticação da sessão atual.
- Elimina as fichas do utilizador atual da loja token.
- Para o Azure Ative Directory e o Google, executa uma súmia do lado do servidor no fornecedor de identidade.

Esta é uma ligação simples de fim de sessão numa página Web:

```html
<a href="/.auth/logout">Sign out</a>
```

Por predefinição, uma assinatura bem sucedida redireciona o cliente para o URL `/.auth/logout/done` . Pode alterar a página de redirecionamento pós-assinatura adicionando o `post_logout_redirect_uri` parâmetro de consulta. Por exemplo:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Recomenda-se que [codifique](https://wikipedia.org/wiki/Percent-encoding) o valor de `post_logout_redirect_uri` .

Ao utilizar URLs totalmente qualificados, o URL deve ser hospedado no mesmo domínio ou configurado como um URL de redirecionamento externo permitido para a sua aplicação. No exemplo seguinte, redirecionar para `https://myexternalurl.com` o que não está hospedado no mesmo domínio:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Executar o seguinte comando na [Azure Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Preservar fragmentos de URL

Depois de os utilizadores iniciarem seduções na sua aplicação, normalmente querem ser redirecionados para a mesma secção da mesma página, como `/wiki/Main_Page#SectionZ` . No entanto, como [os fragmentos de URL](https://wikipedia.org/wiki/Fragment_identifier) (por exemplo, ) nunca são `#SectionZ` enviados para o servidor, não são preservados por padrão após o preenérsimo de insuprova e redireciona de volta para a sua app. Em seguida, os utilizadores obtêm uma experiência sub-optimista quando precisam navegar novamente para a âncora desejada. Esta limitação aplica-se a todas as soluções de autenticação do lado do servidor.

Na autenticação do Serviço de Aplicações, pode preservar fragmentos de URL através do sign-in OAuth. Para isso, defina uma definição de aplicação `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` chamada . `true` Pode fazê-lo no [portal Azure,](https://portal.azure.com)ou simplesmente executar o seguinte comando na [Azure Cloud Shell:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Aceder às reclamações dos utilizadores

O Serviço de Aplicações transmite as reclamações dos utilizadores à sua aplicação utilizando cabeçalhos especiais. Os pedidos externos não são permitidos definir estes cabeçalhos, pelo que só estão presentes se forem definidos pelo Serviço de Aplicações. Alguns cabeçalhos de exemplo incluem:

* X-MS-CLIENTE-PRINCIPAL-NOME
* X-MS-CLIENTE-PRINCIPAL-ID

Código que está escrito em qualquer idioma ou enquadramento pode obter a informação de que precisa destes cabeçalhos. Para ASP.NET 4.6 aplicações, o **ClaimsPrincipal** é automaticamente definido com os valores apropriados. ASP.NET Core, no entanto, não fornece um middleware de autenticação que se integre com as alegações do utilizador do App Service. Para uma solução alternativa, consulte [MaximeRouiller.Azure.AppService.EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

Se a [loja de fichas](overview-authentication-authorization.md#token-store) estiver ativada para a sua aplicação, também pode obter detalhes adicionais sobre o utilizador autenticado, ligando para `/.auth/me` . Os SDKs do servidor de Aplicações Móveis fornecem métodos de ajuda para trabalhar com estes dados. Para obter mais informações, consulte [Como utilizar as aplicações móveis Azure Node.js SDK](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity), e [trabalhar com o servidor de backend SDK .NET para aplicações móveis Azure](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Recuperar fichas no código de aplicações

A partir do código do seu servidor, os tokens específicos do fornecedor são injetados no cabeçalho do pedido, para que possa aceder facilmente aos mesmos. A tabela a seguir mostra possíveis nomes de cabeçalho simbólico:

| Fornecedor | Nomes de cabeçalho |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook Token | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Conta Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

A partir do seu código cliente (como uma aplicação móvel ou javaScript no navegador), envie um pedido HTTP `GET` para `/.auth/me` [(token store](overview-authentication-authorization.md#token-store) deve estar ativado). O JSON devolvido tem as fichas específicas do fornecedor.

> [!NOTE]
> Os tokens de acesso são para aceder a recursos do fornecedor, pelo que só estão presentes se configurar o seu fornecedor com um segredo de cliente. Para ver como obter fichas refrescantes, consulte os tokens de acesso refresh.

## <a name="refresh-identity-provider-tokens"></a>Fichas de fornecedor de identidade atualizar

Quando o token de acesso do seu fornecedor (não o token da [sessão)](#extend-session-token-expiration-grace-period)expirar, tem de reautorá-lo antes de voltar a utilizar esse token. Pode evitar a expiração do token fazendo uma `GET` chamada para o ponto final da sua `/.auth/refresh` aplicação. Quando chamado, o Serviço de Aplicações atualiza automaticamente os tokens de acesso na [loja simbólica](overview-authentication-authorization.md#token-store) para o utilizador autenticado. Os pedidos subsequentes de tokens pelo seu código de aplicação recebem os tokens renovados. No entanto, para que a atualização simbólica funcione, a loja simbólica deve conter [fichas de atualização](https://auth0.com/learn/refresh-tokens/) para o seu fornecedor. A forma de obter tokens de atualização é documentada por cada fornecedor, mas a seguinte lista é um breve resumo:

- **Google**: Apeia um `access_type=offline` parâmetro de cadeia de consulta à sua chamada `/.auth/login/google` API. Se utilizar o SDK de aplicações móveis, pode adicionar o parâmetro a uma das `LogicAsync` sobrecargas (ver [Google Refresh Tokens).](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)
- **Facebook**: Não fornece fichas de atualização. As fichas de longa duração expiram em 60 dias (ver [Expiração do Facebook e Extensão de Fichas de Acesso).](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)
- **Twitter**: Os tokens de acesso não expiram (ver [Twitter OAuth FAQ](https://developer.twitter.com/en/docs/authentication/faq)).
- **Conta Microsoft**: Ao [configurar as Definições de Autenticação da Conta microsoft,](configure-authentication-provider-microsoft.md)selecione o `wl.offline_access` âmbito.
- **Diretório Ativo Azure**: Em [https://resources.azure.com](https://resources.azure.com) , faça os seguintes passos:
    1. No topo da página, selecione **Ler/Escrever.**
    2. No navegador esquerdo, navegue para **subscrições** > **_\<subscription\_name_** > **recursos Os grupos** > **_ \<resource\_group\_name> _** ** > **fornecedores**  >  **microsoft.Web**  >  **sites** > **_ \<app\_name> _** > **config**  >  **authsettings**. 
    3. Clique em **Editar**.
    4. Modifique a seguinte propriedade. _\<app\_id>_ Substitua-se pelo ID da aplicação Azure Ative Do serviço a que pretende aceder.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Clique **em Colocar**. 

Uma vez configurado o seu fornecedor, pode [encontrar o token de atualização e o tempo de validade para o token](#retrieve-tokens-in-app-code) de acesso na loja simbólica. 

Para refrescar o seu token de acesso a qualquer momento, basta ligar `/.auth/refresh` para qualquer idioma. O seguinte snippet usa jQuery para refrescar os seus tokens de acesso a um cliente JavaScript.

```javascript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Se um utilizador revogar as permissões concedidas à sua app, a sua chamada `/.auth/me` poderá falhar com uma `403 Forbidden` resposta. Para diagnosticar erros, verifique os registos da sua aplicação para obter mais detalhes.

## <a name="extend-session-token-expiration-grace-period"></a>Prolongar período de validade do símbolo da sessão

A sessão autenticada expira após 8 horas. Após o termo de uma sessão autenticada, existe um período de carência de 72 horas por defeito. Dentro deste período de carência, é permitido atualizar o token da sessão com o Serviço de Aplicações sem reuthentar o utilizador. Pode ligar `/.auth/refresh` quando o seu token de sessão se tornar inválido, e não precisa de rastrear a expiração do token. Uma vez que o período de carência de 72 horas é caducado, o utilizador deve fazer o acordo novamente para obter um token de sessão válido.

Se 72 horas não for tempo suficiente para si, pode estender esta janela de validade. Prolongar a expiração durante um longo período pode ter implicações significativas na segurança (como quando um token de autenticação é vazado ou roubado). Por isso, deve deixá-lo no predefinido 72 horas ou definir o período de extensão para o menor valor.

Para prolongar a janela de validade predefinida, executar o seguinte comando na [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> O período de carência aplica-se apenas à sessão autenticada do Serviço de Aplicações, e não aos tokens dos fornecedores de identidade. Não existe um período de carência para as fichas de validade do fornecedor. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Limitar o domínio das contas de inscrição

Tanto a Microsoft Account como o Azure Ative Directory permitem iniciar seduções a partir de vários domínios. Por exemplo, a Microsoft Account permite _outlook.com_, _live.com_ e _contas hotmail.com._ O Azure AD permite qualquer número de domínios personalizados para as contas de inscrição. No entanto, é possível que queira acelerar os seus utilizadores diretamente para a sua própria página de inscrição Azure AD (tal `contoso.com` como). Para sugerir o nome de domínio das contas de inscrição, siga estes passos.

Em [https://resources.azure.com](https://resources.azure.com) , navegar para **subscrições** > **_\<subscription\_name_** > **recursos Grupos** > ** **_ \<resource\_group\_name> _> **fornecedores**  >  **Microsoft.Web**  >  **sites** > **_ \<app\_name> _** > **config**  >  **authsettings**. 

Clique **em Editar,** modifique a seguinte propriedade e, em seguida, clique em **Colocar**. Certifique-se de que substitui _\<domain\_name>_ pelo domínio que deseja.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Esta definição anexa o parâmetro da `domain_hint` cadeia de consulta ao URL de redirecionamento de login. 

> [!IMPORTANT]
> É possível para o cliente remover o `domain_hint` parâmetro depois de receber o URL de redirecionamento e, em seguida, iniciar sessão com um domínio diferente. Portanto, embora esta função seja conveniente, não é uma característica de segurança.
>

## <a name="authorize-or-deny-users"></a>Autorizar ou negar utilizadores

Embora o Serviço de Aplicações cuide do caso de autorização mais simples (ou seja, rejeite pedidos não autenticados), a sua aplicação pode exigir um comportamento de autorização mais fino, como limitar o acesso a apenas um grupo específico de utilizadores. Em certos casos, é necessário escrever código de aplicação personalizado para permitir ou negar o acesso ao utilizador inscrito. Noutros casos, o Serviço de Aplicações ou o seu fornecedor de identidade poderão ajudar sem exigir alterações de código.

- [Nível de servidor](#server-level-windows-apps-only)
- [Nível de fornecedor de identidade](#identity-provider-level)
- [Nível de aplicação](#application-level)

### <a name="server-level-windows-apps-only"></a>Nível de servidor (apenas aplicações do Windows)

Para qualquer aplicação do Windows, pode definir o comportamento de autorização do servidor web IIS, editando o ficheiro *Web.config.* As aplicações Linux não usam IIS e não podem ser configuradas através *deWeb.config*.

1. Navegue para `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. No navegador explorador dos seus ficheiros de Serviço de Aplicações, navegue para *o site/wwwroot*. Se não existir uma *Web.config,* crie-a selecionando **+**  >  **Novo Ficheiro**. 

1. Selecione o lápis para *Web.config* para editá-lo. Adicione o seguinte código de configuração e clique em **Guardar**. Se *Web.config* já existe, basta adicionar o `<authorization>` elemento com tudo o que está nele. Adicione as contas que pretende permitir no `<allow>` elemento.

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

O fornecedor de identidade pode fornecer determinada autorização chave-na-curva. Por exemplo:

- Para [o Azure App Service,](configure-authentication-provider-aad.md)pode gerir o acesso a [nível empresarial](../active-directory/manage-apps/what-is-access-management.md) diretamente no Azure AD. Para obter instruções, consulte [como remover o acesso de um utilizador a uma aplicação](../active-directory/manage-apps/methods-for-removing-user-access.md).
- Para [o Google](configure-authentication-provider-google.md), os projetos da Google API que pertencem a uma [organização](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) podem ser configurados para permitir o acesso apenas aos utilizadores na sua organização (ver página de [suporte **OAuth 2.0** da Google).](https://support.google.com/cloud/answer/6158849?hl=en)

### <a name="application-level"></a>Nível de aplicação

Se algum dos outros níveis não fornecer a autorização de que necessita, ou se a sua plataforma ou fornecedor de identidade não for suportado, deve escrever código personalizado para autorizar os utilizadores com base nas alegações do [utilizador.](#access-user-claims)

## <a name="updating-the-configuration-version-preview"></a>Atualizar a versão de configuração (pré-visualização)

Existem duas versões da API de gestão para a funcionalidade autenticação/Autorização. A versão V2 de pré-visualização é necessária para a experiência "Autenticação (pré-visualização)" no portal Azure. Uma aplicação que já utiliza a API V1 pode fazer upgrade para a versão V2 uma vez que algumas alterações tenham sido feitas. Especificamente, a configuração secreta deve ser movida para as definições de aplicações pegajosas. A configuração do fornecedor de Conta Microsoft também não é suportada atualmente em V2.

> [!WARNING]
> A migração para a pré-visualização V2 irá desativar a gestão da funcionalidade de Autenticação/Autorização do Serviço de Aplicações para a sua aplicação através de alguns clientes, como a sua experiência existente no portal Azure, Azure CLI e Azure PowerShell. Isto não pode ser invertido. Durante a pré-visualização, a migração das cargas de trabalho de produção não é encorajada ou apoiada. Só deve seguir os passos desta secção para aplicações de teste.

### <a name="moving-secrets-to-application-settings"></a>Movendo segredos para configurações de aplicações

1. Obtenha a sua configuração existente utilizando a API V1:

   ```azurecli
   # For Web Apps
   az webapp auth show -g <group_name> -n <site_name>

   # For Azure Functions
   az functionapp auth show -g <group_name> -n <site_name>
   ```

   Na carga útil JSON resultante, tome nota do valor secreto utilizado para cada fornecedor que configura:

   * AAD: `clientSecret`
   * Google: `googleClientSecret`
   * Facebook: `facebookAppSecret`
   * Twitter: `twitterConsumerSecret`
   * Conta Microsoft: `microsoftAccountClientSecret`

   > [!IMPORTANT]
   > Os valores secretos são importantes credenciais de segurança e devem ser tratados cuidadosamente. Não partilhe estes valores nem os persista numa máquina local.

1. Crie configurações de aplicações pegajosas para cada valor secreto. Pode escolher o nome de cada definição de candidatura. O seu valor deve corresponder ao que obteve no passo anterior ou [referenciar um segredo do Cofre chave](./app-service-key-vault-references.md?toc=/azure/azure-functions/toc.json) que criou com esse valor.

   Para criar a definição, pode utilizar o portal Azure ou executar uma variação do seguinte para cada fornecedor:

   ```azurecli
   # For Web Apps, Google example    
   az webapp config appsettings set -g <group_name> -n <site_name> --slot-settings GOOGLE_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>

   # For Azure Functions, Twitter example
   az functionapp config appsettings set -g <group_name> -n <site_name> --slot-settings TWITTER_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>
   ```

   > [!NOTE]
   > As definições de aplicação para esta configuração devem ser marcadas como adesivos de slot, o que significa que não se movem entre ambientes durante uma [operação de troca de faixas horárias](./deploy-staging-slots.md). Isto porque a sua configuração de autenticação em si está ligada ao ambiente. 

1. Crie um novo ficheiro JSON chamado `authsettings.json` . Pegue na saída que recebeu anteriormente e remova cada valor secreto daí resultante. Escreva a saída restante para o ficheiro, certificando-se de que nenhum segredo está incluído. Em alguns casos, a configuração pode ter matrizes que contenham cordas vazias. Certifique-se de que `microsoftAccountOAuthScopes` isso não, e se o fizer, mude esse valor para `null` .

1. Adicione um imóvel ao `authsettings.json` qual aponta para o nome de definição de aplicação que criou anteriormente para cada fornecedor:
 
   * AAD: `clientSecretSettingName`
   * Google: `googleClientSecretSettingName`
   * Facebook: `facebookAppSecretSettingName`
   * Twitter: `twitterConsumerSecretSettingName`
   * Conta Microsoft: `microsoftAccountClientSecretSettingName`

   Um ficheiro de exemplo após esta operação pode parecer semelhante ao seguinte, neste caso apenas configurado para AAD:

   ```json
   {
       "id": "/subscriptions/00d563f8-5b89-4c6a-bcec-c1b9f6d607e0/resourceGroups/myresourcegroup/providers/Microsoft.Web/sites/mywebapp/config/authsettings",
       "name": "authsettings",
       "type": "Microsoft.Web/sites/config",
       "location": "Central US",
       "properties": {
           "enabled": true,
           "runtimeVersion": "~1",
           "unauthenticatedClientAction": "AllowAnonymous",
           "tokenStoreEnabled": true,
           "allowedExternalRedirectUrls": null,
           "defaultProvider": "AzureActiveDirectory",
           "clientId": "3197c8ed-2470-480a-8fae-58c25558ac9b",
           "clientSecret": "",
           "clientSecretSettingName": "MICROSOFT_IDENTITY_AUTHENTICATION_SECRET",
           "clientSecretCertificateThumbprint": null,
           "issuer": "https://sts.windows.net/0b2ef922-672a-4707-9643-9a5726eec524/",
           "allowedAudiences": [
               "https://mywebapp.azurewebsites.net"
           ],
           "additionalLoginParams": null,
           "isAadAutoProvisioned": true,
           "aadClaimsAuthorization": null,
           "googleClientId": null,
           "googleClientSecret": null,
           "googleClientSecretSettingName": null,
           "googleOAuthScopes": null,
           "facebookAppId": null,
           "facebookAppSecret": null,
           "facebookAppSecretSettingName": null,
           "facebookOAuthScopes": null,
           "gitHubClientId": null,
           "gitHubClientSecret": null,
           "gitHubClientSecretSettingName": null,
           "gitHubOAuthScopes": null,
           "twitterConsumerKey": null,
           "twitterConsumerSecret": null,
           "twitterConsumerSecretSettingName": null,
           "microsoftAccountClientId": null,
           "microsoftAccountClientSecret": null,
           "microsoftAccountClientSecretSettingName": null,
           "microsoftAccountOAuthScopes": null,
           "isAuthFromFile": "false"
       }   
   }
   ```

1. Submeta este ficheiro como a nova configuração de Autenticação/Autorização para a sua aplicação:

   ```azurecli
   az rest --method PUT --url "/subscriptions/<subscription_id>/resourceGroups/<group_name>/providers/Microsoft.Web/sites/<site_name>/config/authsettings?api-version=2020-06-01" --body @./authsettings.json
   ```

1. Valide que a sua aplicação ainda está a funcionar como esperado após este gesto.

1. Elimine o ficheiro utilizado nos passos anteriores.

Já emigrou a app para armazenar segredos de fornecedor de identidade como configurações de aplicações.

### <a name="support-for-microsoft-account-registrations"></a>Suporte para registos de conta microsoft

A API V2 não suporta atualmente a Microsoft Account como um fornecedor distinto. Em vez disso, aproveita a plataforma de identidade da [Microsoft](../active-directory/develop/v2-overview.md) convergida para utilizadores que se inscrevam com contas pessoais da Microsoft. Ao mudar para a API V2, a configuração V1 Azure Ative Directory é utilizada para configurar o fornecedor da Plataforma de Identidade da Microsoft.

Se a sua configuração existente contiver um fornecedor de Conta Microsoft e não contiver um fornecedor de Diretório Azure Ative, pode mudar a configuração para o fornecedor de Diretório Ativo Azure e, em seguida, executar a migração. Para efetuar este procedimento:

1. Vá às [**inscrições**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) da App no portal Azure e encontre o registo associado ao seu fornecedor de Conta Microsoft. Pode estar na rubrica "Aplicações de conta pessoal".
1. Navegue na página "Autenticação" para a inscrição. Em "Redirecionar URIs" deverá ver uma entrada terminando em `/.auth/login/microsoftaccount/callback` . Copie este URI.
1. Adicione um novo URI que corresponda ao que acabou de copiar, exceto que, em vez disso, o tenha acabado `/.auth/login/aad/callback` em . Isto permitirá que o registo seja utilizado pela configuração de Autenticação/Autorização do Serviço de Aplicações.
1. Navegue para a configuração de Autenticação/Autorização do Serviço de Aplicações para a sua aplicação.
1. Colete a configuração para o fornecedor de Conta Microsoft.
1. Configure o fornecedor de Diretório Ativo Azure utilizando o modo de gestão "Avançado", fornecendo o ID do cliente e os valores secretos do cliente que recolheu no passo anterior. Para o URL emitente, utilize- o , `<authentication-endpoint>/<tenant-id>/v2.0` e *\<authentication-endpoint>* substitua-o pelo ponto final de [autenticação para](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) o seu ambiente em nuvem (por exemplo, https://login.microsoftonline.com " para Azure global), substituindo também *\<tenant-id>* o seu **ID do Diretório (inquilino).**
1. Depois de ter guardado a configuração, teste o fluxo de login navegando no seu navegador até ao `/.auth/login/aad` ponto final do seu site e complete o fluxo de entrada.
1. Neste momento, copiou com sucesso a configuração, mas a configuração do fornecedor de Conta microsoft existente permanece. Antes de o remover, certifique-se de que todas as partes da sua aplicação referenciam o fornecedor Azure Ative Directory através de links de login, etc. Verifique se todas as partes da sua aplicação funcionam como esperado.
1. Depois de ter validado que as coisas funcionam contra o fornecedor de Diretório AAD Azure Ative, poderá remover a configuração do fornecedor de conta da Microsoft.

Algumas aplicações podem já ter registos separados para o Azure Ative Directory e para a Microsoft Account. Estas aplicações não podem ser migradas neste momento. 

> [!WARNING]
> É possível convergir os dois registos modificando os [tipos de conta suportados](../active-directory/develop/supported-accounts-validation.md) para o registo de aplicações AAD. No entanto, isto forçaria um novo consentimento para os utilizadores da Microsoft Account, e as alegações de identidade desses utilizadores podem ser diferentes na estrutura, `sub` nomeadamente alterando valores uma vez que um novo ID da App está a ser utilizado. Esta abordagem não é recomendada a menos que seja compreendida minuciosamente. Em vez disso, deve aguardar o apoio para as duas inscrições na superfície da API V2.

### <a name="switching-to-v2"></a>Mudar para V2

Uma vez realizados os passos acima, navegue para a aplicação no portal Azure. Selecione a secção "Autenticação (pré-visualização)". 

Em alternativa, pode fazer um pedido PUT contra o `config/authsettingsv2` recurso sob o recurso do site. O esquema para a carga útil é o mesmo que capturado no [Configure usando uma](#config-file) secção de ficheiro.

## <a name="configure-using-a-file-preview"></a><a name="config-file"> </a>Configure usando um ficheiro (pré-visualização)

As definições de auth podem ser configuradas opcionalmente através de um ficheiro fornecido pela sua implementação. Isto pode ser exigido por certas capacidades de pré-visualização de Autenticação/Autorização do Serviço de Aplicações.

> [!IMPORTANT]
> Lembre-se de que a sua carga útil da aplicação, e portanto este ficheiro, pode mover-se entre ambientes, como nas [faixas horárias](./deploy-staging-slots.md). É provável que pretenda um registo de aplicações diferente fixado a cada ranhura e, nestes casos, deverá continuar a utilizar o método de configuração padrão em vez de utilizar o ficheiro de configuração.

### <a name="enabling-file-based-configuration"></a>Habilitação de configuração baseada em ficheiros

> [!CAUTION]
> Durante a pré-visualização, a configuração baseada em ficheiros irá desativar a gestão da funcionalidade de Autenticação/Autorização do Serviço de Aplicações para a sua aplicação através de alguns clientes, como o portal Azure, Azure CLI e Azure PowerShell.

1. Crie um novo ficheiro JSON para a sua configuração na raiz do seu projeto (implantado em D:\home\site\wwwroot na sua web/app de função). Preencha a configuração desejada de acordo com a [referência de configuração baseada em ficheiros](#configuration-file-reference). Se modificar uma configuração existente do Gestor de Recursos Azure, certifique-se de traduzir as propriedades capturadas na coleção para o `authsettings` seu ficheiro de configuração.

2. Modifique a configuração existente, que é capturada nas APIs [do Gestor de Recursos Azure](../azure-resource-manager/management/overview.md) em `Microsoft.Web/sites/<siteName>/config/authsettings` . Para modificar isto, pode utilizar um [modelo de Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md) ou uma ferramenta como o [Azure Resource Explorer](https://resources.azure.com/). Dentro da coleção de authsettings, você precisará definir três propriedades (e pode remover outras):

    1.  Definido `enabled` para "verdadeiro"
    2.  Definido `isAuthFromFile` para "verdadeiro"
    3.  Definir `authFilePath` o nome do ficheiro (por exemplo, "auth.jsligado")

> [!NOTE]
> O formato para `authFilePath` varia entre plataformas. No Windows, os caminhos relativos e absolutos são suportados. O parente é recomendado. Para o Linux, apenas os caminhos absolutos são suportados atualmente, pelo que o valor da definição deve ser "/home/site/wwwroot/auth.json" ou similar.

Uma vez então então então a atualização de configuração, o conteúdo do ficheiro será utilizado para definir o comportamento da Autenticação/Autorização do Serviço de Aplicações para esse site. Se alguma vez desejar voltar à configuração do Gestor de Recursos Azure, pode fazê-lo `isAuthFromFile` definindo de volta para "falso".

### <a name="configuration-file-reference"></a>Referência de ficheiro de configuração

Quaisquer segredos que serão referenciados a partir do seu ficheiro de configuração devem ser armazenados como [configurações de aplicação](./configure-common.md#configure-app-settings). Pode nomear as definições para o que desejar. Certifique-se apenas de que as referências do ficheiro de configuração utilizam as mesmas teclas.

Os seguintes esgotam possíveis opções de configuração dentro do ficheiro:

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|Return401|Return403",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "httpSettings": {
        "requireHttps": <true|false>,
        "routes": {
            "apiPrefix": "<api prefix>"
        },
        "forwardProxy": {
            "convention": "NoProxy|Standard|Custom",
            "customHostHeaderName": "<host header value>",
            "customProtoHeaderName": "<proto header value>"
        }
    },
    "login": {
        "routes": {
            "logoutEndpoint": "<logout endpoint>"
        },
        "tokenStore": {
            "enabled": <true|false>,
            "tokenRefreshExtensionHours": "<double>",
            "fileSystem": {
                "directory": "<directory to store the tokens in if using a file system token store (default)>"
            },
            "azureBlobStorage": {
                "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
            }
        },
        "preserveUrlFragmentsForLogins": <true|false>,
        "allowedExternalRedirectUrls": [
            "https://uri1.azurewebsites.net/",
            "https://uri2.azurewebsites.net/",
            "url_scheme_of_your_app://easyauth.callback"
        ],
        "cookieExpiration": {
            "convention": "FixedTime|IdentityDerived",
            "timeToExpiration": "<timespan>"
        },
        "nonce": {
            "validateNonce": <true|false>,
            "nonceExpirationInterval": "<timespan>"
        }
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "public_profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "apple": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "openIdConnectProviders": {
            "<providerName>": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "clientSecretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scopes": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        }
    }
}
```

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>Pin a sua app a uma versão específica do tempo de execução da autenticação

Quando ativa a autenticação /Autorização, o middleware da plataforma é injetado no seu pipeline de pedido HTTP, conforme descrito na [visão geral](overview-authentication-authorization.md#how-it-works)da funcionalidade . Este middleware da plataforma é periodicamente atualizado com novas funcionalidades e melhorias como parte das atualizações de plataforma de rotina. Por predefinição, a sua web ou aplicação de função será executada na versão mais recente desta plataforma de middleware. Estas atualizações automáticas são sempre compatíveis com o retro-costas. No entanto, no caso raro de esta atualização automática introduzir um problema de tempo de execução para a sua web ou aplicação de função, pode voltar temporariamente para a versão anterior do middleware. Este artigo explica como fixar temporariamente uma aplicação a uma versão específica do middleware de autenticação.

### <a name="automatic-and-manual-version-updates"></a>Atualizações automáticas e manuais da versão 

Pode fixar a sua aplicação numa versão específica do middleware da plataforma, definindo uma `runtimeVersion` definição para a aplicação. A sua aplicação é sempre executado na versão mais recente, a menos que opte por fixá-la explicitamente numa versão específica. Haverá algumas versões suportadas de cada vez. Se fixar numa versão inválida que já não seja suportada, a sua aplicação utilizará a versão mais recente. Para executar sempre a versão mais recente, definido `runtimeVersion` para ~1. 

### <a name="view-and-update-the-current-runtime-version"></a>Ver e atualizar a versão atual do tempo de execução

Pode alterar a versão de tempo de execução utilizada pela sua aplicação. A nova versão runtime deverá entrar em vigor depois de reiniciar a aplicação. 

#### <a name="view-the-current-runtime-version"></a>Ver a versão atual do tempo de execução

Pode ver a versão atual do middleware de autenticação da plataforma, utilizando o CLI Azure ou através de um dos pontos finais HTTP da versão incorporada na sua aplicação.

##### <a name="from-the-azure-cli"></a>Do Azure CLI

Utilizando o Azure CLI, veja a versão atual do middleware com o comando [az webapp auth show.](/cli/azure/webapp/auth#az-webapp-auth-show)

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

Neste código, `<my_app_name>` substitua-o pelo nome da sua aplicação. Substitua também `<my_resource_group>` o nome do grupo de recursos para a sua aplicação.

Verá o `runtimeVersion` campo na saída do CLI. Assemelhar-se-á à seguinte produção de exemplo, que foi truncada para maior clareza: 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>A partir do ponto final da versão

Também pode acertar no ponto final /.auth/versão numa aplicação também para ver a versão atual do middleware em que a aplicação está a funcionar. Assemelhar-se-á à seguinte saída de exemplo:
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>Atualizar a versão atual do tempo de execução

Utilizando o Azure CLI, pode atualizar a `runtimeVersion` definição na aplicação com o comando [de atualização auth auth da webapp.](/cli/azure/webapp/auth#az-webapp-auth-update)

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

`<my_app_name>`Substitua-o pelo nome da sua aplicação. Substitua também `<my_resource_group>` o nome do grupo de recursos para a sua aplicação. Além disso, `<version>` substitua por uma versão válida do tempo de execução 1.x ou `~1` pela versão mais recente. Pode encontrar as notas de lançamento nas diferentes versões de tempo de execução [aqui] ( https://github.com/Azure/app-service-announcements) para ajudar a determinar a versão a fixar.

Pode executar este comando a partir da [Azure Cloud Shell](../cloud-shell/overview.md) escolhendo-o na amostra de código anterior.  Também pode utilizar o [Azure CLI localmente](/cli/azure/install-azure-cli) para executar este comando depois de executar [o login az](/cli/azure/reference-index#az-login) para iniciar sessão.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Autenticar e autorizar utilizadores ponto a ponto](tutorial-auth-aad.md)
