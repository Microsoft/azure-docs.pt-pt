---
title: Recursos do modelo de gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre os tipos de recursos disponíveis para uso em modelos de portal do desenvolvedor no gerenciamento de API do Azure.
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
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176510"
---
# <a name="azure-api-management-template-resources"></a>Recursos de modelo de gerenciamento de API do Azure
O gerenciamento de API do Azure fornece os seguintes tipos de recursos para uso nos modelos de portal do desenvolvedor.  
  
-   [Recursos de cadeia de caracteres](#strings)  
  
-   [Recursos de glifo](#glyphs)  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="strings"></a>Recursos de cadeia de caracteres  
 O gerenciamento de API fornece um conjunto abrangente de recursos de cadeia de caracteres para uso no portal do desenvolvedor. Esses recursos estão localizados em todos os idiomas com suporte do gerenciamento de API. O conjunto padrão de modelos usa esses recursos para cabeçalhos de página, rótulos e quaisquer cadeias de caracteres constantes que são exibidas no portal do desenvolvedor. Para usar um recurso de cadeia de caracteres em seus modelos, forneça o prefixo da cadeia de caracteres do recurso seguido pelo nome da cadeia de caracteres, conforme mostrado no exemplo a seguir.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 O exemplo a seguir é do modelo de lista de produtos e exibe **produtos** na parte superior da página.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
Há suporte para as seguintes opções de localização:

| Região    | Linguagem               |
|-----------|------------------------|
| "en"      | Portuguesa              |
| CS      | "Čeština"              |
| deprecia      | Deutsch              |
| espanhol      | Espanhol              |
| fr      | Français             |
| Hu      | Magyar               |
| fosse      | Italiano             |
| "ja-JP"   | "日本語"                |
| Ko      | "한국어"                |
| NL      | Baixos           |
| p      | Polski               |
| "pt-br"   | "Português (Brasil)"   |
| "pt-pt"   | "Português (Portugal)" |
| executa      | "Русский"              |
| SV      | Svenska              |
| TR      | Türkçe               |
| "zh-Hans" | "中文(简体)"           |
| "zh-Hant" | "中文(繁體)"           |

 Consulte as tabelas a seguir para obter os recursos de cadeia de caracteres disponíveis para uso em seus modelos de portal do desenvolvedor. Use o nome da tabela como o prefixo para os recursos de cadeia de caracteres nessa tabela.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [Sobre](#Documentation)  
  
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
  
-   [Perfil](#UserProfile)  
  
###  <a name="ApisStrings"></a>ApisStrings  
  
|Nome|Texto|  
|----------|----------|  
|PageTitleApis|APIs|  
  
###  <a name="AppDetailsStrings"></a>AppDetailsStrings  
  
|Nome|Texto|  
|----------|----------|  
|WebApplicationsDetailsTitle|Visualização do aplicativo|  
|WebApplicationsRequirementsHeader|Requisitos|  
|WebApplicationsScreenshotAlt|Captura|  
|WebApplicationsScreenshotsHeader|Capturas de Ecrã|  
  
###  <a name="ApplicationListStrings"></a>ApplicationListStrings  
  
|Nome|Texto|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Tem certeza de que deseja remover o aplicativo?|  
|WebDevelopersAppNotPublished|Não publicado|  
|WebDevelopersAppNotSubmitted|Não enviado|  
|WebDevelopersAppTableCategoryHeader|Categoria|  
|WebDevelopersAppTableNameHeader|Nome|  
|WebDevelopersAppTableStateHeader|Estado|  
|WebDevelopersEditLink|Editar|  
|WebDevelopersRegisterAppLink|Registrar aplicativo|  
|WebDevelopersRemoveLink|Remover|  
|WebDevelopersSubmitLink|Submeter|  
|WebDevelopersYourApplicationsHeader|Seus aplicativos|  
  
###  <a name="AppStrings"></a>AppStrings  
  
|Nome|Texto|  
|----------|----------|  
|WebApplicationsHeader|Aplicações|  
  
###  <a name="CommonResources"></a>CommonResources  
  
|Nome|Texto|  
|----------|----------|  
|NoItemsToDisplay|Não foram encontrados resultados.|  
|GeneralExceptionMessage|Algo não é certo. Pode ser uma falha temporária ou um bug. Tente novamente.|  
|GeneralJsonExceptionMessage|Algo não é certo. Pode ser uma falha temporária ou um bug. Recarregue a página e tente novamente.|  
|ConfirmationMessageUnsavedChanges|Há algumas alterações não salvas. Tem certeza de que deseja cancelar e descartar as alterações?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|Corpo da solicitação HTTP muito grande.|  
  
###  <a name="CommonStrings"></a>CommonStrings  
  
|Nome|Texto|  
|----------|----------|  
|ButtonLabelCancel|Cancelar|  
|ButtonLabelSave|Guardar|  
|GeneralExceptionMessage|Algo não é certo. Pode ser uma falha temporária ou um bug. Tente novamente.|  
|NoItemsToDisplay|Não há itens a serem exibidos.|  
|PagerButtonLabelFirst|Primeiro|  
|PagerButtonLabelLast|última|  
|PagerButtonLabelNext|Seguinte|  
|PagerButtonLabelPrevious|Anterior|  
|PagerLabelPageNOfM|{0} de página de {1}|  
|PasswordTooShort|A senha é muito curta|  
|EmailAsPassword|Não use seu email como senha|  
|PasswordSameAsUserName|Sua senha não pode conter seu nome de usuário|  
|PasswordTwoCharacterClasses|Usar classes de caractere diferentes|  
|PasswordTooManyRepetitions|Muitas repetições|  
|PasswordSequenceFound|Sua senha contém sequências|  
|PagerLabelPageSize|Tamanho da página|  
|CurtainLabelLoading|A carregar...|  
|TablePlaceholderNothingToDisplay|Não há dados para o período e o escopo selecionados|  
|ButtonLabelClose|Fechar|  
  
###  <a name="Documentation"></a>Sobre  
  
|Nome|Texto|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Cabeçalho '{0}' inválido|  
|WebDocumentationInvalidRequestErrorMessage|URL de solicitação inválida|  
|TextboxLabelAccessToken|Token de acesso *|  
|DropdownOptionPrimaryKeyFormat|{0} primário|  
|DropdownOptionSecondaryKeyFormat|{0} secundário|  
|WebDocumentationSubscriptionKeyText|Sua chave de assinatura|  
|WebDocumentationTemplatesAddHeaders|Adicionar cabeçalhos HTTP necessários|  
|WebDocumentationTemplatesBasicAuthSample|Exemplo de autorização básica|  
|WebDocumentationTemplatesCurlForBasicAuth|para uso de autorização básica:--usuário {username}: {Password}|  
|WebDocumentationTemplatesCurlValuesForPath|Especifique valores para os parâmetros de caminho (mostrados como {...}), sua chave de assinatura e valores para parâmetros de consulta|  
|WebDocumentationTemplatesDeveloperKey|Especifique sua chave de assinatura|  
|WebDocumentationTemplatesJavaApache|Este exemplo usa o cliente Apache HTTP dos componentes HTTP (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Especifique valores para parâmetros opcionais, conforme necessário|  
|WebDocumentationTemplatesPhpPackage|Este exemplo usa o pacote HTTP_Request2. (para obter mais informações: https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Especifique valores para os parâmetros de caminho (mostrados como {...}) e o corpo da solicitação, se necessário|  
|WebDocumentationTemplatesRequestBody|Especificar corpo da solicitação|  
|WebDocumentationTemplatesRequiredParams|Especifique valores para os seguintes parâmetros obrigatórios|  
|WebDocumentationTemplatesValuesForPath|Especifique valores para os parâmetros de caminho (mostrados como {...})|  
|OAuth2AuthorizationEndpointDescription|O ponto de extremidade de autorização é usado para interagir com o proprietário do recurso e obter uma concessão de autorização.|  
|OAuth2AuthorizationEndpointName|Ponto de extremidade de autorização|  
|OAuth2TokenEndpointDescription|O ponto de extremidade do token é usado pelo cliente para obter um token de acesso apresentando sua concessão de autorização ou token de atualização.|  
|OAuth2TokenEndpointName|Ponto de extremidade do token|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|< p\> o cliente inicia o fluxo direcionando o agente do usuário do proprietário do recurso para o ponto de extremidade de autorização.  O cliente inclui seu identificador de cliente, o escopo solicitado, o estado local e um URI de redirecionamento para o qual o servidor de autorização enviará o agente de usuário novamente quando o acesso for concedido (ou negado).     </p\> < p\> o servidor de autorização autentica o proprietário do recurso (por meio do agente do usuário) e estabelece se o proprietário do recurso concede ou nega a solicitação de acesso do cliente.     </p\> < p\> supondo que o proprietário do recurso concede acesso, o servidor de autorização redireciona o agente do usuário de volta para o cliente usando o URI de redirecionamento fornecido anteriormente (na solicitação ou durante o registro do cliente).  O URI de redirecionamento inclui um código de autorização e qualquer estado local fornecido pelo cliente anteriormente.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|< p\> se o usuário negar a solicitação de acesso de se a solicitação for inválida, o cliente será informado usando os seguintes parâmetros adicionados ao redirecionamento: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Solicitação de autorização|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|< p\> o aplicativo cliente deve enviar o usuário para o ponto de extremidade de autorização a fim de iniciar o processo OAuth.          No ponto de extremidade de autorização, o usuário autentica e, em seguida, concede ou nega o acesso ao aplicativo.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|< p\> supondo que o proprietário do recurso conceda acesso, o servidor de autorização redireciona o agente do usuário para o cliente usando o URI de redirecionamento fornecido anteriormente (na solicitação ou durante o registro do cliente).  O URI de redirecionamento inclui um código de autorização e qualquer estado local fornecido pelo cliente anteriormente. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|< p\> o cliente solicita um token de acesso do ponto de extremidade de token do servidor de autorização, incluindo o código de autorização recebido na etapa anterior.  Ao fazer a solicitação, o cliente é autenticado com o servidor de autorização.  O cliente inclui o URI de redirecionamento usado para obter o código de autorização para verificação. </p\> < p\> o servidor de autorização autentica o cliente, valida o código de autorização e garante que o URI de redirecionamento recebido corresponda ao URI usado para redirecionar o cliente na etapa (C).  Se for válido, o servidor de autorização responderá com um token de acesso e, opcionalmente, um token de atualização. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|< p\> se a solicitação de autenticação do cliente falhar ou for inválida, o servidor de autorização responderá com um código de status HTTP 400 (solicitação inválida) (a menos que especificado o contrário) e incluirá os parâmetros a seguir com a resposta. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|< p\> o cliente faz uma solicitação para o ponto de extremidade do token enviando os seguintes parâmetros usando o formato "application/x-www-form-urlencoded" com uma codificação de caractere UTF-8 no corpo da entidade de solicitação HTTP. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|< p\> o servidor de autorização emite um token de acesso e um token de atualização opcional e constrói a resposta adicionando os parâmetros a seguir ao corpo da entidade da resposta HTTP com um código de status 200 (OK). </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|< p\> o cliente é autenticado com o servidor de autorização e solicita um token de acesso do ponto de extremidade do token. </p\> < p\> o servidor de autorização autentica o cliente e, se for válido, emite um token de acesso. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> se a solicitação falhar na autenticação do cliente ou for inválido, o servidor de autorização responderá com um código de status HTTP 400 (solicitação inválida) (a menos que especificado de outra forma) e incluirá os parâmetros a seguir com a resposta. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> o cliente faz uma solicitação para o ponto de extremidade do token adicionando os seguintes parâmetros usando o formato "application/x-www-form-urlencoded" com uma codificação de caractere UTF-8 no corpo da entidade de solicitação HTTP. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> se a solicitação de token de acesso for válida e autorizada, o servidor de autorização emitirá um token de acesso e um token de atualização opcional e construirá a resposta adicionando os seguintes parâmetros ao corpo da entidade da resposta HTTP com um 200 (OK ) código de status. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|< p\> o cliente inicia o fluxo direcionando o agente do usuário do proprietário do recurso para o ponto de extremidade de autorização.  O cliente inclui seu identificador de cliente, o escopo solicitado, o estado local e um URI de redirecionamento para o qual o servidor de autorização enviará o agente de usuário novamente quando o acesso for concedido (ou negado). </p\> < p\> o servidor de autorização autentica o proprietário do recurso (por meio do agente do usuário) e estabelece se o proprietário do recurso concede ou nega a solicitação de acesso do cliente. </p\> < p\> supondo que o proprietário do recurso conceda acesso, o servidor de autorização redireciona o agente do usuário de volta para o cliente usando o URI de redirecionamento fornecido anteriormente.  O URI de redirecionamento inclui o token de acesso no fragmento de URI. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|< p\> se o proprietário do recurso negar a solicitação de acesso ou se a solicitação falhar por motivos diferentes de um URI de redirecionamento ausente ou inválido, o servidor de autorização informará o cliente adicionando os parâmetros a seguir ao componente de fragmento de th e URI de redirecionamento usando o formato "application/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|< p\> o aplicativo cliente deve enviar o usuário para o ponto de extremidade de autorização a fim de iniciar o processo OAuth.      No ponto de extremidade de autorização, o usuário autentica e, em seguida, concede ou nega o acesso ao aplicativo. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|< p\> se o proprietário do recurso conceder a solicitação de acesso, o servidor de autorização emitirá um token de acesso e o entregará ao cliente adicionando os parâmetros a seguir ao componente de fragmento do URI de redirecionamento usando o "application/x-www-for m-urlencoded "Format. </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|O fluxo de código de autorização é otimizado para clientes capazes de manter a confidencialidade de suas credenciais (por exemplo, aplicativos de servidor Web implementados usando PHP, Java, Python, Ruby, ASP.NET, etc.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Concessão de código de autorização|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|O fluxo de credenciais do cliente é adequado nos casos em que o cliente (seu aplicativo) está solicitando acesso aos recursos protegidos sob seu controle. O cliente é considerado um proprietário do recurso, portanto, nenhuma interação do usuário final é necessária.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Concessão de credenciais de cliente|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|O fluxo implícito é otimizado para clientes que não têm capacidade de manter a confidencialidade de suas credenciais conhecidas para operar um URI de redirecionamento específico. Esses clientes normalmente são implementados em um navegador usando uma linguagem de script, como JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Concessão implícita|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|O fluxo de credenciais de senha do proprietário do recurso é adequado nos casos em que o proprietário do recurso tem uma relação de confiança com o cliente (seu aplicativo), como o sistema operacional do dispositivo ou um aplicativo altamente privilegiado. Esse fluxo é adequado para clientes capazes de obter as credenciais do proprietário do recurso (nome de usuário e senha, normalmente usando um formulário interativo).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Concessão de credenciais de senha do proprietário do recurso|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|< p\> o proprietário do recurso fornece ao cliente seu nome de usuário e senha. </p\> < p\> o cliente solicita um token de acesso do ponto de extremidade de token do servidor de autorização, incluindo as credenciais recebidas do proprietário do recurso.  Ao fazer a solicitação, o cliente é autenticado com o servidor de autorização. </p\> < p\> o servidor de autorização autentica o cliente e valida as credenciais do proprietário do recurso e, se válido, emite um token de acesso. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> se a solicitação falhar na autenticação do cliente ou for inválido, o servidor de autorização responderá com um código de status HTTP 400 (solicitação inválida) (a menos que especificado de outra forma) e incluirá os parâmetros a seguir com a resposta. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> o cliente faz uma solicitação para o ponto de extremidade do token adicionando os seguintes parâmetros usando o formato "application/x-www-form-urlencoded" com uma codificação de caractere UTF-8 no corpo da entidade de solicitação HTTP. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> se a solicitação de token de acesso for válida e autorizada, o servidor de autorização emitirá um token de acesso e um token de atualização opcional e construirá a resposta adicionando os seguintes parâmetros ao corpo da entidade da resposta HTTP com um 200 (O K) código de status. </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Solicitação de token de acesso|  
|OAuth2Step_AuthorizationRequest_Name|Solicitação de autorização|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|Necessário. O token de acesso emitido pelo servidor de autorização.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|Necessário. O token de acesso emitido pelo servidor de autorização.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|Necessário. O token de acesso emitido pelo servidor de autorização.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Necessário. O token de acesso emitido pelo servidor de autorização.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|Necessário. Identificador de cliente.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|NECESSÁRIO se o cliente não estiver autenticando com o servidor de autorização.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|Necessário. O identificador do cliente.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|Necessário. O código de autorização gerado pelo servidor de autorização.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|Necessário. O código de autorização recebido do servidor de autorização.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|Adicional. Texto ASCII legível por humanos que fornece informações adicionais.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|Adicional. Texto ASCII legível por humanos que fornece informações adicionais.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|Adicional. Texto ASCII legível por humanos que fornece informações adicionais.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|Adicional. Texto ASCII legível por humanos que fornece informações adicionais.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Adicional. Texto ASCII legível por humanos que fornece informações adicionais.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|Adicional. Um URI que identifica uma página da Web legível com informações sobre o erro.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|Adicional. Um URI que identifica uma página da Web legível com informações sobre o erro.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|Adicional. Um URI que identifica uma página da Web legível com informações sobre o erro.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|Adicional. Um URI que identifica uma página da Web legível com informações sobre o erro.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Adicional. Um URI que identifica uma página da Web legível com informações sobre o erro.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|Necessário. Um único código de erro ASCII do seguinte: invalid_request, unauthorized_client, ACCESS_DENIED, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|Necessário. Um único código de erro ASCII do seguinte: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|Necessário. Um único código de erro ASCII do seguinte: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|Necessário. Um único código de erro ASCII do seguinte: invalid_request, unauthorized_client, ACCESS_DENIED, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Necessário. Um único código de erro ASCII do seguinte: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|Aconselhável. O tempo de vida em segundos do token de acesso.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|Aconselhável. O tempo de vida em segundos do token de acesso.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|Aconselhável. O tempo de vida em segundos do token de acesso.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Aconselhável. O tempo de vida em segundos do token de acesso.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|Necessário. O valor deve ser definido como "authorization_code".|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|Necessário. O valor deve ser definido como "client_credentials".|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Necessário. O valor deve ser definido como "senha".|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Necessário. A senha do proprietário do recurso.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|Adicional. O URI do ponto de extremidade de redirecionamento deve ser um URI absoluto.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|NECESSÁRIO se o parâmetro "redirect_uri" foi incluído na solicitação de autorização e seus valores devem ser idênticos.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|Adicional. O URI do ponto de extremidade de redirecionamento deve ser um URI absoluto.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|Adicional. O token de atualização, que pode ser usado para obter novos tokens de acesso.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|Adicional. O token de atualização, que pode ser usado para obter novos tokens de acesso.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Adicional. O token de atualização, que pode ser usado para obter novos tokens de acesso.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|Necessário. O valor deve ser definido como "código".|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|Necessário. O valor deve ser definido como "token".|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|Adicional. O escopo da solicitação de acesso.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|OPCIONAL se for idêntico ao escopo solicitado pelo cliente; caso contrário, obrigatório.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|Adicional. O escopo da solicitação de acesso.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|OPCIONAL, se for idêntico ao escopo solicitado pelo cliente; caso contrário, obrigatório.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|Adicional. O escopo da solicitação de acesso.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|OPCIONAL se for idêntico ao escopo solicitado pelo cliente; caso contrário, obrigatório.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Adicional. O escopo da solicitação de acesso.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPCIONAL, se for idêntico ao escopo solicitado pelo cliente; caso contrário, obrigatório.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|OBRIGATÓRIO se o parâmetro "State" estava presente na solicitação de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|Aconselhável. Um valor opaco usado pelo cliente para manter o estado entre a solicitação e o retorno de chamada.  O servidor de autorização inclui esse valor ao redirecionar o agente do usuário de volta para o cliente.  O parâmetro deve ser usado para impedir falsificação de solicitação entre sites.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|OBRIGATÓRIO se o parâmetro "State" estava presente na solicitação de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|OBRIGATÓRIO se o parâmetro "State" estava presente na solicitação de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|Aconselhável. Um valor opaco usado pelo cliente para manter o estado entre a solicitação e o retorno de chamada.  O servidor de autorização inclui esse valor ao redirecionar o agente do usuário de volta para o cliente.  O parâmetro deve ser usado para impedir falsificação de solicitação entre sites.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|OBRIGATÓRIO se o parâmetro "State" estava presente na solicitação de autorização do cliente.  O valor exato recebido do cliente.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|Necessário. O tipo de token emitido.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|Necessário. O tipo de token emitido.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|Necessário. O tipo de token emitido.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Necessário. O tipo de token emitido.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Necessário. O nome de usuário do proprietário do recurso.|  
|OAuth2UnsupportedTokenType|Não há suporte para o tipo de token '{0}'.|  
|OAuth2InvalidState|Resposta inválida do servidor de autorização|  
|OAuth2GrantType_AuthorizationCode|código de autorização|  
|OAuth2GrantType_Implicit|Localiza|  
|OAuth2GrantType_ClientCredentials|Credenciais de cliente|  
|OAuth2GrantType_ResourceOwnerPassword|Senha do proprietário do recurso|  
|WebDocumentation302Code|302 encontrado|  
|WebDocumentation400Code|400 (solicitação inadequada)|  
|OAuth2SendingMethod_AuthHeader|Cabeçalho de autorização|  
|OAuth2SendingMethod_QueryParam|Parâmetro de consulta|  
|OAuth2AuthorizationServerGeneralException|Ocorreu um erro ao autorizar o acesso via {0}|  
|OAuth2AuthorizationServerCommunicationException|Não foi possível estabelecer uma conexão HTTP com o servidor de autorização ou ele foi fechado inesperadamente.|  
|WebDocumentationOAuth2GeneralErrorMessage|Ocorreu um erro inesperado.|  
|AuthorizationServerCommunicationException|Ocorreu uma exceção de comunicação do servidor de autorização. Entre em contato com o administrador.|  
|TextblockSubscriptionKeyHeaderDescription|Chave de assinatura que fornece acesso a essa API. Foi encontrado no seu < um perfil de\>href = '/Developer ' </a\>.|  
|TextblockOAuthHeaderDescription|Token de acesso OAuth 2,0 obtido do <\>{0}</i\>. Tipos de concessão com suporte: < i\>{1}</i\>.|  
|TextblockContentTypeHeaderDescription|Tipo de mídia do corpo enviado para a API.|  
|ErrorMessageApiNotAccessible|A API que você está tentando chamar não está acessível no momento. Entre em contato com o editor de API < um href = "/Issues"\>aqui </a\>.|  
|ErrorMessageApiTimedout|A API que você está tentando chamar está demorando mais do que o normal para obter resposta de volta. Entre em contato com o editor de API < um href = "/Issues"\>aqui </a\>.|  
|BadRequestParameterExpected|o parâmetro "{0}' é esperado"|  
|TooltipTextDoubleClickToSelectAll|Clique duas vezes para selecionar tudo.|  
|TooltipTextHideRevealSecret|Mostrar/ocultar|  
|ButtonLinkOpenConsole|Experimente|  
|SectionHeadingRequestBody|Corpo do pedido|  
|SectionHeadingRequestParameters|Parâmetros de solicitação|  
|SectionHeadingRequestUrl|URL do Pedido|  
|SectionHeadingResponse|Resposta|  
|SectionHeadingRequestHeaders|Cabeçalhos de solicitação|  
|FormLabelSubtextOptional|Adicional|  
|SectionHeadingCodeSamples|Exemplos de código|  
|TextblockOpenidConnectHeaderDescription|Token de ID do OpenID Connect obtido do <\>{0}</i\>. Tipos de concessão com suporte: < i\>{1}</i\>.|  
  
###  <a name="ErrorPageStrings"></a>ErrorPageStrings  
  
|Nome|Texto|  
|----------|----------|  
|LinkLabelBack|Voltar|  
|LinkLabelHomePage|home page|  
|LinkLabelSendUsEmail|Envie-nos um email|  
|PageTitleError|Ocorreu um problema ao atender à página solicitada|  
|TextblockPotentialCauseIntermittentIssue|Isso pode ser um problema de acesso a dados intermitente que já existe.|  
|TextblockPotentialCauseOldLink|O link no qual você clicou pode ser antigo e não apontar mais para o local correto.|  
|TextblockPotentialCauseTechnicalProblem|Pode haver um problema técnico em nosso fim.|  
|TextblockPotentialSolutionRefresh|Tente atualizar a página.|  
|TextblockPotentialSolutionStartOver|Comece de nossa {0}.|  
|TextblockPotentialSolutionTryAgain|Vá {0} e tente a ação que você executou novamente.|  
|TextReportProblem|{0} descrevendo o que deu errado e examinaremos isso assim que pudermos.|  
|TitlePotentialCause|Causa potencial|  
|TitlePotentialSolution|Possivelmente é apenas um problema temporário, algumas coisas a serem experimentadas|  
  
###  <a name="IssuesStrings"></a>IssuesStrings  
  
|Nome|Texto|  
|----------|----------|  
|WebIssuesIndexTitle|Problemas|  
|WebIssuesNoActiveSubscriptions|Você não tem assinaturas ativas. Você precisa assinar um produto para relatar um problema.|  
|WebIssuesNotSignin|Você não está conectado. {0} relatar um problema ou postar um comentário.|  
|WebIssuesReportIssueButton|Relatar problema|  
|WebIssuesSignIn|iniciar sessão|  
|WebIssuesStatusReportedBy|Status: {0} &#124; relatado por {1}|  
  
###  <a name="NotFoundStrings"></a>NotFoundStrings  
  
|Nome|Texto|  
|----------|----------|  
|LinkLabelHomePage|home page|  
|LinkLabelSendUsEmail|Envie-nos um email|  
|PageTitleNotFound|Infelizmente, não foi possível encontrar a página que você está procurando|  
|TextblockPotentialCauseMisspelledUrl|Você pode ter escrito a URL incorretamente se a tiver digitado.|  
|TextblockPotentialCauseOldLink|O link no qual você clicou pode ser antigo e não apontar mais para o local correto.|  
|TextblockPotentialSolutionRetype|Tente digitar novamente a URL.|  
|TextblockPotentialSolutionStartOver|Comece de nossa {0}.|  
|TextReportProblem|{0} descrevendo o que deu errado e examinaremos isso assim que pudermos.|  
|TitlePotentialCause|Causa potencial|  
|TitlePotentialSolution|Solução potencial|  
  
###  <a name="ProductDetailsStrings"></a>ProductDetailsStrings  
  
|Nome|Texto|  
|----------|----------|  
|WebProductsAgreement|Ao inscrever-se no produto {0}, eu concordo com o `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`.|  
|WebProductsLegalTermsLink|Termos de Utilização|  
|WebProductsSubscribeButton|Subscrever|  
|WebProductsUsageLimitsHeader|Limites de utilização|  
|WebProductsYouAreNotSubscribed|Você assinou este produto.|  
|WebProductsYouRequestedSubscription|Você solicitou a assinatura para este produto.|  
|ErrorYouNeedToAgreeWithLegalTerms|Você deve concordar com os termos de uso antes de continuar.|  
|ButtonLabelAddSubscription|Adicionar subscrição|  
|LinkLabelChangeSubscriptionName|Alteração|  
|ButtonLabelConfirm|Confirmar|  
|TextblockMultipleSubscriptionsCount|Você tem {0} assinaturas para este produto:|  
|TextblockSingleSubscriptionsCount|Você tem {0} assinatura para este produto:|  
|TextblockSingleApisCount|Este produto contém {0} API:|  
|TextblockMultipleApisCount|Este produto contém {0} APIs:|  
|TextblockHeaderSubscribe|Assinar o produto|  
|TextblockSubscriptionDescription|Uma nova assinatura será criada da seguinte maneira:|  
|TextblockSubscriptionLimitReached|Limite de assinaturas atingido.|  
  
###  <a name="ProductsStrings"></a>ProductsStrings  
  
|Nome|Texto|  
|----------|----------|  
|PageTitleProducts|Produtos|  
  
###  <a name="ProviderInfoStrings"></a>ProviderInfoStrings  
  
|Nome|Texto|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|A entrada é desabilitada pelos administradores no momento.|  
|TextboxExternalIdentitiesSigninInvitation|Como alternativa, entre com|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Entrar com:|  
  
###  <a name="SigninResources"></a>SigninResources  
  
|Nome|Texto|  
|----------|----------|  
|PrincipalNotFound|A entidade de segurança não foi encontrada ou a assinatura é inválida|  
|ErrorSsoAuthenticationFailed|Falha na autenticação de SSO|  
|ErrorSsoAuthenticationFailedDetailed|Token inválido fornecido ou a assinatura não pode ser verificada.|  
|ErrorSsoTokenInvalid|O token de SSO é inválido|  
|ValidationErrorSpecificEmailAlreadyExists|Email '{0}' já registrado|  
|ValidationErrorSpecificEmailInvalid|O email '{0}' é inválido|  
|ValidationErrorPasswordInvalid|A senha é inválida. Corrija os erros e tente novamente.|  
|PropertyTooShort|{0} é muito curto|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Endereço de email inválido.|  
|ValidationMessageNewPasswordConfirmationRequired|Confirmar nova senha|  
|ValidationErrorPasswordConfirmationRequired|A confirmação de senha está vazia|  
|WebAuthenticationEmailChangeNotice|O email de confirmação de alteração está no caminho para {0}. Siga as instruções dentro dele para confirmar seu novo endereço de email. Se o email não chegar à sua caixa de entrada nos próximos minutos, verifique sua pasta de lixo eletrônico.|  
|WebAuthenticationEmailChangeNoticeHeader|Sua solicitação de alteração de email foi processada com êxito|  
|WebAuthenticationEmailChangeNoticeTitle|Alteração de email solicitada|  
|WebAuthenticationEmailHasBeenRevertedNotice|Seu email já existe. A solicitação foi revertida|  
|ValidationErrorEmailAlreadyExists|O email já existe|  
|ValidationErrorEmailInvalid|Endereço de email inválido|  
|TextboxLabelEmail|E-mail|  
|ValidationErrorEmailRequired|O email é necessário.|  
|WebAuthenticationErrorNoticeHeader|Erro|  
|WebAuthenticationFieldLengthErrorMessage|{0} deve ter um comprimento máximo de {1}|  
|TextboxLabelEmailFirstName|Nome próprio|  
|ValidationErrorFirstNameRequired|O nome é necessário.|  
|ValidationErrorFirstNameInvalid|Nome inválido|  
|NoticeInvalidInvitationToken|Observe que os links de confirmação são válidos por apenas 48 horas. Se você ainda estiver dentro desse período, verifique se o link está correto. Se o seu link tiver expirado, repita a ação que você está tentando confirmar.|  
|NoticeHeaderInvalidInvitationToken|Token de convite inválido|  
|NoticeTitleInvalidInvitationToken|Erro de confirmação|  
|WebAuthenticationLastNameInvalidErrorMessage|Sobrenome inválido|  
|TextboxLabelEmailLastName|Apelido|  
|ValidationErrorLastNameRequired|O sobrenome é obrigatório.|  
|WebAuthenticationLinkExpiredNotice|O link de confirmação enviado para você expirou. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|O link de redefinição de senha é inválido ou expirou.|  
|WebAuthenticationLinkExpiredNoticeTitle|Link enviado|  
|WebAuthenticationNewPasswordLabel|Nova senha|  
|ValidationMessageNewPasswordRequired|A nova senha é necessária.|  
|TextboxLabelNotificationsSenderEmail|Email do remetente de notificações|  
|TextboxLabelOrganizationName|Nome da organização|  
|WebAuthenticationOrganizationRequiredErrorMessage|O nome da organização está vazio|  
|WebAuthenticationPasswordChangedNotice|Sua senha foi atualizada com êxito|  
|WebAuthenticationPasswordChangedNoticeTitle|Senha atualizada|  
|WebAuthenticationPasswordCompareErrorMessage|As senhas não correspondem|  
|WebAuthenticationPasswordConfirmLabel|Confirmar palavra-passe|  
|ValidationErrorPasswordInvalidDetailed|A senha é muito fraca.|  
|WebAuthenticationPasswordLabel|Palavra-passe|  
|ValidationErrorPasswordRequired|A senha é necessária.|  
|WebAuthenticationPasswordResetSendNotice|O email de confirmação de alteração de senha está no caminho para {0}. Siga as instruções no email para continuar seu processo de alteração de senha.|  
|WebAuthenticationPasswordResetSendNoticeHeader|Sua solicitação de redefinição de senha foi processada com êxito|  
|WebAuthenticationPasswordResetSendNoticeTitle|Redefinição de senha solicitada|  
|WebAuthenticationRequestNotFoundNotice|Solicitação não encontrada|  
|WebAuthenticationSenderEmailRequiredErrorMessage|O email do remetente de notificações está vazio|  
|WebAuthenticationSigninPasswordLabel|Confirme a alteração inserindo uma senha|  
|WebAuthenticationSignupConfirmNotice|O email de confirmação de registro está a caminho para {0}. < br/\> siga as instruções no email para ativar sua conta. < br/\> se o email não chegar em sua caixa de entrada nos próximos minutos, verifique seu lixo eletrônico pasta.|  
|WebAuthenticationSignupConfirmNoticeHeader|Sua conta foi criada com êxito|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|O email de confirmação de registro foi enviado novamente|  
|WebAuthenticationSignupConfirmNoticeTitle|Conta criada|  
|WebAuthenticationTokenRequiredErrorMessage|O token está vazio|  
|WebAuthenticationUserAlreadyRegisteredNotice|Parece que um usuário com este email já está registrado no sistema. Se você esqueceu sua senha, tente restaurá-la ou contate nossa equipe de suporte.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|Usuário já registrado|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Já registrado|  
|ButtonLabelChangePassword|Alterar senha|  
|ButtonLabelChangeAccountInfo|Alterar informações da conta|  
|ButtonLabelCloseAccount|Fechar conta|  
|WebAuthenticationInvalidCaptchaErrorMessage|O texto digitado não corresponde ao texto na imagem. Tente novamente.|  
|ValidationErrorCredentialsInvalid|O email ou a senha é inválido. Corrija os erros e tente novamente.|  
|WebAuthenticationRequestIsNotValid|A solicitação não é válida|  
|WebAuthenticationUserIsNotConfirm|Confirme seu registro antes de tentar entrar.|  
|WebAuthenticationInvalidEmailFormated|O email é inválido: {0}|  
|WebAuthenticationUserNotFound|O utilizador não foi encontrado|  
|WebAuthenticationTenantNotRegistered|Sua conta pertence a um locatário Azure Active Directory que não está autorizado a acessar este portal.|  
|WebAuthenticationAuthenticationFailed|Falha na autenticação.|  
|WebAuthenticationGooglePlusNotEnabled|Falha na autenticação. Se você autorizou o aplicativo, entre em contato com o administrador para verificar se a autenticação do Google está configurada corretamente.|  
|ValidationErrorAllowedTenantIsRequired|O locatário permitido é obrigatório|  
|ValidationErrorTenantIsNotValid|O locatário de Azure Active Directory '{0}' não é válido.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Faça logon usando sua conta do {0}|  
|WebAuthenticationUserLimitNotice|Este serviço atingiu o número máximo de usuários permitidos. `<a href="mailto:{0}"\>contact the administrator</a\>` atualizar seu serviço e reabilitar o registro do usuário.|  
|WebAuthenticationUserLimitNoticeHeader|Registro de usuário desabilitado|  
|WebAuthenticationUserLimitNoticeTitle|Registro de usuário desabilitado|  
|WebAuthenticationUserRegistrationDisabledNotice|O registro de usuários foi desabilitado pelo administrador. Faça logon com o provedor de identidade externo.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Registro de usuário desabilitado|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Registro de usuário desabilitado|  
|WebAuthenticationSignupPendingConfirmationNotice|Antes que possamos concluir a criação de sua conta, precisamos verificar seu endereço de email. Enviamos um email para {0}. Siga as instruções no email para ativar sua conta. Se o email não chegar nos próximos minutos, verifique sua pasta de lixo eletrônico.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Encontramos uma conta não confirmada para o endereço de email {0}. Para concluir a criação de sua conta, precisamos verificar seu endereço de email. Enviamos um email para {0}. Siga as instruções no email para ativar sua conta. Se o email não chegar nos próximos minutos, verifique sua pasta de lixo eletrônico|  
|WebAuthenticationSignupConfirmationAlmostDone|Quase pronto|  
|WebAuthenticationSignupConfirmationEmailSent|Enviamos um email para {0}. Siga as instruções no email para ativar sua conta. Se o email não chegar nos próximos minutos, verifique sua pasta de lixo eletrônico.|  
|WebAuthenticationEmailSentNotificationMessage|Email enviado com êxito para {0}|  
|WebAuthenticationNoAadTenantConfigured|Nenhum locatário Azure Active Directory configurado para o serviço.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Eu concordo com o `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`.|  
|TextblockUserRegistrationTermsProvided|Verifique `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Termos de Utilização|  
|ValidationMessageConsentNotAccepted|Você deve concordar com os termos de uso antes de continuar.|  
  
###  <a name="SigninStrings"></a>SigninStrings  
  
|Nome|Texto|  
|----------|----------|  
|WebAuthenticationForgotPassword|Esqueceu sua senha?|  
|WebAuthenticationIfAdministrator|Se você for um administrador, deverá entrar `<a href="{0}"\>here</a\>`.|  
|WebAuthenticationNotAMember|Ainda não é um membro? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Lembrar-me deste computador|  
|WebAuthenticationSigininWithPassword|Entre com seu nome de usuário e senha|  
|WebAuthenticationSigninTitle|Iniciar sessão|  
|WebAuthenticationSignUpNow|Registe-se já|  
  
###  <a name="SignupStrings"></a>SignupStrings  
  
|Nome|Texto|  
|----------|----------|  
|PageTitleSignup|Inscrição|  
|WebAuthenticationAlreadyAMember|Já é membro?|  
|WebAuthenticationCreateNewAccount|Criar uma nova conta de gerenciamento de API|  
|WebAuthenticationSigninNow|Entrar agora|  
|ButtonLabelSignup|Inscrição|  
  
###  <a name="SubscriptionListStrings"></a>SubscriptionListStrings  
  
|Nome|Texto|  
|----------|----------|  
|SubscriptionCancelConfirmation|Tem certeza de que deseja cancelar esta assinatura?|  
|SubscriptionRenewConfirmation|Tem certeza de que deseja renovar esta assinatura?|  
|WebDevelopersManageSubscriptions|Gerir subscrições|  
|WebDevelopersPrimaryKey|Chave primária|  
|WebDevelopersRegenerateLink|Regenerar|  
|WebDevelopersSecondaryKey|Chave secundária|  
|ButtonLabelShowKey|Mostrar|  
|ButtonLabelRenewSubscription|Renovar|  
|WebDevelopersSubscriptionRequested|Solicitado em {0}|  
|WebDevelopersSubscriptionRequestedState|Solicitado|  
|WebDevelopersSubscriptionTableNameHeader|Nome|  
|WebDevelopersSubscriptionTableStateHeader|Estado|  
|WebDevelopersUsageStatisticsLink|Relatórios de análise|  
|WebDevelopersYourSubscriptions|Suas assinaturas|  
|SubscriptionPropertyLabelRequestedDate|Solicitado em|  
|SubscriptionPropertyLabelStartedDate|Iniciado em|  
|PageTitleRenameSubscription|Renomear assinatura|  
|SubscriptionPropertyLabelName|Nome da assinatura|  
  
###  <a name="SubscriptionStrings"></a>SubscriptionStrings  
  
|Nome|Texto|  
|----------|----------|  
|SectionHeadingCloseAccount|Está procurando fechar sua conta?|  
|PageTitleDeveloperProfile|Perfil|  
|ButtonLabelHideKey|Exibe|  
|ButtonLabelRegenerateKey|Regenerar|  
|InformationMessageKeyWasRegenerated|Tem certeza de que deseja regenerar essa chave?|  
|ButtonLabelShowKey|Mostrar|  
  
###  <a name="UpdateProfileStrings"></a>UpdateProfileStrings  
  
|Nome|Texto|  
|----------|----------|  
|ButtonLabelUpdateProfile|Atualizar perfil|  
|PageTitleUpdateProfile|Atualizar informações da conta|  
  
###  <a name="UserProfile"></a>Perfil  
  
|Nome|Texto|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Alterar informações da conta|  
|ButtonLabelChangePassword|Alterar senha|  
|ButtonLabelCloseAccount|Fechar conta|  
|TextboxLabelEmail|E-mail|  
|TextboxLabelEmailFirstName|Nome próprio|  
|TextboxLabelEmailLastName|Apelido|  
|TextboxLabelNotificationsSenderEmail|Email do remetente de notificações|  
|TextboxLabelOrganizationName|Nome da organização|  
|SubscriptionStateActive|Ativa|  
|SubscriptionStateCancelled|Cancelar|  
|SubscriptionStateExpired|Fora do prazo|  
|SubscriptionStateRejected|Recusa|  
|SubscriptionStateRequested|Solicitado|  
|SubscriptionStateSuspended|Suspenso|  
|DefaultSubscriptionNameTemplate|{0} (padrão)|  
|SubscriptionNameTemplate|{0} de acesso do desenvolvedor|  
|TextboxLabelSubscriptionName|Nome da assinatura|  
|ValidationMessageSubscriptionNameRequired|O nome da assinatura não pode ficar vazio.|  
|ApiManagementUserLimitReached|Este serviço atingiu o número máximo de usuários permitidos. Atualize para um tipo de preço mais alto.|  
  
##  <a name="glyphs"></a>Recursos de glifo  
 Os modelos do portal do desenvolvedor do gerenciamento de API podem usar os glifos do [Glyphicons da inicialização](https://getbootstrap.com/components/#glyphicons). Esse conjunto de glifos inclui mais de 250 glifos no formato de fonte do conjunto de halflings [Glyphicon](https://glyphicons.com/) . Para usar um glifo desse conjunto, use a sintaxe a seguir.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Para obter a lista completa de glifos, consulte [Glyphicons da inicialização](https://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do desenvolvedor de gerenciamento de API usando modelos](api-management-developer-portal-templates.md).
