---
title: Web-in com OpenID Connect - Azure Ative Directory B2C
description: Construa aplicações web utilizando o protocolo de autenticação OpenID Connect em Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: bf70d4381a7d128f7a2716540b1318b39cd729b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90602137"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Web-in com OpenID Connect em Azure Ative Directory B2C

O OpenID Connect é um protocolo de autenticação, construído em cima do OAuth 2.0, que pode ser usado para iniciar com segurança os utilizadores em aplicações web. Ao utilizar a implementação do Azure Ative Directory B2C (Azure AD B2C) do OpenID Connect, pode subcontratar inscrições, iniciar seduções e outras experiências de gestão de identidade nas suas aplicações web para a Azure Ative Directory (Azure AD). Este guia mostra-lhe como fazê-lo de forma independente da linguagem. Descreve como enviar e receber mensagens HTTP sem utilizar nenhuma das nossas bibliotecas de código aberto.

[O OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) alarga o protocolo de *autorização* OAuth 2.0 para utilização como protocolo de *autenticação.* Este protocolo de autenticação permite-lhe realizar uma única sação. Introduz o conceito de símbolo de *ID,* que permite ao cliente verificar a identidade do utilizador e obter informações básicas sobre o perfil sobre o utilizador.

Uma vez que estende o OAuth 2.0, também permite que as aplicações adquiram de forma segura *os tokens de acesso*. Pode utilizar fichas de acesso para aceder a recursos que são protegidos por um [servidor de autorização.](protocols-overview.md) O OpenID Connect é recomendado se estiver a construir uma aplicação web que esteja hospedada num servidor e acedida através de um browser. Para obter mais informações sobre fichas, consulte a [visão geral dos tokens no Azure Ative Directory B2C](tokens-overview.md)

Azure AD B2C estende o protocolo padrão OpenID Connect para fazer mais do que simples autenticação e autorização. Introduz o [parâmetro de fluxo](user-flow-overview.md)do utilizador, que lhe permite utilizar o OpenID Connect para adicionar experiências de utilizador à sua aplicação, tais como inscrição, inscrição e gestão de perfis.

## <a name="send-authentication-requests"></a>Enviar pedidos de autenticação

Quando a sua aplicação web necessitar de autenticar o utilizador e executar um fluxo de utilizador, pode direcionar o utilizador para o `/authorize` ponto final. O utilizador toma medidas dependendo do fluxo do utilizador.

Neste pedido, o cliente indica as permissões que necessita de adquirir do utilizador no `scope` parâmetro, e especifica o fluxo do utilizador para executar. Para ter uma noção de como o pedido funciona, tente colar o pedido num browser e executá-lo. `{tenant}`Substitua-o pelo nome do seu inquilino. `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6`Substitua-se pelo ID da aplicação que já se registou no seu inquilino. Altere também o nome da apólice `{policy}` para o nome da apólice que tem no seu inquilino, por `b2c_1_sign_in` exemplo.

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parâmetro | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| {inquilino} | Sim | Nome do seu inquilino Azure AD B2C |
| {política} | Sim | O fluxo do utilizador a ser executado. Especifique o nome de um fluxo de utilizador que criou no seu inquilino Azure AD B2C. Por exemplo: `b2c_1_sign_in` `b2c_1_sign_up` , ou . `b2c_1_edit_profile` . |
| client_id | Sim | O ID da aplicação que o [portal Azure](https://portal.azure.com/) atribuiu à sua aplicação. |
| nonce | Sim | Um valor incluído no pedido (gerado pela aplicação) que está incluído no token de ID resultante como reclamação. A aplicação pode então verificar este valor para mitigar os ataques de repetição de token. O valor é tipicamente uma corda única aleatória que pode ser usada para identificar a origem do pedido. |
| response_type | Sim | Deve incluir um token de ID para OpenID Connect. Se a sua aplicação web também precisar de fichas para chamar uma API web, pode usar `code+id_token` . |
| scope | Sim | Uma lista de âmbitos separados pelo espaço. O `openid` âmbito indica uma permissão para assinar no utilizador e obter dados sobre o utilizador sob a forma de fichas de identificação. O `offline_access` âmbito é opcional para aplicações web. Indica que a sua aplicação necessitará de um *token de atualização* para um acesso alargado aos recursos. |
| rápido | Não | O tipo de interação do utilizador que é necessária. O único valor válido neste momento é `login` , o que obriga o utilizador a introduzir as suas credenciais nesse pedido. |
| redirect_uri | Não | O `redirect_uri` parâmetro da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua aplicação. Deve corresponder exatamente a um dos `redirect_uri` parâmetros que registou no portal Azure, exceto que deve ser URL codificado. |
| response_mode | Não | O método utilizado para enviar o código de autorização resultante de volta à sua aplicação. Pode `query` ser, `form_post` ou `fragment` . .  O `form_post` modo de resposta é recomendado para uma melhor segurança. |
| state | Não | Um valor incluído no pedido que também é devolvido na resposta simbólica. Pode ser uma série de conteúdos que queiras. Um valor único gerado aleatoriamente é normalmente usado para prevenir ataques de falsificação de pedidos de trans-locais. O Estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página em que se encontravam. |

Neste momento, pede-se ao utilizador que complete o fluxo de trabalho. O utilizador poderá ter de introduzir o seu nome de utilizador e senha, iniciar sação com identidade social ou inscrever-se no diretório. Pode haver qualquer outro número de passos dependendo da forma como o fluxo do utilizador é definido.

Após o utilizador completar o fluxo do utilizador, uma resposta é devolvida à sua aplicação no `redirect_uri` parâmetro indicado, utilizando o método especificado no `response_mode` parâmetro. A resposta é a mesma para cada um dos casos anteriores, independentemente do fluxo do utilizador.

Uma resposta bem sucedida `response_mode=fragment` seria como:

```http
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --------- | ----------- |
| id_token | O sinal de identificação que o pedido solicitou. Pode utilizar o token de identificação para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. |
| code | O código de autorização que o pedido solicitou, se `response_type=code+id_token` utilizar. A aplicação pode usar o código de autorização para solicitar um sinal de acesso para um recurso-alvo. Os códigos de autorização normalmente expiram após cerca de 10 minutos. |
| state | Se um `state` parâmetro for incluído no pedido, o mesmo valor deve aparecer na resposta. O pedido deve verificar se os `state` valores do pedido e resposta são idênticos. |

As respostas de erro também podem ser enviadas para o `redirect_uri` parâmetro para que a aplicação possa manuseá-las adequadamente:

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --------- | ----------- |
| erro | Um código que pode ser usado para classificar os tipos de erros que ocorrem. |
| error_description | Uma mensagem de erro específica que pode ajudar a identificar a causa principal de um erro de autenticação. |
| state | Se um `state` parâmetro for incluído no pedido, o mesmo valor deve aparecer na resposta. O pedido deve verificar se os `state` valores do pedido e resposta são idênticos. |

## <a name="validate-the-id-token"></a>Validar o token de ID

Receber apenas um token de identificação não é suficiente para autenticar o utilizador. Valide a assinatura do token de identificação e verifique as reclamações no token de acordo com os requisitos da sua aplicação. Azure AD B2C utiliza [tokens web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chaves públicas para assinar fichas e verificar se são válidos. Existem muitas bibliotecas de código aberto que estão disponíveis para validar JWTs, dependendo do seu idioma de preferência. Recomendamos explorar essas opções em vez de implementar a sua própria lógica de validação.

O Azure AD B2C tem um ponto final de metadados OpenID Connect, que permite que uma aplicação obtenha informações sobre o Azure AD B2C no tempo de execução. Esta informação inclui pontos finais, conteúdo simbólico e chaves de assinatura simbólicas. Existe um documento de metadados JSON para cada fluxo de utilizador no seu inquilino B2C. Por exemplo, o documento de metadados para o fluxo do `b2c_1_sign_in` utilizador `fabrikamb2c.onmicrosoft.com` está localizado em:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Uma das propriedades deste documento de configuração `jwks_uri` é, cujo valor para o mesmo fluxo de utilizador seria:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Para determinar qual o fluxo do utilizador utilizado na assinatura de um token de ID (e de onde obter os metadados), tem duas opções. Em primeiro lugar, o nome de fluxo do utilizador está incluído na `acr` reclamação no token de ID. A sua outra opção é codificar o fluxo do utilizador no valor do `state` parâmetro quando emite o pedido e, em seguida, descodificá-lo para determinar qual o fluxo do utilizador que foi utilizado. Qualquer um dos métodos é válido.

Depois de ter adquirido o documento de metadados do ponto final de metadados OpenID Connect, pode utilizar as chaves públicas RSA 256 para validar a assinatura do token de ID. Pode haver várias chaves listadas neste ponto final, cada uma identificada por uma `kid` reclamação. O cabeçalho do símbolo de identificação também contém uma `kid` reclamação, que indica qual destas chaves foi usada para assinar o token de identificação.

Para verificar os tokens do Azure AD B2C, é necessário gerar a chave pública utilizando o expoente(e) e o módulo n. Tem de determinar como fazê-lo na sua respetiva linguagem de programação em conformidade. A documentação oficial sobre a geração De Chave Pública com o protocolo RSA pode ser consultada aqui: https://tools.ietf.org/html/rfc3447#section-3.1

Depois de validar a assinatura do token de identificação, há várias alegações que precisa de verificar. Por exemplo:

- Valide a `nonce` alegação para evitar ataques de repetição de fichas. O seu valor deve ser o que especificou no pedido de inscrição.
- Valide a `aud` reclamação para garantir que o token de ID foi emitido para a sua aplicação. O seu valor deve ser o ID de aplicação da sua aplicação.
- Valide o e afirma para garantir que o token de `iat` `exp` identificação não expirou.

Há também várias validações que deve realizar. As validações são descritas em detalhe na [Especificação de Núcleo de Ligação OpenID](https://openid.net/specs/openid-connect-core-1_0.html). Também pode querer validar reclamações adicionais, dependendo do seu cenário. Algumas validações comuns incluem:

- Garantindo que o utilizador/organização se inscreveu para a aplicação.
- Certificando-se de que o utilizador dispõe de uma autorização/privilégios adequados.
- Garantindo que ocorreu uma certa força de autenticação, como a autenticação multi-factor Azure.

Depois de validar o token de ID, pode iniciar uma sessão com o utilizador. Pode utilizar as reclamações no token de identificação para obter informações sobre o utilizador na sua aplicação. As utilizações para esta informação incluem exibição, registos e autorização.

## <a name="get-a-token"></a>Obter um símbolo

Se precisar da sua aplicação web apenas para executar fluxos de utilizador, pode saltar as próximas secções. Estas secções aplicam-se apenas a aplicações web que necessitam de fazer chamadas autenticadas para uma API web e também estão protegidas pelo Azure AD B2C.

Pode resgatar o código de autorização que adquiriu `response_type=code+id_token` (utilizando) para um símbolo ao recurso pretendido, enviando um pedido para o ponto `POST` `/token` final. Em Azure AD B2C, pode [solicitar tokens de acesso para outras APIs](access-tokens.md#request-a-token) como de costume, especificando o seu(s) âmbito(s) no pedido.

Também pode solicitar um token de acesso para a própria API web de back-end da sua aplicação por convenção de usar o ID do cliente da aplicação como o âmbito solicitado (o que resultará num token de acesso com esse iD do cliente como "público"):

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parâmetro | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| {inquilino} | Sim | Nome do seu inquilino Azure AD B2C |
| {política} | Sim | O fluxo de utilizador que foi utilizado para adquirir o código de autorização. Não é possível utilizar um fluxo de utilizador diferente neste pedido. Adicione este parâmetro à cadeia de consulta, não ao corpo POST. |
| client_id | Sim | O ID da aplicação que o [portal Azure](https://portal.azure.com/) atribuiu à sua aplicação. |
| client_secret | Sim, em Web Apps | O segredo da aplicação que foi gerado no [portal Azure.](https://portal.azure.com/) Os segredos do cliente são usados neste fluxo para cenários de Web App, onde o cliente pode armazenar de forma segura um segredo do cliente. Para cenários de Aplicação Nativa (cliente público), os segredos dos clientes não podem ser armazenados de forma segura, portanto não são utilizados neste fluxo. Se utilizar um segredo de cliente, por favor altere-o periódico. |
| code | Sim | O código de autorização que adquiriu no início do fluxo de utilizador. |
| grant_type | Sim | O tipo de subvenção, que deve ser `authorization_code` para o fluxo de código de autorização. |
| redirect_uri | Sim | O `redirect_uri` parâmetro do pedido onde recebeu o código de autorização. |
| scope | Não | Uma lista de âmbitos separados pelo espaço. O `openid` âmbito indica uma permissão para assinar no utilizador e obter dados sobre o utilizador sob a forma de id_token parâmetros. Pode ser usado para obter fichas para a própria API web de back-end da sua aplicação, que é representada pela mesma identificação de aplicação que o cliente. O `offline_access` âmbito indica que a sua aplicação precisa de um token de atualização para um acesso alargado aos recursos. |

Uma resposta simbólica bem sucedida parece:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parâmetro | Descrição |
| --------- | ----------- |
| not_before | O momento em que o símbolo é considerado válido, em tempo de época. |
| token_type | O valor do tipo símbolo. `Bearer` é o único tipo que é suportado. |
| access_token | O sinal assinado jWT que pediu. |
| scope | Os âmbitos para os quais o símbolo é válido. |
| expires_in | O tempo de duração do token de acesso é válido (em segundos). |
| refresh_token | Um token de atualização OAuth 2.0. A aplicação pode usar este token para adquirir fichas adicionais após o fim do token atual. As fichas de atualização podem ser usadas para manter o acesso aos recursos por longos períodos de tempo. O âmbito de aplicação `offline_access` deve ter sido utilizado tanto na autorização como nos pedidos simbólicos, a fim de receber um token de atualização. |

As respostas de erro parecem:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --------- | ----------- |
| erro | Um código que pode ser usado para classificar tipos de erros que ocorrem. |
| error_description | Uma mensagem que pode ajudar a identificar a causa principal de um erro de autenticação. |

## <a name="use-the-token"></a>Use o símbolo

Agora que adquiriu com sucesso um token de acesso, pode usar o token em pedidos para as suas APIs web de back-end, incluindo-o no `Authorization` cabeçalho:

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Refresque o símbolo

As fichas de identificação expiram num curto espaço de tempo. Refresque os tokens depois de expirarem para continuar a poder aceder aos recursos. Pode refrescar um token submetendo outro `POST` pedido ao `/token` ponto final. Desta vez, forneça o `refresh_token` parâmetro em vez do `code` parâmetro:

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parâmetro | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| {inquilino} | Sim | Nome do seu inquilino Azure AD B2C |
| {política} | Sim | O fluxo de utilizador que foi usado para adquirir o token original da atualização. Não é possível utilizar um fluxo de utilizador diferente neste pedido. Adicione este parâmetro à cadeia de consulta, não ao corpo POST. |
| client_id | Sim | O ID da aplicação que o [portal Azure](https://portal.azure.com/) atribuiu à sua aplicação. |
| client_secret | Sim, em Web Apps | O segredo da aplicação que foi gerado no [portal Azure.](https://portal.azure.com/) Os segredos do cliente são usados neste fluxo para cenários de Web App, onde o cliente pode armazenar de forma segura um segredo do cliente. Para cenários de Aplicação Nativa (cliente público), os segredos dos clientes não podem ser armazenados de forma segura, portanto não são utilizados nesta chamada. Se utilizar um segredo de cliente, por favor altere-o periódico. |
| grant_type | Sim | O tipo de subvenção, que deve ser `refresh_token` para esta parte do fluxo do código de autorização. |
| refresh_token | Sim | O token original da atualização que foi adquirido na segunda parte do fluxo. O `offline_access` âmbito de aplicação deve ser utilizado tanto na autorização como nos pedidos simbólicos, a fim de receber um token de atualização. |
| redirect_uri | Não | O `redirect_uri` parâmetro do pedido onde recebeu o código de autorização. |
| scope | Não | Uma lista de âmbitos separados pelo espaço. O `openid` âmbito indica uma permissão para assinar no utilizador e obter dados sobre o utilizador sob a forma de fichas de identificação. Pode ser usado para enviar fichas para a própria API web de back-end da sua aplicação, que é representada pela mesma identificação de aplicação que o cliente. O `offline_access` âmbito indica que a sua aplicação precisa de um token de atualização para um acesso alargado aos recursos. |

Uma resposta simbólica bem sucedida parece:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parâmetro | Descrição |
| --------- | ----------- |
| not_before | O momento em que o símbolo é considerado válido, em tempo de época. |
| token_type | O valor do tipo símbolo. `Bearer` é o único tipo que é suportado. |
| access_token | O sinal assinado jWT que foi solicitado. |
| scope | O âmbito para o qual o símbolo é válido. |
| expires_in | O tempo de duração do token de acesso é válido (em segundos). |
| refresh_token | Um token de atualização OAuth 2.0. A aplicação pode usar este token para adquirir fichas adicionais após o fim do token atual. As fichas de atualização podem ser usadas para manter o acesso aos recursos por longos períodos de tempo. |

As respostas de erro parecem:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --------- | ----------- |
| erro | Um código que pode ser usado para classificar tipos de erros que ocorrem. |
| error_description | Uma mensagem que pode ajudar a identificar a causa principal de um erro de autenticação. |

## <a name="send-a-sign-out-request"></a>Enviar um pedido de sinalização

Quando pretender assinar o utilizador fora da aplicação, não basta limpar os cookies da aplicação ou terminar a sessão com o utilizador. Redirecione o utilizador para Azure AD B2C para assinar. Se não o fizer, o utilizador poderá voltar a recorrer à sua aplicação sem voltar a introduzir as suas credenciais. Para mais informações, consulte [a sessão Azure AD B2C](session-overview.md).

Para assinar o utilizador, redirecione o utilizador para o `end_session` ponto final que está listado no documento de metadados OpenID Connect descrito anteriormente:

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| Parâmetro | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| {inquilino} | Sim | Nome do seu inquilino Azure AD B2C |
| {política} | Sim | O fluxo do utilizador que pretende utilizar para assinar o utilizador fora da sua aplicação. |
| id_token_hint| Não | Um token de ID previamente emitido para passar para o ponto final do logout como uma dica sobre a sessão autenticada atual do utilizador final com o cliente. `id_token_hint`O garante que o é um URL de resposta registado nas `post_logout_redirect_uri` definições de aplicação Azure AD B2C. |
| client_id | Não* | O ID da aplicação que o [portal Azure](https://portal.azure.com/) atribuiu à sua aplicação.<br><br>\**Isto é necessário quando se utiliza `Application` a configuração SSO de isolamento e _o Pedido de ID ID_ no pedido de logout está definido para `No` .* |
| post_logout_redirect_uri | Não | O URL para o que o utilizador deve ser redirecionado após a assinatura com sucesso. Se não estiver incluído, o Azure AD B2C mostra ao utilizador uma mensagem genérica. A menos que forneça um `id_token_hint` URL, não deve registar este URL como URL de resposta nas definições de aplicação Azure AD B2C. |
| state | Não | Se um `state` parâmetro for incluído no pedido, o mesmo valor deve aparecer na resposta. O pedido deve verificar se os `state` valores do pedido e resposta são idênticos. |

### <a name="secure-your-logout-redirect"></a>Fixe o seu redirecionamento de logout

Após o logout, o utilizador é redirecionado para o URI especificado no `post_logout_redirect_uri` parâmetro, independentemente dos URLs de resposta especificados para a aplicação. No entanto, se for aprovado um `id_token_hint` válido, o Azure AD B2C verifica que o valor da `post_logout_redirect_uri` correspondência corresponde a um dos URIs de redirecionamento configurados da aplicação antes de efetuar o redirecionamento. Se não tiver sido configurado um URL de resposta correspondente para a aplicação, é apresentada uma mensagem de erro e o utilizador não é redirecionado.


