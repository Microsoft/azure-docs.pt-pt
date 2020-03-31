---
title: Recursos do modelo azure API Management / Microsoft Docs
description: Conheça os tipos de recursos disponíveis para uso em modelos de portal de desenvolvimento na Gestão da API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: c3b6123c63bf530463379a175745ef86baf2c5a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249506"
---
# <a name="azure-api-management-template-resources"></a>Recursos do modelo de gestão da API Azure
A Azure API Management fornece os seguintes tipos de recursos para utilização nos modelos do portal de desenvolvimento.  
  
-   [Recursos de cadeia](#strings)  
  
-   [Recursos do glifo](#glyphs)  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="string-resources"></a><a name="strings"></a>Recursos de cadeia  
 A API Management fornece um conjunto abrangente de recursos de cadeia para uso no portal de desenvolvimento. Estes recursos estão localizados em todas as línguas apoiadas pela API Management. O conjunto padrão de modelos utiliza estes recursos para cabeçalhos de página, etiquetas e quaisquer cordas constantes que são exibidas no portal do desenvolvedor. Para utilizar um recurso de cadeia nos seus modelos, forneça o prefixo de cadeia de recursos seguido pelo nome da corda, como mostra o seguinte exemplo.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 O exemplo seguinte é do modelo da lista de produtos, e exibe **produtos** no topo da página.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
As seguintes opções de localização são apoiadas:

| Região    | Idioma               |
|-----------|------------------------|
| "en"      | "Inglês"              |
| "cs"      | "Čeština"              |
| "de"      | "Deutsch"              |
| "es"      | "Español"              |
| "fr"      | "Français"             |
| "hu"      | "Magyar"               |
| "it"      | "Italiano"             |
| "ja-JP"   | ""                |
| "ko"      | "                |
| "nl"      | "Nederlands"           |
| "pl"      | "Polski"               |
| "pt-br"   | "Português (Brasil)"   |
| "pt-pt"   | "Português (Portugal)" |
| "ru"      | "Русский"              |
| "sv"      | "Svenska"              |
| "tr"      | "Türkçe"               |
| "zh-hans" | "A)           |
| "zh-hant" | "A)           |

 Consulte as tabelas seguintes para os recursos de cadeia disponíveis para utilização nos modelos do portal do desenvolvedor. Use o nome da mesa como prefixo para os recursos de cordas nessa tabela.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [Recursos Comuns](#CommonResources)  
  
-   [Cadeias Comuns](#CommonStrings)  
  
-   [Documentação](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [ProblemasStrings](#IssuesStrings)  
  
-   [Não FoundStrings](#NotFoundStrings)  
  
-   [Detalhes do produtoStrings](#ProductDetailsStrings)  
  
-   [ProdutosStrings](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [SigninStrings](#SigninStrings)  
  
-   [InscriçõesStrings](#SignupStrings)  
  
-   [SubscriçãoListStrings](#SubscriptionListStrings)  
  
-   [Strings de subscrição](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="apisstrings"></a><a name="ApisStrings"></a>ApisStrings  
  
|Nome|Texto|  
|----------|----------|  
|PageTitleApis|APIs|  
  
###  <a name="appdetailsstrings"></a><a name="AppDetailsStrings"></a>AppDetailsStrings  
  
|Nome|Texto|  
|----------|----------|  
|WebApplicationsDetailsTitle|Pré-visualização de candidaturas|  
|WebApplicationsRequirementsHeader|Requisitos|  
|WebApplicationsScreenshotAlt|Captura de ecrã|  
|WebApplicationsScreenshotsHeader|Capturas de Ecrã|  
  
###  <a name="applicationliststrings"></a><a name="ApplicationListStrings"></a>ApplicationListStrings  
  
|Nome|Texto|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Tem certeza de que quer remover a inscrição?|  
|WebDevelopersAppNotPublished|Não publicado|  
|WebDevelopersAppNotSubmitted|Não submetido|  
|WebDevelopersAppTableCategoryHeader|Categoria|  
|WebDevelopersAppTableNameHeader|Nome|  
|WebDevelopersAppTableStateHeader|Estado|  
|WebDevelopersEditLink|Editar|  
|WebDevelopersRegisterAppLink|Registar aplicação|  
|WebDevelopersRemoveLink|Remover|  
|WebDevelopersSubmitLink|Submeter|  
|WebDevelopersYourApplicationsHeader|As suas aplicações|  
  
###  <a name="appstrings"></a><a name="AppStrings"></a>AppStrings  
  
|Nome|Texto|  
|----------|----------|  
|WebApplicationsHeader|Aplicações|  
  
###  <a name="commonresources"></a><a name="CommonResources"></a>Recursos Comuns  
  
|Nome|Texto|  
|----------|----------|  
|NoItemsToDisplay|Não foram encontrados resultados.|  
|Mensagem de Exceção Geral|Algo não está certo. Pode ser uma falha temporária ou um inseto. Por favor, tente de novo.|  
|GeneralJsonExceptionMessage|Algo não está certo. Pode ser uma falha temporária ou um inseto. Por favor, recarregue a página e tente de novo.|  
|Mensagem de confirmaçãoNão salvos|Há algumas mudanças não guardadas. Tem a certeza de que pretende cancelar e descartar as alterações?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|Http Request Body muito grande.|  
  
###  <a name="commonstrings"></a><a name="CommonStrings"></a>Cadeias Comuns  
  
|Nome|Texto|  
|----------|----------|  
|ButtonLabelCancel|Cancelar|  
|ButtonLabelSave|Guardar|  
|Mensagem de Exceção Geral|Algo não está certo. Pode ser uma falha temporária ou um inseto. Por favor, tente de novo.|  
|NoItemsToDisplay|Não há itens para exibir.|  
|PagerButtonLabelFirst|Primeiro|  
|PagerButtonLabelLast|Último|  
|PagerButtonLabelNext|Seguinte|  
|PagerButtonLabelAnterior|Prev|  
|PagerLabelPageNOfM|Página {0} de {1}|  
|PasswordTooShort|A palavra-passe é muito curta|  
|EmailAsPassword|Não utilize o seu e-mail como senha|  
|PasswordSameAsUserName|A sua palavra-passe não pode conter o seu nome de utilizador|  
|PasswordTwoCharacterClasses|Use diferentes classes de personagens|  
|PasswordTooManyRepetitions|Demasiadas repetições|  
|PasswordSequenceFound|A sua palavra-passe contém sequências|  
|PagerLabelPageSize|Tamanho da página|  
|CurtainLabelLoading|A carregar...|  
|TablePlacePlaceNothingToDisplay|Não existem dados para o período e âmbito selecionados|  
|ButtonLabelClose|Fechar|  
  
###  <a name="documentation"></a><a name="Documentation"></a>Documentação  
  
|Nome|Texto|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Cabeçalho inválido ' '{0}|  
|Mensagem de erro de pedido de pedido de documentos na Web|URL de pedido inválido|  
|TextboxLabelAccessToken|Ficha de acesso *|  
|DropdownOptionPrimaryKeyFormat|Primária...{0}|  
|DropdownOptionSecondaryKeyFormat|Secundário...{0}|  
|Texto-chave de subscrição de documentação web|A sua chave de subscrição|  
|Modelos de documentação webAddHeaders|Adicione os cabeçalhos HTTP necessários|  
|Modelos de documentação webBasicAuthSample|Amostra de autorização básica|  
|Modelos de documentação webCurlForBasicAuth|para a utilização de Autorização Básica: -utilizador {username}:{password}|  
|Modelos de documentação webCurlValuesForPath|Especifique valores para parâmetros de percurso (mostrados como {...}), a sua chave de subscrição e valores para parâmetros de consulta|  
|WebDocumentationTemplatesDeveloperKey|Especifique a sua chave de subscrição|  
|Modelos de documentação webJavaApache|Esta amostra utiliza o cliente Apache HTTP da HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)|  
|Modelos de documentação webOpcionalParams|Especificar valores para parâmetros opcionais, conforme necessário|  
|Pacote de Modelos de Documentação WebPhpPackage|Esta amostra utiliza o pacote HTTP_Request2. (para mais informações:https://pear.php.net/package/HTTP_Request2)|  
|Modelos de documentação webPythonValuesForPath|Especifique valores para parâmetros de trajetória (mostrados como {...}) e solicite o corpo se necessário|  
|WebDocumentationTemplatesRequestBody|Especificar corpo de pedido|  
|Modelos de Documentação Web Necessários|Especificar valores para os seguintes parâmetros necessários|  
|Modelos de documentação webValuesForPath|Especificar valores para parâmetros de caminho (mostrados como {...})|  
|OAuth2AuthorizationEndpointDescription|O ponto final de autorização é utilizado para interagir com o proprietário do recurso e obter uma concessão de autorização.|  
|OAuth2AutorizaEndpointName|Ponto final de autorização|  
|OAuth2TokenEndpointDescription|O ponto final simbólico é utilizado pelo cliente para obter um sinal de acesso apresentando a sua concessão de autorização ou ficha de atualização.|  
|OAuth2TokenEndpointName|Ponto final de token|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|<\> p O cliente inicia o fluxo direcionando o utilizador-agente do proprietário do recurso para o ponto final da autorização.  O cliente inclui o seu identificador de cliente, âmbito solicitado, estado local e um URI de reorientação para o qual o servidor de autorização enviará o agente-utilizador de volta assim que o acesso for concedido (ou negado).     </p\> <\> p O servidor de autorização autentica o proprietário do recurso (através do agente-utilizador) e estabelece se o proprietário do recurso concede ou nega o pedido de acesso do cliente.     </p\> <\> p Assumindo que o proprietário do recurso concede acesso, o servidor de autorização redireciona o utilizador-agente de volta para o cliente utilizando a reorientação URI fornecida anteriormente (no pedido ou durante o registo do cliente).  A reorientação URI inclui um código de autorização e qualquer estado local fornecido pelo cliente anteriormente.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|<\> p Se o utilizador negar o pedido de acesso de se o pedido for inválido, o cliente será informado utilizando os seguintes parâmetros adicionados ao redirecionamento: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Pedido de autorização|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|<\> p A aplicação cliente deve enviar o utilizador para o ponto final da autorização para iniciar o processo OAuth.          No ponto final da autorização, o utilizador autentica e, em seguida, concede ou nega o acesso à app.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|<\> p Assumindo que o proprietário do recurso concede acesso, o servidor de autorização redireciona o utilizador-agente de volta para o cliente utilizando a reorientação URI fornecida anteriormente (no pedido ou durante o registo do cliente).  A reorientação URI inclui um código de autorização e qualquer estado local fornecido pelo cliente anteriormente. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|<\> p O cliente solicita um sinal de acesso do ponto final do servidor de autorização, incluindo o código de autorização recebido na etapa anterior.  Ao fazer o pedido, o cliente autentica com o servidor de autorização.  O cliente inclui a reorientação URI utilizada para obter o código de autorização para verificação. </p\> <\> p O servidor de autorização autentica o cliente, valida o código de autorização e garante que a reorientação uri recebida corresponde ao URI utilizado para redirecionar o cliente em passo (C).  Se válido, o servidor de autorização responde com um token de acesso e, opcionalmente, um token de atualização. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|<\> p Se a autenticação do cliente solicitado falhou ou for inválida, o servidor de autorização responde com um código de estado HTTP 400 (Pedido De Pedido Mau) (salvo especificação em contrário) e inclui os seguintes parâmetros com a resposta. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|<\> p O cliente faz um pedido ao ponto final simbólico enviando os seguintes parâmetros utilizando o formato "application/x-www-form-urlencoded" com uma codificação de caracteres de UTF-8 no organismo de solicitação HTTP. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|<\> p O servidor de autorização emite um token de acesso e atualização opcional, e constrói a resposta adicionando os seguintes parâmetros ao corpo de entidade da resposta HTTP com um código de estado de 200 (OK). </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|<\> p O cliente autentica com o servidor de autorização e solicita um sinal de acesso a partir do ponto final simbólico. </p\> <\> p O servidor de autorização autentica o cliente e, se válido, emite um token de acesso. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|<\> p Se o pedido falhou na autenticação do cliente ou se for inválido, o servidor de autorização responde com um código de estado HTTP 400 (Pedido De Pedido Mau) (salvo especificação em contrário) e inclui os seguintes parâmetros com a resposta. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|<\> p O cliente faz um pedido ao ponto final simbólico adicionando os seguintes parâmetros utilizando o formato "application/x-www-form-urlencoded" com uma codificação de caracteres de UTF-8 no organismo de solicitação HTTP. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|<\> p Se o pedido de acesso for válido e autorizado, o servidor de autorização emite um token de acesso e atualização opcional, e constrói a resposta adicionando os seguintes parâmetros ao corpo de entidade da resposta HTTP com um código de estado de 200 (OK). </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|<\> p O cliente inicia o fluxo direcionando o utilizador-agente do proprietário do recurso para o ponto final da autorização.  O cliente inclui o seu identificador de cliente, âmbito solicitado, estado local e um URI de reorientação para o qual o servidor de autorização enviará o agente-utilizador de volta assim que o acesso for concedido (ou negado). </p\> <\> p O servidor de autorização autentica o proprietário do recurso (através do agente-utilizador) e estabelece se o proprietário do recurso concede ou nega o pedido de acesso do cliente. </p\> <\> p Assumindo que o proprietário do recurso concede acesso, o servidor de autorização redireciona o utilizador-agente de volta para o cliente utilizando a reorientação URI fornecida anteriormente.  A reorientação URI inclui o símbolo de acesso no fragmento uri. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|<\> p Se o proprietário do recurso negar o pedido de acesso ou se o pedido falhar por razões que não sejam um URI de reorientação em falta ou inválido, o servidor de autorização informa o cliente adicionando os seguintes parâmetros ao componente fragmento do redirecionamento URI utilizando o formato "aplicação/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|<\> p A aplicação cliente deve enviar o utilizador para o ponto final da autorização para iniciar o processo OAuth.      No ponto final da autorização, o utilizador autentica e, em seguida, concede ou nega o acesso à app. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|<\> p Se o proprietário do recurso conceder o pedido de acesso, o servidor de autorização emite um token de acesso e entrega-o ao cliente adicionando os seguintes parâmetros ao componente fragmento do uri de reorientação utilizando o formato "application/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|O fluxo de código de autorização é otimizado para clientes capazes de manter a confidencialidade das suas credenciais (por exemplo, aplicações de servidorweb implementadas usando PHP, Java, Python, Ruby, ASP.NET, etc.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Concessão de Código de Autorização|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|O fluxo de credenciais de cliente é adequado nos casos em que o cliente (a sua aplicação) está a solicitar o acesso aos recursos protegidos sob o seu controlo. O cliente é considerado proprietário de recursos, pelo que não é necessária qualquer interação com o utilizador final.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Concessão de Credenciais de Cliente|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|O fluxo implícito é otimizado para clientes incapazes de manter a confidencialidade das suas credenciais conhecidas por operarem uma determinada reorientação URI. Estes clientes são normalmente implementados num navegador usando um idioma de scriptcomo o JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Subvenção implícita|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|O fluxo de credenciais de senha do proprietário de recursos é adequado nos casos em que o proprietário do recurso tem uma relação de confiança com o cliente (a sua aplicação), como o sistema operativo do dispositivo ou uma aplicação altamente privilegiada. Este fluxo é adequado para clientes capazes de obter as credenciais do proprietário do recurso (nome de utilizador e palavra-passe, normalmente utilizando um formulário interativo).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Bolsa de credenciais de senha do proprietário de recursos|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|<\> p O proprietário do recurso fornece ao cliente o seu nome de utilizador e senha. </p\> <\> p O cliente solicita um sinal de acesso do ponto final do servidor de autorização, incluindo as credenciais recebidas do proprietário do recurso.  Ao fazer o pedido, o cliente autentica com o servidor de autorização. </p\> <\> p O servidor de autorização autentica o cliente e valida as credenciais do proprietário do recurso e, se válido, emite um token de acesso. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|<\> p Se o pedido falhou na autenticação do cliente ou se for inválido, o servidor de autorização responde com um código de estado HTTP 400 (Pedido De Pedido Mau) (salvo especificação em contrário) e inclui os seguintes parâmetros com a resposta. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|<\> p O cliente faz um pedido ao ponto final simbólico adicionando os seguintes parâmetros utilizando o formato "application/x-www-form-urlencoded" com uma codificação de caracteres de UTF-8 no organismo de solicitação HTTP. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|<\> p Se o pedido de acesso for válido e autorizado, o servidor de autorização emite um token de acesso e atualização opcional, e constrói a resposta adicionando os seguintes parâmetros ao corpo de entidade da resposta HTTP com um código de estado de 200 (OK). </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Pedido de ficha de acesso|  
|OAuth2Step_AuthorizationRequest_Name|Pedido de autorização|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|Necessário. O sinal de acesso emitido pelo servidor de autorização.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|Necessário. O sinal de acesso emitido pelo servidor de autorização.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|Necessário. O sinal de acesso emitido pelo servidor de autorização.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Necessário. O sinal de acesso emitido pelo servidor de autorização.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|Necessário. Identificador de cliente.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|EXIGIDO se o cliente não estiver autenticando com o servidor de autorização.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|Necessário. O identificador de cliente.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|Necessário. O código de autorização gerado pelo servidor de autorização.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|Necessário. O código de autorização recebido do servidor de autorização.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|OPCIONAL. Texto ASCII legível pelo homem fornecendo informações adicionais.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|OPCIONAL. Texto ASCII legível pelo homem fornecendo informações adicionais.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|OPCIONAL. Texto ASCII legível pelo homem fornecendo informações adicionais.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|OPCIONAL. Texto ASCII legível pelo homem fornecendo informações adicionais.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OPCIONAL. Texto ASCII legível pelo homem fornecendo informações adicionais.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|OPCIONAL. Um URI identificando uma página web legível pelo homem com informações sobre o erro.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|OPCIONAL. Um URI identificando uma página web legível pelo homem com informações sobre o erro.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|OPCIONAL. Um URI identificando uma página web legível pelo homem com informações sobre o erro.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|OPCIONAL. Um URI identificando uma página web legível pelo homem com informações sobre o erro.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OPCIONAL. Um URI identificando uma página web legível pelo homem com informações sobre o erro.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|Necessário. Um único código de erro ASCII do seguinte: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|Necessário. Um único código de erro ASCII do seguinte: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|Necessário. Um único código de erro ASCII do seguinte: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|Necessário. Um único código de erro ASCII do seguinte: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Necessário. Um único código de erro ASCII do seguinte: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|RECOMENDADO. A vida em segundos do sinal de acesso.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|RECOMENDADO. A vida em segundos do sinal de acesso.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|RECOMENDADO. A vida em segundos do sinal de acesso.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|RECOMENDADO. A vida em segundos do sinal de acesso.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|Necessário. O valor DEVE ser definido para "authorization_code".|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|Necessário. O valor DEVE ser definido para "client_credentials".|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Necessário. O valor DEVE ser definido para "password".|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Necessário. A senha do dono do recurso.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|OPCIONAL. O ponto final de reorientação URI deve ser um URI absoluto.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|Exigido se o parâmetro "redirect_uri" foi incluído no pedido de autorização, e os seus valores DEVEM ser idênticos.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|OPCIONAL. O ponto final de reorientação URI deve ser um URI absoluto.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|OPCIONAL. O token de atualização, que pode ser usado para obter novos tokens de acesso.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|OPCIONAL. O token de atualização, que pode ser usado para obter novos tokens de acesso.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPCIONAL. O token de atualização, que pode ser usado para obter novos tokens de acesso.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|Necessário. O valor DEVE ser definido para "código".|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|Necessário. O valor DEVE ser definido como "símbolo".|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|OPCIONAL. O âmbito do pedido de acesso.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|OPCIONAL se idêntico ao âmbito solicitado pelo cliente; caso contrário, exigido.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|OPCIONAL. O âmbito do pedido de acesso.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|OPCIONAL, se idêntico ao âmbito solicitado pelo cliente; caso contrário, exigido.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|OPCIONAL. O âmbito do pedido de acesso.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|OPCIONAL se idêntico ao âmbito solicitado pelo cliente; caso contrário, exigido.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|OPCIONAL. O âmbito do pedido de acesso.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPCIONAL, se idêntico ao âmbito solicitado pelo cliente; caso contrário, exigido.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|EXIGIDO se o parâmetro "estado" estivesse presente no pedido de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|RECOMENDADO. Um valor opaco usado pelo cliente para manter o estado entre o pedido e o backback.  O servidor de autorização inclui este valor ao redirecionar o agente utilizador de volta para o cliente.  O parâmetro deve ser utilizado para evitar falsificação de pedido de local.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|EXIGIDO se o parâmetro "estado" estivesse presente no pedido de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|EXIGIDO se o parâmetro "estado" estivesse presente no pedido de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|RECOMENDADO. Um valor opaco usado pelo cliente para manter o estado entre o pedido e o backback.  O servidor de autorização inclui este valor ao redirecionar o agente utilizador de volta para o cliente.  O parâmetro deve ser utilizado para evitar falsificação de pedido de local.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|EXIGIDO se o parâmetro "estado" estivesse presente no pedido de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|Necessário. O tipo do símbolo emitido.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|Necessário. O tipo do símbolo emitido.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|Necessário. O tipo do símbolo emitido.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Necessário. O tipo do símbolo emitido.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Necessário. O nome de utilizador do proprietário do recurso.|  
|OAuth2Não suportadoTokenType|O tipo{0}token ' ' não é suportado.|  
|OAuth2Invalidstate|Resposta inválida do servidor de autorização|  
|OAuth2GrantType_AuthorizationCode|Código de autorização|  
|OAuth2GrantType_Implicit|Implícito|  
|OAuth2GrantType_ClientCredentials|Credenciais de cliente|  
|OAuth2GrantType_ResourceOwnerPassword|Senha do proprietário de recursos|  
|WebDocumentation302Code|302 Encontrado|  
|WebDocumentation400Code|400 (Mau pedido)|  
|OAuth2SendingMethod_AuthHeader|Cabeçalho de autorização|  
|OAuth2SendingMethod_QueryParam|Parâmetro de consulta|  
|OAuth2AuthorizationServerGeneralException|Ocorreu um erro ao autorizar o acesso via{0}|  
|OAuth2AuthorizationServerCommunicationException|Não foi possível estabelecer uma ligação HTTP ao servidor de autorização ou foi inesperadamente encerrada.|  
|WebDocumentationOAuth2GeneralErrorMessage|Ocorreu um erro inesperado.|  
|Exceção de comunicações do servidor de autorizações|A exceção à comunicação do servidor de autorização aconteceu. Por favor contacte o administrador.|  
|Descrição do cabeçalho da chave de subscrição do bloco de texto|Chave de subscrição que dá acesso a esta API. Encontrado na sua <um\>perfil de href='/desenvolvedor'</a\>.|  
|Descrição do Cabeçalho do TextblockOAuth|Ficha de acesso OAuth 2.0\> {0} obtida a\>partir de <i</i . Tipos de subvenção\> {1} suportados:\><i</i .|  
|Descrição do ContentTypeHeaderdo do Bloco de Texto|Tipo de mídia do corpo enviado para a API.|  
|ErrorMessageApiNotAcessível|A API que está a tentar ligar não está acessível neste momento. Contacte a editora da API <um\>href="/issues" aqui</a\>.|  
|ErrorMessageApiTimedout|A API que está a tentar ligar está a demorar mais do que o normal para recuperar a resposta. Contacte a editora da API <um\>href="/issues" aqui</a\>.|  
|Mau RequestRequestParameter Esperado|"Espera-se parâmetro"{0}|  
|Dica TextDoubleClickToSelectAll|Clique duas vezes para selecionar tudo.|  
|DicaTextHideRevealSecret|Show/Hide|  
|ButtonLinkOpenConsole|Experimente|  
|Órgão de Pedido de SecçãoHeading|Corpo do pedido|  
|Parâmetros de pedidos de direção de secção|Parâmetros de pedido|  
|Url de Pedido de Secção|URL do Pedido|  
|Resposta de SecçãoHeading|Resposta|  
|Cabeçalhos de Pedido de Secção|Cabeçalhos do pedido|  
|FormLabelSubtextOptional|opcional|  
|SeccionandoCodeSamples|Exemplos de código|  
|TextoBlockOpenidConnectHeaderDescription|Ficha de idi de ligação\> {0} aberta\>obtida a partir de <i</i . Tipos de subvenção\> {1} suportados:\><i</i .|  
  
###  <a name="errorpagestrings"></a><a name="ErrorPageStrings"></a>ErrorPageStrings  
  
|Nome|Texto|  
|----------|----------|  
|LinkLabelBack|anterior|  
|Página de LinkLabelHomePage|página inicial|  
|LinkLabelSendUsEmail|Envie-nos um e-mail|  
|Erro de página|Desculpe, houve um problema ao servir a página solicitada.|  
|TextblockPotentialCauseIntermitenteIssue|Este pode ser um problema intermitente de acesso a dados que já se foi.|  
|BlockblockPotentialCauseOldLink|O link em que clicou pode ser antigo e já não apontar para a localização correta.|  
|Problema técnico do bloco de textoPotentialCause|Pode haver um problema técnico do nosso lado.|  
|Bloqueio de textoPotencialOluçãoRefresh|Experimente atualizar a página.​|  
|BlockblockPotentialSolutionStartOver|Comece de {0}novo a partir do nosso.|  
|Bloco de textoPotencialOluçãoTryNovamente|Vai {0} e tenta a ação que fizeste outra vez.|  
|Problema do Relatório de Texto|{0}descrevendo o que correu mal e vamos vê-lo o mais rápido possível.|  
|TítuloPotencialCausa|Causa potencial|  
|TitlePotentialSolution|É possivelmente apenas uma questão temporária, algumas coisas para tentar|  
  
###  <a name="issuesstrings"></a><a name="IssuesStrings"></a>ProblemasStrings  
  
|Nome|Texto|  
|----------|----------|  
|WebIssuesIndexTitle|Problemas|  
|WebIssuesNoActiveSubscriptions|Não tem subscrições ativas. Precisa subscrever um produto para reportar um problema.|  
|WebIssuesNotSignin|Não está inscrito. Por {0} favor, informe um problema ou publique um comentário.|  
|WebIssuesReportIssueButton|Número de relatório|  
|WebIssuesSignIn|iniciar sessão|  
|WebIssuesStatusReportedBy|Estado: {0} &#124; reportado por{1}|  
  
###  <a name="notfoundstrings"></a><a name="NotFoundStrings"></a>Não FoundStrings  
  
|Nome|Texto|  
|----------|----------|  
|Página de LinkLabelHomePage|página inicial|  
|LinkLabelSendUsEmail|enviar-nos um e-mail|  
|PáginaTítuloNão Encontrado|Desculpe, não conseguimos encontrar a página que procura.|  
|TextblockPotentialCauseMisspelledUrl|Pode ter escrito mal o URL se o digitou.|  
|BlockblockPotentialCauseOldLink|O link em que clicou pode ser antigo e já não apontar para a localização correta.|  
|TextoblockPotentialSolutionRetype|Tente reescrever o URL.|  
|BlockblockPotentialSolutionStartOver|Comece de {0}novo a partir do nosso.|  
|Problema do Relatório de Texto|{0}descrevendo o que correu mal e vamos vê-lo o mais rápido possível.|  
|TítuloPotencialCausa|Causa potencial|  
|TitlePotentialSolution|Solução potencial|  
  
###  <a name="productdetailsstrings"></a><a name="ProductDetailsStrings"></a>Detalhes do produtoStrings  
  
|Nome|Texto|  
|----------|----------|  
|Acordo de Produtos Web|Ao subscrever {0} o Produto, concordo `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`com o .|  
|WebProductsLegalTermsLink|Termos de Utilização|  
|WebProductsSubscribeButton|Subscrever|  
|WebProductsUsageLimitsHeader|Limites de utilização|  
|WebProductsYouAreNotSubscribed|Está subscrito a este produto.|  
|WebProductsYouRequestedSubscription|Solicitou a subscrição deste produto.|  
|ErrorYouNeedto agreewith legalterms|Deve concordar com os Termos de Utilização antes de poder prosseguir.|  
|Subscrição de ButtonLabelAdd|Adicionar subscrição|  
|LinkLabelChangeNome de subscrição|alterar|  
|ButtonLabelConfirm|Confirmar|  
|TextblockMultipleSubscriptionsCount|Tem {0} subscrições deste produto:|  
|Inscrição Uninom|Tem {0} subscrição deste produto:|  
|BlocksingleApisCount|Este produto {0} contém API:|  
|BlockMultipleApisCount|Este produto {0} contém APIs:|  
|Cabeçalho de bloco de texto|Subscreva o produto|  
|Descrição da subscrição do bloco de texto|Será criada uma nova subscrição da seguinte forma:|  
|Subscrição de bloco de textoLimiteS|Limite de assinaturas atingido.|  
  
###  <a name="productsstrings"></a><a name="ProductsStrings"></a>ProdutosStrings  
  
|Nome|Texto|  
|----------|----------|  
|PageTitleProducts|Produtos|  
  
###  <a name="providerinfostrings"></a><a name="ProviderInfoStrings"></a>ProviderInfoStrings  
  
|Nome|Texto|  
|----------|----------|  
|TextboxExternalIdentidadesDeficiente|O inserção é desativado pelos administradores no momento.|  
|TextboxExternalIdentitiesSigninInvitation|Alternativamente, inscreva-se com|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Inscreva-se com:|  
  
###  <a name="signinresources"></a><a name="SigninResources"></a>SigninResources  
  
|Nome|Texto|  
|----------|----------|  
|PrincipalNão Encontrado|O principal não é encontrado ou a assinatura é inválida|  
|ErroSsoAutenticaçãoFalhou|A autenticação SSO falhou|  
|ErrorSsoAuthenticationFailedDetailed|Não é possível verificar o token inválido fornecido ou a assinatura.|  
|ErrorSsoTokenInvalid|Ficha SSO é inválida|  
|Erro de validaçãoEmailJá existe|E-mail '{0}' já registado|  
|ValidaçãoErrorSpecificEmailInvalid|E-mail '{0}é inválido|  
|ValidaçãoErrorPasswordInvalid|A palavra-passe é inválida. Por favor, corrija os erros e tente novamente.|  
|PropertyTooShort|{0}é muito curto|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Endereço de e-mail inválido.|  
|ValidaçãoMensagemNewPasswordConfirmationRequired|Confirmar nova senha|  
|ValidaçãoErrorPasswordConfirmationRequired|Confirmar que a palavra-passe está vazia|  
|WebAuthenticationEmailChangeNotice|Alterar o e-mail {0}de confirmação está a caminho de . Siga as instruções no seu interior para confirmar o seu novo endereço de e-mail. Se o e-mail não chegar à sua caixa de entrada nos próximos minutos, verifique a sua pasta de e-mail sucata.|  
|WebAuthenticationEmailChangeNoticeHeader|O seu pedido de mudança de e-mail foi processado com sucesso|  
|WebAuthenticationEmailChangeNoticeTitle|Alteração de e-mail solicitada|  
|WebAuthenticationEmailHasBeenRevertedNotice|O seu e-mail já existe. Pedido foi revertido|  
|Erro de validaçãoEmail Já existe|E-mail já existe|  
|ValidaçãoErrorEmailInvalid|Endereço de e-mail inválido|  
|TextboxLabelEmail|Email|  
|ValidaçãoErrorEmailRequired|O e-mail é obrigatório.|  
|WebAuthenticationErrorNoticeHeader|Erro|  
|Mensagem de erro do campo de autenticação web|{0}deve ser um comprimento máximo de{1}|  
|TextboxLabelEmailFirstName|Nome próprio|  
|ValidaçãoErrorFirstNameRequired|O primeiro nome é necessário.|  
|ValidaçãoErrorFirstNameInvalid|Primeiro nome inválido|  
|AvisoInvalidInvitationToken|Por favor, note que os links de confirmação são válidos por apenas 48 horas. Se ainda estiver dentro deste prazo, certifique-se de que o seu link está correto. Se o seu link expirou, por favor repita a ação que está a tentar confirmar.|  
|NoticeHeaderInvalidInvitationToken|Ficha de convite inválido|  
|AvisoTitleInvalidInvitationToken|Erro de confirmação|  
|WebAuthenticationLastNameInvalidErrorMessage|Apelido inválido|  
|TextboxLabelEmailLastName|Apelido|  
|ValidaçãoErrorLastNameRequired|É necessário o último nome.|  
|WebAuthenticationLinkExpiredNotice|O link de confirmação enviado para si expirou. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|O link de reset da sua palavra-passe é inválido ou expirado.|  
|WebAuthenticationLinkExpiradNoticeTitle|Link enviado|  
|WebAuthenticationNewPasswordLabel|Nova palavra-passe|  
|ValidaçãoMensagemNova Palavra-passe Necessária|É necessária uma nova senha.|  
|TextboxLabelNotificationsSenderEmail|E-mail de remetente de notificações|  
|Nome da Organização do Rótulo de Texto|Nome da organização|  
|WebAuthenticationOrganizationRequiredErrorMessage|O nome da organização está vazio|  
|WebAuthenticationPasswordChangedNotice|A sua senha foi atualizada com sucesso|  
|WebAuthenticationPasswordChangedNoticeTitle|Senha atualizada|  
|WebAuthenticationPasswordCompareErrorMessage|As palavras-passe não combinam|  
|WebAuthenticationPasswordConfirmLabel|Confirmar palavra-passe|  
|ValidaçãoErrorPasswordInvalidd|A palavra-passe é muito fraca.|  
|Rótulo de senha de autenticação web|Palavra-passe|  
|ValidaçãoErrorPasswordRequired|É necessária uma senha.|  
|WebAuthenticationPasswordResetEnviarAviso|Alterar o email de confirmação da palavra-passe está a caminho de {0}. Siga as instruções dentro do e-mail para continuar o seu processo de alteração de palavra-passe.|  
|WebAuthenticationPasswordResetEnviarNoticeHeader|O seu pedido de reset de palavra-passe foi processado com sucesso|  
|WebAuthenticationPasswordResetEnviarTítulo|Reset de palavra-passe solicitado|  
|WebAuthenticationRequestNotFoundNotice|Pedido não encontrado|  
|WebAuthenticationSenderEmailRequiredErrorMessage|Notificações de e-mail remetente está vazia|  
|WebAuthenticationSigninPasswordLabel|Por favor, confirme a alteração inserindo uma senha|  
|WebAuthenticationSignupConfirmNotice|O e-mail de {0}confirmação de\> inscrição está a caminho de .<\> br / Por favor siga as instruções dentro do e-mail para ativar a sua conta.<br / Se o e-mail não chegar na sua caixa de entrada nos próximos minutos, consulte a sua pasta de e-mail sucata.|  
|WebAuthenticationSignupConfirmNoticeHeader|A sua conta foi criada com sucesso|  
|WebAuthenticationSignupConfirmnoticeRepeatHeader|O e-mail de confirmação de inscrição foi novamente enviado|  
|WebAuthenticationSignupConfirmNoticeTitle|Conta criada|  
|WebAuthenticationTokenRequiredErrorMessage|Token está vazio.|  
|WebAuthenticationUserAlreadyRegisteredNotice|Parece que um utilizador com este e-mail já está registado no sistema. Se esqueceu da sua senha, tente restaurá-la ou contacte a nossa equipa de suporte.|  
|WebAuthenticationUserAlready RegisteredNoticeHeader|Utilizador já registado|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Já registado|  
|Palavra-passe para teclasLabelChange|Alterar palavra-passe|  
|ButtonLabelChangeAccountInfo|Alterar informação sobre contas|  
|ButtonLabelCloseAccount|Fechar conta|  
|WebAuthenticationInvalidCaptchaErrorMessage|O texto introduzido não corresponde ao texto da imagem. Tente novamente.|  
|ValidaçãoErrorCredentialsInvalid|O e-mail ou senha é inválido. Por favor, corrija os erros e tente novamente.|  
|WebAuthenticationRequestIsNotValid|Pedido não é válido|  
|WebAuthenticationUserIsNotConfirm|Confirme o seu registo antes de tentar iniciar o seu registo.|  
|WebAuthenticationInvalidEmailFormated|O e-mail é inválido:{0}|  
|WebAuthenticationUserNotFound|O utilizador não foi encontrado|  
|WebAuthenticationTenantNotRegistered|A sua conta pertence a um inquilino do Azure Ative Directory que não está autorizado a aceder a este portal.|  
|Autenticação da WebAutenticaçãoFalha|A autenticação falhou.|  
|WebAuthenticationGooglePlusNotEnabled|A autenticação falhou. Se autorizou a aplicação, contacte o administrador para se certificar de que a autenticação do Google está corretamente configurada.|  
|Erro de validaçãoAllowedTenantIsRequired|Inquilino Permitido é necessário|  
|ValidaçãoErrorTenantIsNotValid|O inquilino{0}do Diretório Ativo Azure ' não é válido.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Faça login {0} usando a sua conta|  
|WebAuthenticationUserLimitNotice|Este serviço atingiu o número máximo de utilizadores permitidos. Por `<a href="mailto:{0}"\>contact the administrator</a\>` favor, atualize o seu serviço e reative o registo do utilizador.|  
|WebAuthenticationUserLimitNoticeHeader|Registo de utilizadores incapacitado|  
|WebAuthenticationUserLimitNoticeTitle|Registo de utilizadores incapacitado|  
|WebAuthenticationUserRegistrationDisabledNotice|O registo de utilizadores foi desativado pelo administrador. Por favor, faça login com o fornecedor de identidade externo.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Registo de utilizadores incapacitado|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Registo de utilizadores incapacitado|  
|WebAuthenticationSignupPendingConfirmationNotice|Antes de podermos concluir a criação da sua conta, precisamos de verificar o seu endereço de e-mail. Enviamos um e-mail {0}para. Siga as instruções dentro do e-mail para ativar a sua conta. Se o e-mail não chegar nos próximos minutos, verifique a sua pasta de e-mail sucata.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Encontrámos uma conta não confirmada para {0}o endereço de e-mail. Para completar a criação da sua conta precisamos de verificar o seu endereço de e-mail. Enviamos um e-mail {0}para. Siga as instruções dentro do e-mail para ativar a sua conta. Se o e-mail não chegar nos próximos minutos, por favor, verifique a sua pasta de e-mail sucata|  
|WebAuthenticationSignupConfirmationAlmostDone|Quase Feito|  
|WebAuthenticationSignupConfirmationEmailSent|Enviamos um e-mail {0}para. Siga as instruções dentro do e-mail para ativar a sua conta. Se o e-mail não chegar nos próximos minutos, verifique a sua pasta de e-mail sucata.|  
|Mensagem de notificação por email de email de autenticação web|E-mail enviado com sucesso para{0}|  
|WebAuthenticationNoAadTenantConfigured|Nenhum inquilino do Azure Ative Directory configurado para o serviço.|  
|Caixa de verificaçãoEtiquetaEtiquetaRegistoDeConsentimentoNecessário|Concordo com `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`o.|  
|TextoblockUserRegistrationTermsProvided|Por favor, reveja`<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|Termos de dialogheadingOfUse|Termos de Utilização|  
|ValidaçãoMessageConsentNão Aceito|Deve concordar com os Termos de Utilização antes de poder prosseguir.|  
  
###  <a name="signinstrings"></a><a name="SigninStrings"></a>SigninStrings  
  
|Nome|Texto|  
|----------|----------|  
|WebAuthenticationForgotPassword|Forgot your password?|  
|WebAuthenticationIfAdministrator|Se for administrador, tem `<a href="{0}"\>here</a\>`de se inscrever.|  
|WebAuthenticationNotAMember|Ainda não é um membro? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Lembre-se de mim neste computador.|  
|WebAuthenticationSigininWithPassword|Inscreva-se com o seu nome de utilizador e senha|  
|WebAuthenticationSigninTitle|Iniciar sessão|  
|WebAuthenticationSignUpNow|Inscreva-se já|  
  
###  <a name="signupstrings"></a><a name="SignupStrings"></a>InscriçõesStrings  
  
|Nome|Texto|  
|----------|----------|  
|PageTitleSignup|Inscrever-se|  
|WebAuthenticationAlreadyAMember|Já é membro?|  
|WebAuthenticationCreateNewAccount|Criar uma nova conta de Gestão API|  
|Autenticação WebSigninNow|Inscreva-se agora|  
|ButtonLabelSignup|Inscrever-se|  
  
###  <a name="subscriptionliststrings"></a><a name="SubscriptionListStrings"></a>SubscriçãoListStrings  
  
|Nome|Texto|  
|----------|----------|  
|SubscriçõesCancelamento Confirmação|Tem certeza de que quer cancelar esta subscrição?|  
|SubscriçãoRenovaconfirmação|Tem certeza de que quer renovar esta subscrição?|  
|WebDevelopersManageSubscris|Gerir subscrições|  
|WebDevelopersPrimaryKey|Chave primária|  
|WebDevelopersRegeneraLink|Regenerar|  
|WebDevelopersSecondaryKey|Chave secundária|  
|Tecla de rótulo de botão|Mostrar|  
|Assinatura de marca de botões|Renovar|  
|Subscrições WebDevelopersSolicitadas|Solicitado em{0}|  
|Subscrições webDevelopersRequestedState|Pedido|  
|WebDevelopersSubscriptionTableNameHeader|Nome|  
|WebDevelopersSubscriptiontableStateHeader|Estado|  
|WebDevelopersUsageStatisticsLink|Relatórios de análise|  
|WebDevelopersYourSubscriptions|As suas subscrições|  
|SubscriçãoPropertyLabelRequestedDate|Solicitado em|  
|SubscriçãoPropertyLabelStartedDate|Começou|  
|Subscrição de Nome de PáginaTitleRename|Assinatura de renomear|  
|Nome de etiquetagem property de subscrição|Nome da subscrição|  
  
###  <a name="subscriptionstrings"></a><a name="SubscriptionStrings"></a>Strings de subscrição  
  
|Nome|Texto|  
|----------|----------|  
|Conta De Encerramento de SecçãoHeading|Quer fechar a sua conta?|  
|Perfil de desenvolvedor de páginatitle|Perfil|  
|Tecla de design de botões LabelHideKey|Ocultar|  
|Tecla de regeneração do rótulo dos botões|Regenerar|  
|Mensagem de informaçãoKeyWasRegenerado|Tem certeza que quer regenerar esta chave?|  
|Tecla de rótulo de botão|Mostrar|  
  
###  <a name="updateprofilestrings"></a><a name="UpdateProfileStrings"></a>UpdateProfileStrings  
  
|Nome|Texto|  
|----------|----------|  
|Perfil de atualização do ButtonLabel|Perfil de atualização|  
|Perfil de atualização da página|Atualizar informação sobre contas|  
  
###  <a name="userprofile"></a><a name="UserProfile"></a>Perfil de utilizador  
  
|Nome|Texto|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Alterar informação sobre contas|  
|Palavra-passe para teclasLabelChange|Alterar palavra-passe|  
|ButtonLabelCloseAccount|Fechar conta|  
|TextboxLabelEmail|Email|  
|TextboxLabelEmailFirstName|Nome próprio|  
|TextboxLabelEmailLastName|Apelido|  
|TextboxLabelNotificationsSenderEmail|E-mail de remetente de notificações|  
|Nome da Organização do Rótulo de Texto|Nome da organização|  
|SubscriçãoStateActive|Ativa|  
|Subscrição Estado Cancelado|Cancelada|  
|SubscriçãoStateExpired|Fora do prazo|  
|Subscrição Do Estado Rejeitada|Rejected|  
|SubscriçãoStateSolicitado|Pedido|  
|Subscrição StateSuspendEd|Suspenso|  
|Modelo de nome de subscrição predefinido|{0}(predefinido)|  
|Modelo de nome de subscrição|Acesso ao programador #{0}|  
|Nome de subscrição do Rótulo de texto|Nome da subscrição|  
|ValidaçãoMessageNome de Subscrição de Mensagens|O nome da subscrição não pode estar vazio.|  
|ApiManagementUserLimitReached|Este serviço atingiu o número máximo de utilizadores permitidos. Por favor, atualize para um nível de preços mais elevado.|  
  
##  <a name="glyph-resources"></a><a name="glyphs"></a>Recursos do glifo  
 Os modelos do portal de desenvolvimento de gestão api podem usar os glifos de [Glicifos de Bootstrap](https://getbootstrap.com/components/#glyphicons). Este conjunto de glifos inclui mais de 250 glifos em formato de fonte do conjunto [Glyphicon](https://glyphicons.com/) Halflings. Para utilizar um glifo deste conjunto, utilize a seguinte sintaxe.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Para obter a lista completa de glifos, consulte [os glicifos de Bootstrap](https://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o trabalho com modelos, consulte como personalizar o portal de desenvolvimento de [gestão da API utilizando modelos](api-management-developer-portal-templates.md).
