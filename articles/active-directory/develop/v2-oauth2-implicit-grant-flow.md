---
title: OAuth 2.0 fluxo de subvenção implícita - A plataforma de identidade da Microsoft | Rio Azure
description: Proteja as aplicações de uma página única utilizando o fluxo implícito da plataforma de identidade da Microsoft.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: f3598c6f072d09d7e427db66dcfbf8721b92a3a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226493"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Plataforma de identidade da Microsoft e fluxo de subvenção implícito

A plataforma de identidade da Microsoft suporta o fluxo de subvenção implícita OAuth 2.0, conforme descrito na [Especificação OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). A característica determinante da subvenção implícita é que os tokens (fichas de identificação ou fichas de acesso) são devolvidos diretamente do ponto final /autorizado em vez do ponto final /token. Isto é frequentemente usado como parte do fluxo de código de [autorização](v2-oauth2-auth-code-flow.md), no que é chamado de "fluxo híbrido" - recuperando o token de ID no pedido /autorizado juntamente com um código de autorização.

[!INCLUDE [suggest-msal-from-protocols](includes/suggest-msal-from-protocols.md)]

## <a name="prefer-the-auth-code-flow"></a>Prefere o fluxo de código auth

Com os [planos para que os cookies de terceiros sejam removidos dos navegadores,](reference-third-party-cookies-spas.md)o **fluxo de concessão implícito deixou de ser um método de autenticação adequado.**  As [funcionalidades silenciosas do SSO](#getting-access-tokens-silently-in-the-background) do fluxo implícito não funcionam sem cookies de terceiros, fazendo com que as aplicações se rompam quando tentam obter um novo token. Recomendamos vivamente que todas as novas aplicações utilizem o fluxo de código de [autorização](v2-oauth2-auth-code-flow.md) que agora suporta aplicações de página única em vez do fluxo implícito, e que [as aplicações de página única existentes comecem a migrar também para o fluxo de código de autorização.](migrate-spa-implicit-to-auth-code.md)

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Cenários adequados para a subvenção implícita OAuth2

A subvenção implícita só é fiável para a parte inicial e interativa do seu sinal em fluxo, onde a falta de cookies de [terceiros](reference-third-party-cookies-spas.md) não pode afetar a sua aplicação. Esta limitação significa que deve usá-lo exclusivamente como parte do fluxo híbrido, onde a sua aplicação solicita um código, bem como um símbolo do ponto final de autorização. Isto garante que a sua aplicação recebe um código que pode ser resgatado para um token de atualização, garantindo assim que a sessão de login da sua aplicação permanece válida ao longo do tempo.

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama seguinte mostra como é todo o fluxo de entrada implícita e as secções que se seguem descrevem cada passo com mais detalhes.

![Diagrama mostrando o fluxo implícito de entrada](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Envie o pedido de inscrição

Para inicialmente iniciar a inscrição do utilizador na sua aplicação, pode enviar um pedido de autenticação [OpenID Connect](v2-protocols-oidc.md) e obter um `id_token` a partir da plataforma de identidade da Microsoft.

> [!IMPORTANT]
> Para solicitar com sucesso um token de ID e/ou um token de acesso, o registo da aplicação no [portal Azure - Página de registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) deve ter o fluxo de subvenção implícita correspondente, selecionando fichas de **identificação** e **fichas de acesso** na secção de **fluxos implícitos e híbridos.** Se não estiver ativado, será devolvido um `unsupported_response` erro: `The provided value for the input parameter 'response_type' is not allowed for this client. Expected value is 'code'`

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
> Para testar a assinatura utilizando o fluxo implícito, clique em <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize.. .</a> Após a sua sessão, o seu navegador deve ser redirecionado para `https://localhost/myapp/` um na barra de `id_token` endereços.
>

| Parâmetro | Tipo | Description |
| --- | --- | --- |
| `tenant` | obrigatório |O `{tenant}` valor no caminho do pedido pode ser usado para controlar quem pode assinar a aplicação. Os valores permitidos `common` `organizations` são, `consumers` e os identificadores de inquilinos. Para mais detalhes, consulte [o protocolo básico.](active-directory-v2-protocols.md#endpoints) |
| `client_id` | obrigatório | O ID da Aplicação (cliente) que o [portal Azure - Página de registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) atribuído à sua app. |
| `response_type` | obrigatório |Deve incluir `id_token` para o iniciar sê-in OpenID Connect. Também pode incluir o response_type `token` . A utilização `token` aqui permitirá que a sua aplicação receba imediatamente um token de acesso a partir do ponto final autorizado sem ter de fazer um segundo pedido ao ponto final autorizado. Se utilizar o `token` response_type, o `scope` parâmetro deve conter um âmbito que indique qual o recurso para emitir o token (por exemplo, user.read no Microsoft Graph). Também pode conter `code` o lugar de fornecer um código de `token` autorização, para utilização no [fluxo](v2-oauth2-auth-code-flow.md)de código de autorização . Esta resposta id_token+código é por vezes chamada de fluxo híbrido.  |
| `redirect_uri` | recomendado |O redirect_uri da sua app, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a uma das redirect_uris que registou no portal, exceto que deve estar codificada url. |
| `scope` | obrigatório |Uma lista de [âmbitos separados](v2-permissions-and-consent.md)pelo espaço. Para o OpenID Connect (id_tokens), deve incluir o `openid` âmbito, que se traduz na permissão "Iniciar a sua inscrição" na UI de consentimento. Opcionalmente, também pode querer incluir os `email` `profile` e âmbitos para o acesso a dados adicionais do utilizador. Você também pode incluir outros âmbitos neste pedido de pedido de consentimento para vários recursos, se for solicitado um token de acesso. |
| `response_mode` | opcional |Especifica o método que deve ser usado para enviar o símbolo resultante de volta para a sua aplicação. Predefinições para consulta para apenas um token de acesso, mas fragmento se o pedido inclui uma id_token. |
| `state` | recomendado |Um valor incluído no pedido que também será devolvido na resposta simbólica. Pode ser uma série de conteúdos que desejes. Um valor único gerado aleatoriamente é normalmente usado para [prevenir ataques de falsificação de pedidos de trans-locais](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou a vista em que estavam. |
| `nonce` | obrigatório |Um valor incluído no pedido, gerado pela app, que será incluído no id_token resultante como reclamação. A aplicação pode então verificar este valor para mitigar os ataques de reprodução de token. O valor é tipicamente uma corda aleatória e única que pode ser usada para identificar a origem do pedido. Só é necessário quando é solicitada uma id_token. |
| `prompt` | opcional |Indica o tipo de interação do utilizador que é necessária. Os únicos valores válidos neste momento são 'login', 'nenhum', 'select_account', e 'consentimento'. `prompt=login` forçará o utilizador a introduzir as suas credenciais nesse pedido, negando um único sinal. `prompt=none` é o oposto - irá garantir que o utilizador não é apresentado com qualquer posição interativa. Se o pedido não puder ser concluído silenciosamente através de um único sinal, a plataforma de identidade da Microsoft retornará um erro. `prompt=select_account` envia o utilizador para um selecionador de contas onde todas as contas lembradas na sessão aparecerão. `prompt=consent` irá acionar o diálogo de consentimento OAuth após o utilizador entrar, pedindo ao utilizador que conceda permissões à aplicação. |
| `login_hint`  |opcional |Pode ser usado para pré-preenchimento do nome de utilizador/endereço de e-mail do sinal na página para o utilizador, se souber o seu nome de utilizador com antecedência. Muitas vezes as aplicações usam este parâmetro durante a reauthentication, tendo já extraído o nome de utilizador de um pré-in usando a `preferred_username` alegação.|
| `domain_hint` | opcional |Se incluído, saltará o processo de descoberta baseado em e-mail que o utilizador passa no sinal na página, levando a uma experiência de utilizador um pouco mais simplificada. Este parâmetro é comumente usado para aplicações da Linha de Negócios que operam em um único inquilino, onde eles fornecerão um nome de domínio dentro de um determinado inquilino, reencaminhando o utilizador para o provedor da federação para esse inquilino.  Note que esta dica impede os hóspedes de assinarem esta aplicação, e limita o uso de credenciais de nuvem como o FIDO.  |

Neste momento, o utilizador será convidado a introduzir as suas credenciais e a completar a autenticação. A plataforma de identidade da Microsoft também garantirá que o utilizador consentiu nas permissões indicadas no `scope` parâmetro de consulta. Se o utilizador não tiver consentido **em nenhuma** dessas permissões, solicitará ao utilizador que consinta nas permissões necessárias. Para mais informações, consulte [permissões, consentimento e aplicações multi-arrendantes.](v2-permissions-and-consent.md)

Assim que o utilizador autenticar e conceder o consentimento, a plataforma de identidade da Microsoft devolverá uma resposta à sua aplicação no indicado `redirect_uri` , utilizando o método especificado no `response_mode` parâmetro.

#### <a name="successful-response"></a>Resposta bem sucedida

Uma resposta bem sucedida utilizando `response_mode=fragment` e parece o seguinte `response_type=id_token+code` (com quebras de linha para legibilidade):

```HTTP
GET https://localhost/myapp/#
code=0.AgAAktYV-sfpYESnQynylW_UKZmH-C9y_G1A
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| `code` | Incluído se `response_type` inclui `code` . Este é um código de autorização adequado para ser utilizado no fluxo de código de [autorização](v2-oauth2-auth-code-flow.md).  |
| `access_token` |Incluído se `response_type` inclui `token` . O sinal de acesso que a app solicitou. O token de acesso não deve ser descodificado ou inspecionado de outra forma, deve ser tratado como uma corda opaca. |
| `token_type` |Incluído se `response_type` inclui `token` . Sempre `Bearer` será. |
| `expires_in`|Incluído se `response_type` inclui `token` . Indica o número de segundos em que o token é válido, para efeitos de caching. |
| `scope` |Incluído se `response_type` inclui `token` . Indica o(s) âmbito(s) para o qual o access_token será válido. Não podem incluir todos os âmbitos solicitados, se não forem aplicáveis ao utilizador (no caso de âmbitos apenas AZure AD solicitados quando uma conta pessoal é utilizada para iniciar sessão). |
| `id_token` | Um JSON Web Token assinado (JWT). A aplicação pode descodificar os segmentos deste token para solicitar informações sobre o utilizador que se inscreveu. A aplicação pode cache os valores e exibi-los, mas não deve confiar neles para qualquer autorização ou limites de segurança. Para mais informações sobre id_tokens, consulte o [`id_token reference`](id-tokens.md) . <br> **Nota:** Só for fornecido se `openid` o âmbito de aplicação for solicitado e `response_type` `id_tokens` incluído. |
| `state` |Se um parâmetro de estado for incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deve verificar se os valores do Estado no pedido e resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas para `redirect_uri` a aplicação para que a aplicação possa manuseá-las adequadamente:

```HTTP
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| `error` |Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usado para reagir a erros. |
| `error_description` |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro de autenticação. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Obter fichas de acesso silenciosamente no fundo

> [!Important]
> Esta parte do fluxo implícito é improvável que funcione para a sua aplicação, uma vez que é usada em diferentes navegadores devido à [remoção de cookies de terceiros por padrão.](reference-third-party-cookies-spas.md)  Apesar de ainda funcionar em navegadores baseados em Crómio que não se encontrem em Incógnito, os desenvolvedores devem reconsiderar a utilização desta parte do fluxo. Nos navegadores que não suportam cookies de terceiros, receberá um erro indicando que nenhum utilizadores estão inscritos, uma vez que os cookies de sessão da página de login foram removidos pelo navegador. 

Agora que inscreveu o utilizador na sua aplicação de uma página única, pode aceder silenciosamente a tokens para chamar APIs da web protegidos pela plataforma de identidade microsoft, como o [Microsoft Graph](https://developer.microsoft.com/graph). Mesmo que já tenha recebido um token usando o `token` response_type, pode usar este método para adquirir fichas para recursos adicionais sem ter que redirecionar o utilizador para iniciar novamente o súmis.

No fluxo normal de OpenID Connect/OAuth, faria-o fazendo um pedido ao ponto final da plataforma de identidade da `/token` Microsoft. Você pode fazer o pedido em um iframe oculto para obter novos tokens para outras APIs web:

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

Para obter mais informações sobre os parâmetros de consulta no URL, consulte [enviar o sinal a pedido](#send-the-sign-in-request).

> [!TIP]
> Experimente copiar & colar o pedido abaixo num separador de navegador! (Não se esqueça de substituir os `login_hint` valores pelo valor correto para o seu utilizador)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>
> Note que isso funcionará mesmo em navegadores sem suporte a cookies de terceiros, uma vez que você está inserindo isso diretamente em uma barra de navegador em oposição a abri-lo dentro de um iframe. 

Graças ao `prompt=none` parâmetro, este pedido terá sucesso ou falhará imediatamente e regressará à sua aplicação. A resposta será enviada para a sua aplicação no `redirect_uri` indicado, utilizando o método especificado no `response_mode` parâmetro.

#### <a name="successful-response"></a>Resposta bem sucedida

Uma resposta bem sucedida `response_mode=fragment` parece:

```HTTP
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| Parâmetro | Descrição |
| --- | --- |
| `access_token` |Incluído se `response_type` inclui `token` . O sinal de acesso que a aplicação solicitou, neste caso para o Microsoft Graph. O token de acesso não deve ser descodificado ou inspecionado de outra forma, deve ser tratado como uma corda opaca. |
| `token_type` | Sempre `Bearer` será. |
| `expires_in` | Indica o número de segundos em que o token é válido, para efeitos de caching. |
| `scope` | Indica o(s) âmbito(s) para o qual o access_token será válido. Não podem incluir todos os âmbitos solicitados, se não forem aplicáveis ao utilizador (no caso de âmbitos apenas AZure AD solicitados quando uma conta pessoal é utilizada para iniciar sessão). |
| `id_token` | Um JSON Web Token assinado (JWT). Incluído se `response_type` inclui `id_token` . A aplicação pode descodificar os segmentos deste token para solicitar informações sobre o utilizador que se inscreveu. A aplicação pode cache os valores e exibi-los, mas não deve confiar neles para qualquer autorização ou limites de segurança. Para mais informações sobre id_tokens, consulte a [ `id_token` referência](id-tokens.md). <br> **Nota:** Só for fornecida se `openid` o âmbito de aplicação for solicitado. |
| `state` |Se um parâmetro de estado for incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deve verificar se os valores do Estado no pedido e resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro também podem ser enviadas para `redirect_uri` o modo como a aplicação pode manuseá-las adequadamente. No caso de `prompt=none` , um erro esperado será:

```HTTP
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | Descrição |
| --- | --- |
| `error` |Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usado para reagir a erros. |
| `error_description` |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro de autenticação. |

Se receber este erro no pedido do iframe, o utilizador deve iniciar serção interativamente para recuperar um novo token. Pode optar por lidar com este caso da forma que fizer sentido para a sua aplicação.

## <a name="refreshing-tokens"></a>Fichas refrescantes

A subvenção implícita não fornece fichas de atualização. Tanto `id_token` s como s `access_token` expirarão após um curto período de tempo, pelo que a sua aplicação deve estar preparada para atualizar estes tokens periodicamente. Para atualizar qualquer tipo de token, pode executar o mesmo pedido de iframe oculto de cima usando o `prompt=none` parâmetro para controlar o comportamento da plataforma de identidade. Se quiser receber um novo `id_token` , certifique-se de usar `id_token` no `response_type` `scope=openid` e, bem como um `nonce` parâmetro.

Nos navegadores que não suportam cookies de terceiros, isso resultará num erro que indica que nenhum utilizador está inscrito. 

## <a name="send-a-sign-out-request"></a>Enviar um pedido de sinalização

O OpenID Connect `end_session_endpoint` permite que a sua aplicação envie um pedido à plataforma de identidade da Microsoft para terminar a sessão de um utilizador e limpar cookies definidos pela plataforma de identidade microsoft. Para assinar totalmente um utilizador a partir de uma aplicação web, a sua aplicação deve terminar a sua própria sessão com o utilizador (normalmente limpando uma cache simbólica ou largando cookies), e, em seguida, redirecionar o navegador para:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parâmetro | Tipo | Description |
| --- | --- | --- |
| `tenant` |obrigatório |O `{tenant}` valor no caminho do pedido pode ser usado para controlar quem pode assinar a aplicação. Os valores permitidos `common` `organizations` são, `consumers` e os identificadores de inquilinos. Para mais detalhes, consulte [o protocolo básico.](active-directory-v2-protocols.md#endpoints) |
| `post_logout_redirect_uri` | recomendado | O URL a que o utilizador deve ser devolvido após o início de sê-lo. Este valor deve corresponder a um dos URIs de redirecionamento registados para a aplicação. Se não estiver incluído, o utilizador será mostrado uma mensagem genérica pela plataforma de identidade da Microsoft. |

## <a name="next-steps"></a>Passos seguintes

* Ver as amostras do [MSAL JS](sample-v2-code.md) para começar a codificar.
* Reveja o [fluxo de código](v2-oauth2-auth-code-flow.md) de autorização como uma alternativa mais recente e melhor à concessão implícita. 
