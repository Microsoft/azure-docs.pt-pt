---
title: Referência de modelo de dados de modelo de gestão de API do Azure | Documentos da Microsoft
description: Saiba mais sobre as representações de entidade e tipo de itens comuns utilizados em modelos de dados para os modelos de portal do programador na gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 8fb60f36bbc7c8886c1f465177a11224a1c90659
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541252"
---
# <a name="azure-api-management-template-data-model-reference"></a>Referência de modelo de dados de modelo de gestão de API do Azure
Este tópico descreve as representações de entidade e tipo de itens comuns utilizados em modelos de dados para os modelos de portal do programador na gestão de API do Azure.  
  
 Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de gestão de API através de modelos](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

O portal do Programador não está disponível na camada de consumo.

## <a name="reference"></a>Referência

-   [API](#API)  
-   [Resumo de API](#APISummary)  
-   [Aplicação](#Application)  
-   [Attachment](#Attachment)  
-   [Exemplo de código](#Sample)  
-   [Comentário](#Comment)  
-   [Filtragem](#Filtering)  
-   [Cabeçalho](#Header)  
-   [Pedido de HTTP](#HTTPRequest)  
-   [Resposta de HTTP](#HTTPResponse)  
-   [Problema](#Issue)  
-   [Operação](#Operation)  
-   [Menu de operação](#Menu)  
-   [Item de menu de operação](#MenuItem)  
-   [Paginação](#Paging)  
-   [Parâmetro](#Parameter)  
-   [Produto](#Product)  
-   [Fornecedor](#Provider)  
-   [Representação](#Representation)  
-   [Subscrição](#Subscription)  
-   [Resumo da subscrição](#SubscriptionSummary)  
-   [Informações da conta de utilizador](#UserAccountInfo)  
-   [Sessão do utilizador](#UseSignIn)  
-   [Inscrição do utilizador](#UserSignUp)  
  
##  <a name="API"></a> API  
 O `API` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`id`|string|Identificador de recurso. Identifica exclusivamente a API dentro da instância de serviço de gestão de API atual. O valor é um URL relativo válido no formato `apis/{id}` onde `{id}` é um identificador de API. Esta propriedade é só de leitura.|  
|`name`|string|Nome da API. Não pode estar vazio. Comprimento máximo é 100 carateres.|  
|`description`|string|Descrição da API. Não pode estar vazio. Pode incluir a formatação de etiquetas HTML. Comprimento máximo é de 1000 carateres.|  
|`serviceUrl`|string|URL absoluto do serviço de back-end que implementa esta API.|  
|`path`|string|URL relativo, identificando exclusivamente esta API e todos os seus caminhos de recursos dentro da instância de serviço de gestão de API. Ele é acrescentado para o ponto final base URL da API especificado durante a criação de instância de serviço para formar um URL público para esta API.|  
|`protocols`|matriz de número|Descreve em quais protocolos as operações nessa API podem ser invocadas. Valores permitidos são `1 - http` e `2 - https`, ou ambos.|  
|`authenticationSettings`|[Definições de autenticação de servidor de autorização](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Coleção de definições de autenticação incluído nesta API.|  
|`subscriptionKeyParameterNames`|objeto|Propriedade opcional que pode ser utilizada para especificar os nomes personalizados para os parâmetros de consulta e/ou de cabeçalho que contém a chave de subscrição. Quando esta propriedade estiver presente, tem de conter, pelo menos, uma das duas propriedades seguintes.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a> Resumo de API  
 O `API summary` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`id`|string|Identificador de recurso. Identifica exclusivamente a API dentro da instância de serviço de gestão de API atual. O valor é um URL relativo válido no formato `apis/{id}` onde `{id}` é um identificador de API. Esta propriedade é só de leitura.|  
|`name`|string|Nome da API. Não pode estar vazio. Comprimento máximo é 100 carateres.|  
|`description`|string|Descrição da API. Não pode estar vazio. Pode incluir a formatação de etiquetas HTML. Comprimento máximo é de 1000 carateres.|  
  
##  <a name="Application"></a> Aplicação  
 O `application` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|string|O identificador exclusivo da aplicação.|  
|`Title`|string|O título do aplicativo.|  
|`Description`|string|A descrição da aplicação.|  
|`Url`|URI|O URI para a aplicação.|  
|`Version`|string|Informações de versão para a aplicação.|  
|`Requirements`|string|Uma descrição dos requisitos para a aplicação.|  
|`State`|número|O estado atual do aplicativo.<br /><br /> -0 - registado<br /><br /> -1 - submetido<br /><br /> -2 - publicado<br /><br /> -3 - rejeitado<br /><br /> -4 - não publicado|  
|`RegistrationDate`|DateTime|A data e hora, que a aplicação foi registrada.|  
|`CategoryId`|número|A categoria da aplicação (Finanças, entretenimento, etc.)|  
|`DeveloperId`|string|O identificador exclusivo do desenvolvedor que submeteu a aplicação.|  
|`Attachments`|Coleção de [anexo](#Attachment) entidades.|Os anexos para a aplicação, tais como capturas de ecrã ou ícones.|  
|`Icon`|[Attachment](#Attachment)|O ícone da para a aplicação.|  
  
##  <a name="Attachment"></a> Anexo  
 O `attachment` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`UniqueId`|string|O identificador exclusivo para o anexo.|  
|`Url`|string|O URL do recurso.|  
|`Type`|string|O tipo de anexo.|  
|`ContentType`|string|O tipo de suporte do anexo.|  
  
##  <a name="Sample"></a> Exemplo de código  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`title`|string|O nome da operação.|  
|`snippet`|string|Esta propriedade foi preterida e não deve ser utilizada.|  
|`brush`|string|Qual código cores da sintaxe modelo a utilizar quando se apresenta o código de exemplo. Valores permitidos são `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby`, e `csharp`.|  
|`template`|string|O nome deste modelo de exemplo de código.|  
|`body`|string|Um marcador de posição para a parte do exemplo de código do trecho.|  
|`method`|string|O método HTTP da operação.|  
|`scheme`|string|O protocolo a utilizar para o pedido de operação.|  
|`path`|string|O caminho da operação.|  
|`query`|string|Exemplo de cadeia de caracteres de consulta com parâmetros definidos.|  
|`host`|string|O URL do gateway de serviço de gestão de API para a API que contém esta operação.|  
|`headers`|Coleção de [cabeçalho](#Header) entidades.|Cabeçalhos para esta operação.|  
|`parameters`|Coleção de [parâmetro](#Parameter) entidades.|Parâmetros que são definidos para esta operação.|  
  
##  <a name="Comment"></a> Comentário  
 O `API` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|número|O id do comentário.|  
|`CommentText`|string|O corpo do comentário. Pode incluir HTML.|  
|`DeveloperCompany`|string|O nome da empresa do desenvolvedor.|  
|`PostedOn`|DateTime|A data e hora, que o comentário foi postado.|  
  
##  <a name="Issue"></a> Problema  
 O `issue` entidade tem as seguintes propriedades.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|string|O identificador exclusivo para o problema.|  
|`ApiID`|string|O id para a API para o qual este problema foi relatado.|  
|`Title`|string|O título do problema|  
|`Description`|string|Descrição do problema.|  
|`SubscriptionDeveloperName`|string|Nome próprio do desenvolvedor que comunicou o problema.|  
|`IssueState`|string|O estado atual do problema. Valores possíveis são proposta, aberto, fechado.|  
|`ReportedOn`|DateTime|A data e hora, que o problema foi relatado.|  
|`Comments`|Coleção de [comentário](#Comment) entidades.|Comentários sobre este problema.|  
|`Attachments`|Coleção de [anexo](api-management-template-data-model-reference.md#Attachment) entidades.|Os anexos para o problema.|  
|`Services`|Coleção de [API](#API) entidades.|As APIs subscrito pelo utilizador que arquivou o problema.|  
  
##  <a name="Filtering"></a> Filtragem  
 O `filtering` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Pattern`|string|O termo de pesquisa atual; ou `null` se não houver nenhum termo de pesquisa.|  
|`Placeholder`|string|O texto a apresentar na caixa de pesquisa quando não existe nenhum termo de pesquisa especificado.|  
  
##  <a name="Header"></a> Cabeçalho  
 Esta secção descreve o `parameter` representação.  
  
|Propriedade|Descrição|Type|  
|--------------|-----------------|----------|  
|`name`|string|Nome do parâmetro.|  
|`description`|string|Descrição do parâmetro.|  
|`value`|string|Valor do cabeçalho.|  
|`typeName`|string|Tipo de dados do valor do cabeçalho.|  
|`options`|string|Opções.|  
|`required`|boolean|Se o cabeçalho é obrigatório.|  
|`readOnly`|boolean|Se o cabeçalho é só de leitura.|  
  
##  <a name="HTTPRequest"></a> Pedido de HTTP  
 Esta secção descreve o `request` representação.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`description`|string|Descrição do pedido de operação.|  
|`headers`|matriz de [cabeçalho](#Header) entidades.|Cabeçalhos de pedido.|  
|`parameters`|matriz de [parâmetro](#Parameter)|Coleção de parâmetros do pedido de operação.|  
|`representations`|matriz de [representação](#Representation)|Coleção de representações de pedido de operação.|  
  
##  <a name="HTTPResponse"></a> Resposta de HTTP  
 Esta secção descreve o `response` representação.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`statusCode`|número inteiro positivo|Código de estado de resposta de operação.|  
|`description`|string|Descrição de resposta de operação.|  
|`representations`|matriz de [representação](#Representation)|Coleção de representações de resposta de operação.|  
  
##  <a name="Operation"></a> Operação  
 O `operation` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`id`|string|Identificador de recurso. Identifica exclusivamente a operação dentro da instância de serviço de gestão de API atual. O valor é um URL relativo válido no formato `apis/{aid}/operations/{id}` em que `{aid}` é um identificador de API e `{id}` é um identificador de operação. Esta propriedade é só de leitura.|  
|`name`|string|Nome da operação. Não pode estar vazio. Comprimento máximo é 100 carateres.|  
|`description`|string|Descrição da operação. Não pode estar vazio. Pode incluir a formatação de etiquetas HTML. Comprimento máximo é de 1000 carateres.|  
|`scheme`|string|Descreve em quais protocolos as operações nessa API podem ser invocadas. Valores permitidos são `http`, `https`, ou ambos `http` e `https`.|  
|`uriTemplate`|string|Modelo de URL relativo identificar o recurso de destino para esta operação. Pode incluir parâmetros. Exemplo: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|string|O URL do gateway de gestão de API que aloja a API.|  
|`httpMethod`|string|Método de operação HTTP.|  
|`request`|[Pedido de HTTP](#HTTPRequest)|Uma entidade que contém detalhes do pedido.|  
|`responses`|matriz de [resposta de HTTP](#HTTPResponse)|Matriz de operação [resposta HTTP](#HTTPResponse) entidades.|  
  
##  <a name="Menu"></a> Menu de operação  
 O `operation menu` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`ApiId`|string|O id da API atual.|  
|`CurrentOperationId`|string|O id da operação atual.|  
|`Action`|string|O tipo de menu.|  
|`MenuItems`|Coleção de [item de menu de operação](#MenuItem) entidades.|As operações para a API atual.|  
  
##  <a name="MenuItem"></a> Item de menu de operação  
 O `operation menu item` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|string|O id da operação.|  
|`Title`|string|A descrição da operação.|  
|`HttpMethod`|string|O método Http da operação.|  
  
##  <a name="Paging"></a> Paginação  
 O `paging` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Page`|número|O número de página atual.|  
|`PageSize`|número|O máximo de resultados a serem exibidos numa única página.|  
|`TotalItemCount`|número|O número de itens para exibição.|  
|`ShowAll`|boolean|Se deve ser Mostr tudo resulta numa única página.|  
|`PageCount`|número|O número de páginas de resultados.|  
  
##  <a name="Parameter"></a> Parâmetro  
 Esta secção descreve o `parameter` representação.  
  
|Propriedade|Descrição|Type|  
|--------------|-----------------|----------|  
|`name`|string|Nome do parâmetro.|  
|`description`|string|Descrição do parâmetro.|  
|`value`|string|Valor do parâmetro.|  
|`options`|matriz da cadeia|Valores definidos para valores de parâmetro de consulta.|  
|`required`|boolean|Especifica se o parâmetro é necessário ou não.|  
|`kind`|número|Se este parâmetro é um parâmetro de caminho (1) ou um parâmetro de cadeia de consulta (2).|  
|`typeName`|string|Tipo de parâmetro.|  
  
##  <a name="Product"></a> Produto  
 O `product` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|string|Identificador de recurso. Identifica exclusivamente o produto dentro da instância de serviço de gestão de API atual. O valor é um URL relativo válido no formato `products/{pid}` onde `{pid}` é um identificador de produto. Esta propriedade é só de leitura.|  
|`Title`|string|Nome do produto. Não pode estar vazio. Comprimento máximo é 100 carateres.|  
|`Description`|string|Descrição do produto. Não pode estar vazio. Pode incluir a formatação de etiquetas HTML. Comprimento máximo é de 1000 carateres.|  
|`Terms`|string|Termos de produto de utilização. Os desenvolvedores tentando subscrever o produto serão apresentados e de aceitar estes termos, para que poderem concluir o processo de subscrição.|  
|`ProductState`|número|Especifica se o produto é publicado ou não. Produtos publicados podem ser detetados pelos programadores no portal do programador. Publicado por não os produtos são visíveis apenas para administradores.<br /><br /> Os valores permitidos para o estado de produto são:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|boolean|Especifica se um utilizador pode ter várias subscrições neste produto ao mesmo tempo.|  
|`MultipleSubscriptionsCount`|número|Número máximo de subscrições para este produto um utilizador pode ter ao mesmo tempo.|  
  
##  <a name="Provider"></a> Fornecedor  
 O `provider` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Properties`|dicionário da cadeia de caracteres|Propriedades para este fornecedor de autenticação.|  
|`AuthenticationType`|string|O tipo de fornecedor. (O azure Active Directory, início de sessão do Facebook, conta do Google, Microsoft Account, Twitter).|  
|`Caption`|string|Nome a apresentar do fornecedor.|  
  
##  <a name="Representation"></a> Representação  
 Esta secção descreve uma `representation`.  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`contentType`|string|Especifica um tipo de conteúdo personalizado ou registado para este representação, por exemplo, `application/xml`.|  
|`sample`|string|Um exemplo da representação.|  
  
##  <a name="Subscription"></a> Subscrição  
 O `subscription` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|string|Identificador de recurso. Identifica de forma exclusiva a subscrição dentro da instância de serviço de gestão de API atual. O valor é um URL relativo válido no formato `subscriptions/{sid}` onde `{sid}` é um identificador de subscrição. Esta propriedade é só de leitura.|  
|`ProductId`|string|O identificador de recurso de produto do produto subscrito. O valor é um URL relativo válido no formato `products/{pid}` onde `{pid}` é um identificador de produto.|  
|`ProductTitle`|string|Nome do produto. Não pode estar vazio. Comprimento máximo é 100 carateres.|  
|`ProductDescription`|string|Descrição do produto. Não pode estar vazio. Pode incluir a formatação de etiquetas HTML. Comprimento máximo é de 1000 carateres.|  
|`ProductDetailsUrl`|string|URL relativa para os detalhes do produto.|  
|`state`|string|O estado da subscrição. Estados possíveis são:<br /><br /> - `0 - suspended` – a subscrição está bloqueada e o subscritor não é possível chamar qualquer API do produto.<br /><br /> - `1 - active` – a subscrição está ativa.<br /><br /> - `2 - expired` – a subscrição atingiu a respetiva data de expiração e foi desativada.<br /><br /> - `3 - submitted` – o pedido de subscrição foi efetuado pelo desenvolvedor, mas ainda não foi aprovado ou rejeitado.<br /><br /> - `4 - rejected` – o pedido de subscrição foi negado por um administrador.<br /><br /> - `5 - cancelled` – a subscrição foi cancelada pelo desenvolvedor ou administrador.|  
|`DisplayName`|string|Nome a apresentar da subscrição.|  
|`CreatedDate`|DateTime|A data a subscrição foi criada, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`CanBeCancelled`|boolean|Se a subscrição pode ser cancelada pelo utilizador atual.|  
|`IsAwaitingApproval`|boolean|Se a subscrição está aguardando aprovação.|  
|`StartDate`|DateTime|A data de início para a subscrição, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`ExpirationDate`|DateTime|A data de expiração da subscrição, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`NotificationDate`|DateTime|A data de notificação para a subscrição, no formato ISO 8601: `2014-06-24T16:25:00Z`.|  
|`primaryKey`|string|A chave de subscrição primária. Comprimento máximo é de 256 carateres.|  
|`secondaryKey`|string|A chave de subscrição secundário. Comprimento máximo é de 256 carateres.|  
|`CanBeRenewed`|boolean|Se a subscrição pode ser renovada do utilizador atual.|  
|`HasExpired`|boolean|Se a subscrição expirou.|  
|`IsRejected`|boolean|Se o pedido de subscrição foi negado.|  
|`CancelUrl`|string|A Url relativa para cancelar a subscrição.|  
|`RenewUrl`|string|A Url relativa para renovar a subscrição.|  
  
##  <a name="SubscriptionSummary"></a> Resumo da subscrição  
 O `subscription summary` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Id`|string|Identificador de recurso. Identifica de forma exclusiva a subscrição dentro da instância de serviço de gestão de API atual. O valor é um URL relativo válido no formato `subscriptions/{sid}` onde `{sid}` é um identificador de subscrição. Esta propriedade é só de leitura.|  
|`DisplayName`|string|O nome a apresentar da subscrição|  
  
##  <a name="UserAccountInfo"></a> Informações da conta de utilizador  
 O `user account info` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`FirstName`|string|Nome próprio. Não pode estar vazio. Comprimento máximo é 100 carateres.|  
|`LastName`|string|Apelido. Não pode estar vazio. Comprimento máximo é 100 carateres.|  
|`Email`|string|Endereço de e-mail. Não pode estar vazio e tem de ser exclusivo dentro da instância de serviço. Comprimento máximo é 254 carateres.|  
|`Password`|string|Palavra-passe da conta de utilizador.|  
|`NameIdentifier`|string|Identificador de conta, o mesmo que a mensagem de e-mail do utilizador.|  
|`ProviderName`|string|Nome do fornecedor de autenticação.|  
|`IsBasicAccount`|boolean|TRUE se esta conta foi registrada com o e-mail e palavra-passe; FALSO se a conta foi registada através de um fornecedor.|  
  
##  <a name="UseSignIn"></a> Início de sessão de utilizador  
 O `user sign in` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`Email`|string|Endereço de e-mail. Não pode estar vazio e tem de ser exclusivo dentro da instância de serviço. Comprimento máximo é 254 carateres.|  
|`Password`|string|Palavra-passe da conta de utilizador.|  
|`ReturnUrl`|string|O URL da página em que o usuário clicou iniciar sessão.|  
|`RememberMe`|boolean|Se guardar as informações do utilizador atual.|  
|`RegistrationEnabled`|boolean|Se o registo está ativado.|  
|`DelegationEnabled`|boolean|Se o início de sessão delegado está ativado.|  
|`DelegationUrl`|string|O delegado de sessão no url, se estiver ativada.|  
|`SsoSignUpUrl`|string|O início de sessão único na URL para o utilizador, se estiver presente.|  
|`AuxServiceUrl`|string|Se o usuário atual for um administrador, esta é uma ligação para a instância de serviço no portal do Azure.|  
|`Providers`|Coleção de [fornecedor](#Provider) entidades|Os fornecedores de autenticação para este utilizador.|  
|`UserRegistrationTerms`|string|Termos de um utilizador tem de aceitar antes de iniciar sessão.|  
|`UserRegistrationTermsEnabled`|boolean|Se os termos estão ativados.|  
  
##  <a name="UserSignUp"></a> Inscrição de utilizador  
 O `user sign up` entidade tem as seguintes propriedades:  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|boolean|Valor usado pelos [Inscreva-se](api-management-page-controls.md#sign-up)controle Inscreva-se.|  
|`Password`|string|Palavra-passe da conta de utilizador.|  
|`PasswordVerdictLevel`|número|Valor usado pelos [Inscreva-se](api-management-page-controls.md#sign-up)controle Inscreva-se.|  
|`UserRegistrationTerms`|string|Termos de um utilizador tem de aceitar antes de iniciar sessão.|  
|`UserRegistrationTermsOptions`|número|Valor usado pelos [Inscreva-se](api-management-page-controls.md#sign-up)controle Inscreva-se.|  
|`ConsentAccepted`|boolean|Valor usado pelos [Inscreva-se](api-management-page-controls.md#sign-up)controle Inscreva-se.|  
|`Email`|string|Endereço de e-mail. Não pode estar vazio e tem de ser exclusivo dentro da instância de serviço. Comprimento máximo é 254 carateres.|  
|`FirstName`|string|Nome próprio. Não pode estar vazio. Comprimento máximo é 100 carateres.|  
|`LastName`|string|Apelido. Não pode estar vazio. Comprimento máximo é 100 carateres.|  
|`UserData`|string|Valor usado pelos [Inscreva-se](api-management-page-controls.md#sign-up) controle.|  
|`NameIdentifier`|string|Valor usado pelos [Inscreva-se](api-management-page-controls.md#sign-up)controle Inscreva-se.|  
|`ProviderName`|string|Nome do fornecedor de autenticação.|

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de gestão de API através de modelos](api-management-developer-portal-templates.md).
