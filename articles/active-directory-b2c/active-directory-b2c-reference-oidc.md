---
title: Web início de sessão com OpenID Connect - Azure Active Directory B2C | Documentos da Microsoft
description: Criação de aplicativos web, utilizando a implementação do Azure Active Directory do protocolo de autenticação OpenID Connect.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: bd7ecf273d4e842909d88eeaa3683203d8d9e841
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429170"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Sessão Web com OpenID Connect no Azure Active Directory B2C

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) é um protocolo de autenticação, criado com base no OAuth 2.0, que pode ser utilizado para assinar com segurança os utilizadores aplicações web. Ao utilizar o Azure Active Directory B2C (Azure AD B2C) a implementação do OpenID Connect, que possa terceirizar a inscrição, início de sessão e experiências de outro gestão de identidades nas suas aplicações web ao Azure Active Directory (Azure AD). Este guia mostra-lhe como fazê-lo de forma independente de idioma. Ele descreve como enviar e receber mensagens HTTP sem utilizar qualquer uma das nossas bibliotecas de código-fonte aberto.

OpenID Connect expande o OAuth 2.0 *autorização* protocolo para utilização como um *autenticação* protocolo. Isto permite-lhe efetuar o início de sessão único com o OAuth. Ele introduz o conceito de um *token de ID*, que é um token de segurança que permite que o cliente verificar a identidade do utilizador e obter informações de perfil básicas sobre o utilizador.

Uma vez que expande o OAuth 2.0, também permite aplicações com segurança adquirir *tokens de acesso*. Pode utilizar tokens de acesso para aceder a recursos que estão protegidos por um [servidor de autorização](active-directory-b2c-reference-protocols.md#the-basics). É recomendável OpenID Connect se estiver a criar uma aplicação web que é alojada num servidor e acedida através de um browser. Se pretender adicionar o gerenciamento de identidades às suas aplicações móveis ou ambiente de trabalho utilizando o Azure AD B2C, deve usar [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) em vez de OpenID Connect.

O Azure AD B2C expande o protocolo OpenID Connect padrão para fazer mais do que a autenticação e autorização simples. Ele introduz o [parâmetro de fluxo de utilizador](active-directory-b2c-reference-policies.md), que permite-lhe utilizar o OpenID Connect para adicionar experiências de utilizador, tais como inscrição, início de sessão e gestão de perfis para a sua aplicação. Fornecedores de identidade que utilizam o protocolo OpenID Connect incluem [conta Microsoft](active-directory-b2c-setup-msa-app.md) e outras [fornecedores de ligação do OpenID](active-directory-b2c-setup-oidc-idp.md).

Os pedidos HTTP de exemplo na secção seguinte utilizam o nosso diretório de B2C de exemplo, fabrikamb2c.onmicrosoft.com, bem como nosso aplicativo de exemplo, https://aadb2cplayground.azurewebsites.nete fluxos de utilizador. Tem liberdade para experimentar as solicitações por conta própria, utilizando estes valores, ou pode substituí-los com os seus próprios.
Saiba como [obter seus próprios fluxos de utilizador, aplicação e inquilino de B2C](#use-your-own-b2c-tenant).

## <a name="send-authentication-requests"></a>Enviar pedidos de autenticação
Quando a aplicação web tiver de autenticar o usuário e executar um fluxo de utilizador, pode direcionar o utilizador para o `/authorize` ponto final. Esta é a parte interativa do fluxo, em que o utilizador efetua uma ação, consoante o fluxo de utilizador.

Este pedido, o cliente indica as permissões que ele necessita obter do usuário no `scope` parâmetro e o fluxo de utilizador para executar o `p` parâmetro. Três exemplos são fornecidos nas secções seguintes (com quebras de linha para facilitar a leitura), cada um usando um fluxo de utilizador diferente. Para ter uma noção de como funciona cada solicitação, tente colar o pedido para um browser e executá-lo.

#### <a name="use-a-sign-in-user-flow"></a>Utilizar um fluxo de início de sessão do utilizador
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-user-flow"></a>Utilizar um fluxo de inscrição de utilizador
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-user-flow"></a>Utilizar um fluxo de utilizador de Editar perfil
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
| client_id |Necessário |ID de aplicação que o [portal do Azure](https://portal.azure.com/) atribuído à sua aplicação. |
| response_type |Necessário |O tipo de resposta, que tem de incluir um token de ID para OpenID Connect. Se a sua aplicação web tem também de tokens para chamar uma API web, pode utilizar `code+id_token`, como fizemos aqui. |
| redirect_uri |Recomendado |O `redirect_uri` parâmetro da sua aplicação, onde as respostas podem ser enviadas e recebidas pela sua aplicação. Tem de corresponder exatamente um do `redirect_uri` parâmetros que registou no portal, exceto pelo fato de que tem de ser codificado de URL. |
| scope |Necessário |Uma lista de âmbitos separadas por espaços. Indica um valor de âmbito único para o Azure AD ambas as permissões que estão a ser solicitadas. O `openid` âmbito indica uma permissão para iniciar a sessão do utilizador e obter dados sobre o utilizador na forma de tokens de ID (mais por vir sobre isso posteriormente neste artigo). O `offline_access` âmbito é opcional para aplicações web. Ele indica que a sua aplicação será necessário um *token de atualização* de longa duração acesso a recursos. |
| response_mode |Recomendado |O método que deve ser utilizado para enviar o código de autorização resultante de volta à aplicação. Ele pode ser uma `query`, `form_post`, ou `fragment`.  O `form_post` modo de resposta é recomendado para melhor segurança. |
| state |Recomendado |Um valor incluído no pedido que também é devolvido na resposta de token. Pode ser uma cadeia de caracteres de qualquer conteúdo que pretende. Um valor exclusivo gerado aleatoriamente é normalmente utilizado para impedir ataques de falsificação de solicitação entre sites. O estado também é usado para codificar as informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, como a página que onde estavam. |
| Valor de uso único |Necessário |Um valor incluído no pedido (gerado pela aplicação) que será incluído no token de ID resultante como uma afirmação. A aplicação pode, em seguida, verifique se este valor para mitigar ataques de repetição de token. O valor normalmente é uma cadeia exclusiva aleatória que pode ser utilizada para identificar a origem do pedido. |
| p |Necessário |O fluxo de utilizador que será executado. É o nome de um fluxo de utilizador que é criado no seu inquilino do B2C. O valor de nome de fluxo de utilizador deve iniciar com `b2c\_1\_`. Saiba mais sobre as políticas e o [estrutura de fluxo de utilizador extensible](active-directory-b2c-reference-policies.md). |
| linha de comandos |Opcional |O tipo de interação do utilizador que é necessário. O valor só é válido neste momento é `login`, que força o utilizador introduza as credenciais desse pedido. Início de sessão único não irão surtir efeito. |

Neste momento, é pedido ao utilizador para concluir o fluxo de trabalho do fluxo de utilizador. Isto pode envolver o utilizador introduzir o nome de utilizador e palavra-passe, início de sessão com uma identidade de redes sociais, inscrever-se para o diretório ou qualquer outro número de passos, dependendo de como o fluxo de utilizador está definido.

Depois do utilizador concluir o fluxo de utilizador, do Azure AD devolve uma resposta à sua aplicação no indicado `redirect_uri` parâmetro, usando o método especificado no `response_mode` parâmetro. A resposta é o mesmo para cada um dos casos descritos atrás, independentemente do fluxo de utilizador que é executado.

Uma resposta com êxito utilizando `response_mode=fragment` teria o seguinte aspeto:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --- | --- |
| id_token |O token de ID que a aplicação pedida. Pode usar o token de ID para verificar a identidade do utilizador e iniciar uma sessão com o utilizador. Obter mais detalhes sobre os tokens de ID e seu conteúdo são incluídos no [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). |
| Código |O código de autorização que a aplicação é solicitada, se utilizou `response_type=code+id_token`. A aplicação pode utilizar o código de autorização para pedir um token de acesso para um recurso de destino. Códigos de autorização são muito curta duração. Normalmente, esta expira após cerca de 10 minutos. |
| state |Se um `state` parâmetro está incluído na solicitação, o mesmo valor deve aparecer na resposta. A aplicação deve verificar se o `state` valores no pedido e resposta são idênticos. |

Respostas de erro também podem ser enviadas para o `redirect_uri` parâmetro para que a aplicação pode processar corretamente:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e que podem ser utilizados para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação do desenvolvedor. |
| state |Consulte a descrição completa na primeira tabela nesta secção. Se um `state` parâmetro está incluído na solicitação, o mesmo valor deve aparecer na resposta. A aplicação deve verificar se o `state` valores no pedido e resposta são idênticos. |

## <a name="validate-the-id-token"></a>Validar o token de ID
Apenas receber um token de ID não é suficiente para autenticar o utilizador. Tem de validar a assinatura do token de ID e verifique se as afirmações no token de conformidade com os requisitos da sua aplicação. Azure AD B2C utiliza [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar os tokens e certifique-se de que são válidas.

Existem muitas bibliotecas de código-fonte aberto que estão disponíveis para validar JWTs, consoante o idioma de preferência. Recomendamos explorar essas opções, em vez de implementar sua própria lógica de validação. As informações aqui será útil para descobrir como usar adequadamente dessas bibliotecas.

O Azure AD B2C tem um OpenID Connect ponto final de metadados, que permite que uma aplicação obter informações sobre o Azure AD B2C no tempo de execução. Estas informações incluem pontos finais, conteúdo de token e chaves de assinatura de tokens. Existe um documento de metadados JSON para cada fluxo de utilizador no seu inquilino do B2C. Por exemplo, o documento de metadados para o `b2c_1_sign_in` fluxo de utilizador em `fabrikamb2c.onmicrosoft.com` está localizado em:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

Uma das propriedades deste documento de configuração é `jwks_uri`, cujo valor para o mesmo fluxo de utilizador seria:

`https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

Para determinar o fluxo de utilizador foi utilizado na assinatura de um ID de token (e o local obter os metadados), tem duas opções. Em primeiro lugar, o nome do fluxo de utilizador está incluído no `acr` de afirmações no token de ID. Para obter informações sobre como analisar as afirmações a partir de um token de ID, consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md). A outra opção é codificar o fluxo de utilizador no valor do `state` parâmetro quando emitir o pedido e, em seguida, decodificá-la para determinar o fluxo de utilizador foi utilizado. Qualquer um dos métodos é válido.

Depois de adquirir o documento de metadados a partir do ponto final de metadados OpenID Connect, pode utilizar as chaves públicas RSA 256 (que estão localizadas neste ponto final) para validar a assinatura do token de ID. Podem existir várias chaves listadas neste ponto final num determinado período tempo, cada identificado por uma `kid` de afirmação. O cabeçalho do token de ID também contém um `kid` de afirmação, que indica que estas chaves foi utilizado para assinar o token de ID. Para obter mais informações, consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md) (a seção sobre [tokens de validação](active-directory-b2c-reference-tokens.md#token-validation), em particular).
<!--TODO: Improve the information on this-->

Depois de ter confirmado a assinatura do token de ID, existem várias afirmações que precisa verificar. Por exemplo:

* Deve validar o `nonce` impedir ataques de repetição de token de afirmação. O valor deve ser especificado no pedido de início de sessão.
* Deve validar o `aud` de afirmação para se certificar de que o token de ID foi emitido para a sua aplicação. O valor deve ser o ID da aplicação da sua aplicação.
* Deve validar os `iat` e `exp` afirmações para se certificar de que o token de ID não expirou.

Também há vários validações mais que deve executar. Estas são descritas detalhadamente na [especificação do OpenID Connect Core](https://openid.net/specs/openid-connect-core-1_0.html).  Pode também querer validar afirmações adicionais, dependendo do seu cenário. Algumas validações comuns incluem:

* Garantir que o utilizador/organização tiver se inscrito para a aplicação.
* Garantir que o utilizador tem autorização/privilégios apropriados.
* Garantir que um determinada força de autenticação ocorreu, como o Azure multi-factor Authentication.

Para obter mais informações sobre as afirmações no token de ID, consulte a [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md).

Depois de validar o token de ID, pode iniciar uma sessão com o utilizador. Pode utilizar as declarações no token de ID para obter informações sobre o utilizador na sua aplicação. Utilizações estas informações incluem a exibição, registos e autorização.

## <a name="get-a-token"></a>Obter um token
Se precisar da aplicação web para executar apenas fluxos de utilizador, pode ignorar as próximas seções. Estas secções são aplicáveis apenas a web, aplicações que precisam para fazer chamadas a uma API web autenticadas e também estão protegidas pelo Azure AD B2C.

Pode resgatar o código de autorização que obteve (utilizando `response_type=code+id_token`) para um token para o recurso pretendido, enviando um `POST` pedido para o `/token` ponto final. Atualmente, o único recurso que pode pedir um token para é a API web back-end da aplicação. A Convenção para pedir um token a próprio é usar o ID de cliente da sua aplicação como o âmbito:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
| p |Necessário |O fluxo de utilizador que foi utilizado para adquirir o código de autorização. Não é possível utilizar um fluxo de utilizador diferente neste pedido. Tenha em atenção que adicionar este parâmetro para a cadeia de consulta, não o `POST` corpo. |
| client_id |Necessário |ID de aplicação que o [portal do Azure](https://portal.azure.com/) atribuído à sua aplicação. |
| grant_type |Necessário |O tipo de concessão, que tem de ser `authorization_code` para o fluxo de código de autorização. |
| scope |Recomendado |Uma lista de âmbitos separadas por espaços. Indica um valor de âmbito único para o Azure AD ambas as permissões que estão a ser solicitadas. O `openid` âmbito indica uma permissão para iniciar a sessão do utilizador e obter dados sobre o utilizador na forma de id_token parâmetros. Ele pode ser usado para obter os tokens para a web de back-end da aplicação API, que é representada pelo mesmo ID de aplicação que o cliente. O `offline_access` âmbito indica que a aplicação terá um token de atualização de longa duração acesso a recursos. |
| Código |Necessário |O código de autorização que obteve no leg primeiro do fluxo. |
| redirect_uri |Necessário |O `redirect_uri` parâmetro do aplicativo em que recebeu o código de autorização. |
| client_secret |Necessário |O segredo de aplicação que gerou no [portal do Azure](https://portal.azure.com/). Este segredo da aplicação é um artefato de segurança importantes. Deve armazená-lo com segurança no seu servidor. Também deve girar Este segredo de cliente periodicamente. |

Uma resposta com êxito do token é semelhante a:

```
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
| --- | --- |
| not_before |A hora em que o token é considerado válido, na hora "Epoch". |
| token_type |O valor de tipo de token. O único tipo, que é o Azure AD suporta `Bearer`. |
| access_token |O token JWT assinado que pediu. |
| scope |Os âmbitos para a qual o token é válido. Estes podem ser utilizados para colocar em cache tokens para utilização posterior. |
| expires_in |O período de tempo que o token de acesso é válido (em segundos). |
| refresh_token |Um token de atualização de OAuth 2.0. A aplicação pode utilizar este token para adquirir tokens adicionais depois do atual expire. Atualizar tokens são vida longa e pode ser utilizados para manter o acesso aos recursos por longos períodos de tempo. Para obter mais detalhes, consulte a [referência de token de B2C](active-directory-b2c-reference-tokens.md). Tenha em atenção que tem utiliza o âmbito `offline_access` na autorização e pedidos de token para receber um token de atualização. |

Respostas de erro parecida com:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e que podem ser utilizados para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação do desenvolvedor. |

## <a name="use-the-token"></a>Utilizar o token
Agora que adquirir um token de acesso com êxito, pode utilizar o token em pedidos para o back-end web APIs, incluindo-na `Authorization` cabeçalho:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Atualizar o token
Os tokens de ID são de curta duração. Tem de atualizá-los depois que ocorra para continuar a capacidade de aceder aos recursos. Pode fazê-lo ao submeter outro `POST` pedido para o `/token` ponto final. Desta vez, forneça o `refresh_token` parâmetro, em vez do `code` parâmetro:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parâmetro | Necessário | Descrição |
| --- | --- | --- |
| p |Necessário |O fluxo de utilizador que foi utilizado para adquirir o token de atualização original. Não é possível utilizar um fluxo de utilizador diferente neste pedido. Tenha em atenção de que adicione este parâmetro para a cadeia de consulta, não ao corpo da mensagem. |
| client_id |Necessário |ID de aplicação que o [portal do Azure](https://portal.azure.com/) atribuído à sua aplicação. |
| grant_type |Necessário |O tipo de concessão, que tem de ser um token de atualização para esta vertente do fluxo de código de autorização. |
| scope |Recomendado |Uma lista de âmbitos separadas por espaços. Indica um valor de âmbito único para o Azure AD ambas as permissões que estão a ser solicitadas. O `openid` âmbito indica uma permissão para iniciar a sessão do utilizador e obter dados sobre o utilizador na forma de tokens de ID. Ele pode ser usado para obter os tokens para a web de back-end da aplicação API, que é representada pelo mesmo ID de aplicação que o cliente. O `offline_access` âmbito indica que a aplicação terá um token de atualização de longa duração acesso a recursos. |
| redirect_uri |Recomendado |O `redirect_uri` parâmetro do aplicativo em que recebeu o código de autorização. |
| refresh_token |Necessário |O token de atualização original que obteve no segundo leg do fluxo. Tenha em atenção que tem utiliza o âmbito `offline_access` na autorização e pedidos de token para receber um token de atualização. |
| client_secret |Necessário |O segredo de aplicação que gerou no [portal do Azure](https://portal.azure.com/). Este segredo da aplicação é um artefato de segurança importantes. Deve armazená-lo com segurança no seu servidor. Também deve girar Este segredo de cliente periodicamente. |

Uma resposta com êxito do token é semelhante a:

```
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
| --- | --- |
| not_before |A hora em que o token é considerado válido, na hora "Epoch". |
| token_type |O valor de tipo de token. O único tipo, que é o Azure AD suporta `Bearer`. |
| access_token |O token JWT assinado que pediu. |
| scope |O âmbito que o token for válido, que pode ser utilizado para colocar em cache tokens para utilização posterior. |
| expires_in |O período de tempo que o token de acesso é válido (em segundos). |
| refresh_token |Um token de atualização de OAuth 2.0. A aplicação pode utilizar este token para adquirir tokens adicionais depois do atual expire.  Atualizar tokens são vida longa e pode ser utilizados para manter o acesso aos recursos por longos períodos de tempo. Para obter mais detalhes, consulte a [referência de token de B2C](active-directory-b2c-reference-tokens.md). |

Respostas de erro parecida com:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e que podem ser utilizados para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação do desenvolvedor. |

## <a name="send-a-sign-out-request"></a>Enviar um pedido de fim de sessão
Quando deseja iniciar sessão do utilizador fora da aplicação, não é suficiente limpar os cookies da sua aplicação ou do fim; caso contrário, a sessão com o utilizador. Também tem de redirecionar o utilizador para o Azure AD para terminar sessão. Se não conseguir fazê-lo, o utilizador poderá conseguir autenticar-se à sua aplicação sem introduzir as respetivas credenciais novamente. Isso é porque eles terão uma única início de sessão em sessão válida com o Azure AD.

Simplesmente pode redirecionar o utilizador para o `end_session` ponto final que está listado no documento de metadados do OpenID Connect descrito anteriormente no "validar o token de ID" secção:

```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parâmetro | Necessário? | Descrição |
| --- | --- | --- |
| p |Necessário |O fluxo de utilizador que pretende utilizar para iniciar sessão do utilizador fora do seu aplicativo. |
| post_logout_redirect_uri |Recomendado |O URL que o utilizador deve ser redirecionado para depois de concluída com êxito fim de sessão. Se não estiver incluída, o Azure AD B2C mostra ao utilizador uma mensagem genérica. |

> [!NOTE]
> Embora que direciona o utilizador para o `end_session` endpoint limpará alguns do Estado do utilizador único início de sessão com o Azure AD B2C, ele não irá iniciar sessão do utilizador sem a sessão do fornecedor (IDP) de identidade de redes sociais. Se o usuário seleciona do mesmo IDP durante um subsequente início de sessão, eles serão pode ser reautenticados, sem introduzir as respetivas credenciais. Se um usuário deseja terminar sessão do seu aplicativo de B2C, isso não significa necessariamente que pretende terminar sessão sua conta do Facebook. No entanto, no caso de contas locais, a sessão do utilizador será terminada corretamente.
> 
> 

## <a name="use-your-own-b2c-tenant"></a>Utilizar o seu inquilino B2C
Se quiser experimentar essas solicitações para si próprio, tem primeiro de efetuar estes três passos e, em seguida, substitua os valores de exemplo descritos anteriormente, com os seus próprios:

1. [Criar um inquilino B2C](active-directory-b2c-get-started.md)e utilize o nome do seu inquilino nos pedidos.
2. [Criar uma aplicação](active-directory-b2c-app-registration.md) para obter uma ID da aplicação. Inclua uma web aplicação/API web na sua aplicação. Opcionalmente, crie um segredo de aplicação.
3. [Criar os seus fluxos de utilizador](active-directory-b2c-reference-policies.md) para obter nomes de fluxo de seu usuário.

