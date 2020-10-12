---
title: Compreenda o fluxo de código de autorização OAuth 2.0 em Azure AD
description: Este artigo descreve como usar mensagens HTTP para autorizar o acesso a aplicações web e APIs web no seu inquilino usando Ozure Ative Directory e OAuth 2.0.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 5f987ab15201e4c4dabf147ac468184881e9ed17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85551644"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Autorizar o acesso a aplicações Web do Azure Active Directory através do fluxo de concessão de código do OAuth 2.0

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

> [!NOTE]
>  Se não disser ao servidor que recursos pretende chamar, então o servidor não ativará as políticas de Acesso Condicional para esse recurso. Assim, para ter o gatilho MFA, terá de incluir um recurso no seu URL. 
>

O Azure Active Directory (Azure AD) utiliza o OAuth 2.0 para lhe permitir autorizar o acesso a aplicações Web e a APIs Web no seu inquilino do Azure AD. Este guia é independente da linguagem e descreve como enviar e receber mensagens HTTP sem utilizar nenhuma das [nossas bibliotecas de código aberto.](active-directory-authentication-libraries.md)

O fluxo de código de autorização OAuth 2.0 é descrito na [secção 4.1 da especificação OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1). É utilizado para realizar autenticação e autorização na maioria dos tipos de aplicações, incluindo aplicações web e aplicações instaladas nativamente.

## <a name="register-your-application-with-your-ad-tenant"></a>Registar a aplicação com o inquilino do AD
Em primeiro lugar, registe a sua candidatura junto do seu inquilino Azure Ative Directory (Azure AD). Esta ação dar-lhe-á um ID de Aplicação para a aplicação e ativá-lo-á para receber tokens.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
   
1. Escolha o seu inquilino Azure AD selecionando a sua conta no canto superior direito da página, seguindo-se a seleção da navegação do **Diretório** da Switch e, em seguida, selecionando o inquilino apropriado. 
   - Ignore este passo se tiver apenas um inquilino AZure AD na sua conta, ou se já selecionou o inquilino Azure AD apropriado.
   
1. No portal Azure, procure e selecione **O Diretório Ativo Azure**.
   
1. No menu esquerdo do **Azure Ative,** selecione **Registos de Aplicações**e, em seguida, selecione **Novo registo**.
   
1. Siga as instruções e crie uma nova aplicação. Não importa se é uma aplicação web ou um cliente público (mobile & desktop) para este tutorial, mas se você gostaria de exemplos específicos para aplicações web ou aplicações de clientes públicos, confira os [nossos quickstarts](v1-overview.md).
   
   - **Nome** é o nome da aplicação e descreve a sua aplicação aos utilizadores finais.
   - Nos **tipos de conta suportada**, selecione Contas em qualquer **diretório organizacional e contas pessoais da Microsoft.**
   - Forneça o **URI de redirecionamento**. Para aplicações web, este é o URL base da sua app onde os utilizadores podem iniciar sposição.  Por exemplo, `http://localhost:12345`. Para o cliente público (mobile & desktop), a Azure AD usa-o para devolver respostas simbólicas. Introduza um valor específico na aplicação.  Por exemplo, `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Uma vez concluída a inscrição, a Azure AD atribuirá à sua aplicação um identificador de cliente único (o ID da **aplicação).** Precisa deste valor nas próximas secções, por isso copie-o da página de candidatura.
   
1. Para encontrar a sua aplicação no portal Azure, selecione **registos de Aplicações**e, em seguida, selecione **Ver todas as aplicações**.

## <a name="oauth-20-authorization-flow"></a>Fluxo de autorização OAuth 2.0

A um nível elevado, todo o fluxo de autorização para uma aplicação é um pouco parecido com este:

![Fluxo de código OAuth Auth](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Solicitar um código de autorização

O fluxo de código de autorização começa com o cliente a direcionar o utilizador para o `/authorize` ponto final. Neste pedido, o cliente indica as permissões que necessita de adquirir ao utilizador. Você pode obter o ponto final de autorização OAuth 2.0 para o seu inquilino, selecionando **registos de Aplicações > Endpoints** no portal Azure.

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

| Parâmetro | Tipo | Descrição |
| --- | --- | --- |
| inquilino |obrigatório |O `{tenant}` valor no caminho do pedido pode ser usado para controlar quem pode assinar a aplicação. Os valores permitidos são identificadores de inquilinos, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` `contoso.onmicrosoft.com` ou para `common` tokens independentes do inquilino |
| client_id |obrigatório |O ID da aplicação atribuído à sua app quando a registou no Azure AD. Pode encontrar isto no Portal Azure. Clique no **Azure Ative Directory** na barra lateral dos **serviços,** clique nos registos da App e escolha a aplicação. |
| response_type |obrigatório |Deve incluir `code` para o fluxo de código de autorização. |
| redirect_uri |recomendado |O redirect_uri da sua app, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a uma das redirect_uris que registou no portal, exceto que deve estar codificada url. Para aplicações móveis & nativas, deve utilizar o valor predefinido de `https://login.microsoftonline.com/common/oauth2/nativeclient` . |
| response_mode |opcional |Especifica o método que deve ser usado para enviar o símbolo resultante de volta para a sua aplicação. Pode `query` `fragment` ser, ou `form_post` . . `query` fornece o código como um parâmetro de cadeia de consulta no seu URI de redirecionamento. Se estiver a solicitar um token de identificação utilizando o fluxo implícito, não pode utilizar `query` como especificado na [especificação OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Se está a solicitar apenas o código, pode `query` `fragment` usar, ou `form_post` . `form_post` executa um POST contendo o código para o seu URI de redirecionamento. O padrão é `query` para um fluxo de código.  |
| state |recomendado |Um valor incluído no pedido que também é devolvido na resposta simbólica. Um valor único gerado aleatoriamente é normalmente usado para [prevenir ataques de falsificação de pedidos de trans-locais](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou a vista em que estavam. |
| recurso | recomendado |A App ID URI da API web alvo (recurso seguro). Para encontrar a App ID URI, no Portal Azure, clique no **Diretório Ativo Azure,** clique nas **inscrições da Aplicação,** abra a página **de Definições** da aplicação e, em seguida, clique em **Propriedades**. Também pode ser um recurso externo como `https://graph.microsoft.com` . Isto é exigido em um dos pedidos de autorização ou simbólico. Para garantir menos instruções de autenticação, coloque-o no pedido de autorização para garantir que o consentimento é recebido do utilizador. |
| scope | **ignorado** | Para as aplicações V1 AD Azure, os âmbitos devem ser configurados estáticamente no Portal Azure ao abrigo das **definições**de aplicações , **Permissões Necessárias**. |
| rápido |opcional |Indique o tipo de interação do utilizador que é necessária.<p> Os valores válidos são: <p> *início de sessão*: O utilizador deve ser solicitado a reauthenticar. <p> *select_account*: O utilizador é solicitado a selecionar uma conta, interrompendo um único sinal. O utilizador pode selecionar uma conta inscrita existente, introduzir as suas credenciais para uma conta lembrada ou optar por utilizar uma conta diferente. <p> *consentimento*: O consentimento do utilizador foi concedido, mas precisa de ser atualizado. O utilizador deve ser solicitado a consentir. <p> *admin_consent*: Um administrador deve ser solicitado a consentir em nome de todos os utilizadores da sua organização |
| login_hint |opcional |Pode ser usado para pré-preenchimento do nome de utilizador/endereço de endereço de e-mail da página de inscrição para o utilizador, se souber o seu nome de utilizador com antecedência. Muitas vezes as aplicações usam este parâmetro durante a reautornação, tendo já extraído o nome de utilizador de um pré-in usando a `preferred_username` alegação. |
| domain_hint |opcional |Fornece uma pista sobre o inquilino ou domínio que o utilizador deve usar para iniciar sinsutar. O valor do domain_hint é um domínio registado para o arrendatário. Se o inquilino for federado para um diretório no local, a AAD redireciona para o servidor da federação de inquilinos especificado. |
| code_challenge_method | recomendado    | O método usado para codificar o `code_verifier` `code_challenge` parâmetro. Pode ser um dos `plain` `S256` ou. Se excluído, `code_challenge` presume-se que é texto simples se `code_challenge` estiver incluído. Azure AAD v1.0 suporta ambos `plain` e `S256` . Para mais informações, consulte o [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | recomendado    | Utilizado para garantir concessões de código de autorização através da Chave de Prova para Troca de Código (PKCE) de um cliente nativo ou público. Necessário se `code_challenge_method` estiver incluído. Para mais informações, consulte o [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Se o utilizador fizer parte de uma organização, um administrador da organização pode consentir ou recusar em nome do utilizador, ou permitir que o utilizador consinta. O utilizador só tem a opção de consentir quando o administrador o permitir.
>
>

Neste momento, o utilizador é solicitado a introduzir as suas credenciais e consentir com as permissões solicitadas pela app no Portal Azure. Assim que o utilizador autentica e concede o consentimento, a Azure AD envia uma resposta à sua aplicação no `redirect_uri` endereço no seu pedido com o código.

### <a name="successful-response"></a>Resposta bem sucedida
Uma resposta bem sucedida pode ser assim:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parâmetro | Descrição |
| --- | --- |
| admin_consent |O valor é verdadeiro se um administrador consentir com um pedido de consentimento. |
| code |O código de autorização que o pedido solicitou. A aplicação pode usar o código de autorização para solicitar um sinal de acesso para o recurso-alvo. |
| session_state |Um valor único que identifica a sessão de utilizador atual. Este valor é um GUID, mas deve ser tratado como um valor opaco que é passado sem exame. |
| state |Se um parâmetro de estado for incluído no pedido, o mesmo valor deve aparecer na resposta. É uma boa prática para o pedido verificar que os valores do Estado no pedido e resposta são idênticos antes de usar a resposta. Isto ajuda a detetar [ataques de falsificação de pedidos de cross-site (CSRF)](https://tools.ietf.org/html/rfc6749#section-10.12) contra o cliente. |

### <a name="error-response"></a>Resposta de erro
As respostas de erro também podem ser enviadas para `redirect_uri` o modo de a aplicação poder manuseá-las adequadamente.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Um valor de código de erro definido na secção 5.2 do Quadro de [Autorização OAuth 2.0](https://tools.ietf.org/html/rfc6749). A tabela seguinte descreve os códigos de erro que o Azure AD devolve. |
| error_description |Uma descrição mais detalhada do erro. Esta mensagem não se destina a ser amigável para o utilizador final. |
| state |O valor do Estado é um valor não reutilizado gerado aleatoriamente que é enviado no pedido e devolvido na resposta para evitar ataques de falsificação de pedidos de trans-locais (CSRF). |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto final de autorização
A tabela seguinte descreve os vários códigos de erro que podem ser devolvidos no `error` parâmetro da resposta de erro.

| Código de Erro | Descrição | Ação do Cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um parâmetro exigido em falta. |Corrija e reenvia o pedido. Este é um erro de desenvolvimento, e é tipicamente apanhado durante os testes iniciais. |
| unauthorized_client |O pedido do cliente não está autorizado a solicitar um código de autorização. |Isto ocorre geralmente quando a aplicação do cliente não está registada no Azure AD ou não é adicionada ao inquilino AZure AD do utilizador. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |
| access_denied |Proprietário de recursos negado consentimento |A aplicação do cliente pode notificar o utilizador de que não pode prosseguir a menos que o utilizador consinta. |
| unsupported_response_type |O servidor de autorização não suporta o tipo de resposta no pedido. |Corrija e reenvia o pedido. Este é um erro de desenvolvimento, e é tipicamente apanhado durante os testes iniciais. |
| server_error |O servidor encontrou um erro inesperado. |Recandidutar o pedido. Estes erros podem resultar de condições temporárias. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada devido a um erro temporário. |
| temporarily_unavailable |O servidor está temporariamente demasiado ocupado para lidar com o pedido. |Recandidutar o pedido. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada devido a uma condição temporária. |
| invalid_resource |O recurso-alvo é inválido porque não existe, a Azure AD não pode encontrá-lo, ou não está corretamente configurado. |Isto indica que o recurso, se existir, não foi configurado no arrendatário. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Use o código de autorização para solicitar um token de acesso
Agora que adquiriu um código de autorização e foi-lhe concedida autorização pelo utilizador, pode resgatar o código para um símbolo de acesso ao recurso pretendido, enviando um pedido de CORREIO para o `/token` ponto final:

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

| Parâmetro | Tipo | Descrição |
| --- | --- | --- |
| inquilino |obrigatório |O `{tenant}` valor no caminho do pedido pode ser usado para controlar quem pode assinar a aplicação. Os valores permitidos são identificadores de inquilinos, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` `contoso.onmicrosoft.com` ou para `common` tokens independentes do inquilino |
| client_id |obrigatório |O ID de aplicação atribuído à sua app quando a registou no Azure AD. Pode encontrar isto no portal Azure. O Id da aplicação é apresentado nas definições do registo da aplicação. |
| grant_type |obrigatório |Deve ser `authorization_code` para o fluxo de código de autorização. |
| code |obrigatório |O `authorization_code` que adquiriu na secção anterior |
| redirect_uri |obrigatório | Um `redirect_uri` registo no pedido de cliente. |
| client_secret |necessário para aplicações web, não permitido para clientes públicos |O segredo da aplicação que criou no Portal Azure para a sua aplicação em **Chaves.** Não pode ser usado numa aplicação nativa (cliente público), porque client_secrets não podem ser armazenados de forma fiável em dispositivos. É necessário para aplicações web e APIs web (todos os clientes confidenciais), que têm a capacidade de armazenar o `client_secret` securely no lado do servidor. O client_secret deve ser codificado por URL antes de ser enviado. |
| recurso | recomendado |A App ID URI da API web alvo (recurso seguro). Para encontrar a App ID URI, no Portal Azure, clique no **Diretório Ativo Azure,** clique nas **inscrições da Aplicação,** abra a página **de Definições** da aplicação e, em seguida, clique em **Propriedades**. Também pode ser um recurso externo como `https://graph.microsoft.com` . Isto é exigido em um dos pedidos de autorização ou simbólico. Para garantir menos instruções de autenticação, coloque-o no pedido de autorização para garantir que o consentimento é recebido do utilizador. Se, tanto no pedido de autorização como no pedido simbólico, os parâmetros do recurso devem coincidir. | 
| code_verifier | opcional | O mesmo code_verifier que foi usado para obter o authorization_code. Exigido se o PKCE foi utilizado no pedido de concessão de código de autorização. Para mais informações, consulte o [PKCE RFC](https://tools.ietf.org/html/rfc7636)   |

Para encontrar a App ID URI, no Portal Azure, clique no **Diretório Ativo Azure,** clique nas **inscrições da Aplicação,** abra a página **de Definições** da aplicação e, em seguida, clique em **Propriedades**.

### <a name="successful-response"></a>Resposta bem sucedida
A Azure AD devolve um [token de acesso após](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) uma resposta bem sucedida. Para minimizar as chamadas de rede da aplicação do cliente e da sua latência associada, a aplicação do cliente deve cache de tokens de acesso para o token lifetime que é especificado na resposta OAuth 2.0. Para determinar o tempo de vida útil do símbolo, utilize os `expires_in` valores ou `expires_on` parâmetros.

Se um recurso da API web retornar um `invalid_token` código de erro, isto pode indicar que o recurso determinou que o token está expirado. Se os tempos do relógio do cliente e do relógio de recursos forem diferentes (conhecidos como "distorção do tempo"), o recurso pode considerar que o token deve ser expirado antes de o token ser retirado da cache do cliente. Se isto ocorrer, limpe o símbolo da cache, mesmo que ainda esteja dentro do seu tempo de vida calculado.

Uma resposta bem sucedida pode ser assim:

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
| access_token |O sinal de acesso solicitado.  Esta é uma corda opaca - depende do que o recurso espera receber, e não se destina a olhar para o cliente. A aplicação pode usar este token para autenticar o recurso seguro, como uma API web. |
| token_type |Indica o valor do tipo símbolo. O único tipo que a Azure AD suporta é o Portador. Para obter mais informações sobre fichas do Portador, consulte [o Quadro de Autorização OAuth2.0: Utilização de token ao portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Quanto tempo o token de acesso é válido (em segundos). |
| expires_on |O tempo em que o sinal de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0Z UTC até ao prazo de validade. Este valor é usado para determinar a vida útil de fichas em cache. |
| recurso |A App ID URI da API web (recurso seguro). |
| scope |Permissões de personificação concedidas ao pedido do cliente. A permissão por defeito é `user_impersonation` . O proprietário do recurso seguro pode registar valores adicionais em Azure AD. |
| refresh_token |Um token de atualização OAuth 2.0. A aplicação pode usar este token para adquirir fichas de acesso adicionais após o fim do token de acesso atual. Os tokens de atualização são de longa duração, e podem ser usados para manter o acesso aos recursos por longos períodos de tempo. |
| id_token |Um Token Web JSON (JWT) não assinado que representa um [símbolo de identificação.](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) A aplicação pode basear64Url descodificar os segmentos deste token para solicitar informações sobre o utilizador que se inscreveu. A aplicação pode cache os valores e exibi-los, mas não deve confiar neles para qualquer autorização ou limites de segurança. |

Para obter mais informações sobre fichas web JSON, consulte o [projeto de especificação JWT IETF](https://go.microsoft.com/fwlink/?LinkId=392344).   Para saber mais `id_tokens` sobre , consulte o fluxo [v1.0 OpenID Connect](v1-protocols-openid-connect-code.md).

### <a name="error-response"></a>Resposta de erro
Os erros do ponto final de emissão de fichas são códigos de erro HTTP, porque o cliente chama diretamente o ponto final de emissão de token. Além do código de estado HTTP, o ponto final de emissão de ad Ad Azure também devolve um documento JSON com objetos que descrevem o erro.

Uma resposta de erro de amostra pode ser assim:

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
| erro |Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usado para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro de autenticação. |
| error_codes |Uma lista de códigos de erro específicos do STS que podem ajudar no diagnóstico. |
| carimbo de data/hora |A hora em que ocorreu o erro. |
| trace_id |Um identificador único para o pedido que pode ajudar no diagnóstico. |
| correlation_id |Um identificador único para o pedido que pode ajudar no diagnóstico em todos os componentes. |

#### <a name="http-status-codes"></a>Códigos de estado HTTP
A tabela que se segue lista os códigos de estado HTTP que o ponto de terminação da emissão de token devolve. Em alguns casos, o código de erro é suficiente para descrever a resposta, mas se houver erros, é necessário analisar o documento JSON que o acompanha e examinar o seu código de erro.

| Código HTTP | Descrição |
| --- | --- |
| 400 |Código HTTP predefinido. Usado na maioria dos casos e é normalmente devido a um pedido mal formado. Corrija e reenvia o pedido. |
| 401 |Falha na autenticação. Por exemplo, o pedido está faltando o parâmetro client_secret. |
| 403 |A autorização falhou. Por exemplo, o utilizador não tem permissão para aceder ao recurso. |
| 500 |Ocorreu um erro interno no serviço. Recandidutar o pedido. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Códigos de erro para erros de ponto final simbólicos
| Código de Erro | Descrição | Ação do Cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um parâmetro exigido em falta. |Corrigir e reenviar o pedido |
| invalid_grant |O código de autorização é inválido ou caducou. |Tente um novo pedido para o `/authorize` ponto final |
| unauthorized_client |O cliente autenticado não está autorizado a utilizar este tipo de concessão de autorização. |Isto ocorre geralmente quando a aplicação do cliente não está registada no Azure AD ou não é adicionada ao inquilino AZure AD do utilizador. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |
| invalid_client |A autenticação do cliente falhou. |As credenciais do cliente não são válidas. Para corrigir, o administrador de aplicação atualiza as credenciais. |
| unsupported_grant_type |O servidor de autorização não suporta o tipo de concessão de autorização. |Altere o tipo de subvenção no pedido. Este tipo de erro só deve ocorrer durante o desenvolvimento e ser detetado durante os testes iniciais. |
| invalid_resource |O recurso-alvo é inválido porque não existe, a Azure AD não pode encontrá-lo, ou não está corretamente configurado. |Isto indica que o recurso, se existir, não foi configurado no arrendatário. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |
| interaction_required |O pedido requer interação do utilizador. Por exemplo, é necessário um passo adicional de autenticação. | Em vez de um pedido não interativo, recandidutar-se com um pedido de autorização interativo para o mesmo recurso. |
| temporarily_unavailable |O servidor está temporariamente demasiado ocupado para lidar com o pedido. |Recandidutar o pedido. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada devido a uma condição temporária. |

## <a name="use-the-access-token-to-access-the-resource"></a>Use o token de acesso para aceder ao recurso
Agora que adquiriu com sucesso um `access_token` , pode usar o token em pedidos para APIs web, incluindo-o no `Authorization` cabeçalho. A especificação [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) explica como usar fichas portadoras em pedidos HTTP para aceder a recursos protegidos.

### <a name="sample-request"></a>Pedido de amostra
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Resposta a Erros
Recursos seguros que implementam rfc 6750 emitem códigos de estado HTTP. Se o pedido não incluir credenciais de autenticação ou faltar o sinal, a resposta inclui um `WWW-Authenticate` cabeçalho. Quando um pedido falha, o servidor de recursos responde com o código de estado HTTP e um código de erro.

Segue-se um exemplo de uma resposta infrutífera quando o pedido do cliente não inclui o símbolo do portador:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Parâmetros de erro
| Parâmetro | Descrição |
| --- | --- |
| authorization_uri |O URI (ponto final físico) do servidor de autorização. Este valor também é usado como uma chave de procura para obter mais informações sobre o servidor a partir de um ponto final de descoberta. <p><p> O cliente deve validar que o servidor de autorização é confiável. Quando o recurso é protegido pelo Azure AD, é suficiente verificar se o URL começa com `https://login.microsoftonline.com` ou outro nome de anfitrião que a Azure AD suporta. Um recurso específico do arrendatário deve sempre devolver uma autorização específica do arrendatário URI. |
| erro |Um valor de código de erro definido na secção 5.2 do Quadro de [Autorização OAuth 2.0](https://tools.ietf.org/html/rfc6749). |
| error_description |Uma descrição mais detalhada do erro. Esta mensagem não se destina a ser amigável para o utilizador final. |
| resource_id |Devolve o identificador único do recurso. A aplicação do cliente pode usar este identificador como o valor do `resource` parâmetro quando solicita um sinal para o recurso. <p><p> É importante que a aplicação do cliente verifique este valor, caso contrário um serviço malicioso pode induzir um ataque **de elevação de privilégios** <p><p> A estratégia recomendada para prevenir um ataque é verificar se a base do URL da API web que está `resource_id` a ser acedido. Por exemplo, se `https://service.contoso.com/data` estiver a ser acedido, `resource_id` pode ser `https://service.contoso.com/` . A aplicação do cliente deve rejeitar um `resource_id` que não comece com o URL base, a menos que haja uma forma alternativa fiável de verificar o id. |

#### <a name="bearer-scheme-error-codes"></a>Códigos de erro do esquema do portador
A especificação RFC 6750 define os seguintes erros para os recursos que utilizam o WWW-Authenticate cabeçalho e o regime do Portador na resposta.

| Código de Estado HTTP | Código de Erro | Descrição | Ação do Cliente |
| --- | --- | --- | --- |
| 400 |invalid_request |O pedido não está bem formado. Por exemplo, pode estar a perder um parâmetro ou a usar o mesmo parâmetro duas vezes. |Corrija o erro e redaça o pedido. Este tipo de erro só deve ocorrer durante o desenvolvimento e ser detetado nos testes iniciais. |
| 401 |invalid_token |O sinal de acesso está em falta, inválido ou revogado. O valor do parâmetro error_description fornece detalhes adicionais. |Solicite um novo sinal do servidor de autorização. Se o novo símbolo falhar, ocorreu um erro inesperado. Envie uma mensagem de erro ao utilizador e redaça após atrasos aleatórios. |
| 403 |insufficient_scope |O token de acesso não contém as permissões de personificação necessárias para aceder ao recurso. |Envie um novo pedido de autorização para o ponto final de autorização. Se a resposta contiver o parâmetro de âmbito, utilize o valor de âmbito no pedido ao recurso. |
| 403 |insufficient_access |O objeto do token não tem as permissões necessárias para aceder ao recurso. |Ins solicite ao utilizador que utilize uma conta diferente ou que solicite permissões ao recurso especificado. |

## <a name="refreshing-the-access-tokens"></a>Refrescar os tokens de acesso

Os Tokens de acesso são de curta duração e devem ser refrescados depois de expirarem para continuarem a aceder aos recursos. Pode refrescar o `access_token` pedido submetendo outro `POST` pedido ao `/token` ponto final, mas desta vez fornecendo o `refresh_token` em vez do `code` .  Os tokens de atualização são válidos para todos os recursos a que o seu cliente já recebeu o consentimento para aceder - assim, um token de atualização emitido num pedido `resource=https://graph.microsoft.com` de pedido pode ser usado para solicitar um novo token de acesso para `resource=https://contoso.com/api` . 

Os tokens de atualização não têm vidas de vida especificadas. Tipicamente, as vidas de fichas de atualização são relativamente longas. No entanto, em alguns casos, os tokens de atualização expiram, são revogados, ou carecem de privilégios suficientes para a ação desejada. A sua aplicação precisa de esperar e lidar com erros devolvidos pelo ponto final de emissão de token corretamente.

Quando receber uma resposta com um erro de token de atualização, deite fora o token atual da atualização e solicite um novo código de autorização ou ficha de acesso. Em particular, ao utilizar um token de atualização no fluxo de concessão do código de autorização, se receber uma resposta com os `interaction_required` códigos ou `invalid_grant` códigos de erro, deite fora o token de atualização e solicite um novo código de autorização.

Um pedido de amostra para o ponto final específico do **inquilino** (você também pode usar o ponto final **comum)** para obter um novo token de acesso usando um token de atualização é assim:

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

### <a name="successful-response"></a>Resposta bem sucedida
Uma resposta simbólica bem sucedida será como:

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
| token_type |O tipo simbólico. O único valor suportado é **o portador.** |
| expires_in |A vida restante do símbolo em segundos. Um valor típico é 3600 (uma hora). |
| expires_on |A data e a hora em que o símbolo expira. A data é representada como o número de segundos de 1970-01-01T0:0Z UTC até ao prazo de validade. |
| recurso |Identifica o recurso seguro a que o token de acesso pode ser usado para aceder. |
| scope |Permissões de personificação concedidas ao pedido de cliente nativo. A permissão por defeito é **user_impersonation**. O proprietário do recurso-alvo pode registar valores alternativos em Azure AD. |
| access_token |O novo sinal de acesso que foi solicitado. |
| refresh_token |Um novo OAuth 2.0 refresh_token que pode ser usado para solicitar novos tokens de acesso quando o desta resposta expirar. |

### <a name="error-response"></a>Resposta de erro
Uma resposta de erro de amostra pode ser assim:

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
| erro |Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem e pode ser usado para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro de autenticação. |
| error_codes |Uma lista de códigos de erro específicos do STS que podem ajudar no diagnóstico. |
| carimbo de data/hora |A hora em que ocorreu o erro. |
| trace_id |Um identificador único para o pedido que pode ajudar no diagnóstico. |
| correlation_id |Um identificador único para o pedido que pode ajudar no diagnóstico em todos os componentes. |

Para obter uma descrição dos códigos de erro e da ação recomendada do cliente, consulte [códigos de erro para erros de ponto final simbólicos](#error-codes-for-token-endpoint-errors).

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o ponto final Azure AD v1.0 e como adicionar autenticação e autorização às suas aplicações web e APIs web, consulte [as aplicações da amostra](sample-v1-code.md).
