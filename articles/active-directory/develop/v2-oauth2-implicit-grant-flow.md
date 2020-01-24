---
title: Fluxo de concessão implícita do OAuth 2,0 – plataforma de identidade da Microsoft | Azure
description: Proteger aplicativos de página única usando o fluxo implícito da plataforma de identidade da Microsoft.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 42d315b44a76e79d6f1db48e5024094099564a98
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76700486"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Plataforma de identidade da Microsoft e fluxo de concessão implícita

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Com o ponto de extremidade da plataforma Microsoft Identity, você pode conectar os usuários em seus aplicativos de página única com contas pessoais e corporativas ou de estudante da Microsoft. Uma página única e outros aplicativos JavaScript que são executados principalmente em um navegador enfrentam alguns desafios interessantes quando se trata de autenticação:

* As características de segurança desses aplicativos são significativamente diferentes dos aplicativos Web tradicionais baseados em servidor.
* Muitos servidores de autorização e provedores de identidade não dão suporte a solicitações CORS.
* O navegador de página inteira redireciona para fora do aplicativo se torna particularmente invasivo para a experiência do usuário.

Para esses aplicativos (AngularJS, Ember. js, reajam. js e assim por diante), a plataforma de identidade da Microsoft dá suporte ao fluxo de concessão implícita do OAuth 2,0. O fluxo implícito é descrito na [especificação do OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-4.2). Seu principal benefício é que ele permite que o aplicativo obtenha tokens da plataforma de identidade da Microsoft sem executar uma troca de credenciais de servidor de back-end. Isso permite que o aplicativo entre no usuário, mantenha a sessão e obtenha tokens para outras APIs Web no código JavaScript do cliente. Há algumas considerações de segurança importantes a serem levadas em conta ao usar o fluxo implícito especificamente em relação à representação do [cliente](https://tools.ietf.org/html/rfc6749#section-10.3) e do [usuário](https://tools.ietf.org/html/rfc6749#section-10.3).

Este artigo descreve como programar diretamente em relação ao protocolo em seu aplicativo.  Quando possível, recomendamos que você use as MSAL (bibliotecas de autenticação da Microsoft) com suporte em vez de [adquirir tokens e chamar APIs da Web protegidas](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Veja também os [aplicativos de exemplo que usam MSAL](sample-v2-code.md).

No entanto, se você preferir não usar uma biblioteca em seu aplicativo de página única e enviar mensagens de protocolo por conta própria, siga as etapas gerais abaixo.

> [!NOTE]
> Nem todos os cenários e recursos do Azure Active Directory (AD do Azure) são suportados pelo ponto de extremidade da plataforma de identidade da Microsoft. Para determinar se você deve usar o ponto de extremidade da plataforma de identidade da Microsoft, leia sobre as [limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama a seguir mostra a aparência de todo o fluxo de entrada implícito e as seções a seguir descrevem cada etapa mais detalhadamente.

![Diagrama mostrando o fluxo de entrada implícito](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Enviar a solicitação de entrada

Para inicialmente assinar o utilizador na sua aplicação, pode enviar um pedido de autenticação [OpenID Connect](v2-protocols-oidc.md) e obter uma `id_token` a partir do ponto final da plataforma de identidade da Microsoft.

> [!IMPORTANT]
> Para solicitar um token de ID e/ou um token de acesso com êxito, o registro do aplicativo na página de [portal do Azure registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) deve ter o fluxo de concessão implícito correspondente habilitado, selecionando **tokens de ID** e. ou **tokens de acesso** na seção **concessão implícita** . Se não estiver ativado, será devolvido um erro `unsupported_response`: O valor fornecido para o parâmetro de **entrada 'response_type' não é permitido para este cliente. Valor esperado é 'código'**

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> Para testar a assinatura utilizando o fluxo implícito, clique em <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Após a sua inscrição, o seu navegador deve ser redirecionado para `https://localhost/myapp/` com um `id_token` na barra de endereços.
>

| Parâmetro |  | Descrição |
| --- | --- | --- |
| `tenant` | required |O valor de `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `client_id` | required | A ID do aplicativo (cliente) que a página de [portal do Azure registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribuída ao seu aplicativo. |
| `response_type` | required |Deve incluir `id_token` para o acesso ao OpenID Connect. Pode também incluir a response_type `token`. Usar `token` aqui permitirá que seu aplicativo receba um token de acesso imediatamente do ponto de extremidade de autorização sem precisar fazer uma segunda solicitação para o ponto de extremidade de autorização. Se utilizar o `token` response_type, o parâmetro `scope` deve conter um âmbito que indique para que recurso emitir o símbolo (por exemplo, user.read no Microsoft Graph).  |
| `redirect_uri` | recomendado |O redirect_uri da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a uma das redirect_uris que registou no portal, exceto que deve ser codificada. |
| `scope` | required |Uma lista de [escopos](v2-permissions-and-consent.md)separados por espaços. Para o OpenID Connect (id_tokens), deve incluir o âmbito `openid`, que se traduz na permissão "Iniciar sessão" na UI de consentimento. Opcionalmente, talvez você também queira incluir os escopos `email` e `profile` para obter acesso a dados de usuário adicionais. Você também pode incluir outros escopos nesta solicitação para solicitar o consentimento para vários recursos, se um token de acesso for solicitado. |
| `response_mode` | opcional |Especifica o método que deve ser usado para enviar o token resultante de volta ao seu aplicativo. Incumprimentos à consulta por apenas um sinal de acesso, mas fragmentado se o pedido incluir um id_token. |
| `state` | recomendado |Um valor incluído na solicitação que também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo que você desejar. Um valor exclusivo gerado aleatoriamente geralmente é usado para [impedir ataques de solicitação entre sites forjado](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que eles estavam. |
| `nonce` | required |Um valor incluído no pedido, gerado pela app, que será incluído na id_token resultante como reivindicação. O aplicativo pode, então, verificar esse valor para atenuar os ataques de reprodução de token. O valor normalmente é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. Só é necessário quando é solicitado um id_token. |
| `prompt` | opcional |Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos neste momento são 'login', 'nenhum', 'select_account' e 'consentimento'. `prompt=login` forçará o usuário a inserir suas credenciais nessa solicitação, negando o logon único. `prompt=none` é o oposto, ele garantirá que o usuário não seja apresentado a nenhum prompt interativo. Se a solicitação não puder ser concluída silenciosamente por meio de logon único, o ponto de extremidade da plataforma de identidade da Microsoft retornará um erro. `prompt=select_account` envia o utilizador para um apanhador de conta onde todas as contas lembradas na sessão aparecerão. `prompt=consent` disparará a caixa de diálogo de consentimento do OAuth depois que o usuário fizer logon, solicitando que o usuário conceda permissões ao aplicativo. |
| `login_hint`  |opcional |Pode ser usado para preencher previamente o campo nome de usuário/endereço de email da página de entrada do usuário, se você souber seu nome de usuário antes do tempo. Muitas vezes as aplicações usam este parâmetro durante a reautenticação, tendo já extraído o nome de utilizador de um início de sessão anterior utilizando a `preferred_username` reivindicação.|
| `domain_hint` | opcional |Se for incluído, ele ignorará o processo de descoberta baseado em email que o usuário passa na página de entrada, levando a uma experiência de usuário um pouco mais simplificada. Isso é normalmente usado para aplicativos de linha de negócios que operam em um único locatário, no qual eles fornecerão um nome de domínio dentro de um determinado locatário.  Isso encaminhará o usuário para o provedor de Federação para esse locatário.  Observe que isso impedirá convidados de entrar neste aplicativo.  |

Neste ponto, o usuário será solicitado a inserir suas credenciais e concluir a autenticação. O ponto de extremidade da plataforma Microsoft Identity também garantirá que o usuário tenha consentido as permissões indicadas no parâmetro de consulta `scope`. Se o usuário tiver consentido **nenhuma** dessas permissões, ele solicitará que o usuário consenti as permissões necessárias. Para obter mais informações, consulte [permissões, consentimento e aplicativos de vários locatários](v2-permissions-and-consent.md).

Assim que o utilizador autenticar e conceder o consentimento, o ponto final da plataforma de identidade da Microsoft devolverá uma resposta à sua aplicação no `redirect_uri`indicado, utilizando o método especificado no parâmetro `response_mode`.

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem sucedida usando `response_mode=fragment` e `response_type=id_token+token` se parece com o seguinte (com quebras de linha para a legibilidade):

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| `access_token` |Incluído se `response_type` inclui `token`. O token de acesso solicitado pelo aplicativo. O token de acesso não deve ser decodificado ou inspecionado de outra forma, ele deve ser tratado como uma cadeia de caracteres opaca. |
| `token_type` |Incluído se `response_type` inclui `token`. Sempre será `Bearer`. |
| `expires_in`|Incluído se `response_type` inclui `token`. Indica o número de segundos que o token é válido, para fins de cache. |
| `scope` |Incluído se `response_type` inclui `token`. Indica o âmbito(s) para o qual o access_token será válido. Pode não incluir todos os escopos solicitados, se eles não forem aplicáveis ao usuário (no caso de escopos somente AD do Azure serem solicitados quando uma conta pessoal for usada para fazer logon). |
| `id_token` | Um JWT (token Web JSON) assinado. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que se conectou. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. Para mais informações sobre id_tokens, consulte a [`id_token reference`](id-tokens.md). <br> **Observação:** Fornecido somente se `openid` escopo foi solicitado. |
| `state` |Se um parâmetro de estado for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas para o `redirect_uri` para que a aplicação possa manuseá-las adequadamente:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| `error` |Uma cadeia de caracteres de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| `error_description` |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Obtendo tokens de acesso silenciosamente em segundo plano

Agora que você assinou o usuário em seu aplicativo de página única, você pode obter silenciosamente tokens de acesso para chamar APIs da Web protegidas pela plataforma de identidade da Microsoft, como o [Microsoft Graph](https://developer.microsoft.com/graph). Mesmo que já tenha recebido um símbolo usando o response_type `token`, pode usar este método para adquirir fichas para recursos adicionais sem ter que redirecionar o utilizador para iniciar sessão novamente.

No fluxo normal do OpenID Connect/OAuth, você faria isso fazendo uma solicitação para o ponto de extremidade de `/token` da plataforma de identidade da Microsoft. No entanto, o ponto de extremidade da plataforma Microsoft Identity não oferece suporte a solicitações CORS, portanto, fazer chamadas AJAX para obter e atualizar tokens está fora da pergunta. Em vez disso, você pode usar o fluxo implícito em um iframe oculto para obter novos tokens para outras APIs da Web: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345
&nonce=678910
&prompt=none
&login_hint=myuser@mycompany.com
```

Para obter detalhes sobre os parâmetros de consulta na URL, consulte [enviar a solicitação de entrada](#send-the-sign-in-request).

> [!TIP]
> Tente copiar & colar a solicitação abaixo em uma guia do navegador! (Não se esqueça de substituir os valores `login_hint` pelo valor correto para o utilizador)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

Graças ao parâmetro `prompt=none`, essa solicitação será bem-sucedida ou falhará imediatamente e retornará ao seu aplicativo. Uma resposta bem sucedida será enviada para a sua aplicação no `redirect_uri`indicado, utilizando o método especificado no parâmetro `response_mode`.

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem sucedida usando `response_mode=fragment` parece:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parâmetro | Descrição |
| --- | --- |
| `access_token` |Incluído se `response_type` inclui `token`. O token de acesso que o aplicativo solicitou, neste caso, para o Microsoft Graph. O token de acesso não deve ser decodificado ou inspecionado de outra forma, ele deve ser tratado como uma cadeia de caracteres opaca. |
| `token_type` | Sempre será `Bearer`. |
| `expires_in` | Indica o número de segundos que o token é válido, para fins de cache. |
| `scope` | Indica o âmbito(s) para o qual o access_token será válido. Pode não incluir todos os escopos solicitados, se eles não forem aplicáveis ao usuário (no caso de escopos somente AD do Azure serem solicitados quando uma conta pessoal for usada para fazer logon). |
| `id_token` | Um JWT (token Web JSON) assinado. Incluído se `response_type` inclui `id_token`. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que se conectou. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. Para mais informações sobre id_tokens, consulte a [referência`id_token`.](id-tokens.md) <br> **Observação:** Fornecido somente se `openid` escopo foi solicitado. |
| `state` |Se um parâmetro de estado for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas para o `redirect_uri` para que a aplicação possa manuseá-las adequadamente. No caso do `prompt=none`, um erro esperado será:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | Descrição |
| --- | --- |
| `error` |Uma cadeia de caracteres de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| `error_description` |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

Se você receber esse erro na solicitação de iframe, o usuário deverá fazer logon interativamente novamente para recuperar um novo token. Você pode optar por lidar com esse caso de qualquer maneira faz sentido para seu aplicativo.

## <a name="refreshing-tokens"></a>Atualizando tokens

A concessão implícita não fornece tokens de atualização. Tanto `id_token`s como `access_token`expirarão após um curto período de tempo, pelo que a sua aplicação deve estar preparada para refrescar estes tokens periodicamente. Para atualizar qualquer tipo de token, você pode executar a mesma solicitação de iframe oculto acima usando o parâmetro `prompt=none` para controlar o comportamento da plataforma de identidade. Se quiser receber um novo `id_token`, certifique-se de utilizar `id_token` nos `response_type` e `scope=openid`, bem como um parâmetro `nonce`.

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de saída

O OpenID Connect `end_session_endpoint` permite que a sua aplicação envie um pedido para o ponto final da plataforma de identidade da Microsoft para terminar a sessão de um utilizador e limpar cookies definidos pelo ponto final da plataforma de identidade da Microsoft. Para desconectar completamente um usuário de um aplicativo Web, seu aplicativo deve encerrar sua própria sessão com o usuário (normalmente limpando um cache de token ou descartando cookies) e, em seguida, redirecionar o navegador para:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| `tenant` |required |O valor de `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | recomendado | A URL para a qual o usuário deve ser devolvido após a conclusão do logout. Esse valor deve corresponder a um dos URIs de redirecionamento registrados para o aplicativo. Se não estiver incluído, o usuário receberá uma mensagem genérica do ponto de extremidade da plataforma Microsoft Identity. |

## <a name="next-steps"></a>Passos seguintes

* Veja as [amostras do MSAL js](sample-v2-code.md) para começar a codificar.
