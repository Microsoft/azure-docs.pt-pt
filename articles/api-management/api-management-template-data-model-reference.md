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
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 323b3effb4c4a63d03ab7ea5251e0d59271d9dcd
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072141"
---
# <a name="azure-api-management-template-data-model-reference"></a>Referência de modelo de dados do modelo de gerenciamento de API do Azure
Este tópico descreve as representações de entidade e tipo para itens comuns usados nos modelos de dados para os modelos de portal do desenvolvedor no gerenciamento de API do Azure.  
  
 Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do desenvolvedor de gerenciamento de API usando modelos](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

O portal do desenvolvedor não está disponível na camada de consumo.

## <a name="reference"></a>Referência

-   [API](#API)  
-   [Resumo da API](#APISummary)  
-   [Aplicação](#Application)  
-   [Associação](#Attachment)  
-   [Exemplo de código](#Sample)  
-   [Mente](#Comment)  
-   [Filtragem](#Filtering)  
-   [Cabeçalho](#Header)  
-   [Solicitação HTTP](#HTTPRequest)  
-   [Resposta HTTP](#HTTPResponse)  
-   [Lo](#Issue)  
-   [Operação](#Operation)  
-   [Menu de operação](#Menu)  
-   [Item de menu de operação](#MenuItem)  
-   [Houver](#Paging)  
-   [Parâmetro](#Parameter)  
-   [Remessa](#Product)  
-   [Fornecedor](#Provider)  
-   [Representação](#Representation)  
-   [Subscrição](#Subscription)  
-   [Resumo da assinatura](#SubscriptionSummary)  
-   [Informações da conta de usuário](#UserAccountInfo)  
-   [Entrada do usuário](#UseSignIn)  
-   [Inscrição do usuário](#UserSignUp)  
  
##  <a name="API"></a>API  
 A `API` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`id`|Cadeia de caracteres|Identificador de recurso. Identifica exclusivamente a API na instância do serviço de gerenciamento de API atual. O valor é uma URL relativa válida no formato de onde `apis/{id}` `{id}` é um identificador de API. Esta propriedade é somente leitura.|  
|`name`|Cadeia de caracteres|Nome da API. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`description`|Cadeia de caracteres|Descrição da API. Não deve ficar vazio. Pode incluir marcas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`serviceUrl`|Cadeia de caracteres|URL absoluta do serviço de back-end que implementa essa API.|  
|`path`|Cadeia de caracteres|URL relativa que identifica exclusivamente essa API e todos os seus caminhos de recursos na instância do serviço de gerenciamento de API. Ele é anexado à URL base do ponto de extremidade da API especificada durante a criação da instância de serviço para formar uma URL pública para essa API.|  
|`protocols`|matriz de número|Descreve em quais protocolos as operações nesta API podem ser invocadas. Os valores permitidos `1 - http` são `2 - https`e, ou ambos.|  
|`authenticationSettings`|[Configurações de autenticação do servidor de autorização](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Coleção de configurações de autenticação incluídas nesta API.|  
|`subscriptionKeyParameterNames`|object|Propriedade opcional que pode ser usada para especificar nomes personalizados para parâmetros de consulta e/ou cabeçalho que contêm a chave de assinatura. Quando essa propriedade está presente, ela deve conter pelo menos uma das duas propriedades a seguir.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a>Resumo da API  
 A `API summary` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`id`|Cadeia de caracteres|Identificador de recurso. Identifica exclusivamente a API na instância do serviço de gerenciamento de API atual. O valor é uma URL relativa válida no formato de onde `apis/{id}` `{id}` é um identificador de API. Esta propriedade é somente leitura.|  
|`name`|Cadeia de caracteres|Nome da API. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`description`|Cadeia de caracteres|Descrição da API. Não deve ficar vazio. Pode incluir marcas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
  
##  <a name="Application"></a>Aplicativo  
 A `application` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|Cadeia de caracteres|O identificador exclusivo do aplicativo.|  
|`Title`|Cadeia de caracteres|O título do aplicativo.|  
|`Description`|Cadeia de caracteres|A descrição do aplicativo.|  
|`Url`|URI|O URI para o aplicativo.|  
|`Version`|Cadeia de caracteres|Informações de versão do aplicativo.|  
|`Requirements`|Cadeia de caracteres|Uma descrição dos requisitos para o aplicativo.|  
|`State`|número|O estado atual do aplicativo.<br /><br /> -0-registrado<br /><br /> -1-enviado<br /><br /> -2-publicado<br /><br /> -3-rejeitado<br /><br /> -4-não publicado|  
|`RegistrationDate`|DateTime|A data e a hora em que o aplicativo foi registrado.|  
|`CategoryId`|número|A categoria do aplicativo (finanças, entretenimento, etc.)|  
|`DeveloperId`|Cadeia de caracteres|O identificador exclusivo do desenvolvedor que enviou o aplicativo.|  
|`Attachments`|Coleção de entidades de [anexo](#Attachment) .|Todos os anexos do aplicativo, como capturas de tela ou ícones.|  
|`Icon`|[Associação](#Attachment)|O ícone do para o aplicativo.|  
  
##  <a name="Attachment"></a>Associação  
 A `attachment` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`UniqueId`|Cadeia de caracteres|O identificador exclusivo para o anexo.|  
|`Url`|Cadeia de caracteres|A URL do recurso.|  
|`Type`|Cadeia de caracteres|O tipo de anexo.|  
|`ContentType`|Cadeia de caracteres|O tipo de mídia do anexo.|  
  
##  <a name="Sample"></a>Exemplo de código  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`title`|Cadeia de caracteres|O nome da operação.|  
|`snippet`|Cadeia de caracteres|Esta propriedade foi preterida e não deve ser usada.|  
|`brush`|Cadeia de caracteres|Qual modelo de cores de sintaxe de código a ser usado ao exibir o exemplo de código. Os valores permitidos `plain`são `php` `java`, ,,`xml`,,, e .`csharp` `objc` `python` `ruby`|  
|`template`|Cadeia de caracteres|O nome deste modelo de exemplo de código.|  
|`body`|Cadeia de caracteres|Um espaço reservado para a parte de exemplo de código do trecho.|  
|`method`|Cadeia de caracteres|O método HTTP da operação.|  
|`scheme`|Cadeia de caracteres|O protocolo a ser usado para a solicitação de operação.|  
|`path`|Cadeia de caracteres|O caminho da operação.|  
|`query`|Cadeia de caracteres|Exemplo de cadeia de caracteres de consulta com parâmetros definidos.|  
|`host`|Cadeia de caracteres|A URL do gateway de serviço de gerenciamento de API para a API que contém essa operação.|  
|`headers`|Coleção de entidades de [cabeçalho](#Header) .|Cabeçalhos para esta operação.|  
|`parameters`|Coleção de entidades de [parâmetro](#Parameter) .|Parâmetros que são definidos para esta operação.|  
  
##  <a name="Comment"></a>Mente  
 A `API` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|número|A ID do comentário.|  
|`CommentText`|Cadeia de caracteres|O corpo do comentário. Pode incluir HTML.|  
|`DeveloperCompany`|Cadeia de caracteres|O nome da empresa do desenvolvedor.|  
|`PostedOn`|DateTime|A data e a hora em que o comentário foi Postado.|  
  
##  <a name="Issue"></a>Lo  
 A `issue` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|Cadeia de caracteres|O identificador exclusivo do problema.|  
|`ApiID`|Cadeia de caracteres|A ID da API para a qual esse problema foi relatado.|  
|`Title`|Cadeia de caracteres|Título do problema.|  
|`Description`|Cadeia de caracteres|Descrição do problema.|  
|`SubscriptionDeveloperName`|Cadeia de caracteres|Nome do desenvolvedor que relatou o problema.|  
|`IssueState`|Cadeia de caracteres|O estado atual do problema. Os valores possíveis são proposto, aberto e fechado.|  
|`ReportedOn`|DateTime|A data e a hora em que o problema foi relatado.|  
|`Comments`|Coleção de entidades de [Comentário](#Comment) .|Comentários sobre esse problema.|  
|`Attachments`|Coleção de entidades de [anexo](api-management-template-data-model-reference.md#Attachment) .|Todos os anexos do problema.|  
|`Services`|Coleção de entidades de [API](#API) .|As APIs assinadas pelo usuário que arquivaram o problema.|  
  
##  <a name="Filtering"></a>Aplica  
 A `filtering` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Pattern`|Cadeia de caracteres|O termo de pesquisa atual; ou `null` se não houver nenhum termo de pesquisa.|  
|`Placeholder`|Cadeia de caracteres|O texto a ser exibido na caixa de pesquisa quando não há nenhum termo de pesquisa especificado.|  
  
##  <a name="Header"></a>Verga  
 Esta seção descreve a `parameter` representação.  
  
|Propriedade|Tipo|Descrição|  
|--------------|-----------------|----------|  
|`name`|Cadeia de caracteres|Nome do parâmetro.|  
|`description`|Cadeia de caracteres|Descrição do parâmetro.|  
|`value`|Cadeia de caracteres|Valor do cabeçalho.|  
|`typeName`|Cadeia de caracteres|Tipo de dados do valor do cabeçalho.|  
|`options`|Cadeia de caracteres|Opções.|  
|`required`|boolean|Se o cabeçalho é necessário.|  
|`readOnly`|boolean|Se o cabeçalho é somente leitura.|  
  
##  <a name="HTTPRequest"></a>Solicitação HTTP  
 Esta seção descreve a `request` representação.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`description`|Cadeia de caracteres|Descrição da solicitação de operação.|  
|`headers`|matriz de entidades de [cabeçalho](#Header) .|Cabeçalhos de solicitação.|  
|`parameters`|matriz de [parâmetro](#Parameter)|Coleção de parâmetros de solicitação de operação.|  
|`representations`|matriz de [representação](#Representation)|Coleção de representações de solicitação de operação.|  
  
##  <a name="HTTPResponse"></a>Resposta HTTP  
 Esta seção descreve a `response` representação.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`statusCode`|inteiro positivo|Código de status de resposta da operação.|  
|`description`|Cadeia de caracteres|Descrição da resposta da operação.|  
|`representations`|matriz de [representação](#Representation)|Coleção de representações de resposta de operação.|  
  
##  <a name="Operation"></a>Operacional  
 A `operation` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`id`|Cadeia de caracteres|Identificador de recurso. Identifica exclusivamente a operação na instância atual do serviço de gerenciamento de API. O valor é uma URL relativa válida no formato de onde `apis/{aid}/operations/{id}` `{aid}` é um identificador de API e `{id}` é um identificador de operação. Esta propriedade é somente leitura.|  
|`name`|Cadeia de caracteres|Nome da operação. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`description`|Cadeia de caracteres|Descrição da operação. Não deve ficar vazio. Pode incluir marcas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`scheme`|Cadeia de caracteres|Descreve em quais protocolos as operações nesta API podem ser invocadas. Os valores permitidos `http`são `https`,, ou `http` e `https`.|  
|`uriTemplate`|Cadeia de caracteres|Modelo de URL relativa que identifica o recurso de destino para esta operação. Pode incluir parâmetros. Exemplo: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|Cadeia de caracteres|A URL do gateway de gerenciamento de API que hospeda a API.|  
|`httpMethod`|Cadeia de caracteres|Método HTTP da operação.|  
|`request`|[Solicitação HTTP](#HTTPRequest)|Uma entidade que contém os detalhes da solicitação.|  
|`responses`|matriz de [resposta http](#HTTPResponse)|Matriz de entidades de [resposta http](#HTTPResponse) de operação.|  
  
##  <a name="Menu"></a>Menu de operação  
 A `operation menu` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`ApiId`|Cadeia de caracteres|A ID da API atual.|  
|`CurrentOperationId`|Cadeia de caracteres|A ID da operação atual.|  
|`Action`|Cadeia de caracteres|O tipo de menu.|  
|`MenuItems`|Coleção de entidades de [item de menu de operação](#MenuItem) .|As operações para a API atual.|  
  
##  <a name="MenuItem"></a>Item de menu de operação  
 A `operation menu item` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|Cadeia de caracteres|A ID da operação.|  
|`Title`|Cadeia de caracteres|A descrição da operação.|  
|`HttpMethod`|Cadeia de caracteres|O método http da operação.|  
  
##  <a name="Paging"></a>Houver  
 A `paging` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Page`|número|O número da página atual.|  
|`PageSize`|número|O máximo de resultados a serem exibidos em uma única página.|  
|`TotalItemCount`|número|O número de itens para exibição.|  
|`ShowAll`|boolean|Se deseja mostrar todos os resultados em uma única página.|  
|`PageCount`|número|O número de páginas de resultados.|  
  
##  <a name="Parameter"></a>Meter  
 Esta seção descreve a `parameter` representação.  
  
|Propriedade|Tipo|Descrição|  
|--------------|-----------------|----------|  
|`name`|Cadeia de caracteres|Nome do parâmetro.|  
|`description`|Cadeia de caracteres|Descrição do parâmetro.|  
|`value`|Cadeia de caracteres|Valor do parâmetro.|  
|`options`|matriz da cadeia|Valores definidos para valores de parâmetro de consulta.|  
|`required`|boolean|Especifica se o parâmetro é obrigatório ou não.|  
|`kind`|número|Se esse parâmetro é um parâmetro de caminho (1) ou um parâmetro de QueryString (2).|  
|`typeName`|Cadeia de caracteres|Tipo de parâmetro.|  
  
##  <a name="Product"></a>Remessa  
 A `product` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|Cadeia de caracteres|Identificador de recurso. Identifica exclusivamente o produto na instância atual do serviço de gerenciamento de API. O valor é uma URL relativa válida no formato de em `products/{pid}` que `{pid}` é um identificador de produto. Esta propriedade é somente leitura.|  
|`Title`|Cadeia de caracteres|Nome do produto. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`Description`|Cadeia de caracteres|Descrição do produto. Não deve ficar vazio. Pode incluir marcas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`Terms`|Cadeia de caracteres|Termos de uso do produto. Os desenvolvedores que tentarem assinar o produto serão apresentados e precisarão aceitar esses termos antes que possam concluir o processo de assinatura.|  
|`ProductState`|número|Especifica se o produto foi publicado ou não. Os produtos publicados são detectáveis por desenvolvedores no portal do desenvolvedor. Produtos não publicados são visíveis apenas para administradores.<br /><br /> Os valores permitidos para o estado do produto são:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|boolean|Especifica se um usuário pode ter várias assinaturas para este produto ao mesmo tempo.|  
|`MultipleSubscriptionsCount`|número|Número máximo de assinaturas para este produto que um usuário tem permissão para ter ao mesmo tempo.|  
  
##  <a name="Provider"></a>Operador  
 A `provider` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Properties`|dicionário de cadeias de caracteres|Propriedades para este provedor de autenticação.|  
|`AuthenticationType`|Cadeia de caracteres|O tipo de provedor. (Azure Active Directory, logon do Facebook, conta do Google, conta da Microsoft, Twitter).|  
|`Caption`|Cadeia de caracteres|Nome de exibição do provedor.|  
  
##  <a name="Representation"></a>Representação  
 Esta seção descreve um `representation`.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`contentType`|Cadeia de caracteres|Especifica um tipo de conteúdo registrado ou personalizado para essa representação, por exemplo `application/xml`,.|  
|`sample`|Cadeia de caracteres|Um exemplo da representação.|  
  
##  <a name="Subscription"></a>Scriçõe  
 A `subscription` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|Cadeia de caracteres|Identificador de recurso. Identifica exclusivamente a assinatura na instância atual do serviço de gerenciamento de API. O valor é uma URL relativa válida no formato de onde `subscriptions/{sid}` `{sid}` é um identificador de assinatura. Esta propriedade é somente leitura.|  
|`ProductId`|Cadeia de caracteres|O identificador de recurso de produto do produto assinado. O valor é uma URL relativa válida no formato de em `products/{pid}` que `{pid}` é um identificador de produto.|  
|`ProductTitle`|Cadeia de caracteres|Nome do produto. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`ProductDescription`|Cadeia de caracteres|Descrição do produto. Não deve ficar vazio. Pode incluir marcas de formatação HTML. O comprimento máximo é de 1000 caracteres.|  
|`ProductDetailsUrl`|Cadeia de caracteres|URL relativa para os detalhes do produto.|  
|`state`|Cadeia de caracteres|O estado da assinatura. Os Estados possíveis são:<br /><br /> - `0 - suspended`– a assinatura está bloqueada e o Assinante não pode chamar nenhuma API do produto.<br /><br /> - `1 - active`– a assinatura está ativa.<br /><br /> - `2 - expired`– a assinatura atingiu sua data de validade e foi desativada.<br /><br /> - `3 - submitted`– a solicitação de assinatura foi feita pelo desenvolvedor, mas ainda não foi aprovada ou rejeitada.<br /><br /> - `4 - rejected`– a solicitação de assinatura foi negada por um administrador.<br /><br /> - `5 - cancelled`– a assinatura foi cancelada pelo desenvolvedor ou pelo administrador.|  
|`DisplayName`|Cadeia de caracteres|Nome de exibição da assinatura.|  
|`CreatedDate`|DateTime|A data em que a assinatura foi criada, no formato ISO `2014-06-24T16:25:00Z`8601:.|  
|`CanBeCancelled`|boolean|Se a assinatura pode ser cancelada pelo usuário atual.|  
|`IsAwaitingApproval`|boolean|Se a assinatura está aguardando aprovação.|  
|`StartDate`|DateTime|A data de início da assinatura, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`ExpirationDate`|DateTime|A data de validade da assinatura, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`NotificationDate`|DateTime|A data de notificação para a assinatura, no formato ISO 8601 `2014-06-24T16:25:00Z`:.|  
|`primaryKey`|Cadeia de caracteres|A chave de assinatura primária. O comprimento máximo é de 256 caracteres.|  
|`secondaryKey`|Cadeia de caracteres|A chave de assinatura secundária. O comprimento máximo é de 256 caracteres.|  
|`CanBeRenewed`|boolean|Se a assinatura pode ser renovada pelo usuário atual.|  
|`HasExpired`|boolean|Se a assinatura expirou.|  
|`IsRejected`|boolean|Se a solicitação de assinatura foi negada.|  
|`CancelUrl`|Cadeia de caracteres|A URL relativa para cancelar a assinatura.|  
|`RenewUrl`|Cadeia de caracteres|A URL relativa para renovar a assinatura.|  
  
##  <a name="SubscriptionSummary"></a>Resumo da assinatura  
 A `subscription summary` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|Cadeia de caracteres|Identificador de recurso. Identifica exclusivamente a assinatura na instância atual do serviço de gerenciamento de API. O valor é uma URL relativa válida no formato de onde `subscriptions/{sid}` `{sid}` é um identificador de assinatura. Esta propriedade é somente leitura.|  
|`DisplayName`|Cadeia de caracteres|O nome de exibição da assinatura|  
  
##  <a name="UserAccountInfo"></a>Informações da conta de usuário  
 A `user account info` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`FirstName`|Cadeia de caracteres|Nome. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`LastName`|Cadeia de caracteres|Sobrenome. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`Email`|Cadeia de caracteres|Endereço de e-mail. Não deve estar vazio e deve ser exclusivo na instância do serviço. O comprimento máximo é de 254 caracteres.|  
|`Password`|Cadeia de caracteres|Senha da conta de usuário.|  
|`NameIdentifier`|Cadeia de caracteres|Identificador de conta, o mesmo que o email do usuário.|  
|`ProviderName`|Cadeia de caracteres|Nome do provedor de autenticação.|  
|`IsBasicAccount`|boolean|True se essa conta tiver sido registrada usando email e senha; false se a conta foi registrada usando um provedor.|  
  
##  <a name="UseSignIn"></a>Entrada do usuário  
 A `user sign in` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Email`|Cadeia de caracteres|Endereço de e-mail. Não deve estar vazio e deve ser exclusivo na instância do serviço. O comprimento máximo é de 254 caracteres.|  
|`Password`|Cadeia de caracteres|Senha da conta de usuário.|  
|`ReturnUrl`|Cadeia de caracteres|A URL da página em que o usuário clicou para entrar.|  
|`RememberMe`|boolean|Se as informações do usuário atual devem ser salvas.|  
|`RegistrationEnabled`|boolean|Se o registro está habilitado.|  
|`DelegationEnabled`|boolean|Se a entrada delegada está habilitada.|  
|`DelegationUrl`|Cadeia de caracteres|A URL de entrada delegada, se habilitada.|  
|`SsoSignUpUrl`|Cadeia de caracteres|A URL de logon único para o usuário, se presente.|  
|`AuxServiceUrl`|Cadeia de caracteres|Se o usuário atual for um administrador, esse será um link para a instância de serviço no portal do Azure.|  
|`Providers`|Coleção de entidades de [provedor](#Provider)|Os provedores de autenticação para este usuário.|  
|`UserRegistrationTerms`|Cadeia de caracteres|Termos que um usuário deve concordar antes de entrar.|  
|`UserRegistrationTermsEnabled`|boolean|Se os termos estão habilitados.|  
  
##  <a name="UserSignUp"></a>Inscrição do usuário  
 A `user sign up` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|boolean|Valor usado pelo controle de inscrição de [inscrição](api-management-page-controls.md#sign-up).|  
|`Password`|Cadeia de caracteres|Senha da conta de usuário.|  
|`PasswordVerdictLevel`|número|Valor usado pelo controle de inscrição de [inscrição](api-management-page-controls.md#sign-up).|  
|`UserRegistrationTerms`|Cadeia de caracteres|Termos que um usuário deve concordar antes de entrar.|  
|`UserRegistrationTermsOptions`|número|Valor usado pelo controle de inscrição de [inscrição](api-management-page-controls.md#sign-up).|  
|`ConsentAccepted`|boolean|Valor usado pelo controle de inscrição de [inscrição](api-management-page-controls.md#sign-up).|  
|`Email`|Cadeia de caracteres|Endereço de e-mail. Não deve estar vazio e deve ser exclusivo na instância do serviço. O comprimento máximo é de 254 caracteres.|  
|`FirstName`|Cadeia de caracteres|Nome. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`LastName`|Cadeia de caracteres|Sobrenome. Não deve ficar vazio. O comprimento máximo é de 100 caracteres.|  
|`UserData`|Cadeia de caracteres|Valor usado pelo controle de [inscrição](api-management-page-controls.md#sign-up) .|  
|`NameIdentifier`|Cadeia de caracteres|Valor usado pelo controle de inscrição de [inscrição](api-management-page-controls.md#sign-up).|  
|`ProviderName`|Cadeia de caracteres|Nome do provedor de autenticação.|

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do desenvolvedor de gerenciamento de API usando modelos](api-management-developer-portal-templates.md).
