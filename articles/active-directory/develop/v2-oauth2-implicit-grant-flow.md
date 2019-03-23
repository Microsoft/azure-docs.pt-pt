---
title: Proteger aplicações de página única com o fluxo implícito de v2.0 do Azure AD | Documentos da Microsoft
description: Criando aplicativos web com implementação do Azure AD v2.0 o fluxo implícito para aplicações de página única.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9ce388c53a28d6b04bf7685da397eade4b1fd94
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371779"
---
# <a name="v20-protocols---spas-using-the-implicit-flow"></a>v2.0 protocolos - SPAs com o fluxo implícito

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Com o ponto final v2.0, pode iniciar a sessão de utilizadores nas suas aplicações de página única com contas pessoais e profissionais ou escolares da Microsoft. Página única e outras aplicações de JavaScript que executar principalmente num rosto de navegador interessante alguns desafios que diz respeito à autenticação:

* As características de segurança destas aplicações são significativamente diferentes dos aplicativos web baseados em servidor tradicional.
* Muitos servidores de autorização e fornecedores de identidade não suportam solicitações CORS.
* Redireciona o navegador de página inteira na direção oposta a aplicação que se tornar particularmente invasiva para a experiência do usuário.

Para estas aplicações (AngularJS, ember, react. js, etc.), o Azure Active Directory (Azure AD) suporta o fluxo de concessão implícita OAuth 2.0. O fluxo implícito está descrito com o [especificação do OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). O principal benefício é que ela permite que a aplicação obter os tokens do Azure AD sem executar um servidor de back-end exchange de credencial. Isso permite que a aplicação iniciar a sessão do utilizador, manter a sessão e obtenha tokens para outras APIs web tudo dentro do cliente código JavaScript. Existem algumas considerações de segurança importante levar em conta ao utilizar o fluxo implícito especificamente cerca [cliente](https://tools.ietf.org/html/rfc6749#section-10.3) e [representação de utilizadores](https://tools.ietf.org/html/rfc6749#section-10.3).

Se pretender utilizar o fluxo implícito e o Azure AD para adicionar autenticação à sua aplicação do JavaScript, é recomendado que utilize a biblioteca de JavaScript de código-fonte aberto [msal](https://github.com/AzureAD/microsoft-authentication-library-for-js).

No entanto, se preferir não usar uma biblioteca na sua aplicação de página única e enviar mensagens de protocolo por conta própria, siga os passos gerais abaixo.

> [!NOTE]
> Observe que todos os cenários e funcionalidades do Azure AD são compatíveis com o ponto final do v2.0. Para determinar se deve utilizar o ponto final v2.0, leia sobre [v2.0 limitações](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama seguinte mostra a aparência de todo início de sessão fluxo implícito e seções a seguir descrevem cada passo mais detalhadamente.

![Pistas de diagrama de ligação do OpenId](./media/v2-oauth2-implicit-grant-flow/convergence_scenarios_implicit.png)

## <a name="send-the-sign-in-request"></a>Enviar o pedido de início de sessão

Inicialmente iniciar a sessão do utilizador na sua aplicação, pode enviar um [OpenID Connect](v2-protocols-oidc.md) pedido de autorização e obtenha um `id_token` do ponto final v2.0.

> [!IMPORTANT]
> Para pedir com êxito um token de ID, o registo de aplicações no [portal do Azure – registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) página tem de ter o fluxo de concessão implícita ativado corretamente, ao selecionar **tokens de acesso** e **Tokens de ID** sob o **concessão implícita** secção. Se não estiver ativada, um `unsupported_response` vai ser devolvido o erro: **O valor fornecido para o parâmetro de entrada "response_type" não é permitido para este cliente. Valor esperado é 'code'**

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
> Para testar a iniciar sessão utilizando o fluxo implícito, clique em <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Depois de iniciar sessão, o browser deve ser redirecionado para `https://localhost/myapp/` com um `id_token` na barra de endereço.
>

| Parâmetro |  | Descrição |
| --- | --- | --- |
| `tenant` | obrigatório |O `{tenant}` valor no caminho do pedido pode ser utilizado para controlar quem pode iniciar sessão na aplicação. Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de inquilinos. Para obter mais detalhes, consulte [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `client_id` | obrigatório |ID de aplicação (cliente) que o [portal do Azure – registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) atribuída à sua aplicação de página. |
| `response_type` | obrigatório |Tem de incluir `id_token` OpenID Connect para início de sessão. Também pode incluir o response_type `token`. Usando `token` aqui irá permitir que a sua aplicação receber um token de acesso imediatamente a partir do ponto final de autorização sem ter de efetuar um pedido de segundo para o ponto final de autorização. Se utilizar o `token` response_type, o `scope` parâmetro tem de conter um âmbito que indica qual para emitir o token para o recurso. |
| `redirect_uri` | Recomendado |O redirect_uri da sua aplicação, onde as respostas podem ser enviadas e recebidas pela sua aplicação. Ele deve corresponder exatamente um dos redirect_uris registado no portal, exceto pelo fato tem de ser codificados de url. |
| `scope` | obrigatório |Uma lista separada por espaço de [âmbitos](v2-permissions-and-consent.md). Para o OpenID Connect, tem de incluir o âmbito `openid`, que traduz-se a permissão "Iniciar sessão" no consentimento da interface do Usuário. Opcionalmente, também poderá incluir a `email` ou `profile` âmbitos para terem acesso aos dados de utilizador adicionais. Também pode incluir outros âmbitos neste pedido para pedir consentimento a vários recursos. |
| `response_mode` | opcional |Especifica o método que deve ser utilizado para enviar a cópia de token resultante à sua aplicação. Assume a predefinição de consulta para um token de acesso, mas o fragmento, se o pedido inclui um id_token. |
| `state` | Recomendado |Um valor incluído no pedido que também vai ser devolvido na resposta de token. Pode ser uma cadeia de caracteres de qualquer conteúdo que desejar. Um valor exclusivo gerado aleatoriamente é normalmente utilizado para [impedir ataques de falsificação de solicitação](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar as informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, como a página ou a vista estivessem na. |
| `nonce` | obrigatório |Um valor incluído na solicitação, gerada pela aplicação, que incluirá o id_token resultante como uma afirmação. A aplicação pode, em seguida, verifique se este valor para mitigar ataques de repetição de token. O valor normalmente é uma cadeia aleatória, exclusiva que pode ser utilizada para identificar a origem do pedido. Apenas é necessário quando um id_token é solicitada. |
| `prompt` | opcional |Indica o tipo de interação do utilizador que é necessário. Os valores só é válidos neste momento são 'login', 'none', "select_account" e "consentimento". `prompt=login` irá forçar o utilizador para introduzir as respetivas credenciais na solicitação, eliminando-início de sessão único. `prompt=none` é o oposto – ele garantirá que não é apresentada ao utilizador com qualquer linha de comandos interativa tipo. Se o pedido não pode ser concluído silenciosamente por meio de início de sessão único, o ponto final v2.0 irá devolver um erro. `prompt=select_account` envia o utilizador para um Seletor de conta em que todas as contas memorizadas na sessão aparecerá. `prompt=consent` irá acionar a caixa de diálogo de consentimento do OAuth depois do utilizador inicia sessão, solicitando que o usuário para conceder permissões à aplicação. |
| `login_hint`  |opcional |Pode ser utilizado para preencher previamente o campo de endereço de e-mail/nome de utilizador de início de sessão na página do utilizador, se souber que o respetivo nome de utilizador antes do tempo. Aplicações, muitas vezes, irão utilizar este parâmetro durante a reautenticação, já após extrair o nome de utilizador de um anterior início de sessão com o `preferred_username` de afirmação.|
| `domain_hint` | opcional |Pode ser um dos `consumers` ou `organizations`. Se incluído, ele irá ignorar o processo de descoberta baseada em e-mail que o utilizador passa por no sinal v2.0 na página, que leva a uma experiência de usuário ligeiramente mais simplificada. Muitas vezes, aplicações, irão utilizar este parâmetro durante a reautenticação, extraindo o `tid` o id_token de afirmação. Se o `tid` é o valor de afirmação `9188040d-6c67-4c5b-b112-36a304b66dad` (o Microsoft Account consumidor inquilino), deve usar `domain_hint=consumers`. Caso contrário, pode usar `domain_hint=organizations` durante a reavaliação de autenticação. |

Neste momento, o usuário será solicitado para introduzir as respetivas credenciais e concluir a autenticação. O ponto final v2.0 também vai assegurar que o utilizador consentiu as permissões indicadas no `scope` parâmetro de consulta. Se o utilizador consentiu **none** dessas permissões, pedirá ao utilizador para autorizar as permissões necessárias. Para mais informações, veja [permissões e consentimento e aplicações multi-inquilino](v2-permissions-and-consent.md).

Depois do utilizador autentica e concede o consentimento, o ponto final v2.0 irá devolver uma resposta à sua aplicação no indicado `redirect_uri`, utilizando o método especificado no `response_mode` parâmetro.

#### <a name="successful-response"></a>Resposta com êxito

Uma resposta com êxito utilizando `response_mode=fragment` e `response_type=id_token+token` se parece com o seguinte (com quebras de linha para melhorar a legibilidade):

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fuser.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| `access_token` |If incluído `response_type` inclui `token`. O token de acesso que a aplicação pedida, nesse caso para o Microsoft Graph. O token de acesso não deve ser descodificado ou inspecionado caso contrário, ele deve ser tratado como uma cadeia opaca. |
| `token_type` |If incluído `response_type` inclui `token`. Será sempre `Bearer`. |
| `expires_in`|If incluído `response_type` inclui `token`. Indica o número de segundos que o token for válido, para fins de colocação em cache. |
| `scope` |If incluído `response_type` inclui `token`. Indica os âmbitos que se para o qual o access_token será válido. Não pode incluir todos os âmbitos solicitados, se não fosse aplicáveis para o utilizador (no caso de âmbitos de só de AAD a ser solicitada quando uma conta pessoal é utilizada para iniciar sessão). |
| `id_token` | Um assinado JSON Web Token (JWT). A aplicação pode decodificar os segmentos deste token solicite informações sobre o utilizador que iniciou sessão. A aplicação pode armazenar em cache os valores e exibi-los, mas não deverá confiar nos mesmos para qualquer autorização ou limites de segurança. Para obter mais informações sobre id_tokens, consulte a [ `id_token reference` ](id-tokens.md). <br> **Nota:** Só será fornecido se `openid` âmbito foi pedido. |
| `state` |Se um parâmetro de estado está incluído na solicitação, o mesmo valor deve aparecer na resposta. A aplicação deve verificar que os valores de estado no pedido e resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

Respostas de erro também podem ser enviadas para o `redirect_uri` para que a aplicação pode processar corretamente:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| `error` |Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para reagir a erros. |
| `error_description` |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação do desenvolvedor. |

## <a name="validate-the-idtoken"></a>Validar o id_token

Não é suficiente para autenticar o usuário; apenas receber uma id_token Também tem de validar a assinatura o id_token e verifique se as afirmações no token com base nos requisitos da sua aplicação. Utiliza o ponto final v2.0 [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar os tokens e certifique-se de que são válidas.

Pode optar por validar a `id_token` num cliente de código, mas uma prática comum é enviar o `id_token` para um servidor de back-end e executar a validação lá. Assim que validar a assinatura do id_token, existem algumas declarações, que será necessário para verificar. Consulte a [ `id_token` referência](id-tokens.md) para obter mais informações, incluindo [tokens de validação](id-tokens.md#validating-an-id_token) e [informações importantes sobre o rollover da chave de assinatura](active-directory-signing-key-rollover.md). Recomendamos que fazendo uso de uma biblioteca para análise e a validar tokens – há, pelo menos, um disponível para a maioria das linguagens e plataformas.

Também pode pretender validar afirmações adicionais, dependendo do seu cenário. Algumas validações comuns incluem:

* Garantir que a organização/utilizador tiver inscrito a aplicação.
* Garantir que o usuário tem autorização/privilégios apropriados.
* Garantir que um determinada força da autenticação tiver ocorrido, como a autenticação multifator.

Depois de validar o id_token completamente, pode iniciar uma sessão com o utilizador e utilizar as afirmações no id_token para obter informações sobre o utilizador na sua aplicação. Estas informações podem ser utilizadas para apresentar, registos, personalização, etc.

## <a name="get-access-tokens"></a>Obter os tokens de acesso

Agora que o utilizador ter iniciado sessão na sua aplicação de página única, pode obter os tokens de acesso para chamadas de web APIs protegidas pelo Azure AD, como o [Microsoft Graph](https://developer.microsoft.com/graph). Mesmo que já recebeu um token através do `token` response_type, pode utilizar este método para adquirir tokens para recursos adicionais sem ter de redirecionar o utilizador iniciar sessão novamente.

No fluxo de OpenID Connect/OAuth normal, pode fazê-lo fazendo uma solicitação para a versão 2.0 `/token` ponto final. No entanto, o ponto final v2.0 não suporta solicitações CORS, portanto, fazer chamadas AJAX para obter e tokens de atualização está fora da pergunta. Em vez disso, pode utilizar o fluxo implícito num iframe oculto para obter novos tokens para outras APIs web: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

Para obter detalhes sobre os parâmetros de consulta no URL, consulte [enviar o início de sessão no pedido](#send-the-sign-in-request).

> [!TIP]
> Tente copiar e colar o abaixo pedido num separador do browser! (Não se esqueça de substituir o `login_hint` valores com o valor correto para o seu utilizador)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint=your-username`
>

Graças ao `prompt=none` parâmetro, este pedido será o êxito ou falhar de imediato e retorne ao seu aplicativo. Uma resposta com êxito será enviada para a sua aplicação no indicado `redirect_uri`, utilizando o método especificado no `response_mode` parâmetro.

#### <a name="successful-response"></a>Resposta com êxito

Uma resposta com êxito utilizando `response_mode=fragment` se parece com:

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
| `access_token` |If incluído `response_type` inclui `token`. O token de acesso que a aplicação pedida, nesse caso para o Microsoft Graph. O token de acesso não deve ser descodificado ou inspecionado caso contrário, ele deve ser tratado como uma cadeia opaca. |
| `token_type` | Será sempre `Bearer`. |
| `expires_in` | Indica o número de segundos que o token for válido, para fins de colocação em cache. |
| `scope` | Indica os âmbitos que se para o qual o access_token será válido. Não pode incluir todos os âmbitos solicitados, se não fosse aplicáveis para o utilizador (no caso de âmbitos de só de AAD a ser solicitada quando uma conta pessoal é utilizada para iniciar sessão). |
| `id_token` | Um assinado JSON Web Token (JWT). If incluído `response_type` inclui `id_token`. A aplicação pode decodificar os segmentos deste token solicite informações sobre o utilizador que iniciou sessão. A aplicação pode armazenar em cache os valores e exibi-los, mas não deverá confiar nos mesmos para qualquer autorização ou limites de segurança. Para obter mais informações sobre id_tokens, consulte a [ `id_token` referência](id-tokens.md). <br> **Nota:** Só será fornecido se `openid` âmbito foi pedido. |
| `state` |Se um parâmetro de estado está incluído na solicitação, o mesmo valor deve aparecer na resposta. A aplicação deve verificar que os valores de estado no pedido e resposta são idênticos. |


#### <a name="error-response"></a>Resposta de erro

Respostas de erro também podem ser enviadas para o `redirect_uri` para que a aplicação pode processar corretamente. No caso de `prompt=none`, será um erro inesperado:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | Descrição |
| --- | --- |
| `error` |Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para reagir a erros. |
| `error_description` |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação do desenvolvedor. |

Se receber este erro no pedido de iframe, o utilizador tem interativamente iniciar sessão novamente para obter um novo token. Pode optar por lidar com esse caso da maneira que fizer sentido para a sua aplicação.

## <a name="validating-access-tokens"></a>A validar os tokens de acesso

Assim que receber um access_token, certifique-se validar a assinatura de token, bem como as seguintes declarações. Também pode optar por validar afirmações adicionais com base no seu cenário. 

* **público-alvo** alegam, certifique-se de que o token destinava-se a ser dado à sua aplicação
* **emissor** alegam, certifique-se de que o token foi emitido para a sua aplicação, o ponto final v2.0
* **não antes** e **hora de expiração** afirma, certifique-se de que o token não expirou

Para obter mais informações sobre as ações presentes no token de acesso, consulte o [referência de token de acesso](access-tokens.md)

## <a name="refreshing-tokens"></a>Atualizar tokens

A concessão implícita não fornece tokens de atualização. Ambos `id_token`s e `access_token`s irá expirar após um curto período de tempo, para que a sua aplicação tem de estar preparada para atualizar estes tokens periodicamente. Para atualizar de qualquer tipo de token, pode executar o mesmo pedido de iframe ocultos from above usando o `prompt=none` parâmetro para controlar o comportamento do Azure AD. Se pretender receber uma nova `id_token`, certifique-se de que utiliza `response_type=id_token` e `scope=openid`, bem como um `nonce` parâmetro.

## <a name="send-a-sign-out-request"></a>Enviar um pedido de sessão

O OpenIdConnect `end_session_endpoint` permite à aplicação enviar um pedido para o ponto final v2.0 para terminar uma sessão de utilizador e desmarque cookies definidos pelo ponto final v2.0. Para assinar totalmente um utilizador de um aplicativo web, a aplicação deve terminar sua própria sessão com o utilizador (normalmente, limpar a cache de tokens ou remover cookies) e, em seguida, redirecionar o browser para:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| `tenant` |obrigatório |O `{tenant}` valor no caminho do pedido pode ser utilizado para controlar quem pode iniciar sessão na aplicação. Os valores permitidos são `common`, `organizations`, `consumers`e identificadores de inquilinos. Para obter mais detalhes, consulte [Noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | Recomendado | O URL que o utilizador deve ser retornado para depois de concluída a fim de sessão. Este valor tem de corresponder a um dos URIs registado para a aplicação de redirecionamento. Se não incluído, o utilizador será apresentado uma mensagem genérica, o ponto final v2.0. |

## <a name="next-steps"></a>Passos Seguintes

* Aceda através de [exemplos de MSAL JS](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) para começar a utilizar a codificação.
