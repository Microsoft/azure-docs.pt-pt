---
title: Referência de modelo de dados do modelo de gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre as representações de entidade e tipo para itens comuns usados nos modelos de dados para os modelos de portal do desenvolvedor no gerenciamento de API do Azure.
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
ms.openlocfilehash: 5625ff7e4fc51b9b6b894698719247902a480f44
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176532"
---
# <a name="azure-api-management-template-data-model-reference"></a>Referência de modelo de dados do modelo de gerenciamento de API do Azure
Este tópico descreve as representações de entidade e tipo para itens comuns usados nos modelos de dados para os modelos de portal do desenvolvedor no gerenciamento de API do Azure.  
  
 Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do desenvolvedor de gerenciamento de API usando modelos](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="reference"></a>Referência

-   [API](#API)  
-   [Resumo da API](#APISummary)  
-   [Aplicativo](#Application)  
-   [Associação](#Attachment)  
-   [Exemplo de código](#Sample)  
-   [Mente](#Comment)  
-   [Aplica](#Filtering)  
-   [Cabeçalho](#Header)  
-   [Solicitação HTTP](#HTTPRequest)  
-   [Resposta HTTP](#HTTPResponse)  
-   [Lo](#Issue)  
-   [Operação](#Operation)  
-   [Menu de operação](#Menu)  
-   [Item de menu de operação](#MenuItem)  
-   [Houver](#Paging)  
-   [Meter](#Parameter)  
-   [Remessa](#Product)  
-   [Fornecedor](#Provider)  
-   [Representação](#Representation)  
-   [Subscrição](#Subscription)  
-   [Resumo da assinatura](#SubscriptionSummary)  
-   [Informações da conta de usuário](#UserAccountInfo)  
-   [Entrada do usuário](#UseSignIn)  
-   [Inscrição do usuário](#UserSignUp)  
  
##  <a name="API"></a>API  
 A entidade `API` tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`id`|string|Identificador de recurso. Identifica exclusivamente a API na instância do serviço de gerenciamento de API atual. O valor é uma URL relativa válida no formato de `apis/{id}` em que `{id}` é um identificador de API. Esta propriedade é somente leitura.|  
|`name`|string|Nome da API. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`description`|string|Descrição da API. Não deve ficar vazio. Pode incluir marcas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`serviceUrl`|string|URL absoluta do serviço de back-end que implementa essa API.|  
|`path`|string|URL relativa que identifica exclusivamente essa API e todos os seus caminhos de recursos na instância do serviço de gerenciamento de API. Ele é anexado à URL base do ponto de extremidade da API especificada durante a criação da instância de serviço para formar uma URL pública para essa API.|  
|`protocols`|matriz de número|Descreve em quais protocolos as operações nesta API podem ser invocadas. Os valores permitidos são `1 - http` e `2 - https`, ou ambos.|  
|`authenticationSettings`|[Configurações de autenticação do servidor de autorização](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Coleção de configurações de autenticação incluídas nesta API.|  
|`subscriptionKeyParameterNames`|objeto|Propriedade opcional que pode ser usada para especificar nomes personalizados para parâmetros de consulta e/ou cabeçalho que contêm a chave de assinatura. Quando essa propriedade está presente, ela deve conter pelo menos uma das duas propriedades a seguir.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a>Resumo da API  
 A entidade `API summary` tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`id`|string|Identificador de recurso. Identifica exclusivamente a API na instância do serviço de gerenciamento de API atual. O valor é uma URL relativa válida no formato de `apis/{id}` em que `{id}` é um identificador de API. Esta propriedade é somente leitura.|  
|`name`|string|Nome da API. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`description`|string|Descrição da API. Não deve ficar vazio. Pode incluir marcas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
  
##  <a name="Application"></a>Aplicativo  
 A entidade `application` tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|string|O identificador exclusivo do aplicativo.|  
|`Title`|string|O título do aplicativo.|  
|`Description`|string|A descrição do aplicativo.|  
|`Url`|URI|O URI para o aplicativo.|  
|`Version`|string|Informações de versão do aplicativo.|  
|`Requirements`|string|Uma descrição dos requisitos para o aplicativo.|  
|`State`|número|O estado atual do aplicativo.<br /><br /> -0-registrado<br /><br /> -1-enviado<br /><br /> -2-publicado<br /><br /> -3-rejeitado<br /><br /> -4-não publicado|  
|`RegistrationDate`|DateTime|A data e a hora em que o aplicativo foi registrado.|  
|`CategoryId`|número|A categoria do aplicativo (finanças, entretenimento, etc.)|  
|`DeveloperId`|string|O identificador exclusivo do desenvolvedor que enviou o aplicativo.|  
|`Attachments`|Coleção de entidades de [anexo](#Attachment) .|Todos os anexos do aplicativo, como capturas de tela ou ícones.|  
|`Icon`|[Associação](#Attachment)|O ícone do para o aplicativo.|  
  
##  <a name="Attachment"></a>Associação  
 A entidade `attachment` tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`UniqueId`|string|O identificador exclusivo para o anexo.|  
|`Url`|string|A URL do recurso.|  
|`Type`|string|O tipo de anexo.|  
|`ContentType`|string|O tipo de mídia do anexo.|  
  
##  <a name="Sample"></a>Exemplo de código  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`title`|string|O nome da operação.|  
|`snippet`|string|Esta propriedade foi preterida e não deve ser usada.|  
|`brush`|string|Qual modelo de cores de sintaxe de código a ser usado ao exibir o exemplo de código. Os valores permitidos são `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby`e `csharp`.|  
|`template`|string|O nome deste modelo de exemplo de código.|  
|`body`|string|Um espaço reservado para a parte de exemplo de código do trecho.|  
|`method`|string|O método HTTP da operação.|  
|`scheme`|string|O protocolo a ser usado para a solicitação de operação.|  
|`path`|string|O caminho da operação.|  
|`query`|string|Exemplo de cadeia de caracteres de consulta com parâmetros definidos.|  
|`host`|string|A URL do gateway de serviço de gerenciamento de API para a API que contém essa operação.|  
|`headers`|Coleção de entidades de [cabeçalho](#Header) .|Cabeçalhos para esta operação.|  
|`parameters`|Coleção de entidades de [parâmetro](#Parameter) .|Parâmetros que são definidos para esta operação.|  
  
##  <a name="Comment"></a>Mente  
 A entidade `API` tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|número|A ID do comentário.|  
|`CommentText`|string|O corpo do comentário. Pode incluir HTML.|  
|`DeveloperCompany`|string|O nome da empresa do desenvolvedor.|  
|`PostedOn`|DateTime|A data e a hora em que o comentário foi Postado.|  
  
##  <a name="Issue"></a>Lo  
 A entidade `issue` tem as propriedades a seguir.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|string|O identificador exclusivo do problema.|  
|`ApiID`|string|A ID da API para a qual esse problema foi relatado.|  
|`Title`|string|Título do problema.|  
|`Description`|string|Descrição do problema.|  
|`SubscriptionDeveloperName`|string|Nome do desenvolvedor que relatou o problema.|  
|`IssueState`|string|O estado atual do problema. Os valores possíveis são proposto, aberto e fechado.|  
|`ReportedOn`|DateTime|A data e a hora em que o problema foi relatado.|  
|`Comments`|Coleção de entidades de [Comentário](#Comment) .|Comentários sobre esse problema.|  
|`Attachments`|Coleção de entidades de [anexo](api-management-template-data-model-reference.md#Attachment) .|Todos os anexos do problema.|  
|`Services`|Coleção de entidades de [API](#API) .|As APIs assinadas pelo usuário que arquivaram o problema.|  
  
##  <a name="Filtering"></a>Aplica  
 A entidade `filtering` tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Pattern`|string|O termo de pesquisa atual; ou `null` se não houver nenhum termo de pesquisa.|  
|`Placeholder`|string|O texto a ser exibido na caixa de pesquisa quando não há nenhum termo de pesquisa especificado.|  
  
##  <a name="Header"></a>Verga  
 Esta seção descreve a representação de `parameter`.  
  
|Propriedade|Tipo|Descrição|  
|--------------|-----------------|----------|  
|`name`|string|Nome do parâmetro.|  
|`description`|string|Descrição do parâmetro.|  
|`value`|string|Valor do cabeçalho.|  
|`typeName`|string|Tipo de dados do valor do cabeçalho.|  
|`options`|string|Opções.|  
|`required`|boolean|Se o cabeçalho é necessário.|  
|`readOnly`|boolean|Se o cabeçalho é somente leitura.|  
  
##  <a name="HTTPRequest"></a>Solicitação HTTP  
 Esta seção descreve a representação de `request`.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`description`|string|Descrição da solicitação de operação.|  
|`headers`|matriz de entidades de [cabeçalho](#Header) .|Cabeçalhos de solicitação.|  
|`parameters`|matriz de [parâmetro](#Parameter)|Coleção de parâmetros de solicitação de operação.|  
|`representations`|matriz de [representação](#Representation)|Coleção de representações de solicitação de operação.|  
  
##  <a name="HTTPResponse"></a>Resposta HTTP  
 Esta seção descreve a representação de `response`.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`statusCode`|Inteiro positivo|Código de status de resposta da operação.|  
|`description`|string|Descrição da resposta da operação.|  
|`representations`|matriz de [representação](#Representation)|Coleção de representações de resposta de operação.|  
  
##  <a name="Operation"></a>Operacional  
 A entidade `operation` tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`id`|string|Identificador de recurso. Identifica exclusivamente a operação na instância atual do serviço de gerenciamento de API. O valor é uma URL relativa válida no formato de `apis/{aid}/operations/{id}` em que `{aid}` é um identificador de API e `{id}` é um identificador de operação. Esta propriedade é somente leitura.|  
|`name`|string|Nome da operação. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`description`|string|Descrição da operação. Não deve ficar vazio. Pode incluir marcas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`scheme`|string|Descreve em quais protocolos as operações nesta API podem ser invocadas. Os valores permitidos são `http`, `https`ou ambos `http` e `https`.|  
|`uriTemplate`|string|Modelo de URL relativa que identifica o recurso de destino para esta operação. Pode incluir parâmetros. Exemplo: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|string|A URL do gateway de gerenciamento de API que hospeda a API.|  
|`httpMethod`|string|Método HTTP da operação.|  
|`request`|[Solicitação HTTP](#HTTPRequest)|Uma entidade que contém os detalhes da solicitação.|  
|`responses`|matriz de [resposta http](#HTTPResponse)|Matriz de entidades de [resposta http](#HTTPResponse) de operação.|  
  
##  <a name="Menu"></a>Menu de operação  
 A entidade `operation menu` tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`ApiId`|string|A ID da API atual.|  
|`CurrentOperationId`|string|A ID da operação atual.|  
|`Action`|string|O tipo de menu.|  
|`MenuItems`|Coleção de entidades de [item de menu de operação](#MenuItem) .|As operações para a API atual.|  
  
##  <a name="MenuItem"></a>Item de menu de operação  
 A entidade `operation menu item` tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|string|A ID da operação.|  
|`Title`|string|A descrição da operação.|  
|`HttpMethod`|string|O método http da operação.|  
  
##  <a name="Paging"></a>Houver  
 A entidade `paging` tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Page`|número|O número da página atual.|  
|`PageSize`|número|O máximo de resultados a serem exibidos em uma única página.|  
|`TotalItemCount`|número|O número de itens para exibição.|  
|`ShowAll`|boolean|Se deseja mostrar todos os resultados em uma única página.|  
|`PageCount`|número|O número de páginas de resultados.|  
  
##  <a name="Parameter"></a>Meter  
 Esta seção descreve a representação de `parameter`.  
  
|Propriedade|Tipo|Descrição|  
|--------------|-----------------|----------|  
|`name`|string|Nome do parâmetro.|  
|`description`|string|Descrição do parâmetro.|  
|`value`|string|Valor do parâmetro.|  
|`options`|matriz da cadeia|Valores definidos para valores de parâmetro de consulta.|  
|`required`|boolean|Especifica se o parâmetro é obrigatório ou não.|  
|`kind`|número|Se esse parâmetro é um parâmetro de caminho (1) ou um parâmetro de QueryString (2).|  
|`typeName`|string|Tipo de parâmetro.|  
  
##  <a name="Product"></a>Remessa  
 A entidade `product` tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|string|Identificador de recurso. Identifica exclusivamente o produto na instância atual do serviço de gerenciamento de API. O valor é uma URL relativa válida no formato de `products/{pid}` em que `{pid}` é um identificador de produto. Esta propriedade é somente leitura.|  
|`Title`|string|Nome do produto. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`Description`|string|Descrição do produto. Não deve ficar vazio. Pode incluir marcas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`Terms`|string|Termos de uso do produto. Os desenvolvedores que tentarem assinar o produto serão apresentados e precisarão aceitar esses termos antes que possam concluir o processo de assinatura.|  
|`ProductState`|número|Especifica se o produto foi publicado ou não. Os produtos publicados são detectáveis por desenvolvedores no portal do desenvolvedor. Produtos não publicados são visíveis apenas para administradores.<br /><br /> Os valores permitidos para o estado do produto são:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|boolean|Especifica se um usuário pode ter várias assinaturas para este produto ao mesmo tempo.|  
|`MultipleSubscriptionsCount`|número|Número máximo de assinaturas para este produto que um usuário tem permissão para ter ao mesmo tempo.|  
  
##  <a name="Provider"></a>Operador  
 A entidade `provider` tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Properties`|dicionário de cadeias de caracteres|Propriedades para este provedor de autenticação.|  
|`AuthenticationType`|string|O tipo de provedor. (Azure Active Directory, logon do Facebook, conta do Google, conta da Microsoft, Twitter).|  
|`Caption`|string|Nome de exibição do provedor.|  
  
##  <a name="Representation"></a>Representação  
 Esta seção descreve um `representation`.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`contentType`|string|Especifica um tipo de conteúdo registrado ou personalizado para essa representação, por exemplo, `application/xml`.|  
|`sample`|string|Um exemplo da representação.|  
  
##  <a name="Subscription"></a>Scriçõe  
 A entidade `subscription` tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|string|Identificador de recurso. Identifica exclusivamente a assinatura na instância atual do serviço de gerenciamento de API. O valor é uma URL relativa válida no formato de `subscriptions/{sid}` em que `{sid}` é um identificador de assinatura. Esta propriedade é somente leitura.|  
|`ProductId`|string|O identificador de recurso de produto do produto assinado. O valor é uma URL relativa válida no formato de `products/{pid}` em que `{pid}` é um identificador de produto.|  
|`ProductTitle`|string|Nome do produto. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`ProductDescription`|string|Descrição do produto. Não deve ficar vazio. Pode incluir marcas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`ProductDetailsUrl`|string|URL relativa para os detalhes do produto.|  
|`state`|string|O estado da assinatura. Os Estados possíveis são:<br /><br /> - `0 - suspended` – a assinatura está bloqueada e o Assinante não pode chamar nenhuma API do produto.<br /><br /> - `1 - active` – a assinatura está ativa.<br /><br /> - `2 - expired` – a assinatura atingiu sua data de validade e foi desativada.<br /><br /> - `3 - submitted` – a solicitação de assinatura foi feita pelo desenvolvedor, mas ainda não foi aprovada ou rejeitada.<br /><br /> - `4 - rejected` – a solicitação de assinatura foi negada por um administrador.<br /><br /> - `5 - cancelled` – a assinatura foi cancelada pelo desenvolvedor ou pelo administrador.|  
|`DisplayName`|string|Nome de exibição da assinatura.|  
|`CreatedDate`|Horário|A data em que a assinatura foi criada, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`CanBeCancelled`|boolean|Se a assinatura pode ser cancelada pelo usuário atual.|  
|`IsAwaitingApproval`|boolean|Se a assinatura está aguardando aprovação.|  
|`StartDate`|Horário|A data de início da assinatura, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`ExpirationDate`|Horário|A data de validade da assinatura, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`NotificationDate`|Horário|A data de notificação para a assinatura, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`primaryKey`|string|A chave de assinatura primária. O comprimento máximo é de 256 caracteres.|  
|`secondaryKey`|string|A chave de assinatura secundária. O comprimento máximo é de 256 caracteres.|  
|`CanBeRenewed`|boolean|Se a assinatura pode ser renovada pelo usuário atual.|  
|`HasExpired`|boolean|Se a assinatura expirou.|  
|`IsRejected`|boolean|Se a solicitação de assinatura foi negada.|  
|`CancelUrl`|string|A URL relativa para cancelar a assinatura.|  
|`RenewUrl`|string|A URL relativa para renovar a assinatura.|  
  
##  <a name="SubscriptionSummary"></a>Resumo da assinatura  
 A entidade `subscription summary` tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|string|Identificador de recurso. Identifica exclusivamente a assinatura na instância atual do serviço de gerenciamento de API. O valor é uma URL relativa válida no formato de `subscriptions/{sid}` em que `{sid}` é um identificador de assinatura. Esta propriedade é somente leitura.|  
|`DisplayName`|string|O nome de exibição da assinatura|  
  
##  <a name="UserAccountInfo"></a>Informações da conta de usuário  
 A entidade `user account info` tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`FirstName`|string|Nome. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`LastName`|string|Sobrenome. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`Email`|string|Endereço de e-mail. Não deve estar vazio e deve ser exclusivo na instância do serviço. O comprimento máximo é de 254 caracteres.|  
|`Password`|string|Senha da conta de usuário.|  
|`NameIdentifier`|string|Identificador de conta, o mesmo que o email do usuário.|  
|`ProviderName`|string|Nome do provedor de autenticação.|  
|`IsBasicAccount`|boolean|True se essa conta tiver sido registrada usando email e senha; false se a conta foi registrada usando um provedor.|  
  
##  <a name="UseSignIn"></a>Entrada do usuário  
 A entidade `user sign in` tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Email`|string|Endereço de e-mail. Não deve estar vazio e deve ser exclusivo na instância do serviço. O comprimento máximo é de 254 caracteres.|  
|`Password`|string|Senha da conta de usuário.|  
|`ReturnUrl`|string|A URL da página em que o usuário clicou para entrar.|  
|`RememberMe`|boolean|Se as informações do usuário atual devem ser salvas.|  
|`RegistrationEnabled`|boolean|Se o registro está habilitado.|  
|`DelegationEnabled`|boolean|Se a entrada delegada está habilitada.|  
|`DelegationUrl`|string|A URL de entrada delegada, se habilitada.|  
|`SsoSignUpUrl`|string|A URL de logon único para o usuário, se presente.|  
|`AuxServiceUrl`|string|Se o usuário atual for um administrador, esse será um link para a instância de serviço no portal do Azure.|  
|`Providers`|Coleção de entidades de [provedor](#Provider)|Os provedores de autenticação para este usuário.|  
|`UserRegistrationTerms`|string|Termos que um usuário deve concordar antes de entrar.|  
|`UserRegistrationTermsEnabled`|boolean|Se os termos estão habilitados.|  
  
##  <a name="UserSignUp"></a>Inscrição do usuário  
 A entidade `user sign up` tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|boolean|Valor usado pelo controle de inscrição de [inscrição](api-management-page-controls.md#sign-up).|  
|`Password`|string|Senha da conta de usuário.|  
|`PasswordVerdictLevel`|número|Valor usado pelo controle de inscrição de [inscrição](api-management-page-controls.md#sign-up).|  
|`UserRegistrationTerms`|string|Termos que um usuário deve concordar antes de entrar.|  
|`UserRegistrationTermsOptions`|número|Valor usado pelo controle de inscrição de [inscrição](api-management-page-controls.md#sign-up).|  
|`ConsentAccepted`|boolean|Valor usado pelo controle de inscrição de [inscrição](api-management-page-controls.md#sign-up).|  
|`Email`|string|Endereço de e-mail. Não deve estar vazio e deve ser exclusivo na instância do serviço. O comprimento máximo é de 254 caracteres.|  
|`FirstName`|string|Nome. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`LastName`|string|Sobrenome. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`UserData`|string|Valor usado pelo controle de [inscrição](api-management-page-controls.md#sign-up) .|  
|`NameIdentifier`|string|Valor usado pelo controle de inscrição de [inscrição](api-management-page-controls.md#sign-up).|  
|`ProviderName`|string|Nome do provedor de autenticação.|

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do desenvolvedor de gerenciamento de API usando modelos](api-management-developer-portal-templates.md).
