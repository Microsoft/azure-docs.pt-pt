---
title: Compreender o fluxo de código de autorização de OAuth 2.0 no Azure AD
description: Este artigo descreve como utilizar mensagens HTTP para autorizar o acesso a aplicações web e APIs web no seu inquilino através do Azure Active Directory e o OAuth 2.0.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/5/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2598bb4deef0c7dae9f5df558ec1054ad02fb2f7
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531118"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Autorizar o acesso a aplicações de web do Azure Active Directory utilizando o fluxo de concessão de código do OAuth 2.0

O Azure Active Directory (Azure AD) utiliza o OAuth 2.0 para lhe permitir autorizar o acesso a aplicações web e APIs web no inquilino do Azure AD. Este guia é independente do idioma e descreve como enviar e receber mensagens HTTP sem utilizar qualquer um dos nossos [bibliotecas de código-fonte aberto](active-directory-authentication-libraries.md).

O fluxo de código de autorização de OAuth 2.0 é descrito em [secção 4.1 da especificação de OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1). Ele é usado para realizar a autenticação e autorização na maioria dos tipos de aplicação, incluindo aplicações web e aplicações instaladas nativamente.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)]

## <a name="oauth-20-authorization-flow"></a>Fluxo de autorização de OAuth 2.0

Num alto nível, o fluxo de autorização completa para uma aplicação é um pouco semelhante a isso:

![Fluxo de código de autenticação do OAuth](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Pedir um código de autorização

O fluxo de código de autorização começa com o cliente que direciona o utilizador para o `/authorize` ponto final. Este pedido, o cliente indica as permissões necessárias para adquirir do usuário. Pode obter o ponto final de autorização de OAuth 2.0 para o seu inquilino, selecionando **registos de aplicações > pontos finais** no portal do Azure.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%3A12345
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| inquilino |obrigatório |O `{tenant}` valor no caminho do pedido pode ser utilizado para controlar quem pode iniciar sessão na aplicação. Os valores permitidos são identificadores de inquilino, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para tokens de independente de inquilino |
| client_id |obrigatório |O ID de aplicação atribuída à aplicação quando registou com o Azure AD. Pode encontrar isto no Portal do Azure. Clique em **do Azure Active Directory** na barra lateral serviços, clique em **registos das aplicações**e escolha a aplicação. |
| response_type |obrigatório |Tem de incluir `code` para o fluxo de código de autorização. |
| redirect_uri |Recomendado |O redirect_uri da sua aplicação, onde as respostas podem ser enviadas e recebidas pela sua aplicação. Ele deve corresponder exatamente um dos redirect_uris registado no portal, exceto pelo fato tem de ser codificados de url. Para aplicações de dispositivos móveis e nativas, deve usar o valor predefinido de `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode |opcional |Especifica o método que deve ser utilizado para enviar a cópia de token resultante à sua aplicação. Pode ser `query`, `fragment`, ou `form_post`. `query` Fornece o código como um parâmetro de cadeia de caracteres de consulta no seu URI de redirecionamento. Se estiver solicitando um token de ID com o fluxo implícito, é possível utilizar `query` conforme especificado no [especificação de OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Se estiver solicitando apenas do código, pode usar `query`, `fragment`, ou `form_post`. `form_post` executa uma POSTAGEM que contém o código para o seu URI de redirecionamento. A predefinição é `query` para um fluxo de código.  |
| estado |Recomendado |Um valor incluído no pedido que também é devolvido na resposta de token. Um valor exclusivo gerado aleatoriamente é normalmente utilizado para [impedir ataques de falsificação de solicitação](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar as informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorreu, como a página ou a vista estivessem na. |
| Recurso | Recomendado |O URI de ID de aplicação da API (recurso protegido) da web de destino. Para encontrar o URI de ID de aplicação, no Portal do Azure, clique em **do Azure Active Directory**, clique em **registos de aplicação**, abra a aplicação **definições** página, em seguida, clique em  **Propriedades**. Também pode ser um recurso externo, como `https://graph.microsoft.com`. Isto é necessário em um dos autorização ou pedidos de token. Para garantir autenticação menos prompts colocá-lo no pedido de autorização para se certificar de consentimento é recebido do usuário. |
| âmbito | **ignored** | Para aplicações do Azure AD v1, âmbitos devem ser configurados estaticamente no Portal do Azure em aplicativos **configurações**, **permissões obrigatórias**. |
| linha de comandos |opcional |Indica o tipo de interação do usuário que é necessário.<p> Valores válidos são: <p> *login*: Deve ser pedido ao utilizador para autenticar. <p> *select_account*: É pedido ao utilizador para selecionar uma conta, interromper o início de sessão único no. O utilizador pode selecionar uma conta de início de sessão iniciada existente, introduza as credenciais para uma conta de memorizados ou optar por utilizar uma conta diferente completamente. <p> *consent*: Consentimento do utilizador foram concedido, mas tem de ser atualizado. Deve ser pedido ao utilizador para dar consentimento. <p> *admin_consent*: Um administrador deve ser-lhe pedido para consentir em nome de todos os utilizadores na sua organização |
| login_hint |opcional |Pode ser usada para preencher previamente o campo de endereço de e-mail/nome de utilizador da página início de sessão do utilizador, se souber que o respetivo nome de utilizador antes do tempo. Aplicações, muitas vezes, utilizam este parâmetro durante a reautenticação, já após extrair o nome de utilizador de um anterior início de sessão com o `preferred_username` de afirmação. |
| domain_hint |opcional |Fornece uma dica sobre o inquilino ou domínio que o utilizador deve utilizar para iniciar sessão. O valor da domain_hint é um domínio registado para o inquilino. Se o inquilino está federado para um diretório no local, o AAD redireciona para o servidor de Federação do inquilino especificado. |
| code_challenge_method | Recomendado    | O método utilizado para codificar a `code_verifier` para o `code_challenge` parâmetro. Pode ser um dos `plain` ou `S256`. Se excluído, `code_challenge` é considerado como texto sem formatação se `code_challenge` está incluído. Oferece suporte do Azure AAD v1.0 `plain` e `S256`. Para obter mais informações, consulte a [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | Recomendado    | Utilizado para proteger concessões de código de autorização por meio da chave de prova para código Exchange (PKCE) de um cliente nativo ou público. Se necessário `code_challenge_method` está incluído. Para obter mais informações, consulte a [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Se o utilizador faz parte de uma organização, um administrador da organização pode consentir ou recusar no nome do usuário ou permitir o consentimento ao utilizador. O utilizador é dada a opção para dar consentimento apenas quando o administrador permite que o faça.
>
>

Neste momento, é pedido ao utilizador para introduzir as respetivas credenciais e o consentimento às permissões solicitadas pela aplicação no Portal do Azure. Depois do utilizador autentica e concede o consentimento, do Azure AD envia uma resposta à sua aplicação no `redirect_uri` endereço no seu pedido com o código.

### <a name="successful-response"></a>Resposta com êxito
Uma resposta com êxito poderia ter esta aparência:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parâmetro | Descrição |
| --- | --- |
| admin_consent |O valor for True, se um administrador que deu consentimento para um pedido de solicitação de consentimento. |
| Código |O código de autorização que o aplicativo solicitou. O aplicativo pode usar o código de autorização para pedir um token de acesso para o recurso de destino. |
| session_state |Um valor exclusivo que identifica a sessão do utilizador atual. Este valor é um GUID, mas deve ser tratado como um valor indefinido, que é transmitido sem exame. |
| estado |Se um parâmetro de estado está incluído na solicitação, o mesmo valor deve aparecer na resposta. É uma boa prática para a aplicação verificar se os valores de estado no pedido e resposta são idênticos antes de utilizar a resposta. Isto ajuda a detetar [ataques de falsificação de pedidos entre sites (CSRF)](https://tools.ietf.org/html/rfc6749#section-10.12) contra o cliente. |

### <a name="error-response"></a>Resposta de erro
Respostas de erro também podem ser enviadas para o `redirect_uri` para que o aplicativo pode manipulá-las adequadamente.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Um valor de código de erro definido na seção 5.2 do [Framework de autorização do OAuth 2.0](https://tools.ietf.org/html/rfc6749). A tabela seguinte descreve os códigos de erro que retorna do Azure AD. |
| error_description |Uma descrição mais detalhada do erro. Esta mensagem não se destina a ser amigável de utilizador final. |
| estado |O valor de estado é um valor de não reutilizado gerado aleatoriamente que seja enviado no pedido e devolvido na resposta para impedir ataques do solicitação intersite forjada (CSRF). |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto final de autorização
A tabela seguinte descreve os vários códigos de erro que podem ser devolvidos no `error` parâmetro da resposta de erro.

| Código de Erro | Descrição | Ação de cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um parâmetro necessário em falta. |Corrija e submeta novamente o pedido. Este é um erro de desenvolvimento e normalmente é capturado durante o teste inicial. |
| unauthorized_client |A aplicação de cliente não tem permissão para pedir um código de autorização. |Isso geralmente ocorre quando o aplicativo cliente não está registado no Azure AD ou não for adicionado ao inquilino do Azure AD do utilizador. A aplicação pode pedir ao utilizador com instruções para a instalação do aplicativo e adicioná-lo para o Azure AD. |
| access_denied |Proprietário do recurso negado consentimento |A aplicação cliente pode notificar o utilizador que não é possível continuar a não ser que o utilizador permitir. |
| unsupported_response_type |O servidor de autorização não suporta o tipo de resposta no pedido. |Corrija e submeta novamente o pedido. Este é um erro de desenvolvimento e normalmente é capturado durante o teste inicial. |
| server_error |O servidor encontrou um erro inesperado. |Repita o pedido. Estes erros podem resultar de condições temporárias. A aplicação cliente pode explicar ao usuário que a sua resposta está atrasada devido a um erro temporário. |
| temporarily_unavailable |O servidor está temporariamente demasiado ocupado para processar o pedido. |Repita o pedido. A aplicação cliente pode explicar ao usuário que a sua resposta está atrasada devido a uma condição temporária. |
| invalid_resource |O recurso de destino é inválido porque não existe, do Azure AD não é possível encontrá-lo ou não está corretamente configurado. |Isto indica que o recurso, se existir, não foi configurado no inquilino. A aplicação pode pedir ao utilizador com instruções para a instalação do aplicativo e adicioná-lo para o Azure AD. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Utilize o código de autorização para pedir um token de acesso
Agora que comprou um código de autorização e tenha sido concedida permissão pelo utilizador, podem resgatar o código para um token de acesso para o recurso pretendido, enviando um pedido POST para o `/token` ponto final:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%3A12345
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Parâmetro |  | Descrição |
| --- | --- | --- |
| inquilino |obrigatório |O `{tenant}` valor no caminho do pedido pode ser utilizado para controlar quem pode iniciar sessão na aplicação. Os valores permitidos são identificadores de inquilino, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para tokens de independente de inquilino |
| client_id |obrigatório |O Id de aplicação atribuída à aplicação quando registou com o Azure AD. Pode encontrar isto no portal do Azure. O Id da aplicação é apresentado nas definições de registo de aplicações. |
| grant_type |obrigatório |Tem de ser `authorization_code` para o fluxo de código de autorização. |
| Código |obrigatório |O `authorization_code` que obteve na secção anterior |
| redirect_uri |obrigatório | A `redirect_uri`registado no aplicativo do cliente. |
| client_secret |necessária para as aplicações web, não é permitidas para os clientes públicos |O segredo de aplicação que criou no Portal do Azure para a sua aplicação sob **chaves**. Não pode ser utilizado num aplicativo nativo (cliente público), porque client_secrets não podem ser armazenados com confiança nos dispositivos. É necessário para aplicações web e web APIs (clientes confidenciais tudo), que têm a capacidade de armazenar o `client_secret` com segurança no lado do servidor. O client_secret deve ser codificada de URL antes de serem enviados. |
| Recurso | Recomendado |O URI de ID de aplicação da API (recurso protegido) da web de destino. Para encontrar o URI de ID de aplicação, no Portal do Azure, clique em **do Azure Active Directory**, clique em **registos de aplicação**, abra a aplicação **definições** página, em seguida, clique em  **Propriedades**. Também pode ser um recurso externo, como `https://graph.microsoft.com`. Isto é necessário em um dos autorização ou pedidos de token. Para garantir autenticação menos prompts colocá-lo no pedido de autorização para se certificar de consentimento é recebido do usuário. Se estiver em pedido de autorização e o pedido de token, o recurso "parâmetros têm de corresponder. | 
| code_verifier | opcional | O code_verifier mesmo que foi utilizado para obter o authorization_code. Necessário se PKCE foi utilizada no pedido de concessão do código de autorização. Para obter mais informações, consulte o [PKCE RFC](https://tools.ietf.org/html/rfc7636)   |

Para encontrar o URI de ID de aplicação, no Portal do Azure, clique em **do Azure Active Directory**, clique em **registos de aplicação**, abra a aplicação **definições** página, em seguida, clique em  **Propriedades**.

### <a name="successful-response"></a>Resposta com êxito
O Azure AD devolve um [token de acesso](access-tokens.md) após uma resposta com êxito. Para minimizar as chamadas de rede da aplicação cliente e sua latência associada, o aplicativo cliente deve armazenar em cache tokens de acesso para a duração do token que é especificada na resposta OAuth 2.0. Para determinar a duração do token, utilize o `expires_in` ou `expires_on` valores de parâmetros.

Se um recurso de API web devolve um `invalid_token` código de erro, isto pode indicar que o recurso determinou que o token expirou. Se os tempos do relógio cliente e de recursos diferente (também conhecida como um "distorção de tempo"), o recurso poderá considerar o token expirar antes do token será eliminado da cache do cliente. Se isto ocorrer, desmarque o token da cache, mesmo que esteja ainda dentro do seu ciclo de vida calculado.

Uma resposta com êxito poderia ter esta aparência:

```
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
  "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ."
}

```

| Parâmetro | Descrição |
| --- | --- |
| access_token |O pedido [token de acesso](access-tokens.md) como um assinado JSON Web Token (JWT). A aplicação pode utilizar este token para autenticar para o recurso protegido, como uma API web. |
| token_type |Indica o valor de tipo de token. O único tipo que o Azure AD suporta é portador. Para obter mais informações sobre os tokens de portador, consulte [OAuth2.0 autorização Framework: Utilização de Token de portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |O tempo que o token de acesso é válido (em segundos). |
| expires_on |O tempo que o token de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até a hora de expiração. Este valor é utilizado para determinar o tempo de vida de tokens em cache. |
| Recurso |O URI de ID de aplicação da web API (recurso protegido). |
| âmbito |Permissões de representação concedidas à aplicação cliente. A permissão predefinida é `user_impersonation`. O proprietário do recurso protegido pode registar os valores adicionais no Azure AD. |
| refresh_token |Um token de atualização de OAuth 2.0. A aplicação pode utilizar este token para adquirir os tokens de acesso adicionais depois do token de acesso atual expira. Atualizar tokens são vida longa e pode ser utilizados para manter o acesso aos recursos por longos períodos de tempo. |
| id_token |Um que de JSON Web Token (JWT) não assinados representam um [token de ID](id-tokens.md). Base64Url de podem aplicação decodificar os segmentos deste token solicite informações sobre o utilizador que iniciou sessão. A aplicação pode armazenar em cache os valores e exibi-los, mas não deverá confiar nos mesmos para qualquer autorização ou limites de segurança. |

Para obter mais informações sobre os JSON web tokens, consulte a [especificação do JWT IETF rascunho](https://go.microsoft.com/fwlink/?LinkId=392344).   Para saber mais sobre `id_tokens`, consulte a [v1.0 fluxo de OpenID Connect](v1-protocols-openid-connect-code.md).

### <a name="error-response"></a>Resposta de erro
Os erros de ponto final de emissão de token são códigos de erro HTTP, uma vez que o cliente chama o ponto final de emissão de tokens diretamente. Além do código de estado HTTP, o ponto de final de emissão de token do Azure AD também devolve um documento JSON com objetos que descrevem o erro.

Uma resposta de erro de exemplo poderia ter esta aparência:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação do desenvolvedor. |
| error_codes |Uma lista de códigos de erro do STS específicas que podem ajudar no diagnóstico. |
| carimbo de data/hora |A hora em que ocorreu o erro. |
| trace_id |Um identificador exclusivo para o pedido que pode ajudar no diagnóstico. |
| correlation_id |Um identificador exclusivo para o pedido que pode ajudar no diagnóstico em componentes. |

#### <a name="http-status-codes"></a>Códigos de estado HTTP
A tabela seguinte lista os códigos de estado HTTP que retorna o ponto final de emissão de token. Em alguns casos, o código de erro é suficiente para descrever a resposta, mas se houver erros, tem de analisar o documento JSON e examine seu código de erro.

| Código de HTTP | Descrição |
| --- | --- |
| 400 |Código HTTP padrão. Usado na maioria dos casos e é normalmente devido a um pedido com formato incorreto. Corrija e submeta novamente o pedido. |
| 401 |Falha na autenticação. Por exemplo, o pedido está em falta o parâmetro client_secret. |
| 403 |Falha na autorização. Por exemplo, o utilizador não tem permissão para aceder ao recurso. |
| 500 |Erro interno no serviço. Repita o pedido. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Códigos de erro para erros de ponto final do token
| Código de Erro | Descrição | Ação de cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um parâmetro necessário em falta. |Corrigir e submeta novamente o pedido |
| invalid_grant |O código de autorização é inválido ou expirou. |Experimente um novo pedido para o `/authorize` ponto final |
| unauthorized_client |O cliente autenticado não está autorizado a utilizar este tipo de concessão de autorização. |Isso geralmente ocorre quando o aplicativo cliente não está registado no Azure AD ou não for adicionado ao inquilino do Azure AD do utilizador. A aplicação pode pedir ao utilizador com instruções para a instalação do aplicativo e adicioná-lo para o Azure AD. |
| invalid_client |Falha na autenticação de cliente. |As credenciais de cliente não são válidas. Para corrigir o problema, o administrador do aplicativo atualiza as credenciais. |
| unsupported_grant_type |O servidor de autorização não suporta o tipo de concessão de autorização. |Altere o tipo de concessão no pedido. Este tipo de erro deve ocorrer apenas durante o desenvolvimento e ser detetado durante o teste inicial. |
| invalid_resource |O recurso de destino é inválido porque não existe, do Azure AD não é possível encontrá-lo ou não está corretamente configurado. |Isto indica que o recurso, se existir, não foi configurado no inquilino. A aplicação pode pedir ao utilizador com instruções para a instalação do aplicativo e adicioná-lo para o Azure AD. |
| interaction_required |O pedido requer interação do utilizador. Por exemplo, um passo de autenticação adicional é necessário. | Em vez de um pedido de não-interativa, tente novamente com um pedido de autorização interativo para o mesmo recurso. |
| temporarily_unavailable |O servidor está temporariamente demasiado ocupado para processar o pedido. |Repita o pedido. A aplicação cliente pode explicar ao usuário que a sua resposta está atrasada devido a uma condição temporária. |

## <a name="use-the-access-token-to-access-the-resource"></a>Utilizar o token de acesso para aceder ao recurso
Agora que tive adquirido com sucesso um `access_token`, pode utilizar o token em pedidos para as APIs da Web, incluindo-o no `Authorization` cabeçalho. O [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) especificação explica como utilizar os tokens de portador nos pedidos HTTP para aceder a recursos protegidos.

### <a name="sample-request"></a>Pedido de exemplo
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Resposta de erro
Recursos protegidos que implementam os códigos de estado HTTP do RFC 6750 problema. Se o pedido não inclui as credenciais de autenticação ou está em falta o token, a resposta inclui uma `WWW-Authenticate` cabeçalho. Quando um pedido falhar, o servidor de recursos responde com o código de estado HTTP e um código de erro.

Segue-se um exemplo de uma resposta bem-sucedida quando o pedido de cliente não inclui o token de portador:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Parâmetros do erro
| Parâmetro | Descrição |
| --- | --- |
| authorization_uri |O URI (ponto de extremidade físico) do servidor de autorização. Este valor também é utilizado como uma chave de pesquisa para obter mais informações sobre o servidor de um ponto de final de deteção. <p><p> O cliente deve validar que o servidor de autorização é confiável. Quando o recurso está protegido pelo Azure AD, é suficiente para verificar que o URL começa com https://login.microsoftonline.com ou outro nome de anfitrião que suporte do Azure AD. Um recurso específico de inquilino deve sempre retornar um URI de autorização de inquilino específico. |
| erro |Um valor de código de erro definido na seção 5.2 do [Framework de autorização do OAuth 2.0](https://tools.ietf.org/html/rfc6749). |
| error_description |Uma descrição mais detalhada do erro. Esta mensagem não se destina a ser amigável de utilizador final. |
| resource_id |Devolve o identificador exclusivo do recurso. A aplicação cliente pode utilizar este identificador como o valor do `resource` parâmetro quando ele solicita um token para o recurso. <p><p> É importante para a aplicação de cliente verificar este valor, caso contrário, um serviço mal-intencionado poderá induza uma **elevação de privilégios** ataque <p><p> A estratégia recomendada para impedir a um ataque é verificar se o `resource_id` corresponde à base de URL da API web que está sendo acessado. Por exemplo, se https://service.contoso.com/data está sendo acessado, o `resource_id` pode ser htttps://service.contoso.com/. A aplicação cliente tem rejeitar um `resource_id` que não começa com o URL de base, exceto se houver uma forma alternativa fiável para verificar o id. |

#### <a name="bearer-scheme-error-codes"></a>Códigos de erro do esquema de portador
A especificação RFC 6750 define os seguintes erros para os recursos que utilizam o cabeçalho WWW-Authenticate e o esquema de portador na resposta.

| Código de estado de HTTP | Código de Erro | Descrição | Ação de cliente |
| --- | --- | --- | --- |
| 400 |invalid_request |O pedido não é bem formado. Por exemplo, ela pode ser um parâmetro em falta ou usando o mesmo parâmetro duas vezes. |Corrija o erro e repita o pedido. Este tipo de erro deve ocorrer apenas durante o desenvolvimento e ser detectado no teste inicial. |
| 401 |invalid_token |O token de acesso está em falta, inválido ou foi revogado. O valor do parâmetro error_description fornece detalhes adicionais. |Pedir um novo token do servidor de autorização. Se o novo token falhar, Ocorreu um erro inesperado. Envie uma mensagem de erro para o utilizador e tente novamente após atrasados aleatórios. |
| 403 |insufficient_scope |O token de acesso não contém as permissões de representação necessárias para aceder ao recurso. |Envie um novo pedido de autorização para o ponto final de autorização. Se a resposta contém o parâmetro de âmbito, utilize o valor de âmbito no pedido para o recurso. |
| 403 |insufficient_access |O assunto do token não tem as permissões que são necessárias para aceder ao recurso. |Pedir ao utilizador para utilizar uma conta diferente ou para pedir permissões para o recurso especificado. |

## <a name="refreshing-the-access-tokens"></a>Atualizar os tokens de acesso

Tokens de acesso são de curta duração e tem de ser atualizados depois de esta expira para continuar a aceder a recursos. Pode atualizar o `access_token` enviando outra `POST` pedido para o `/token` ponto final, mas este tempo, fornecer o `refresh_token` em vez do `code`.  Tokens de atualização são válidos para todos os recursos que seu cliente já tenha sido dado consentimento para aceder - dessa forma, um token de atualização emitido um pedido para `resource=https://graph.microsoft.com` pode ser utilizado para pedir um novo token de acesso para `resource=https://contoso.com/api`. 

Atualizar tokens não têm tempo de vida especificado. Normalmente, os tempos de vida de tokens de atualização são relativamente longos. No entanto, em alguns casos, tokens de atualização expirarem, são revogados ou não têm privilégios suficientes para a ação desejada. Seu aplicativo precisa esperar e lidar com erros devolvidos pelo ponto de final de emissão de token corretamente.

Quando receber uma resposta com um erro de token de atualização, eliminar o token de atualização atual e pedir um novo código de autorização ou token de acesso. Em particular, quando utilizar a uma atualização do token no fluxo de concessão de código de autorização, se receber uma resposta com o `interaction_required` ou `invalid_grant` códigos de erro, eliminar o token de atualização e pedir um novo código de autorização.

Um pedido de exemplo para o **específico de inquilino** ponto final (também pode utilizar o **comuns** endpoint) para obter um novo acesso através de um token de atualização de token é semelhante a este:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

### <a name="successful-response"></a>Resposta com êxito
Uma resposta com êxito de token terá o seguinte aspeto:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```
| Parâmetro | Descrição |
| --- | --- |
| token_type |O tipo de token. É o único valor suportado **portador**. |
| expires_in |A duração restante do token em segundos. Um valor de típico é de 3600 (uma hora). |
| expires_on |A data e hora em que o token expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até a hora de expiração. |
| Recurso |Identifica o recurso protegido que o token de acesso pode ser utilizados para aceder. |
| âmbito |Permissões de representação concedidas à aplicação cliente nativa. A permissão de predefinição está **user_impersonation**. O proprietário do recurso de destino pode registar valores alternativos no Azure AD. |
| access_token |Novo token de acesso que foi pedido. |
| refresh_token |Refresh_token OAuth 2.0 nova que pode ser utilizado para pedir novos tokens de acesso quando expira nesta resposta. |

### <a name="error-response"></a>Resposta de erro
Uma resposta de erro de exemplo poderia ter esta aparência:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant. You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Uma cadeia de código de erro que pode ser utilizada para classificar tipos de erros que ocorrem e pode ser utilizada para reagir a erros. |
| error_description |Uma mensagem de erro específicas que pode ajudar a identificar a causa de raiz de um erro de autenticação do desenvolvedor. |
| error_codes |Uma lista de códigos de erro do STS específicas que podem ajudar no diagnóstico. |
| carimbo de data/hora |A hora em que ocorreu o erro. |
| trace_id |Um identificador exclusivo para o pedido que pode ajudar no diagnóstico. |
| correlation_id |Um identificador exclusivo para o pedido que pode ajudar no diagnóstico em componentes. |

Para obter uma descrição dos códigos de erro e a ação de cliente recomendado, consulte [códigos de erro para erros de ponto final do token](#error-codes-for-token-endpoint-errors).
