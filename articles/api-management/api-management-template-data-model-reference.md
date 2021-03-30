---
title: Referência do modelo de modelo de gestão da Azure API | Microsoft Docs
description: Conheça a entidade e as representações de tipo para itens comuns utilizados nos modelos de dados para os modelos do portal do desenvolvedor na Gestão API da Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 868ad3d1c6e7e7ef2cf32dcf675bc471a614f3ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86243159"
---
# <a name="azure-api-management-template-data-model-reference"></a>Referência do modelo de modelo de gestão da Azure API
Este tópico descreve a entidade e as representações do tipo para itens comuns utilizados nos modelos de dados para os modelos de portais de desenvolvimento na Gestão API da Azure.  
  
 Para obter mais informações sobre o trabalho com modelos, consulte [como personalizar o portal de desenvolvedores da API Management utilizando modelos](./api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="reference"></a>Referência

-   [API](#API)  
-   [Resumo da API](#APISummary)  
-   [Aplicação](#Application)  
-   [Anexo](#Attachment)  
-   [Exemplo de código](#Sample)  
-   [Comentário](#Comment)  
-   [Filtragem](#Filtering)  
-   [Cabeçalho](#Header)  
-   [Pedido de HTTP](#HTTPRequest)  
-   [RESPOSTA HTTP](#HTTPResponse)  
-   [Problema](#Issue)  
-   [Operação](#Operation)  
-   [Menu de operação](#Menu)  
-   [Item do menu de operação](#MenuItem)  
-   [Paginação](#Paging)  
-   [Parâmetro](#Parameter)  
-   [Produto](#Product)  
-   [Fornecedor](#Provider)  
-   [Representação](#Representation)  
-   [Subscrição](#Subscription)  
-   [Resumo da assinatura](#SubscriptionSummary)  
-   [Informação da conta do utilizador](#UserAccountInfo)  
-   [S-in do utilizador](#UseSignIn)  
-   [Inscrição do utilizador](#UserSignUp)  
  
##  <a name="api"></a><a name="API"></a> API  
 A `API` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`id`|cadeia (de carateres)|Identificador de recursos. Identifica exclusivamente a API no âmbito da atual instância de serviço de Gestão da API. O valor é um URL relativo válido no formato de `apis/{id}` onde `{id}` está um identificador API. Esta propriedade é só para leitura.|  
|`name`|string|Nome da API. Não deve estar vazio. O comprimento máximo é de 100 caracteres.|  
|`description`|string|Descrição da API. Não deve estar vazio. Pode incluir tags de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`serviceUrl`|string|URL absoluto do serviço backend implementando esta API.|  
|`path`|string|URL relativo identificando exclusivamente esta API e todos os seus caminhos de recursos dentro da instância do serviço de Gestão API. É anexado ao URL de base de ponto final da API especificado durante a criação da instância de serviço para formar um URL público para esta API.|  
|`protocols`|matriz de número|Descreve quais os protocolos que as operações desta API podem ser invocadas. Valores permitidos são `1 - http` `2 - https` e, ou ambos.|  
|`authenticationSettings`|[Definições de autenticação do servidor de autorização](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Recolha de configurações de autenticação incluídas nesta API.|  
|`subscriptionKeyParameterNames`|objeto|Propriedade opcional que pode ser usada para especificar nomes personalizados para consulta e/ou parâmetros de cabeçalho contendo a chave de subscrição. Quando esta propriedade estiver presente, deve conter pelo menos uma das duas propriedades seguintes.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="api-summary"></a><a name="APISummary"></a> Resumo da API  
 A `API summary` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`id`|cadeia (de carateres)|Identificador de recursos. Identifica exclusivamente a API no âmbito da atual instância de serviço de Gestão da API. O valor é um URL relativo válido no formato de `apis/{id}` onde `{id}` está um identificador API. Esta propriedade é só para leitura.|  
|`name`|string|Nome da API. Não deve estar vazio. O comprimento máximo é de 100 caracteres.|  
|`description`|string|Descrição da API. Não deve estar vazio. Pode incluir tags de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
  
##  <a name="application"></a><a name="Application"></a> Aplicação  
 A `application` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`Id`|cadeia (de carateres)|O identificador único da aplicação.|  
|`Title`|string|O título da candidatura.|  
|`Description`|string|A descrição do pedido.|  
|`Url`|URI|O URI para a aplicação.|  
|`Version`|string|Informações da versão para a aplicação.|  
|`Requirements`|string|Uma descrição dos requisitos para o pedido.|  
|`State`|número|O estado atual da aplicação.<br /><br /> - 0 - Registado<br /><br /> - 1 - Submetido<br /><br /> - 2 - Publicado<br /><br /> - 3 - Rejeitado<br /><br /> - 4 - Não publicado|  
|`RegistrationDate`|DateTime|A data e a hora em que o pedido foi registado.|  
|`CategoryId`|número|A categoria da candidatura (Finanças, entretenimento, etc.)|  
|`DeveloperId`|string|O identificador único do desenvolvedor que submeteu a aplicação.|  
|`Attachments`|Recolha de entidades [de anexos.](#Attachment)|Quaisquer anexos para a aplicação, tais como imagens ou ícones.|  
|`Icon`|[Anexo](#Attachment)|O ícone para a aplicação.|  
  
##  <a name="attachment"></a><a name="Attachment"></a> Anexo  
 A `attachment` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`UniqueId`|cadeia (de carateres)|O identificador único para o anexo.|  
|`Url`|string|A URL do recurso.|  
|`Type`|string|O tipo de ligação.|  
|`ContentType`|string|O tipo de mídia do anexo.|  
  
##  <a name="code-sample"></a><a name="Sample"></a> Amostra de código  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`title`|cadeia (de carateres)|O nome da operação.|  
|`snippet`|string|Esta propriedade é depreciada e não deve ser usada.|  
|`brush`|string|Qual o modelo de coloração de sintaxe de código a utilizar ao exibir a amostra de código. Os valores permitidos `plain` são, `php` , , , , , e `java` `xml` `objc` `python` `ruby` `csharp` .|  
|`template`|string|O nome deste modelo de amostra de código.|  
|`body`|string|Um espaço reservado para a parte da amostra de código do corte.|  
|`method`|string|O método HTTP da operação.|  
|`scheme`|string|O protocolo a utilizar para o pedido de operação.|  
|`path`|string|O caminho da operação.|  
|`query`|string|Exemplo de cadeia de consulta com parâmetros definidos.|  
|`host`|string|O URL da porta de serviço de gestão da API para a API que contém esta operação.|  
|`headers`|Coleção de entidades [header.](#Header)|Cabeçalhos para esta operação.|  
|`parameters`|Recolha de entidades [parâmetros.](#Parameter)|Parâmetros definidos para esta operação.|  
  
##  <a name="comment"></a><a name="Comment"></a> Comentário  
 A `API` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`Id`|número|A identificação do comentário.|  
|`CommentText`|string|O corpo do comentário. Pode incluir HTML.|  
|`DeveloperCompany`|string|O nome da empresa do construtor.|  
|`PostedOn`|DateTime|A data e a hora do comentário foi publicado.|  
  
##  <a name="issue"></a><a name="Issue"></a> Emissão  
 A `issue` entidade tem os seguintes imóveis.  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`Id`|cadeia (de carateres)|O identificador único para o problema.|  
|`ApiID`|string|A identificação da API para a qual esta questão foi reportada.|  
|`Title`|string|Título da edição.|  
|`Description`|string|Descrição do assunto.|  
|`SubscriptionDeveloperName`|string|Primeiro nome do desenvolvedor que reportou o problema.|  
|`IssueState`|string|O estado atual da questão. Os valores possíveis são propostos, abertos, fechados.|  
|`ReportedOn`|DateTime|A data e a hora em que o assunto foi comunicado.|  
|`Comments`|Entidades de Cobrança de [Comentários.](#Comment)|Comentários sobre esta questão.|  
|`Attachments`|Recolha de entidades [de anexos.](api-management-template-data-model-reference.md#Attachment)|Qualquer ligação com o assunto.|  
|`Services`|Recolha de entidades da [API.](#API)|As APIs subscreveram pelo utilizador que apresentou o problema.|  
  
##  <a name="filtering"></a><a name="Filtering"></a> Filtragem  
 A `filtering` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`Pattern`|cadeia (de carateres)|O termo de pesquisa atual; ou `null` se não houver termo de pesquisa.|  
|`Placeholder`|string|O texto a exibir na caixa de pesquisa quando não houver um termo de pesquisa especificado.|  
  
##  <a name="header"></a><a name="Header"></a> Cabeçalho  
 Esta secção descreve a `parameter` representação.  
  
|Propriedade|Tipo|Description|  
|--------------|-----------------|----------|  
|`name`|cadeia (de carateres)|Nome do parâmetro.|  
|`description`|string|Descrição do parâmetro.|  
|`value`|string|Valor do cabeçalho.|  
|`typeName`|string|Tipo de dados de valor do cabeçalho.|  
|`options`|string|Opções.|  
|`required`|boolean|Se o cabeçalho é necessário.|  
|`readOnly`|boolean|Se o cabeçalho é só leitura.|  
  
##  <a name="http-request"></a><a name="HTTPRequest"></a> HTTP Pedido  
 Esta secção descreve a `request` representação.  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`description`|cadeia (de carateres)|Operação pedido descrição.|  
|`headers`|conjunto de entidades [header.](#Header)|Pedir cabeçalhos.|  
|`parameters`|matriz de [parâmetro](#Parameter)|Recolha de parâmetros de pedido de operação.|  
|`representations`|matriz de [representação](#Representation)|Recolha de operação requer representações.|  
  
##  <a name="http-response"></a><a name="HTTPResponse"></a> RESPOSTA HTTP  
 Esta secção descreve a `response` representação.  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`statusCode`|inteiro positivo|Código de estado de resposta da operação.|  
|`description`|string|Descrição da resposta da operação.|  
|`representations`|matriz de [representação](#Representation)|Recolha de representações de resposta de operação.|  
  
##  <a name="operation"></a><a name="Operation"></a> Operação  
 A `operation` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`id`|cadeia (de carateres)|Identificador de recursos. Identifica exclusivamente a operação no âmbito da atual instância de serviço de Gestão da API. O valor é um URL relativo válido no formato de `apis/{aid}/operations/{id}` onde `{aid}` é um identificador API e `{id}` é um identificador de operação. Esta propriedade é só para leitura.|  
|`name`|string|O nome da operação. Não deve estar vazio. O comprimento máximo é de 100 caracteres.|  
|`description`|string|Descrição da operação. Não deve estar vazio. Pode incluir tags de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`scheme`|string|Descreve quais os protocolos que as operações desta API podem ser invocadas. Os valores permitidos `http` `https` são, ou ambos `http` e `https` .|  
|`uriTemplate`|string|Modelo de URL relativo que identifica o recurso-alvo para esta operação. Pode incluir parâmetros. Exemplo: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|string|O URL de gateway de gestão da API que acolhe a API.|  
|`httpMethod`|string|Método de operação HTTP.|  
|`request`|[Pedido de HTTP](#HTTPRequest)|Uma entidade que contém detalhes do pedido.|  
|`responses`|matriz de [resposta HTTP](#HTTPResponse)|Conjunto de entidades de resposta [HTTP.](#HTTPResponse)|  
  
##  <a name="operation-menu"></a><a name="Menu"></a> Menu de operação  
 A `operation menu` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`ApiId`|cadeia (de carateres)|A identificação da atual API.|  
|`CurrentOperationId`|string|A identificação da operação atual.|  
|`Action`|string|O tipo de menu.|  
|`MenuItems`|Entidades de item do menu de coleção de [operação.](#MenuItem)|As operações para a atual API.|  
  
##  <a name="operation-menu-item"></a><a name="MenuItem"></a> Item do menu de operação  
 A `operation menu item` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`Id`|cadeia (de carateres)|A identificação da operação.|  
|`Title`|string|A descrição da operação.|  
|`HttpMethod`|string|O método http da operação.|  
  
##  <a name="paging"></a><a name="Paging"></a> Paging  
 A `paging` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`Page`|número|O número da página atual.|  
|`PageSize`|número|Os resultados máximos a serem apresentados numa única página.|  
|`TotalItemCount`|número|O número de itens para visualização.|  
|`ShowAll`|boolean|Se sho todos os resultados em uma única página.|  
|`PageCount`|número|O número de páginas de resultados.|  
  
##  <a name="parameter"></a><a name="Parameter"></a> Parâmetro  
 Esta secção descreve a `parameter` representação.  
  
|Propriedade|Tipo|Description|  
|--------------|-----------------|----------|  
|`name`|cadeia (de carateres)|Nome do parâmetro.|  
|`description`|string|Descrição do parâmetro.|  
|`value`|string|Valor do parâmetro.|  
|`options`|matriz da cadeia|Valores definidos para valores de parâmetros de consulta.|  
|`required`|boolean|Especifica se o parâmetro é necessário ou não.|  
|`kind`|número|Se este parâmetro é um parâmetro de caminho (1), ou um parâmetro de consulta (2).|  
|`typeName`|string|Tipo de parâmetro.|  
  
##  <a name="product"></a><a name="Product"></a> Produto  
 A `product` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`Id`|cadeia (de carateres)|Identificador de recursos. Identifica exclusivamente o produto dentro da atual instância de serviço de Gestão da API. O valor é um URL relativo válido no formato de `products/{pid}` onde `{pid}` está um identificador de produto. Esta propriedade é só para leitura.|  
|`Title`|string|Nome do produto. Não deve estar vazio. O comprimento máximo é de 100 caracteres.|  
|`Description`|string|Descrição do produto. Não deve estar vazio. Pode incluir tags de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`Terms`|string|Termos de utilização do produto. Os desenvolvedores que tentarem subscrever o produto serão apresentados e obrigados a aceitar estes termos antes de poderem concluir o processo de subscrição.|  
|`ProductState`|número|Especifica se o produto é publicado ou não. Os produtos publicados são detetáveis pelos desenvolvedores no portal do desenvolvedor. Os produtos não publicados só são visíveis aos administradores.<br /><br /> Os valores admissíveis para o estado do produto são:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|boolean|Especifica se um utilizador pode ter várias subscrições deste produto ao mesmo tempo.|  
|`MultipleSubscriptionsCount`|número|O número máximo de subscrições deste produto é permitido por um utilizador ter ao mesmo tempo.|  
  
##  <a name="provider"></a><a name="Provider"></a> Provedor  
 A `provider` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`Properties`|dicionário de cordas|Propriedades para este fornecedor de autenticação.|  
|`AuthenticationType`|string|O tipo de fornecedor. (Azure Ative Directy, Login do Facebook, Google Account, Microsoft Account, Twitter).|  
|`Caption`|string|Mostrar o nome do fornecedor.|  
  
##  <a name="representation"></a><a name="Representation"></a> Representação  
 Esta secção descreve um `representation` .  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`contentType`|cadeia (de carateres)|Especifica um tipo de conteúdo registado ou personalizado para esta representação, por exemplo, `application/xml` .|  
|`sample`|string|Um exemplo da representação.|  
  
##  <a name="subscription"></a><a name="Subscription"></a> Assinatura  
 A `subscription` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`Id`|cadeia (de carateres)|Identificador de recursos. Identifica exclusivamente a subscrição no âmbito da atual instância de serviço de Gestão da API. O valor é um URL relativo válido no formato de onde está um identificador de `subscriptions/{sid}` `{sid}` subscrição. Esta propriedade é só para leitura.|  
|`ProductId`|string|O identificador de recursos do produto do produto subscrito. O valor é um URL relativo válido no formato de `products/{pid}` onde `{pid}` está um identificador de produto.|  
|`ProductTitle`|string|Nome do produto. Não deve estar vazio. O comprimento máximo é de 100 caracteres.|  
|`ProductDescription`|string|Descrição do produto. Não deve estar vazio. Pode incluir tags de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`ProductDetailsUrl`|string|URL relativo aos detalhes do produto.|  
|`state`|string|O estado da assinatura. Os estados possíveis são:<br /><br /> - `0 - suspended` – a subscrição está bloqueada e o assinante não pode chamar APIs do produto.<br /><br /> - `1 - active` – a subscrição está ativa.<br /><br /> - `2 - expired` – a subscrição atingiu a data de validade e foi desativada.<br /><br /> - `3 - submitted` – o pedido de subscrição foi feito pelo desenvolvedor, mas ainda não foi aprovado ou rejeitado.<br /><br /> - `4 - rejected` – o pedido de subscrição foi negado por um administrador.<br /><br /> - `5 - cancelled` – a subscrição foi cancelada pelo desenvolvedor ou administrador.|  
|`DisplayName`|string|Mostrar o nome da subscrição.|  
|`CreatedDate`|dataTime|A data em que a subscrição foi criada, no formato ISO 8601: `2014-06-24T16:25:00Z` .|  
|`CanBeCancelled`|boolean|Se a subscrição pode ser cancelada pelo utilizador atual.|  
|`IsAwaitingApproval`|boolean|Se a subscrição está a aguardar aprovação.|  
|`StartDate`|dataTime|A data de início da subscrição, no formato ISO 8601: `2014-06-24T16:25:00Z` .|  
|`ExpirationDate`|dataTime|A data de validade da subscrição, no formato ISO 8601: `2014-06-24T16:25:00Z` .|  
|`NotificationDate`|dataTime|A data de notificação da subscrição, no formato ISO 8601: `2014-06-24T16:25:00Z` .|  
|`primaryKey`|string|A chave de assinatura primária. O comprimento máximo é de 256 caracteres.|  
|`secondaryKey`|string|A chave de assinatura secundária. O comprimento máximo é de 256 caracteres.|  
|`CanBeRenewed`|boolean|Se a subscrição pode ser renovada pelo utilizador atual.|  
|`HasExpired`|boolean|Se a subscrição expirou.|  
|`IsRejected`|boolean|Se o pedido de subscrição foi negado.|  
|`CancelUrl`|string|O Url relativo para cancelar a subscrição.|  
|`RenewUrl`|string|O Url relativo para renovar a subscrição.|  
  
##  <a name="subscription-summary"></a><a name="SubscriptionSummary"></a> Resumo da assinatura  
 A `subscription summary` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`Id`|cadeia (de carateres)|Identificador de recursos. Identifica exclusivamente a subscrição no âmbito da atual instância de serviço de Gestão da API. O valor é um URL relativo válido no formato de onde está um identificador de `subscriptions/{sid}` `{sid}` subscrição. Esta propriedade é só para leitura.|  
|`DisplayName`|string|O nome de exibição da subscrição|  
  
##  <a name="user-account-info"></a><a name="UserAccountInfo"></a> Informação da conta do utilizador  
 A `user account info` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`FirstName`|cadeia (de carateres)|Primeiro nome. Não deve estar vazio. O comprimento máximo é de 100 caracteres.|  
|`LastName`|string|Sobrenome. Não deve estar vazio. O comprimento máximo é de 100 caracteres.|  
|`Email`|string|Endereço de e-mail. Não deve estar vazio e deve ser único dentro da instância de serviço. O comprimento máximo é de 254 caracteres.|  
|`Password`|string|Senha de conta de utilizador.|  
|`NameIdentifier`|string|Identificador de conta, o mesmo que o e-mail do utilizador.|  
|`ProviderName`|string|Nome do fornecedor de autenticação.|  
|`IsBasicAccount`|boolean|Verdade se esta conta foi registada usando e-mail e senha; falso se a conta foi registada usando um provedor.|  
  
##  <a name="user-sign-in"></a><a name="UseSignIn"></a> Sindução do utilizador  
 A `user sign in` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`Email`|cadeia (de carateres)|Endereço de e-mail. Não deve estar vazio e deve ser único dentro da instância de serviço. O comprimento máximo é de 254 caracteres.|  
|`Password`|string|Senha de conta de utilizador.|  
|`ReturnUrl`|string|O URL da página onde o utilizador clicou em iniciar sposição.|  
|`RememberMe`|boolean|Se guardar as informações do utilizador atual.|  
|`RegistrationEnabled`|boolean|Se o registo está habilitado.|  
|`DelegationEnabled`|boolean|Se o sinal delegado está ativado.|  
|`DelegationUrl`|string|O sinal delegado em url, se ativado.|  
|`SsoSignUpUrl`|string|O único sinal no URL para o utilizador, se estiver presente.|  
|`AuxServiceUrl`|string|Se o utilizador atual for um administrador, este é um link para a instância de serviço no portal Azure.|  
|`Providers`|Recolha de entidades [fornecedoras](#Provider)|Os fornecedores de autenticação deste utilizador.|  
|`UserRegistrationTerms`|string|Termos que um utilizador deve concordar antes de iniciar sessão.|  
|`UserRegistrationTermsEnabled`|boolean|Se os termos estão ativados.|  
  
##  <a name="user-sign-up"></a><a name="UserSignUp"></a> Inscrição do utilizador  
 A `user sign up` entidade tem os seguintes imóveis:  
  
|Propriedade|Tipo|Description|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|boolean|Valor utilizado pelo controlo [de](api-management-page-controls.md#sign-up)inscrição.|  
|`Password`|string|Senha de conta de utilizador.|  
|`PasswordVerdictLevel`|número|Valor utilizado pelo controlo [de](api-management-page-controls.md#sign-up)inscrição.|  
|`UserRegistrationTerms`|string|Termos que um utilizador deve concordar antes de iniciar sessão.|  
|`UserRegistrationTermsOptions`|número|Valor utilizado pelo controlo [de](api-management-page-controls.md#sign-up)inscrição.|  
|`ConsentAccepted`|boolean|Valor utilizado pelo controlo [de](api-management-page-controls.md#sign-up)inscrição.|  
|`Email`|string|Endereço de e-mail. Não deve estar vazio e deve ser único dentro da instância de serviço. O comprimento máximo é de 254 caracteres.|  
|`FirstName`|string|Primeiro nome. Não deve estar vazio. O comprimento máximo é de 100 caracteres.|  
|`LastName`|string|Sobrenome. Não deve estar vazio. O comprimento máximo é de 100 caracteres.|  
|`UserData`|string|Valor utilizado pelo controlo [de inscrição.](api-management-page-controls.md#sign-up)|  
|`NameIdentifier`|string|Valor utilizado pelo controlo [de](api-management-page-controls.md#sign-up)inscrição.|  
|`ProviderName`|string|Nome do fornecedor de autenticação.|

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o trabalho com modelos, consulte [como personalizar o portal de desenvolvedores da API Management utilizando modelos](api-management-developer-portal-templates.md).
