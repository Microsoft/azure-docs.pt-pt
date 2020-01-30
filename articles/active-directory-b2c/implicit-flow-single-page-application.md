---
title: Entrada de uma página única utilizando fluxo implícito
titleSuffix: Azure AD B2C
description: Saiba como adicionar um sinal de entrada de uma página usando o fluxo implícito OAuth 2.0 com o Azure Ative Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: efef55ab42313854ab27d323824a76488d520ad1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847384"
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

## <a name="send-authentication-requests"></a>Enviar solicitações de autenticação

Quando seu aplicativo Web precisa autenticar o usuário e executar um fluxo de usuário, ele pode direcionar o usuário para o ponto de extremidade `/authorize`. O usuário executa a ação dependendo do fluxo do usuário.

Neste pedido, o cliente indica as permissões que necessita de adquirir ao utilizador no parâmetro `scope` e o fluxo do utilizador para executar. Para ter uma ideia de como a solicitação funciona, tente colar a solicitação em um navegador e executá-la. Substitua `{tenant}` com o nome do seu inquilino do Azure AD B2C. Substitua `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` pela ID do aplicativo que você registrou anteriormente em seu locatário. Substitua `{policy}` pelo nome de uma apólice que criou no seu inquilino, por exemplo, `b2c_1_sign_in`.

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

| Parâmetro | Obrigatório | Descrição |
| --------- | -------- | ----------- |
|vários| Sim | Nome do seu locatário de Azure AD B2C|
|regras| Sim| O fluxo do usuário a ser executado. Especifique o nome de um fluxo de usuário que você criou em seu locatário Azure AD B2C. Por exemplo: `b2c_1_sign_in`, `b2c_1_sign_up`ou `b2c_1_edit_profile`. |
| client_id | Sim | A ID do aplicativo que o [portal do Azure](https://portal.azure.com/) atribuído ao seu aplicativo. |
| response_type | Sim | Deve incluir `id_token` para o acesso ao OpenID Connect. Também pode incluir o tipo de resposta `token`. Se utilizar `token`, a sua aplicação pode receber imediatamente um sinal de acesso do ponto final de autorização, sem fazer um segundo pedido para o ponto final de autorização.  Se utilizar o tipo de resposta `token`, o parâmetro `scope` deve conter um âmbito que indique para que recurso emitir o símbolo. |
| redirect_uri | Não | O URI de redirecionamento do seu aplicativo, em que as respostas de autenticação podem ser enviadas e recebidas pelo seu aplicativo. Ele deve corresponder exatamente a um dos URIs de redirecionamento que você registrou no portal, exceto que ele deve ser codificado por URL. |
| response_mode | Não | Especifica o método a utilizar para enviar o token resultante de volta para a sua aplicação.  Para fluxos implícitos, use `fragment`. |
| scope | Sim | Uma lista de escopos separados por espaços. Um único valor de escopo indica ao Azure AD as permissões que estão sendo solicitadas. O escopo de `openid` indica uma permissão para conectar o usuário e obter dados sobre o usuário na forma de tokens de ID. O âmbito `offline_access` é opcional para aplicações web. Indica que a sua aplicação precisa de um token de atualização para o acesso de longa duração aos recursos. |
| state | Não | Um valor incluído no pedido que também é devolvido na resposta simbólica. Pode ser uma série de qualquer conteúdo que queira usar. Normalmente, um valor único gerado aleatoriamente é usado, para evitar ataques de falsificação de pedidos de local. O Estado também é usado para codificar informações sobre o estado do utilizador na app antes do pedido de autenticação ocorrer, como a página em que estavam. |
| nonce | Sim | Um valor incluído no pedido (gerado pela app) que está incluído no símbolo de identificação resultante como uma reclamação. O aplicativo pode, então, verificar esse valor para atenuar os ataques de reprodução de token. Normalmente, o valor é uma cadeia aleatória e única que pode ser usada para identificar a origem do pedido. |
| aviso | Não | O tipo de interação do usuário que é necessário. Atualmente, o único valor válido é `login`. Este parâmetro obriga o utilizador a introduzir as suas credenciais nesse pedido. Um único sinal não faz efeito. |

Neste momento, é solicitado ao utilizador que complete o fluxo de trabalho da apólice. O utilizador poderá ter de introduzir o seu nome de utilizador e palavra-passe, iniciar sessão com uma identidade social, inscrever-se no diretório ou qualquer outro número de passos. As ações do usuário dependem de como o fluxo do usuário é definido.

Depois de o utilizador completar o fluxo de utilizador, o Azure AD devolve uma resposta à sua aplicação pelo valor utilizado para `redirect_uri`. Utiliza o método especificado no parâmetro `response_mode`. A resposta é exatamente a mesma para cada um dos cenários de ação do usuário, independentemente do fluxo do usuário que foi executado.

### <a name="successful-response"></a>Resposta bem-sucedida
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
| access_token | O token de acesso solicitado pelo aplicativo. |
| token_type | O valor do tipo de token. O único tipo ao qual o Azure AD dá suporte é portador. |
| expires_in | O período de tempo que o token de acesso é válido (em segundos). |
| scope | Os escopos para os quais o token é válido. Você também pode usar escopos para armazenar tokens em cache para uso posterior. |
| id_token | O token de ID que o aplicativo solicitou. Você pode usar o token de ID para verificar a identidade do usuário e iniciar uma sessão com o usuário. Para obter mais informações sobre fichas de identificação e seus conteúdos, consulte a [referência simbólica Azure AD B2C](tokens-overview.md). |
| state | Se um parâmetro `state` for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de `state` na solicitação e na resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro
As respostas de erro também podem ser enviadas para o URI de redirecionamento para que o aplicativo possa tratá-las adequadamente:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --------- | ----------- |
| erro | Um código usado para classificar tipos de erros que ocorrem. |
| error_description | Uma mensagem de erro específica que pode ajudá-lo a identificar a causa raiz de um erro de autenticação. |
| state | Se um parâmetro `state` for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de `state` na solicitação e na resposta são idênticos.|

## <a name="validate-the-id-token"></a>Validar o token de ID

Receber um símbolo de identificação não é suficiente para autenticar o utilizador. Valide a assinatura do token de identificação e verifique as reclamações no token de acordo com os requisitos da sua aplicação. O Azure AD B2C usa [JWTs (tokens Web JSON)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar se eles são válidos.

Muitas bibliotecas de código aberto estão disponíveis para validação de JWTs, dependendo da linguagem que você prefere usar. Considere explorar bibliotecas de código aberto disponíveis em vez de implementar a sua própria lógica de validação. Pode utilizar a informação deste artigo para ajudá-lo a aprender a usar corretamente essas bibliotecas.

O Azure AD B2C tem um ponto final de metadados OpenID Connect. Uma aplicação pode usar o ponto final para obter informações sobre O Azure AD B2C em tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e chaves de assinatura de token. Existe um documento de metadados JSON para cada fluxo de utilizador no seu inquilino Azure AD B2C. Por exemplo, o documento de metadados para o fluxo de utilizador b2c_1_sign_in no fabrikamb2c.onmicrosoft.com inquilino está localizado em:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Uma das propriedades deste documento de configuração é o `jwks_uri`. O valor para o mesmo fluxo de utilizador seria:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Para determinar qual o fluxo do utilizador utilizado para assinar um símbolo de identificação (e de onde recolher os metadados), tem duas opções. Em primeiro lugar, o nome de fluxo do utilizador está incluído na reclamação `acr` em `id_token`. Para obter informações sobre como analisar as reclamações de um símbolo de identificação, consulte a [referência simbólica Azure AD B2C](tokens-overview.md). A outra opção é codificar o fluxo do utilizador no valor do parâmetro `state` quando emitir o pedido. Em seguida, descodifique o parâmetro `state` para determinar qual o fluxo do utilizador utilizado. O método é válido.

Depois de ter adquirido o documento de metadados do ponto final dos metadados OpenID Connect, pode utilizar as teclas públicas RSA-256 (localizadas neste ponto final) para validar a assinatura do símbolo de identificação. Pode haver várias chaves listadas neste ponto final a qualquer momento, cada uma identificada por um `kid`. O cabeçalho de `id_token` também contém uma reivindicação `kid`. Indica qual destas chaves foi usada para assinar o símbolo de identificação. Para obter mais informações, incluindo a aprendizagem sobre a validação de [fichas,](tokens-overview.md)consulte a [referência simbólica Azure AD B2C](tokens-overview.md).
<!--TODO: Improve the information on this-->

Depois de validar a assinatura do símbolo de identificação, várias reclamações requerem verificação. Por exemplo:

* Valide a declaração de `nonce` para evitar ataques de reprodução de token. Seu valor deve ser o que você especificou na solicitação de entrada.
* Valide a alegação `aud` para garantir que o token de identificação foi emitido para a sua aplicação. O seu valor deve ser a identificação da aplicação da sua aplicação.
* Valide as alegações de `iat` e `exp` para garantir que o token de identificação não expirou.

Várias outras validações que deve executar são descritas em detalhe na Especificação de Núcleo de [Ligação OpenID](https://openid.net/specs/openid-connect-core-1_0.html). Também pode querer validar reclamações adicionais, dependendo do seu cenário. Algumas validações comuns incluem:

* Garantindo que o utilizador ou organização se inscreveu na aplicação.
* Garantindo que o utilizador tem autorização e privilégios adequados.
* Garantindo que ocorreu uma certa força de autenticação, como por exemplo, utilizando a Autenticação Multi-Factor Azure.

Para obter mais informações sobre as reclamações num símbolo de identificação, consulte a [referência simbólica Azure AD B2C](tokens-overview.md).

Depois de ter validado o símbolo de identificação, pode iniciar uma sessão com o utilizador. Na sua aplicação, utilize as reclamações no token id para obter informações sobre o utilizador. Estas informações podem ser utilizadas para exibição, registos, autorização, e assim por diante.

## <a name="get-access-tokens"></a>Obter tokens de acesso
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
|vários| Obrigatório | Nome do seu locatário de Azure AD B2C|
regras| Obrigatório| O fluxo do usuário a ser executado. Especifique o nome de um fluxo de usuário que você criou em seu locatário Azure AD B2C. Por exemplo: `b2c_1_sign_in`, `b2c_1_sign_up`ou `b2c_1_edit_profile`. |
| client_id |Obrigatório |A ID do aplicativo atribuída ao seu aplicativo no [portal do Azure](https://portal.azure.com). |
| response_type |Obrigatório |Deve incluir `id_token` para o acesso ao OpenID Connect.  Pode também incluir o tipo de resposta `token`. Se utilizar `token` aqui, a sua aplicação pode receber imediatamente um sinal de acesso do ponto final de autorização, sem fazer um segundo pedido para o ponto final de autorização. Se utilizar o tipo de resposta `token`, o parâmetro `scope` deve conter um âmbito que indique para que recurso emitir o símbolo. |
| redirect_uri |Recomendado |O URI de redirecionamento do seu aplicativo, em que as respostas de autenticação podem ser enviadas e recebidas pelo seu aplicativo. Deve corresponder exatamente a um dos URIs redirecionados registados no portal, exceto que deve ser codificado por URL. |
| scope |Obrigatório |Uma lista de escopos separados por espaços.  Para obter fichas, inclua todos os âmbitos que você precisa para o recurso pretendido. |
| response_mode |Recomendado |Especifica o método utilizado para enviar o token resultante de volta para a sua aplicação. Para um fluxo implícito, utilize `fragment`. Dois outros modos podem ser especificados, `query` e `form_post`, mas não funcionam no fluxo implícito. |
| state |Recomendado |Um valor incluído no pedido que é devolvido na resposta simbólica.  Pode ser uma série de qualquer conteúdo que queira usar.  Normalmente, um valor único gerado aleatoriamente é usado, para evitar ataques de falsificação de pedidos de local.  O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação tenha ocorrido. Por exemplo, a página ou a visualização do utilizador estava. |
| nonce |Obrigatório |Um valor incluído no pedido, gerado pela app, que está incluído no token de identificação resultante como uma reclamação.  O aplicativo pode, então, verificar esse valor para atenuar os ataques de reprodução de token. Normalmente, o valor é uma cadeia aleatória e única que identifica a origem do pedido. |
| aviso |Obrigatório |Para refrescar e obter fichas num iframe oculto, use `prompt=none` para garantir que o iframe não fique preso na página de inscrição e regresse imediatamente. |
| login_hint |Obrigatório |Para refrescar e obter fichas num iframe oculto, inclua o nome de utilizador do utilizador nesta dica para distinguir entre várias sessões que o utilizador possa ter num dado momento. Pode extrair o nome de utilizador de um início de sessão anterior utilizando a reivindicação `preferred_username` (o âmbito `profile` é necessário para receber a `preferred_username` reclamação). |
| domain_hint |Obrigatório |Pode ser `consumers` ou `organizations`.  Para refrescar e obter fichas num iframe escondido, inclua o valor `domain_hint` no pedido.  Extrair a reclamação `tid` do documento de identificação de um início de sessão anterior para determinar qual o valor a utilizar (o âmbito `profile` é necessário para receber a reclamação `tid`). Se o valor da reclamação `tid` for `9188040d-6c67-4c5b-b112-36a304b66dad`, utilize `domain_hint=consumers`.  Caso contrário, use `domain_hint=organizations`. |

Ao definir o parâmetro `prompt=none`, este pedido ou sucede ou falha imediatamente, e regressa à sua aplicação.  Uma resposta bem sucedida é enviada para a sua aplicação no uri redirecionamento indicado, utilizando o método especificado no parâmetro `response_mode`.

### <a name="successful-response"></a>Resposta bem-sucedida
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
| state |Se um parâmetro `state` for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de `state` na solicitação e na resposta são idênticos. |
| expires_in |Por quanto tempo o token de acesso é válido (em segundos). |
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
| erro |Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem. Você também pode usar a cadeia de caracteres para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudá-lo a identificar a causa raiz de um erro de autenticação. |

Se você receber esse erro na solicitação de iframe, o usuário deverá fazer logon interativamente novamente para recuperar um novo token.

## <a name="refresh-tokens"></a>Fichas de atualização
Fichas de identificação e fichas de acesso expiram após um curto período de tempo. A sua aplicação deve estar preparada para refrescar estas fichas periodicamente.  Para refrescar qualquer tipo de ficha, execute o mesmo pedido de iframe escondido que usamos num exemplo anterior, utilizando o parâmetro `prompt=none` para controlar os passos da AD Azure.  Para receber um novo valor `id_token`, certifique-se de utilizar `response_type=id_token` e `scope=openid`, e um parâmetro `nonce`.

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de saída
Quando pretender contratar o utilizador para fora da aplicação, redirecione o utilizador para a AD Azure para assinar. Se não redirecionar o utilizador, poderá reautenticar a sua aplicação sem voltar a introduzir as suas credenciais porque tem uma única sessão de inscrição válida com a Azure AD.

Pode simplesmente redirecionar o utilizador para o `end_session_endpoint` listado no mesmo documento de metadados OpenID Connect descrito em [Validate o token ID](#validate-the-id-token). Por exemplo:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parâmetro | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| vários | Sim | Nome do seu locatário de Azure AD B2C |
| regras | Sim | O fluxo de usuário que você deseja usar para desconectar o usuário do seu aplicativo. |
| post_logout_redirect_uri | Não | A URL para a qual o usuário deve ser redirecionado após a saída bem-sucedida. Se não estiver incluído, Azure AD B2C mostrará ao usuário uma mensagem genérica. |
| state | Não | Se um parâmetro `state` for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de `state` na solicitação e na resposta são idênticos. |


> [!NOTE]
> Direcionar o utilizador para o `end_session_endpoint` iliba parte do único estado de inscrição do utilizador com o Azure AD B2C. No entanto, não assina o utilizador fora da sessão de fornecedor de identidade social do utilizador. Se o utilizador selecionar o mesmo fornecedor de identidade durante um início de sessão subsequente, o utilizador é reautenticado, sem introduzir as suas credenciais. Se um utilizador quiser assinar a sua aplicação Azure AD AD B2C, isso não significa necessariamente que queira assinar completamente a sua conta de Facebook, por exemplo. No entanto, para as contas locais, a sessão do utilizador terminará corretamente.
>

## <a name="next-steps"></a>Passos seguintes

### <a name="code-sample-hellojs-with-azure-ad-b2c"></a>Amostra de código: hello.js com Azure AD B2C

[Aplicação de uma página única construída em hello.js com Azure AD B2C][github-hello-js-example] (GitHub)

Esta amostra no GitHub destina-se a ajudá-lo a começar com o Azure AD B2C numa aplicação web simples construída em [hello.js][github-hello-js] e usando autenticação ao estilo pop-up.

<!-- Links - EXTERNAL -->
[github-hello-js-example]: https://github.com/Azure-Samples/active-directory-b2c-javascript-hellojs-singlepageapp
[github-hello-js]: https://github.com/MrSwitch/hello.js
