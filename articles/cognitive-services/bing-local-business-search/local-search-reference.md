---
title: Referência de v7 de API de pesquisa do Bing comercial | Documentos da Microsoft
description: Descreve os elementos de programação da API de pesquisa de negócios locais do Bing.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: e96b1959d9e03273a9ca4c549c0f8b0bda6a708b
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592795"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Referência da API de pesquisa de negócios locais do Bing v7

A API de pesquisa de negócios locais envia uma consulta de pesquisa do Bing para obter resultados que incluem restaurantes, hotéis ou outros negócios locais. Para locais, a consulta pode especificar o nome da empresa local, ou uma categoria (por exemplo, restaurantes perto de mim). Os resultados de entidades incluem pessoas, locais ou coisas. Local neste contexto é entidades comerciais, Estados, países/regiões, etc.  

Esta secção fornece detalhes técnicos sobre os objetos de resposta e os parâmetros de consulta e os cabeçalhos que afetam os resultados da pesquisa. Para obter exemplos que mostram como fazer pedidos, consulte [Local de negócios de pesquisa C# início rápido](quickstarts/local-quickstart.md) ou [início rápido de Java de pesquisa de negócios locais](quickstarts/local-search-java-quickstart.md). 
  
Para obter informações sobre cabeçalhos de pedidos devem incluir, consulte [cabeçalhos](#headers).  
  
Para obter informações sobre parâmetros de consulta que devem incluir pedidos, consulte [parâmetros de consulta](#query-parameters).  
  
Para obter informações sobre o JSON de objetos que inclui a resposta, consulte [objetos de resposta](#response-objects).

Para obter informações sobre o uso permitido e a exibição de resultados, consulte [utilizar e apresentam os requisitos](use-display-requirements.md).


  
## <a name="endpoint"></a>Ponto Final  
Para pedir os resultados de negócios locais, envie um pedido GET para: 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
O pedido tem de utilizar o protocolo HTTPS.  
  
> [!NOTE]
> O comprimento máximo do URL é de 2048 carateres. Para garantir que o comprimento do URL não excede o limite, o comprimento máximo de seus parâmetros de consulta deve ser inferior a 1 500 carateres. Se o URL exceder 2048 carateres, o servidor devolve 404 não encontrado.  
  
  
## <a name="headers"></a>Cabeçalhos  
Seguem-se os cabeçalhos que podem incluir uma solicitação e resposta.  
  
|Cabeçalho|Descrição|  
|------------|-----------------|  
|Aceite|Cabeçalho de pedido opcional.<br /><br /> O tipo de suporte de dados predefinido é application/json. Para especificar que a resposta é usar [JSON LD](https://json-ld.org/), defina o cabeçalho Accept como application/ld + json.|  
|<a name="acceptlanguage" />Accept-Language|Cabeçalho de pedido opcional.<br /><br /> Uma lista separada por vírgulas dos idiomas que vão ser utilizados nas cadeias da interface de utilizador. A lista está em ordem decrescente de preferência. Para obter mais informações, incluindo o formato esperado, veja [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Este cabeçalho e o parâmetro de consulta [setLang](#setlang) são mutuamente exclusivos &mdash; não os especifique a ambos.<br /><br /> Se definir este cabeçalho, também tem de especificar o parâmetro de consulta de cc. Para determinar o mercado do qual devolver resultados, o Bing utiliza o primeiro idioma suportado que encontra na lista e combina-o com o valor do parâmetro `cc`. Se a lista não incluir um idioma suportado, o Bing encontra o idioma e o mercado mais parecidos que suportem o pedido ou utiliza um mercado agregado ou predefinido para devolver os resultados. Para determinar o mercado que o Bing utiliza, veja o cabeçalho BingAPIs-Market.<br /><br /> Utilize este cabeçalho o parâmetro de consulta `cc` apenas se especificar vários idiomas. Caso contrário, utilize os parâmetros de consulta [mkt](#mkt) e [setLang](#setlang).<br /><br /> As cadeias da interface de utilizador são cadeias utilizadas como etiquetas na interface de utilizador. Os objetos de resposta JSON têm poucas cadeias de interface de utilizador. Todas as ligações para propriedades de Bing.com nos objetos de resposta aplicam o idioma especificado.|  
|<a name="market" />BingAPIs-Market|O cabeçalho da resposta.<br /><br /> O mercado que o pedido utiliza. O formato é \<languageCode\>-\<códigodeidioma\>. Por exemplo: en-US.|  
|<a name="traceid" />BingAPIs-TraceId|O cabeçalho da resposta.<br /><br /> O ID da entrada de registo que contém os detalhes do pedido. Quando ocorrer um erro, capture este ID. Se não conseguir determinar nem resolver o problema, inclua o ID juntamente com as outras informações que enviar à equipa de suporte.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Cabeçalho de pedido obrigatório.<br /><br /> A chave de subscrição que recebeu quando se inscreveu neste serviço nos [Serviços Cognitivos](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Cabeçalho de pedido opcional<br /><br /> Por predefinição, o Bing devolve conteúdo em cache, se disponível. Para impedir que o Bing devolva conteúdo em cache, defina o cabeçalho Pragma como no-cache (por exemplo, Pragma: no-cache).
|<a name="useragent" />User-Agent|Cabeçalho de pedido opcional.<br /><br /> O agente de utilizador que origina o pedido. O Bing utiliza o agente de utilizador para proporcionar uma experiência otimizada aos utilizadores de dispositivos móveis. Embora seja opcional, recomenda-se que especifique sempre este cabeçalho.<br /><br /> O agente de utilizador deve ser a mesma cadeia que qualquer browser geralmente utilizado envia. Para obter informações sobre os agentes de utilizador, veja [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Pode ver exemplos de cadeias de agente de utilizador abaixo:<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Cabeçalho de pedido e resposta opcional.<br /><br /> O Bing utiliza este cabeçalho para proporcionar um comportamento consistente nas chamada à API do Bing aos utilizadores. O Bing lança, muitas vezes, funcionalidades e melhorias novas e utiliza o ID de cliente como uma chave para atribuir tráfego aos diferentes lançamentos. Se não utilizar o mesmo ID de cliente para um utilizador em vários pedidos, o Bing poderá atribuir esse utilizador a vários lançamentos sobrepostos. A atribuição a vários lançamentos sobrepostos pode provocar uma experiência de utilizador inconsistente. Por exemplo, se a atribuição de lançamento do segundo pedido for diferente da do primeiro, a experiência poderá ser inesperada. Além disso, o Bing pode utilizar o ID de cliente para personalizar os resultados da Web o histórico de pesquisas desse ID, proporcionando uma experiência mais rica ao utilizador.<br /><br /> O Bing também utiliza este cabeçalho para ajudar a melhorar as classificações dos resultados ao analisar a atividade gerada por um ID de cliente. As melhorias à relevância ajudam a que as APIs do Bing devolvam resultados com melhor qualidade, o que, por sua vez, origina taxas de cliques mais altas para o consumidor da API.<br /><br /> **IMPORTANTE:** Embora seja opcional, deve considerar este cabeçalho obrigado. Persistir o ID de cliente em vários pedidos para a mesma combinação de utilizador final e dispositivo permite 1) que o consumidor da API receba uma experiência de utilizador consistente e 2) taxas de cliques mais altas, através de resultados com melhor qualidade das APIs do Bing.<br /><br /> As regras abaixo são as regras de utilização básicas aplicáveis a este cabeçalho.<br /><ul><li>Cada utilizador que utilize a sua aplicação no dispositivo tem de ter um ID de cliente exclusivo e gerado pelo Bing.<br /><br/>Se não incluir este cabeçalho no pedido, o Bing gera um ID e devolve-o no cabeçalho da resposta X-MSEdge-ClientID. O único momento em que NÃO deve incluir este cabeçalho num pedido é na primeira vez que o utilizador utiliza a sua aplicação nesse dispositivo.<br /><br/></li><li>Utilize o ID de cliente para cada pedido da API do Bing que a sua aplicação fizer para este utilizador no dispositivo.<br /><br/></li><li>**ATENÇÃO:** Tem de se certificar de que este ID de cliente não é vinculação para quaisquer informações de conta de utilizador authenticatable.</li><br/><li>Persista o ID de cliente. Para persistir o ID numa aplicação de browser, utilize um cookie HTTP persistente para garantir que esse ID é utilizado em todas as sessões. Não utilize um cookie de sessão. Relativamente a outras aplicações, como aplicações móveis, utilize o armazenamento persistente do dispositivo para persistir o ID.<br /><br/>Da próxima vez que o utilizador utilizar a aplicação nesse dispositivo, recebe o ID de cliente que foi persistido.</li></ul><br /> **NOTA:** Respostas do Bing podem ou não podem incluir esse cabeçalho. Se a resposta incluir este cabeçalho, capture o ID de cliente e utilize-o em todos os pedidos do Bing subsequentes do utilizador nesse dispositivo.<br /><br /> **NOTA:** Se incluir o ClientID de MSEdge X, não pode incluir cookies no pedido.|  
|<a name="clientip" />X-MSEdge-ClientIP|Cabeçalho de pedido opcional.<br /><br /> O endereço IPv4 ou IPv6 do dispositivo cliente. O endereço IP é utilizado para detetar a localização do utilizador. O Bing utiliza as informações de localização para determinar o comportamento do safesearch.<br /><br /> **NOTA:** Embora seja opcional, é encorajado a sempre especificar esse cabeçalho e o cabeçalho X-pesquisa-Location.<br /><br /> Não ofusque a morada (por exemplo, ao alterar o último octeto para 0). Ofuscar a morada faz com que a localização não esteja perto da localização efetiva do dispositivo, o que pode fazer com que o Bing sirva resultados errados.|  
|<a name="location" />X-Search-Location|Cabeçalho de pedido opcional.<br /><br /> Uma lista separada por ponto e vírgula de pares de chaves/valores que descreve a localização geográfica do cliente. O Bing utiliza as informações de localização para determinar o comportamento do safesearch e para devolver conteúdos locais relevantes. Indique o par chave/valor como \<chave\>:\<valor\>. As chaves seguintes são as chaves utilizadas para especificar a localização do utilizador.<br /><br /><ul><li>LAT&mdash;a latitude da localização do cliente, em graus. Tem de ser igual ou superior a -90,0 e inferior ou igual a +90,0. Os valores negativos indicam latitudes no hemisfério sul e os positivos latitudes no hemisfério norte.<br /><br /></li><li>longa&mdash;a longitude da localização do cliente, em graus. Tem de ser igual ou superior a -180.0 e inferior ou igual a +180.0. Os valores negativos indicam longitudes a ocidente e os positivos a oriente.<br /><br /></li><li>Re&mdash; raio, em metros, que especifica a precisão horizontal das coordenadas. Transmita o valor que o serviço de localização do dispositivo devolve. Os valores mais comuns podem ser 22 m para GPS/Wi-Fi, 380 m para triangulação de antenas e 18 000 m para pesquisa de IP inversa.<br /><br /></li><li>TS&mdash; timestamp o UNIX de UTC de quando o cliente estava no local. (o carimbo de data/hora é o número de segundos desde 1 de janeiro de 1970.)<br /><br /></li><li>head&mdash;Optional. O cabeçalho relativo do cliente ou a direção de deslocamento. Especifique a direção de deslocamento em graus, de 0 a 360, a contar no sentido dos ponteiros do relógio em relação ao norte verdadeiro. Especifique esta chave apenas se a chave `sp` não for zero.<br /><br /></li><li>SP&mdash; a velocidade horizontal (velocidade), em metros por segundo, o que o dispositivo cliente está em uma viagem.<br /><br /></li><li>ALT&mdash; a altitude do dispositivo cliente, em metros.<br /><br /></li><li>are&mdash;Optional. O raio, em metros, que especifica a exatidão vertical das coordenadas. Predefinições de RADIUS para 50 Quilômetros. Especifique esta chave apenas se especificar a chave `alt`.<br /><br /></li></ul> **NOTA:** Embora estas chaves são opcionais, a obter mais informações fornecidas por si, são mais precisas dos resultados de localização.<br /><br /> **NOTA:** É encorajado a sempre especificar a localização geográfica do utilizador. Especificar a localização é particularmente importante se o endereço IP do cliente não refletir, com precisão, a localização física do utilizador (por exemplo, se este utilizar VPN). Para obter os melhores resultados, deve incluir este cabeçalho e o cabeçalho X-MSEdge-ClientIP, mas deve incluir este cabeçalho, no mínimo.|

> [!NOTE] 
> Não se esqueça de que os Termos de Utilização requerem conformidade com toda a legislação aplicável, incluindo a relativa à utilização destes cabeçalhos. Por exemplo, em determinadas jurisdições, como a Europa, há requisitos para obter o consentimento do utilizador antes de colocar certos tipos de dispositivos de monitorização nos dispositivos dos utilizadores.
  

## <a name="query-parameters"></a>Parâmetros de consulta  
O pedido pode incluir os seguintes parâmetros de consulta. Consulte a coluna necessária para parâmetros obrigatórios. Tem o URL de codificar os parâmetros de consulta.  
  
  
|Name|Value|Type|Necessário|  
|----------|-----------|----------|--------------|
|<a name="count" />Contagem|O número de resultados a devolver, começando com o índice especificado pelo `offset` parâmetro.|String|Não|   
|<a name="localCategories" />localCategories|Lista de opções que definem a pesquisa por categoria de negócios.  Consulte [Local de negócios de categorias de pesquisa](local-categories.md)|String|Não|  
|<a name="mkt" />mkt|O mercado de onde os resultados provêm. <br /><br />Para obter uma lista de possíveis valores de mercado, consulte códigos de mercado.<br /><br /> **NOTA:** A API de pesquisa de negócios locais atualmente suporta apenas en--nos mercado e a linguagem.<br /><br />|String|Sim|
|<a name="offset"/>offset|O índice inicial de resultados especificados pelo `count` parâmetro.|Integer|Não|  
|<a name="query" />q|Termo de pesquisa do usuário.|String|Não|  
|<a name="responseformat" />responseFormat|O tipo de mídia a utilizar para a resposta. Seguem-se os valores possíveis de maiúsculas e minúsculas.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> A predefinição é JSON. Para obter informações sobre o JSON de objetos que contém a resposta, consulte [objetos de resposta](#response-objects).<br /><br />  Se especificar JsonLd, o corpo da resposta inclui os objetos de JSON LD que contêm os resultados da pesquisa. Para obter informações sobre o JSON-LD, consulte [JSON LD](https://json-ld.org/).|String|Não|  
|<a name="safesearch" />safeSearch|É um filtro utilizado para filtrar conteúdo para adultos. Os valores seguintes são os possíveis valores do filtro, não sensíveis a maiúsculas e minúsculas.<br /><ul><li>Desativar&mdash;devolver páginas da Web com o texto para adultos, imagens ou vídeos.<br /><br/></li><li>Moderado&mdash;devolver páginas da Web com o texto para adultos, mas não para adultos imagens ou vídeos.<br /><br/></li><li>Strict&mdash;não retornam páginas da Web com o texto para adultos, imagens ou vídeos.</li></ul><br /> A predefinição é Moderado.<br /><br /> **NOTA:** Se a solicitação é proveniente de um mercado de política de adultos do Bing que requer que `safeSearch` está definido para Strict, Bing ignora o `safeSearch` valor e utiliza Strict.<br/><br/>**NOTA:** Se utilizar o `site:` operador de consulta, é provável que a resposta pode conter o conteúdo para adultos, independentemente do que o `safeSearch` parâmetro de consulta está definido como. Utilize `site:` apenas se tiver conhecimento do conteúdo do site e se o seu cenário suportar a possibilidade de conteúdos para adultos. |String|Não|  
|<a name="setlang" />setLang|O idioma a utilizar nas cadeias da interface de utilizador. Especifique o idioma com o código de idioma ISO 639-1 de duas letras. Por exemplo, o código de idioma de inglês é EN. A predefinição é EN (inglês).<br /><br /> Embora seja opcional, deve indicar sempre o idioma. Normalmente, `setLang` é definido como o mesmo idioma especificado por `mkt`, a não ser que o utilizador queira que as cadeias da interface de utilizador sejam apresentadas noutro idioma.<br /><br /> Este parâmetro e o cabeçalho [Accept-Language](#acceptlanguage) são mutuamente exclusivos &mdash; não os especifique a ambos.<br /><br /> As cadeias da interface de utilizador são cadeias utilizadas como etiquetas na interface de utilizador. Os objetos de resposta JSON têm poucas cadeias de interface de utilizador. Além disso, todas as ligações para propriedades de Bing.com nos objetos de resposta aplicam o idioma especificado.|String|Não| 


## <a name="response-objects"></a>Objetos de resposta  
Seguem-se os objetos de resposta JSON que pode incluir a resposta. Se a solicitação for bem-sucedida, o objeto de nível superior na resposta é o [SearchResponse](#searchresponse) objeto. Se o pedido falhar, o objeto de nível superior é o [byl vrácen Prvek](#errorresponse) objeto.


|Object|Descrição|  
|------------|-----------------|  
|[Local](#place)|Define as informações sobre um negócio local como um restaurante ou hotel.|  

  
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

  
  
### <a name="license"></a>Licença  
Define a licença sob a qual podem ser utilizada o texto ou fotos.  
  
|Name|Value|Type|  
|----------|-----------|----------|  
|name|O nome da licença.|String|  
|url|O URL para um Web site em que o utilizador pode obter mais informações sobre a licença.<br /><br /> Utilize o nome e o URL para criar uma hiperligação.|String|  


### <a name="link"></a>Ligação  
Define os componentes de um hiperlink.  
  
|Name|Value|Type|  
|----------|-----------|----------|  
|_type|Dica de tipo.|String|  
|texto|O texto de exibição.|String|  
|url|UM URL. Utilize o URL e apresentar o texto para criar uma hiperligação.|String|  
  


  
### <a name="organization"></a>Organização  
Define um publicador.  
  
Tenha em atenção que um fabricante poderá fornecer seu nome ou o site ou ambos.  
  
|Name|Value|Type|  
|----------|-----------|----------|  
|name|O nome do Editor.|String|  
|url|O URL para o site do publicador.<br /><br /> Tenha em atenção que o publicador poderão não fornecer um Web site.|String|  
  
  

### <a name="place"></a>Local  
Define as informações sobre o local de uma empresa, como um restaurante ou hotel.  
  
|Name|Value|Type|  
|----------|-----------|----------|  
|_type|Dica de tipo, que pode ser definida como um dos seguintes:<br /><br /><ul><li>Hotel</li><li>LocalBusiness<br /></li><li>Restaurante</ul><li>|String|  
|Endereço|O endereço postal de onde está localizada a entidade.|PostalAddress|  
|entityPresentationInfo|Obter informações adicionais sobre a entidade como sugestões que pode utilizar para determinar o tipo da entidade. Por exemplo, se é um restaurante ou hotel. O `entityScenario` campo é definido como ListItem.|EntityPresentationInfo|  
|name|Nome da entidade.|String|  
|Telefone|Número de telefone da entidade.|String|  
|url|O URL para o site da entidade.<br /><br /> Utilizar este URL, juntamente com o nome da entidade para criar uma hiperligação que quando clicado leva o usuário ao site da entidade.|String|  
|webSearchUrl|O URL para o resultado da pesquisa do Bing para este local.|String| 
  
  
### <a name="querycontext"></a>QueryContext  
Define o contexto de consulta que Bing utilizado para o pedido.  
  
|Elemento|Descrição|Type|  
|-------------|-----------------|----------|  
|adultIntent|Um valor booleano que indica se a consulta especificada tem intencional adulto. O valor é **true** se a consulta tem intencional adulto; caso contrário, **falso**.|Boolean|  
|alterationOverrideQuery|A cadeia de consulta a utilizar para forçar o Bing para utilizar a cadeia de caracteres original. Por exemplo, se a cadeia de consulta é *saling downwind*, a cadeia de caracteres de consulta de substituição será *+ saling downwind*. Lembre-se codificar a cadeia de consulta que resulta em *% 2Bsaling + downwind*.<br /><br /> Este campo está incluído apenas se a cadeia de consulta original contém um erro de ortografia.|String|  
|alteredQuery|A cadeia de consulta utilizada pelo Bing para realizar a consulta. O Bing utiliza a cadeia de consulta alterada se a cadeia de consulta original contido erros de ortografia. Por exemplo, se a cadeia de consulta é `saling downwind`, a cadeia de consulta alterada será `sailing downwind`.<br /><br /> Este campo está incluído apenas se a cadeia de consulta original contém um erro de ortografia.|String|  
|askUserForLocation|Um valor booleano que indica se Bing necessita que a localização do utilizador para fornecer resultados precisos. Se tiver especificado na localização do utilizador utilizando o [MSEdge-X-ClientIP](#clientip) e [X-pesquisa-Location](#location) cabeçalhos, pode ignorar este campo.<br /><br /> Para consultas com suporte para localização, como "meteorológica de hoje" ou "restaurantes contra ao meu redor" que precisam de localização do utilizador para fornecer resultados precisos, este campo é definido como **true**.<br /><br /> Para localização com suporte para consultas que incluem a localização (por exemplo, "tempo da Seattle"), este campo é definido como **false**. Este campo também é definido como **false** para consultas que não são localização com suporte para, por exemplo, "mais vendidos".|Boolean|  
|originalQuery|A cadeia de consulta, conforme especificado no pedido.|String|  

### <a name="identifiable"></a>Identificação

|Name|Value|Type|  
|-------------|-----------------|----------|
|id|Um identificador de recurso|String|
 
### <a name="rankinggroup"></a>RankingGroup
Define uma pesquisa resulta de grupo, tal como mainline.

|Name|Value|Type|  
|-------------|-----------------|----------|
|items|Uma lista de resultados de pesquisa para ver o grupo.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Define um item de resultado de pesquisa para apresentar.

|Name|Value|Type|  
|-------------|-----------------|----------|
|resultIndex|Um índice baseado em zero do item na resposta a apresentar. Se o item não incluir este campo, apresenta todos os itens na resposta. Por exemplo, exiba todos os artigos de notícias na resposta de notícias.|Integer|
|answerType|A resposta que contém o item para apresentar. Por exemplo, notícias.<br /><br />Utilize o tipo para encontrar a resposta no objeto SearchResponse. O tipo é o nome de um campo de SearchResponse.<br /><br /> No entanto, utilizar o tipo de resposta apenas se este objeto inclui o campo de valor; caso contrário, ignorá-lo.|String|
|textualIndex|O índice da resposta sob textualAnswers para apresentar.| Número inteiro não assinado|
|value|O ID identifica uma resposta para apresentar ou um item de uma resposta para apresentar. Se o ID identifica uma resposta, exiba todos os itens da resposta.|Identificação|

### <a name="rankingresponse"></a>RankingResponse  
Define onde na pesquisa deve ser colocado o conteúdo da página de resultados e por que ordem.  
  
|Name|Value|  
|----------|-----------|  
|<a name="ranking-mainline" />mainline|Os resultados da pesquisa para apresentar os principais no.|  
|<a name="ranking-pole" />Polo|Os resultados da pesquisa que devem ser proporcionados o tratamento mais visível (por exemplo, são apresentados acima o principal e barra lateral).|  
|<a name="ranking-sidebar" />sidebar|Os resultados da pesquisa para apresentar na barra lateral.| 

### <a name="searchresponse"></a>SearchResponse  
Define o objeto de nível superior que a resposta inclui quando o pedido for concluída com êxito.  
  
Tenha em atenção que, se o serviço suspeita de um ataque de negação de serviço, o pedido terá êxito (código de estado HTTP é 200 OK); No entanto, o corpo da resposta estará vazio.  
  
|Name|Value|Type|  
|----------|-----------|----------|  
|_type|Dica de tipo, que é definida como SearchResponse.|String|  
|Locais|Uma lista de entidades que são relevantes para a consulta de pesquisa.|Objeto JSON|  
|queryContext|Um objeto que contenha a cadeia de consulta que Bing utilizado para o pedido.<br /><br /> Este objeto contém a cadeia de consulta, tal como foi introduzido pelo utilizador. Também pode conter uma cadeia de caracteres de consulta alterada Bing utilizado para a consulta se a cadeia de consulta continha um erro de ortografia.|[QueryContext](#querycontext)|  


## <a name="error-codes"></a>Códigos de erro

Seguem-se os possíveis códigos de estado HTTP que retorna um pedido.  
  
|Código de estado|Descrição|  
|-----------------|-----------------|  
|200|Êxito.|  
|400|Um dos parâmetros de consulta está em falta ou não é válido.|  
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
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Código de estado HTTP é 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqueado|O Bing devolve InvalidRequest sempre que qualquer parte do pedido não é válido. Por exemplo, um parâmetro necessário está em falta ou um valor de parâmetro não é válido.<br/><br/>Se o erro é ParameterMissing ou ParameterInvalidValue, o código de estado HTTP é 400.<br/><br/>Se utilizar o protocolo HTTP em vez de HTTPS, o Bing devolve HttpNotAllowed e o código de estado HTTP é 410.
|RateLimitExceeded|Sem códigos secundárias|O Bing devolve RateLimitExceeded sempre que excedem suas consultas por segundo (QPS) ou consultas por quota do mês (QPM).<br/><br/>Se ultrapassar QPS, o Bing devolve o código de estado HTTP 429 e, se exceder QPM, o Bing devolve 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|O Bing devolve InvalidAuthorization ao Bing não é possível autenticar o chamador. Por exemplo, o `Ocp-Apim-Subscription-Key` cabeçalho está em falta ou a chave de subscrição não é válida.<br/><br/>Redundância ocorre se especificar mais do que um método de autenticação.<br/><br/>Se o erro for InvalidAuthorization, o código de estado HTTP é 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|O Bing devolve InsufficientAuthorization quando o chamador não tem permissões para aceder ao recurso. Isto pode ocorrer se a chave de subscrição foi desativada ou expirou. <br/><br/>Se o erro for InsufficientAuthorization, o código de estado HTTP é 403.

## <a name="next-steps"></a>Passos Seguintes
- [Guia de introdução de pesquisa de negócios local](quickstarts/local-quickstart.md)
- [Início rápido de Java de pesquisa de negócios local](quickstarts/local-search-java-quickstart.md)
- [Guia de introdução de nó de pesquisa de negócios local](quickstarts/local-search-node-quickstart.md)
- [Guia de introdução de Python de pesquisa de negócios local](quickstarts/local-search-python-quickstart.md)
