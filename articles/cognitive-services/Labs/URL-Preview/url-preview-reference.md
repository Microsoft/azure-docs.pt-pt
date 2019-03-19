---
title: Referência de pré-visualização do URL do projeto
titlesuffix: Azure Cognitive Services
description: Referência para o ponto final de pré-visualização de URL do projeto.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh
ms.openlocfilehash: 69db722295c9c81d45913bd078fe9cc5ab74c512
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104714"
---
# <a name="project-url-preview-v7-reference"></a>Referência de v7 pré-visualização do URL do projeto

Pré-visualização do URL suporta breves descrições de recursos da Web para mensagens de blogue, discussões em fóruns, páginas de pré-visualização, etc. O URL pode ser qualquer tipo de recurso de Internet: Página da Web, notícias, imagem ou vídeo. A consulta tem de ser um URL absoluto com um esquema de http ou https; URLs relativas ou outros esquemas, como ftp: / / não são suportadas.

Aplicativos que usam a pré-visualização do URL enviam pedidos Web para o ponto final com um URL para um parâmetro de consulta de pré-visualização. O pedido tem de incluir o *Ocp-Apim-Subscription-Key* cabeçalho.

A resposta JSON pode ser analisada para as informações de pré-visualização: nome, descrição do recurso, *isFamilyFriendly*e ligações que fornecem acesso a uma imagem representativa e para o recurso completo online.

Tem de utilizar apenas os dados de pré-visualização do URL para apresentar os fragmentos de pré-visualização e imagens em miniatura associado à hiperligação aos respetivos sites de origem, no URL iniciada pelo utilizador final de partilha nas redes sociais, chatbot ou ofertas semelhante. Não deve copiar, armazenar ou colocar em cache todos os dados recebidos da pré-visualização de URL do projeto. Tem de respeitar quaisquer pedidos para desativar pré-visualizações que possa receber a partir do Web site ou os proprietários de conteúdo.

## <a name="endpoint"></a>Ponto Final
Para resultados de pré-visualização do URL do pedido, envie um pedido para o seguinte ponto de extremidade. Utilize os cabeçalhos e os parâmetros de URL para definir ainda mais especificações.

Ponto final GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

```

O pedido tem de utilizar o protocolo HTTPS e incluir o parâmetro de consulta a seguir:

p - a consulta que identifica o URL para a pré-visualização

As secções seguintes fornecem detalhes técnicos sobre os objetos de resposta, parâmetros de consulta e cabeçalhos que afetam os resultados da pesquisa.

Para obter informações sobre cabeçalhos de pedidos devem incluir, consulte [cabeçalhos](#headers).

Para obter informações sobre parâmetros de consulta que devem incluir pedidos, consulte [parâmetros de consulta](#query-parameters).

Para obter informações sobre o JSON de objetos que inclui a resposta, consulte [objetos de resposta](#response-objects).

O comprimento do URL de consulta máxima é de 2048 carateres. Para garantir que o comprimento do URL não excede o limite, o comprimento máximo de seus parâmetros de consulta deve ser inferior a 1 500 carateres. Se o URL exceder 2048 carateres, o servidor devolve 404 não encontrado.

Para obter informações sobre o uso permitido e a exibição de resultados, consulte [utilizar e apresentam os requisitos](use-display-requirements.md).

> [!NOTE]
> Alguns cabeçalhos de pedido com significado para outras APIs de pesquisa não afetam a pré-visualização do URL
> - Pragma – o chamador não tem controlo sobre se o URL de visualização utiliza a cache
> - Agente do utilizador – por enquanto, Url da API de pré-visualização não fornece respostas diferentes para chamadas procedentes da PC, Laptop ou móveis.
> 
> Além disso, alguns parâmetros não são atualmente significativos para a API de pré-visualização do URL, mas podem ser utilizados no futuro para a globalização melhorada.

## <a name="headers"></a>Cabeçalhos
Seguem-se os cabeçalhos que podem incluir uma solicitação e resposta.

|Cabeçalho|Descrição|
|------------|-----------------|
|<a name="market" />BingAPIs-Market|O cabeçalho da resposta.<br /><br /> O mercado que o pedido utiliza. O formato é \<languageCode\>-\<códigodeidioma\>. Por exemplo: en-US.|
|<a name="traceid" />BingAPIs-TraceId|O cabeçalho da resposta.<br /><br /> O ID da entrada de registo que contém os detalhes do pedido. Quando ocorrer um erro, capture este ID. Se não conseguir determinar nem resolver o problema, inclua o ID juntamente com as outras informações que enviar à equipa de suporte.|
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Cabeçalho de pedido obrigatório.<br /><br /> A chave de subscrição que recebeu quando se inscreveu neste serviço nos [Serviços Cognitivos](https://www.microsoft.com/cognitive-services/).|
|<a name="clientid" />X-MSEdge-ClientID|Cabeçalho de pedido e resposta opcional.<br /><br /> O Bing utiliza este cabeçalho para proporcionar um comportamento consistente nas chamada à API do Bing aos utilizadores. O Bing lança, muitas vezes, funcionalidades e melhorias novas e utiliza o ID de cliente como uma chave para atribuir tráfego aos diferentes lançamentos. Se não utilizar o mesmo ID de cliente para um utilizador em vários pedidos, o Bing poderá atribuir esse utilizador a vários lançamentos sobrepostos. A atribuição a vários lançamentos sobrepostos pode provocar uma experiência de utilizador inconsistente. Por exemplo, se a atribuição de lançamento do segundo pedido for diferente da do primeiro, a experiência poderá ser inesperada. Além disso, o Bing pode utilizar o ID de cliente para personalizar os resultados da Web o histórico de pesquisas desse ID, proporcionando uma experiência mais rica ao utilizador.<br /><br /> O Bing também utiliza este cabeçalho para ajudar a melhorar as classificações dos resultados ao analisar a atividade gerada por um ID de cliente. As melhorias à relevância ajudam a que as APIs do Bing devolvam resultados com melhor qualidade, o que, por sua vez, origina taxas de cliques mais altas para o consumidor da API.<br /><br />As regras abaixo são as regras de utilização básicas aplicáveis a este cabeçalho.<br /><ul><li>Cada utilizador que utilize a sua aplicação no dispositivo tem de ter um ID de cliente exclusivo e gerado pelo Bing.<br /><br/>Se não incluir este cabeçalho no pedido, o Bing gera um ID e devolve-o no cabeçalho da resposta X-MSEdge-ClientID. O único momento em que NÃO deve incluir este cabeçalho num pedido é na primeira vez que o utilizador utiliza a sua aplicação nesse dispositivo.<br /><br/></li><li>Utilize o ID de cliente para cada pedido da API do Bing que a sua aplicação fizer para este utilizador no dispositivo.<br /><br/></li><li>**ATENÇÃO:** Tem de se certificar de que este ID de cliente não é vinculação para quaisquer informações de conta de utilizador authenticatable.</li><br/><li>Persista o ID de cliente. Para persistir o ID numa aplicação de browser, utilize um cookie HTTP persistente para garantir que esse ID é utilizado em todas as sessões. Não utilize um cookie de sessão. Relativamente a outras aplicações, como aplicações móveis, utilize o armazenamento persistente do dispositivo para persistir o ID.<br /><br/>Da próxima vez que o utilizador utilizar a aplicação nesse dispositivo, recebe o ID de cliente que foi persistido.</li></ul><br /> **NOTA:** Respostas do Bing podem ou não podem incluir esse cabeçalho. Se a resposta incluir este cabeçalho, capture o ID de cliente e utilize-o em todos os pedidos do Bing subsequentes do utilizador nesse dispositivo.<br /><br /> **NOTA:** Se incluir o ClientID de MSEdge X, não pode incluir cookies no pedido.|
|<a name="clientip" />X-MSEdge-ClientIP|Cabeçalho de pedido opcional.<br /><br /> O endereço IPv4 ou IPv6 do dispositivo cliente. O endereço IP é utilizado para detetar a localização do utilizador. O Bing utiliza as informações de localização para determinar o comportamento do safesearch.<br /><br /> Não ofusque a morada (por exemplo, ao alterar o último octeto para 0). Ofuscar a morada faz com que a localização não esteja perto da localização efetiva do dispositivo, o que pode fazer com que o Bing sirva resultados errados.|

## <a name="query-parameters"></a>Parâmetros de consulta
O pedido pode incluir os seguintes parâmetros de consulta. Consulte a coluna necessária para parâmetros obrigatórios. Tem o URL de codificar os parâmetros de consulta. A consulta tem de ser um URL absoluto com um esquema de http ou https; Não é suportada URLs relativas ou outros esquemas, como ftp: / /

|Name|Value|Type|Necessário|
|----------|-----------|----------|--------------|
|<a name="mkt" />mkt|O mercado de onde os resultados provêm. <br /><br />Para obter uma lista de possíveis valores de mercado, consulte códigos de mercado.<br /><br /> **NOTA:** A API de pré-visualização do URL atualmente suporta apenas geografia dos EUA e do idioma inglês.<br /><br />|String|Sim|
|<a name="query" />q|O URL para a pré-visualização|String|Sim|
|<a name="responseformat" />responseFormat|O tipo de mídia a utilizar para a resposta. Seguem-se os valores possíveis de maiúsculas e minúsculas.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> A predefinição é JSON. Para obter informações sobre o JSON de objetos que contém a resposta, consulte [objetos de resposta](#response-objects).<br /><br />Se especificar JsonLd, o corpo da resposta inclui os objetos de JSON LD que contêm os resultados da pesquisa. Para obter informações sobre o JSON-LD, consulte [JSON LD](https://json-ld.org/).|String|Não|
|<a name="safesearch"/>safeSearch|Conteúdo para adultos ilegal ou pirateado conteúdo, é bloqueado com o código de erro 400 e o *isFamilyFriendly* sinalizador não for devolvido. <p>Para o conteúdo para adultos legal, segue-se o comportamento. Código de estado devolve 200 e o *isFamilyFriendly* sinalizador estiver definido como false.<ul><li>safeSearch=strict: Título, descrição, URL e imagem não serão devolvidos.</li><li>pesquisa segura = moderado; Obtenha title, URL e descrição mas não a imagem descritiva.</li><li>pesquisa segura = desativar; Obter todos os resposta objetos/elementos – title, URL, descrição e imagem.</li></ul> |String|Não é necessário. </br> O padrão é safeSearch = rigorosa.|

## <a name="response-objects"></a>Objetos de resposta
O esquema de resposta é qualquer um de uma [página Web] ou byl vrácen Prvek, tal como a API de pesquisa na Web. Se o pedido falhar, o objeto de nível superior é o [byl vrácen Prvek](#errorresponse) objeto.

|Object|Descrição|
|------------|-----------------|
|[WebPage](#webpage)|Principal ao nível objeto JSON que contém os atributos de pré-visualização.|

### <a name="error"></a>Erro
Define o erro ocorrido.

|Elemento|Descrição|Type|
|-------------|-----------------|----------|
|<a name="error-code" />Código|O código de erro que identifica a categoria de erro. Para obter uma lista de códigos possíveis, consulte [códigos de erro](#error-codes).|String|
|<a name="error-message" />mensagem|Uma descrição do erro.|String|
|<a name="error-moredetails" />moreDetails|Uma descrição que fornece informações adicionais sobre o erro.|String|
|<a name="error-parameter" />parameter|O parâmetro de consulta no pedido que causou o erro.|String|
|<a name="error-subcode" />subCode|O código de erro que identifica o erro. Por exemplo, se `code` é InvalidRequest, `subCode` pode ser ParameterInvalid ou ParameterInvalidValue. |String|
|<a name="error-value" />Valor|Valor do parâmetro de consulta que não era válido.|String|

### <a name="errorresponse"></a>ErrorResponse
O objeto de nível superior que a resposta inclui quando o pedido falhar.

|Name|Value|Type|
|----------|-----------|----------|
|_type|Dica de tipo.|String|
|<a name="errors" />Erros|Uma lista de erros que descrevem os motivos por que o pedido falhou.|[Error](#error)[]|

### <a name="webpage"></a>Página Web
Define as informações sobre uma página da Web em pré-visualização.

|Name|Value|Type|
|----------|-----------|----------|
|nome|O título da página, não necessariamente o título do HTML|String|
|url|O URL que, na verdade, foi pesquisado (pedido pode seguiu redirecionamentos)|String|
|descrição|Breve descrição da página e conteúdo|String|
|isFamilyFriendly|Mais precisos para itens no índice web; buscas em tempo real fazer esta deteção com base apenas no URL e não o conteúdo da página|boolean|
|primaryImageOfPage/contentUrl|O URL para uma imagem representativo para incluir na pré-visualização|String|

### <a name="identifiable"></a>Identificação
|Name|Value|Type|
|-------------|-----------------|----------|
|ID|Um identificador de recurso|String|

## <a name="error-codes"></a>Códigos de erro

Seguem-se os possíveis códigos de estado HTTP que retorna um pedido.

|Código de Estado|Descrição|
|-----------------|-----------------|
|200|Êxito.|
|400|Um dos parâmetros de consulta está em falta ou não é válido.|
|400|ServerError, subCode ResourceError: Não foi possível contactar o URL pedido|
|400|ServerError, subCode ResourceError: O URL pedido não devolveu um código de êxito (incluindo se ele retornasse HTTP 404)|
|400|InvalidRequest, subCode bloqueado: O URL pedido pode conter o conteúdo para adultos e foi bloqueado|
|401|A chave de subscrição está em falta ou não é válida.|
|403|O utilizador é autenticado (por exemplo, eles utilizaram uma chave de subscrição válido), mas que não tem permissão para o recurso solicitado.<br /><br /> Bing também pode devolver este estado se o chamador excedido suas consultas por quota do mês.|
|410|O pedido de utilizado HTTP em vez do protocolo HTTPS. O HTTPS é o único protocolo suportado.|
|429|O autor da chamada excedeu as consultas por segundo quota.|
|500|Erro de servidor inesperada.|

Se o pedido falhar, a resposta contém um [byl vrácen Prvek](#errorresponse) objeto, que contém uma lista de [erro](#error) objetos que descrevem o que causou de erro. Se o erro está relacionado com um parâmetro, o `parameter` campo identifica o parâmetro que é o problema. E se o erro está relacionado com um valor de parâmetro, o `value` campo identifica o valor que não é válido.

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

Seguem-se os valores de código de erro secundárias e de código de erro possíveis.

|Código|SubCode|Descrição
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NãoImplementado|Código de estado HTTP é 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqueado|O Bing devolve InvalidRequest sempre que qualquer parte do pedido não é válido. Por exemplo, um parâmetro necessário está em falta ou um valor de parâmetro não é válido.<br/><br/>Se o erro é ParameterMissing ou ParameterInvalidValue, o código de estado HTTP é 400.<br/><br/>Se utilizar o protocolo HTTP em vez de HTTPS, o Bing devolve HttpNotAllowed e o código de estado HTTP é 410.
|RateLimitExceeded|Sem códigos secundárias|O Bing devolve RateLimitExceeded sempre que excedem suas consultas por segundo (QPS) ou consultas por quota do mês (QPM).<br/><br/>Se ultrapassar QPS, o Bing devolve o código de estado HTTP 429 e, se exceder QPM, o Bing devolve 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|O Bing devolve InvalidAuthorization ao Bing não é possível autenticar o chamador. Por exemplo, o `Ocp-Apim-Subscription-Key` cabeçalho está em falta ou a chave de subscrição não é válida.<br/><br/>Redundância ocorre se especificar mais do que um método de autenticação.<br/><br/>Se o erro for InvalidAuthorization, o código de estado HTTP é 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|O Bing devolve InsufficientAuthorization quando o chamador não tem permissões para aceder ao recurso. Isto pode ocorrer se a chave de subscrição foi desativada ou expirou. <br/><br/>Se o erro for InsufficientAuthorization, o código de estado HTTP é 403.

## <a name="next-steps"></a>Passos Seguintes
- [Início rápido do C#](csharp.md)
- [Início rápido do Java](java-quickstart.md)
- [Início Rápido do JavaScript](javascript.md)
- [Início Rápido do Node](node-quickstart.md)
- [Início Rápido do Python](python-quickstart.md)
