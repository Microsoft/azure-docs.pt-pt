---
title: Início de sessão de página única com fluxo implícito - Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como adicionar o início de sessão de página única sessão com o fluxo implícito de OAuth 2.0 com o Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1d415686e4d8a10043df59aa6bf58a5ed4be0149
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154022"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Página única início de sessão com o fluxo implícito de OAuth 2.0 no Azure Active Directory B2C

Muitos aplicativos modernos têm um aplicação de página única front-end que é principalmente escrito em JavaScript. Muitas vezes, a aplicação é escrita usando uma estrutura como o React, Angular ou VUE. Aplicações de página única e outras aplicações de JavaScript que são executados principalmente num navegador tem alguns desafios adicionais para a autenticação:

- As características de segurança destas aplicações são diferentes dos aplicativos web baseados em servidor tradicional.
- Muitos servidores de autorização e fornecedores de identidade não suportam os recursos de várias origens (CORS) pedidos de partilha.
- Os redirecionamentos de navegador de página inteira na direção oposta a aplicação podem ser invasivas para a experiência do usuário.

Para suportar estas aplicações, o Azure Active Directory B2C (Azure AD B2C) usa o fluxo implícito de OAuth 2.0. O fluxo de concessão implícita de autorização de OAuth 2.0 é descrito na [secção 4.2 da especificação de OAuth 2.0](https://tools.ietf.org/html/rfc6749). No fluxo implícito, a aplicação recebe tokens diretamente a partir do Azure Active Directory (Azure AD) autorizar o ponto de extremidade, sem qualquer troca de servidor a servidor. Todos os lógica de autenticação e manipulação de sessão são feitas inteiramente no cliente JavaScript, sem o redirecionamento da página adicional.

O Azure AD B2C estende o fluxo implícito de OAuth 2.0 padrão a mais do que a autenticação e autorização simples. O Azure AD B2C apresenta os [parâmetro de política](active-directory-b2c-reference-policies.md). Com o parâmetro de política, pode utilizar o OAuth 2.0 para adicionar políticas para a sua aplicação, tais como inscrição, início de sessão e os fluxos de utilizador de gestão de perfil. Nos pedidos HTTP de exemplo neste artigo, **fabrikamb2c.onmicrosoft.com** é utilizado como um exemplo. Pode substituir `fabrikamb2c` com o nome do seu inquilino se tiver um e criou um fluxo de utilizador.

O fluxo de início de sessão implícito é parecido com a figura a seguir. Cada passo é descrito em detalhe posteriormente neste artigo.

![OpenID Connect pistas de diagrama](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Enviar pedidos de autenticação

Quando seu aplicativo web precisa para autenticar o usuário e executar um fluxo de utilizador, pode direcionar o utilizador para o `/authorize` ponto final. O utilizador efetua uma ação consoante o fluxo de utilizador.

Este pedido, o cliente indica as permissões que ele necessita obter do usuário na `scope` parâmetro e o fluxo de utilizador seja executado `p` parâmetro. Três exemplos são fornecidos nas secções seguintes (com quebras de linha para facilitar a leitura), cada um usando um fluxo de utilizador diferente. Para ter uma noção de como funciona cada solicitação, tente colar o pedido para um browser e executá-lo. Pode substituir `fabrikamb2c` com o nome do seu inquilino se tiver um e criou um fluxo de utilizador.

### <a name="use-a-sign-in-user-flow"></a>Utilizar um fluxo de início de sessão do utilizador

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-user-flow"></a>Utilizar um fluxo de inscrição de utilizador
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-user-flow"></a>Utilizar um fluxo de utilizador de Editar perfil
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parâmetro | Necessário | Descrição |
| --------- | -------- | ----------- |
| client_id | Sim | ID de aplicação que o [portal do Azure](https://portal.azure.com/) atribuído à sua aplicação. |
| response_type | Sim | Tem de incluir `id_token` OpenID Connect para início de sessão. Também pode incluir o tipo de resposta `token`. Se usar `token`, a aplicação imediatamente pode receber um token de acesso de ponto final de autorização, sem fazer uma segunda solicitação para o ponto final de autorização.  Se utilizar o `token` tipo de resposta, o `scope` parâmetro tem de conter um âmbito que indica qual para emitir o token para o recurso. |
| redirect_uri | Não | O URI de redirecionamento de seu aplicativo, onde as respostas podem ser enviadas e recebidas pela sua aplicação. Ele deve corresponder exatamente um dos URIs que registou no portal de redirecionamento, exceto pelo fato de que tem de ser codificados de URL. |
| response_mode | Não | Especifica o método a utilizar para enviar a cópia de token resultante à sua aplicação.  Para fluxos implícitos, utilize `fragment`. |
| scope | Sim | Uma lista de âmbitos separadas por espaços. Indica um valor de âmbito único para o Azure AD ambas as permissões que estão a ser solicitada. O `openid` âmbito indica uma permissão para iniciar a sessão do utilizador e obter dados sobre o utilizador na forma de tokens de ID. O `offline_access` âmbito é opcional para aplicações web. Ele indica que o aplicativo precisa de um token de atualização de longa duração acesso a recursos. |
| state | Não | Um valor incluído no pedido que também é devolvido na resposta de token. Pode ser uma cadeia de caracteres de qualquer conteúdo que pretende utilizar. Normalmente, um valor exclusivo, gerado aleatoriamente, é usado, para impedir ataques de falsificação de solicitação entre sites. O estado também é usado para codificar as informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, como a página onde estavam. |
| nonce | Sim | Um valor incluído no pedido (gerado pela aplicação) que está incluído no token de ID resultante como uma afirmação. A aplicação pode, em seguida, verifique se este valor para mitigar ataques de repetição de token. Normalmente, o valor é uma cadeia aleatória, exclusiva que pode ser utilizada para identificar a origem do pedido. |
| p | Sim | A política para executar. É o nome da política (fluxo de utilizador) que é criada no seu inquilino do Azure AD B2C. O valor de nome de política deve começar com **b2c\_1\_** . |
| linha de comandos | Não | O tipo de interação do utilizador que é necessário. Atualmente, o único valor válido é `login`. Este parâmetro força o utilizador introduza as credenciais desse pedido. Início de sessão único não entre em vigor. |

Neste momento, é pedido ao utilizador para concluir o fluxo de trabalho da política. O utilizador poderá ter de introduzir o respetivo nome de utilizador e palavra-passe, inicie sessão com uma identidade de redes sociais, início de sessão cópia de segurança para o diretório ou qualquer outro número de passos. Ações do usuário dependem de como o fluxo de utilizador está definido.

Depois do utilizador concluir o fluxo de utilizador, o AD do Azure devolve uma resposta à sua aplicação com o valor que utilizou para `redirect_uri`. Ele usa o método especificado no `response_mode` parâmetro. A resposta é exatamente o mesmo para cada um dos cenários de ação do utilizador, independentemente do fluxo de utilizador que foi executado.

### <a name="successful-response"></a>Resposta com êxito
Uma resposta com êxito, que utiliza `response_mode=fragment` e `response_type=id_token+token` se parece com o seguinte, com quebras de linha para melhorar a legibilidade:

```
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
| access_token | O token de acesso que a aplicação pedida. |
| token_type | O valor de tipo de token. O único tipo que o Azure AD suporta é portador. |
| expires_in | O período de tempo que o token de acesso é válido (em segundos). |
| scope | Os âmbitos que o token é válido para. Também pode utilizar âmbitos para tokens de cache para utilização posterior. |
| id_token | O token de ID que a aplicação pedida. Pode usar o token de ID para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. Para obter mais informações sobre os tokens de ID e seu conteúdo, consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| state | Se um `state` parâmetro está incluído na solicitação, o mesmo valor deve aparecer na resposta. A aplicação deve verificar se o `state` valores no pedido e resposta são idênticos. |

### <a name="error-response"></a>Resposta de erro
Respostas de erro também podem ser enviadas para o URI de redirecionamento para que a aplicação pode processar corretamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --------- | ----------- |
| error | Um código utilizado para classificar tipos de erros que ocorrem. |
| error_description | Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação. |
| state | Se um `state` parâmetro está incluído na solicitação, o mesmo valor deve aparecer na resposta. A aplicação deve verificar se o `state` valores no pedido e resposta são idênticos.|

## <a name="validate-the-id-token"></a>Validar o token de ID

Receber um token de ID não é suficiente para autenticar o utilizador. Validar a assinatura do token de ID e verifique se as afirmações no token de conformidade com os requisitos da sua aplicação. Azure AD B2C utiliza [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar os tokens e certifique-se de que são válidas.

Muitas bibliotecas de código-fonte aberto estão disponíveis para validar JWTs, dependendo do idioma a que se preferir usar. Considere a explorar as bibliotecas de código aberto disponíveis, em vez de implementar sua própria lógica de validação. Pode utilizar as informações neste artigo para ajudar a aprender como usar adequadamente dessas bibliotecas.

O Azure AD B2C tem um ponto final de metadados OpenID Connect. Uma aplicação pode utilizar o ponto final para obter informações sobre o Azure AD B2C no tempo de execução. Estas informações incluem pontos finais, conteúdo de token e chaves de assinatura de tokens. Existe um documento de metadados JSON para cada fluxo de utilizador no seu inquilino do Azure AD B2C. Por exemplo, o documento de metadados para o fluxo de utilizador no inquilino fabrikamb2c.onmicrosoft.com b2c_1_sign_in está localizado em:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Uma das propriedades deste documento de configuração é o `jwks_uri`. O valor para o mesmo fluxo de utilizador deve ser:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

Para determinar o fluxo de utilizador foi utilizado para assinar um token de ID (e onde obter os metadados da), tem duas opções. Em primeiro lugar, o nome do fluxo de utilizador está incluído nos `acr` de afirmação no `id_token`. Para obter informações sobre como analisar as afirmações a partir de um token de ID, consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). A outra opção é codificar o fluxo de utilizador no valor do `state` parâmetro quando emitir o pedido. Em seguida, decodificar o `state` parâmetro para determinar o fluxo de utilizador foi utilizado. Qualquer um dos métodos é válido.

Depois de adquirir o documento de metadados a partir do ponto final de metadados OpenID Connect, pode utilizar as chaves públicas RSA-256 (localizadas neste ponto final) para validar a assinatura do token de ID. Podem existir várias chaves listadas neste ponto final num determinado momento, cada identificado por uma `kid`. O cabeçalho de `id_token` também contém um `kid` de afirmação. Ele indica que estas chaves foi utilizado para assinar o token de ID. Para obter mais informações, incluindo saber mais sobre [tokens de validação](active-directory-b2c-reference-tokens.md), consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

Depois de validar a assinatura do token de ID, várias afirmações exigem verificação. Por exemplo:

* Validar o `nonce` impedir ataques de repetição de token de afirmação. O valor deve ser especificado no pedido de início de sessão.
* Validar o `aud` de afirmação para se certificar de que o token de ID foi emitido para a sua aplicação. O valor deve ser o ID da aplicação da sua aplicação.
* Validar a `iat` e `exp` afirmações para se certificar de que o token de ID não expirou.

Vários validações mais que deve executar são descritas detalhadamente na [especificação do OpenID Connect Core](https://openid.net/specs/openid-connect-core-1_0.html). Pode também querer validar afirmações adicionais, dependendo do seu cenário. Algumas validações comuns incluem:

* Garantir que o utilizador ou a organização tiver se inscrito para a aplicação.
* Garantir que o utilizador tem autorização adequada e privilégios.
* Garantir que um determinada força da autenticação ocorreu, como, por exemplo, ao utilizar o Azure multi-factor Authentication.

Para obter mais informações sobre as afirmações no token de ID, consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md).

Depois de validar o token de ID, pode iniciar uma sessão com o utilizador. Na sua aplicação, utilize as afirmações no token de ID para obter informações sobre o utilizador. Estas informações podem ser utilizadas para apresentar, registos, autorização e assim por diante.

## <a name="get-access-tokens"></a>Obter os tokens de acesso
Se a única coisa que seus aplicativos web precisa fazer é executar fluxos de utilizador, pode ignorar as próximas seções. As informações nas secções seguintes são aplicáveis apenas a aplicações web que precisam fazer chamadas autenticadas para uma API web, e que estão protegido pelo Azure AD B2C.

Agora que o utilizador ter iniciado sessão na sua aplicação de página única, pode obter os tokens de acesso para chamadas de web APIs que estão protegidas pelo Azure AD. Mesmo que já tenha recebido um token ao utilizar o `token` tipo de resposta, pode utilizar este método para adquirir tokens para recursos adicionais sem redirecionar o utilizador iniciar sessão novamente.

Num fluxo de aplicativo web típico, teria de fazer um pedido para o `/token` ponto final. No entanto, o ponto final não suporta solicitações CORS, portanto, fazer chamadas AJAX para obter e tokens de atualização não é uma opção. Em vez disso, pode utilizar o fluxo implícito num elemento de iframe HTML oculto para obter novos tokens para outras APIs web. Eis um exemplo, com quebras de linha para melhorar a legibilidade:

```

https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
&p=b2c_1_sign_in
```

| Parâmetro | Obrigatório? | Descrição |
| --- | --- | --- |
| client_id |Necessário |O ID da aplicação atribuído à sua aplicação no [portal do Azure](https://portal.azure.com). |
| response_type |Necessário |Tem de incluir `id_token` OpenID Connect para início de sessão.  Pode também incluir o tipo de resposta `token`. Se usar `token` aqui, a aplicação imediatamente recebe um token de acesso de ponto final de autorização, sem fazer uma segunda solicitação para o ponto final de autorização. Se utilizar o `token` tipo de resposta, o `scope` parâmetro tem de conter um âmbito que indica qual para emitir o token para o recurso. |
| redirect_uri |Recomendado |O URI de redirecionamento de seu aplicativo, onde as respostas podem ser enviadas e recebidas pela sua aplicação. Ele deve corresponder exatamente um dos URIs que registou no portal de redirecionamento, exceto pelo fato de que tem de ser codificados de URL. |
| scope |Necessário |Uma lista de âmbitos separadas por espaços.  Para obter os tokens, incluem todos os âmbitos que necessita para o recurso pretendido. |
| response_mode |Recomendado |Especifica o método que é utilizado para enviar a cópia de token resultante à sua aplicação.  Pode ser `query`, `form_post`, ou `fragment`. |
| state |Recomendado |Um valor incluído no pedido que é devolvido na resposta de token.  Pode ser uma cadeia de caracteres de qualquer conteúdo que pretende utilizar.  Normalmente, um valor exclusivo, gerado aleatoriamente, é usado, para impedir ataques de falsificação de solicitação entre sites.  O estado também é usado para codificar as informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu. Por exemplo, a página ou a vista foi o utilizador no. |
| nonce |Necessário |Um valor incluído na solicitação, gerada pela aplicação, que está incluída no token de ID resultante como uma afirmação.  A aplicação pode, em seguida, verifique se este valor para mitigar ataques de repetição de token. Normalmente, o valor é uma cadeia aleatória, exclusiva que identifica a origem do pedido. |
| linha de comandos |Necessário |Para atualizar e obter os tokens num iframe oculto, utilize `prompt=none` para garantir que o iframe não ficar preso na página de início de sessão e retorna imediatamente. |
| login_hint |Necessário |Para atualizar e obter tokens num iframe oculto, inclua o nome de utilizador do utilizador desta sugestão para distinguir entre várias sessões, que o utilizador pode ter um determinado momento. É possível extrair o nome de utilizador de um anterior início de sessão utilizando o `preferred_username` de afirmação. |
| domain_hint |Necessário |Pode ser `consumers` ou `organizations`.  Para atualizar e obter os tokens num iframe oculto, incluem o `domain_hint` valor no pedido.  Extrair o `tid` afirmação do token de ID de um início de sessão-in anterior para determinar qual o valor a utilizar.  Se o `tid` é o valor de afirmação `9188040d-6c67-4c5b-b112-36a304b66dad`, utilize `domain_hint=consumers`.  Caso contrário, utilize `domain_hint=organizations`. |

Ao definir o `prompt=none` parâmetro, este pedido ou com êxito ou falha imediatamente e retorna para seu aplicativo.  Uma resposta com êxito é enviada para a aplicação no URI, de redirecionamento indicado, usando o método especificado no `response_mode` parâmetro.

### <a name="successful-response"></a>Resposta com êxito
Uma resposta com êxito utilizando `response_mode=fragment` se parece com este exemplo:

```
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parâmetro | Descrição |
| --- | --- |
| access_token |O token que a aplicação pedida. |
| token_type |O tipo de token será sempre portador. |
| state |Se um `state` parâmetro está incluído na solicitação, o mesmo valor deve aparecer na resposta. A aplicação deve verificar se o `state` valores no pedido e resposta são idênticos. |
| expires_in |O tempo que o token de acesso é válido (em segundos). |
| scope |Os âmbitos que o token de acesso é válido para. |

### <a name="error-response"></a>Resposta de erro
Respostas de erro também podem ser enviadas para o URI de redirecionamento para que a aplicação pode processar corretamente.  Para `prompt=none`, um erro esperado aspeto deste exemplo:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem. Também pode utilizar a cadeia de caracteres para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação. |

Se receber este erro no pedido de iframe, o utilizador tem interativamente iniciar sessão novamente para obter um novo token.

## <a name="refresh-tokens"></a>Tokens de atualização
Tokens de ID e tokens de acesso expiram após um curto período de tempo. A aplicação deve estar preparada para atualizar estes tokens periodicamente.  Para atualizar de qualquer tipo de token, executar o mesmo pedido iframe ocultos utilizámos no exemplo anterior, usando o `prompt=none` parâmetro para controlar os passos do Azure AD.  Para receber um novo `id_token` valor, certifique-se de que utiliza `response_type=id_token` e `scope=openid`e um `nonce` parâmetro.

## <a name="send-a-sign-out-request"></a>Enviar um pedido de fim de sessão
Quando deseja iniciar sessão do utilizador fora da aplicação, redireciona o utilizador para o Azure AD para terminar sessão. Se não a redirecionar o utilizador, poderá conseguir autenticar-se à sua aplicação sem introduzir as respetivas credenciais novamente porque têm uma única início de sessão em sessão válida com o Azure AD.

Simplesmente pode redirecionar o utilizador para o `end_session_endpoint` que está listado na ligação de OpenID mesmo documento de metadados descrito na [validar o token de ID](#validate-the-id-token). Por exemplo:

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parâmetro | Obrigatório? | Descrição |
| --- | --- | --- |
| p |Necessário |A política a utilizar para iniciar sessão do utilizador fora do seu aplicativo. |
| post_logout_redirect_uri |Recomendado |O URL que o utilizador deve ser redirecionado para depois de concluída com êxito fim de sessão. Se não estiver incluída, o Azure AD B2C apresenta uma mensagem genérica para o utilizador. |

> [!NOTE]
> Que direciona o utilizador para o `end_session_endpoint` limpa alguns do Estado do utilizador único início de sessão com o Azure AD B2C. No entanto, ele não iniciar sessão do utilizador sem sessão de fornecedor de identidade social do utilizador. Se o utilizador selecionar o mesmo identificar o fornecedor durante um subsequente início de sessão, o utilizador é reautenticar, sem introduzir as respetivas credenciais. Se um usuário deseja terminar a sua aplicação do Azure AD B2C, isso não significa necessariamente desejam completamente terminar sessão sua conta do Facebook, por exemplo. No entanto, para contas locais, a sessão do utilizador será terminada corretamente.
> 

