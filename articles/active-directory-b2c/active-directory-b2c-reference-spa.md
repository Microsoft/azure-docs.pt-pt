---
title: Logon de página única usando o fluxo implícito-Azure Active Directory B2C
description: Saiba como adicionar entrada de página única usando o fluxo implícito do OAuth 2,0 com Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ab8c8a582b90976ada20b1e970c9e9648d14b2a9
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596443"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Entrada de página única usando o fluxo implícito do OAuth 2,0 no Azure Active Directory B2C

Muitos aplicativos modernos têm um front-end de aplicativo de página única que é escrito principalmente em JavaScript. Geralmente, o aplicativo é escrito usando uma estrutura como reagir, angulares ou Vue. js. Aplicativos de página única e outros aplicativos JavaScript que são executados principalmente em um navegador têm alguns desafios adicionais para a autenticação:

- As características de segurança desses aplicativos são diferentes dos aplicativos Web tradicionais baseados em servidor.
- Muitos servidores de autorização e provedores de identidade não dão suporte a solicitações de CORS (compartilhamento de recursos entre origens).
- O navegador de página inteira redireciona para fora do aplicativo pode ser invasivo para a experiência do usuário.

Para dar suporte a esses aplicativos, Azure Active Directory B2C (Azure AD B2C) usa o fluxo implícito do OAuth 2,0. O fluxo de concessão implícita de autorização do OAuth 2,0 é descrito na [seção 4,2 da especificação do OAuth 2,0](https://tools.ietf.org/html/rfc6749). No fluxo implícito, o aplicativo recebe tokens diretamente do ponto de extremidade de autorização do Azure Active Directory (Azure AD), sem qualquer troca de servidor para servidor. Toda a lógica de autenticação e manipulação de sessão é feita inteiramente no cliente JavaScript com um redirecionamento de página ou uma caixa pop-up.

Azure AD B2C estende o fluxo implícito padrão do OAuth 2,0 para mais do que autenticação e autorização simples. Azure AD B2C introduz o [parâmetro de política](active-directory-b2c-reference-policies.md). Com o parâmetro de política, você pode usar o OAuth 2,0 para adicionar políticas ao seu aplicativo, como os fluxos de usuário de inscrição, de entrada e de gerenciamento de perfil. No exemplo de solicitações HTTP neste artigo, **{Tenant}. o onmicrosoft. com** é usado como exemplo. Substitua `{tenant}` pelo nome do seu locatário se você tiver um e também tiver criado um fluxo de usuário.

O fluxo de entrada implícito é semelhante à figura a seguir. Cada etapa é descrita em detalhes posteriormente neste artigo.

![Diagrama de estilo de raia mostrando o fluxo implícito do OpenID Connect](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Enviar solicitações de autenticação

Quando seu aplicativo Web precisa autenticar o usuário e executar um fluxo de usuário, ele pode direcionar o usuário para o ponto de extremidade `/authorize`. O usuário executa a ação dependendo do fluxo do usuário.

Nessa solicitação, o cliente indica as permissões que ele precisa adquirir do usuário no parâmetro `scope` e o fluxo do usuário a ser executado. Para ter uma ideia de como a solicitação funciona, tente colar a solicitação em um navegador e executá-la. Substitua `{tenant}` pelo nome do seu locatário Azure AD B2C. Substitua `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` pela ID do aplicativo que você registrou anteriormente em seu locatário. Substitua `{policy}` pelo nome de uma política que você criou em seu locatário, por exemplo `b2c_1_sign_in`.

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
|regras| Sim| O fluxo do usuário a ser executado. Especifique o nome de um fluxo de usuário que você criou em seu locatário Azure AD B2C. Por exemplo: `b2c_1_sign_in`, `b2c_1_sign_up` ou `b2c_1_edit_profile`. |
| client_id | Sim | A ID do aplicativo que o [portal do Azure](https://portal.azure.com/) atribuído ao seu aplicativo. |
| response_type | Sim | Deve incluir `id_token` para entrar no OpenID Connect. Ele também pode incluir o tipo de resposta `token`. Se você usar `token`, seu aplicativo poderá receber imediatamente um token de acesso do ponto de extremidade de autorização, sem fazer uma segunda solicitação para o ponto de extremidade de autorização.  Se você usar o tipo de resposta `token`, o parâmetro `scope` deverá conter um escopo que indica para qual recurso emitir o token. |
| redirect_uri | Não | O URI de redirecionamento do seu aplicativo, em que as respostas de autenticação podem ser enviadas e recebidas pelo seu aplicativo. Ele deve corresponder exatamente a um dos URIs de redirecionamento que você registrou no portal, exceto que ele deve ser codificado por URL. |
| response_mode | Não | Especifica o método a ser usado para enviar o token resultante de volta para seu aplicativo.  Para fluxos implícitos, use `fragment`. |
| scope | Sim | Uma lista de escopos separados por espaços. Um único valor de escopo indica ao Azure AD as permissões que estão sendo solicitadas. O escopo `openid` indica uma permissão para conectar o usuário e obter dados sobre o usuário na forma de tokens de ID. O escopo de `offline_access` é opcional para aplicativos Web. Ele indica que seu aplicativo precisa de um token de atualização para acesso de longa duração aos recursos. |
| state | Não | Um valor incluído na solicitação que também é retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo que você queira usar. Normalmente, um valor exclusivo gerado aleatoriamente é usado, para evitar ataques de solicitação entre sites forjado. O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página em que eles estavam. |
| momentos | Sim | Um valor incluído na solicitação (gerado pelo aplicativo) que é incluído no token de ID resultante como uma declaração. O aplicativo pode, então, verificar esse valor para atenuar os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
| Aviso | Não | O tipo de interação do usuário que é necessário. Atualmente, o único valor válido é `login`. Esse parâmetro força o usuário a inserir suas credenciais nessa solicitação. O logon único não entra em vigor. |

Neste ponto, o usuário será solicitado a concluir o fluxo de trabalho da política. O usuário pode precisar inserir seu nome de usuário e senha, entrar com uma identidade social, inscrever-se no diretório ou qualquer outro número de etapas. As ações do usuário dependem de como o fluxo do usuário é definido.

Depois que o usuário concluir o fluxo do usuário, o Azure AD retornará uma resposta ao seu aplicativo no valor usado para `redirect_uri`. Ele usa o método especificado no parâmetro `response_mode`. A resposta é exatamente a mesma para cada um dos cenários de ação do usuário, independentemente do fluxo do usuário que foi executado.

### <a name="successful-response"></a>Resposta bem-sucedida
Uma resposta bem-sucedida que usa `response_mode=fragment` e `response_type=id_token+token` é semelhante à seguinte, com quebras de linha para legibilidade:

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
| id_token | O token de ID que o aplicativo solicitou. Você pode usar o token de ID para verificar a identidade do usuário e iniciar uma sessão com o usuário. Para obter mais informações sobre tokens de ID e seu conteúdo, consulte a [referência de token de Azure ad B2C](active-directory-b2c-reference-tokens.md). |
| state | Se um parâmetro `state` for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores `state` na solicitação e na resposta são idênticos. |

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
| state | Se um parâmetro `state` for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores `state` na solicitação e na resposta são idênticos.|

## <a name="validate-the-id-token"></a>Validar o token de ID

O recebimento de um token de ID não é suficiente para autenticar o usuário. Valide a assinatura do token de ID e verifique as declarações no token de acordo com os requisitos do aplicativo. O Azure AD B2C usa [JWTs (tokens Web JSON)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar se eles são válidos.

Muitas bibliotecas de software livre estão disponíveis para validar JWTs, dependendo do idioma que você preferir usar. Considere explorar bibliotecas de software livre disponíveis em vez de implementar sua própria lógica de validação. Você pode usar as informações neste artigo para ajudá-lo a aprender como usar essas bibliotecas corretamente.

Azure AD B2C tem um ponto de extremidade de metadados do OpenID Connect. Um aplicativo pode usar o ponto de extremidade para buscar informações sobre Azure AD B2C em tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e chaves de assinatura de token. Há um documento de metadados JSON para cada fluxo de usuário em seu locatário de Azure AD B2C. Por exemplo, o documento de metadados para o fluxo de usuário b2c_1_sign_in no locatário fabrikamb2c.onmicrosoft.com está localizado em:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Uma das propriedades deste documento de configuração é a `jwks_uri`. O valor para o mesmo fluxo de usuário seria:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Para determinar qual fluxo de usuário foi usado para assinar um token de ID (e para onde buscar os metadados), você tem duas opções. Primeiro, o nome do fluxo de usuário é incluído na declaração de `acr` no `id_token`. Para obter informações sobre como analisar as declarações de um token de ID, consulte a [referência de token Azure ad B2C](active-directory-b2c-reference-tokens.md). Sua outra opção é codificar o fluxo do usuário no valor do parâmetro `state` ao emitir a solicitação. Em seguida, decodifique o parâmetro `state` para determinar qual fluxo de usuário foi usado. O método é válido.

Depois de adquirir o documento de metadados do ponto de extremidade de metadados do OpenID Connect, você pode usar as chaves públicas RSA-256 (localizadas neste ponto de extremidade) para validar a assinatura do token de ID. Pode haver várias chaves listadas nesse ponto de extremidade em um determinado momento, cada uma identificada por um `kid`. O cabeçalho de `id_token` também contém uma declaração de `kid`. Indica quais dessas chaves foram usadas para assinar o token de ID. Para obter mais informações, incluindo aprender sobre como [validar tokens](active-directory-b2c-reference-tokens.md), consulte a [referência de token Azure ad B2C](active-directory-b2c-reference-tokens.md).
<!--TODO: Improve the information on this-->

Depois de validar a assinatura do token de ID, várias declarações exigem verificação. Por exemplo:

* Valide a declaração `nonce` para evitar ataques de reprodução de token. Seu valor deve ser o que você especificou na solicitação de entrada.
* Valide a declaração de `aud` para garantir que o token de ID foi emitido para seu aplicativo. Seu valor deve ser a ID do aplicativo do seu aplicativo.
* Valide as declarações de `iat` e `exp` para garantir que o token de ID não tenha expirado.

Várias outras validações que você deve executar são descritas em detalhes na [especificação do OpenID Connect Core](https://openid.net/specs/openid-connect-core-1_0.html). Você também pode querer validar declarações adicionais, dependendo do seu cenário. Algumas validações comuns incluem:

* Garantindo que o usuário ou a organização tenha se inscrito no aplicativo.
* Garantindo que o usuário tenha autorização e privilégios adequados.
* Garantindo que uma determinada força de autenticação tenha ocorrido, como usando a autenticação multifator do Azure.

Para obter mais informações sobre as declarações em um token de ID, consulte a [referência de token Azure ad B2C](active-directory-b2c-reference-tokens.md).

Depois de validar o token de ID, você pode iniciar uma sessão com o usuário. Em seu aplicativo, use as declarações no token de ID para obter informações sobre o usuário. Essas informações podem ser usadas para exibição, registros, autorização e assim por diante.

## <a name="get-access-tokens"></a>Obter tokens de acesso
Se a única coisa que seus aplicativos Web precisam fazer é executar fluxos de usuário, você pode ignorar as próximas seções. As informações nas seções a seguir são aplicáveis somente a aplicativos Web que precisam fazer chamadas autenticadas para uma API da Web e que são protegidas pelo Azure AD B2C.

Agora que você assinou o usuário em seu aplicativo de página única, você pode obter tokens de acesso para chamar APIs Web que são protegidas pelo Azure AD. Mesmo que você já tenha recebido um token usando o tipo de resposta `token`, você pode usar esse método para adquirir tokens para recursos adicionais sem redirecionar o usuário para entrar novamente.

Em um fluxo de aplicativo Web típico, você fará uma solicitação para o ponto de extremidade `/token`. No entanto, o ponto de extremidade não oferece suporte a solicitações CORS, portanto, fazer chamadas AJAX para obter um token de atualização não é uma opção. Em vez disso, você pode usar o fluxo implícito em um elemento iframe HTML oculto para obter novos tokens para outras APIs da Web. Aqui está um exemplo, com quebras de linha para legibilidade:

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
regras| Obrigatório| O fluxo do usuário a ser executado. Especifique o nome de um fluxo de usuário que você criou em seu locatário Azure AD B2C. Por exemplo: `b2c_1_sign_in`, `b2c_1_sign_up` ou `b2c_1_edit_profile`. |
| client_id |Obrigatório |A ID do aplicativo atribuída ao seu aplicativo no [portal do Azure](https://portal.azure.com). |
| response_type |Obrigatório |Deve incluir `id_token` para entrar no OpenID Connect.  Ele também pode incluir o tipo de resposta `token`. Se você usar `token` aqui, seu aplicativo poderá receber imediatamente um token de acesso do ponto de extremidade de autorização, sem fazer uma segunda solicitação para o ponto de extremidade de autorização. Se você usar o tipo de resposta `token`, o parâmetro `scope` deverá conter um escopo que indica para qual recurso emitir o token. |
| redirect_uri |Recomendado |O URI de redirecionamento do seu aplicativo, em que as respostas de autenticação podem ser enviadas e recebidas pelo seu aplicativo. Ele deve corresponder exatamente a um dos URIs de redirecionamento que você registrou no portal, exceto que ele deve ser codificado por URL. |
| scope |Obrigatório |Uma lista de escopos separados por espaços.  Para obter tokens, inclua todos os escopos necessários para o recurso pretendido. |
| response_mode |Recomendado |Especifica o método usado para enviar o token resultante de volta para seu aplicativo. Para o fluxo implícito, use `fragment`. Dois outros modos podem ser especificados, `query` e `form_post`, mas não funcionam no fluxo implícito. |
| state |Recomendado |Um valor incluído na solicitação que é retornado na resposta do token.  Pode ser uma cadeia de caracteres de qualquer conteúdo que você queira usar.  Normalmente, um valor exclusivo gerado aleatoriamente é usado, para evitar ataques de solicitação entre sites forjado.  O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação tenha ocorrido. Por exemplo, a página ou a exibição em que o usuário estava. |
| momentos |Obrigatório |Um valor incluído na solicitação, gerado pelo aplicativo, que está incluído no token de ID resultante como uma declaração.  O aplicativo pode, então, verificar esse valor para atenuar os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que identifica a origem da solicitação. |
| Aviso |Obrigatório |Para atualizar e obter tokens em um iframe oculto, use `prompt=none` para garantir que o iframe não fique preso na página de entrada e retorne imediatamente. |
| login_hint |Obrigatório |Para atualizar e obter tokens em um iframe oculto, inclua o nome de usuário dessa dica para distinguir entre várias sessões que o usuário pode ter em um determinado momento. Você pode extrair o nome de usuário de uma entrada anterior usando a declaração de `preferred_username` (o escopo de `profile` é necessário para receber a declaração de `preferred_username`). |
| domain_hint |Obrigatório |Pode ser `consumers` ou `organizations`.  Para atualizar e obter tokens em um iframe oculto, inclua o valor `domain_hint` na solicitação.  Extraia a declaração de `tid` do token de ID de uma entrada anterior para determinar qual valor usar (o escopo de `profile` é necessário para receber a declaração de `tid`). Se o valor de declaração de `tid` for `9188040d-6c67-4c5b-b112-36a304b66dad`, use `domain_hint=consumers`.  Caso contrário, use `domain_hint=organizations`. |

Ao definir o parâmetro `prompt=none`, essa solicitação é bem-sucedida ou falha imediatamente e retorna ao seu aplicativo.  Uma resposta bem-sucedida é enviada ao seu aplicativo no URI de redirecionamento indicado, usando o método especificado no parâmetro `response_mode`.

### <a name="successful-response"></a>Resposta bem-sucedida
Uma resposta bem-sucedida usando `response_mode=fragment` é semelhante a este exemplo:

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
| access_token |O token solicitado pelo aplicativo. |
| token_type |O tipo de token sempre será portador. |
| state |Se um parâmetro `state` for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores `state` na solicitação e na resposta são idênticos. |
| expires_in |Por quanto tempo o token de acesso é válido (em segundos). |
| scope |Os escopos para os quais o token de acesso é válido. |

### <a name="error-response"></a>Resposta de erro
As respostas de erro também podem ser enviadas para o URI de redirecionamento para que o aplicativo possa tratá-las adequadamente.  Por `prompt=none`, um erro esperado é semelhante a este exemplo:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de caracteres de código de erro que pode ser usada para classificar tipos de erros que ocorrem. Você também pode usar a cadeia de caracteres para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudá-lo a identificar a causa raiz de um erro de autenticação. |

Se você receber esse erro na solicitação de iframe, o usuário deverá fazer logon interativamente novamente para recuperar um novo token.

## <a name="refresh-tokens"></a>Tokens de atualização
Tokens de ID e tokens de acesso expiram após um curto período de tempo. Seu aplicativo deve estar preparado para atualizar esses tokens periodicamente.  Para atualizar qualquer tipo de token, execute a mesma solicitação de iframe oculta que usamos em um exemplo anterior, usando o parâmetro `prompt=none` para controlar as etapas do Azure AD.  Para receber um novo valor de `id_token`, certifique-se de usar `response_type=id_token` e `scope=openid` e um parâmetro de `nonce`.

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de saída
Quando você quiser desconectar o usuário do aplicativo, redirecione o usuário para o Azure AD para sair. Se você não redirecionar o usuário, ele poderá ser capaz de autenticar novamente em seu aplicativo sem inserir suas credenciais novamente porque eles têm uma sessão de logon único válida com o Azure AD.

Você pode simplesmente redirecionar o usuário para o `end_session_endpoint` listado no mesmo documento de metadados do OpenID Connect descrito em [validar o token de ID](#validate-the-id-token). Por exemplo:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parâmetro | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| vários | Sim | Nome do seu locatário de Azure AD B2C |
| regras | Sim | O fluxo de usuário que você deseja usar para desconectar o usuário do seu aplicativo. |
| post_logout_redirect_uri | Não | A URL para a qual o usuário deve ser redirecionado após a saída bem-sucedida. Se não estiver incluído, Azure AD B2C mostrará ao usuário uma mensagem genérica. |
| state | Não | Se um parâmetro `state` for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores `state` na solicitação e na resposta são idênticos. |


> [!NOTE]
> Direcionar o usuário para a `end_session_endpoint` limpa parte do estado de logon único do usuário com Azure AD B2C. No entanto, ele não desconectará o usuário da sessão do provedor de identidade social do usuário. Se o usuário selecionar o mesmo provedor de identidade durante uma entrada subsequente, o usuário será autenticado novamente, sem inserir suas credenciais. Se um usuário quiser sair do seu aplicativo Azure AD B2C, isso não significa necessariamente que deseja se desconectar completamente de sua conta do Facebook, por exemplo. No entanto, para contas locais, a sessão do usuário será encerrada corretamente.
>

## <a name="next-steps"></a>Passos seguintes

### <a name="code-sample-hellojs-with-azure-ad-b2c"></a>Exemplo de código: Hello. js com Azure AD B2C

[Aplicativo de página única criado no Hello. js com Azure ad B2C][github-hello-js-example] (github)

Este exemplo no GitHub destina-se a ajudá-lo a começar a usar Azure AD B2C em um aplicativo Web simples criado no [Hello. js][github-hello-js] e usando a autenticação de estilo pop-up.

<!-- Links - EXTERNAL -->
[github-hello-js-example]: https://github.com/azure-ad-b2c/apps/tree/master/spa/javascript-hellojs-singlepageapp-popup
[github-hello-js]: https://github.com/MrSwitch/hello.js
