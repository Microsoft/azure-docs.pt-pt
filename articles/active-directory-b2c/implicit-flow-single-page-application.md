---
title: Entrada de entrada de uma página única utilizando fluxo implícito
titleSuffix: Azure AD B2C
description: Saiba como adicionar o sign-in de uma página única utilizando o fluxo implícito OAuth 2.0 com o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 44300771ce6471c97dcd582884995395daae4995
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92215489"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Sinal de uma página única na utilização do fluxo implícito OAuth 2.0 em Azure Ative Directory B2C

Muitas aplicações modernas têm uma primeira página de uma aplicação que é escrita principalmente no JavaScript. Frequentemente, a aplicação é escrita usando uma estrutura como React, Angular ou Vue.js. Aplicações de página única e outras aplicações JavaScript que funcionam principalmente num navegador têm alguns desafios adicionais para a autenticação:

- As características de segurança destas aplicações são diferentes das aplicações web tradicionais baseadas em servidores.
- Muitos servidores de autorização e fornecedores de identidade não suportam pedidos de partilha de recursos de origem cruzada (CORS).
- Os redirecionamentos de navegador de página inteira para longe da aplicação podem ser invasivos para a experiência do utilizador.

A forma recomendada de suportar aplicações de uma só página é o fluxo de [código de autorização OAuth 2.0 (com PKCE)](./authorization-code-flow.md).

Alguns quadros, como [MSAL.js 1.x,](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)apenas apoiam o fluxo implícito de subvenções. Nestes casos, o Azure Ative Directory B2C (Azure AD B2C) apoia o fluxo de subvenção implícita de autorização OAuth 2.0. O fluxo de si é descrito na [secção 4.2 da especificação OAuth 2.0](https://tools.ietf.org/html/rfc6749). No fluxo implícito, a aplicação recebe fichas diretamente do Azure Ative Directory (Azure AD) autorizando o ponto final, sem qualquer troca de servidor para servidor. Toda a lógica de autenticação e tratamento de sessão é feito inteiramente no cliente JavaScript com um redirecionamento de página ou uma caixa pop-up.

O Azure AD B2C alarga o fluxo implícito padrão OAuth 2.0 a mais do que simples autenticação e autorização. Azure AD B2C introduz o [parâmetro de política](user-flow-overview.md). Com o parâmetro de política, pode utilizar o OAuth 2.0 para adicionar políticas à sua aplicação, tais como fluxos de utilizador de inscrição, inscrição e gestão de perfis. No exemplo, os pedidos HTTP neste artigo, **{tenant}.onmicrosoft.com** é usado como exemplo. `{tenant}`Substitua-o pelo nome do seu inquilino se tiver um e também criou um fluxo de utilizador.

O fluxo implícito de entrada de entrada parece algo como a seguinte figura. Cada passo é descrito em detalhe mais tarde no artigo.

![Diagrama estilo Natação mostrando o fluxo implícito OpenID Connect](./media/implicit-flow-single-page-application/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Enviar pedidos de autenticação

Quando a sua aplicação web necessitar de autenticar o utilizador e executar um fluxo de utilizador, pode direcionar o utilizador para o `/authorize` ponto final. O utilizador toma medidas dependendo do fluxo do utilizador.

Neste pedido, o cliente indica as permissões que necessita de adquirir do utilizador no `scope` parâmetro e no fluxo do utilizador para executar. Para ter uma noção de como o pedido funciona, tente colar o pedido num browser e executá-lo. `{tenant}`Substitua-o pelo nome do seu inquilino Azure AD B2C. `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6`Substitua-se pelo ID da aplicação que já se registou no seu inquilino. `{policy}`Substitua-o pelo nome de uma apólice que criou no seu inquilino, por `b2c_1_sign_in` exemplo.

```http
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
|{inquilino}| Yes | Nome do seu inquilino Azure AD B2C|
|{política}| Yes| O fluxo do utilizador a ser executado. Especifique o nome de um fluxo de utilizador que criou no seu inquilino Azure AD B2C. Por exemplo: `b2c_1_sign_in` `b2c_1_sign_up` , ou . `b2c_1_edit_profile` . |
| client_id | Yes | O ID da aplicação que o [portal Azure](https://portal.azure.com/) atribuiu à sua aplicação. |
| response_type | Yes | Deve incluir `id_token` para o iniciar sê-in OpenID Connect. Também pode incluir o tipo de `token` resposta. Se `token` utilizar, a sua aplicação pode receber imediatamente um token de acesso a partir do ponto final autorizado, sem fazer um segundo pedido ao ponto final autorizado.  Se utilizar o `token` tipo de resposta, o `scope` parâmetro deve conter um âmbito que indique qual o recurso para o qual emitir o token. |
| redirect_uri | No | O URI redirecionado da sua app, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a um dos URIs de redirecionamento que registou no portal, exceto que deve estar codificado por URL. |
| response_mode | No | Especifica o método a utilizar para enviar o símbolo resultante de volta para a sua aplicação.  Para fluxos implícitos, use `fragment` . |
| scope | Yes | Uma lista de âmbitos separados pelo espaço. Um único valor de âmbito indica ao Azure AD ambas as permissões que estão a ser solicitadas. O `openid` âmbito indica uma permissão para assinar no utilizador e obter dados sobre o utilizador sob a forma de fichas de identificação. O `offline_access` âmbito é opcional para aplicações web. Indica que a sua aplicação precisa de um token de atualização para o acesso de longa duração aos recursos. |
| state | No | Um valor incluído no pedido que também é devolvido na resposta simbólica. Pode ser uma série de conteúdos que queiras usar. Normalmente, um valor único gerado aleatoriamente é usado para evitar ataques de falsificação de pedidos de trans-locais. O Estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página em que se encontravam. |
| nonce | Yes | Um valor incluído no pedido (gerado pela app) que está incluído no token de ID resultante como uma reclamação. A aplicação pode então verificar este valor para mitigar os ataques de reprodução de token. Normalmente, o valor é uma corda aleatória e única que pode ser usada para identificar a origem do pedido. |
| rápido | No | O tipo de interação do utilizador que é necessária. Atualmente, o único valor válido é `login` . Este parâmetro obriga o utilizador a introduzir as suas credenciais nesse pedido. Uma única inscrição não faz efeito. |

Neste momento, pede-se ao utilizador que complete o fluxo de trabalho da apólice. O utilizador poderá ter de introduzir o seu nome de utilizador e senha, iniciar sação com identidade social, inscrever-se no diretório ou qualquer outro número de passos. As ações do utilizador dependem da definição do fluxo do utilizador.

Depois de o utilizador completar o fluxo do utilizador, o Azure AD devolve uma resposta à sua aplicação pelo valor utilizado para o valor utilizado `redirect_uri` . Utiliza o método especificado no `response_mode` parâmetro. A resposta é exatamente a mesma para cada um dos cenários de ação do utilizador, independentemente do fluxo de utilizador que foi executado.

### <a name="successful-response"></a>Resposta bem sucedida
Uma resposta bem sucedida que usa `response_mode=fragment` e se parece com o `response_type=id_token+token` seguinte, com quebras de linha para legibilidade:

```http
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
| token_type | O valor do tipo símbolo. O único tipo que a Azure AD suporta é o Portador. |
| expires_in | O tempo de duração do token de acesso é válido (em segundos). |
| scope | Os âmbitos para os que o símbolo é válido. Também pode utilizar os âmbitos para cache tokens para utilização posterior. |
| id_token | O sinal de identificação que a aplicação solicitou. Pode utilizar o token de identificação para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. Para obter mais informações sobre fichas de identificação e seus conteúdos, consulte a [referência simbólica AZURE AD B2C](tokens-overview.md). |
| state | Se um `state` parâmetro for incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deve verificar se os `state` valores do pedido e resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro
As respostas de erro também podem ser enviadas para o URI de redirecionamento para que a aplicação possa manuseá-las adequadamente:

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --------- | ----------- |
| erro | Um código usado para classificar tipos de erros que ocorrem. |
| error_description | Uma mensagem de erro específica que pode ajudá-lo a identificar a causa principal de um erro de autenticação. |
| state | Se um `state` parâmetro for incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deve verificar se os `state` valores do pedido e resposta são idênticos.|

## <a name="validate-the-id-token"></a>Validar o token de ID

Receber um token de identificação não é suficiente para autenticar o utilizador. Valide a assinatura do token de ID e verifique as reclamações no token de acordo com os requisitos da sua aplicação. Azure AD B2C utiliza [tokens web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chaves públicas para assinar fichas e verificar se são válidos.

Muitas bibliotecas de código aberto estão disponíveis para validar JWTs, dependendo do idioma que você prefere usar. Considere explorar bibliotecas de código aberto disponíveis em vez de implementar a sua própria lógica de validação. Pode utilizar as informações deste artigo para ajudá-lo a aprender a usar corretamente essas bibliotecas.

Azure AD B2C tem um ponto final de ligação de metadados OpenID. Uma aplicação pode usar o ponto final para obter informações sobre O Azure AD B2C no tempo de execução. Esta informação inclui pontos finais, conteúdo simbólico e chaves de assinatura simbólicas. Existe um documento de metadados JSON para cada fluxo de utilizador no seu inquilino Azure AD B2C. Por exemplo, o documento de metadados para o fluxo de utilizador b2c_1_sign_in no fabrikamb2c.onmicrosoft.com inquilino está localizado em:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Uma das propriedades deste documento de configuração é o `jwks_uri` . O valor para o mesmo fluxo de utilizador seria:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Para determinar qual o fluxo do utilizador utilizado para assinar um token de ID (e de onde ir buscar os metadados), tem duas opções. Em primeiro lugar, o nome de fluxo do utilizador está incluído na `acr` reclamação em `id_token` . Para obter informações sobre como analisar as reclamações a partir de um token de identificação, consulte a [referência simbólica Azure AD B2C](tokens-overview.md). A sua outra opção é codificar o fluxo do utilizador no valor do `state` parâmetro quando emite o pedido. Em seguida, descodificar o `state` parâmetro para determinar qual o fluxo do utilizador que foi utilizado. Qualquer um dos métodos é válido.

Depois de ter adquirido o documento de metadados do ponto final de metadados OpenID Connect, pode utilizar as teclas públicas RSA-256 (localizadas neste ponto final) para validar a assinatura do token de ID. Pode haver várias chaves listadas neste ponto final a qualquer momento, cada uma identificada por um `kid` . O cabeçalho `id_token` também contém uma `kid` reivindicação. Indica qual destas chaves foi usada para assinar o sinal de identificação. Para obter mais informações, incluindo aprender sobre [a validação de fichas,](tokens-overview.md)consulte a [referência simbólica Azure AD B2C](tokens-overview.md).
<!--TODO: Improve the information on this-->

Depois de validar a assinatura do token de ID, várias reclamações requerem verificação. Por exemplo:

* Valide a `nonce` alegação para evitar ataques de repetição de fichas. O seu valor deve ser o que especificou no pedido de inscrição.
* Valide a `aud` alegação para garantir que o token de ID foi emitido para a sua aplicação. O seu valor deve ser o ID da aplicação da sua aplicação.
* Validar as `iat` `exp` alegações para garantir que o token de ID não expirou.

Várias outras validações que deve realizar são descritas em detalhe na [Especificação de Núcleo de Ligação OpenID](https://openid.net/specs/openid-connect-core-1_0.html). Também pode querer validar reclamações adicionais, dependendo do seu cenário. Algumas validações comuns incluem:

* Garantindo que o utilizador ou organização se inscreveu para a aplicação.
* Garantindo que o utilizador tem a devida autorização e privilégios.
* Garantindo que ocorreu uma certa força de autenticação, como por exemplo através da utilização da Autenticação Multi-Factor Azure.

Para obter mais informações sobre as reclamações num token de identificação, consulte a [referência simbólica Azure AD B2C](tokens-overview.md).

Depois de ter validado o token de ID, pode iniciar uma sessão com o utilizador. Na sua aplicação, utilize as reclamações no token de ID para obter informações sobre o utilizador. Estas informações podem ser usadas para exibição, registos, autorização, e assim por diante.

## <a name="get-access-tokens"></a>Obter fichas de acesso
Se a única coisa que as suas aplicações web precisam de fazer é executar os fluxos dos utilizadores, pode saltar as próximas secções. As informações nas seguintes secções aplicam-se apenas a aplicações web que necessitem de fazer chamadas autenticadas para uma API web, e que estejam protegidas pelo Azure AD B2C.

Agora que assinou o utilizador na sua aplicação de uma página única, pode obter fichas de acesso para chamar APIs web que são protegidas pelo Azure AD. Mesmo que já tenha recebido um token usando o `token` tipo de resposta, pode usar este método para adquirir fichas para recursos adicionais sem redirecionar o utilizador para iniciar novamente o súm.

Num fluxo típico de aplicativos web, faria um pedido para o `/token` ponto final. No entanto, o ponto final não suporta pedidos de CORS, por isso fazer chamadas do AJAX para obter um token de atualização não é uma opção. Em vez disso, pode utilizar o fluxo implícito num elemento de iframe HTML oculto para obter novos tokens para outras APIs web. Aqui está um exemplo, com quebras de linha para a legibilidade:

```http
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
{política}| Necessário| O fluxo do utilizador a ser executado. Especifique o nome de um fluxo de utilizador que criou no seu inquilino Azure AD B2C. Por exemplo: `b2c_1_sign_in` `b2c_1_sign_up` , ou . `b2c_1_edit_profile` . |
| client_id |Necessário |O ID da aplicação atribuído à sua aplicação no [portal Azure.](https://portal.azure.com) |
| response_type |Necessário |Deve incluir `id_token` para o iniciar sê-in OpenID Connect.  Pode também incluir o tipo de `token` resposta. Se utilizar `token` aqui, a sua aplicação pode receber imediatamente um sinal de acesso a partir do ponto final autorizado, sem fazer um segundo pedido ao ponto final autorizado. Se utilizar o `token` tipo de resposta, o `scope` parâmetro deve conter um âmbito que indique qual o recurso para o qual emitir o token. |
| redirect_uri |Recomendado |O URI redirecionado da sua app, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a um dos URIs de redirecionamento que registou no portal, exceto que deve estar codificado por URL. |
| scope |Necessário |Uma lista de âmbitos separados pelo espaço.  Para obter fichas, inclua todos os âmbitos que você precisa para o recurso pretendido. |
| response_mode |Recomendado |Especifica o método que é usado para enviar o símbolo resultante de volta para a sua aplicação. Para fluxo implícito, use `fragment` . Dois outros modos podem ser `query` especificados, mas não funcionam no fluxo `form_post` implícito. |
| state |Recomendado |Um valor incluído no pedido que é devolvido na resposta simbólica.  Pode ser uma série de conteúdos que queiras usar.  Normalmente, um valor único gerado aleatoriamente é usado para evitar ataques de falsificação de pedidos de trans-locais.  O Estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer. Por exemplo, a página ou visualização do utilizador estava ligado. |
| nonce |Necessário |Um valor incluído no pedido, gerado pela app, que está incluído no token de ID resultante como uma reclamação.  A aplicação pode então verificar este valor para mitigar os ataques de reprodução de token. Normalmente, o valor é uma cadeia aleatória e única que identifica a origem do pedido. |
| rápido |Necessário |Para refrescar e obter fichas num iframe oculto, use `prompt=none` para garantir que o iframe não fique preso na página de inscrição e regresse imediatamente. |
| login_hint |Necessário |Para atualizar e obter fichas num iframe oculto, inclua o nome de utilizador do utilizador nesta dica para distinguir entre várias sessões que o utilizador pode ter num dado momento. Pode extrair o nome de utilizador de um pré-aviso de inscrição utilizando a `preferred_username` reclamação `profile` (o âmbito é necessário para receber a `preferred_username` reclamação). |
| domain_hint |Necessário |Pode ser `consumers` ou `organizations`.  Para refrescar e obter fichas num iframe oculto, inclua o `domain_hint` valor no pedido.  Extrair a `tid` reclamação do sinal de ID de um início de sedutor anterior para determinar qual o valor a utilizar (o `profile` âmbito é necessário para receber a `tid` reclamação). Se o `tid` valor da reclamação `9188040d-6c67-4c5b-b112-36a304b66dad` for, utilize `domain_hint=consumers` .  Caso contrário, `domain_hint=organizations` utilize. |

Ao definir o `prompt=none` parâmetro, este pedido sucede ou falha imediatamente, e regressa à sua aplicação.  Uma resposta bem sucedida é enviada para a sua aplicação no URI de redirecionamento indicado, utilizando o método especificado no `response_mode` parâmetro.

### <a name="successful-response"></a>Resposta bem sucedida
Uma resposta bem sucedida usando `response_mode=fragment` parece este exemplo:

```http
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
| token_type |O tipo de símbolo será sempre o Portador. |
| state |Se um `state` parâmetro for incluído no pedido, o mesmo valor deve aparecer na resposta. A aplicação deve verificar se os `state` valores do pedido e resposta são idênticos. |
| expires_in |Quanto tempo o token de acesso é válido (em segundos). |
| scope |Os âmbitos para os que o token de acesso é válido. |

### <a name="error-response"></a>Resposta de erro
As respostas de erro também podem ser enviadas para o URI de redirecionamento para que a aplicação possa manuseá-las adequadamente.  Para `prompt=none` , um erro esperado parece-se com este exemplo:

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem. Também pode usar a corda para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudá-lo a identificar a causa principal de um erro de autenticação. |

Se receber este erro no pedido do iframe, o utilizador deve iniciar serção interativamente para recuperar um novo token.

## <a name="refresh-tokens"></a>Fichas de atualização
As fichas de identificação e os tokens de acesso expiram após um curto período de tempo. A sua aplicação deve estar preparada para refrescar estes tokens periodicamente.  Para refrescar qualquer tipo de token, execute o mesmo pedido de iframe oculto que usamos num exemplo anterior, usando o parâmetro para controlar os `prompt=none` passos AD AD do Azure.  Para receber um novo `id_token` valor, certifique-se de usar `response_type=id_token` e um `scope=openid` `nonce` parâmetro.

## <a name="send-a-sign-out-request"></a>Enviar um pedido de sinalização
Quando pretender tirar o utilizador da aplicação, redirecione o utilizador para Azure AD para assinar. Se não redirecionar o utilizador, poderão ser capazes de reautorar para a sua app sem introduzir novamente as suas credenciais, uma vez que têm uma sessão de sessão de sessão de inscrição válida com a Azure AD.

Pode simplesmente redirecionar o utilizador para o `end_session_endpoint` que está listado no mesmo documento de metadados OpenID Connect descrito em [Validar o token de ID](#validate-the-id-token). Por exemplo:

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parâmetro | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| {inquilino} | Yes | Nome do seu inquilino Azure AD B2C |
| {política} | Yes | O fluxo do utilizador que pretende utilizar para assinar o utilizador fora da sua aplicação. |
| post_logout_redirect_uri | No | O URL para o que o utilizador deve ser redirecionado após a assinatura com sucesso. Se não estiver incluído, o Azure AD B2C mostra ao utilizador uma mensagem genérica. |
| state | No | Se um `state` parâmetro for incluído no pedido, o mesmo valor deve aparecer na resposta. O pedido deve verificar se os `state` valores do pedido e resposta são idênticos. |


> [!NOTE]
> Direcionar o utilizador para a `end_session_endpoint` eliminação de alguns dos únicos estados de entrada do utilizador com O Azure AD B2C. No entanto, não assina o utilizador fora da sessão de provedor de identidade social do utilizador. Se o utilizador selecionar o mesmo fornecedor de identidade durante uma sintrodução subsequente, o utilizador é reautentado sem introduzir as suas credenciais. Se um utilizador quiser assinar a sua aplicação Azure AD B2C, não significa necessariamente que queira assinar completamente fora da sua conta de Facebook, por exemplo. No entanto, para as contas locais, a sessão do utilizador terminará corretamente.
>

## <a name="next-steps"></a>Passos seguintes

### <a name="code-sample-azure-ad-b2c-with-microsoft-authentication-library-for-javascript"></a>Amostra de código: Azure AD B2C com a Microsoft Authentication Library for JavaScript

[Aplicação de uma página única construída com msal.js para Azure AD B2C][github-msal-js-example] (GitHub)

Esta amostra no GitHub destina-se a ajudá-lo a começar a Azure AD B2C numa simples aplicação web construída com [msal.js][github-msal-js] e usando a autenticação de estilo pop-up.

<!-- Links - EXTERNAL -->
[github-msal-js-example]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
[github-msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
