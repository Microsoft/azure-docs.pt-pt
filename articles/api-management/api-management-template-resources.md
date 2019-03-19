---
title: Recursos de modelo de gestão de API do Azure | Documentos da Microsoft
description: Saiba mais sobre os tipos de recursos disponíveis para utilização em modelos de portais de programador na gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: c52565403c7fbc80eacbf65428863db07d634030
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57897287"
---
# <a name="azure-api-management-template-resources"></a>Recursos de modelo de gestão de API do Azure
Gestão de API do Azure fornece os seguintes tipos de recursos para utilização em que o desenvolvedor modelos de portais.  
  
-   [Recursos de cadeia de caracteres](#strings)  
  
-   [Recursos de glifo](#glyphs)  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="strings"></a> Recursos de cadeia de caracteres  
 Gestão de API fornece um conjunto abrangente de recursos de cadeia de caracteres para utilização no portal do programador. Estes recursos são localizados em todos os idiomas suportados pela gestão de API. O conjunto de modelos predefinidos utiliza estes recursos para cabeçalhos de página, etiquetas e qualquer cadeia de caracteres constante que é apresentadas no portal do programador. Para usar um recurso de cadeia de caracteres em seus modelos, forneça o prefixo de cadeia de caracteres de recurso seguido do nome de cadeia de caracteres, conforme mostrado no exemplo a seguir.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 O exemplo seguinte é a partir do modelo de lista do produto e exibe **produtos** na parte superior da página.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
 Consulte as tabelas seguintes para os recursos de cadeia de caracteres disponíveis para utilização nos seus modelos de portal do desenvolvedor. Utilize o nome da tabela como o prefixo para os recursos de cadeia de caracteres dessa tabela.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [Documentação](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [IssuesStrings](#IssuesStrings)  
  
-   [NotFoundStrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProductsStrings](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [SigninStrings](#SigninStrings)  
  
-   [SignupStrings](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [SubscriptionStrings](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="ApisStrings"></a> ApisStrings  
  
|Name|Texto|  
|----------|----------|  
|PageTitleApis|APIs|  
  
###  <a name="AppDetailsStrings"></a> AppDetailsStrings  
  
|Name|Texto|  
|----------|----------|  
|WebApplicationsDetailsTitle|Pré-visualização de aplicações|  
|WebApplicationsRequirementsHeader|Requisitos|  
|WebApplicationsScreenshotAlt|captura de ecrã|  
|WebApplicationsScreenshotsHeader|Capturas de Ecrã|  
  
###  <a name="ApplicationListStrings"></a> ApplicationListStrings  
  
|Name|Texto|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Quer mesmo remover a aplicação?|  
|WebDevelopersAppNotPublished|Não publicado|  
|WebDevelopersAppNotSubmitted|Não submetido|  
|WebDevelopersAppTableCategoryHeader|Categoria|  
|WebDevelopersAppTableNameHeader|Name|  
|WebDevelopersAppTableStateHeader|Estado|  
|WebDevelopersEditLink|Editar|  
|WebDevelopersRegisterAppLink|Registar aplicação|  
|WebDevelopersRemoveLink|Remover|  
|WebDevelopersSubmitLink|Submeter|  
|WebDevelopersYourApplicationsHeader|As suas aplicações|  
  
###  <a name="AppStrings"></a> AppStrings  
  
|Name|Texto|  
|----------|----------|  
|WebApplicationsHeader|Aplicações|  
  
###  <a name="CommonResources"></a> CommonResources  
  
|Name|Texto|  
|----------|----------|  
|NoItemsToDisplay|Não foram encontrados resultados.|  
|GeneralExceptionMessage|Algo não está correto. Pode ser uma falha temporária ou um bug. Tente novamente.|  
|GeneralJsonExceptionMessage|Algo não está correto. Pode ser uma falha temporária ou um bug. Por favor, recarregue a página e tente novamente.|  
|ConfirmationMessageUnsavedChanges|Existem algumas alterações não guardadas. A certeza de que pretende cancelar e eliminar as alterações?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|O Corpo do Pedido HTTP é demasiado grande.|  
  
###  <a name="CommonStrings"></a> CommonStrings  
  
|Name|Texto|  
|----------|----------|  
|ButtonLabelCancel|Cancelar|  
|ButtonLabelSave|Guardar|  
|GeneralExceptionMessage|Algo não está correto. Pode ser uma falha temporária ou um bug. Tente novamente.|  
|NoItemsToDisplay|Não existem itens a apresentar.|  
|PagerButtonLabelFirst|Primeiro|  
|PagerButtonLabelLast|Último|  
|PagerButtonLabelNext|Seguinte|  
|PagerButtonLabelPrevious|Anterior|  
|PagerLabelPageNOfM|Página {0} de {1}|  
|PasswordTooShort|A Palavra-passe é demasiado curta|  
|EmailAsPassword|Não utilize o seu e-mail como palavra-passe|  
|PasswordSameAsUserName|A palavra-passe não pode conter o seu nome de utilizador|  
|PasswordTwoCharacterClasses|Utilize diferentes classes de carateres|  
|PasswordTooManyRepetitions|Demasiadas repetições|  
|PasswordSequenceFound|A palavra-passe contém sequências|  
|PagerLabelPageSize|Tamanho da página|  
|CurtainLabelLoading|A carregar...|  
|TablePlaceholderNothingToDisplay|Não existem dados para o período e âmbito selecionados|  
|ButtonLabelClose|Fechar|  
  
###  <a name="Documentation"></a> Documentação  
  
|Name|Texto|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Cabeçalho inválido '{0}'|  
|WebDocumentationInvalidRequestErrorMessage|URL de Pedido Inválido|  
|TextboxLabelAccessToken|Access token *|  
|DropdownOptionPrimaryKeyFormat|Primário-{0}|  
|DropdownOptionSecondaryKeyFormat|Secondary-{0}|  
|WebDocumentationSubscriptionKeyText|Chave de subscrição|  
|WebDocumentationTemplatesAddHeaders|Adicionar cabeçalhos HTTP necessários|  
|WebDocumentationTemplatesBasicAuthSample|Exemplo de Basic Authorization|  
|WebDocumentationTemplatesCurlForBasicAuth|para Basic Authorization utilize: --user {username}:{password}|  
|WebDocumentationTemplatesCurlValuesForPath|Especifique valores para os parâmetros de caminho (apresentados como {...}), a chave de subscrição e valores para parâmetros de consulta|  
|WebDocumentationTemplatesDeveloperKey|Especifique a chave de subscrição|  
|WebDocumentationTemplatesJavaApache|Este exemplo utiliza o cliente Apache HTTP no HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Especifique valores para parâmetros opcionais, conforme necessário|  
|WebDocumentationTemplatesPhpPackage|Este exemplo utiliza o pacote HTTP_Request2. (para obter mais informações: https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Especifique valores para os parâmetros de caminho (apresentados como {...}) e para o corpo do pedido, se necessário|  
|WebDocumentationTemplatesRequestBody|Especifique o corpo do pedido|  
|WebDocumentationTemplatesRequiredParams|Especifique valores para os parâmetros necessários seguintes|  
|WebDocumentationTemplatesValuesForPath|Especifique valores para os parâmetros de caminho (apresentados como {...})|  
|OAuth2AuthorizationEndpointDescription|O ponto final de autorização é utilizado para interagir com o proprietário do recurso e obter uma concessão de autorização.|  
|OAuth2AuthorizationEndpointName|Ponto final de autorização|  
|OAuth2TokenEndpointDescription|O ponto final do token é utilizado pelo cliente para obter um token de acesso apresentando a concessão de autorização ou o token de atualização.|  
|OAuth2TokenEndpointName|Ponto final do token|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|< p\> o cliente inicia o fluxo ao direcionar agente o proprietário de recursos de utilizador para o ponto final de autorização.  O cliente inclui o seu identificador de cliente, o âmbito pedido, o estado local e um URI de redirecionamento para o qual o servidor de autorização irá enviar o agente de utilizador após acesso é concedido (ou negado).     </p\> < p\> o servidor de autorização autentica o proprietário do recurso (por meio do agente do usuário) e estabelece se o proprietário do recurso concede ou nega o pedido de acesso do cliente.     </p\> < p\> supondo que o proprietário do recurso concede o acesso, o servidor de autorização redireciona o agente de utilizador para o cliente utilizando o URI fornecido do redirecionamento anterior (no pedido ou durante a registrati de cliente ativado).  O URI de redirecionamento inclui um código de autorização e qualquer Estado local fornecido pelo cliente anteriormente.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|< p\> se o utilizador Negar o pedido de se o pedido é inválido, o cliente será informado com os seguintes parâmetros adicionados ao redirecionamento: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Pedido de autorização|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|< p\> a aplicação cliente tem de enviar o utilizador para o ponto final de autorização para iniciar o processo OAuth.          No ponto de final de autorização, o utilizador autentica e, em seguida, concede ou nega o acesso à aplicação.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|< p\> supondo que o proprietário do recurso concede o acesso, o servidor de autorização redireciona o agente de utilizador para o cliente utilizando o URI fornecido do redirecionamento anterior (no pedido ou durante o registo de cliente).  O URI de redirecionamento inclui um código de autorização e qualquer Estado local fornecido pelo cliente anteriormente. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|< p\> o cliente solicita um token de acesso do servidor de autorização ' ponto final do token s, incluindo o código de autorização recebido no passo anterior.  Quando efetua o pedido, o cliente efetua a autenticação com o servidor de autorização.  O cliente inclui o URI utilizado para obter o código de autorização para a verificação de redirecionamento. </p\> < p\> o servidor de autorização autentica o cliente, valida o código de autorização e garante que o redirecionamento URI recebido corresponda o URI usado no redirecionamento do cliente no passo (C).  Caso seja válido, o servidor de autorização responde com um token de acesso e, opcionalmente, um token de atualização. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|< p\> se a autenticação de cliente de solicitação falha ou é inválida, o servidor de autorização responde com um código de estado HTTP 400 (pedido incorreto) (a menos que especificado de outra forma) e inclui os seguintes parâmetros com a resposta. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|< p\> o cliente faz um pedido ao ponto final do token através do envio de corpo de entidade do pedido dos seguintes parâmetros com o formato "application/x-www-form-urlencoded" com uma codificação de carateres de UTF-8 no HTTP. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|< p\> o servidor de autorização emite um token de acesso e o token de atualização opcional e cria a resposta adicionando os seguintes parâmetros ao corpo da entidade de resposta HTTP com um código de 200 Estado (OK). </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|< p\> o cliente é autenticado com o servidor de autorização e solicita um token de acesso do ponto final do token. </p\> < p\> o servidor de autorização autentica o cliente e, caso seja válido, emite um token de acesso. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> se o pedido falhou a autenticação de cliente ou for inválido o servidor de autorização responde com um código de estado HTTP 400 (pedido incorreto) (a menos que especificado de outra forma) e inclui os seguintes parâmetros com a resposta. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> o cliente faz um pedido ao ponto final do token adicionando os seguintes parâmetros com o formato "application/x-www-form-urlencoded" com uma codificação de carateres de UTF-8 no HTTP corpo de entidade do pedido. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> se o pedido de token de acesso for válido e estiver autorizado, o servidor de autorização emite um token de acesso e o token de atualização opcional e cria a resposta adicionando os seguintes parâmetros ao corpo da entidade de resposta HTTP com um 200  Código de estado (OK). </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|< p\> o cliente inicia o fluxo ao direcionar o proprietário do recurso ' s-agente de utilizador para o ponto final de autorização.  O cliente inclui o seu identificador de cliente, o âmbito pedido, o estado local e um URI de redirecionamento para o qual o servidor de autorização irá enviar o agente de utilizador após acesso é concedido (ou negado). </p\> < p\> o servidor de autorização autentica o proprietário do recurso (por meio do agente do usuário) e estabelece se o proprietário do recurso concede ou nega o cliente ' pedido de acesso de s. </p\> < p\> supondo que o proprietário do recurso concede o acesso, o servidor de autorização redireciona o agente de utilizador para o cliente usando o redirecionamento URI fornecido anteriormente.  O URI de redirecionamento inclui o token de acesso, o fragmento do URI. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|< p\> se o proprietário do recurso negar o pedido de acesso ou se o pedido falhar por motivos que não seja um em falta ou inválido URI de redirecionamento, o servidor de autorização informa o cliente adicionando os seguintes parâmetros para a o componente de fragmento f o redirecionamento de URI usando o formato "application/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|< p\> a aplicação cliente tem de enviar o utilizador para o ponto final de autorização para iniciar o processo OAuth.      No ponto de final de autorização, o utilizador autentica e, em seguida, concede ou nega o acesso à aplicação. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|< p\> se o proprietário do recurso conceder o pedido de acesso, o servidor de autorização emite um token de acesso e o disponibiliza ao cliente adicionando os seguintes parâmetros ao componente de fragmento de redirecionamento de URI usando o "application/x-www formato - form - urlencoded". </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|O fluxo do código de autorização está otimizado para clientes capazes de manter a confidencialidade das credenciais (por exemplo, aplicações de servidor Web implementadas com PHP, Java, Python, Ruby, ASP.NET, etc.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Concessão do Código de Autorização|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Fluxo de credenciais de cliente é adequado em casos em que o cliente (a sua aplicação) está a pedir acesso a recursos protegidos sob seu controlo. O cliente é considerado como um proprietário do recurso, pelo que nenhuma interação do utilizador final é necessária.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Concessão de Credenciais de Cliente|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Fluxo implícito está otimizado para clientes incapazes de manter a confidencialidade das credenciais conhecidas para operar uma URI de redirecção específico. Estes clientes normalmente são implementados num navegador usando uma linguagem de script como JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Concessão implícita|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Fluxo de credenciais de palavra-passe de proprietário do recurso é adequado em casos em que o proprietário do recurso tem uma relação de confiança com o cliente (a sua aplicação), como o sistema operativo do dispositivo ou um aplicativo com privilégios elevados. Este fluxo é adequado para clientes com capacidade de obter o recurso de credenciais do proprietário (nome de utilizador e palavra-passe, normalmente com um formulário interativo).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Concessão de Credenciais de Palavra-Passe do Proprietário do Recurso|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|< p\> o proprietário do recurso fornece ao cliente com o seu nome de utilizador e palavra-passe. </p\> < p\> o cliente solicita um token de acesso do servidor de autorização ' ponto final do token s, incluindo as credenciais recebidas do proprietário do recurso.  Quando efetua o pedido, o cliente efetua a autenticação com o servidor de autorização. </p\> < p\> o servidor de autorização autentica o cliente e valida as credenciais de proprietário do recurso e, caso seja válido, emite um token de acesso. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> se o pedido falhou a autenticação de cliente ou for inválido o servidor de autorização responde com um código de estado HTTP 400 (pedido incorreto) (a menos que especificado de outra forma) e inclui os seguintes parâmetros com a resposta. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> o cliente faz um pedido ao ponto final do token adicionando os seguintes parâmetros com o formato "application/x-www-form-urlencoded" com uma codificação de carateres de UTF-8 no HTTP corpo de entidade do pedido. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> se o pedido de token de acesso for válido e estiver autorizado, o servidor de autorização emite um token de acesso e o token de atualização opcional e cria a resposta adicionando os seguintes parâmetros ao corpo da entidade de resposta HTTP com um 20 Código de estado (OK) 0. </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Pedido de token de acesso|  
|OAuth2Step_AuthorizationRequest_Name|Pedido de autorização|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|É NECESSÁRIO. O token de acesso emitido pelo servidor de autorização.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|É NECESSÁRIO. O token de acesso emitido pelo servidor de autorização.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|É NECESSÁRIO. O token de acesso emitido pelo servidor de autorização.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|É NECESSÁRIO. O token de acesso emitido pelo servidor de autorização.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|É NECESSÁRIO. Identificador do cliente.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|OBRIGATÓRIO se o cliente não estiver a autenticar com o servidor de autorização.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|É NECESSÁRIO. O identificador de cliente.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|É NECESSÁRIO. O código de autorização gerado pelo servidor de autorização.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|É NECESSÁRIO. O código de autorização recebido do servidor de autorização.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|OPCIONAL. Texto de ASCII legível por humanos fornecer informações adicionais.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|OPCIONAL. Texto de ASCII legível por humanos fornecer informações adicionais.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|OPCIONAL. Texto de ASCII legível por humanos fornecer informações adicionais.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|OPCIONAL. Texto de ASCII legível por humanos fornecer informações adicionais.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OPCIONAL. Texto de ASCII legível por humanos fornecer informações adicionais.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|OPCIONAL. Um URI que identifica uma página web legível por humanos com informações sobre o erro.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|OPCIONAL. Um URI que identifica uma página web legível por humanos com informações sobre o erro.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|OPCIONAL. Um URI que identifica uma página web legível por humanos com informações sobre o erro.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|OPCIONAL. Um URI que identifica uma página web legível por humanos com informações sobre o erro.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|OPCIONAL. Um URI que identifica uma página web legível por humanos com informações sobre o erro.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|É NECESSÁRIO. Um único código de erro ASCII dos seguintes: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|É NECESSÁRIO. Um único código de erro ASCII dos seguintes: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|É NECESSÁRIO. Um único código de erro ASCII dos seguintes: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|É NECESSÁRIO. Um único código de erro ASCII dos seguintes: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|É NECESSÁRIO. Um único código de erro ASCII dos seguintes: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|RECOMENDADO. A duração em segundos do token de acesso.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|RECOMENDADO. A duração em segundos do token de acesso.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|RECOMENDADO. A duração em segundos do token de acesso.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|RECOMENDADO. A duração em segundos do token de acesso.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|É NECESSÁRIO. Valor deve ser definido para "authorization_code".|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|É NECESSÁRIO. Valor deve ser definido para "client_credentials".|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|É NECESSÁRIO. Valor deve ser definido para "password".|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|É NECESSÁRIO. A palavra-passe de proprietário de recursos.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|OPCIONAL. O URI do ponto final do redirecionamento tem de ser um URI absoluto.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|OBRIGATÓRIO se o parâmetro "redirect_uri" foi incluindo no pedido de autorização e os seus valores TIVEREM DE ser idênticos.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|OPCIONAL. O URI do ponto final do redirecionamento tem de ser um URI absoluto.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|OPCIONAL. O token de atualização, que pode ser utilizado para obter novos tokens de acesso.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|OPCIONAL. O token de atualização, que pode ser utilizado para obter novos tokens de acesso.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPCIONAL. O token de atualização, que pode ser utilizado para obter novos tokens de acesso.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|É NECESSÁRIO. Valor deve ser definido para "code".|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|É NECESSÁRIO. O valor tem de ser definido para "token".|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|OPCIONAL. O âmbito do pedido de acesso.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|OPCIONAL se idêntico ao âmbito pedido pelo cliente; de outro modo, OBRIGATÓRIO.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|OPCIONAL. O âmbito do pedido de acesso.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|OPCIONAL, se idêntico ao âmbito pedido pelo cliente; de outro modo, OBRIGATÓRIO.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|OPCIONAL. O âmbito do pedido de acesso.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|OPCIONAL se idêntico ao âmbito pedido pelo cliente; de outro modo, OBRIGATÓRIO.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|OPCIONAL. O âmbito do pedido de acesso.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPCIONAL, se idêntico ao âmbito pedido pelo cliente; de outro modo, OBRIGATÓRIO.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|NECESSÁRIO se o parâmetro "state" estava presente no pedido de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|RECOMENDADO. Um valor indefinido utilizado pelo cliente para manter o estado entre o pedido e o retorno de chamada.  O servidor de autorização inclui este valor ao redirecionar o agente de utilizador para o cliente.  O parâmetro deve ser utilizado para prevenir falsificação de pedidos entre sites entre.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|NECESSÁRIO se o parâmetro "state" estava presente no pedido de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|NECESSÁRIO se o parâmetro "state" estava presente no pedido de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|RECOMENDADO. Um valor indefinido utilizado pelo cliente para manter o estado entre o pedido e o retorno de chamada.  O servidor de autorização inclui este valor ao redirecionar o agente de utilizador para o cliente.  O parâmetro deve ser utilizado para prevenir falsificação de pedidos entre sites entre.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|NECESSÁRIO se o parâmetro "state" estava presente no pedido de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|É NECESSÁRIO. O tipo de token emitido.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|É NECESSÁRIO. O tipo de token emitido.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|É NECESSÁRIO. O tipo de token emitido.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|É NECESSÁRIO. O tipo de token emitido.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|É NECESSÁRIO. O nome de utilizador de proprietário do recurso.|  
|OAuth2UnsupportedTokenType|Tipo de token "{0}' não é suportada.|  
|OAuth2InvalidState|Resposta inválida do servidor de autorização|  
|OAuth2GrantType_AuthorizationCode|Código de autorização|  
|OAuth2GrantType_Implicit|Implícito|  
|OAuth2GrantType_ClientCredentials|Credenciais de cliente|  
|OAuth2GrantType_ResourceOwnerPassword|Palavra-passe do proprietário de recurso|  
|WebDocumentation302Code|302 Encontrado|  
|WebDocumentation400Code|400 (Pedido incorreto)|  
|OAuth2SendingMethod_AuthHeader|Cabeçalho de autorização|  
|OAuth2SendingMethod_QueryParam|Parâmetro de consulta|  
|OAuth2AuthorizationServerGeneralException|Ocorreu um erro ao autorizar o acesso através de {0}|  
|OAuth2AuthorizationServerCommunicationException|Não foi possível estabelecer uma ligação HTTP ao servidor de autorização ou a mesma foi fechada inesperadamente.|  
|WebDocumentationOAuth2GeneralErrorMessage|Ocorreu um erro inesperado.|  
|AuthorizationServerCommunicationException|Ocorreu uma exceção de comunicação do servidor de autorização. Contacte o administrador.|  
|TextblockSubscriptionKeyHeaderDescription|Chave de assinatura que fornece acesso a esta API. Foi encontrado no seu < href ='/ desenvolvedor '\>perfil < /a\>.|  
|TextblockOAuthHeaderDescription|Token de acesso de OAuth 2.0 obtido a partir do < eu\>{0}</i\>. Tipos de concessão suportados: < eu\>{1}</i\>.|  
|TextblockContentTypeHeaderDescription|Tipo de multimédia do corpo enviado para a API.|  
|ErrorMessageApiNotAccessible|A API que está a tentar chamar não está acessível neste momento. Entre em contato com o publicador da API < href = "/issues"\>aqui < /a\>.|  
|ErrorMessageApiTimedout|A API que está a tentar chamar está a demorar mais tempo do que o normal a devolver uma resposta de volta. Entre em contato com o publicador da API < href = "/issues"\>aqui < /a\>.|  
|BadRequestParameterExpected|"'{0}" é esperado o parâmetro "|  
|TooltipTextDoubleClickToSelectAll|Faça duplo clique para selecionar tudo.|  
|TooltipTextHideRevealSecret|Mostrar/Ocultar|  
|ButtonLinkOpenConsole|Experimente|  
|SectionHeadingRequestBody|Corpo do pedido|  
|SectionHeadingRequestParameters|Parâmetros do pedido|  
|SectionHeadingRequestUrl|URL do Pedido|  
|SectionHeadingResponse|Resposta|  
|SectionHeadingRequestHeaders|Cabeçalhos do pedido|  
|FormLabelSubtextOptional|opcional|  
|SectionHeadingCodeSamples|Exemplos de código|  
|TextblockOpenidConnectHeaderDescription|Token de id OpenID Connect obtido a partir do < eu\>{0}</i\>. Tipos de concessão suportados: < eu\>{1}</i\>.|  
  
###  <a name="ErrorPageStrings"></a> ErrorPageStrings  
  
|Name|Texto|  
|----------|----------|  
|LinkLabelBack|anterior|  
|LinkLabelHomePage|home page|  
|LinkLabelSendUsEmail|Envie-nos um e-mail|  
|PageTitleError|Ocorreu um problema ao processar a página pedida|  
|TextblockPotentialCauseIntermittentIssue|Isto pode ser um problema de acesso de dados intermitente que já não existe mais.|  
|TextblockPotentialCauseOldLink|A ligação na qual clicou pode ser antiga e já não apontar para a localização correta.|  
|TextblockPotentialCauseTechnicalProblem|Pode haver um problema técnico do nosso lado.|  
|TextblockPotentialSolutionRefresh|Tente atualizar a página.|  
|TextblockPotentialSolutionStartOver|Iniciar do nosso {0}.|  
|TextblockPotentialSolutionTryAgain|Vá {0} e repita a ação efetuada novamente.|  
|TextReportProblem|{0} descrever o que aconteceu de errado e veremos-lo assim que possível.|  
|TitlePotentialCause|Causa potencial|  
|TitlePotentialSolution|Possivelmente é apenas um problema temporário, algumas coisas a tentar|  
  
###  <a name="IssuesStrings"></a> IssuesStrings  
  
|Name|Texto|  
|----------|----------|  
|WebIssuesIndexTitle|Problemas|  
|WebIssuesNoActiveSubscriptions|Tem que não existem subscrições ativas. Tem de subscrever um produto comunicar um problema.|  
|WebIssuesNotSignin|Não tem sessão iniciada. Tente {0} para comunicar um problema ou publicar um comentário.|  
|WebIssuesReportIssueButton|Comunicar Problema|  
|WebIssuesSignIn|iniciar sessão|  
|WebIssuesStatusReportedBy|Estado: {0} &#124; comunicado pelo {1}|  
  
###  <a name="NotFoundStrings"></a> NotFoundStrings  
  
|Name|Texto|  
|----------|----------|  
|LinkLabelHomePage|home page|  
|LinkLabelSendUsEmail|envie-nos um e-mail|  
|PageTitleNotFound|Lamentamos, mas não foi possível encontrar a página que procura|  
|TextblockPotentialCauseMisspelledUrl|Poderá ter escrito incorretamente o URL caso o tenha digitado no.|  
|TextblockPotentialCauseOldLink|A ligação na qual clicou pode ser antiga e já não apontar para a localização correta.|  
|TextblockPotentialSolutionRetype|Tente voltar a escrever o URL.|  
|TextblockPotentialSolutionStartOver|Iniciar do nosso {0}.|  
|TextReportProblem|{0} descrever o que aconteceu de errado e veremos-lo assim que possível.|  
|TitlePotentialCause|Causa potencial|  
|TitlePotentialSolution|Solução potencial|  
  
###  <a name="ProductDetailsStrings"></a> ProductDetailsStrings  
  
|Name|Texto|  
|----------|----------|  
|WebProductsAgreement|Ao subscrever {0} produto, eu Concordo com os `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`.|  
|WebProductsLegalTermsLink|Termos de Utilização|  
|WebProductsSubscribeButton|Subscrever|  
|WebProductsUsageLimitsHeader|Limites de utilização|  
|WebProductsYouAreNotSubscribed|Está subscrito neste produto.|  
|WebProductsYouRequestedSubscription|Pediu a subscrição neste produto.|  
|ErrorYouNeedToAgreeWithLegalTerms|Tem de concordar com os Termos de Utilização antes de poder prosseguir.|  
|ButtonLabelAddSubscription|Adicionar subscrição|  
|LinkLabelChangeSubscriptionName|alterar|  
|ButtonLabelConfirm|Confirmar|  
|TextblockMultipleSubscriptionsCount|Tem {0} subscrições neste produto:|  
|TextblockSingleSubscriptionsCount|Tem {0} subscrição neste produto:|  
|TextblockSingleApisCount|Este produto contém {0} API:|  
|TextblockMultipleApisCount|Este produto contém {0} APIs:|  
|TextblockHeaderSubscribe|Subscrever o produto|  
|TextblockSubscriptionDescription|Será criada uma nova subscrição do seguinte modo:|  
|TextblockSubscriptionLimitReached|Limite da subscrição atingido.|  
  
###  <a name="ProductsStrings"></a> ProductsStrings  
  
|Name|Texto|  
|----------|----------|  
|PageTitleProducts|Produtos|  
  
###  <a name="ProviderInfoStrings"></a> ProviderInfoStrings  
  
|Name|Texto|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|De momento, o início de sessão está desativado pelos administradores.|  
|TextboxExternalIdentitiesSigninInvitation|Em alternativa, inicie sessão com|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Iniciar sessão com:|  
  
###  <a name="SigninResources"></a> SigninResources  
  
|Name|Texto|  
|----------|----------|  
|PrincipalNotFound|O principal não foi encontrado ou a assinatura é inválida|  
|ErrorSsoAuthenticationFailed|Falha de autenticação SSO|  
|ErrorSsoAuthenticationFailedDetailed|Foi fornecido um token inválido ou não é possível verificar a assinatura.|  
|ErrorSsoTokenInvalid|O token de SSO é inválido|  
|ValidationErrorSpecificEmailAlreadyExists|E-mail '{0}' já está registado|  
|ValidationErrorSpecificEmailInvalid|E-mail '{0}"é inválido|  
|ValidationErrorPasswordInvalid|Palavra-passe é inválida. Corrija os erros e tente novamente.|  
|PropertyTooShort|{0} é demasiado curta|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Endereço de e-mail inválido.|  
|ValidationMessageNewPasswordConfirmationRequired|Confirmar nova palavra-passe|  
|ValidationErrorPasswordConfirmationRequired|A confirmação da palavra-passe está vazia|  
|WebAuthenticationEmailChangeNotice|E-mail de confirmação de alteração vai a caminho de {0}. Siga as instruções dentro dele para confirmar o seu novo endereço de e-mail. Se receber o e-mail para a pasta a receber nos próximos minutos, verifique a pasta de e-mail de lixo.|  
|WebAuthenticationEmailChangeNoticeHeader|O seu pedido de alteração de e-mail foi processado com êxito|  
|WebAuthenticationEmailChangeNoticeTitle|Foi pedida a alteração do e-mail|  
|WebAuthenticationEmailHasBeenRevertedNotice|Seu e-mail já existe. Pedido foi revertido|  
|ValidationErrorEmailAlreadyExists|O e-mail já existe|  
|ValidationErrorEmailInvalid|Endereço de e-mail inválido|  
|TextboxLabelEmail|Email|  
|ValidationErrorEmailRequired|O e-mail é obrigatório.|  
|WebAuthenticationErrorNoticeHeader|Erro|  
|WebAuthenticationFieldLengthErrorMessage|{0} tem de ser um comprimento máximo de {1}|  
|TextboxLabelEmailFirstName|Nome próprio|  
|ValidationErrorFirstNameRequired|O primeiro nome é obrigatório.|  
|ValidationErrorFirstNameInvalid|Nome próprio inválido|  
|NoticeInvalidInvitationToken|Tenha em atenção que as ligações de confirmação são válidas durante apenas 48 horas. Se ainda não tiver ultrapassado este período de tempo, certifique-se de que a sua ligação está correta. Se a ligação tiver expirado, repita a ação para confirmar que está a tentar.|  
|NoticeHeaderInvalidInvitationToken|Token de convite inválido|  
|NoticeTitleInvalidInvitationToken|Erro de confirmação|  
|WebAuthenticationLastNameInvalidErrorMessage|Apelido inválido|  
|TextboxLabelEmailLastName|Apelido|  
|ValidationErrorLastNameRequired|O apelido é obrigatório.|  
|WebAuthenticationLinkExpiredNotice|Ligação de confirmação é enviada a expirou. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|A sua ligação de reposição de palavra-passe é inválida ou expirou.|  
|WebAuthenticationLinkExpiredNoticeTitle|Ligação enviada|  
|WebAuthenticationNewPasswordLabel|Nova palavra-passe|  
|ValidationMessageNewPasswordRequired|É obrigatória uma nova palavra-passe.|  
|TextboxLabelNotificationsSenderEmail|E-mail do remetente das notificações|  
|TextboxLabelOrganizationName|Nome da organização|  
|WebAuthenticationOrganizationRequiredErrorMessage|O nome da organização está vazio|  
|WebAuthenticationPasswordChangedNotice|A palavra-passe foi atualizada com êxito|  
|WebAuthenticationPasswordChangedNoticeTitle|Palavra-passe atualizada|  
|WebAuthenticationPasswordCompareErrorMessage|As palavras-passe não coincidem|  
|WebAuthenticationPasswordConfirmLabel|Confirmar palavra-passe|  
|ValidationErrorPasswordInvalidDetailed|A palavra-passe é demasiado fraca.|  
|WebAuthenticationPasswordLabel|Palavra-passe|  
|ValidationErrorPasswordRequired|A palavra-passe é obrigatória.|  
|WebAuthenticationPasswordResetSendNotice|E-mail de confirmação de palavra-passe de alteração é o caminho de {0}. Siga as instruções no e-mail para continuar o processo de alteração de palavra-passe.|  
|WebAuthenticationPasswordResetSendNoticeHeader|O pedido de reposição de palavra-passe foi processado com êxito|  
|WebAuthenticationPasswordResetSendNoticeTitle|Foi pedida a reposição de palavra-passe|  
|WebAuthenticationRequestNotFoundNotice|O pedido não foi encontrado|  
|WebAuthenticationSenderEmailRequiredErrorMessage|O e-mail do remetente das notificações está vazio|  
|WebAuthenticationSigninPasswordLabel|Introduza uma palavra-passe para confirmar a alteração|  
|WebAuthenticationSignupConfirmNotice|E-mail de confirmação do registo está a caminho para {0}. < br /\> Siga instruções no e-mail para ativar a sua conta. < br /\> se receber o e-mail na pasta a receber nos próximos minutos, Verifique a pasta de e-mail de lixo.|  
|WebAuthenticationSignupConfirmNoticeHeader|A sua conta foi criada com êxito|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|O e-mail de confirmação do registo foi reenviado|  
|WebAuthenticationSignupConfirmNoticeTitle|Conta criada|  
|WebAuthenticationTokenRequiredErrorMessage|O token está vazio|  
|WebAuthenticationUserAlreadyRegisteredNotice|Parece que um utilizador com este e-mail já está registado no sistema. Se se esqueceu a palavra-passe, tente restaurá-lo ou contacte a nossa equipa de suporte.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|O utilizador já está registado|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Já está registado|  
|ButtonLabelChangePassword|Alterar palavra-passe|  
|ButtonLabelChangeAccountInfo|Alterar informações da conta|  
|ButtonLabelCloseAccount|Fechar conta|  
|WebAuthenticationInvalidCaptchaErrorMessage|O texto introduzido não corresponde ao texto na imagem. Tente novamente.|  
|ValidationErrorCredentialsInvalid|E-mail ou palavra-passe é inválida. Corrija os erros e tente novamente.|  
|WebAuthenticationRequestIsNotValid|O pedido não é válido|  
|WebAuthenticationUserIsNotConfirm|Confirme o registo antes de tentar iniciar sessão.|  
|WebAuthenticationInvalidEmailFormated|O e-mail é inválido: {0}|  
|WebAuthenticationUserNotFound|Utilizador não encontrado|  
|WebAuthenticationTenantNotRegistered|Sua conta pertencer a um inquilino do Azure Active Directory que não está autorizado a aceder a este portal.|  
|WebAuthenticationAuthenticationFailed|A autenticação falhou.|  
|WebAuthenticationGooglePlusNotEnabled|A autenticação falhou. Se autorizou a aplicação, em seguida,. Contacte o administrador para se certificar de que a autenticação do Google está configurado corretamente.|  
|ValidationErrorAllowedTenantIsRequired|O Inquilino Permitido é obrigatório|  
|ValidationErrorTenantIsNotValid|O inquilino do Azure Active Directory '{0}' não é válido.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Inicie sessão com a sua {0} conta|  
|WebAuthenticationUserLimitNotice|Este serviço atingiu o número máximo de utilizadores permitidos. Tente `<a href="mailto:{0}"\>contact the administrator</a\>` para atualizar o serviço e volte a ativar o registo de utilizador.|  
|WebAuthenticationUserLimitNoticeHeader|Registo de utilizadores desativado|  
|WebAuthenticationUserLimitNoticeTitle|Registo de utilizadores desativado|  
|WebAuthenticationUserRegistrationDisabledNotice|O registo de utilizadores foi desativado pelo administrador. Inicie sessão com o fornecedor de identidade externo.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Registo de utilizadores desativado|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Registo de utilizadores desativado|  
|WebAuthenticationSignupPendingConfirmationNotice|Antes de concluirmos a criação da sua conta, precisamos verificar o seu endereço de email. Enviámos um e-mail para {0}. Siga as instruções dentro do email para ativar a sua conta. Se não receber nos próximos minutos, verifique a pasta de e-mail de lixo.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Encontrámos uma conta não confirmada para o endereço de email {0}. Para concluir a criação da sua conta, precisamos de verificar o seu endereço de email. Enviámos um e-mail para {0}. Siga as instruções dentro do email para ativar a sua conta. Se não receber nos próximos minutos, verifique a pasta de e-mail de lixo|  
|WebAuthenticationSignupConfirmationAlmostDone|Está Quase Pronto|  
|WebAuthenticationSignupConfirmationEmailSent|Enviámos um e-mail para {0}. Siga as instruções dentro do email para ativar a sua conta. Se não receber nos próximos minutos, verifique a pasta de e-mail de lixo.|  
|WebAuthenticationEmailSentNotificationMessage|E-mail enviado com êxito para {0}|  
|WebAuthenticationNoAadTenantConfigured|Não existe nenhum inquilino do Azure Active Directory configurado para o serviço.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Eu concordo com os `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`.|  
|TextblockUserRegistrationTermsProvided|Consulte `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Termos de Utilização|  
|ValidationMessageConsentNotAccepted|Tem de concordar com os Termos de Utilização antes de poder prosseguir.|  
  
###  <a name="SigninStrings"></a> SigninStrings  
  
|Name|Texto|  
|----------|----------|  
|WebAuthenticationForgotPassword|Esqueceu-se da palavra-passe?|  
|WebAuthenticationIfAdministrator|Se for um administrador tem de iniciar sessão `<a href="{0}"\>here</a\>`.|  
|WebAuthenticationNotAMember|Ainda não é membro? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Lembrar-me neste computador|  
|WebAuthenticationSigininWithPassword|Iniciar sessão com o nome de utilizador e palavra-passe|  
|WebAuthenticationSigninTitle|Iniciar sessão|  
|WebAuthenticationSignUpNow|Inscreva-se já|  
  
###  <a name="SignupStrings"></a> SignupStrings  
  
|Name|Texto|  
|----------|----------|  
|PageTitleSignup|Inscrever-se|  
|WebAuthenticationAlreadyAMember|Já é membro?|  
|WebAuthenticationCreateNewAccount|Criar uma nova conta de Gestão de API|  
|WebAuthenticationSigninNow|Iniciar sessão agora|  
|ButtonLabelSignup|Inscrever-se|  
  
###  <a name="SubscriptionListStrings"></a> SubscriptionListStrings  
  
|Name|Texto|  
|----------|----------|  
|SubscriptionCancelConfirmation|Quer mesmo cancelar esta subscrição?|  
|SubscriptionRenewConfirmation|Quer mesmo renovar esta subscrição?|  
|WebDevelopersManageSubscriptions|Gerir subscrições|  
|WebDevelopersPrimaryKey|Chave primária|  
|WebDevelopersRegenerateLink|Regenerar|  
|WebDevelopersSecondaryKey|Chave secundária|  
|ButtonLabelShowKey|Mostrar|  
|ButtonLabelRenewSubscription|Renovar|  
|WebDevelopersSubscriptionRequested|Pedido em {0}|  
|WebDevelopersSubscriptionRequestedState|Pedido|  
|WebDevelopersSubscriptionTableNameHeader|Name|  
|WebDevelopersSubscriptionTableStateHeader|Estado|  
|WebDevelopersUsageStatisticsLink|Relatórios de análise|  
|WebDevelopersYourSubscriptions|As suas subscrições|  
|SubscriptionPropertyLabelRequestedDate|Pedido em|  
|SubscriptionPropertyLabelStartedDate|Iniciadas em|  
|PageTitleRenameSubscription|Mudar o nome da subscrição|  
|SubscriptionPropertyLabelName|Nome da subscrição|  
  
###  <a name="SubscriptionStrings"></a> SubscriptionStrings  
  
|Name|Texto|  
|----------|----------|  
|SectionHeadingCloseAccount|Quer fechar a sua conta?|  
|PageTitleDeveloperProfile|Perfil|  
|ButtonLabelHideKey|Ocultar|  
|ButtonLabelRegenerateKey|Regenerar|  
|InformationMessageKeyWasRegenerated|Quer mesmo voltar a gerar a chave?|  
|ButtonLabelShowKey|Mostrar|  
  
###  <a name="UpdateProfileStrings"></a> UpdateProfileStrings  
  
|Name|Texto|  
|----------|----------|  
|ButtonLabelUpdateProfile|Atualizar perfil|  
|PageTitleUpdateProfile|Atualizar informações da conta|  
  
###  <a name="UserProfile"></a> UserProfile  
  
|Name|Texto|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Alterar informações da conta|  
|ButtonLabelChangePassword|Alterar palavra-passe|  
|ButtonLabelCloseAccount|Fechar conta|  
|TextboxLabelEmail|Email|  
|TextboxLabelEmailFirstName|Nome próprio|  
|TextboxLabelEmailLastName|Apelido|  
|TextboxLabelNotificationsSenderEmail|E-mail do remetente das notificações|  
|TextboxLabelOrganizationName|Nome da organização|  
|SubscriptionStateActive|Ativa|  
|SubscriptionStateCancelled|Cancelado|  
|SubscriptionStateExpired|Fora do prazo|  
|SubscriptionStateRejected|Rejeitado|  
|SubscriptionStateRequested|Pedido|  
|SubscriptionStateSuspended|Suspenso|  
|DefaultSubscriptionNameTemplate|{0}  (predefinição)|  
|SubscriptionNameTemplate|N. º de acesso de programador{0}|  
|TextboxLabelSubscriptionName|Nome da subscrição|  
|ValidationMessageSubscriptionNameRequired|O nome da subscrição não pode estar vazio.|  
|ApiManagementUserLimitReached|Este serviço atingiu o número máximo de utilizadores permitidos. Atualize para um escalão de preço superior.|  
  
##  <a name="glyphs"></a> Recursos de glifo  
 Modelos de portais de Programador de gestão de API podem utilizar os glifos partir [Glyphicons de arranque de](https://getbootstrap.com/components/#glyphicons). Este conjunto de glifos inclui mais de 250 glifos no formato de tipo de letra a partir da [Glyphicon](https://glyphicons.com/) Halflings definido. Para utilizar um glifo a partir desse conjunto, utilize a seguinte sintaxe.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Para obter a lista completa de glifos, consulte [Glyphicons de arranque de](https://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de gestão de API através de modelos](api-management-developer-portal-templates.md).
