---
title: Referência da versão prévia da URL do projeto
titlesuffix: Azure Cognitive Services
description: Referência para o ponto de extremidade de visualização da URL do projeto.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: f92c0faaaa3aa0cd2af16a031f3bed4c6b41fc22
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706851"
---
# <a name="project-url-preview-v7-reference"></a>Referência do Project versão preview v7

A visualização de URL dá suporte a breves descrições de recursos da Web para postagens de blog, discussões de fórum, páginas de visualização etc. A URL pode ser qualquer tipo de recurso da Internet: Página da Web, notícias, imagem ou vídeo. A consulta deve ser uma URL absoluta com um esquema http ou https; Não há suporte para URLs relativas ou outros esquemas como ftp://.

Os aplicativos que usam a visualização de URL enviam solicitações da Web para o ponto de extremidade com uma URL a ser visualizada em um parâmetro de consulta. A solicitação deve incluir o cabeçalho *OCP-APIM-Subscription-Key* .

A resposta JSON pode ser analisada para as informações de visualização: nome, descrição do recurso, *isFamilyFriendly*e links que fornecem acesso a uma imagem representativa e ao recurso online completo.

Você deve usar apenas os dados da versão prévia da URL para exibir trechos de código de visualização e imagens em miniatura hiperlinks para seus sites de origem, no compartilhamento de URL iniciado pelo usuário final em mídia social, no bot de chat ou em ofertas semelhantes. Você não deve copiar, armazenar ou colocar em cache todos os dados recebidos da versão prévia da URL do projeto. Você deve honrar as solicitações para desabilitar as visualizações que você pode receber do site ou dos proprietários do conteúdo.

## <a name="endpoint"></a>Ponto Final
Para solicitar resultados de visualização de URL, envie uma solicitação para o ponto de extremidade a seguir. Use os cabeçalhos e os parâmetros de URL para definir outras especificações.

GET do ponto de extremidade:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

```

A solicitação deve usar o protocolo HTTPS e incluir o seguinte parâmetro de consulta:

q-a consulta que identifica a URL a ser visualizada

As seções a seguir fornecem detalhes técnicos sobre os objetos de resposta, os parâmetros de consulta e os cabeçalhos que afetam os resultados da pesquisa.

Para obter informações sobre os cabeçalhos que as solicitações devem incluir, consulte [cabeçalhos](#headers).

Para obter informações sobre parâmetros de consulta que as solicitações devem incluir, consulte [parâmetros de consulta](#query-parameters).

Para obter informações sobre os objetos JSON que a resposta inclui, consulte [objetos de resposta](#response-objects).

O comprimento máximo da URL de consulta é de 2.048 caracteres. Para garantir que o comprimento da URL não exceda o limite, o comprimento máximo dos parâmetros de consulta deve ter menos de 1.500 caracteres. Se a URL exceder 2.048 caracteres, o servidor retornará 404 não encontrado.

Para obter informações sobre o uso permitido e a exibição de resultados, consulte [requisitos de uso e exibição](use-display-requirements.md).

> [!NOTE]
> Alguns cabeçalhos de solicitação que são significativos para outras APIs de pesquisa não afetam a visualização da URL
> - Pragma – o chamador não tem controle sobre se a visualização da URL usa o cache
> - Agente de usuário – por enquanto, a API de visualização de URL não fornece respostas diferentes para chamadas provenientes de PC, laptop ou móvel.
> 
> Além disso, alguns parâmetros não são significativos no momento para a API de visualização de URL, mas podem ser usados no futuro para melhorar a globalização.

## <a name="headers"></a>Cabeçalhos
A seguir estão os cabeçalhos que uma solicitação e uma resposta podem incluir.

|Cabeçalho|Descrição|
|------------|-----------------|
|<a name="market" />BingAPIs-Market|O cabeçalho da resposta.<br /><br /> O mercado que o pedido utiliza. O formato é \<languageCode\>-\<códigodeidioma\>. Por exemplo: en-US.|
|<a name="traceid" />BingAPIs-TraceId|O cabeçalho da resposta.<br /><br /> O ID da entrada de registo que contém os detalhes do pedido. Quando ocorrer um erro, capture este ID. Se não conseguir determinar nem resolver o problema, inclua o ID juntamente com as outras informações que enviar à equipa de suporte.|
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Cabeçalho de pedido obrigatório.<br /><br /> A chave de subscrição que recebeu quando se inscreveu neste serviço nos [Serviços Cognitivos](https://www.microsoft.com/cognitive-services/).|
|<a name="clientid" />X-MSEdge-ClientID|Cabeçalho de pedido e resposta opcional.<br /><br /> O Bing utiliza este cabeçalho para proporcionar um comportamento consistente nas chamada à API do Bing aos utilizadores. O Bing lança, muitas vezes, funcionalidades e melhorias novas e utiliza o ID de cliente como uma chave para atribuir tráfego aos diferentes lançamentos. Se não utilizar o mesmo ID de cliente para um utilizador em vários pedidos, o Bing poderá atribuir esse utilizador a vários lançamentos sobrepostos. A atribuição a vários lançamentos sobrepostos pode provocar uma experiência de utilizador inconsistente. Por exemplo, se a atribuição de lançamento do segundo pedido for diferente da do primeiro, a experiência poderá ser inesperada. Além disso, o Bing pode utilizar o ID de cliente para personalizar os resultados da Web o histórico de pesquisas desse ID, proporcionando uma experiência mais rica ao utilizador.<br /><br /> O Bing também utiliza este cabeçalho para ajudar a melhorar as classificações dos resultados ao analisar a atividade gerada por um ID de cliente. As melhorias à relevância ajudam a que as APIs do Bing devolvam resultados com melhor qualidade, o que, por sua vez, origina taxas de cliques mais altas para o consumidor da API.<br /><br />As regras abaixo são as regras de utilização básicas aplicáveis a este cabeçalho.<br /><ul><li>Cada utilizador que utilize a sua aplicação no dispositivo tem de ter um ID de cliente exclusivo e gerado pelo Bing.<br /><br/>Se não incluir este cabeçalho no pedido, o Bing gera um ID e devolve-o no cabeçalho da resposta X-MSEdge-ClientID. O único momento em que NÃO deve incluir este cabeçalho num pedido é na primeira vez que o utilizador utiliza a sua aplicação nesse dispositivo.<br /><br/></li><li>Utilize o ID de cliente para cada pedido da API do Bing que a sua aplicação fizer para este utilizador no dispositivo.<br /><br/></li><li>**SEGURA** Você deve garantir que essa ID do cliente não seja vinculável a nenhuma informação da conta de usuário do authenticatable.</li><br/><li>Persista o ID de cliente. Para persistir o ID numa aplicação de browser, utilize um cookie HTTP persistente para garantir que esse ID é utilizado em todas as sessões. Não utilize um cookie de sessão. Relativamente a outras aplicações, como aplicações móveis, utilize o armazenamento persistente do dispositivo para persistir o ID.<br /><br/>Da próxima vez que o utilizador utilizar a aplicação nesse dispositivo, recebe o ID de cliente que foi persistido.</li></ul><br /> **NOTA:** As respostas do Bing podem ou não incluir esse cabeçalho. Se a resposta incluir este cabeçalho, capture o ID de cliente e utilize-o em todos os pedidos do Bing subsequentes do utilizador nesse dispositivo.<br /><br /> **NOTA:** Se você incluir o X-MSEdge-ClientID, não deverá incluir cookies na solicitação.|
|<a name="clientip" />X-MSEdge-ClientIP|Cabeçalho de pedido opcional.<br /><br /> O endereço IPv4 ou IPv6 do dispositivo cliente. O endereço IP é utilizado para detetar a localização do utilizador. O Bing utiliza as informações de localização para determinar o comportamento do safesearch.<br /><br /> Não ofusque a morada (por exemplo, ao alterar o último octeto para 0). Ofuscar a morada faz com que a localização não esteja perto da localização efetiva do dispositivo, o que pode fazer com que o Bing sirva resultados errados.|

## <a name="query-parameters"></a>Parâmetros de consulta
A solicitação pode incluir os seguintes parâmetros de consulta. Consulte a coluna necessária para os parâmetros necessários. Você deve codificar a URL dos parâmetros de consulta. A consulta deve ser uma URL absoluta com um esquema http ou https; Não há suporte para URLs relativas ou outros esquemas como ftp://

|Nome|Value|Type|Requerido|
|----------|-----------|----------|--------------|
|<a name="mkt" />mkt|O mercado de onde os resultados provêm. <br /><br />Para obter uma lista de possíveis valores de mercado, consulte códigos de mercado.<br /><br /> **NOTA:** A API de visualização de URL atualmente dá suporte apenas à geografia e idioma inglês dos EUA.<br /><br />|Cadeia|Sim|
|<a name="query" />q|A URL a ser visualizada|Cadeia|Sim|
|<a name="responseformat" />responseFormat|O tipo de mídia a ser usado para a resposta. A seguir estão os valores possíveis que não diferenciam maiúsculas de minúsculas.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> O padrão é JSON. Para obter informações sobre os objetos JSON que a resposta contém, consulte [objetos de resposta](#response-objects).<br /><br />Se você especificar JsonLd, o corpo da resposta incluirá objetos JSON-LD que contêm os resultados da pesquisa. Para obter informações sobre o JSON-LD, consulte [JSON-LD](https://json-ld.org/).|Cadeia|Não|
|<a name="safesearch"/>safeSearch|Conteúdo adulto ilegal, ou conteúdo pirateado, está bloqueado com o código de erro 400 e o sinalizador *isFamilyFriendly* não é retornado. <p>Para conteúdo de adulto legal, abaixo está o comportamento. O código de status retorna 200 e o sinalizador *isFamilyFriendly* é definido como false.<ul><li>safeSearch=strict: O título, a descrição, a URL e a imagem não serão retornados.</li><li>Adulto = moderada; Obtenha o título, a URL e a descrição, mas não a imagem descritiva.</li><li>Adulto = desativado; Obtenha todos os objetos/elementos de resposta – título, URL, descrição e imagem.</li></ul> |Cadeia|Não é necessário. </br> O padrão é o assegurado = estrito.|

## <a name="response-objects"></a>Objetos de resposta
O esquema de resposta é um [webpage] ou ErrorResponse, como na API Pesquisa na Web. Se a solicitação falhar, o objeto de nível superior será o objeto [ErrorResponse](#errorresponse) .

|Objeto|Descrição|
|------------|-----------------|
|[Página da Web](#webpage)|Objeto JSON de nível superior que contém atributos de visualização.|

### <a name="error"></a>Erro
Define o erro que ocorreu.

|Elemento|Descrição|Type|
|-------------|-----------------|----------|
|<a name="error-code" />auto-completar|O código de erro que identifica a categoria de erro. Para obter uma lista de códigos possíveis, consulte [códigos de erro](#error-codes).|Cadeia|
|<a name="error-message" />Mensagem|Uma descrição do erro.|Cadeia|
|<a name="error-moredetails" />moreDetails|Uma descrição que fornece informações adicionais sobre o erro.|Cadeia|
|<a name="error-parameter" />meter|O parâmetro de consulta na solicitação que causou o erro.|Cadeia|
|<a name="error-subcode" />Subcódigo|O código de erro que identifica o erro. Por exemplo, se `code` for InvalidRequest, `subCode` poderá ser ParameterInvalid ou ParameterInvalidValue. |Cadeia|
|<a name="error-value" />valor|O valor do parâmetro de consulta que não era válido.|Cadeia|

### <a name="errorresponse"></a>ErrorResponse
O objeto de nível superior que a resposta inclui quando a solicitação falha.

|Nome|Value|Type|
|----------|-----------|----------|
|_type|Dica de tipo.|Cadeia|
|<a name="errors" />los|Uma lista de erros que descrevem os motivos pelos quais a solicitação falhou.|[Erro](#error) do []|

### <a name="webpage"></a>Página da Web
Define informações sobre uma página da Web em visualização.

|Nome|Value|Type|
|----------|-----------|----------|
|name|O título da página, não necessariamente o título HTML|Cadeia|
|url|A URL que foi realmente rastreada (a solicitação pode ter seguido redirecionamentos)|Cadeia|
|description|Breve descrição da página e do conteúdo|Cadeia|
|isFamilyFriendly|Mais preciso para itens no índice da Web; buscas em tempo real fazem essa detecção com base apenas na URL e não no conteúdo da página|boolean|
|primaryImageOfPage/contentUrl|A URL para uma imagem representativa a ser incluída na visualização|Cadeia|

### <a name="identifiable"></a>Identificação
|Nome|Valor|Type|
|-------------|-----------------|----------|
|id|Um identificador de recurso|Cadeia|

## <a name="error-codes"></a>Códigos de erro

A seguir estão os códigos de status HTTP possíveis que uma solicitação retorna.

|Código de Estado|Descrição|
|-----------------|-----------------|
|200|Êxito.|
|400|Um dos parâmetros de consulta está ausente ou não é válido.|
|400|ServerError, subcódigo ResourceError: A URL solicitada não pôde ser acessada|
|400|ServerError, subcódigo ResourceError: A URL solicitada não retornou um código de êxito (incluindo se ele retornou HTTP 404)|
|400|InvalidRequest, subcódigo bloqueado: A URL solicitada pode conter conteúdo adulto e foi bloqueada|
|401|A chave de assinatura está ausente ou não é válida.|
|403|O usuário é autenticado (por exemplo, ele usou uma chave de assinatura válida), mas não tem permissão para o recurso solicitado.<br /><br /> O Bing também poderá retornar esse status se o chamador exceder a cota de consultas por mês.|
|410|A solicitação usou HTTP em vez do protocolo HTTPS. HTTPS é o único protocolo com suporte.|
|429|O chamador excedeu a cota de consultas por segundo.|
|500|Erro de servidor inesperado.|

Se a solicitação falhar, a resposta conterá um objeto [ErrorResponse](#errorresponse) , que contém uma lista de objetos de [erro](#error) que descrevem o que causou o erro. Se o erro estiver relacionado a um parâmetro, o `parameter` campo identificará o parâmetro que é o problema. E se o erro estiver relacionado a um valor de parâmetro, `value` o campo identificará o valor que não é válido.

```json
{
  "_type": "ErrorResponse",
  "errors": [
    {
      "code": "InvalidRequest",
      "subCode": "ParameterMissing",
      "message": "Required parameter is missing.",
      "parameter": "q"
    }
  ]
}

{
  "_type": "ErrorResponse",
  "errors": [
    {
      "code": "InvalidAuthorization",
      "subCode": "AuthorizationMissing",
      "message": "Authorization is required.",
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

A seguir estão os valores de código de erro e de suberros possíveis.

|Código|Subcódigo|Descrição
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|O código de status HTTP é 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqueado|O Bing retorna InvalidRequest sempre que qualquer parte da solicitação não é válida. Por exemplo, um parâmetro obrigatório está ausente ou um valor de parâmetro não é válido.<br/><br/>Se o erro for ParameterMissing ou ParameterInvalidValue, o código de status HTTP será 400.<br/><br/>Se você usar o protocolo HTTP em vez de HTTPS, o Bing retornará HttpNotAllowed e o código de status HTTP será 410.
|RateLimitExceeded|Nenhum subcódigo|O Bing retorna RateLimitExceeded sempre que você excede a cota de consultas por segundo (QPS) ou consultas por mês (QPM).<br/><br/>Se você exceder QPS, o Bing retornará o código de status HTTP 429 e, se você exceder QPM, o Bing retornará 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|O Bing retorna InvalidAuthorization quando o Bing não pode autenticar o chamador. Por exemplo, o `Ocp-Apim-Subscription-Key` cabeçalho está ausente ou a chave de assinatura não é válida.<br/><br/>A redundância ocorrerá se você especificar mais de um método de autenticação.<br/><br/>Se o erro for InvalidAuthorization, o código de status HTTP será 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|O Bing retorna InsufficientAuthorization quando o chamador não tem permissões para acessar o recurso. Isso pode ocorrer se a chave de assinatura tiver sido desabilitada ou tiver expirado. <br/><br/>Se o erro for InsufficientAuthorization, o código de status HTTP será 403.

## <a name="next-steps"></a>Passos Seguintes
- [Início rápido do C#](csharp.md)
- [Início rápido do Java](java-quickstart.md)
- [Início Rápido do JavaScript](javascript.md)
- [Início Rápido do Node](node-quickstart.md)
- [Início Rápido do Python](python-quickstart.md)
