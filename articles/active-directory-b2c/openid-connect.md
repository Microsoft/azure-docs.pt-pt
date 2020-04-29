---
title: Entrada web com OpenID Connect - Diretório Ativo Azure B2C
description: Construa aplicações web utilizando o protocolo de autenticação OpenID Connect no Diretório Ativo Azure B2C.
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
ms.openlocfilehash: 314d7ebe9cc363b4186b81d8eda5f892710d71c8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82229991"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Entrada web com OpenID Connect no Diretório Ativo Azure B2C

OpenID Connect é um protocolo de autenticação, construído em cima do OAuth 2.0, que pode ser usado para assinar de forma segura os utilizadores em aplicações web. Ao utilizar a implementação do Azure Ative Directory B2C (Azure AD B2C) do OpenID Connect, pode subcontratar experiências de inscrição, inscrição e outras experiências de gestão de identidade nas suas aplicações web para o Azure Ative Directory (Azure AD). Este guia mostra-lhe como fazê-lo de forma independente de linguagem. Descreve como enviar e receber mensagens HTTP sem usar nenhuma das nossas bibliotecas de código aberto.

[O OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) alarga o protocolo de *autorização* OAuth 2.0 para utilização como protocolo de *autenticação.* Este protocolo de autenticação permite-lhe realizar uma única inscrição. Introduz o conceito de ficha *de identificação,* que permite ao cliente verificar a identidade do utilizador e obter informações básicas sobre o utilizador.

Uma vez que estende o OAuth 2.0, também permite que as aplicações adquiram de forma segura fichas de *acesso.* Pode utilizar fichas de acesso para aceder a recursos que são protegidos por um servidor de [autorização](protocols-overview.md). O OpenID Connect é recomendado se estiver a construir uma aplicação web que esteja hospedada num servidor e acessada através de um browser. Para mais informações sobre fichas, consulte a [visão geral dos tokens no Diretório Ativo Azure B2C](tokens-overview.md)

O Azure AD B2C alarga o protocolo padrão OpenID Connect para fazer mais do que simples autenticação e autorização. Introduz o parâmetro de fluxo do [utilizador,](user-flow-overview.md)que lhe permite utilizar o OpenID Connect para adicionar experiências de utilizador à sua aplicação, tais como inscrição, inscrição e gestão de perfis.

## <a name="send-authentication-requests"></a>Enviar pedidos de autenticação

Quando a sua aplicação web precisa de autenticar o utilizador e `/authorize` executar um fluxo de utilizador, pode direcionar o utilizador para o ponto final. O utilizador toma medidas dependendo do fluxo do utilizador.

Neste pedido, o cliente indica as permissões que necessita `scope` de adquirir ao utilizador no parâmetro, e especifica o fluxo do utilizador para executar. Para obter uma sensação de como o pedido funciona, tente colar o pedido num navegador e executá-lo. Substitua `{tenant}` pelo nome do seu inquilino. Substitua `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` com a identificação da aplicação que já registou no seu inquilino. Altere também o`{policy}`nome da política () para o `b2c_1_sign_in`nome de política que tem no seu inquilino, por exemplo.

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parâmetro | Necessário | Descrição |
| --------- | -------- | ----------- |
| {inquilino} | Sim | Nome do seu inquilino Azure AD B2C |
| {política} | Sim | O fluxo do utilizador para ser executado. Especifique o nome de um fluxo de utilizador que criou no seu inquilino Azure AD B2C. Por `b2c_1_sign_in`exemplo: `b2c_1_sign_up`, `b2c_1_edit_profile`ou . |
| client_id | Sim | O ID de aplicação que o [portal Azure](https://portal.azure.com/) atribuiu à sua aplicação. |
| nonce | Sim | Um valor incluído no pedido (gerado pelo pedido) que está incluído no símbolo de identificação resultante como reivindicação. A aplicação pode então verificar este valor para mitigar ataques de repetição de tokens. O valor é tipicamente uma cadeia única aleatória que pode ser usada para identificar a origem do pedido. |
| response_type | Sim | Deve incluir um símbolo de identificação para openID Connect. Se a sua aplicação web também precisar de fichas `code+id_token`para chamar uma API web, pode usar . |
| scope | Sim | Uma lista de âmbitos separados pelo espaço. O `openid` âmbito indica uma permissão para iniciar sessão no utilizador e obter dados sobre o utilizador sob a forma de fichas de identificação. O `offline_access` âmbito é opcional para aplicações web. Indica que a sua aplicação necessitará de um *sinal refrescante* para um acesso alargado aos recursos. |
| pronta | Não | O tipo de interação do utilizador que é necessário. O único valor válido `login`neste momento é que o utilizador obriga o utilizador a introduzir as suas credenciais nesse pedido. |
| redirect_uri | Não | O `redirect_uri` parâmetro da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas através da sua aplicação. Deve corresponder exatamente `redirect_uri` a um dos parâmetros registados no portal Azure, exceto que deve ser codificado por URL. |
| response_mode | Não | O método utilizado para enviar o código de autorização resultante de volta para a sua aplicação. Pode ser `query`qualquer `form_post`um, ou `fragment`.  O `form_post` modo de resposta é recomendado para uma melhor segurança. |
| state | Não | Um valor incluído no pedido que também é devolvido na resposta simbólica. Pode ser uma série de qualquer conteúdo que queira. Um valor único gerado aleatoriamente é normalmente usado para prevenir ataques de falsificação de pedidos de local. O Estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página em que se encontrava. |

Neste ponto, é solicitado ao utilizador que complete o fluxo de trabalho. O utilizador poderá ter de introduzir o seu nome de utilizador e palavra-passe, iniciar sessão com uma identidade social ou inscrever-se no diretório. Pode haver qualquer outro número de passos dependendo da forma como o fluxo do utilizador é definido.

Após o utilizador completar o fluxo do utilizador, uma `redirect_uri` resposta é devolvida à sua aplicação no `response_mode` parâmetro indicado, utilizando o método especificado no parâmetro. A resposta é a mesma para cada um dos casos anteriores, independentemente do fluxo do utilizador.

Uma resposta `response_mode=fragment` bem sucedida usando seria como:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --------- | ----------- |
| id_token | O iD que o pedido solicitou. Pode utilizar o símbolo de identificação para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. |
| code | O código de autorização que o `response_type=code+id_token`pedido solicitou, se utilizou. O pedido pode usar o código de autorização para solicitar um sinal de acesso para um recurso-alvo. Os códigos de autorização normalmente expiram após cerca de 10 minutos. |
| state | Se `state` um parâmetro estiver incluído no pedido, o mesmo valor deve figurar na resposta. O pedido deve `state` verificar se os valores do pedido e da resposta são idênticos. |

As respostas de erro `redirect_uri` também podem ser enviadas para o parâmetro para que a aplicação possa manuseá-las adequadamente:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --------- | ----------- |
| erro | Um código que pode ser usado para classificar os tipos de erros que ocorrem. |
| error_description | Uma mensagem de erro específica que pode ajudar a identificar a causa principal de um erro de autenticação. |
| state | Se `state` um parâmetro estiver incluído no pedido, o mesmo valor deve figurar na resposta. O pedido deve `state` verificar se os valores do pedido e da resposta são idênticos. |

## <a name="validate-the-id-token"></a>Validar o símbolo de identificação

Receber apenas um símbolo de identificação não é suficiente para autenticar o utilizador. Valide a assinatura do token de identificação e verifique as reclamações no token de acordo com os requisitos da sua aplicação. O Azure AD B2C utiliza [os Tofros Web JSON (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e a criptografia de chaves públicas para assinar fichas e verificar se são válidas. Existem muitas bibliotecas de código aberto que estão disponíveis para validação de JWTs, dependendo da sua linguagem de preferência. Recomendamos explorar essas opções em vez de implementar a sua própria lógica de validação.

O Azure AD B2C tem um ponto final de metadados OpenID Connect, que permite obter informações sobre o Azure AD B2C no prazo de execução. Estas informações incluem pontos finais, conteúdo simbólico e chaves de assinatura simbólicas. Existe um documento de metadados JSON para cada fluxo de utilizador no seu inquilino B2C. Por exemplo, o documento `b2c_1_sign_in` de metadados para o fluxo do utilizador `fabrikamb2c.onmicrosoft.com` está localizado em:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Uma das propriedades deste documento `jwks_uri`de configuração é, cujo valor para o mesmo fluxo de utilizador seria:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Para determinar qual o fluxo do utilizador utilizado na assinatura de um token de identificação (e de onde obter os metadados), tem duas opções. Em primeiro lugar, o nome `acr` de fluxo do utilizador está incluído na reclamação no token ID. A outra opção é codificar o fluxo `state` do utilizador no valor do parâmetro quando emitir o pedido e, em seguida, descodificá-lo para determinar qual o fluxo do utilizador utilizado. Qualquer um dos métodos é válido.

Depois de ter adquirido o documento de metadados do ponto final dos metadados OpenID Connect, pode utilizar as teclas públicas RSA 256 para validar a assinatura do token ID. Pode haver várias chaves listadas neste ponto `kid` final, cada uma identificada por uma reclamação. O cabeçalho do símbolo de `kid` identificação também contém uma reclamação, que indica quais destas chaves foram usadas para assinar o símbolo de identificação.

Para verificar as fichas do Azure AD B2C, é necessário gerar a chave pública utilizando o expoente(e) e o modulo(n). Tem de determinar como fazê-lo na sua respetiva linguagem de programação em conformidade. A documentação oficial sobre a geração Chave Pública com o protocolo RSA pode ser consultada aqui:https://tools.ietf.org/html/rfc3447#section-3.1

Depois de ter validado a assinatura do símbolo de identificação, existem várias alegações que precisa de verificar. Por exemplo:

- Valide `nonce` a alegação para evitar ataques de repetição de tokens. O seu valor deve ser o que especificou no pedido de inscrição.
- Valide `aud` a alegação para garantir que o token de identificação foi emitido para a sua aplicação. O seu valor deve ser a identificação da aplicação da sua aplicação.
- Valide `iat` `exp` o e alega que o token de identificação não expirou.

Há também várias mais validações que deve realizar. As validações são descritas em detalhe na Especificação do Núcleo de [Ligação OpenID](https://openid.net/specs/openid-connect-core-1_0.html). Também pode querer validar reclamações adicionais, dependendo do seu cenário. Algumas validações comuns incluem:

- Garantindo que o utilizador/organização se inscreveu para a aplicação.
- Garantindo que o utilizador tem autorização/privilégios adequados.
- Garantindo que ocorreu uma certa força de autenticação, como a Autenticação Multi-Factor Azure.

Depois de validar o símbolo de identificação, pode iniciar uma sessão com o utilizador. Pode utilizar as reclamações no token ID para obter informações sobre o utilizador na sua aplicação. As utilizações para estas informações incluem exibição, registos e autorização.

## <a name="get-a-token"></a>Pegue um símbolo

Se precisar da sua aplicação web apenas para executar fluxos de utilizadores, pode ignorar as próximas secções. Estas secções aplicam-se apenas a aplicações web que precisam de fazer chamadas autenticadas para uma API web e também estão protegidas pelo Azure AD B2C.

Pode resgatar o código de autorização que `response_type=code+id_token`adquiriu (utilizando) para um símbolo `POST` do recurso `/token` pretendido, enviando um pedido para o ponto final. No Azure AD B2C, pode solicitar fichas de [acesso para outras APIs,](access-tokens.md#request-a-token) como de costume, especificando os seus âmbitos no pedido.

Também pode solicitar um sinal de acesso para a própria Web API da sua aplicação por convenção de utilização do ID do cliente da aplicação como âmbito solicitado (o que resultará num sinal de acesso com esse ID do cliente como "público"):

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parâmetro | Necessário | Descrição |
| --------- | -------- | ----------- |
| {inquilino} | Sim | Nome do seu inquilino Azure AD B2C |
| {política} | Sim | O fluxo de utilizador que foi usado para adquirir o código de autorização. Não é possível utilizar um fluxo de utilizador diferente neste pedido. Adicione este parâmetro à corda de consulta, não ao corpo DO POST. |
| client_id | Sim | O ID de aplicação que o [portal Azure](https://portal.azure.com/) atribuiu à sua aplicação. |
| client_secret | Sim, em Aplicações Web | O segredo de aplicação que foi gerado no [portal Azure.](https://portal.azure.com/) Os segredos do cliente são usados neste fluxo para cenários de Web App, onde o cliente pode armazenar de forma segura um segredo de cliente. Para cenários de App Nativa (cliente público), os segredos dos clientes não podem ser armazenados de forma segura, pelo que não são utilizados neste fluxo. Se utilizar um segredo de cliente, por favor, troque-o periodontime. |
| code | Sim | O código de autorização que adquiriu no início do fluxo do utilizador. |
| grant_type | Sim | O tipo de subvenção, que deve ser `authorization_code` para o fluxo de código de autorização. |
| redirect_uri | Sim | O `redirect_uri` parâmetro do pedido onde recebeu o código de autorização. |
| scope | Não | Uma lista de âmbitos separados pelo espaço. O `openid` âmbito indica uma permissão para iniciar sessão no utilizador e obter dados sobre o utilizador sob a forma de parâmetros id_token. Pode ser usado para obter fichas para a própria Web API da sua aplicação, que é representada pelo mesmo ID de aplicação que o cliente. O `offline_access` âmbito indica que a sua aplicação necessita de um token de atualização para um acesso alargado aos recursos. |

Uma resposta simbólica bem sucedida parece:

```JSON
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
| not_before | O tempo em que o símbolo é considerado válido, em época. |
| token_type | O valor do tipo simbólico. `Bearer`é o único tipo que é suportado. |
| access_token | O símbolo assinado jWT que pediu. |
| scope | Os âmbitos para os quais o símbolo é válido. |
| expires_in | O tempo que o token de acesso é válido (em segundos). |
| refresh_token | Um token refresh OAuth 2.0. O pedido pode usar este símbolo para adquirir tokens adicionais após o token atual expirar. As fichas de atualização podem ser utilizadas para manter o acesso aos recursos por longos períodos de tempo. O `offline_access` âmbito deve ter sido utilizado tanto nos pedidos de autorização como de token para receber um sinal de atualização. |

As respostas de erro parecem:

```JSON
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

Agora que adquiriu com sucesso um sinal de acesso, pode usar o símbolo em pedidos para as `Authorization` suas APIs web back-end, incluindo-o no cabeçalho:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Refrescar o símbolo

As fichas de identificação expiram num curto espaço de tempo. Refresque as fichas depois de expirarem para continuar a ter acesso aos recursos. Pode refrescar um símbolo submetendo `POST` outro `/token` pedido ao ponto final. Desta vez, `refresh_token` forneça o parâmetro `code` em vez do parâmetro:

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parâmetro | Necessário | Descrição |
| --------- | -------- | ----------- |
| {inquilino} | Sim | Nome do seu inquilino Azure AD B2C |
| {política} | Sim | O fluxo do utilizador que foi usado para adquirir o token de atualização original. Não é possível utilizar um fluxo de utilizador diferente neste pedido. Adicione este parâmetro à corda de consulta, não ao corpo DO POST. |
| client_id | Sim | O ID de aplicação que o [portal Azure](https://portal.azure.com/) atribuiu à sua aplicação. |
| client_secret | Sim, em Aplicações Web | O segredo de aplicação que foi gerado no [portal Azure.](https://portal.azure.com/) Os segredos do cliente são usados neste fluxo para cenários de Web App, onde o cliente pode armazenar de forma segura um segredo de cliente. Para cenários de App Nativa (cliente público), os segredos dos clientes não podem ser armazenados de forma segura, pelo que não são utilizados nesta chamada. Se utilizar um segredo de cliente, por favor, troque-o periodontime. |
| grant_type | Sim | O tipo de subvenção, que deve ser um sinal de atualização para esta parte do fluxo do código de autorização. |
| refresh_token | Sim | O símbolo original de atualização que foi adquirido na segunda parte do fluxo. O `offline_access` âmbito deve ser utilizado tanto nos pedidos de autorização como em token, a fim de receber um sinal de atualização. |
| redirect_uri | Não | O `redirect_uri` parâmetro do pedido onde recebeu o código de autorização. |
| scope | Não | Uma lista de âmbitos separados pelo espaço. O `openid` âmbito indica uma permissão para iniciar sessão no utilizador e obter dados sobre o utilizador sob a forma de fichas de identificação. Pode ser usado para enviar fichas para a própria Web API da sua aplicação, que é representada pelo mesmo ID de aplicação que o cliente. O `offline_access` âmbito indica que a sua aplicação necessita de um token de atualização para um acesso alargado aos recursos. |

Uma resposta simbólica bem sucedida parece:

```JSON
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
| not_before | O tempo em que o símbolo é considerado válido, em época. |
| token_type | O valor do tipo simbólico. `Bearer`é o único tipo que é suportado. |
| access_token | O símbolo assinado jWT que foi solicitado. |
| scope | O âmbito para o qual o símbolo é válido. |
| expires_in | O tempo que o token de acesso é válido (em segundos). |
| refresh_token | Um token refresh OAuth 2.0. O pedido pode usar este símbolo para adquirir tokens adicionais após o token atual expirar. As fichas de atualização podem ser utilizadas para manter o acesso aos recursos por longos períodos de tempo. |

As respostas de erro parecem:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --------- | ----------- |
| erro | Um código que pode ser usado para classificar tipos de erros que ocorrem. |
| error_description | Uma mensagem que pode ajudar a identificar a causa principal de um erro de autenticação. |

## <a name="send-a-sign-out-request"></a>Enviar um pedido de inscrição

Quando pretende assinar o utilizador fora da aplicação, não basta limpar os cookies da aplicação ou terminar a sessão com o utilizador. Redirecione o utilizador para O Azure AD B2C para assinar. Se não o fizer, o utilizador poderá reautenticar a sua aplicação sem voltar a introduzir as suas credenciais. Para mais informações, consulte a [sessão Azure AD B2C](session-overview.md).

Para assinar o utilizador, redirecione o utilizador para o `end_session` ponto final listado no documento de metadados OpenID Connect descrito anteriormente:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| Parâmetro | Necessário | Descrição |
| --------- | -------- | ----------- |
| {inquilino} | Sim | Nome do seu inquilino Azure AD B2C |
| {política} | Sim | O fluxo de utilizador que pretende utilizar para assinar o utilizador fora da sua aplicação. |
| id_token_hint| Não | Um token id previamente emitido para passar para o ponto final de logout como uma dica sobre a atual sessão autenticada do utilizador final com o cliente. O `id_token_hint` garante que `post_logout_redirect_uri` se trata de um URL de resposta registado nas definições de aplicação Do AD B2C do Azure. |
| client_id | Não, não, não. | O ID de aplicação que o [portal Azure](https://portal.azure.com/) atribuiu à sua aplicação.<br><br>\**Isto é necessário `Application` quando se utiliza a configuração SSO de `No`isolamento e requer id _token_ no pedido de logout para .* |
| post_logout_redirect_uri | Não | O URL para o que o utilizador deve ser redirecionado após o sucesso da assinatura. Se não estiver incluído, o Azure AD B2C mostra ao utilizador uma mensagem genérica. A menos `id_token_hint`que forneça um , não deve registar este URL como URL de resposta nas definições de aplicação Azure AD B2C. |
| state | Não | Se `state` um parâmetro estiver incluído no pedido, o mesmo valor deve figurar na resposta. O pedido deve `state` verificar se os valores do pedido e da resposta são idênticos. |

### <a name="secure-your-logout-redirect"></a>Fixe o seu redirecionamento de logout

Após o logout, o utilizador é redirecionado `post_logout_redirect_uri` para o URI especificado no parâmetro, independentemente dos URLs de resposta especificados para a aplicação. No entanto, `id_token_hint` se um válido for aprovado, o Azure `post_logout_redirect_uri` AD B2C verifica que o valor da correspondência corresponde a um dos URIs de redirecionamento configurado da aplicação antes de realizar o redirecionamento. Se não tiver sido configurado nenhum URL de resposta correspondente para a aplicação, é apresentada uma mensagem de erro e o utilizador não é redirecionado.


