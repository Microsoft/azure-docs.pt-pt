---
title: Entrada de uma página única utilizando fluxo implícito
titleSuffix: Azure AD B2C
description: Saiba como adicionar um sinal de entrada de uma página usando o fluxo implícito OAuth 2.0 com o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37b59c2a23a8f00e8376be2ac4a7b35a6d58aa28
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78398995"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Sinal de uma página na utilização do fluxo implícito OAuth 2.0 no Diretório Ativo Azure B2C

Muitas aplicações modernas têm uma ponta frontal de uma aplicação de uma página única que é escrita principalmente no JavaScript. Muitas vezes, a aplicação é escrita usando uma estrutura como Reagir, Angular ou Vue.js. Aplicações de página única e outras aplicações JavaScript que funcionam principalmente num navegador têm alguns desafios adicionais para a autenticação:

- As características de segurança destas aplicações são diferentes das aplicações web tradicionais baseadas em servidores.
- Muitos servidores de autorização e fornecedores de identidade não suportam pedidos de partilha de recursos de origem cruzada (CORS).
- Os redirecionamentos de navegador de página inteira para longe da aplicação podem ser invasivos para a experiência do utilizador.

Para suportar estas aplicações, o Azure Ative Directory B2C (Azure AD B2C) utiliza o fluxo implícito OAuth 2.0. O fluxo implícito de subvenção de autorização OAuth 2.0 é descrito na [secção 4.2 da especificação OAuth 2.0.](https://tools.ietf.org/html/rfc6749) No fluxo implícito, a aplicação recebe fichas diretamente do Azure Ative Directory (Azure AD) autoriza ponto final, sem qualquer troca de servidor-a-servidor. Toda a lógica de autenticação e manuseamento de sessão é feito inteiramente no cliente JavaScript com um redirecionamento de página ou uma caixa pop-up.

O Azure AD B2C alarga o fluxo implícito OAuth 2.0 padrão para mais do que simples autenticação e autorização. Azure AD B2C introduz o [parâmetro de política](user-flow-overview.md). Com o parâmetro de política, pode utilizar o OAuth 2.0 para adicionar políticas à sua app, tais como fluxos de utilizador de inscrição, inscrição e gestão de perfis. No exemplo, os pedidos http neste artigo, **{tenant}.onmicrosoft.com** é usado como exemplo. Substitua `{tenant}` pelo nome do seu inquilino se tiver um e também criou um fluxo de utilizador.

O fluxo implícito de entrada parece-se com a figura seguinte. Cada passo é descrito em detalhe mais tarde no artigo.

![Diagrama estilo Swimlane mostrando o fluxo implícito OpenID Connect](./media/implicit-flow-single-page-application/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Enviar pedidos de autenticação

Quando a sua aplicação web precisa de autenticar o utilizador e executar um fluxo de utilizador, pode direcionar o utilizador para o ponto final `/authorize`. O utilizador toma medidas dependendo do fluxo do utilizador.

Neste pedido, o cliente indica as permissões que necessita de adquirir ao utilizador no parâmetro `scope` e o fluxo do utilizador para executar. Para obter uma sensação de como o pedido funciona, tente colar o pedido num navegador e executá-lo. Substitua `{tenant}` pelo nome do seu inquilino Azure AD B2C. Substitua `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` com a identificação da aplicação que já registou no seu inquilino. Substitua `{policy}` pelo nome de uma apólice que criou no seu inquilino, por exemplo, `b2c_1_sign_in`.

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parâmetro | Necessário | Descrição |
| --------- | -------- | ----------- |
|{inquilino}| Sim | Nome do seu inquilino Azure AD B2C|
|{política}| Sim| O fluxo do utilizador para ser executado. Especifique o nome de um fluxo de utilizador que criou no seu inquilino Azure AD B2C. Por exemplo: `b2c_1_sign_in`, `b2c_1_sign_up`ou `b2c_1_edit_profile`. |
| client_id | Sim | O ID de aplicação que o [portal Azure](https://portal.azure.com/) atribuiu à sua aplicação. |
| response_type | Sim | Deve incluir `id_token` para o acesso ao OpenID Connect. Também pode incluir o tipo de resposta `token`. Se utilizar `token`, a sua aplicação pode receber imediatamente um sinal de acesso do ponto final de autorização, sem fazer um segundo pedido para o ponto final de autorização.  Se utilizar o tipo de resposta `token`, o parâmetro `scope` deve conter um âmbito que indique para que recurso emitir o símbolo. |
| redirect_uri | Não | O URI redirecionado da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a uma das URIs redirecionadas que registou no portal, exceto que deve ser codificada por URL. |
| response_mode | Não | Especifica o método a utilizar para enviar o token resultante de volta para a sua aplicação.  Para fluxos implícitos, use `fragment`. |
| scope | Sim | Uma lista de âmbitos separados pelo espaço. Um único valor de âmbito indica à Azure AD ambas as permissões que estão a ser solicitadas. O âmbito `openid` indica uma permissão para iniciar sessão no utilizador e obter dados sobre o utilizador sob a forma de fichas de identificação. O âmbito `offline_access` é opcional para aplicações web. Indica que a sua aplicação precisa de um token de atualização para o acesso de longa duração aos recursos. |
| state | Não | Um valor incluído no pedido que também é devolvido na resposta simbólica. Pode ser uma série de qualquer conteúdo que queira usar. Normalmente, um valor único gerado aleatoriamente é usado, para evitar ataques de falsificação de pedidos de local. O Estado também é usado para codificar informações sobre o estado do utilizador na app antes do pedido de autenticação ocorrer, como a página em que estavam. |
| nonce | Sim | Um valor incluído no pedido (gerado pela app) que está incluído no símbolo de identificação resultante como uma reclamação. A aplicação pode então verificar este valor para mitigar ataques de repetição de tokens. Normalmente, o valor é uma cadeia aleatória e única que pode ser usada para identificar a origem do pedido. |
| pronta | Não | O tipo de interação do utilizador que é necessário. Atualmente, o único valor válido é `login`. Este parâmetro obriga o utilizador a introduzir as suas credenciais nesse pedido. Um único sinal não faz efeito. |

Neste momento, é solicitado ao utilizador que complete o fluxo de trabalho da apólice. O utilizador poderá ter de introduzir o seu nome de utilizador e palavra-passe, iniciar sessão com uma identidade social, inscrever-se no diretório ou qualquer outro número de passos. As ações do utilizador dependem da definição do fluxo do utilizador.

Depois de o utilizador completar o fluxo de utilizador, o Azure AD devolve uma resposta à sua aplicação pelo valor utilizado para `redirect_uri`. Utiliza o método especificado no parâmetro `response_mode`. A resposta é exatamente a mesma para cada um dos cenários de ação do utilizador, independentemente do fluxo de utilizador que foi executado.

### <a name="successful-response"></a>Resposta bem sucedida
Uma resposta bem sucedida que usa `response_mode=fragment` e `response_type=id_token+token` se parece com o seguinte, com quebras de linha para a legibilidade:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Parâmetro | Descrição |
| --------- | ----------- |
| access_token | O sinal de acesso que a app solicitou. |
| token_type | O valor do tipo simbólico. O único tipo que a Azure AD suporta é o Bearer. |
| expires_in | O tempo que o token de acesso é válido (em segundos). |
| scope | Os âmbitos para os os que o símbolo é válido. Também pode utilizar os telescópios para obter tokens de cache para posterior utilização. |
| id_token | O símbolo de identificação que a aplicação solicitou. Pode utilizar o símbolo de identificação para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. Para obter mais informações sobre fichas de identificação e seus conteúdos, consulte a [referência simbólica Azure AD B2C](tokens-overview.md). |
| state | Se um parâmetro `state` estiver incluído no pedido, o mesmo valor deve figurar na resposta. A aplicação deve verificar se os valores `state` no pedido e resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro
As respostas de erro também podem ser enviadas para o URI redireccional para que a aplicação possa manuseá-las adequadamente:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --------- | ----------- |
| erro | Um código usado para classificar tipos de erros que ocorrem. |
| error_description | Uma mensagem de erro específica que pode ajudá-lo a identificar a causa principal de um erro de autenticação. |
| state | Se um parâmetro `state` estiver incluído no pedido, o mesmo valor deve figurar na resposta. A aplicação deve verificar se os valores `state` no pedido e resposta são idênticos.|

## <a name="validate-the-id-token"></a>Validar o símbolo de identificação

Receber um símbolo de identificação não é suficiente para autenticar o utilizador. Valide a assinatura do token de identificação e verifique as reclamações no token de acordo com os requisitos da sua aplicação. O Azure AD B2C utiliza [os Tofros Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e a criptografia de chaves públicas para assinar fichas e verificar se são válidas.

Muitas bibliotecas de código aberto estão disponíveis para validação de JWTs, dependendo da linguagem que você prefere usar. Considere explorar bibliotecas de código aberto disponíveis em vez de implementar a sua própria lógica de validação. Pode utilizar a informação deste artigo para ajudá-lo a aprender a usar corretamente essas bibliotecas.

O Azure AD B2C tem um ponto final de metadados OpenID Connect. Uma aplicação pode usar o ponto final para obter informações sobre O Azure AD B2C em tempo de execução. Estas informações incluem pontos finais, conteúdo simbólico e chaves de assinatura simbólicas. Existe um documento de metadados JSON para cada fluxo de utilizador no seu inquilino Azure AD B2C. Por exemplo, o documento de metadados para o fluxo de utilizador b2c_1_sign_in no fabrikamb2c.onmicrosoft.com inquilino está localizado em:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Uma das propriedades deste documento de configuração é o `jwks_uri`. O valor para o mesmo fluxo de utilizador seria:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Para determinar qual o fluxo do utilizador utilizado para assinar um símbolo de identificação (e de onde recolher os metadados), tem duas opções. Em primeiro lugar, o nome de fluxo do utilizador está incluído na reclamação `acr` em `id_token`. Para obter informações sobre como analisar as reclamações de um símbolo de identificação, consulte a [referência simbólica Azure AD B2C](tokens-overview.md). A outra opção é codificar o fluxo do utilizador no valor do parâmetro `state` quando emitir o pedido. Em seguida, descodifique o parâmetro `state` para determinar qual o fluxo do utilizador utilizado. Qualquer um dos métodos é válido.

Depois de ter adquirido o documento de metadados do ponto final dos metadados OpenID Connect, pode utilizar as teclas públicas RSA-256 (localizadas neste ponto final) para validar a assinatura do símbolo de identificação. Pode haver várias chaves listadas neste ponto final a qualquer momento, cada uma identificada por um `kid`. O cabeçalho de `id_token` também contém uma reivindicação `kid`. Indica qual destas chaves foi usada para assinar o símbolo de identificação. Para obter mais informações, incluindo a aprendizagem sobre a validação de [fichas,](tokens-overview.md)consulte a [referência simbólica Azure AD B2C](tokens-overview.md).
<!--TODO: Improve the information on this-->

Depois de validar a assinatura do símbolo de identificação, várias reclamações requerem verificação. Por exemplo:

* Valide a alegação `nonce` para evitar ataques de repetição de tokens. O seu valor deve ser o que especificou no pedido de inscrição.
* Valide a alegação `aud` para garantir que o token de identificação foi emitido para a sua aplicação. O seu valor deve ser a identificação da aplicação da sua aplicação.
* Valide as alegações de `iat` e `exp` para garantir que o token de identificação não expirou.

Várias outras validações que deve executar são descritas em detalhe na Especificação de Núcleo de [Ligação OpenID](https://openid.net/specs/openid-connect-core-1_0.html). Também pode querer validar reclamações adicionais, dependendo do seu cenário. Algumas validações comuns incluem:

* Garantindo que o utilizador ou organização se inscreveu na aplicação.
* Garantindo que o utilizador tem autorização e privilégios adequados.
* Garantindo que ocorreu uma certa força de autenticação, como por exemplo, utilizando a Autenticação Multi-Factor Azure.

Para obter mais informações sobre as reclamações num símbolo de identificação, consulte a [referência simbólica Azure AD B2C](tokens-overview.md).

Depois de ter validado o símbolo de identificação, pode iniciar uma sessão com o utilizador. Na sua aplicação, utilize as reclamações no token id para obter informações sobre o utilizador. Estas informações podem ser utilizadas para exibição, registos, autorização, e assim por diante.

## <a name="get-access-tokens"></a>Obtenha fichas de acesso
Se a única coisa que as suas aplicações web precisam de fazer é executar fluxos de utilizadores, pode ignorar as próximas secções. As informações nas seguintes secções aplicam-se apenas a aplicações web que necessitem de fazer chamadas autenticadas para uma API web, e que estão protegidas pelo Azure AD B2C.

Agora que assinou o utilizador na sua aplicação de uma página única, pode obter fichas de acesso para chamadas de APIs web que são protegidas pela Azure AD. Mesmo que já tenha recebido um sinal utilizando o tipo de resposta `token`, pode utilizar este método para adquirir fichas para obter recursos adicionais sem redirecionar o utilizador para iniciar sessão novamente.

Num fluxo típico de aplicação web, você faria um pedido para o `/token` ponto final. No entanto, o ponto final não suporta pedidos cors, pelo que fazer chamadas do AJAX para obter um token refrescante não é uma opção. Em vez disso, pode utilizar o fluxo implícito num elemento iframe HTML oculto para obter novos tokens para outras APIs web. Aqui está um exemplo, com quebras de linha para a legibilidade:

```HTTP
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
|{inquilino}| Necessário | Nome do seu inquilino Azure AD B2C|
{política}| Necessário| O fluxo do utilizador para ser executado. Especifique o nome de um fluxo de utilizador que criou no seu inquilino Azure AD B2C. Por exemplo: `b2c_1_sign_in`, `b2c_1_sign_up`ou `b2c_1_edit_profile`. |
| client_id |Necessário |O ID da aplicação atribuído à sua aplicação no [portal Azure.](https://portal.azure.com) |
| response_type |Necessário |Deve incluir `id_token` para o acesso ao OpenID Connect.  Pode também incluir o tipo de resposta `token`. Se utilizar `token` aqui, a sua aplicação pode receber imediatamente um sinal de acesso do ponto final de autorização, sem fazer um segundo pedido para o ponto final de autorização. Se utilizar o tipo de resposta `token`, o parâmetro `scope` deve conter um âmbito que indique para que recurso emitir o símbolo. |
| redirect_uri |Recomendado |O URI redirecionado da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a um dos URIs redirecionados registados no portal, exceto que deve ser codificado por URL. |
| scope |Necessário |Uma lista de âmbitos separados pelo espaço.  Para obter fichas, inclua todos os âmbitos que você precisa para o recurso pretendido. |
| response_mode |Recomendado |Especifica o método utilizado para enviar o token resultante de volta para a sua aplicação. Para um fluxo implícito, utilize `fragment`. Dois outros modos podem ser especificados, `query` e `form_post`, mas não funcionam no fluxo implícito. |
| state |Recomendado |Um valor incluído no pedido que é devolvido na resposta simbólica.  Pode ser uma série de qualquer conteúdo que queira usar.  Normalmente, um valor único gerado aleatoriamente é usado, para evitar ataques de falsificação de pedidos de local.  O Estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer. Por exemplo, a página ou a visualização do utilizador estava. |
| nonce |Necessário |Um valor incluído no pedido, gerado pela app, que está incluído no token de identificação resultante como uma reclamação.  A aplicação pode então verificar este valor para mitigar ataques de repetição de tokens. Normalmente, o valor é uma cadeia aleatória e única que identifica a origem do pedido. |
| pronta |Necessário |Para refrescar e obter fichas num iframe oculto, use `prompt=none` para garantir que o iframe não fique preso na página de inscrição e regresse imediatamente. |
| login_hint |Necessário |Para refrescar e obter fichas num iframe oculto, inclua o nome de utilizador do utilizador nesta dica para distinguir entre várias sessões que o utilizador possa ter num dado momento. Pode extrair o nome de utilizador de um início de sessão anterior utilizando a reivindicação `preferred_username` (o âmbito `profile` é necessário para receber a `preferred_username` reclamação). |
| domain_hint |Necessário |Pode ser `consumers` ou `organizations`.  Para refrescar e obter fichas num iframe escondido, inclua o valor `domain_hint` no pedido.  Extrair a reclamação `tid` do documento de identificação de um início de sessão anterior para determinar qual o valor a utilizar (o âmbito `profile` é necessário para receber a reclamação `tid`). Se o valor da reclamação `tid` for `9188040d-6c67-4c5b-b112-36a304b66dad`, utilize `domain_hint=consumers`.  Caso contrário, use `domain_hint=organizations`. |

Ao definir o parâmetro `prompt=none`, este pedido ou sucede ou falha imediatamente, e regressa à sua aplicação.  Uma resposta bem sucedida é enviada para a sua aplicação no uri redirecionamento indicado, utilizando o método especificado no parâmetro `response_mode`.

### <a name="successful-response"></a>Resposta bem sucedida
Uma resposta bem sucedida usando `response_mode=fragment` se parece com este exemplo:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parâmetro | Descrição |
| --- | --- |
| access_token |O símbolo que a aplicação solicitou. |
| token_type |O tipo de símbolo será sempre O Portador. |
| state |Se um parâmetro `state` estiver incluído no pedido, o mesmo valor deve figurar na resposta. A aplicação deve verificar se os valores `state` no pedido e resposta são idênticos. |
| expires_in |Quanto tempo o token de acesso é válido (em segundos). |
| scope |Os âmbitos para os os que o sinal de acesso é válido. |

### <a name="error-response"></a>Resposta de erro
As respostas de erro também podem ser enviadas para o URI redirecionamento para que a aplicação possa manuseá-las adequadamente.  Para `prompt=none`, um erro esperado parece com este exemplo:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem. Também pode usar a corda para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudá-lo a identificar a causa principal de um erro de autenticação. |

Se receber este erro no pedido de iframe, o utilizador deve voltar a iniciar sessão para recuperar um novo token.

## <a name="refresh-tokens"></a>Fichas de atualização
Fichas de identificação e fichas de acesso expiram após um curto período de tempo. A sua aplicação deve estar preparada para refrescar estas fichas periodicamente.  Para refrescar qualquer tipo de ficha, execute o mesmo pedido de iframe escondido que usamos num exemplo anterior, utilizando o parâmetro `prompt=none` para controlar os passos da AD Azure.  Para receber um novo valor `id_token`, certifique-se de utilizar `response_type=id_token` e `scope=openid`, e um parâmetro `nonce`.

## <a name="send-a-sign-out-request"></a>Enviar um pedido de inscrição
Quando pretender contratar o utilizador para fora da aplicação, redirecione o utilizador para a AD Azure para assinar. Se não redirecionar o utilizador, poderá reautenticar a sua aplicação sem voltar a introduzir as suas credenciais porque tem uma única sessão de inscrição válida com a Azure AD.

Pode simplesmente redirecionar o utilizador para o `end_session_endpoint` listado no mesmo documento de metadados OpenID Connect descrito em [Validate o token ID](#validate-the-id-token). Por exemplo:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parâmetro | Necessário | Descrição |
| --------- | -------- | ----------- |
| {inquilino} | Sim | Nome do seu inquilino Azure AD B2C |
| {política} | Sim | O fluxo de utilizador que pretende utilizar para assinar o utilizador fora da sua aplicação. |
| post_logout_redirect_uri | Não | O URL para o que o utilizador deve ser redirecionado após o sucesso da assinatura. Se não estiver incluído, o Azure AD B2C mostra ao utilizador uma mensagem genérica. |
| state | Não | Se um parâmetro `state` estiver incluído no pedido, o mesmo valor deve figurar na resposta. O pedido deve verificar se os valores `state` no pedido e resposta são idênticos. |


> [!NOTE]
> Direcionar o utilizador para o `end_session_endpoint` iliba parte do único estado de inscrição do utilizador com o Azure AD B2C. No entanto, não assina o utilizador fora da sessão de fornecedor de identidade social do utilizador. Se o utilizador selecionar o mesmo fornecedor de identidade durante um início de sessão subsequente, o utilizador é reautenticado, sem introduzir as suas credenciais. Se um utilizador quiser assinar a sua aplicação Azure AD AD B2C, isso não significa necessariamente que queira assinar completamente a sua conta de Facebook, por exemplo. No entanto, para as contas locais, a sessão do utilizador terminará corretamente.
>

## <a name="next-steps"></a>Passos seguintes

### <a name="code-sample-azure-ad-b2c-with-microsoft-authentication-library-for-javascript"></a>Amostra de código: Azure AD B2C com Microsoft Authentication Library for JavaScript

[Aplicação de página única construída com msal.js para Azure AD B2C][github-msal-js-example] (GitHub)

Esta amostra no GitHub destina-se a ajudá-lo a começar a Azure AD B2C numa simples aplicação web construída com [msal.js][github-msal-js] e usando autenticação ao estilo pop-up.

<!-- Links - EXTERNAL -->
[github-msal-js-example]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
[github-msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
