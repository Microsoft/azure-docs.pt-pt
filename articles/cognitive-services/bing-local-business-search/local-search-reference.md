---
title: Bing Local Pesquisa de Negócios Referência V7 Referência
titleSuffix: Azure Cognitive Services
description: Este artigo fornece detalhes técnicos sobre os objetos de resposta, e os parâmetros de consulta e cabeçalhos que afetam os resultados da pesquisa.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: dbd4f32e77dc8d386067987a0ab0436a7875c15e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095395"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Bing Local Business Search API v7 referência

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

A API local de pesquisa de negócios envia uma consulta de pesquisa para Bing para obter resultados que incluem restaurantes, hotéis ou outros negócios locais. Para lugares, a consulta pode especificar o nome da empresa local ou uma categoria (por exemplo, restaurantes perto de mim). Os resultados de entidades incluem pessoas, locais ou coisas. Neste contexto estão entidades empresariais, estados, países/regiões, etc.  

Esta secção fornece detalhes técnicos sobre os objetos de resposta e os parâmetros de consulta e cabeçalhos que afetam os resultados da pesquisa. Para exemplos que mostram como fazer pedidos, consulte [o quickstart da Pesquisa de Negócios Locais C#](quickstarts/local-quickstart.md) ou o [quickstart de Pesquisa de Negócios Locais Java](quickstarts/local-search-java-quickstart.md). 
  
Para obter informações sobre cabeçalhos que os pedidos devem incluir, consulte [Headers](#headers).  
  
Para obter informações sobre os parâmetros de consulta que os pedidos devem incluir, consulte [parâmetros de consulta](#query-parameters).  
  
Para obter informações sobre os objetos JSON que a resposta inclui, consulte [os objetos de resposta](#response-objects).

Para obter informações sobre a utilização permitida e exibição de [resultados, consulte os requisitos de utilização e exibição](use-display-requirements.md).


  
## <a name="endpoint"></a>Ponto final  
Para solicitar os resultados locais do negócio, envie um pedido GET para: 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
O pedido tem de utilizar o protocolo HTTPS.  
  
> [!NOTE]
> O comprimento máximo de URL é de 2.048 caracteres. Para garantir que o comprimento do URL não exceda o limite, o comprimento máximo dos seus parâmetros de consulta deve ser inferior a 1.500 caracteres. Se o URL exceder 2.048 caracteres, o servidor retorna 404 Não encontrado.  
  
  
## <a name="headers"></a>Cabeçalhos  
Seguem-se os cabeçalhos que um pedido e resposta podem incluir.  
  
|Cabeçalho|Description|  
|------------|-----------------|  
|Aceitar|Cabeçalho de pedido opcional.<br /><br /> O tipo de mídia predefinido é aplicação/json. Para especificar que a utilização da resposta [JSON-LD,](https://json-ld.org/)desa estale o cabeçalho Aceitar para aplicação/ld+json.|  
|<a name="acceptlanguage"></a>Accept-Language|Cabeçalho de pedido opcional.<br /><br /> Uma lista separada por vírgulas dos idiomas que vão ser utilizados nas cadeias da interface de utilizador. A lista está em ordem decrescente de preferência. Para obter mais informações, incluindo o formato esperado, veja [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Este cabeçalho e o parâmetro de consulta [setLang](#setlang) são mutuamente exclusivos &mdash; não os especifique a ambos.<br /><br /> Se definir este cabeçalho, também tem de especificar o parâmetro de consulta cc. Para determinar o mercado do qual devolver resultados, o Bing utiliza o primeiro idioma suportado que encontra na lista e combina-o com o valor do parâmetro `cc`. Se a lista não incluir um idioma suportado, o Bing encontra o idioma e o mercado mais parecidos que suportem o pedido ou utiliza um mercado agregado ou predefinido para devolver os resultados. Para determinar o mercado que o Bing utiliza, veja o cabeçalho BingAPIs-Market.<br /><br /> Utilize este cabeçalho o parâmetro de consulta `cc` apenas se especificar vários idiomas. Caso contrário, utilize os parâmetros de consulta [mkt](#mkt) e [setLang](#setlang).<br /><br /> As cadeias da interface de utilizador são cadeias utilizadas como etiquetas na interface de utilizador. Os objetos de resposta JSON têm poucas cadeias de interface de utilizador. Todas as ligações para propriedades de Bing.com nos objetos de resposta aplicam o idioma especificado.|  
|<a name="market"></a>BingAPIs-Market|O cabeçalho da resposta.<br /><br /> O mercado que o pedido utiliza. O formulário \<languageCode\> - \<countryCode\> é. Por exemplo: en-US.|  
|<a name="traceid"></a>BingAPIs-TraceId|O cabeçalho da resposta.<br /><br /> O ID da entrada de registo que contém os detalhes do pedido. Quando ocorrer um erro, capture este ID. Se não conseguir determinar nem resolver o problema, inclua o ID juntamente com as outras informações que enviar à equipa de suporte.|  
|<a name="subscriptionkey"></a>Ocp-Apim-Subscription-Key|Cabeçalho de pedido obrigatório.<br /><br /> A chave de subscrição que recebeu quando se inscreveu neste serviço nos [Serviços Cognitivos](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma"></a>Pragma|Cabeçalho de pedido opcional<br /><br /> Por predefinição, o Bing devolve conteúdo em cache, se disponível. Para impedir que o Bing devolva conteúdo em cache, defina o cabeçalho Pragma como no-cache (por exemplo, Pragma: no-cache).
|<a name="useragent"></a>User-Agent|Cabeçalho de pedido opcional.<br /><br /> O agente de utilizador que origina o pedido. O Bing utiliza o agente de utilizador para proporcionar uma experiência otimizada aos utilizadores de dispositivos móveis. Embora seja opcional, recomenda-se que especifique sempre este cabeçalho.<br /><br /> O agente de utilizador deve ser a mesma cadeia que qualquer browser geralmente utilizado envia. Para obter informações sobre os agentes de utilizador, veja [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Pode ver exemplos de cadeias de agente de utilizador abaixo:<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid"></a>X-MSEdge-ClientID|Cabeçalho de pedido e resposta opcional.<br /><br /> O Bing utiliza este cabeçalho para proporcionar um comportamento consistente nas chamada à API do Bing aos utilizadores. O Bing lança, muitas vezes, funcionalidades e melhorias novas e utiliza o ID de cliente como uma chave para atribuir tráfego aos diferentes lançamentos. Se não utilizar o mesmo ID de cliente para um utilizador em vários pedidos, o Bing poderá atribuir esse utilizador a vários lançamentos sobrepostos. A atribuição a vários lançamentos sobrepostos pode provocar uma experiência de utilizador inconsistente. Por exemplo, se a atribuição de lançamento do segundo pedido for diferente da do primeiro, a experiência poderá ser inesperada. Além disso, o Bing pode utilizar o ID de cliente para personalizar os resultados da Web o histórico de pesquisas desse ID, proporcionando uma experiência mais rica ao utilizador.<br /><br /> O Bing também utiliza este cabeçalho para ajudar a melhorar as classificações dos resultados ao analisar a atividade gerada por um ID de cliente. As melhorias à relevância ajudam a que as APIs do Bing devolvam resultados com melhor qualidade, o que, por sua vez, origina taxas de cliques mais altas para o consumidor da API.<br /><br /> **IMPORTANTE:** embora seja opcional, deve ver este cabeçalho como obrigatório. Persistir o ID de cliente em vários pedidos para a mesma combinação de utilizador final e dispositivo permite 1) que o consumidor da API receba uma experiência de utilizador consistente e 2) taxas de cliques mais altas, através de resultados com melhor qualidade das APIs do Bing.<br /><br /> As regras abaixo são as regras de utilização básicas aplicáveis a este cabeçalho.<br /><ul><li>Cada utilizador que utilize a sua aplicação no dispositivo tem de ter um ID de cliente exclusivo e gerado pelo Bing.<br /><br/>Se não incluir este cabeçalho no pedido, o Bing gera um ID e devolve-o no cabeçalho da resposta X-MSEdge-ClientID. O único momento em que NÃO deve incluir este cabeçalho num pedido é na primeira vez que o utilizador utiliza a sua aplicação nesse dispositivo.<br /><br/></li><li>Utilize o ID de cliente para cada pedido da API do Bing que a sua aplicação fizer para este utilizador no dispositivo.<br /><br/></li><li>**ATENÇÃO:** Tem de garantir que este ID do Cliente não está ligado a nenhuma informação autenticada da conta de utilizador.</li><br/><li>Persista o ID de cliente. Para persistir o ID numa aplicação de browser, utilize um cookie HTTP persistente para garantir que esse ID é utilizado em todas as sessões. Não utilize um cookie de sessão. Relativamente a outras aplicações, como aplicações móveis, utilize o armazenamento persistente do dispositivo para persistir o ID.<br /><br/>Da próxima vez que o utilizador utilizar a aplicação nesse dispositivo, recebe o ID de cliente que foi persistido.</li></ul><br /> **NOTA:** as respostas do Bing podem incluir ou não este cabeçalho. Se a resposta incluir este cabeçalho, capture o ID de cliente e utilize-o em todos os pedidos do Bing subsequentes do utilizador nesse dispositivo.<br /><br /> **NOTA:** se incluir X-MSEdge-ClientID, não pode incluir cookies no pedido.|  
|<a name="clientip"></a>X-MSEdge-ClientIP|Cabeçalho de pedido opcional.<br /><br /> O endereço IPv4 ou IPv6 do dispositivo cliente. O endereço IP é utilizado para detetar a localização do utilizador. O Bing utiliza as informações de localização para determinar o comportamento do safesearch.<br /><br /> **NOTA:** embora seja opcional, recomenda-se que especifique sempre este cabeçalho e o cabeçalho X-Search-Location.<br /><br /> Não ofusque a morada (por exemplo, ao alterar o último octeto para 0). Ofuscar a morada faz com que a localização não esteja perto da localização efetiva do dispositivo, o que pode fazer com que o Bing sirva resultados errados.|  
|<a name="location"></a>X-Search-Location|Cabeçalho de pedido opcional.<br /><br /> Uma lista separada por ponto e vírgula de pares de chaves/valores que descreve a localização geográfica do cliente. O Bing utiliza as informações de localização para determinar o comportamento do safesearch e para devolver conteúdos locais relevantes. Especifique o par chave/valor como \<key\> : \<value\> . As chaves seguintes são as chaves utilizadas para especificar a localização do utilizador.<br /><br /><ul><li>lat &mdash; A latitude da localização do cliente, em graus. Tem de ser igual ou superior a -90,0 e inferior ou igual a +90,0. Os valores negativos indicam latitudes no hemisfério sul e os positivos latitudes no hemisfério norte.<br /><br /></li><li>&mdash;longa a longitude da localização do cliente, em graus. Tem de ser igual ou superior a -180.0 e inferior ou igual a +180.0. Os valores negativos indicam longitudes a ocidente e os positivos a oriente.<br /><br /></li><li>re &mdash; O raio, em metros, que especifica a precisão horizontal das coordenadas. Transmita o valor que o serviço de localização do dispositivo devolve. Os valores mais comuns podem ser 22 m para GPS/Wi-Fi, 380 m para triangulação de antenas e 18 000 m para pesquisa de IP inversa.<br /><br /></li><li>ts &mdash; O calendário UTC UNIX de quando o cliente estava no local. (o carimbo de data/hora é o número de segundos desde 1 de janeiro de 1970.)<br /><br /></li><li>head&mdash;Optional. O cabeçalho relativo do cliente ou a direção de deslocamento. Especifique a direção de deslocamento em graus, de 0 a 360, a contar no sentido dos ponteiros do relógio em relação ao norte verdadeiro. Especifique esta chave apenas se a chave `sp` não for zero.<br /><br /></li><li>sp &mdash; A velocidade horizontal (velocidade), em metros por segundo, que o dispositivo cliente está a viajar.<br /><br /></li><li>alt &mdash; A altitude do dispositivo cliente, em metros.<br /><br /></li><li>are&mdash;Optional. O raio, em metros, que especifica a exatidão vertical das coordenadas. Raio de raio está a 50 km. Especifique esta chave apenas se especificar a chave `alt`.<br /><br /></li></ul> **NOTA:** Embora estas chaves sejam opcionais, quanto mais informação fornecer, mais precisas são os resultados da localização.<br /><br /> **NOTA:** É-lhe encorajado a especificar sempre a localização geográfica do utilizador. Especificar a localização é particularmente importante se o endereço IP do cliente não refletir, com precisão, a localização física do utilizador (por exemplo, se este utilizar VPN). Para obter os melhores resultados, deve incluir este cabeçalho e o cabeçalho X-MSEdge-ClientIP, mas deve incluir este cabeçalho, no mínimo.|

> [!NOTE] 
> Não se esqueça de que os Termos de Utilização requerem conformidade com toda a legislação aplicável, incluindo a relativa à utilização destes cabeçalhos. Por exemplo, em determinadas jurisdições, como a Europa, há requisitos para obter o consentimento do utilizador antes de colocar certos tipos de dispositivos de monitorização nos dispositivos dos utilizadores.
  

## <a name="query-parameters"></a>Parâmetros de consulta  
O pedido pode incluir os seguintes parâmetros de consulta. Consulte a coluna requerida para obter os parâmetros necessários. Tem de codificar os parâmetros de consulta.  
  
  
|Name|Valor|Tipo|Necessário|  
|----------|-----------|----------|--------------|
|<a name="count"></a>contar|O número de resultados a devolver, a começar pelo índice especificado pelo `offset` parâmetro.|String|No|   
|<a name="localCategories"></a>localCategorias|Lista de opções que definem pesquisa por categoria de negócio.  Consulte [a pesquisa das Categorias de Negócios Locais](local-categories.md)|String|No|  
|<a name="mkt"></a>mkt|O mercado de onde os resultados provêm. <br /><br />Para obter uma lista de possíveis valores de mercado, consulte códigos de mercado.<br /><br /> **NOTA:** A API local de pesquisa de negócios suporta apenas o mercado e a linguagem.<br /><br />|String|Yes|
|<a name="offset"></a>compensar|O índice para iniciar os resultados especificados pelo `count` parâmetro.|Número inteiro|No|  
|<a name="query"></a>q|O termo de pesquisa do utilizador.|String|No|  
|<a name="responseformat"></a>respostaFormat|O tipo de mídia a usar para a resposta. Seguem-se os possíveis valores caso-insensíveis.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> O padrão é JSON. Para obter informações sobre os objetos JSON que a resposta contém, consulte [Objetos de Resposta](#response-objects).<br /><br />  Se especificar JsonLd, o corpo de resposta inclui objetos JSON-LD que contêm os resultados da pesquisa. Para obter informações sobre o JSON-LD, consulte [JSON-LD](https://json-ld.org/).|String|No|  
|<a name="safesearch"></a>safeSearch|É um filtro utilizado para filtrar conteúdo para adultos. Os valores seguintes são os possíveis valores do filtro, não sensíveis a maiúsculas e minúsculas.<br /><ul><li>Off &mdash; Devolver páginas web com texto adulto, imagens ou vídeos.<br /><br/></li><li>Páginas de retorno moderado &mdash; com texto adulto, mas não imagens ou vídeos adultos.<br /><br/></li><li>Estrita &mdash; Não devolva páginas web com texto adulto, imagens ou vídeos.</li></ul><br /> A predefinição é Moderado.<br /><br /> **NOTA:** Se o pedido vier de um mercado que a política de adultos de Bing requer que `safeSearch` seja definido como Strict, Bing ignora o `safeSearch` valor e usa Strict.<br/><br/>**NOTA:** se utilizar o operador de consulta `site:`, existe a possibilidade de a resposta poder conter conteúdos para adultos, independentemente da definição do parâmetro de consulta `safeSearch`. Utilize `site:` apenas se tiver conhecimento do conteúdo do site e se o seu cenário suportar a possibilidade de conteúdos para adultos. |String|No|  
|<a name="setlang"></a>setLang|O idioma a utilizar nas cadeias da interface de utilizador. Especifique o idioma com o código de idioma ISO 639-1 de duas letras. Por exemplo, o código de idioma de inglês é EN. A predefinição é EN (inglês).<br /><br /> Embora seja opcional, deve indicar sempre o idioma. Normalmente, `setLang` é definido como o mesmo idioma especificado por `mkt`, a não ser que o utilizador queira que as cadeias da interface de utilizador sejam apresentadas noutro idioma.<br /><br /> Este parâmetro e o cabeçalho [Accept-Language](#acceptlanguage) são mutuamente exclusivos &mdash; não os especifique a ambos.<br /><br /> As cadeias da interface de utilizador são cadeias utilizadas como etiquetas na interface de utilizador. Os objetos de resposta JSON têm poucas cadeias de interface de utilizador. Além disso, todas as ligações para propriedades de Bing.com nos objetos de resposta aplicam o idioma especificado.|String|No| 


## <a name="response-objects"></a>Objetos de resposta  
Seguem-se os objetos de resposta JSON que a resposta pode incluir. Se o pedido for bem sucedido, o objeto de nível superior na resposta é o objeto [SearchResponse.](#searchresponse) Se o pedido falhar, o objeto de nível superior é o objeto [ErrorResponse.](#errorresponse)


|Objeto|Description|  
|------------|-----------------|  
|[Lugar](#place)|Define informações sobre um negócio local, como um restaurante ou hotel.|  

  
### <a name="error"></a>Erro  
Define o erro que ocorreu.  
  
|Elemento|Descrição|Tipo|  
|-------------|-----------------|----------|  
|<a name="error-code"></a>código|O código de erro que identifica a categoria de erro. Para obter uma lista de códigos possíveis, consulte [Códigos de Erro](#error-codes).|String|  
|<a name="error-message"></a>Mensagem|Descrição do erro.|String|  
|<a name="error-moredetails"></a>moreDetails|Uma descrição que fornece informações adicionais sobre o erro.|String|  
|<a name="error-parameter"></a>parâmetro|O parâmetro de consulta no pedido que causou o erro.|String|  
|<a name="error-subcode"></a>subCódigo|O código de erro que identifica o erro. Por exemplo, se `code` for InválidaRequest, `subCode` pode ser ParâmetroInvalid ou ParameterInvalidValue. |String|  
|<a name="error-value"></a>valor|O valor do parâmetro de consulta que não era válido.|String|  
  

### <a name="errorresponse"></a>Resposta de erros  
O objeto de nível superior que a resposta inclui quando o pedido falha.  
  
|Name|Valor|Tipo|  
|----------|-----------|----------|  
|_type|Tipo dica.|String|  
|<a name="errors"></a>erros|Uma lista de erros que descrevem as razões pelas quais o pedido falhou.|[Erro](#error)[]|  

  
  
### <a name="license"></a>Licença  
Define a licença segundo a qual o texto ou a foto podem ser utilizados.  
  
|Name|Valor|Tipo|  
|----------|-----------|----------|  
|name|O nome da licença.|String|  
|url|O URL para um site onde o utilizador pode obter mais informações sobre a licença.<br /><br /> Use o nome e o URL para criar uma hiperligação.|String|  


### <a name="link"></a>Ligação  
Define os componentes de uma hiperligação.  
  
|Name|Valor|Tipo|  
|----------|-----------|----------|  
|_type|Tipo dica.|String|  
|texto|O texto do visor.|String|  
|url|UMA URL. Utilize o URL e o texto de exibição para criar uma hiperligação.|String|  
  


  
### <a name="organization"></a>Organização  
Define uma editora.  
  
Note que um editor pode fornecer o seu nome ou o seu website ou ambos.  
  
|Name|Valor|Tipo|  
|----------|-----------|----------|  
|name|O nome da editora.|String|  
|url|O URL para o site da editora.<br /><br /> Note que o editor não pode fornecer um site.|String|  
  
  

### <a name="place"></a>Lugar  
Define informações sobre um negócio local, como um restaurante ou hotel.  
  
|Name|Valor|Tipo|  
|----------|-----------|----------|  
|_type|Dica de tipo, que pode ser definida para uma das seguintes:<br /><br /><ul><li>Hotel</li><li>Negócios Locais<br /></li><li>Restaurante</ul><li>|String|  
|address|O endereço postal do local onde se encontra a entidade.|PostalAddress|  
|entidadePresentaçãoInfo|Informações adicionais sobre a entidade, tais como indícios que pode usar para determinar o tipo da entidade. Por exemplo, seja um restaurante ou hotel. O `entityScenario` campo está definido para ListItem.|EntityPresentationInfo|  
|name|O nome da entidade.|String|  
|telefone|O número de telefone da entidade.|String|  
|url|O URL para o site da entidade.<br /><br /> Utilize este URL juntamente com o nome da entidade para criar uma hiperligação que quando clicada leva o utilizador ao website da entidade.|String|  
|webSearchUrl|O URL do resultado de pesquisa de Bing para este lugar.|String| 
  
  
### <a name="querycontext"></a>QueryContexto  
Define o contexto de consulta que Bing usou para o pedido.  
  
|Elemento|Descrição|Tipo|  
|-------------|-----------------|----------|  
|adultoIntente|Um valor Boolean que indica se a consulta especificada tem intenção adulta. O valor é **verdadeiro** se a consulta tiver intenção adulta; caso contrário, **falso** .|Booleano|  
|alterationOverrideQuery|A cadeia de consulta para usar para forçar Bing a usar a corda original. Por exemplo, se a cadeia de consulta estiver *a salar para baixo,* a cadeia de consulta de sobreposição será *+saling downwind* . Lembre-se de codificar a cadeia de consulta que resulta em *%2Bsaling+downwind* .<br /><br /> Este campo só está incluído se a cadeia de consulta original contiver um erro ortográfico.|String|  
|alteradoQuery|A cadeia de consulta utilizada por Bing para realizar a consulta. Bing usa a cadeia de consulta alterada se a cadeia de consulta original contiver erros ortográficos. Por exemplo, se a cadeia de consulta `saling downwind` for, a cadeia de consulta alterada será `sailing downwind` .<br /><br /> Este campo só está incluído se a cadeia de consulta original contiver um erro ortográfico.|String|  
|askUserForLocation|Um valor Boolean que indica se Bing requer a localização do utilizador para fornecer resultados precisos. Se especificar a localização do utilizador utilizando os cabeçalhos [X-MSEdge-ClientIP](#clientip) e [X-Search-Location,](#location) pode ignorar este campo.<br /><br /> Para consultas conscientes da localização, como "o tempo de hoje" ou "restaurantes perto de mim" que precisam da localização do utilizador para fornecer resultados precisos, este campo está definido como **verdadeiro.**<br /><br /> Para consultas de localização que incluam a localização (por exemplo, "Seattle weather"), este campo está definido como **falso** . Este campo também é definido como **falso** para consultas que não estão cientes da localização, como os "best sellers".|Booleano|  
|OriginalQuery|O string de consulta conforme especificado no pedido.|String|  

### <a name="identifiable"></a>Identificável

|Name|Valor|Tipo|  
|-------------|-----------------|----------|
|ID|Um identificador de recursos|String|
 
### <a name="rankinggroup"></a>RankingGroup
Define um grupo de resultados de pesquisa, como mainline.

|Name|Valor|Tipo|  
|-------------|-----------------|----------|
|itens|Uma lista de resultados de pesquisa a apresentar no grupo.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Define um item de resultado de pesquisa para visualizar.

|Name|Valor|Tipo|  
|-------------|-----------------|----------|
|resultadoIndex|Um índice baseado em zero do item na resposta a exibir. Se o artigo não incluir este campo, apresente todos os itens na resposta. Por exemplo, exiba todos os artigos noticiosos na resposta notícia.|Número inteiro|
|answerType|A resposta que contém o item a exibir. Por exemplo, notícias.<br /><br />Utilize o tipo para encontrar a resposta no objeto SearchResponse. O tipo é o nome de um campo SearchResponse.<br /><br /> No entanto, utilize o tipo de resposta apenas se este objeto incluir o campo de valor; caso contrário, ignorá-lo.|String|
|textualIndex|O índice da resposta em textualAnswers para exibir.| Inteiro não assinado|
|valor|O ID que identifica uma resposta a exibir ou um item de resposta a exibir. Se o ID identificar uma resposta, apresente todos os itens da resposta.|Identificável|

### <a name="rankingresponse"></a>RankingResponse  
Define onde no conteúdo da página de resultados de pesquisa deve ser colocado e em que ordem.  
  
|Name|Valor|  
|----------|-----------|  
|<a name="ranking-mainline"></a>mainline|Os resultados da pesquisa a exibir na linha principal.|  
|<a name="ranking-pole"></a>polo|Os resultados da pesquisa que devem ser proporcionados ao tratamento mais visível (por exemplo, exibidos acima da linha principal e da barra lateral).|  
|<a name="ranking-sidebar"></a>barra lateral|Os resultados da pesquisa a exibir na barra lateral.| 

### <a name="searchresponse"></a>PesquisaResponse  
Define o objeto de nível superior que a resposta inclui quando o pedido é bem sucedido.  
  
Note que se o serviço suspeitar de um ataque de negação de serviço, o pedido será bem sucedido (código de estado HTTP é de 200 OK); no entanto, o corpo da resposta estará vazio.  
  
|Name|Valor|Tipo|  
|----------|-----------|----------|  
|_type|Sugestão do tipo, que está definida para SearchResponse.|String|  
|realiza|Uma lista de entidades relevantes para a consulta de pesquisa.|Objeto JSON|  
|consultaContexto|Um objeto que contém a cadeia de consulta que Bing usou para o pedido.<br /><br /> Este objeto contém a cadeia de consulta tal como inserida pelo utilizador. Também pode conter uma cadeia de consulta alterada que Bing usou para a consulta se a cadeia de consulta contivesse um erro ortográfico.|[QueryContexto](#querycontext)|  


## <a name="error-codes"></a>Códigos de erro

Seguem-se os possíveis códigos de estado HTTP que um pedido devolve.  
  
|Código de Estado|Description|  
|-----------------|-----------------|  
|200|Com êxito.|  
|400|Um dos parâmetros de consulta está em falta ou não é válido.|  
|401|A chave de subscrição está em falta ou não é válida.|  
|403|O utilizador é autenticado (por exemplo, utilizaram uma chave de subscrição válida) mas não têm permissão para o recurso solicitado.<br /><br /> Bing também pode devolver este estatuto se o chamador exceder as suas consultas por quota mensal.|  
|410|O pedido utilizado HTTP em vez do protocolo HTTPS. HTTPS é o único protocolo suportado.|  
|429|O chamador excedeu as suas consultas por segundo contingente.|  
|500|Erro de servidor inesperado.|

Se o pedido falhar, a resposta contém um objeto [ErrorResponse,](#errorresponse) que contém uma lista de objetos [De erro](#error) que descrevem o que causou o erro. Se o erro estiver relacionado com um parâmetro, o `parameter` campo identifica o parâmetro que é o problema. E se o erro estiver relacionado com um valor de parâmetro, o `value` campo identifica o valor que não é válido.

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

Seguem-se os possíveis valores de código de erro e de sub-erro.

|Código|SubCódes|Description
|-|-|-
|ServerError|InesperadoError<br/>Recurso<br/>Não ÉDopliizado|O código de estado HTTP é 500.
|InáduloRequest|ParâmetroSMissing<br/>ParâmetroInvalvalue<br/>HttpNotAllowed<br/>Bloqueado|Bing devolve InvalidRequest sempre que qualquer parte do pedido não seja válida. Por exemplo, falta um parâmetro necessário ou um valor de parâmetro não é válido.<br/><br/>Se o erro for ParameterMissing ou ParameterInvalidValue, o código de estado HTTP é 400.<br/><br/>Se utilizar o protocolo HTTP em vez de HTTPS, bing devolve HttpNotAllowed e o código de estado HTTP é 410.
|TaxaLimitExceed|Sem sub-códigos|Bing devoluções TaxaLimitExceed sempre que exceder as suas consultas por segundo (QPS) ou consultas por mês (QPM).<br/><br/>Se exceder o QPS, bing devolve o código de estado HTTP 429, e se exceder QPM, Bing devolve 403.
|InvalidAuthorization|AutorizaçãoSDissing<br/>AutorizaçãoSAundação|Bing devolve invalidAuthorization quando Bing não pode autenticar o chamador. Por exemplo, falta o `Ocp-Apim-Subscription-Key` cabeçalho ou a chave de subscrição não é válida.<br/><br/>A redundância ocorre se especificar mais de um método de autenticação.<br/><br/>Se o erro for invalidauthorization, o código de estado HTTP é 401.
|Authorização insuficiente|AutorizaçãoDisabled<br/>AutorizaçãoSDusa|Bing devolve aautorização insuficiente quando o chamador não tem permissões para aceder ao recurso. Isto pode ocorrer se a chave de subscrição tiver sido desativada ou tiver expirado. <br/><br/>Se o erro for insuficiente, o código de estado HTTP é 403.

## <a name="next-steps"></a>Passos seguintes
- [Pesquisa de negócios local quickstart](quickstarts/local-quickstart.md)
- [Pesquisa de negócios locais Java quickstart](quickstarts/local-search-java-quickstart.md)
- [Início rápido do nó de pesquisa de negócios local](quickstarts/local-search-node-quickstart.md)
- [Pesquisa de negócios locais Python quickstart](quickstarts/local-search-python-quickstart.md)
