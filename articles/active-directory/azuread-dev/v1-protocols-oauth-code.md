---
title: Compreender o fluxo de código de autorização OAuth 2.0 em Azure AD
description: Este artigo descreve como usar mensagens HTTP para autorizar o acesso a aplicações web e APIs web no seu inquilino usando o Azure Ative Directory e o OAuth 2.0.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: bc303dc62892f8fac67bb6869e72db0e40f19779
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263546"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Autorizar o acesso a aplicações Web do Azure Active Directory através do fluxo de concessão de código do OAuth 2.0

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

> [!NOTE]
>  Se não disser ao servidor que recurso pretende ligar, o servidor não irá acionar as políticas de Acesso Condicional para esse recurso. Assim, para ter o gatilho do MFA, terá de incluir um recurso no seu URL. 
>

O Azure Ative Directory (Azure AD) utiliza o OAuth 2.0 para permitir autorizar o acesso a aplicações web e APIs web no seu inquilino Azure AD. Este guia é independente de linguagem e descreve como enviar e receber mensagens HTTP sem utilizar nenhuma das [nossas bibliotecas de código aberto.](active-directory-authentication-libraries.md)

O fluxo de código de autorização OAuth 2.0 é descrito na [secção 4.1 da especificação OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1). É utilizado para realizar a autenticação e autorização na maioria dos tipos de aplicações, incluindo aplicações web e aplicações instaladas de forma nativa.

## <a name="register-your-application-with-your-ad-tenant"></a>Registar a aplicação com o inquilino do AD
Em primeiro lugar, registe a sua candidatura junto do seu inquilino azure Ative Directory (Azure AD). Esta ação dar-lhe-á um ID de Aplicação para a aplicação e ativá-lo-á para receber tokens.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
   
1. Escolha o seu inquilino Azure AD selecionando a sua conta no canto superior direito da página, seguido selecionando a navegação do **Diretório switch** e, em seguida, selecionando o inquilino apropriado. 
   - Ignore este passo se tiver apenas um inquilino Azure AD sob a sua conta, ou se já selecionou o inquilino da AD Azure apropriado.
   
1. No portal Azure, procure e selecione **Azure Ative Directory**.
   
1. No menu esquerdo do **Diretório Ativo Azure,** selecione Registos de **Aplicações,** e, em seguida, selecione **Nova inscrição**.
   
1. Siga os avisos e crie uma nova aplicação. Não importa se se trata de uma aplicação web ou de um cliente público (mobile & desktop) para este tutorial, mas se quiser exemplos específicos para aplicações web ou aplicações de clientes públicos, confira os nossos [quickstarts](v1-overview.md).
   
   - **Nome** é o nome da aplicação e descreve a sua aplicação aos utilizadores finais.
   - Nos tipos de **conta suportados,** selecione **Contas em qualquer diretório organizacional e contas pessoais**da Microsoft .
   - Forneça o **Redirect URI**. Para aplicações web, este é o URL base da sua aplicação onde os utilizadores podem iniciar sessão.  Por exemplo, `http://localhost:12345`. Para cliente público (mobile & desktop), a Azure AD usa-o para devolver respostas simbólicas. Insira um valor específico da sua aplicação.  Por exemplo, `http://MyFirstAADApp`.
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. Assim que tiver concluído o registo, a Azure AD atribuirá à sua aplicação um identificador de cliente único (o ID de **aplicação).** Precisa deste valor nas secções seguintes, por isso copie-o a partir da página de candidatura.
   
1. Para encontrar a sua aplicação no portal Azure, selecione **registos**de Aplicações e, em seguida, selecione **Ver todas as aplicações**.

## <a name="oauth-20-authorization-flow"></a>Fluxo de autorização OAuth 2.0

A um nível elevado, todo o fluxo de autorização para um pedido é um pouco assim:

![Fluxo de Código Auth Auth OAuth](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Solicitar um código de autorização

O fluxo de código de autorização começa com o cliente a direcionar o utilizador para o ponto final `/authorize`. Neste pedido, o cliente indica as permissões que necessita para adquirir ao utilizador. Você pode obter o ponto final de autorização OAuth 2.0 para o seu inquilino, selecionando inscrições de **App > Endpoints** no portal Azure.

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
| tenant |Necessário |O valor `{tenant}` no caminho do pedido pode ser usado para controlar quem pode assinar a aplicação. Os valores permitidos são identificadores de inquilinos, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para fichas independentes de inquilinos |
| client_id |Necessário |O ID de aplicação atribuído à sua aplicação quando o registou no Azure AD. Pode encontrar isso no Portal Azure. Clique no **Diretório Ativo Azure** na barra lateral dos **serviços,** clique nos registos da App e escolha a aplicação. |
| response_type |Necessário |Deve incluir `code` para o fluxo do código de autorização. |
| redirect_uri |recomendado |O redirect_uri da sua aplicação, onde as respostas de autenticação podem ser enviadas e recebidas pela sua app. Deve corresponder exatamente a uma das redirect_uris que registou no portal, exceto que deve ser codificada. Para aplicações nativas e móveis, deve utilizar o valor padrão de `https://login.microsoftonline.com/common/oauth2/nativeclient`. |
| response_mode |opcional |Especifica o método que deve ser usado para enviar o símbolo resultante de volta para a sua aplicação. Pode ser `query`, `fragment`ou `form_post`. `query` fornece o código como parâmetro de corda de consulta no seu URI redirecionamento. Se estiver a solicitar um símbolo de identificação utilizando o fluxo implícito, não pode utilizar `query` conforme especificado na [especificação OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Se estiver a pedir apenas o código, pode usar `query`, `fragment`ou `form_post`. `form_post` executa um POST contendo o código para o seu redirecionamento URI. O padrão é `query` para um fluxo de código.  |
| state |recomendado |Um valor incluído no pedido que também é devolvido na resposta simbólica. Um valor único gerado aleatoriamente é normalmente usado para [prevenir ataques de falsificação de pedidos de local.](https://tools.ietf.org/html/rfc6749#section-10.12) O Estado também é utilizado para codificar informações sobre o estado do utilizador na aplicação antes do pedido de autenticação ocorrer, como a página ou visualização em que estavam. |
| resource | recomendado |O ID da aplicação URI da API web-alvo (recurso seguro). Para encontrar o ID da aplicação URI, no Portal Azure, clique no **Diretório Ativo do Azure,** clique nos registos de **aplicação,** abra a página **Definições** da aplicação e, em seguida, clique em **Propriedades**. Pode também ser um recurso externo como `https://graph.microsoft.com`. Isto é exigido num dos pedidos de autorização ou ficha. Para garantir menos pedidos de autenticação coloca-o no pedido de autorização para garantir que o consentimento seja recebido do utilizador. |
| scope | **ignorado** | Para aplicações V1 Azure AD, os âmbitos devem ser configurados estáticamente no Portal Azure sob as **Definições**de aplicações , **Permissões Necessárias**. |
| pronta |opcional |Indique o tipo de interação do utilizador que é necessário.<p> Os valores válidos são: <p> início de *sessão*: O utilizador deve ser solicitado a reautenticar. <p> *select_account:* O utilizador é solicitado a selecionar uma conta, interrompendo o único sinal. O utilizador pode selecionar uma conta de entrada assinada existente, introduzir as suas credenciais para uma conta lembrada ou optar por utilizar uma conta diferente. <p> *consentimento*: O consentimento do utilizador foi concedido, mas precisa de ser atualizado. O utilizador deve ser solicitado a consentir. <p> *admin_consent*: Um administrador deve ser solicitado a consentir em nome de todos os utilizadores da sua organização |
| login_hint |opcional |Pode ser usado para pré-preencher o campo de endereços de utilizador/e-mail da página de sessão para o utilizador, se souber o seu nome de utilizador com antecedência. Muitas vezes as aplicações utilizam este parâmetro durante a reautenticação, tendo já extraído o nome de utilizador de um início de sessão anterior utilizando a `preferred_username` reivindicação. |
| domain_hint |opcional |Fornece uma dica sobre o inquilino ou domínio que o utilizador deve usar para iniciar sessão. O valor do domain_hint é um domínio registado para o inquilino. Se o inquilino for federado para um diretório no local, a AAD redireciona para o servidor da federação de inquilinos especificado. |
| code_challenge_method | recomendado    | O método utilizado para codificar o `code_verifier` para o parâmetro `code_challenge`. Pode ser um dos `plain` ou `S256`. Se for excluído, assume-se `code_challenge` texto simples se `code_challenge` estiver incluído. O Azure AAD v1.0 suporta tanto `plain` como `S256`. Para mais informações, consulte o [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | recomendado    | Usado para garantir subsídios de código de autorização através da Chave Comprovativa para Troca de Códigos (PKCE) de um cliente nativo ou público. Necessário se `code_challenge_method` estiver incluído. Para mais informações, consulte o [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Se o utilizador fizer parte de uma organização, um administrador da organização pode consentir ou recusar em nome do utilizador, ou permitir que o utilizador consinta. O utilizador só tem a opção de consentir quando o administrador o autoriza.
>
>

Neste momento, é solicitado ao utilizador que insira as suas credenciais e consinta nas permissões solicitadas pela app no Portal Do Azure. Assim que o utilizador autentica e concede o consentimento, a Azure AD envia uma resposta à sua aplicação no endereço `redirect_uri` no seu pedido com o código.

### <a name="successful-response"></a>Resposta bem sucedida
Uma resposta bem sucedida pode parecer assim:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parâmetro | Descrição |
| --- | --- |
| admin_consent |O valor é verdadeiro se um administrador consentiu num pedido de consentimento. |
| code |O código de autorização que o pedido solicitou. O pedido pode usar o código de autorização para solicitar um sinal de acesso para o recurso-alvo. |
| session_state |Um valor único que identifica a atual sessão de utilizador. Este valor é um GUID, mas deve ser tratado como um valor opaco que é passado sem exame. |
| state |Se um parâmetro estatal estiver incluído no pedido, o mesmo valor deve figurar na resposta. É uma boa prática para a aplicação verificar que os valores do Estado no pedido e resposta são idênticos antes de usar a resposta. Isto ajuda a detetar ataques de [falsificação de pedido de cross-site (CSRF)](https://tools.ietf.org/html/rfc6749#section-10.12) contra o cliente. |

### <a name="error-response"></a>Resposta de erro
As respostas de erro também podem ser enviadas para o `redirect_uri` para que a aplicação possa manuseá-las adequadamente.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| erro |Um valor de código de erro definido na secção 5.2 do Quadro de [Autorização OAuth 2.0](https://tools.ietf.org/html/rfc6749). A tabela seguinte descreve os códigos de erro que a Azure AD devolve. |
| error_description |Uma descrição mais detalhada do erro. Esta mensagem não se destina a ser fácil de utilizar. |
| state |O valor do Estado é um valor não reutilizado gerado aleatoriamente que é enviado no pedido e devolvido na resposta para evitar ataques de falsificação de pedidos de local transversal (CSRF). |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto final de autorização
A tabela seguinte descreve os vários códigos de erro que podem ser devolvidos no parâmetro `error` da resposta ao erro.

| Código de Erro | Descrição | Ação do Cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um parâmetro em falta necessário. |Corrija e reenvie o pedido. Este é um erro de desenvolvimento, e é tipicamente apanhado durante os testes iniciais. |
| unauthorized_client |O pedido do cliente não está autorizado a solicitar um código de autorização. |Isto ocorre geralmente quando a aplicação do cliente não está registada no Azure AD ou não é adicionada ao inquilino Azure AD do utilizador. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |
| access_denied |Proprietário de recursos negado consentimento |A aplicação do cliente pode notificar o utilizador de que não pode proceder a menos que o utilizador consinta. |
| unsupported_response_type |O servidor de autorização não suporta o tipo de resposta no pedido. |Corrija e reenvie o pedido. Este é um erro de desenvolvimento, e é tipicamente apanhado durante os testes iniciais. |
| server_error |O servidor encontrou um erro inesperado. |Tente o pedido. Estes erros podem resultar de condições temporárias. A aplicação do cliente pode explicar ao utilizador que a sua resposta está atrasada devido a um erro temporário. |
| temporarily_unavailable |O servidor está temporariamente demasiado ocupado para lidar com o pedido. |Tente o pedido. A aplicação do cliente pode explicar ao utilizador que a sua resposta é adiada devido a uma condição temporária. |
| invalid_resource |O recurso-alvo é inválido porque não existe, a AD Azure não o encontra, ou não está corretamente configurado. |Isto indica que o recurso, caso exista, não foi configurado no inquilino. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Utilize o código de autorização para solicitar um sinal de acesso
Agora que adquiriu um código de autorização e foi-lhe concedida autorização pelo utilizador, pode resgatar o código para um sinal de acesso ao recurso pretendido, enviando um pedido de CORREIO para o `/token` ponto final:

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
| tenant |Necessário |O valor `{tenant}` no caminho do pedido pode ser usado para controlar quem pode assinar a aplicação. Os valores permitidos são identificadores de inquilinos, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para fichas independentes de inquilinos |
| client_id |Necessário |O Id de aplicação atribuído à sua aplicação quando o registou no Azure AD. Pode encontrar isso no portal Azure. O Id da Aplicação é apresentado nas definições do registo da aplicação. |
| grant_type |Necessário |Deve ser `authorization_code` para o fluxo do código de autorização. |
| code |Necessário |O `authorization_code` que adquiriu na secção anterior |
| redirect_uri |Necessário | Um `redirect_uri`registado no pedido de cliente. |
| client_secret |necessário para aplicações web, não permitido para clientes públicos |O segredo de aplicação que criou no Portal Azure para a sua aplicação em **Keys**. Não pode ser usado numa aplicação nativa (cliente público), porque client_secrets não pode ser armazenado de forma fiável em dispositivos. É necessário para aplicações web e APIs web (todos os clientes confidenciais), que têm a capacidade de armazenar o `client_secret` de forma segura no lado do servidor. O client_secret deve ser codificado por URL antes de ser enviado. |
| resource | recomendado |O ID da aplicação URI da API web-alvo (recurso seguro). Para encontrar o ID da aplicação URI, no Portal Azure, clique no **Diretório Ativo do Azure,** clique nos registos de **aplicação,** abra a página **Definições** da aplicação e, em seguida, clique em **Propriedades**. Pode também ser um recurso externo como `https://graph.microsoft.com`. Isto é exigido num dos pedidos de autorização ou ficha. Para garantir menos pedidos de autenticação coloca-o no pedido de autorização para garantir que o consentimento seja recebido do utilizador. Se, tanto no pedido de autorização como no pedido de ficha, os parâmetros do recurso devem coincidir. | 
| code_verifier | opcional | O mesmo code_verifier que foi usado para obter o authorization_code. Necessário se o PKCE foi utilizado no pedido de concessão de código de autorização. Para mais informações, consulte o [PKCE RFC](https://tools.ietf.org/html/rfc7636)   |

Para encontrar o ID da aplicação URI, no Portal Azure, clique no **Diretório Ativo do Azure,** clique nos registos de **aplicação,** abra a página **Definições** da aplicação e, em seguida, clique em **Propriedades**.

### <a name="successful-response"></a>Resposta bem sucedida
A Azure AD devolve um sinal de [acesso](../develop/access-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) a uma resposta bem sucedida. Para minimizar as chamadas de rede da aplicação do cliente e a sua latência associada, a aplicação do cliente deve cache acessos de acesso para o período de vida simbólico especificado na resposta OAuth 2.0. Para determinar a vida útil do símbolo, utilize os valores `expires_in` ou `expires_on` parâmetros.

Se um recurso Web API devolver um código de erro `invalid_token`, isto pode indicar que o recurso determinou que o token está expirado. Se os tempos do cliente e do relógio de recursos forem diferentes (conhecidos como "time skew"), o recurso pode considerar que o símbolo deve ser expirado antes de o token ser retirado da cache do cliente. Se isto ocorrer, retire o símbolo da cache, mesmo que ainda esteja dentro do seu tempo de vida calculado.

Uma resposta bem sucedida pode parecer assim:

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
| access_token |O sinal de acesso solicitado.  Esta é uma corda opaca - depende do que o recurso espera receber, e não se destina a que o cliente olhe. A aplicação pode usar este símbolo para autenticar o recurso seguro, como uma API web. |
| token_type |Indica o valor do tipo simbólico. O único tipo que a Azure AD suporta é o Bearer. Para mais informações sobre tokens bearer, consulte [OAuth2.0 Quadro de Autorização: Utilização do Token ao portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Quanto tempo o token de acesso é válido (em segundos). |
| expires_on |O tempo em que o sinal de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até ao tempo de validade. Este valor é usado para determinar a vida útil de fichas em cache. |
| resource |O App ID URI da Web API (recurso seguro). |
| scope |Permissões de personificação concedidas ao pedido do cliente. A permissão padrão é `user_impersonation`. O proprietário do recurso seguro pode registar valores adicionais em Azure AD. |
| refresh_token |Um token refresh OAuth 2.0. A aplicação pode usar este token para adquirir fichas de acesso adicionais após o termo do token de acesso atual. As fichas de atualização são de longa duração, e podem ser usadas para manter o acesso aos recursos por longos períodos de tempo. |
| id_token |Um JSON Web Token (JWT) não assinado que representa um [símbolo de identificação](../develop/id-tokens.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json). A aplicação pode descodificar os segmentos deste token para solicitar informações sobre o utilizador que assinou. A aplicação pode cache os valores e exibi-los, mas não deve confiar neles para qualquer autorização ou limites de segurança. |

Para obter mais informações sobre as fichas web da JSON, consulte a especificação de [projeto JWT IETF](https://go.microsoft.com/fwlink/?LinkId=392344).   Para saber mais sobre `id_tokens`, consulte o [fluxo v1.0 OpenID Connect](v1-protocols-openid-connect-code.md).

### <a name="error-response"></a>Resposta de erro
Os erros finais da emissão de token são códigos de erro HTTP, porque o cliente chama o ponto final da emissão de token diretamente. Além do código de estado HTTP, o ponto final de emissão de fichas Azure AD também devolve um documento JSON com objetos que descrevem o erro.

Uma resposta de erro de amostra pode parecer assim:

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
| erro |Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem, e pode ser usada para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro de autenticação. |
| error_codes |Uma lista de códigos de erro específicos do STS que podem ajudar nos diagnósticos. |
| carimbo de data/hora |O momento em que ocorreu o erro. |
| trace_id |Um identificador único para o pedido que pode ajudar nos diagnósticos. |
| correlation_id |Um identificador único para o pedido que pode ajudar no diagnóstico através de componentes. |

#### <a name="http-status-codes"></a>Códigos de estado HTTP
A tabela que se segue lista os códigos de estado HTTP que o ponto final da emissão do token devolve. Em alguns casos, o código de erro é suficiente para descrever a resposta, mas se houver erros, é necessário analisar o documento JSON que o acompanha e examinar o seu código de erro.

| Código HTTP | Descrição |
| --- | --- |
| 400 |Código HTTP predefinido. Usado na maioria dos casos e é normalmente devido a um pedido mal formado. Corrija e reenvie o pedido. |
| 401 |A autenticação falhou. Por exemplo, o pedido está a faltar ao parâmetro client_secret. |
| 403 |Falha na autorização. Por exemplo, o utilizador não tem permissão para aceder ao recurso. |
| 500 |Ocorreu um erro interno no serviço. Tente o pedido. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Códigos de erro para erros de ponto final simbólicos
| Código de Erro | Descrição | Ação do Cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um parâmetro em falta necessário. |Corrigir e reenviar o pedido |
| invalid_grant |O código de autorização é inválido ou expirou. |Experimente um novo pedido para o ponto final `/authorize` |
| unauthorized_client |O cliente autenticado não está autorizado a utilizar este tipo de concessão de autorização. |Isto ocorre geralmente quando a aplicação do cliente não está registada no Azure AD ou não é adicionada ao inquilino Azure AD do utilizador. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |
| invalid_client |A autenticação do cliente falhou. |As credenciais do cliente não são válidas. Para corrigir, o administrador de aplicação atualiza as credenciais. |
| unsupported_grant_type |O servidor de autorização não suporta o tipo de concessão de autorização. |Alterar o tipo de subvenção no pedido. Este tipo de erro só deve ocorrer durante o desenvolvimento e ser detetado durante os testes iniciais. |
| invalid_resource |O recurso-alvo é inválido porque não existe, a AD Azure não o encontra, ou não está corretamente configurado. |Isto indica que o recurso, caso exista, não foi configurado no inquilino. A aplicação pode solicitar ao utilizador instruções para instalar a aplicação e adicioná-la ao Azure AD. |
| interaction_required |O pedido requer interação do utilizador. Por exemplo, é necessário um passo adicional de autenticação. | Em vez de um pedido não interativo, retente com um pedido de autorização interativo para o mesmo recurso. |
| temporarily_unavailable |O servidor está temporariamente demasiado ocupado para lidar com o pedido. |Tente o pedido. A aplicação do cliente pode explicar ao utilizador que a sua resposta é adiada devido a uma condição temporária. |

## <a name="use-the-access-token-to-access-the-resource"></a>Use o sinal de acesso para aceder ao recurso
Agora que adquiriu com sucesso uma `access_token`, pode usar o símbolo em pedidos de APIs web, incluindo-o no cabeçalho `Authorization`. A especificação [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) explica como utilizar fichas ao portador em pedidos HTTP para aceder a recursos protegidos.

### <a name="sample-request"></a>Pedido de exemplo
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Resposta ao Erro
Recursos seguros que implementam rFC 6750 emitem códigos de estado HTTP. Se o pedido não incluir credenciais de autenticação ou faltar ao símbolo, a resposta inclui um cabeçalho `WWW-Authenticate`. Quando um pedido falha, o servidor de recursos responde com o código de estado HTTP e um código de erro.

Segue-se um exemplo de uma resposta infrutífera quando o pedido do cliente não inclui o token do portador:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Parâmetros de erro
| Parâmetro | Descrição |
| --- | --- |
| authorization_uri |O URI (ponto final físico) do servidor de autorização. Este valor também é usado como uma chave de procura para obter mais informações sobre o servidor a partir de um ponto final de descoberta. <p><p> O cliente deve validar que o servidor de autorização é de confiança. Quando o recurso é protegido por Azure AD, é suficiente verificar se o URL começa com https://login.microsoftonline.com ou outro nome de anfitrião que a AD Azure suporta. Um recurso específico para o inquilino deve sempre devolver uma autorização específica para o arrendatário URI. |
| erro |Um valor de código de erro definido na secção 5.2 do Quadro de [Autorização OAuth 2.0](https://tools.ietf.org/html/rfc6749). |
| error_description |Uma descrição mais detalhada do erro. Esta mensagem não se destina a ser fácil de utilizar. |
| resource_id |Devolve o identificador único do recurso. A aplicação do cliente pode usar este identificador como o valor do parâmetro `resource` quando solicita um símbolo para o recurso. <p><p> É importante que a aplicação do cliente verifique este valor, caso contrário, um serviço malicioso pode ser capaz de induzir um ataque **de elevação de privilégios** <p><p> A estratégia recomendada para prevenir um ataque é verificar se o `resource_id` corresponde à base do URL API web a que está a ser acedido. Por exemplo, se https://service.contoso.com/data estiver a ser acedida, o `resource_id` pode ser https://service.contoso.com/. A aplicação do cliente deve rejeitar uma `resource_id` que não comece com o URL base a menos que exista uma forma alternativa fiável de verificar o id. |

#### <a name="bearer-scheme-error-codes"></a>Códigos de erro do esquema do portador
A especificação RFC 6750 define os seguintes erros para os recursos que utilizam o cabeçalho WWW-Authenticate e o regime Bearer na resposta.

| Código de Estado de HTTP | Código de Erro | Descrição | Ação do Cliente |
| --- | --- | --- | --- |
| 400 |invalid_request |O pedido não está bem formado. Por exemplo, pode estar a faltar um parâmetro ou a utilizar o mesmo parâmetro duas vezes. |Corrija o erro e tente novamente o pedido. Este tipo de erro só deve ocorrer durante o desenvolvimento e ser detetado nos testes iniciais. |
| 401 |invalid_token |O sinal de acesso está em falta, inválido ou é revogado. O valor do parâmetro error_description fornece detalhes adicionais. |Solicite um novo sinal do servidor de autorização. Se o novo símbolo falhar, ocorreu um erro inesperado. Envie uma mensagem de erro ao utilizador e tente novamente após atrasos aleatórios. |
| 403 |insufficient_scope |O sinal de acesso não contém as permissões de imitação necessárias para aceder ao recurso. |Envie um novo pedido de autorização para o ponto final da autorização. Se a resposta contiver o parâmetro de âmbito, utilize o valor de âmbito no pedido ao recurso. |
| 403 |insufficient_access |O objeto do símbolo não tem as permissões necessárias para aceder ao recurso. |Insto o utilizador a utilizar uma conta diferente ou a solicitar permissões ao recurso especificado. |

## <a name="refreshing-the-access-tokens"></a>Refrescar os tokens de acesso

Os Tokens de acesso são de curta duração e devem ser renovados depois de expirarem para continuar a aceder aos recursos. Pode refrescar a `access_token` submetendo outro pedido de `POST` ao `/token` ponto final, mas desta vez fornecendo o `refresh_token` em vez do `code`.  Os tokens de atualização são válidos para todos os recursos a que o seu cliente já recebeu o consentimento para aceder - assim, um token de atualização emitido sobre um pedido de `resource=https://graph.microsoft.com` pode ser usado para solicitar um novo sinal de acesso para `resource=https://contoso.com/api`. 

As fichas de atualização não têm uma vida útil especificada. Tipicamente, as vidas de tokens refrescantes são relativamente longas. No entanto, em alguns casos, as fichas de atualização expiram, são revogadas ou carecem de privilégios suficientes para a ação desejada. A sua aplicação precisa de esperar e lidar com erros devolvidos corretamente pelo ponto final da emissão de fichas.

Quando receber uma resposta com um erro de ficha de atualização, deite fora o token de atualização atual e solicite um novo código de autorização ou ficha de acesso. Em particular, ao utilizar um token de atualização no fluxo de concessão do código de autorização, se receber uma resposta com os códigos de erro `interaction_required` ou `invalid_grant`, deite fora o token de atualização e solicite um novo código de autorização.

Um pedido de amostra para o ponto final específico do **inquilino** (você também pode usar o ponto final **comum)** para obter um novo sinal de acesso usando um token refrescante parece este:

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
| token_type |Do tipo de símbolo. O único valor suportado é **o portador.** |
| expires_in |A vida restante do símbolo em segundos. Um valor típico é de 3600 (uma hora). |
| expires_on |A data e a hora em que o símbolo expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até ao tempo de validade. |
| resource |Identifica o recurso seguro a que o token de acesso pode ser usado para aceder. |
| scope |Permissões de personificação concedidas ao pedido de cliente nativo. A permissão padrão é **user_impersonation.** O proprietário do recurso-alvo pode registar valores alternativos em Azure AD. |
| access_token |O novo sinal de acesso que foi solicitado. |
| refresh_token |Um novo OAuth 2.0 refresh_token que pode ser usado para solicitar novos tokens de acesso quando o desta resposta expirar. |

### <a name="error-response"></a>Resposta de erro
Uma resposta de erro de amostra pode parecer assim:

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
| erro |Uma cadeia de código de erro que pode ser usada para classificar tipos de erros que ocorrem, e pode ser usada para reagir a erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa principal de um erro de autenticação. |
| error_codes |Uma lista de códigos de erro específicos do STS que podem ajudar nos diagnósticos. |
| carimbo de data/hora |O momento em que ocorreu o erro. |
| trace_id |Um identificador único para o pedido que pode ajudar nos diagnósticos. |
| correlation_id |Um identificador único para o pedido que pode ajudar no diagnóstico através de componentes. |

Para obter uma descrição dos códigos de erro e da ação recomendada pelo cliente, consulte os códigos de [erro para erros de ponto final simbólicos](#error-codes-for-token-endpoint-errors).

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o ponto final da AD Azure v1.0 e como adicionar autenticação e autorização às suas aplicações web e APIs web, consulte [aplicações de amostra](sample-v1-code.md).
