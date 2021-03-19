---
title: Recursos de modelo de gestão da Azure API | Microsoft Docs
description: Saiba mais sobre os tipos de recursos disponíveis para uso em modelos de portal de desenvolvedores na Azure API Management.
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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84689992"
---
# <a name="azure-api-management-template-resources"></a>Recursos do modelo de gestão da AZure API
A Azure API Management fornece os seguintes tipos de recursos para utilização nos modelos do portal do desenvolvedor.  
  
-   [Recursos de cordas](#strings)  
  
-   [Recursos glifos](#glyphs)  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="string-resources"></a><a name="strings"></a> Recursos de cordas  
 A API Management fornece um conjunto abrangente de recursos de cordas para uso no portal do desenvolvedor. Estes recursos estão localizados em todas as línguas apoiadas pela API Management. O conjunto predefinido de modelos utiliza estes recursos para cabeçalhos de página, etiquetas e quaisquer cordas constantes que sejam exibidas no portal do desenvolvedor. Para utilizar um recurso de cadeia nos seus modelos, forneça o prefixo da cadeia de recursos seguido pelo nome da corda, como mostra o exemplo a seguir.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 O exemplo a seguir é do modelo da lista de produtos e exibe **produtos** no topo da página.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
São suportadas as seguintes opções de localização:

| Região    | Linguagem               |
|-----------|------------------------|
| "en"      | "Inglês"              |
| "CS"      | "Čeština"              |
| "de"      | "Deutsch"              |
| "es"      | "Español"              |
| "fr"      | "Français"             |
| "hu"      | "Magyar"               |
| "isto"      | "Italiano"             |
| "ja-JP"   | "日本語"                |
| "ko"      | "한국어"                |
| "nl"      | "Nederlands"           |
| "pl"      | "Polski"               |
| "pt-br"   | "Português (Brasil)"   |
| "pt-pt"   | "Português (Portugal)" |
| "ru"      | "Русский"              |
| "sv"      | "Svenska"              |
| "tr"      | "Türkçe"               |
| "zh-hans" | "中文(简体)"           |
| "zh-hant" | "中文(繁體)"           |

 Consulte as seguintes tabelas para os recursos de corda disponíveis para utilização nos modelos do seu portal de desenvolvedores. Utilize o nome da mesa como prefixo para os recursos de corda nessa tabela.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [Documentação](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [EdiçõesStrings](#IssuesStrings)  
  
-   [NãoFoundStrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [Produtos](#ProductsStrings)  
  
-   [FornecedorInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [SigninStrings](#SigninStrings)  
  
-   [Inscrições](#SignupStrings)  
  
-   [SubscriçõesListStrings](#SubscriptionListStrings)  
  
-   [Modelos de subscrição](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="apisstrings"></a><a name="ApisStrings"></a> ApisStrings  
  
|Name|Texto|  
|----------|----------|  
|PageTitleApis|APIs|  
  
###  <a name="appdetailsstrings"></a><a name="AppDetailsStrings"></a> AppDetailsStrings  
  
|Name|Texto|  
|----------|----------|  
|WebApplicationsDetailsTitle|Pré-visualização da aplicação|  
|WebApplicationsRequirementsHeader|Requisitos|  
|WebApplicationsScreenshotAlt|Captura de ecrã|  
|WebApplicationsScreenshotsHeader|Capturas de ecrã|  
  
###  <a name="applicationliststrings"></a><a name="ApplicationListStrings"></a> ApplicationListStrings  
  
|Name|Texto|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Tem a certeza de que pretende remover a aplicação?|  
|WebDevelopersAppNot Publicado|Não publicado|  
|WebDevelopersAppNotSubditado|Não submetido|  
|WebDevelopersAppTableCategoryHeader|Categoria|  
|WebDevelopersAppTableNameHeader|Name|  
|WebDevelopersAppTableStateHeader|Estado|  
|WebDevelopersEditLink|Editar|  
|WebDevelopersRegisterAppLink|Registar aplicação|  
|WebDevelopersRemoveLink|Remover|  
|WebDevelopersSubmitLink|Enviar|  
|WebDevelopersYourApplicationsHeader|As suas candidaturas|  
  
###  <a name="appstrings"></a><a name="AppStrings"></a> AppStrings  
  
|Name|Texto|  
|----------|----------|  
|WebApplicationsHeader|Aplicações|  
  
###  <a name="commonresources"></a><a name="CommonResources"></a> CommonResources  
  
|Name|Texto|  
|----------|----------|  
|NoItemsToDisplay|Não foram encontrados resultados.|  
|GeneralExceptionMessage|Algo não está certo. Pode ser uma falha temporária ou um inseto. Por favor, tente de novo.|  
|GeneralJsonExceptionMessage|Algo não está certo. Pode ser uma falha temporária ou um inseto. Por favor, recarregue a página e tente de novo.|  
|ConfirmaçãoMessageUnsavedChanges|Há algumas mudanças não salvas. Tem a certeza de que pretende cancelar e descartar as alterações?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|Http Request Body demasiado grande.|  
  
###  <a name="commonstrings"></a><a name="CommonStrings"></a> CommonStrings  
  
|Name|Texto|  
|----------|----------|  
|BotãoLabelCancel|Cancelar|  
|ButtonLabelSave|Guardar|  
|GeneralExceptionMessage|Algo não está certo. Pode ser uma falha temporária ou um inseto. Por favor, tente de novo.|  
|NoItemsToDisplay|Não há itens para exibir.|  
|PagerButtonLabelFirst|Primeiro|  
|PagerButtonLabelLast|Último|  
|PagerButtonLabelNext|Seguinte|  
|PagerButtonLabelPrevious|Prev|  
|PagerLabelPageNOfM|Página {0} de {1}|  
|PasswordTooShort|A palavra-passe é muito curta.|  
|EmailAsPassword|Não utilize o seu email como senha|  
|PasswordSameAsUserName|A sua palavra-passe não pode conter o seu nome de utilizador|  
|PasswordTwoCharacterClasses|Use diferentes classes de caracteres|  
|PasswordTooManyRepetitions|Demasiadas repetições|  
|PasswordSequenceFound|A sua palavra-passe contém sequências|  
|PagerLabelPageSize|Tamanho da página|  
|CurtainLabelLoading|A carregar...|  
|TablePlaceholderNothingToDisplay|Não há dados para o período e âmbito selecionados|  
|ButtonLabelClose|Fechar|  
  
###  <a name="documentation"></a><a name="Documentation"></a> Documentação  
  
|Name|Texto|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Cabeçalho inválido {0} ' '|  
|WebDocumentationInvalidRequestErrorMessage|URL de pedido inválido|  
|TextboxLabelAccessToken|Ficha de acesso *|  
|DropdownOptionPrimaryKeyFormat|Primárias...{0}|  
|DropdownOptionSecondaryKeyFormat|Secundário...{0}|  
|WebDocumentationSubscriptionKeyText|A sua chave de subscrição|  
|WebDocumentationTemplatesAddHeaders|Adicionar cabeçalhos HTTP necessários|  
|WebDocumentationTemplatesBasicAuthSample|Amostra de autorização básica|  
|WebDocumentationTemplatesCurlForBasicAuth|para utilização de autorização básica: --user {username}:{password}|  
|WebDocumentationTemplatesCurlValuesForPath|Especifique os valores dos parâmetros de trajetória (mostrados como {...}), a chave de subscrição e os valores para parâmetros de consulta|  
|WebDocumentationTemplatesDeveloperKey|Especifique a sua chave de subscrição|  
|WebDocumentationTemplatesJavaApache|Esta amostra utiliza o cliente Apache HTTP a partir de componentes HTTP (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Especifique os valores para parâmetros opcionais, conforme necessário|  
|WebDocumentationTemplatesPhpPackage|Esta amostra utiliza o pacote HTTP_Request2. (para mais informações: https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Especifique os valores para os parâmetros do caminho (mostrado como {...}) e solicite o corpo se necessário|  
|WebDocumentationTemplatesRequestBody|Especifique o corpo de pedido|  
|WebDocumentationTemplatesRequiredParams|Especificar os valores para os seguintes parâmetros necessários|  
|WebDocumentationTemplatesValuesForPath|Especifique os valores para os parâmetros do caminho (mostrados como {...})|  
|OAuth2AuthorizationEndpointDescription|O ponto final de autorização é utilizado para interagir com o titular do recurso e obter uma concessão de autorização.|  
|OAuth2AuthorizationEndpointName|Ponto final de autorização|  
|Oauth2TokenEndpointDescription|O ponto final simbólico é utilizado pelo cliente para obter um token de acesso, apresentando a sua concessão de autorização ou atualização de token.|  
|Nome OAuth2TokenEndpointName|Ponto final token|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|<p \>         O cliente inicia o fluxo direcionando o utilizador-agente do titular do recurso para o ponto final de autorização.  O cliente inclui o seu identificador de clientes, âmbito solicitado, estado local, e um URI de reorientação para o qual o servidor de autorização enviará de volta o agente de utilizador uma vez que o acesso é concedido (ou negado).     </p \>     <p O \>         servidor de autorização autentica o titular do recurso (através do agente de utilizador) e estabelece se o titular do recurso concede ou nega o pedido de acesso do cliente.     </p \>     <p \>         Assumindo que o titular do recurso concede acesso, o servidor de autorização redireciona o agente de utilizador de volta para o cliente utilizando o URI de reorientação fornecido anteriormente (no pedido ou durante o registo do cliente).  O URI de reorientação inclui um código de autorização e qualquer estado local fornecido pelo cliente anteriormente.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|<p \>     Se o utilizador negar o pedido de acesso de se o pedido for inválido, o cliente será informado utilizando os seguintes parâmetros adicionados ao redirecionamento: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Pedido de autorização|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|<p \>         A aplicação do cliente deve enviar o utilizador para o ponto final de autorização para iniciar o processo OAuth.          No ponto final da autorização, o utilizador autentica e, em seguida, concede ou nega o acesso à app.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|<p \>     Assumindo que o titular do recurso concede acesso, o servidor de autorização redireciona o agente de utilizador de volta para o cliente utilizando a reorientação URI fornecida anteriormente (no pedido ou durante o registo do cliente).  O URI de reorientação inclui um código de autorização e qualquer estado local fornecido pelo cliente anteriormente. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|<p \>  O cliente solicita um token de acesso do ponto final simbólico do servidor de autorização, incluindo o código de autorização recebido no passo anterior.  Ao entear o pedido, o cliente autentica-se com o servidor de autorização.  O cliente inclui a reorientação URI utilizada para obter o código de autorização para verificação. </p \> <p O servidor de autorização autentica o \>     cliente, valida o código de autorização e assegura que o URI de reorientação recebido corresponde ao URI utilizado para redirecionar o cliente em passo (C).  Se válido, o servidor de autorização responde com um token de acesso e, opcionalmente, um token de atualização. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|<p \>     Se a autenticação do cliente pedido falhou ou é inválida, o servidor de autorização responde com um código de estado HTTP 400 (Mau Pedido) (salvo especificação em contrário) e inclui os seguintes parâmetros com a resposta. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|<p \>   O cliente faz um pedido ao ponto final simbólico enviando os seguintes parâmetros utilizando o formato "aplicação/x-www-forma-urlencoded" com uma codificação de caracteres da UTF-8 no pedido HTTP entidade-entidade. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|<p \>  O servidor de autorização emite um token de acesso e um token de atualização opcional, e constrói a resposta adicionando os seguintes parâmetros ao corpo de entidade da resposta HTTP com um código de estado de 200 (OK). </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|<p \>  O cliente autentica-se com o servidor de autorização e solicita um sinal de acesso a partir do ponto final simbólico. </p \> <p O servidor de autorização autentica o cliente \>  e, se válido, emite um token de acesso. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|<p Se o pedido falhar na \>     autenticação do cliente ou se for inválido, o servidor de autorização responde com um código de estado HTTP 400 (Mau Pedido) (salvo especificação em contrário) e inclui os seguintes parâmetros com a resposta. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|<p \>   O cliente faz um pedido ao ponto final simbólico adicionando os seguintes parâmetros utilizando o formato "aplicação/x-www-forma-urlencoded" com uma codificação de caracteres da UTF-8 no pedido HTTP entidade-entidade. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|<p \>  Se o pedido de token de acesso for válido e autorizado, o servidor de autorização emite um token de acesso e ficha de atualização opcional, e constrói a resposta adicionando os seguintes parâmetros à entidade-corpo da resposta HTTP com um código de estado de 200 (OK). </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|<p \>   O cliente inicia o fluxo direcionando o utilizador-agente do titular do recurso para o ponto final de autorização.  O cliente inclui o seu identificador de clientes, âmbito solicitado, estado local, e um URI de reorientação para o qual o servidor de autorização enviará de volta o agente de utilizador uma vez que o acesso é concedido (ou negado). </p \> <p O \>        servidor de autorização autentica o titular do recurso (através do agente de utilizador) e estabelece se o titular do recurso concede ou nega o pedido de acesso do cliente. </p \> <p \>        Assumindo que o titular do recurso concede acesso, o servidor de autorização redireciona o agente de utilizador de volta para o cliente utilizando o URI de reorientação fornecido anteriormente.  O URI de reorientação inclui o símbolo de acesso no fragmento URI. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|<p \>     Se o titular do recurso negar o pedido de acesso ou se o pedido falhar por razões que não um URI de reorientação em falta ou inválido, o servidor de autorização informa o cliente adicionando os seguintes parâmetros ao componente fragmento do URI de reorientação utilizando o formato "aplicação/x-www-forma-urlencoded". </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|<p \>     A aplicação do cliente deve enviar o utilizador para o ponto final de autorização para iniciar o processo OAuth.      No ponto final da autorização, o utilizador autentica e, em seguida, concede ou nega o acesso à app. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|<p \>     Se o titular do recurso conceder o pedido de acesso, o servidor de autorização emite um token de acesso e entrega-o ao cliente adicionando os seguintes parâmetros ao componente fragmento do URI de reorientação utilizando o formato "aplicação/x-www-forma-urlencoded". </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|O fluxo de código de autorização é otimizado para clientes capazes de manter a confidencialidade das suas credenciais (por exemplo, aplicações de servidores web implementadas usando PHP, Java, Python, Ruby, ASP.NET, etc.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Concessão do Código de Autorização|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|O fluxo de credenciais do cliente é adequado nos casos em que o cliente (a sua aplicação) está a solicitar o acesso aos recursos protegidos sob o seu controlo. O cliente é considerado como um proprietário de recursos, pelo que não é necessária qualquer interação com o utilizador final.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Concessão de Credenciais de Cliente|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|O fluxo implícito é otimizado para clientes incapazes de manter a confidencialidade das suas credenciais conhecidas por operarem um URI de redirecionamento particular. Estes clientes são normalmente implementados num navegador usando um idioma de scripts como o JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Subvenção implícita|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|O fluxo de credenciais de senha do proprietário do recurso é adequado nos casos em que o proprietário do recurso tem uma relação de confiança com o cliente (a sua aplicação), como o sistema operativo do dispositivo ou uma aplicação altamente privilegiada. Este fluxo é adequado para clientes capazes de obter as credenciais do proprietário do recurso (nome de utilizador e senha, normalmente utilizando um formulário interativo).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Concessão de credenciais de senha do proprietário de recursos|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|<p \>   O titular do recurso fornece ao cliente o seu nome de utilizador e senha. </p \> <p O \> cliente solicita um token de acesso do ponto final simbólico do servidor de autorização, incluindo as credenciais recebidas do titular do recurso.  Ao entear o pedido, o cliente autentica-se com o servidor de autorização. </p \> <p O \>     servidor de autorização autentica o cliente e valida as credenciais do titular do recurso e, se válido, emite um token de acesso. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|<p Se o pedido falhar na \>  autenticação do cliente ou se for inválido, o servidor de autorização responde com um código de estado HTTP 400 (Mau Pedido) (salvo especificação em contrário) e inclui os seguintes parâmetros com a resposta. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|<p \>    O cliente faz um pedido ao ponto final simbólico adicionando os seguintes parâmetros utilizando o formato "aplicação/x-www-forma-urlencoded" com uma codificação de caracteres da UTF-8 no pedido HTTP entidade-entidade. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|<p \>   Se o pedido de token de acesso for válido e autorizado, o servidor de autorização emite um token de acesso e ficha de atualização opcional, e constrói a resposta adicionando os seguintes parâmetros à entidade-corpo da resposta HTTP com um código de estado de 200 (OK). </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Pedido de sinal de acesso|  
|OAuth2Step_AuthorizationRequest_Name|Pedido de autorização|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|Necessário. O sinal de acesso emitido pelo servidor de autorização.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|Necessário. O sinal de acesso emitido pelo servidor de autorização.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|Necessário. O sinal de acesso emitido pelo servidor de autorização.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Necessário. O sinal de acesso emitido pelo servidor de autorização.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|Necessário. Identificador de cliente.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|OBRIGATÓRIO se o cliente não estiver a autenticar com o servidor de autorização.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|Necessário. O identificador de clientes.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|Necessário. O código de autorização gerado pelo servidor de autorização.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|Necessário. O código de autorização recebido do servidor de autorização.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|Opcional. Texto ASCII legível pelo homem que fornece informações adicionais.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|Opcional. Texto ASCII legível pelo homem que fornece informações adicionais.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|Opcional. Texto ASCII legível pelo homem que fornece informações adicionais.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|Opcional. Texto ASCII legível pelo homem que fornece informações adicionais.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Opcional. Texto ASCII legível pelo homem que fornece informações adicionais.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|Opcional. Um URI identificando uma página web legível pelo homem com informações sobre o erro.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|Opcional. Um URI identificando uma página web legível pelo homem com informações sobre o erro.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|Opcional. Um URI identificando uma página web legível pelo homem com informações sobre o erro.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|Opcional. Um URI identificando uma página web legível pelo homem com informações sobre o erro.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Opcional. Um URI identificando uma página web legível pelo homem com informações sobre o erro.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|Necessário. Um único código de erro ASCII a partir do seguinte: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|Necessário. Um único código de erro ASCII a partir do seguinte: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|Necessário. Um único código de erro ASCII a partir do seguinte: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|Necessário. Um único código de erro ASCII a partir do seguinte: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Necessário. Um único código de erro ASCII a partir do seguinte: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|RECOMENDADO. A vida inteira em segundos do símbolo de acesso.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|RECOMENDADO. A vida inteira em segundos do símbolo de acesso.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|RECOMENDADO. A vida inteira em segundos do símbolo de acesso.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|RECOMENDADO. A vida inteira em segundos do símbolo de acesso.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|Necessário. Valor DEVE ser definido como "authorization_code".|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|Necessário. Valor DEVE ser definido como "client_credentials".|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Necessário. Valor DEVE ser definido como "password".|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Necessário. A senha do proprietário do recurso.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|Opcional. O ponto final de reorientação URI deve ser um URI absoluto.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|OBRIGATÓRIO SE o parâmetro "redirect_uri" foi incluído no pedido de autorização, e os seus valores DEVEM ser idênticos.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|Opcional. O ponto final de reorientação URI deve ser um URI absoluto.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|Opcional. O token refresh, que pode ser usado para obter novos tokens de acesso.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|Opcional. O token refresh, que pode ser usado para obter novos tokens de acesso.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Opcional. O token refresh, que pode ser usado para obter novos tokens de acesso.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|Necessário. Valor DEVE ser definido como "código".|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|Necessário. Valor DEVE ser definido como "token".|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|Opcional. O âmbito do pedido de acesso.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|OPCIONAL se idêntico ao âmbito solicitado pelo cliente; caso contrário, NECESSÁRIO.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|Opcional. O âmbito do pedido de acesso.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|OPCIONAL, se idêntico ao âmbito solicitado pelo cliente; caso contrário, NECESSÁRIO.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|Opcional. O âmbito do pedido de acesso.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|OPCIONAL se idêntico ao âmbito solicitado pelo cliente; caso contrário, NECESSÁRIO.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Opcional. O âmbito do pedido de acesso.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPCIONAL, se idêntico ao âmbito solicitado pelo cliente; caso contrário, NECESSÁRIO.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|OBRIGATÓRIO se o parâmetro "estado" estivesse presente no pedido de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|RECOMENDADO. Um valor opaco usado pelo cliente para manter o estado entre o pedido e o retorno.  O servidor de autorização inclui este valor ao redirecionar o agente de utilizador de volta para o cliente.  O parâmetro deve ser utilizado para evitar a falsificação de pedidos transversais.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|OBRIGATÓRIO se o parâmetro "estado" estivesse presente no pedido de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|OBRIGATÓRIO se o parâmetro "estado" estivesse presente no pedido de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|RECOMENDADO. Um valor opaco usado pelo cliente para manter o estado entre o pedido e o retorno.  O servidor de autorização inclui este valor ao redirecionar o agente de utilizador de volta para o cliente.  O parâmetro deve ser utilizado para evitar a falsificação de pedidos transversais.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|OBRIGATÓRIO se o parâmetro "estado" estivesse presente no pedido de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|Necessário. O tipo de ficha emitida.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|Necessário. O tipo de ficha emitida.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|Necessário. O tipo de ficha emitida.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Necessário. O tipo de ficha emitida.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Necessário. O nome de utilizador do proprietário do recurso.|  
|OAuth2UnsupportedTokenType|O tipo token ' {0} ' não é suportado.|  
|OAuth2InvalidState|Resposta inválida do servidor de autorização|  
|OAuth2GrantType_AuthorizationCode|Código de autorização|  
|OAuth2GrantType_Implicit|Implícito|  
|OAuth2GrantType_ClientCredentials|Credenciais de cliente|  
|OAuth2GrantType_ResourceOwnerPassword|Senha do proprietário de recursos|  
|WebDocumentation302Code|302 Encontrado|  
|WebDocumentation400Code|400 (Mau pedido)|  
|OAuth2SendingMethod_AuthHeader|Cabeçalho de autorização|  
|OAuth2SendingMethod_QueryParam|Parâmetro de consulta|  
|OAuth2AuthorizationServerGeneralException|Ocorreu um erro ao autorizar o acesso através de {0}|  
|OAuth2AuthorizationServerCommunicationException|Não foi possível estabelecer uma ligação HTTP ao servidor de autorização ou foi inesperadamente encerrada.|  
|WebDocumentationOAuth2GenerrorMessage|Ocorreu um erro inesperado.|  
|AutorizaçãoServerComumunicationExcepção|A exceção de comunicação do servidor de autorização aconteceu. Por favor contacte o administrador.|  
|Descrição do TextblockSubscriptionKeyHeaderDscription|Chave de subscrição que dá acesso a esta API. Encontrado na sua <um perfil href='/developer' \> Profile</a \> .|  
|Descrição do TextblockOAuthHeader|Ficha de acesso OAuth 2.0 obtida a partir de <i \> {0}</i \> . Tipos de subvenções suportados: <i \> {1}</i \> .|  
|TextblockContentTypeHeaderDescription|Tipo de mídia do corpo enviado para a API.|  
|ErrorMessageApiNotAccessible|A API que está a tentar ligar não está acessível neste momento. Entre em contato com a editora API <a href="/issues" \> aqui</a \> .|  
|ErrorMessageApiTimedout|A API que está a tentar ligar está a demorar mais do que o normal para recuperar a resposta. Entre em contato com a editora API <a href="/issues" \> aqui</a \> .|  
|BadRequestParameterExpected|"' {0} 'parâmetro é esperado'|  
|TooltipTextDoubleClickToSelectAll|Clique duplo para selecionar todos.|  
|TooltipTextHideRevealSecret|Mostrar/Esconder|  
|ButtonLinkOpenConsole|Experimente|  
|SectionHeadingRequestBody|Corpo do pedido|  
|Separadores de Quadros de Secção|Parâmetros de pedido|  
|SectionHeadingRequestUrl|URL do Pedido|  
|SectionHeadingResponse|Resposta|  
|Cabeças de SecçãoRequestHeaders|Cabeçalhos do pedido|  
|FormLabelSubtextOptional|opcional|  
|SeçãoHeadingCodeSamples|Exemplos de código|  
|TextblockOpenidConnectHeaderDescription|Ficha de ID OpenID Connect obtida a partir de <i \> {0}</i \> . Tipos de subvenções suportados: <i \> {1}</i \> .|  
  
###  <a name="errorpagestrings"></a><a name="ErrorPageStrings"></a> ErrorPageStrings  
  
|Name|Texto|  
|----------|----------|  
|LinkLabelBack|anterior|  
|LinkLabelHomePage|página inicial|  
|LinkLabelSendUsEmail|Envie-nos um e-mail|  
|PageTitleError|Desculpe, houve um problema em servir a página solicitada.|  
|TextblockPotentialCauseIntermittentIssue|Este pode ser um problema intermitente de acesso a dados que já se foi.|  
|TextblockPotentialCauseOldLink|O link em que clicou pode ser antigo e não apontar mais para a localização correta.|  
|TextblockPotentialCauseTechnicalProblem|Pode haver um problema técnico do nosso lado.|  
|TextblockPotentialSolutionRefresh|Experimente atualizar a página.​|  
|TextblockPotentialSolutionStartOver|Começar de novo da {0} nossa.|  
|TextblockPotentialSolutionTryAgain|Vai {0} tentar a ação que voltaste a fazer.|  
|TextReportProblem|{0} descrevendo o que correu mal e vamos vê-lo assim que pudermos.|  
|TitlePotentialCause|Causa potencial|  
|Solução titlepotenciaial|É possivelmente apenas uma questão temporária, algumas coisas para tentar|  
  
###  <a name="issuesstrings"></a><a name="IssuesStrings"></a> EdiçõesStrings  
  
|Name|Texto|  
|----------|----------|  
|WebIssuesIndexTitle|Questões|  
|WebIssuesNoActiveSubscriptions|Não tem assinaturas ativas. Precisa subscrever um produto para reportar um problema.|  
|WebIssuesNotSignin|Não está inscrito. Por favor, {0} reporte um problema ou publique um comentário.|  
|WebIssuesReportIssueButton|Emissão do Relatório|  
|WebIssuesSignIn|iniciar sessão|  
|WebIssuesStatusReportedBy|Estado: {0} &#124; Reportado por {1}|  
  
###  <a name="notfoundstrings"></a><a name="NotFoundStrings"></a> NãoFoundStrings  
  
|Name|Texto|  
|----------|----------|  
|LinkLabelHomePage|página inicial|  
|LinkLabelSendUsEmail|enviar-nos um e-mail|  
|PageTitleNotFound|Desculpe, não conseguimos encontrar a página que procura.|  
|TextblockPotentialCauseMisspelledUrl|Pode ter escrito mal a URL se a digitou.|  
|TextblockPotentialCauseOldLink|O link em que clicou pode ser antigo e não apontar mais para a localização correta.|  
|TextoblockPotentialSolutionRetype|Tente reformular a URL.|  
|TextblockPotentialSolutionStartOver|Começar de novo da {0} nossa.|  
|TextReportProblem|{0} descrevendo o que correu mal e vamos vê-lo assim que pudermos.|  
|TitlePotentialCause|Causa potencial|  
|Solução titlepotenciaial|Solução potencial|  
  
###  <a name="productdetailsstrings"></a><a name="ProductDetailsStrings"></a> ProductDetailsStrings  
  
|Name|Texto|  
|----------|----------|  
|Acordo de Produtos Web|Ao subscrever o {0} Produto, concordo com o `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>` .|  
|WebProductsLegalTermsLink|Termos de Utilização|  
|WebProductsSubscribeButton|Subscrever|  
|WebProductsUsageLimitsHeader|Limites de utilização|  
|Produtos WebDisYouAreNotSubsubscrito|Você está subscrito a este produto.|  
|WebProductsYouRequestedSubscription|Solicitou a subscrição deste produto.|  
|ErrorYouNeedToAgreeWithLegalTerms|Deve concordar com os Termos de Utilização antes de poder prosseguir.|  
|ButtonLabelAddSubscrevição|Adicionar subscrição|  
|Nome linkLabelChangeSubscriptionName|alterar|  
|BotãoLabelConfirm|Confirmar|  
|TextblockMultipleSubscriptionsCount|Tem {0} subscrições deste produto:|  
|TextblockSingleSubscriptionsCount|Tem {0} subscrição deste produto:|  
|TextblockSingleApisCount|Este produto contém {0} API:|  
|TextblockMultipleApisCount|Este produto contém {0} APIs:|  
|TextblockHeaderSubscribe|Subscreva o produto|  
|Descrição da subscrição de blocos de texto|Uma nova subscrição será criada da seguinte forma:|  
|TextblockSubscriptionLimitEd|Limite de assinaturas atingido.|  
  
###  <a name="productsstrings"></a><a name="ProductsStrings"></a> Produtos  
  
|Name|Texto|  
|----------|----------|  
|PageTitleProducts|Produtos|  
  
###  <a name="providerinfostrings"></a><a name="ProviderInfoStrings"></a> FornecedorInfoStrings  
  
|Name|Texto|  
|----------|----------|  
|TextboxExternalIdentidadesDisabled|O sinal é desativado pelos administradores no momento.|  
|TextboxExternalidentidadessigninvitation|Alternativamente, inscreva-se com|  
|TextboxExternalIdentidadesSigninInvitationPrimary|Inscreva-se com:|  
  
###  <a name="signinresources"></a><a name="SigninResources"></a> SigninResources  
  
|Name|Texto|  
|----------|----------|  
|PrincipalNotFound|O principal não é encontrado ou a assinatura é inválida|  
|ErroSsoAuthenticationFailed|A autenticação SSO falhou|  
|ErrorSsoAuthenticationFailedDetailed|O token inválido fornecido ou a assinatura não pode ser verificado.|  
|ErrorSsoTokenInvalid|O símbolo SSO é inválido|  
|ValidaçãoErrSpecificEmailAlreadyExists|E-mail {0} ' já registado|  
|ValidaçãoErrorSpecificEmailInvalid|E-mail {0} ' é inválido|  
|ValidaçãoErrorPasswordInvalid|A palavra-passe é inválida. Por favor, corrija os erros e tente novamente.|  
|PropriedadeTooShort|{0} é muito curto|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Endereço de e-mail inválido.|  
|ValidaçãoMessageNewPasswordConfirmationRequired|Confirme nova senha|  
|ValidaçãoErrorPasswordConfirmationRequired|Confirmar a palavra-passe está vazia|  
|WebAuthenticationEmailChangeNotice|Alterar o e-mail de confirmação está a caminho de {0} . Por favor, siga as instruções para confirmar o seu novo endereço de e-mail. Se o e-mail não chegar à sua caixa de entrada nos próximos minutos, verifique a sua pasta de correio eletrónico de lixo.|  
|WebAuthenticationEmailChangeNoticeHeader|O seu pedido de alteração de email foi processado com sucesso|  
|WebAuthenticationEmailChangeNoticeTitle|Alteração de e-mail solicitada|  
|WebAuthenticationEmailHasBeenRevertedNotice|O seu e-mail já existe. Pedido foi revertido|  
|ValidationErrorEmailAlreadyExists|O e-mail já existe|  
|ValidaçãoErrorEmailInvalid|Endereço de e-mail inválido|  
|TextboxLabelEmail|E-mail|  
|ValidaçãoErrorEmailRequired|O e-mail é obrigatório.|  
|WebAuthenticationErrorNoticeHeader|Erro|  
|WebAuthenticationFieldLengthErrorMessage|{0} deve ser um comprimento máximo de {1}|  
|TextboxLabelEmailFirstName|Nome próprio|  
|ValidaçãoErrorFirstNameRequired|O primeiro nome é necessário.|  
|ValidaçãoErrorFirstNameInvalid|Primeiro nome inválido|  
|AvisoInvalidInvitationToken|Por favor, note que os links de confirmação são válidos por apenas 48 horas. Se ainda estiver dentro deste prazo, certifique-se de que a sua ligação está correta. Se o seu link expirou, por favor repita a ação que está a tentar confirmar.|  
|AvisoHeaderInvalidInvitationToken|Ficha de convite inválida|  
|AvisoTitleInvalidInvitationToken|Erro de confirmação|  
|WebAuthenticationLastNameInvalidErrorMessage|Apelido inválido|  
|TextboxLabelEmailName|Apelido|  
|ValidaçãoErrorLastNameRequired|O sobrenome é necessário.|  
|WebAuthenticationLinkExpiredNotice|O link de confirmação enviado para si expirou. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|AvisoPasswordResetLinkInvalidOrExpired|O link de reset da sua palavra-passe é inválido ou expirado.|  
|WebAuthenticationLinkExpiredNoticeTitle|Link enviado|  
|WebAuthenticationNewPasswordLabel|Nova palavra-passe|  
|ValidaçãoMessageNewPasswordRequired|É necessária nova senha.|  
|TextboxLabelNotificationsSenderEmail|E-mail de remetente de notificações|  
|TextboxLabelOrganizaçãoName|Nome da organização|  
|WebAuthenticationOrganizaçãoRequiredErrorMessage|Nome da organização está vazio|  
|WebAuthenticationPasswordChangedNotice|A sua palavra-passe foi atualizada com sucesso|  
|WebAuthenticationPasswordChangedNoticeTitle|Palavra-passe atualizada|  
|WebAuthenticationPasswordCompareErrorMessage|As palavras-passe não coincidem|  
|WebAuthenticationPasswordConfirmLabel|Confirmar palavra-passe|  
|ValidaçãoErrorPasswordInvalidDetailed|A palavra-passe é muito fraca.|  
|WebAuthenticationPasswordLabel|Palavra-passe|  
|ValidaçãoErrorPasswordRequired|A palavra-passe é necessária.|  
|WebAuthenticationPasswordResetsendNotice|Altere o email de confirmação de senha está a caminho de {0} . Por favor, siga as instruções dentro do e-mail para continuar o seu processo de mudança de senha.|  
|WebAuthenticationPasswordResetsendNoticeHeader|O seu pedido de reset de palavra-passe foi processado com sucesso|  
|WebAuthenticationPasswordResetsendNoticeTitle|Reset de palavra-passe solicitado|  
|WebAuthenticationRequestNotFoundNotice|Pedido não encontrado|  
|WebAuthenticationSenderEmailRequiredErrorMessage|Notificações e-mail remetente está vazio|  
|WebAuthenticationSigninPasswordLabel|Por favor, confirme a alteração introduzindo uma senha|  
|WebAuthenticationSignupConfirmNotice|O email de confirmação de registo está a caminho {0} de .<br / \> Siga as instruções dentro do e-mail para ativar a sua conta.<br / \> Se o e-mail não chegar à sua caixa de entrada nos próximos minutos, verifique a sua pasta de correio eletrónico de lixo.|  
|WebAuthenticationSignupConfirmNoticeHeader|A sua conta foi criada com sucesso|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|O email de confirmação de inscrição foi enviado novamente|  
|WebAuthenticationSignupConfirmNoticeTitle|Conta criada|  
|WebAuthenticationTokenRequiredErrorMessage|O token está vazio.|  
|WebAuthenticationUserAlreadyRegisteredNotice|Parece que um utilizador com este e-mail já está registado no sistema. Se esqueceu a sua senha, tente restaurá-la ou contacte a nossa equipa de apoio.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|Utilizador já registado|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Já registado|  
|ButtonLabelChangePassword|Alterar palavra-passe|  
|ButtonLabelChangeAccountInfo|Alterar informações de conta|  
|ButtonLabelCloseAccount|Fechar conta|  
|WebAuthenticationInvalidCaptchaErrorMessage|O texto introduzido não corresponde ao texto na imagem. Tente novamente.|  
|ValidaçãoErrorCredentialsInvalid|O e-mail ou a palavra-passe são inválidos. Por favor, corrija os erros e tente novamente.|  
|WebAuthenticationRequestIsNotValid|Pedido não é válido|  
|WebAuthenticationUserIsNotConfirm|Por favor, confirme o seu registo antes de tentar entrar.|  
|WebAuthenticationInvalidEmailFormado|O e-mail é inválido: {0}|  
|WebAuthenticationUserNotFound|O utilizador não foi encontrado|  
|WebAuthenticationTenantNotRegistered|A sua conta pertence a um inquilino do Azure Ative Directory que não está autorizado a aceder a este portal.|  
|WebAuthenticationAuthenticationFailed|A autenticação falhou.|  
|WebAuthenticationGooglePlusNotEnabled|A autenticação falhou. Se autorizou a aplicação, contacte o administrador para se certificar de que a autenticação do Google está configurada corretamente.|  
|ValidaçãoErrorAllowedTenantIsRequired|Inquilino permitido é necessário|  
|ValidaçãoErrorTenantIsNotValid|O inquilino do Azure Ative Directory {0} ' não é válido.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Faça login usando a sua {0} conta|  
|WebAuthenticationUserLimitNotice|Este serviço atingiu o número máximo de utilizadores autorizados. Por favor, `<a href="mailto:{0}"\>contact the administrator</a\>` atualize o seu serviço e reesevolva o registo do utilizador.|  
|WebAuthenticationUserLimitNoticeHeader|Registo do utilizador desativado|  
|WebAuthenticationUserLimitNoticeTitle|Registo do utilizador desativado|  
|WebAuthenticationUserRegistrationDisabledNotice|O registo dos utilizadores foi desativado pelo administrador. Faça login com o fornecedor de identidade externo.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Registo do utilizador desativado|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Registo do utilizador desativado|  
|WebAuthenticationSignupPendingConfirmationNotice|Antes de podermos concluir a criação da sua conta, precisamos de verificar o seu endereço de e-mail. Enviámos um e-mail {0} para. Por favor, siga as instruções dentro do e-mail para ativar a sua conta. Se o e-mail não chegar nos próximos minutos, por favor, verifique a sua pasta de correio eletrónico de lixo.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Encontrámos uma conta não confirmada para o endereço de {0} e-mail. Para completar a criação da sua conta precisamos de verificar o seu endereço de e-mail. Enviámos um e-mail {0} para. Por favor, siga as instruções dentro do e-mail para ativar a sua conta. Se o e-mail não chegar nos próximos minutos, por favor, verifique a sua pasta de correio eletrónico de lixo|  
|WebAuthenticationSignupConfirmationAlmostDone|Quase feito|  
|WebAuthenticationSignupConfirmationEmailSent|Enviámos um e-mail {0} para. Por favor, siga as instruções dentro do e-mail para ativar a sua conta. Se o e-mail não chegar nos próximos minutos, por favor, verifique a sua pasta de correio eletrónico de lixo.|  
|WebAuthenticationEmailSentNotificationMessage|E-mail enviado com sucesso para {0}|  
|WebAuthenticationNoAadTenantConfigured|Nenhum inquilino do Azure Ative Directory configurado para o serviço.|  
|Caixa de verificaçãoLabelUserRegistrationTermsConsentRequired|Concordo com `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>` o.|  
|TextblockUserRegistrationTermsProvided|Por favor, reveja `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DiálogoHeadingTermsOfUse|Termos de Utilização|  
|ValidaçãoMessageConsentNotAccepted|Deve concordar com os Termos de Utilização antes de poder prosseguir.|  
  
###  <a name="signinstrings"></a><a name="SigninStrings"></a> SigninStrings  
  
|Name|Texto|  
|----------|----------|  
|WebAuthenticationForgotPassword|Forgot your password?|  
|WebAuthenticationIfAdministrator|Se for administrador, tem de fazer o seu sível `<a href="{0}"\>here</a\>` .|  
|WebAuthenticationNotAMember|Ainda não é um membro? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemembro|Lembre-se de mim neste computador.|  
|WebAuthenticationSigininWithPassword|Iniciar sôr entrada com o seu nome de utilizador e senha|  
|WebAuthenticationSigninTitle|Iniciar sessão|  
|WebAuthenticationSignUpNow|Inscreva-se já|  
  
###  <a name="signupstrings"></a><a name="SignupStrings"></a> Inscrições  
  
|Name|Texto|  
|----------|----------|  
|PageTleSignup|Inscrever-se|  
|WebAuthenticationAlreadyAMember|Já é membro?|  
|WebAuthenticationCreateNewAccount|Criar uma nova conta de Gestão API|  
|WebAuthenticationSigninNow|Inscreva-se agora|  
|ButtonLabelSignup|Inscrever-se|  
  
###  <a name="subscriptionliststrings"></a><a name="SubscriptionListStrings"></a> SubscriçõesListStrings  
  
|Name|Texto|  
|----------|----------|  
|SubscriçãoCancelConfirmation|Tem a certeza de que pretende cancelar esta subscrição?|  
|SubscriçãoRenovaConfirmation|Tem a certeza de que pretende renovar esta subscrição?|  
|WebDevelopersManageSubscriptions|Gerir subscrições|  
|WebDevelopersPrimaryKey|Chave primária|  
|WebDevelopersRegenerateLink|Regenerar|  
|WebDevelopersSecondaryKey|Chave secundária|  
|ButtonLabelShowKey|Mostrar|  
|ButtonLabelRenewSubscrevição|Renovar|  
|WebDevelopersSubscriptionRequested|Solicitado em {0}|  
|WebDevelopersSubscriptionRequestedState|Pedido|  
|WebDevelopersSubscriptionTableNameHeader|Name|  
|WebDevelopersSubscriptionTableStateHeader|Estado|  
|WebDevelopersUsageStatisticsLink|Relatórios de análise|  
|WebDevelopersYourSubscriptions|As suas assinaturas|  
|SubscriçãoPropertyLabelRequestedDate|Solicitado em|  
|SubscriçãoPropertyLabelStartedDate|Começou|  
|PageTitleRenameSubscription|Assinatura de renome|  
|SubscriçãoPropertyLabelName|Nome da subscrição|  
  
###  <a name="subscriptionstrings"></a><a name="SubscriptionStrings"></a> Modelos de subscrição  
  
|Name|Texto|  
|----------|----------|  
|SectionHeadingCloseAccount|Quer fechar a sua conta?|  
|PageTitleDeveloperProfile|Perfil|  
|ButtonLabelHideKey|Ocultar|  
|ButtonLabelRegenerateKey|Regenerar|  
|InformationMessageKeyWasRegerated|Tem certeza que quer regenerar esta chave?|  
|ButtonLabelShowKey|Mostrar|  
  
###  <a name="updateprofilestrings"></a><a name="UpdateProfileStrings"></a> UpdateProfileStrings  
  
|Name|Texto|  
|----------|----------|  
|ButtonLabelUpdateProfile|Atualizar o perfil|  
|PageTitleUpdateProfile|Atualizar informações da conta|  
  
###  <a name="userprofile"></a><a name="UserProfile"></a> UserProfile  
  
|Name|Texto|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Alterar informações de conta|  
|ButtonLabelChangePassword|Alterar palavra-passe|  
|ButtonLabelCloseAccount|Fechar conta|  
|TextboxLabelEmail|E-mail|  
|TextboxLabelEmailFirstName|Nome próprio|  
|TextboxLabelEmailName|Apelido|  
|TextboxLabelNotificationsSenderEmail|E-mail de remetente de notificações|  
|TextboxLabelOrganizaçãoName|Nome da organização|  
|Estado de subscriçãoActivo|Ativo|  
|Estado de subscriçãoCanceled|Cancelada|  
|Estado de subscriçãoExpired|Fora do prazo|  
|Estado de subscrição Reejecto|Rejeitado|  
|Estado de subscrição Rerequested|Pedido|  
|Estado de subscriçãoSuspended|Suspenso|  
|Predefinição SubscriçãoNameTemplate|{0}  (predefinição)|  
|AssinaturaNameTemplate|Acesso ao desenvolvedor #{0}|  
|TextboxLabelSubscriptionName|Nome da subscrição|  
|ValidaçãoMessageSubscriptionNameRequired|O nome da assinatura não pode estar vazio.|  
|ApiManagementUserLimitReached|Este serviço atingiu o número máximo de utilizadores autorizados. Por favor, atualize para um nível de preços mais elevado.|  
  
##  <a name="glyph-resources"></a><a name="glyphs"></a> Recursos glifos  
 Os modelos do portal de desenvolvedores da API Management podem usar os glifos dos [glifos do Bootstrap.](https://getbootstrap.com/components/#glyphicons) Este conjunto de glifos inclui mais de 250 glifos em formato de fonte a partir do conjunto [Glyphicon](https://glyphicons.com/) Halflings. Para utilizar um glifo deste conjunto, utilize a seguinte sintaxe.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Para obter a lista completa de glifos, consulte [os glifos de Bootstrap.](https://getbootstrap.com/components/#glyphicons)

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o trabalho com modelos, consulte [como personalizar o portal de desenvolvedores da API Management utilizando modelos](api-management-developer-portal-templates.md).
