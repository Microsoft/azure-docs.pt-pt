---
title: Entrada na Web com OpenID Connect-Azure Active Directory B2C
description: Crie aplicativos Web usando o protocolo de Autenticação OpenID Connect no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 69b58b402b49e2346621bf473a0e897809f1c008
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712836"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Entrada na Web com o OpenID Connect no Azure Active Directory B2C

O OpenID Connect é um protocolo de autenticação, criado com base no OAuth 2,0, que pode ser usado para conectar com segurança os usuários aos aplicativos Web. Usando a implementação de Azure Active Directory B2C (Azure AD B2C) do OpenID Connect, você pode terceirizar a inscrição, a entrada e outras experiências de gerenciamento de identidade em seus aplicativos Web para Azure Active Directory (Azure AD). Este guia mostra como fazer isso de maneira independente de linguagem. Ele descreve como enviar e receber mensagens HTTP sem usar qualquer uma das nossas bibliotecas de código-fonte aberto.

O [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) estende o protocolo de *autorização* OAuth 2,0 para uso como um protocolo de *autenticação* . Esse protocolo de autenticação permite que você execute o logon único. Ele apresenta o conceito de *token de ID*, que permite ao cliente verificar a identidade do usuário e obter informações básicas de perfil sobre o usuário.

Como ele estende o OAuth 2,0, ele também permite que os aplicativos adquiram *tokens de acesso*com segurança. Você pode usar tokens de acesso para acessar recursos que são protegidos por um [servidor de autorização](active-directory-b2c-reference-protocols.md). O OpenID Connect é recomendado se você estiver criando um aplicativo Web hospedado em um servidor e acessado por meio de um navegador. Para obter mais informações sobre tokens, consulte a [visão geral dos tokens no Azure Active Directory B2C](active-directory-b2c-reference-tokens.md)

Azure AD B2C estende o protocolo OpenID Connect padrão para fazer mais do que a autenticação e a autorização simples. Ele apresenta o [parâmetro de fluxo de usuário](active-directory-b2c-reference-policies.md), que permite que você use o OpenID Connect para adicionar experiências de usuário ao seu aplicativo, como inscrição, entrada e gerenciamento de perfil.

## <a name="send-authentication-requests"></a>Enviar solicitações de autenticação

Quando seu aplicativo Web precisa autenticar o usuário e executar um fluxo de usuário, ele pode direcionar o usuário para o ponto de extremidade `/authorize`. O usuário executa a ação dependendo do fluxo do usuário.

Nessa solicitação, o cliente indica as permissões que precisa adquirir do usuário no parâmetro `scope` e especifica o fluxo de usuário a ser executado. Para ter uma ideia de como a solicitação funciona, tente colar a solicitação em um navegador e executá-la. Substitua `{tenant}` pelo nome do seu locatário. Substitua `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` pela ID do aplicativo que você registrou anteriormente em seu locatário. Mude também o nome da política (`{policy}`) para o nome de política que tem no seu inquilino, por exemplo, `b2c_1_sign_in`.

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

| Parâmetro | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| vários | Sim | Nome do seu locatário de Azure AD B2C |
| regras | Sim | O fluxo do usuário a ser executado. Especifique o nome de um fluxo de usuário que você criou em seu locatário Azure AD B2C. Por exemplo: `b2c_1_sign_in`, `b2c_1_sign_up`ou `b2c_1_edit_profile`. |
| client_id | Sim | A ID do aplicativo que o [portal do Azure](https://portal.azure.com/) atribuído ao seu aplicativo. |
| nonce | Sim | Um valor incluído na solicitação (gerado pelo aplicativo) que é incluído no token de ID resultante como uma declaração. O aplicativo pode, então, verificar esse valor para atenuar os ataques de reprodução de token. O valor normalmente é uma cadeia de caracteres exclusiva aleatória que pode ser usada para identificar a origem da solicitação. |
| response_type | Sim | Deve incluir um token de ID para o OpenID Connect. Se a sua aplicação web também precisar de fichas para chamar uma API web, pode usar `code+id_token`. |
| scope | Sim | Uma lista de escopos separados por espaços. O escopo de `openid` indica uma permissão para conectar o usuário e obter dados sobre o usuário na forma de tokens de ID. O âmbito `offline_access` é opcional para aplicações web. Isso indica que seu aplicativo precisará de um *token de atualização* para acesso estendido aos recursos. |
| aviso | Não | O tipo de interação do usuário que é necessário. O único valor válido no momento é `login`, o que força o usuário a inserir suas credenciais nessa solicitação. |
| redirect_uri | Não | O `redirect_uri` parâmetro da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas através da sua candidatura. Deve corresponder exatamente a um dos parâmetros `redirect_uri` que registou no portal Azure, exceto que deve ser codificado por URL. |
| response_mode | Não | O método usado para enviar o código de autorização resultante de volta para seu aplicativo. Pode ser `query`, `form_post`ou `fragment`.  Recomenda-se o modo de resposta `form_post` para uma melhor segurança. |
| state | Não | Um valor incluído na solicitação que também é retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente geralmente é usado para impedir ataques de solicitação entre sites forjado. O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página em que eles estavam. |

Neste ponto, o usuário é solicitado a concluir o fluxo de trabalho. O usuário pode precisar inserir seu nome de usuário e senha, entrar com uma identidade social ou inscrever-se no diretório. Pode haver qualquer outro número de etapas, dependendo de como o fluxo do usuário é definido.

Após o utilizador completar o fluxo do utilizador, uma resposta é devolvida à sua aplicação no parâmetro indicado `redirect_uri`, utilizando o método especificado no parâmetro `response_mode`. A resposta é a mesma para cada um dos casos anteriores, independentemente do fluxo do usuário.

Uma resposta bem sucedida usando `response_mode=fragment` seria como:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --------- | ----------- |
| id_token | O token de ID que o aplicativo solicitou. Você pode usar o token de ID para verificar a identidade do usuário e iniciar uma sessão com o usuário. |
| code | O código de autorização que o pedido solicitou, se utilizou `response_type=code+id_token`. O aplicativo pode usar o código de autorização para solicitar um token de acesso para um recurso de destino. Os códigos de autorização normalmente expiram após cerca de 10 minutos. |
| state | Se um parâmetro `state` for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de `state` na solicitação e na resposta são idênticos. |

As respostas de erro também podem ser enviadas para o parâmetro `redirect_uri` para que a aplicação possa manuseá-las adequadamente:

```HTTP
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --------- | ----------- |
| erro | Um código que pode ser usado para classificar os tipos de erros que ocorrem. |
| error_description | Uma mensagem de erro específica que pode ajudar a identificar a causa raiz de um erro de autenticação. |
| state | Se um parâmetro `state` for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de `state` na solicitação e na resposta são idênticos. |

## <a name="validate-the-id-token"></a>Validar o token de ID

Apenas receber um token de ID não é suficiente para autenticar o usuário. Valide a assinatura do token de ID e verifique as declarações no token de acordo com os requisitos do seu aplicativo. O Azure AD B2C usa [JWTs (tokens Web JSON)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar se eles são válidos. Há muitas bibliotecas de software livre que estão disponíveis para validar o JWTs, dependendo da linguagem de preferência. É recomendável explorar essas opções em vez de implementar sua própria lógica de validação.

Azure AD B2C tem um ponto de extremidade de metadados do OpenID Connect, que permite que um aplicativo Obtenha informações sobre Azure AD B2C em tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e chaves de assinatura de token. Há um documento de metadados JSON para cada fluxo de usuário em seu locatário B2C. Por exemplo, o documento de metadados para o fluxo de utilizadores `b2c_1_sign_in` em `fabrikamb2c.onmicrosoft.com` está localizado em:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Uma das propriedades deste documento de configuração é `jwks_uri`, cujo valor para o mesmo fluxo de utilizador seria:

```HTTP
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Para determinar qual fluxo de usuário foi usado na assinatura de um token de ID (e de onde obter os metadados), você tem duas opções. Primeiro, o nome do fluxo de usuário é incluído na declaração de `acr` no token de ID. Sua outra opção é codificar o fluxo do usuário no valor do parâmetro `state` ao emitir a solicitação e, em seguida, decodificá-la para determinar qual fluxo do usuário foi usado. O método é válido.

Depois de adquirir o documento de metadados do ponto de extremidade de metadados do OpenID Connect, você pode usar as chaves públicas do RSA 256 para validar a assinatura do token de ID. Pode haver várias chaves listadas nesse ponto de extremidade, cada uma identificada por uma declaração de `kid`. O cabeçalho do token de ID também contém uma declaração de `kid`, que indica quais dessas chaves foram usadas para assinar o token de ID.

Para verificar os tokens de Azure AD B2C, você precisa gerar a chave pública usando o expoente (e) e o módulo (n). Você precisa determinar como fazer isso em sua respectiva linguagem de programação de acordo. A documentação oficial sobre a geração de chave pública com o protocolo RSA pode ser encontrada aqui: https://tools.ietf.org/html/rfc3447#section-3.1

Depois de validar a assinatura do token de ID, há várias declarações que você precisa verificar. Por exemplo:

- Valide a declaração de `nonce` para evitar ataques de reprodução de token. Seu valor deve ser o que você especificou na solicitação de entrada.
- Valide a declaração de `aud` para garantir que o token de ID foi emitido para seu aplicativo. Seu valor deve ser a ID do aplicativo do seu aplicativo.
- Valide as declarações de `iat` e `exp` para garantir que o token de ID não tenha expirado.

Também há várias outras validações que você deve executar. As validações são descritas em detalhes na [especificação de núcleo do OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). Você também pode querer validar declarações adicionais, dependendo do seu cenário. Algumas validações comuns incluem:

- Garantir que o usuário/organização tenha se inscrito no aplicativo.
- Garantindo que o usuário tenha autorização/privilégios adequados.
- Garantindo que uma determinada força de autenticação tenha ocorrido, como a autenticação multifator do Azure.

Depois de validar o token de ID, você pode iniciar uma sessão com o usuário. Você pode usar as declarações no token de ID para obter informações sobre o usuário em seu aplicativo. Os usos para essas informações incluem exibição, registros e autorização.

## <a name="get-a-token"></a>Obter um token

Se precisar que seu aplicativo Web execute apenas fluxos de usuário, você poderá ignorar as próximas seções. Essas seções são aplicáveis somente a aplicativos Web que precisam fazer chamadas autenticadas para uma API da Web e também são protegidas por Azure AD B2C.

Pode resgatar o código de autorização que adquiriu (utilizando `response_type=code+id_token`) para um símbolo do recurso pretendido, enviando um pedido de `POST` para o `/token` ponto final. No Azure AD B2C, você pode [solicitar tokens de acesso para outras APIs](active-directory-b2c-access-tokens.md#request-a-token) como de costume, especificando seus escopos na solicitação.

Você também pode solicitar um token de acesso para a própria API Web de back-end do seu aplicativo por convenção de usar a ID do cliente do aplicativo como o escopo solicitado (o que resultará em um token de acesso com essa ID de cliente como o "público"):

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parâmetro | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| vários | Sim | Nome do seu locatário de Azure AD B2C |
| regras | Sim | O fluxo do usuário que foi usado para adquirir o código de autorização. Você não pode usar um fluxo de usuário diferente nesta solicitação. Adicione esse parâmetro à cadeia de caracteres de consulta, não ao corpo da POSTAgem. |
| client_id | Sim | A ID do aplicativo que o [portal do Azure](https://portal.azure.com/) atribuído ao seu aplicativo. |
| client_secret | Sim, em aplicativos Web | O segredo do aplicativo que foi gerado no [portal do Azure](https://portal.azure.com/). Os segredos do cliente são usados nesse fluxo para cenários de aplicativos Web, em que o cliente pode armazenar com segurança um segredo do cliente. Para cenários de aplicativo nativo (cliente público), os segredos do cliente não podem ser armazenados com segurança, threfore não usados nesse fluxo. Se estiver usando um segredo do cliente, altere-o periodicamente. |
| code | Sim | O código de autorização que você adquiriu no início do fluxo do usuário. |
| grant_type | Sim | O tipo de subvenção, que deve ser `authorization_code` para o fluxo do código de autorização. |
| redirect_uri | Sim | O `redirect_uri` parâmetro do pedido onde recebeu o código de autorização. |
| scope | Não | Uma lista de escopos separados por espaços. O âmbito `openid` indica uma permissão para iniciar sessão no utilizador e obter dados sobre o utilizador sob a forma de parâmetros id_token. Ele pode ser usado para obter tokens para a própria API Web de back-end do seu aplicativo, que é representada pela mesma ID do aplicativo que o cliente. O âmbito `offline_access` indica que a sua aplicação precisa de um token de atualização para um acesso alargado aos recursos. |

Uma resposta de token bem-sucedida é semelhante A:

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
| not_before | A hora em que o token é considerado válido, no momento da época. |
| token_type | O valor do tipo de token. `Bearer` é o único tipo com suporte. |
| access_token | O token JWT assinado que você solicitou. |
| scope | Os escopos para os quais o token é válido. |
| expires_in | O período de tempo que o token de acesso é válido (em segundos). |
| refresh_token | Um token de atualização OAuth 2,0. O aplicativo pode usar esse token para adquirir tokens adicionais depois que o token atual expirar. Os tokens de atualização podem ser usados para manter o acesso aos recursos por longos períodos de tempo. O âmbito `offline_access` deve ter sido utilizado tanto nos pedidos de autorização como em token, a fim de receber um sinal de atualização. |

As respostas de erro são semelhantes a:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --------- | ----------- |
| erro | Um código que pode ser usado para classificar tipos de erros que ocorrem. |
| error_description | Uma mensagem que pode ajudar a identificar a causa raiz de um erro de autenticação. |

## <a name="use-the-token"></a>Usar o token

Agora que você adquiriu com êxito um token de acesso, você pode usar o token em solicitações para suas APIs Web de back-end, incluindo-o no cabeçalho `Authorization`:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Atualizar o token

Tokens de ID expiram em um curto período de tempo. Atualize os tokens depois que eles expirarem para continuar a ser capaz de acessar recursos. Você pode atualizar um token enviando outra solicitação de `POST` para o ponto de extremidade `/token`. Desta vez, forneça o parâmetro de `refresh_token` em vez do parâmetro `code`:

```HTTP
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parâmetro | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| vários | Sim | Nome do seu locatário de Azure AD B2C |
| regras | Sim | O fluxo do usuário que foi usado para adquirir o token de atualização original. Você não pode usar um fluxo de usuário diferente nesta solicitação. Adicione esse parâmetro à cadeia de caracteres de consulta, não ao corpo da POSTAgem. |
| client_id | Sim | A ID do aplicativo que o [portal do Azure](https://portal.azure.com/) atribuído ao seu aplicativo. |
| client_secret | Sim, em aplicativos Web | O segredo do aplicativo que foi gerado no [portal do Azure](https://portal.azure.com/). Os segredos do cliente são usados nesse fluxo para cenários de aplicativos Web, em que o cliente pode armazenar com segurança um segredo do cliente. Para cenários de aplicativo nativo (cliente público), os segredos do cliente não podem ser armazenados com segurança, threfore não usados nessa chamada. Se estiver usando um segredo do cliente, altere-o periodicamente. |
| grant_type | Sim | O tipo de concessão, que deve ser um token de atualização para esta parte do fluxo do código de autorização. |
| refresh_token | Sim | O token de atualização original que foi adquirido na segunda parte do fluxo. O âmbito `offline_access` deve ser utilizado tanto nos pedidos de autorização como em token, a fim de receber um sinal de atualização. |
| redirect_uri | Não | O `redirect_uri` parâmetro do pedido onde recebeu o código de autorização. |
| scope | Não | Uma lista de escopos separados por espaços. O escopo de `openid` indica uma permissão para conectar o usuário e obter dados sobre o usuário na forma de tokens de ID. Ele pode ser usado para enviar tokens para a própria API Web de back-end do seu aplicativo, que é representada pela mesma ID do aplicativo que o cliente. O âmbito `offline_access` indica que a sua aplicação precisa de um token de atualização para um acesso alargado aos recursos. |

Uma resposta de token bem-sucedida é semelhante A:

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
| not_before | A hora em que o token é considerado válido, no momento da época. |
| token_type | O valor do tipo de token. `Bearer` é o único tipo com suporte. |
| access_token | O token JWT assinado que foi solicitado. |
| scope | O escopo para o qual o token é válido. |
| expires_in | O período de tempo que o token de acesso é válido (em segundos). |
| refresh_token | Um token de atualização OAuth 2,0. O aplicativo pode usar esse token para adquirir tokens adicionais depois que o token atual expirar. Os tokens de atualização podem ser usados para manter o acesso aos recursos por longos períodos de tempo. |

As respostas de erro são semelhantes a:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --------- | ----------- |
| erro | Um código que pode ser usado para classificar tipos de erros que ocorrem. |
| error_description | Uma mensagem que pode ajudar a identificar a causa raiz de um erro de autenticação. |

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de saída

Quando você deseja desconectar o usuário do aplicativo, não é suficiente limpar os cookies do aplicativo ou encerrar a sessão com o usuário. Redirecione o usuário para Azure AD B2C para sair. Se você não conseguir fazer isso, o usuário poderá se autenticar novamente em seu aplicativo sem inserir suas credenciais novamente.

Para assinar o utilizador, redirecione o utilizador para o ponto final `end_session` listado no documento de metadados OpenID Connect descrito anteriormente:

```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| Parâmetro | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| vários | Sim | Nome do seu locatário de Azure AD B2C |
| regras | Sim | O fluxo de usuário que você deseja usar para desconectar o usuário do seu aplicativo. |
| id_token_hint| Não | Um token de ID emitido anteriormente para passar para o ponto de extremidade de logout como uma dica sobre a sessão autenticada atual do usuário final com o cliente. O `id_token_hint` garante que o `post_logout_redirect_uri` é um URL de resposta registado nas definições de aplicação Azure AD B2C. |
| client_id | Foi | A ID do aplicativo que o [portal do Azure](https://portal.azure.com/) atribuído ao seu aplicativo.<br><br>\**isso é necessário ao usar `Application` configuração de SSO de isolamento e _exigir token de ID_ na solicitação de logout é definido como `No`.* |
| post_logout_redirect_uri | Não | A URL para a qual o usuário deve ser redirecionado após a saída bem-sucedida. Se não estiver incluído, Azure AD B2C mostrará ao usuário uma mensagem genérica. A menos que forneça um `id_token_hint`, não deve registar este URL como URL de resposta nas definições de aplicação Azure AD B2C. |
| state | Não | Se um parâmetro `state` for incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de `state` na solicitação e na resposta são idênticos. |

### <a name="secure-your-logout-redirect"></a>Proteger seu redirecionamento de logout

Após o logout, o utilizador é redirecionado para o URI especificado no parâmetro `post_logout_redirect_uri`, independentemente dos URLs de resposta especificados para a aplicação. No entanto, se for aprovada uma `id_token_hint` válida, o Azure AD B2C verifica que o valor do `post_logout_redirect_uri` corresponde a um dos URIs de redirecionamento configurado da aplicação antes de realizar o redirecionamento. Se nenhuma URL de resposta correspondente tiver sido configurada para o aplicativo, uma mensagem de erro será exibida e o usuário não será redirecionado.

### <a name="external-identity-provider-sign-out"></a>Logout do provedor de identidade externo

Direcionar o utilizador para o ponto final `end_session` iliba parte do único estado de inscrição do utilizador com o Azure AD B2C, mas não assina o utilizador fora da sua sessão de fornecedor de identidade social (IDP). Se o usuário selecionar o mesmo IDP durante uma entrada subsequente, ele será reautenticado sem inserir suas credenciais. Se um usuário quiser sair do aplicativo, isso não significa necessariamente que deseja sair de sua conta do Facebook. No entanto, se forem usadas contas locais, a sessão do usuário será encerrada corretamente.
