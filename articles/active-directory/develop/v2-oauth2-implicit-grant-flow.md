---
title: OAuth 2.0 fluxo de subvenção implícita - plataforma de identidade da Microsoft Rio Azure
description: Proteja as aplicações de uma página única utilizando o fluxo implícito da plataforma de identidade da Microsoft.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: fbe74b62352babf7a1fdd93bf19a6e1475e3f032
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85553580"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Plataforma de identidade da Microsoft e fluxo de subvenção implícita

Com o ponto final da plataforma de identidade da Microsoft, pode inscrever os utilizadores nas suas aplicações de página única com contas pessoais e de trabalho ou escolares da Microsoft. Aplicações de página única e outras aplicações JavaScript que funcionam principalmente num navegador enfrentam alguns desafios interessantes quando se trata de autenticação:

* As características de segurança destas aplicações são significativamente diferentes das aplicações web tradicionais baseadas em servidores.
* Muitos servidores de autorização e fornecedores de identidade não suportam pedidos de CORS.
* Os redirecionamentos de navegador de página inteira para longe da aplicação tornam-se particularmente invasivos para a experiência do utilizador.

Para estas aplicações (Angular, Ember.js, React.js, e assim por diante), a plataforma de identidade da Microsoft suporta o fluxo de subvenção implícita OAuth 2.0. O fluxo implícito é descrito na [Especificação OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). O seu principal benefício é que permite que a app obtenha fichas da plataforma de identidade da Microsoft sem realizar uma troca de credenciais de backend server. Isto permite que a aplicação assine no utilizador, mantenha a sessão e obtenha fichas para outras APIs web, tudo dentro do código JavaScript do cliente. Existem algumas considerações de segurança importantes a ter em conta ao utilizar o fluxo implícito especificamente em torno da personificação do [cliente](https://tools.ietf.org/html/rfc6749#section-10.3) e [do utilizador.](https://tools.ietf.org/html/rfc6749#section-10.3)

Este artigo descreve como programar diretamente contra o protocolo na sua aplicação.  Quando possível, recomendamos que utilize as Bibliotecas de Autenticação da Microsoft (MSAL) suportadas para [adquirir fichas e chamar APIs web seguras](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Dê também uma olhada nas aplicações de [amostra que utilizam o MSAL.](sample-v2-code.md)

No entanto, se preferir não utilizar uma biblioteca na sua aplicação de página única e enviar mensagens de protocolo, siga os passos gerais abaixo.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Cenários adequados para a subvenção implícita OAuth2

A especificação OAuth2 declara que a subvenção implícita foi concebida para permitir aplicações de agente de utilizador – isto é, aplicações JavaScript executadas dentro de um browser. A característica determinante de tais aplicações é que o código JavaScript é usado para aceder a recursos do servidor (normalmente uma API web) e para atualizar a experiência do utilizador da aplicação em conformidade. Pense em aplicações como o Gmail ou o Outlook Web Access: quando seleciona uma mensagem na sua caixa de entrada, apenas o painel de visualização de mensagens muda para exibir a nova seleção, enquanto o resto da página permanece desmodificado. Esta característica contrasta com as aplicações web tradicionais baseadas em redirecionamento, onde cada interação do utilizador resulta num postback de página inteira e numa renderização de página inteira da nova resposta do servidor.

As aplicações que tomam a abordagem baseada em JavaScript para o seu extremo são chamadas aplicações de uma página única, ou SPAs. A ideia é que estas aplicações apenas sirvam uma página html inicial e JavaScript associado, com todas as interações subsequentes sendo conduzidas por chamadas API web realizadas via JavaScript. No entanto, as abordagens híbridas, em que a aplicação é maioritariamente orientada para o pós-retrocesso, mas realiza chamadas ocasionais de JS, não são incomuns – a discussão sobre o uso implícito do fluxo também é relevante para aqueles.

As aplicações baseadas em redirecionamento normalmente asseguram os seus pedidos através de cookies, no entanto, essa abordagem não funciona tão bem para aplicações JavaScript. Os cookies apenas funcionam contra o domínio para o quais foram gerados, enquanto as chamadas JavaScript podem ser direcionadas para outros domínios. De facto, isso será frequentemente o caso: pense em aplicações que invocam a Microsoft Graph API, Office API, Azure API – todas residentes fora do domínio de onde a aplicação é servida. Uma tendência crescente para aplicações JavaScript é não ter qualquer backend, contando 100% em APIs web de terceiros para implementar a sua função de negócio.

Atualmente, o método preferido para proteger as chamadas para uma API web é utilizar a abordagem simbólica portadora de OAuth2, onde cada chamada é acompanhada por um token de acesso OAuth2. A API web examina o token de acesso de entrada e, se encontrar nele os âmbitos necessários, concede acesso à operação solicitada. O fluxo implícito fornece um mecanismo conveniente para aplicações JavaScript para obter tokens de acesso para uma API web, oferecendo inúmeras vantagens em relação aos cookies:

* Os tokens podem ser obtidos de forma fiável sem necessidade de chamadas de origem cruzada – registo obrigatório do URI redirecionado para o qual os tokens são devolução garantias de que os tokens não são deslocados
* As aplicações JavaScript podem obter o número de tokens de acesso que precisarem, para o maior número de APIs web que visam – sem restrições em domínios
* As funcionalidades HTML5, como sessão ou armazenamento local, concedem controlo total sobre o caching token e a gestão vitalícia, enquanto a gestão de cookies é opaca para a app
* Os tokens de acesso não são suscetíveis a ataques de falsificação de pedidos de cross-site (CSRF)

O fluxo implícito de subvenções não emite fichas de atualização, principalmente por razões de segurança. Um token de atualização não é tão alargado como os tokens de acesso, concedendo muito mais energia, portanto, infligindo muito mais danos no caso de ser vazado. No fluxo implícito, os tokens são entregues na URL, pelo que o risco de interceção é maior do que no código de autorização.

No entanto, uma aplicação JavaScript tem outro mecanismo à sua disposição para renovar fichas de acesso sem solicitar repetidamente ao utilizador credenciais. A aplicação pode usar um iframe oculto para realizar novos pedidos simbólicos contra o ponto final de autorização do Azure AD: desde que o navegador ainda tenha uma sessão ativa (leia-se: tem um cookie de sessão) contra o domínio Azure AD, o pedido de autenticação pode ocorrer com sucesso sem qualquer necessidade de interação do utilizador.

Este modelo confere à aplicação JavaScript a capacidade de renovar independentemente os tokens de acesso e até adquirir novos para uma nova API (desde que o utilizador tenha previamente consentido por eles). Isto evita o fardo adicional de adquirir, manter e proteger um artefacto de alto valor, como um token de atualização. O artefacto que torna possível a renovação silenciosa, o cookie de sessão Azure AD, é gerido fora da aplicação. Outra vantagem desta abordagem é que um utilizador pode assinar a partir do Azure AD, utilizando qualquer uma das aplicações assinadas no AZure AD, correndo em qualquer um dos separadores do navegador. Isto resulta na eliminação do cookie de sessão Ad Azure, e a aplicação JavaScript perderá automaticamente a capacidade de renovar fichas para o utilizador assinado.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>A subvenção implícita é adequada para a minha aplicação?

A subvenção implícita apresenta mais riscos do que outras subvenções, e as áreas a que deve prestar atenção estão bem documentadas (por exemplo, [utilização indevida de Token de Acesso a Proprietário de Recursos Imitadores em Fluxo Implícito][OAuth2-Spec-Implicit-Misuse] e [OAuth 2.0 Modelo de Ameaça e Considerações de Segurança).][OAuth2-Threat-Model-And-Security-Implications] No entanto, o maior perfil de risco deve-se em grande parte ao facto de se destinar a ativar aplicações que executam código ativo, servido por um recurso remoto para um navegador. Se estiver a planear uma arquitetura SPA, não tiver componentes de backend ou pretender invocar uma API web via JavaScript, recomenda-se a utilização do fluxo implícito para a aquisição de token.

Se a sua aplicação é um cliente nativo, o fluxo implícito não é um grande ajuste. A ausência do cookie de sessão Azure AD no contexto de um cliente nativo priva a sua aplicação dos meios de manter uma sessão de longa duração. O que significa que a sua aplicação irá repetidamente incitar o utilizador ao obter fichas de acesso para novos recursos.

Se estiver a desenvolver uma aplicação Web que inclua um backend e a consumir uma API a partir do seu código backend, o fluxo implícito também não é um bom ajuste. Outras subvenções dão-lhe muito mais poder. Por exemplo, a concessão de credenciais de clienteS OAuth2 proporciona a capacidade de obter fichas que reflitam as permissões atribuídas à própria aplicação, em oposição às delegações de utilizadores. Isto significa que o cliente tem a capacidade de manter o acesso programático aos recursos mesmo quando um utilizador não está ativamente envolvido numa sessão, e assim por diante. Não só isso, mas tais subvenções dão garantias de segurança mais elevadas. Por exemplo, os tokens de acesso nunca passam pelo navegador do utilizador, não correm o risco de serem guardados no histórico do navegador, e assim por diante. A aplicação do cliente também pode realizar autenticação forte ao solicitar um token.

[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama seguinte mostra como é todo o fluxo de entrada implícita e as secções que se seguem descrevem cada passo com mais detalhes.

![Diagrama mostrando o fluxo implícito de entrada](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Envie o pedido de inscrição

Para inicialmente iniciar a inscrição do utilizador na sua aplicação, pode enviar um pedido de autenticação [OpenID Connect](v2-protocols-oidc.md) e obter um `id_token` a partir do ponto final da plataforma de identidade da Microsoft.

> [!IMPORTANT]
> Para solicitar com sucesso um token de ID e/ou um token de acesso, o registo da aplicação no [portal Azure - Página de registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) deve ter o fluxo de subvenção implícita correspondente, selecionando fichas de **identificação** e.ou **fichas de acesso ao** abrigo da secção de **subvenção Implícita.** Se não estiver ativado, será devolvido um `unsupported_response` erro: **O valor fornecido para o parâmetro de entrada 'response_type' não é permitido para este cliente. Valor esperado é 'código'**

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

| Parâmetro | Tipo | Descrição |
| --- | --- | --- |
| `tenant` | obrigatório |O `{tenant}` valor no caminho do pedido pode ser usado para controlar quem pode assinar a aplicação. Os valores permitidos `common` `organizations` são, `consumers` e os identificadores de inquilinos. Para mais detalhes, consulte [o protocolo básico.](active-directory-v2-protocols.md#endpoints) |
| `client_id` | obrigatório | O ID da Aplicação (cliente) que o [portal Azure - Página de registos de aplicações](https://go.microsoft.com/fwlink/?linkid=2083908) atribuído à sua app. |
| `response_type` | obrigatório |Deve incluir `id_token` para o iniciar sê-in OpenID Connect. Também pode incluir o response_type `token` . A utilização `token` aqui permitirá que a sua aplicação receba imediatamente um token de acesso a partir do ponto final autorizado sem ter de fazer um segundo pedido ao ponto final autorizado. Se utilizar o `token` response_type, o `scope` parâmetro deve conter um âmbito que indique qual o recurso para emitir o token (por exemplo, user.read no Microsoft Graph).  |
| `redirect_uri` | recomendado |O redirect_uri da sua app, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a uma das redirect_uris que registou no portal, exceto que deve estar codificada url. |
| `scope` | obrigatório |Uma lista de [âmbitos separados](v2-permissions-and-consent.md)pelo espaço. Para o OpenID Connect (id_tokens), deve incluir o `openid` âmbito, que se traduz na permissão "Iniciar a sua inscrição" na UI de consentimento. Opcionalmente, também pode querer incluir os `email` `profile` e âmbitos para o acesso a dados adicionais do utilizador. Você também pode incluir outros âmbitos neste pedido de pedido de consentimento para vários recursos, se for solicitado um token de acesso. |
| `response_mode` | opcional |Especifica o método que deve ser usado para enviar o símbolo resultante de volta para a sua aplicação. Predefinições para consulta para apenas um token de acesso, mas fragmento se o pedido inclui uma id_token. |
| `state` | recomendado |Um valor incluído no pedido que também será devolvido na resposta simbólica. Pode ser uma série de conteúdos que desejes. Um valor único gerado aleatoriamente é normalmente usado para [prevenir ataques de falsificação de pedidos de trans-locais](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou a vista em que estavam. |
| `nonce` | obrigatório |Um valor incluído no pedido, gerado pela app, que será incluído no id_token resultante como reclamação. A aplicação pode então verificar este valor para mitigar os ataques de reprodução de token. O valor é tipicamente uma corda aleatória e única que pode ser usada para identificar a origem do pedido. Só é necessário quando é solicitada uma id_token. |
| `prompt` | opcional |Indica o tipo de interação do utilizador que é necessária. Os únicos valores válidos neste momento são 'login', 'nenhum', 'select_account', e 'consentimento'. `prompt=login` forçará o utilizador a introduzir as suas credenciais nesse pedido, negando um único sinal. `prompt=none` é o oposto - irá garantir que o utilizador não é apresentado com qualquer posição interativa. Se o pedido não puder ser concluído silenciosamente através de um único sinal, o ponto final da plataforma de identidade da Microsoft retornará um erro. `prompt=select_account` envia o utilizador para um selecionador de contas onde todas as contas lembradas na sessão aparecerão. `prompt=consent` irá acionar o diálogo de consentimento OAuth após o utilizador entrar, pedindo ao utilizador que conceda permissões à aplicação. |
| `login_hint`  |opcional |Pode ser usado para pré-preenchimento do nome de utilizador/endereço de e-mail do sinal na página para o utilizador, se souber o seu nome de utilizador com antecedência. Muitas vezes as aplicações utilizam este parâmetro durante a reautorização, tendo já extraído o nome de utilizador de um pré-início de súmato utilizando a `preferred_username` reclamação.|
| `domain_hint` | opcional |Se incluído, saltará o processo de descoberta baseado em e-mail que o utilizador passa no sinal na página, levando a uma experiência de utilizador um pouco mais simplificada. Isto é comumente usado para aplicativos line of business que operam em um único inquilino, onde eles fornecerão um nome de domínio dentro de um determinado inquilino.  Isto irá encaminhar o utilizador para o provedor da federação para aquele inquilino.  Note que isso impedirá os hóspedes de se inscreverem nesta aplicação.  |

Neste momento, o utilizador será convidado a introduzir as suas credenciais e a completar a autenticação. O ponto final da plataforma de identidade da Microsoft também garantirá que o utilizador consentiu nas permissões indicadas no `scope` parâmetro de consulta. Se o utilizador não tiver consentido **em nenhuma** dessas permissões, solicitará ao utilizador que consinta nas permissões necessárias. Para mais informações, consulte [permissões, consentimento e aplicações multi-arrendantes.](v2-permissions-and-consent.md)

Assim que o utilizador autenticar e conceder o consentimento, o ponto final da plataforma de identidade da Microsoft devolverá uma resposta à sua aplicação no indicado `redirect_uri` , utilizando o método especificado no `response_mode` parâmetro.

#### <a name="successful-response"></a>Resposta bem sucedida

Uma resposta bem sucedida utilizando `response_mode=fragment` e parece o seguinte `response_type=id_token+token` (com quebras de linha para legibilidade):

```HTTP
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| `access_token` |Incluído se `response_type` inclui `token` . O sinal de acesso que a app solicitou. O token de acesso não deve ser descodificado ou inspecionado de outra forma, deve ser tratado como uma corda opaca. |
| `token_type` |Incluído se `response_type` inclui `token` . Sempre `Bearer` será. |
| `expires_in`|Incluído se `response_type` inclui `token` . Indica o número de segundos em que o token é válido, para efeitos de caching. |
| `scope` |Incluído se `response_type` inclui `token` . Indica o(s) âmbito(s) para o qual o access_token será válido. Não podem incluir todos os âmbitos solicitados, se não forem aplicáveis ao utilizador (no caso de âmbitos apenas AZure AD solicitados quando uma conta pessoal é utilizada para iniciar sessão). |
| `id_token` | Um JSON Web Token assinado (JWT). A aplicação pode descodificar os segmentos deste token para solicitar informações sobre o utilizador que se inscreveu. A aplicação pode cache os valores e exibi-los, mas não deve confiar neles para qualquer autorização ou limites de segurança. Para mais informações sobre id_tokens, consulte o [`id_token reference`](id-tokens.md) . <br> **Nota:** Só for fornecida se `openid` o âmbito de aplicação for solicitado. |
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

Agora que inscreveu o utilizador na sua aplicação de uma página única, pode aceder silenciosamente a tokens para chamar APIs da web protegidos pela plataforma de identidade microsoft, como o [Microsoft Graph](https://developer.microsoft.com/graph). Mesmo que já tenha recebido um token usando o `token` response_type, pode usar este método para adquirir fichas para recursos adicionais sem ter que redirecionar o utilizador para iniciar novamente o súmis.

No fluxo normal de OpenID Connect/OAuth, faria-o fazendo um pedido ao ponto final da plataforma de identidade da `/token` Microsoft. No entanto, o ponto final da plataforma de identidade da Microsoft não suporta pedidos de CORS, pelo que fazer chamadas AJAX para obter e refrescar tokens está fora de questão. Em vez disso, pode usar o fluxo implícito num iframe oculto para obter novos tokens para outras APIs web:

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

Graças ao `prompt=none` parâmetro, este pedido terá sucesso ou falhará imediatamente e regressará à sua aplicação. Será enviada uma resposta bem sucedida para a sua aplicação no `redirect_uri` indicado, utilizando o método especificado no `response_mode` parâmetro.

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

## <a name="send-a-sign-out-request"></a>Enviar um pedido de sinalização

O OpenID Connect `end_session_endpoint` permite que a sua aplicação envie um pedido para o ponto final da plataforma de identidade da Microsoft para terminar a sessão de um utilizador e cookies claros definidos pelo ponto final da plataforma de identidade da Microsoft. Para assinar totalmente um utilizador a partir de uma aplicação web, a sua aplicação deve terminar a sua própria sessão com o utilizador (normalmente limpando uma cache simbólica ou largando cookies), e, em seguida, redirecionar o navegador para:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parâmetro | Tipo | Descrição |
| --- | --- | --- |
| `tenant` |obrigatório |O `{tenant}` valor no caminho do pedido pode ser usado para controlar quem pode assinar a aplicação. Os valores permitidos `common` `organizations` são, `consumers` e os identificadores de inquilinos. Para mais detalhes, consulte [o protocolo básico.](active-directory-v2-protocols.md#endpoints) |
| `post_logout_redirect_uri` | recomendado | O URL a que o utilizador deve ser devolvido após o início de sê-lo. Este valor deve corresponder a um dos URIs de redirecionamento registados para a aplicação. Se não estiver incluído, o utilizador será mostrado uma mensagem genérica pelo ponto final da plataforma de identidade da Microsoft. |

## <a name="next-steps"></a>Passos seguintes

* Ver as amostras do [MSAL JS](sample-v2-code.md) para começar a codificar.

[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
