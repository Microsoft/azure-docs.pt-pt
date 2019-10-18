---
title: Proteger aplicativos de uma única página usando o fluxo implícito da plataforma de identidade da Microsoft | Azure
description: Criando aplicativos Web usando a implementação da plataforma de identidade da Microsoft do fluxo implícito para aplicativos de página única.
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
ms.date: 10/16/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 375fe839c31062474994d329379b066049272f55
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72527053"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Plataforma de identidade da Microsoft e fluxo de concessão implícita

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Com o ponto de extremidade da plataforma Microsoft Identity, você pode conectar os usuários em seus aplicativos de página única com contas pessoais e corporativas ou de estudante da Microsoft. Uma página única e outros aplicativos JavaScript que são executados principalmente em um navegador enfrentam alguns desafios interessantes quando se trata de autenticação:

* As características de segurança desses aplicativos são significativamente diferentes dos aplicativos Web tradicionais baseados em servidor.
* Muitos servidores de autorização e provedores de identidade não dão suporte a solicitações CORS.
* O navegador de página inteira redireciona para fora do aplicativo se torna particularmente invasivo para a experiência do usuário.

Para esses aplicativos (AngularJS, Ember. js, reajam. js e assim por diante), a plataforma de identidade da Microsoft dá suporte ao fluxo de concessão implícita do OAuth 2,0. O fluxo implícito é descrito na [especificação do OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-4.2). Seu principal benefício é que ele permite que o aplicativo obtenha tokens da plataforma de identidade da Microsoft sem executar uma troca de credenciais de servidor de back-end. Isso permite que o aplicativo entre no usuário, mantenha a sessão e obtenha tokens para outras APIs Web no código JavaScript do cliente. Há algumas considerações de segurança importantes a serem levadas em conta ao usar o fluxo implícito especificamente em relação à representação do [cliente](https://tools.ietf.org/html/rfc6749#section-10.3) e do [usuário](https://tools.ietf.org/html/rfc6749#section-10.3).

Se você quiser usar o fluxo implícito e a plataforma de identidade da Microsoft para adicionar autenticação ao seu aplicativo JavaScript, recomendamos que você use a biblioteca JavaScript de software livre, [MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js).

No entanto, se você preferir não usar uma biblioteca em seu aplicativo de página única e enviar mensagens de protocolo por conta própria, siga as etapas gerais abaixo.

> [!NOTE]
> Nem todos os cenários e recursos do Azure Active Directory (AD do Azure) são suportados pelo ponto de extremidade da plataforma de identidade da Microsoft. Para determinar se você deve usar o ponto de extremidade da plataforma de identidade da Microsoft, leia sobre as [limitações da plataforma de identidade da Microsoft](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama a seguir mostra a aparência de todo o fluxo de entrada implícito e as seções a seguir descrevem cada etapa mais detalhadamente.

![Diagrama mostrando o fluxo de entrada implícito](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Enviar a solicitação de entrada

Para conectar inicialmente o usuário em seu aplicativo, você pode enviar uma solicitação de autenticação do [OpenID Connect](v2-protocols-oidc.md) e obter um `id_token` do ponto de extremidade da plataforma Microsoft Identity.

> [!IMPORTANT]
> Para solicitar um token de ID com êxito, o registro do aplicativo na página de [portal do Azure registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) deve ter o fluxo de concessão implícito habilitado corretamente, selecionando **tokens de acesso** e **tokens de ID** na **concessão implícita** Section. Se não estiver habilitado, um erro de `unsupported_response` será retornado: **o valor fornecido para o parâmetro de entrada ' response_type ' não é permitido para este cliente. O valor esperado é ' code '**

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
> Para testar a entrada usando o fluxo implícito, clique em <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a> Depois de entrar, seu navegador deve ser redirecionado para `https://localhost/myapp/` com um `id_token` na barra de endereços.
>

| Parâmetro |  | Descrição |
| --- | --- | --- |
| `tenant` | Necessário |O valor de `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `client_id` | Necessário | A ID do aplicativo (cliente) que a página de [portal do Azure registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) atribuída ao seu aplicativo. |
| `response_type` | Necessário |Deve incluir `id_token` para entrar no OpenID Connect. Ele também pode incluir o `token` response_type. Usar `token` aqui permitirá que seu aplicativo receba um token de acesso imediatamente do ponto de extremidade de autorização sem precisar fazer uma segunda solicitação para o ponto de extremidade de autorização. Se você usar o `token` response_type, o parâmetro `scope` deverá conter um escopo que indica para qual recurso emitir o token. |
| `redirect_uri` | Aconselhável |O redirect_uri do seu aplicativo, em que as respostas de autenticação podem ser enviadas e recebidas pelo seu aplicativo. Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, exceto que ele deve ser codificado por URL. |
| `scope` | Necessário |Uma lista de [escopos](v2-permissions-and-consent.md)separados por espaços. Para o OpenID Connect, ele deve incluir o escopo `openid`, que se traduz na permissão "entrar" na interface do usuário de consentimento. Opcionalmente, talvez você também queira incluir os escopos `email` ou `profile` para obter acesso a dados de usuário adicionais. Você também pode incluir outros escopos nesta solicitação para solicitar o consentimento para vários recursos. |
| `response_mode` | Adicional |Especifica o método que deve ser usado para enviar o token resultante de volta ao seu aplicativo. O padrão é consultar um token de acesso, mas fragmentar se a solicitação incluir um id_token. |
| `state` | Aconselhável |Um valor incluído na solicitação que também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo que você desejar. Um valor exclusivo gerado aleatoriamente geralmente é usado para [impedir ataques de solicitação entre sites forjado](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que eles estavam. |
| `nonce` | Necessário |Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no id_token resultante como uma declaração. O aplicativo pode, então, verificar esse valor para atenuar os ataques de reprodução de token. O valor normalmente é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. Necessário somente quando um id_token é solicitado. |
| `prompt` | Adicional |Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos no momento são ' login ', ' none ', ' select_account ' e ' consentimento '. `prompt=login` forçará o usuário a inserir suas credenciais nessa solicitação, negando o logon único. `prompt=none` é o oposto, ele garantirá que o usuário não seja apresentado a nenhum prompt interativo. Se a solicitação não puder ser concluída silenciosamente por meio de logon único, o ponto de extremidade da plataforma de identidade da Microsoft retornará um erro. `prompt=select_account` envia o usuário para um seletor de conta onde todas as contas lembradas na sessão serão exibidas. `prompt=consent` disparará a caixa de diálogo de consentimento do OAuth depois que o usuário fizer logon, solicitando que o usuário conceda permissões ao aplicativo. |
| `login_hint`  |Adicional |Pode ser usado para preencher previamente o campo nome de usuário/endereço de email da página de entrada do usuário, se você souber seu nome de usuário antes do tempo. Com frequência, os aplicativos usarão esse parâmetro durante a reautenticação, já tendo extraído o nome de usuário de uma entrada anterior usando a declaração de `preferred_username`.|
| `domain_hint` | Adicional |Pode ser uma das `consumers` ou `organizations`. Se for incluído, ele ignorará o processo de descoberta baseado em email que o usuário passa na página de entrada, levando a uma experiência de usuário um pouco mais simplificada. Frequentemente, os aplicativos usarão esse parâmetro durante a reautenticação, extraindo a declaração de `tid` do id_token. Se o valor de declaração de `tid` for `9188040d-6c67-4c5b-b112-36a304b66dad` (o locatário de consumidor da conta da Microsoft), você deverá usar `domain_hint=consumers`. Caso contrário, você pode usar `domain_hint=organizations` durante a nova autenticação. |

Neste ponto, o usuário será solicitado a inserir suas credenciais e concluir a autenticação. O ponto de extremidade da plataforma Microsoft Identity também garantirá que o usuário tenha consentido as permissões indicadas no parâmetro de consulta `scope`. Se o usuário tiver consentido **nenhuma** dessas permissões, ele solicitará que o usuário consenti as permissões necessárias. Para obter mais informações, consulte [permissões, consentimento e aplicativos de vários locatários](v2-permissions-and-consent.md).

Depois que o usuário autenticar e conceder consentimento, o ponto de extremidade da plataforma de identidade da Microsoft retornará uma resposta ao seu aplicativo no `redirect_uri` indicado, usando o método especificado no parâmetro `response_mode`.

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida usando `response_mode=fragment` e `response_type=id_token+token` é parecida com a seguinte (com quebras de linha para legibilidade):

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
| `access_token` |Incluído se `response_type` incluir `token`. O token de acesso que o aplicativo solicitou, neste caso, para o Microsoft Graph. O token de acesso não deve ser decodificado ou inspecionado de outra forma, ele deve ser tratado como uma cadeia de caracteres opaca. |
| `token_type` |Incluído se `response_type` incluir `token`. Sempre será `Bearer`. |
| `expires_in`|Incluído se `response_type` incluir `token`. Indica o número de segundos que o token é válido, para fins de cache. |
| `scope` |Incluído se `response_type` incluir `token`. Indica os escopos para os quais o access_token será válido. Pode não incluir todos os escopos solicitados, se eles não forem aplicáveis ao usuário (no caso de escopos somente AD do Azure serem solicitados quando uma conta pessoal for usada para fazer logon). |
| `id_token` | Um JWT (token Web JSON) assinado. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que se conectou. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. Para obter mais informações sobre id_tokens, consulte a [`id_token reference`](id-tokens.md). <br> **Observação:** Fornecido somente se `openid` escopo foi solicitado. |
| `state` |Se um parâmetro de estado for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas à `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| `error` |Uma cadeia de caracteres de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usada para reagir a erros. |
| `error_description` |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

## <a name="get-access-tokens"></a>Obter tokens de acesso

Agora que você assinou o usuário em seu aplicativo de página única, você pode obter tokens de acesso para chamar APIs da Web protegidas pela plataforma de identidade da Microsoft, como o [Microsoft Graph](https://developer.microsoft.com/graph). Mesmo que você já tenha recebido um token usando o `token` response_type, você pode usar esse método para adquirir tokens para recursos adicionais sem precisar redirecionar o usuário para entrar novamente.

No fluxo normal do OpenID Connect/OAuth, você faria isso fazendo uma solicitação para o ponto de extremidade de `/token` da plataforma de identidade da Microsoft. No entanto, o ponto de extremidade da plataforma Microsoft Identity não oferece suporte a solicitações CORS, portanto, fazer chamadas AJAX para obter e atualizar tokens está fora da pergunta. Em vez disso, você pode usar o fluxo implícito em um iframe oculto para obter novos tokens para outras APIs da Web: 

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

Para obter detalhes sobre os parâmetros de consulta na URL, consulte [enviar a solicitação de entrada](#send-the-sign-in-request).

> [!TIP]
> Tente copiar & colar a solicitação abaixo em uma guia do navegador! (Não se esqueça de substituir os valores de `login_hint` pelo valor correto para seu usuário)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint=your-username`
>

Graças ao parâmetro `prompt=none`, essa solicitação será bem-sucedida ou falhará imediatamente e retornará ao seu aplicativo. Uma resposta bem-sucedida será enviada ao seu aplicativo no `redirect_uri` indicado, usando o método especificado no parâmetro `response_mode`.

#### <a name="successful-response"></a>Resposta bem-sucedida

Uma resposta bem-sucedida usando `response_mode=fragment` é semelhante a:

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
| `access_token` |Incluído se `response_type` incluir `token`. O token de acesso que o aplicativo solicitou, neste caso, para o Microsoft Graph. O token de acesso não deve ser decodificado ou inspecionado de outra forma, ele deve ser tratado como uma cadeia de caracteres opaca. |
| `token_type` | Sempre será `Bearer`. |
| `expires_in` | Indica o número de segundos que o token é válido, para fins de cache. |
| `scope` | Indica os escopos para os quais o access_token será válido. Pode não incluir todos os escopos solicitados, se eles não forem aplicáveis ao usuário (no caso de escopos somente AD do Azure serem solicitados quando uma conta pessoal for usada para fazer logon). |
| `id_token` | Um JWT (token Web JSON) assinado. Incluído se `response_type` incluir `id_token`. O aplicativo pode decodificar os segmentos desse token para solicitar informações sobre o usuário que se conectou. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. Para obter mais informações sobre id_tokens, consulte a [referência de `id_token`](id-tokens.md). <br> **Observação:** Fornecido somente se `openid` escopo foi solicitado. |
| `state` |Se um parâmetro de estado for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas à `redirect_uri` para que o aplicativo possa tratá-las adequadamente. No caso do `prompt=none`, um erro esperado será:

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

A concessão implícita não fornece tokens de atualização. Tanto `id_token`s quanto `access_token`s expirarão após um curto período de tempo, de modo que seu aplicativo deve estar preparado para atualizar esses tokens periodicamente. Para atualizar qualquer tipo de token, você pode executar a mesma solicitação de iframe oculto acima usando o parâmetro `prompt=none` para controlar o comportamento da plataforma de identidade. Se você quiser receber uma nova `id_token`, certifique-se de usar `response_type=id_token` e `scope=openid`, bem como um parâmetro de `nonce`.

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de saída

O `end_session_endpoint` OpenID Connect permite que seu aplicativo envie uma solicitação ao ponto de extremidade da plataforma Microsoft Identity para encerrar a sessão de um usuário e limpar os cookies definidos pelo ponto de extremidade da plataforma Microsoft Identity. Para desconectar completamente um usuário de um aplicativo Web, seu aplicativo deve encerrar sua própria sessão com o usuário (normalmente limpando um cache de token ou descartando cookies) e, em seguida, redirecionar o navegador para:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| `tenant` |Necessário |O valor de `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| `post_logout_redirect_uri` | Aconselhável | A URL para a qual o usuário deve ser devolvido após a conclusão do logout. Esse valor deve corresponder a um dos URIs de redirecionamento registrados para o aplicativo. Se não estiver incluído, o usuário receberá uma mensagem genérica do ponto de extremidade da plataforma Microsoft Identity. |

## <a name="next-steps"></a>Passos seguintes

* Veja as [amostras do MSAL js](sample-v2-code.md) para começar a codificar.
