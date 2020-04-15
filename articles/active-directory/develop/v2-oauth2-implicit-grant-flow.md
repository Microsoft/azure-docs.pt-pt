---
title: OAuth 2.0 fluxo implícito de subvenção - plataforma de identidade da Microsoft Azure
description: Proteja aplicativos de uma página usando o fluxo implícito da plataforma de identidade microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 0a884850d57418e9daafba980d0a08dc86fc0974
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309397"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Plataforma de identidade da Microsoft e fluxo de subvenção implícita

Com o ponto final da plataforma de identidade da Microsoft, pode inscrever os utilizadores nas suas aplicações de uma página única com contas pessoais e de trabalho ou escolares da Microsoft. As aplicações JavaScript de página única e outras que funcionam principalmente num navegador enfrentam alguns desafios interessantes no que diz respeito à autenticação:

* As características de segurança destas aplicações são significativamente diferentes das aplicações web tradicionais baseadas em servidores.
* Muitos servidores de autorização e fornecedores de identidade não suportam pedidos CORS.
* Os redirecionamentos do navegador de página inteira para longe da app tornam-se particularmente invasivos para a experiência do utilizador.

Para estas aplicações (Angular, Ember.js, React.js, e assim por diante), a plataforma de identidade da Microsoft suporta o fluxo de Subvenção Implícita OAuth 2.0. O fluxo implícito é descrito na [especificação OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.2). O seu principal benefício é que permite que a app obtenha fichas da plataforma de identidade da Microsoft sem realizar uma troca de credenciais de servidor de backend. Isto permite que a aplicação assine no utilizador, mantenha a sessão e obtenha fichas para outras APIs web, todas dentro do código JavaScript do cliente. Existem algumas considerações importantes de segurança a ter em conta ao utilizar o fluxo implícito especificamente em torno da personificação do [cliente](https://tools.ietf.org/html/rfc6749#section-10.3) e do [utilizador.](https://tools.ietf.org/html/rfc6749#section-10.3)

Este artigo descreve como programar diretamente contra o protocolo na sua aplicação.  Sempre que possível, recomendamos que utilize as Bibliotecas de Autenticação da Microsoft (MSAL) suportadas em vez de adquirir fichas e ligar para [APIs web protegidos](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows).  Veja também as [aplicações de amostra que utilizam o MSAL.](sample-v2-code.md)

No entanto, se preferir não usar uma biblioteca na sua aplicação de uma página única e enviar mensagens protocolares, siga os passos gerais abaixo.

> [!NOTE]
> Nem todos os cenários e funcionalidades do Azure Ative Directory (Azure AD) são suportados pelo ponto final da plataforma de identidade da Microsoft. Para determinar se deve utilizar o ponto final da plataforma de identidade da Microsoft, leia sobre [as limitações](active-directory-v2-limitations.md)da plataforma de identidade da Microsoft .

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Cenários adequados para a subvenção implícita OAuth2

A especificação OAuth2 declara que a subvenção implícita foi concebida para permitir aplicações de agente de utilizador – ou seja, aplicações JavaScript executadas dentro de um browser. A característica determinante de tais aplicações é que o código JavaScript é usado para aceder aos recursos do servidor (tipicamente um API Web) e para atualizar a experiência do utilizador da aplicação em conformidade. Pense em aplicações como o Gmail ou o Outlook Web Access: quando selecionar uma mensagem da sua caixa de entrada, apenas o painel de visualização de mensagens muda para exibir a nova seleção, enquanto o resto da página permanece inalterado. Esta característica contrasta com as aplicações Web tradicionais baseadas em redireciones, onde cada interação do utilizador resulta num postback de página inteira e numa renderização de página inteira da nova resposta do servidor.

As aplicações que tomam a abordagem baseada no JavaScript ao seu extremo são chamadas aplicações de página única, ou SPAs. A ideia é que estas aplicações apenas sirvam uma página inicial de HTML e javaScript associado, com todas as interações subsequentes sendo conduzidas por chamadas Web API realizadas via JavaScript. No entanto, as abordagens híbridas, em que a aplicação é maioritariamente orientada para o pós-recuo, mas executam chamadas js ocasionais, não são incomuns – a discussão sobre o uso implícito do fluxo também é relevante para aqueles.

As aplicações baseadas em redirecionamento normalmente asseguram os seus pedidos através de cookies, no entanto, essa abordagem não funciona tão bem para aplicações JavaScript. Os cookies só funcionam contra o domínio para o que foram gerados, enquanto as chamadas JavaScript podem ser direcionadas para outros domínios. De facto, isso será frequentemente o caso: pense em aplicações que invocam a Microsoft Graph API, Office API, Azure API – todos residentes fora do domínio de onde a aplicação é servida. Uma tendência crescente para as aplicações JavaScript é não ter qualquer backend, confiando 100% em APIs web de terceiros para implementar a sua função de negócio.

Atualmente, o método preferido de proteger chamadas para uma API Web é usar a abordagem token do portador OAuth2, onde cada chamada é acompanhada por um sinal de acesso OAuth2. A Web API examina o sinal de acesso de entrada e, se encontrar nele os âmbitos necessários, dá acesso à operação solicitada. O fluxo implícito fornece um mecanismo conveniente para as aplicações JavaScript obterem fichas de acesso para uma API Web, oferecendo inúmeras vantagens em relação aos cookies:

* Os tokens podem ser obtidos de forma fiável sem qualquer necessidade de chamadas de origem cruzada – registo obrigatório do URI redirecionado para o qual as fichas são de retorno garante que as fichas não são deslocadas
* As aplicações JavaScript podem obter o máximo de fichas de acesso que precisarem, para o maior número de APIs web que visam – sem restrições em domínios
* Características HTML5 como sessão ou bolsa de armazenamento local concedem controlo total sobre o caching simbólico e a gestão vitalícia, enquanto a gestão de cookies é opaca para a app
* Fichas de acesso não são suscetíveis a ataques de falsificação de pedido de cross-site (CSRF)

O fluxo implícito de subvenção não emite fichas de atualização, principalmente por razões de segurança. Um token refrescante não é tão estreitamente traçado como fichas de acesso, concedendo muito mais energia, permitindo muito mais danos no caso de ser vazado. No fluxo implícito, as fichas são entregues no URL, daí que o risco de interceção seja maior do que na concessão do código de autorização.

No entanto, uma aplicação JavaScript tem outro mecanismo à sua disposição para renovar fichas de acesso sem solicitar repetidamente ao utilizador credenciais. A aplicação pode utilizar um iframe oculto para realizar novos pedidos simbólicos contra o ponto final de autorização do Azure AD: desde que o navegador ainda tenha uma sessão ativa (leia-se: tem um cookie de sessão) contra o domínio DaA Azure, o pedido de autenticação pode ocorrer com sucesso sem qualquer necessidade de interação do utilizador.

Este modelo concede à aplicação JavaScript a capacidade de renovar independentemente os tokens de acesso e até adquirir novos para uma nova API (desde que o utilizador previamente consentiu por eles). Isto evita o fardo acrescido de adquirir, manter e proteger um artefacto de alto valor, como um símbolo de atualização. O artefacto que torna possível a renovação silenciosa, o cookie de sessão Azure AD, é gerido fora da aplicação. Outra vantagem desta abordagem é que um utilizador pode assinar a partir do Azure AD, utilizando qualquer uma das aplicações assinadas no Azure AD, funcionando em qualquer um dos separadores do navegador. Isto resulta na eliminação do cookie de sessão Azure AD, e a aplicação JavaScript perderá automaticamente a capacidade de renovar fichas para o utilizador assinado.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>A subvenção implícita é adequada para a minha aplicação?

A subvenção implícita apresenta mais riscos do que outras subvenções, e as áreas a que precisa de estar atento estão bem documentadas (por exemplo, [utilização indevida de acesso ao Proprietário de Recursos Imitadores em Fluxo Implícito][OAuth2-Spec-Implicit-Misuse] e [OAuth 2.0 Modelo][OAuth2-Threat-Model-And-Security-Implications]de Ameaça e Considerações de Segurança). No entanto, o perfil de risco mais elevado deve-se, em grande parte, ao facto de se destinar a ativar aplicações que executam código ativo, servidos por um recurso remoto para um navegador. Se estiver a planear uma arquitetura SPA, não tenha componentes de backend ou pretenda invocar um Web API via JavaScript, recomenda-se a utilização do fluxo implícito para aquisição de fichas.

Se a sua aplicação é um cliente nativo, o fluxo implícito não é um grande ajuste. A ausência do cookie de sessão Azure AD no contexto de um cliente nativo priva a sua aplicação dos meios de manter uma sessão de longa duração. O que significa que a sua aplicação irá incitar repetidamente o utilizador a obter fichas de acesso para novos recursos.

Se estiver a desenvolver uma aplicação Web que inclua um backend, e a consumir uma API do seu código de backend, o fluxo implícito também não é um bom ajuste. Outras subvenções dão-lhe muito mais poder. Por exemplo, a concessão de credenciais de cliente OAuth2 fornece a capacidade de obter fichas que reflitam as permissões atribuídas à própria aplicação, ao contrário das delegações dos utilizadores. Isto significa que o cliente tem a capacidade de manter o acesso programático aos recursos mesmo quando um utilizador não está ativamente envolvido numa sessão, e assim por diante. Não só isso, mas tais subvenções dão garantias de segurança mais elevadas. Por exemplo, os tokens de acesso nunca transitam através do navegador de utilizador, não correm o risco de ser guardados no histórico do navegador, e assim por diante. A aplicação do cliente também pode realizar uma autenticação forte ao solicitar um símbolo.

[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819

## <a name="protocol-diagram"></a>Diagrama de protocolo

O diagrama seguinte mostra como é todo o fluxo implícito de entrada e as secções que se seguem descrevem cada passo com mais detalhes.

![Diagrama mostrando o fluxo implícito de entrada](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>Envie o pedido de inscrição

Para inicialmente assinar o utilizador na sua aplicação, pode enviar `id_token` um pedido de autenticação [OpenID Connect](v2-protocols-oidc.md) e obter um ponto final da plataforma de identidade da Microsoft.

> [!IMPORTANT]
> Para solicitar com sucesso um token de identificação e/ou um token de acesso, o registo da aplicação no [portal Azure - Página](https://go.microsoft.com/fwlink/?linkid=2083908) de registos de aplicações deve ter o fluxo implícito de subvenção correspondente habilitado, selecionando **tokens** de ID e.ou **fichas** de acesso ao abrigo da secção **de subvenção implícita.** Se não estiver ativado, `unsupported_response` será devolvido um erro: O valor fornecido para o parâmetro de **entrada 'response_type' não é permitido para este cliente. Valor esperado é 'código'**

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
> Para testar a assinatura utilizando o fluxo implícito, clique <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank"> https://login.microsoftonline.com/common/oauth2/v2.0/authorize..em .</a> Após a essão, o seu navegador `https://localhost/myapp/` deve `id_token` ser redirecionado para um na barra de endereços.
>

| Parâmetro |  | Descrição |
| --- | --- | --- |
| `tenant` | necessário |O `{tenant}` valor no caminho do pedido pode ser usado para controlar quem pode assinar a aplicação. Os valores `common` `organizations`permitidos são, e `consumers`identificadores de inquilinos. Para mais detalhes, consulte [os fundamentos do protocolo.](active-directory-v2-protocols.md#endpoints) |
| `client_id` | necessário | O ID de Aplicação (cliente) que o [portal Azure - Página](https://go.microsoft.com/fwlink/?linkid=2083908) de registos de aplicações atribuída à sua app. |
| `response_type` | necessário |Deve `id_token` incluir o acesso ao OpenID Connect. Pode também incluir `token`o response_type. A `token` utilização aqui permitirá que a sua aplicação receba um sinal de acesso imediatamente a partir do ponto final autorizado sem ter de fazer um segundo pedido para o ponto final de autorização. Se utilizar `token` o response_type, o `scope` parâmetro deve conter um âmbito que indique para que recurso emitir o símbolo (por exemplo, user.read no Microsoft Graph).  |
| `redirect_uri` | recomendado |O redirect_uri da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a uma das redirect_uris que registou no portal, exceto que deve ser codificada. |
| `scope` | necessário |Uma lista de [âmbitos separados](v2-permissions-and-consent.md)pelo espaço. Para o OpenID Connect (id_tokens), `openid`deve incluir o âmbito, que se traduz na permissão "Iniciar sessão" na UI de consentimento. Opcionalmente, também pode querer `email` `profile` incluir os e âmbitos para ter acesso a dados adicionais do utilizador. Pode também incluir outros âmbitos neste pedido de pedido de consentimento para vários recursos, caso seja solicitado um sinal de acesso. |
| `response_mode` | opcional |Especifica o método que deve ser usado para enviar o símbolo resultante de volta para a sua aplicação. Incumprimentos à consulta por apenas um sinal de acesso, mas fragmentado se o pedido incluir um id_token. |
| `state` | recomendado |Um valor incluído no pedido que também será devolvido na resposta simbólica. Pode ser uma série de qualquer conteúdo que desejes. Um valor único gerado aleatoriamente é normalmente usado para [prevenir ataques de falsificação de pedidos de local.](https://tools.ietf.org/html/rfc6749#section-10.12) O Estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou visualização em que estavam. |
| `nonce` | necessário |Um valor incluído no pedido, gerado pela app, que será incluído na id_token resultante como reivindicação. A aplicação pode então verificar este valor para mitigar ataques de repetição de tokens. O valor é tipicamente uma cadeia aleatória e única que pode ser usada para identificar a origem do pedido. Só é necessário quando é solicitado um id_token. |
| `prompt` | opcional |Indica o tipo de interação do utilizador que é necessária. Os únicos valores válidos neste momento são 'login', 'nenhum', 'select_account' e 'consentimento'. `prompt=login`obrigará o utilizador a introduzir as suas credenciais nesse pedido, negando um único sinal. `prompt=none`é o oposto - irá garantir que o utilizador não seja apresentado com qualquer tipo de solicitação interativa. Se o pedido não puder ser concluído silenciosamente através de um único sinal, o ponto final da plataforma de identidade da Microsoft devolverá um erro. `prompt=select_account`envia o utilizador para um apanhador de conta onde todas as contas lembradas na sessão aparecerão. `prompt=consent`irá desencadear o diálogo de consentimento da OAuth após a assinatura do utilizador, pedindo ao utilizador que conceda permissões à aplicação. |
| `login_hint`  |opcional |Pode ser usado para pré-preencher o campo de endereço sinuoso/e-mail da página do utilizador, se souber o seu nome de utilizador com antecedência. Muitas vezes as aplicações usam este parâmetro durante a reautenticação, tendo `preferred_username` já extraído o nome de utilizador de um início de sessão anterior utilizando a reclamação.|
| `domain_hint` | opcional |Se incluído, irá ignorar o processo de descoberta baseado em e-mail que o utilizador passa no sinal na página, levando a uma experiência de utilizador um pouco mais simplificada. Isto é comumente usado para apps Line of Business que operam em um único inquilino, onde eles fornecerão um nome de domínio dentro de um determinado inquilino.  Isto irá encaminhar o utilizador para o provedor da federação para aquele inquilino.  Note que isso impedirá os hóspedes de assinar em esta aplicação.  |

Neste momento, o utilizador será solicitado a introduzir as suas credenciais e a completar a autenticação. O ponto final da plataforma de identidade da Microsoft também garantirá `scope` que o utilizador consentiu nas permissões indicadas no parâmetro de consulta. Se o utilizador tiver consentido **com nenhuma** dessas permissões, pedirá ao utilizador que consinta nas permissões necessárias. Para mais informações, consulte [permissões, consentimento e aplicações multi-inquilinos.](v2-permissions-and-consent.md)

Assim que o utilizador autenticar e conceder o consentimento, o ponto final `redirect_uri`da plataforma de identidade `response_mode` da Microsoft devolverá uma resposta à sua aplicação no indicado , utilizando o método especificado no parâmetro.

#### <a name="successful-response"></a>Resposta bem sucedida

Uma resposta `response_mode=fragment` bem `response_type=id_token+token` sucedida usando e se parece com o seguinte (com quebras de linha para a legibilidade):

```
GET https://localhost/myapp/#
&token_type=Bearer
&expires_in=3599
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| `access_token` |Incluído se `response_type` `token`incluir . O sinal de acesso que a app solicitou. O sinal de acesso não deve ser descodificado ou inspecionado de outra forma, deve ser tratado como uma corda opaca. |
| `token_type` |Incluído se `response_type` `token`incluir . Sempre será. `Bearer` |
| `expires_in`|Incluído se `response_type` `token`incluir . Indica o número de segundos em que o símbolo é válido, para fins de cache. |
| `scope` |Incluído se `response_type` `token`incluir . Indica o âmbito(s) para o qual o access_token será válido. Pode não incluir todos os âmbitos solicitados, se não forem aplicáveis ao utilizador (no caso dos âmbitos apenas da AD Azure apenas solicitados quando uma conta pessoal é utilizada para fazer login). |
| `id_token` | Um JSON Web Token assinado (JWT). A aplicação pode descodificar os segmentos deste token para solicitar informações sobre o utilizador que assinou. A aplicação pode cache os valores e exibi-los, mas não deve confiar neles para qualquer autorização ou limites de segurança. Para mais informações sobre [`id_token reference`](id-tokens.md)id_tokens, consulte o . <br> **Nota:** Só for `openid` solicitado o âmbito se for solicitado âmbito. |
| `state` |Se um parâmetro estatal estiver incluído no pedido, o mesmo valor deve figurar na resposta. A aplicação deve verificar se os valores do Estado no pedido e resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro `redirect_uri` também podem ser enviadas para a aplicação para que a aplicação possa manuseá-las adequadamente:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| `error` |Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem, e pode ser usada para reagir a erros. |
| `error_description` |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro de autenticação. |

## <a name="getting-access-tokens-silently-in-the-background"></a>Obtendo fichas de acesso silenciosamente no fundo

Agora que assinou o utilizador na sua aplicação de uma página única, pode obter silenciosamente fichas de acesso para chamadas de APIs web protegidas pela plataforma de identidade da Microsoft, como o [Microsoft Graph](https://developer.microsoft.com/graph). Mesmo que já tenha recebido `token` um símbolo usando o response_type, pode usar este método para adquirir fichas para recursos adicionais sem ter que redirecionar o utilizador para iniciar sessão novamente.

No fluxo normal de OpenID Connect/OAuth, fá-lo-ia `/token` fazendo um pedido para o ponto final da plataforma de identidade da Microsoft. No entanto, o ponto final da plataforma de identidade da Microsoft não suporta pedidos cors, pelo que fazer chamadas ajax para obter e refrescar tokens está fora de questão. Em vez disso, pode usar o fluxo implícito num iframe oculto para obter novos tokens para outras APIs web:

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
> Tente copiar & colando o pedido abaixo num separador de navegador! (Não se esqueça de `login_hint` substituir os valores pelo valor correto para o utilizador)
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint={your-username}`
>

Graças `prompt=none` ao parâmetro, este pedido irá ter sucesso ou falhar imediatamente e voltar à sua aplicação. Uma resposta bem sucedida será enviada `redirect_uri`para a sua aplicação `response_mode` no indicado, utilizando o método especificado no parâmetro.

#### <a name="successful-response"></a>Resposta bem sucedida

Uma resposta `response_mode=fragment` bem sucedida usando parece:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fdirectory.read
```

| Parâmetro | Descrição |
| --- | --- |
| `access_token` |Incluído se `response_type` `token`incluir . O sinal de acesso que a app solicitou, neste caso para o Microsoft Graph. O sinal de acesso não deve ser descodificado ou inspecionado de outra forma, deve ser tratado como uma corda opaca. |
| `token_type` | Sempre será. `Bearer` |
| `expires_in` | Indica o número de segundos em que o símbolo é válido, para fins de cache. |
| `scope` | Indica o âmbito(s) para o qual o access_token será válido. Pode não incluir todos os âmbitos solicitados, se não forem aplicáveis ao utilizador (no caso dos âmbitos apenas da AD Azure apenas solicitados quando uma conta pessoal é utilizada para fazer login). |
| `id_token` | Um JSON Web Token assinado (JWT). Incluído se `response_type` `id_token`incluir . A aplicação pode descodificar os segmentos deste token para solicitar informações sobre o utilizador que assinou. A aplicação pode cache os valores e exibi-los, mas não deve confiar neles para qualquer autorização ou limites de segurança. Para mais informações sobre id_tokens, consulte a [ `id_token` referência.](id-tokens.md) <br> **Nota:** Só for `openid` solicitado o âmbito se for solicitado âmbito. |
| `state` |Se um parâmetro estatal estiver incluído no pedido, o mesmo valor deve figurar na resposta. A aplicação deve verificar se os valores do Estado no pedido e resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro

As respostas de erro `redirect_uri` também podem ser enviadas para a aplicação para que a aplicação possa manuseá-las adequadamente. No caso `prompt=none`de, um erro esperado será:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | Descrição |
| --- | --- |
| `error` |Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem, e pode ser usada para reagir a erros. |
| `error_description` |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro de autenticação. |

Se receber este erro no pedido de iframe, o utilizador deve voltar a iniciar sessão para recuperar um novo token. Pode optar por lidar com este caso de qualquer forma que faça sentido para a sua aplicação.

## <a name="refreshing-tokens"></a>Fichas refrescantes

A subvenção implícita não fornece fichas refrescantes. Tanto `id_token`s `access_token`como s expirarão após um curto período de tempo, pelo que a sua aplicação deve estar preparada para atualizar estes tokens periodicamente. Para refrescar qualquer tipo de ficha, pode executar o mesmo `prompt=none` pedido de iframe escondido de cima usando o parâmetro para controlar o comportamento da plataforma de identidade. Se quiser receber um `id_token`novo , `id_token` certifique-se `scope=openid`de usar `nonce` no `response_type` e , bem como um parâmetro.

## <a name="send-a-sign-out-request"></a>Enviar um pedido de sinalização

O OpenID `end_session_endpoint` Connect permite que a sua aplicação envie um pedido para o ponto final da plataforma de identidade da Microsoft para terminar a sessão de um utilizador e limpar cookies definidos pelo ponto final da plataforma de identidade da Microsoft. Para assinar totalmente um utilizador a partir de uma aplicação web, a sua aplicação deve terminar a sua própria sessão com o utilizador (normalmente limpando uma cache simbólica ou deixando cair cookies), e depois redirecionar o navegador para:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| `tenant` |necessário |O `{tenant}` valor no caminho do pedido pode ser usado para controlar quem pode assinar a aplicação. Os valores `common` `organizations`permitidos são, e `consumers`identificadores de inquilinos. Para mais detalhes, consulte [os fundamentos do protocolo.](active-directory-v2-protocols.md#endpoints) |
| `post_logout_redirect_uri` | recomendado | O URL a que o utilizador deve ser devolvido após o início do início de sessão. Este valor deve corresponder a um dos URIs redirecionais registados para a aplicação. Caso não esteja incluído, o utilizador será mostrado uma mensagem genérica pelo ponto final da plataforma de identidade da Microsoft. |

## <a name="next-steps"></a>Passos seguintes

* Recorra às [amostras da MSAL JS](sample-v2-code.md) para começar a codificar.
