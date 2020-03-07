---
title: Referência de pré-visualização do URL do projeto
titlesuffix: Azure Cognitive Services
description: Referência para ponto final de pré-visualização do URL do projeto.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393950"
---
# <a name="project-url-preview-v7-reference"></a>Referência de pré-visualização v7 do URL do projeto

Url Preview suporta breves descrições de recursos Web para posts de blog, discussões no fórum, páginas de pré-visualização, etc. O URL pode ser qualquer tipo de recurso de Internet: página web, notícias, imagem ou vídeo. A consulta deve ser um URL absoluto com um esquema http ou https; URLs relativos ou outros regimes como ftp:// não são suportados.

Aplicações que utilizam URL Preview enviem pedidos web para o ponto final com um URL para pré-visualizar num parâmetro de consulta. O pedido deve incluir o cabeçalho *Ocp-Apim-Subscription-Key.*

A resposta JSON pode ser analisada para as informações de pré-visualização: nome, descrição do recurso, *éFamilyFriendly*, e links que fornecem acesso a uma imagem representativa e ao recurso completo online.

Deve utilizar apenas os dados do URL Preview para visualizar snippets de pré-visualização e imagens de miniaturas hiperligadas aos seus sites de origem, em partilha de URL iniciada pelo utilizador nas redes sociais, chat bot ou ofertas semelhantes. Não deve copiar, armazenar ou cache quaisquer dados que receba da Pré-visualização do URL do Projeto. Deve honrar quaisquer pedidos para desativar pré-visualizações que possa receber do site ou dos proprietários de conteúdos.

## <a name="endpoint"></a>Ponto Final
Para solicitar resultados de pré-visualização de URL, envie um pedido para o seguinte ponto final. Utilize os cabeçalhos e os parâmetros url para definir especificações adicionais.

Ponto final GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=queryURL

```

O pedido deve utilizar o protocolo HTTPS e incluir o seguinte parâmetro de consulta:

q - A consulta que identifica o URL para pré-visualizar

As seguintes secções fornecem detalhes técnicos sobre os objetos de resposta, parâmetros de consulta e cabeçalhos que afetam os resultados da pesquisa.

Para obter informações sobre cabeçalhos que os pedidos devem incluir, consulte [Cabeçalhos](#headers).

Para obter informações sobre parâmetros de consulta que os pedidos devem incluir, consulte [os parâmetros de consulta](#query-parameters).

Para obter informações sobre os objetos JSON que a resposta inclui, consulte [objetos de resposta](#response-objects).

O comprimento máximo de URL de consulta é de 2.048 caracteres. Para garantir que o comprimento do URL não exceda o limite, o comprimento máximo dos seus parâmetros de consulta deve ser inferior a 1.500 caracteres. Se o URL exceder 2.048 caracteres, o servidor retorna 404 Não encontrados.

Para obter informações sobre a utilização e exibição permitidas de resultados, consulte os requisitos de [utilização e visualização](use-display-requirements.md).

> [!NOTE]
> Alguns cabeçalhos de pedido que são significativos para outras APIs de pesquisa não afetam a pré-visualização de URL
> - Pragma – o chamador não tem controlo sobre se a pré-visualização de URL utiliza cache
> - User-Agent – Por enquanto, a API de pré-visualização do Url não fornece respostas diferentes para chamadas provenientes de PC, Laptop ou Mobile.
> 
> Além disso, alguns parâmetros não são atualmente significativos para a API de pré-visualização de URL, mas podem ser usados no futuro para uma melhor globalização.

## <a name="headers"></a>Cabeçalhos
Seguem-se os cabeçalhos que um pedido e resposta podem incluir.

|Cabeçalho|Descrição|
|------------|-----------------|
|<a name="market" />BingAPIs-Market|O cabeçalho da resposta.<br /><br /> O mercado que o pedido utiliza. O formato é \<languageCode\>-\<códigodeidioma\>. Por exemplo: en-US.|
|<a name="traceid" />BingAPIs-TraceId|O cabeçalho da resposta.<br /><br /> O ID da entrada de registo que contém os detalhes do pedido. Quando ocorrer um erro, capture este ID. Se não conseguir determinar nem resolver o problema, inclua o ID juntamente com as outras informações que enviar à equipa de suporte.|
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Cabeçalho de pedido obrigatório.<br /><br /> A chave de subscrição que recebeu quando se inscreveu neste serviço nos [Serviços Cognitivos](https://www.microsoft.com/cognitive-services/).|
|<a name="clientid" />X-MSEdge-ClientID|Cabeçalho de pedido e resposta opcional.<br /><br /> O Bing utiliza este cabeçalho para proporcionar um comportamento consistente nas chamada à API do Bing aos utilizadores. O Bing lança, muitas vezes, funcionalidades e melhorias novas e utiliza o ID de cliente como uma chave para atribuir tráfego aos diferentes lançamentos. Se não utilizar o mesmo ID de cliente para um utilizador em vários pedidos, o Bing poderá atribuir esse utilizador a vários lançamentos sobrepostos. A atribuição a vários lançamentos sobrepostos pode provocar uma experiência de utilizador inconsistente. Por exemplo, se a atribuição de lançamento do segundo pedido for diferente da do primeiro, a experiência poderá ser inesperada. Além disso, o Bing pode utilizar o ID de cliente para personalizar os resultados da Web o histórico de pesquisas desse ID, proporcionando uma experiência mais rica ao utilizador.<br /><br /> O Bing também utiliza este cabeçalho para ajudar a melhorar as classificações dos resultados ao analisar a atividade gerada por um ID de cliente. As melhorias à relevância ajudam a que as APIs do Bing devolvam resultados com melhor qualidade, o que, por sua vez, origina taxas de cliques mais altas para o consumidor da API.<br /><br />As regras abaixo são as regras de utilização básicas aplicáveis a este cabeçalho.<br /><ul><li>Cada utilizador que utilize a sua aplicação no dispositivo tem de ter um ID de cliente exclusivo e gerado pelo Bing.<br /><br/>Se não incluir este cabeçalho no pedido, o Bing gera um ID e devolve-o no cabeçalho da resposta X-MSEdge-ClientID. O único momento em que NÃO deve incluir este cabeçalho num pedido é na primeira vez que o utilizador utiliza a sua aplicação nesse dispositivo.<br /><br/></li><li>Utilize o ID de cliente para cada pedido da API do Bing que a sua aplicação fizer para este utilizador no dispositivo.<br /><br/></li><li>**ATENÇÃO:** Deve certificar-se de que este ID do Cliente não é vinculado a qualquer informação autenticada da conta de utilizador.</li><br/><li>Persista o ID de cliente. Para persistir o ID numa aplicação de browser, utilize um cookie HTTP persistente para garantir que esse ID é utilizado em todas as sessões. Não utilize um cookie de sessão. Relativamente a outras aplicações, como aplicações móveis, utilize o armazenamento persistente do dispositivo para persistir o ID.<br /><br/>Da próxima vez que o utilizador utilizar a aplicação nesse dispositivo, recebe o ID de cliente que foi persistido.</li></ul><br /> **NOTA:** as respostas do Bing podem incluir ou não este cabeçalho. Se a resposta incluir este cabeçalho, capture o ID de cliente e utilize-o em todos os pedidos do Bing subsequentes do utilizador nesse dispositivo.<br /><br /> **NOTA:** se incluir X-MSEdge-ClientID, não pode incluir cookies no pedido.|
|<a name="clientip" />X-MSEdge-ClientIP|Cabeçalho de pedido opcional.<br /><br /> O endereço IPv4 ou IPv6 do dispositivo cliente. O endereço IP é utilizado para detetar a localização do utilizador. O Bing utiliza as informações de localização para determinar o comportamento do safesearch.<br /><br /> Não ofusque a morada (por exemplo, ao alterar o último octeto para 0). Ofuscar a morada faz com que a localização não esteja perto da localização efetiva do dispositivo, o que pode fazer com que o Bing sirva resultados errados.|

## <a name="query-parameters"></a>Parâmetros de consulta
O pedido pode incluir os seguintes parâmetros de consulta. Consulte a coluna necessária para os parâmetros necessários. Tem de codificar os parâmetros de consulta. A consulta deve ser um URL absoluto com um esquema http ou https; não apoiamos URLs relativos ou outros esquemas como ftp://

|Nome|Valor|Tipo|Necessário|
|----------|-----------|----------|--------------|
|<a name="mkt" />mkt|O mercado de onde os resultados provêm. <br /><br />Para obter uma lista de possíveis valores de mercado, consulte códigos de mercado.<br /><br /> **NOTA:** A API de pré-visualização de URL atualmente apenas suporta geografia dos EUA e língua inglesa.<br /><br />|String|Sim|
|<a name="query" />q|O URL para pré-visualizar|String|Sim|
|<a name="responseformat" />respostaFormat|O tipo de mídia a utilizar para a resposta. Seguem-se os possíveis valores insensíveis aos casos.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> O padrão é JSON. Para obter informações sobre os objetos JSON que a resposta contém, consulte [Objetos de Resposta](#response-objects).<br /><br />Se especificar jsonLd, o corpo de resposta inclui objetos JSON-LD que contêm os resultados da pesquisa. Para obter informações sobre o JSON-LD, consulte [JSON-LD](https://json-ld.org/).|String|Não|
|<a name="safesearch"/>safeSearch|O conteúdo ilegal de adultos, ou conteúdo pirata, está bloqueado com o código de erro 400, e a bandeira *isFamilyFriendly* não é devolvida. <p>Para conteúdo legal adulto, abaixo está o comportamento. O código de estado devolve 200, e a bandeira *isFamilyFriendly* está definida como falsa.<ul><li>safeSearch=rigoroso: Título, descrição, URL e imagem não serão devolvidos.</li><li>safeSearch=moderado; Obtenha título, URL e descrição, mas não a imagem descritiva.</li><li>safeSearch=off; Obtenha todos os objetos/elementos de resposta – título, URL, descrição e imagem.</li></ul> |String|Não é necessário. </br> Predefinições para safeSearch=rigoroso.|

## <a name="response-objects"></a>Objetos de resposta
O esquema de resposta é ou uma [WebPage] ou ErrorResponse, como na API de pesquisa web. Se o pedido falhar, o objeto de nível superior é o objeto [ErrorResponse.](#errorresponse)

|Object|Descrição|
|------------|-----------------|
|[Página Web](#webpage)|Objeto JSON de nível superior que contém atributos de pré-visualização.|

### <a name="error"></a>Erro
Define o erro que ocorreu.

|Elemento|Descrição|Tipo|
|-------------|-----------------|----------|
|código <a name="error-code" />|O código de erro que identifica a categoria de erro. Para obter uma lista de códigos possíveis, consulte [Códigos](#error-codes)de Erro .|String|
|mensagem <a name="error-message" />|Uma descrição do erro.|String|
|<a name="error-moredetails" />maisDetalhes|Uma descrição que fornece informações adicionais sobre o erro.|String|
|<a name="error-parameter" />parâmetro|O parâmetro de consulta no pedido que causou o erro.|String|
|<a name="error-subcode" />subCódigo|O código de erro que identifica o erro. Por exemplo, se `code` for InvalidRequest, `subCode` pode ser ParameterInvalid ou ParameterInvalidValue. |String|
|<a name="error-value" />valor|O valor do parâmetro de consulta que não era válido.|String|

### <a name="errorresponse"></a>Resposta de erro
O objeto de alto nível que a resposta inclui quando o pedido falha.

|Nome|Valor|Tipo|
|----------|-----------|----------|
|_type|Digite a dica.|String|
|erros <a name="errors" />|Uma lista de erros que descrevem as razões pelas quais o pedido falhou.|[Erro](#error)[]|

### <a name="webpage"></a>Página Web
Define informações sobre uma página Web na pré-visualização.

|Nome|Valor|Tipo|
|----------|-----------|----------|
|nome|O título da página, não necessariamente o título HTML|String|
|url|O URL que foi realmente rastejado (pedido pode ter seguido redirecionamentos)|String|
|descrição|Breve descrição da página e do conteúdo|String|
|isFamilyFriendly|Mais preciso para itens no índice web; atreca-se em tempo real fazer esta deteção com base apenas no URL e não no conteúdo da página|boolean|
|primaryImageOfPage/contentUrl|O URL para uma imagem representativa a incluir na pré-visualização|String|

### <a name="identifiable"></a>Identificável
|Nome|Valor|Tipo|
|-------------|-----------------|----------|
|ID|Um identificador de recursos|String|

## <a name="error-codes"></a>Códigos de erro

Seguem-se os possíveis códigos de estado http que um pedido devolve.

|Código de Estado|Descrição|
|-----------------|-----------------|
|200|Êxito.|
|400|Falta um dos parâmetros de consulta ou não é válido.|
|400|Error server, subCode ResourceError: O URL solicitado não pôde ser alcançado|
|400|ServerError, subCode ResourceError: O URL solicitado não devolveu um código de sucesso (incluindo se devolveu HTTP 404)|
|400|Pedido inválido, subCódigo Bloqueado: O URL solicitado pode conter conteúdo adulto e foi bloqueado|
|401|Falta a chave de subscrição ou não é válida.|
|403|O utilizador é autenticado (por exemplo, utilizou uma chave de subscrição válida) mas não tem permissão para o recurso solicitado.<br /><br /> Bing também pode devolver este estatuto se o chamador exceder as suas consultas por mês.|
|410|O pedido utilizou HTTP em vez do protocolo HTTPS. HTTPS é o único protocolo suportado.|
|429|O chamador excedeu as suas consultas por segunda quota.|
|500|Erro inesperado do servidor.|

Se o pedido falhar, a resposta contém um objeto [ErrorResponse,](#errorresponse) que contém uma lista de objetos [error](#error) que descrevem o que causou o erro. Se o erro estiver relacionado com um parâmetro, o campo `parameter` identifica o parâmetro que é o problema. E se o erro estiver relacionado com um valor de parâmetro, o campo `value` identifica o valor que não é válido.

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

Seguem-se os possíveis códigos de erro e os valores do código de sub-erro.

|Código|Subcódigo|Descrição
|-|-|-
|ServerError|UnexpectedError<br/>Erro de Recursos<br/>NotImplemented|O código de estado HTTP é 500.
|Pedido inválido|Parâmetros desaparecidos<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqueado|Devoluções Bing InvalidRequest sempre que qualquer parte do pedido não for válida. Por exemplo, falta um parâmetro necessário ou não é válido um valor de parâmetro.<br/><br/>Se o erro for ParameterMissing ou ParameterInvalidValue, o código de estado HTTP é de 400.<br/><br/>Se utilizar o protocolo HTTP em vez de HTTPS, bing devolve httpNotAllowed, e o código de estado HTTP é 410.
|RateLimitExceeded|Sem subcódigos|Bing devolve RateLimitExceeded sempre que excede as suas consultas por segundo (QPS) ou consultas por mês (QPM) quota.<br/><br/>Se exceder o QPS, bing devolve o código de estado HTTP 429, e se exceder o QPM, bing devolve 403.
|Autorização Inválida|Autorização Faltando<br/>AutorizaçãoRedundy|Bing devolve InvalidAuthorization quando Bing não pode autenticar o chamador. Por exemplo, falta o cabeçalho `Ocp-Apim-Subscription-Key` ou a chave de subscrição não é válida.<br/><br/>O despedimento ocorre se especificar mais do que um método de autenticação.<br/><br/>Se o erro for InvalidAuthorization, o código de estado HTTP é 401.
|InsufficientAuthorization|AutorizaçãoDeficiente<br/>Autorizações Caducadas|Bing devolve InsuficienteAutorização quando o chamador não tem permissões para aceder ao recurso. Isto pode ocorrer se a chave de subscrição tiver sido desativada ou tiver expirado. <br/><br/>Se o erro for insuficiente, o código de estado HTTP é 403.

## <a name="next-steps"></a>Passos seguintes
- [Início Rápido do C#](csharp.md)
- [Início rápido do Java](java-quickstart.md)
- [Início rápido do JavaScript](javascript.md)
- [Início rápido do Node](node-quickstart.md)
- [Início Rápido do Python](python-quickstart.md)
