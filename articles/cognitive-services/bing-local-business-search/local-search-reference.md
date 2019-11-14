---
title: Referência do Bing local da API de pesquisa de negócios v7
titleSuffix: Azure Cognitive Services
description: Este artigo fornece detalhes técnicos sobre os objetos de resposta e os parâmetros de consulta e cabeçalhos que afetam os resultados da pesquisa.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: db764a73aa1bb18ef2fc0f8f6e5ffe8fd60d388c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075688"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Referência do Bing local da API de pesquisa de negócios v7

A API de pesquisa de negócios local envia uma consulta de pesquisa ao Bing para obter resultados que incluem restaurantes, hotéis ou outras empresas locais. Para locais, a consulta pode especificar o nome do negócio local ou uma categoria (por exemplo, restaurantes ao meu redor). Os resultados de entidades incluem pessoas, locais ou coisas. O local neste contexto é entidades de negócios, Estados, países/regiões, etc.  

Esta seção fornece detalhes técnicos sobre os objetos de resposta e os parâmetros de consulta e cabeçalhos que afetam os resultados da pesquisa. Para obter exemplos que mostram como fazer solicitações, consulte [início rápido da C# pesquisa de negócios local](quickstarts/local-quickstart.md) ou [início rápido do Java da pesquisa de negócios local](quickstarts/local-search-java-quickstart.md). 
  
Para obter informações sobre os cabeçalhos que as solicitações devem incluir, consulte [cabeçalhos](#headers).  
  
Para obter informações sobre parâmetros de consulta que as solicitações devem incluir, consulte [parâmetros de consulta](#query-parameters).  
  
Para obter informações sobre os objetos JSON que a resposta inclui, consulte [objetos de resposta](#response-objects).

Para obter informações sobre o uso permitido e a exibição de resultados, consulte [requisitos de uso e exibição](use-display-requirements.md).


  
## <a name="endpoint"></a>Ponto Final  
Para solicitar resultados de negócios locais, envie uma solicitação GET para: 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
O pedido tem de utilizar o protocolo HTTPS.  
  
> [!NOTE]
> O comprimento máximo da URL é de 2.048 caracteres. Para garantir que o comprimento da URL não exceda o limite, o comprimento máximo dos parâmetros de consulta deve ter menos de 1.500 caracteres. Se a URL exceder 2.048 caracteres, o servidor retornará 404 não encontrado.  
  
  
## <a name="headers"></a>Cabeçalhos  
A seguir estão os cabeçalhos que uma solicitação e uma resposta podem incluir.  
  
|Cabeçalho|Descrição|  
|------------|-----------------|  
|aceitar|Cabeçalho de pedido opcional.<br /><br /> O tipo de mídia padrão é Application/JSON. Para especificar que a resposta use [JSON-LD](https://json-ld.org/), defina o cabeçalho Accept como Application/LD + JSON.|  
|<a name="acceptlanguage" />Accept-Language|Cabeçalho de pedido opcional.<br /><br /> Uma lista separada por vírgulas dos idiomas que vão ser utilizados nas cadeias da interface de utilizador. A lista está em ordem decrescente de preferência. Para obter mais informações, incluindo o formato esperado, veja [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Este cabeçalho e o parâmetro de consulta [setLang](#setlang) são mutuamente exclusivos &mdash; não os especifique a ambos.<br /><br /> Se você definir esse cabeçalho, também deverá especificar o parâmetro de consulta CC. Para determinar o mercado do qual devolver resultados, o Bing utiliza o primeiro idioma suportado que encontra na lista e combina-o com o valor do parâmetro `cc`. Se a lista não incluir um idioma suportado, o Bing encontra o idioma e o mercado mais parecidos que suportem o pedido ou utiliza um mercado agregado ou predefinido para devolver os resultados. Para determinar o mercado que o Bing utiliza, veja o cabeçalho BingAPIs-Market.<br /><br /> Utilize este cabeçalho o parâmetro de consulta `cc` apenas se especificar vários idiomas. Caso contrário, utilize os parâmetros de consulta [mkt](#mkt) e [setLang](#setlang).<br /><br /> As cadeias da interface de utilizador são cadeias utilizadas como etiquetas na interface de utilizador. Os objetos de resposta JSON têm poucas cadeias de interface de utilizador. Todas as ligações para propriedades de Bing.com nos objetos de resposta aplicam o idioma especificado.|  
|<a name="market" />BingAPIs-Market|O cabeçalho da resposta.<br /><br /> O mercado que o pedido utiliza. O formato é \<languageCode\>-\<códigodeidioma\>. Por exemplo: en-US.|  
|<a name="traceid" />BingAPIs-TraceId|O cabeçalho da resposta.<br /><br /> O ID da entrada de registo que contém os detalhes do pedido. Quando ocorrer um erro, capture este ID. Se não conseguir determinar nem resolver o problema, inclua o ID juntamente com as outras informações que enviar à equipa de suporte.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Cabeçalho de pedido obrigatório.<br /><br /> A chave de subscrição que recebeu quando se inscreveu neste serviço nos [Serviços Cognitivos](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Cabeçalho de pedido opcional<br /><br /> Por predefinição, o Bing devolve conteúdo em cache, se disponível. Para impedir que o Bing devolva conteúdo em cache, defina o cabeçalho Pragma como no-cache (por exemplo, Pragma: no-cache).
|<a name="useragent" />User-Agent|Cabeçalho de pedido opcional.<br /><br /> O agente de utilizador que origina o pedido. O Bing utiliza o agente de utilizador para proporcionar uma experiência otimizada aos utilizadores de dispositivos móveis. Embora seja opcional, recomenda-se que especifique sempre este cabeçalho.<br /><br /> O agente de utilizador deve ser a mesma cadeia que qualquer browser geralmente utilizado envia. Para obter informações sobre os agentes de utilizador, veja [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Pode ver exemplos de cadeias de agente de utilizador abaixo:<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Cabeçalho de pedido e resposta opcional.<br /><br /> O Bing utiliza este cabeçalho para proporcionar um comportamento consistente nas chamada à API do Bing aos utilizadores. O Bing lança, muitas vezes, funcionalidades e melhorias novas e utiliza o ID de cliente como uma chave para atribuir tráfego aos diferentes lançamentos. Se não utilizar o mesmo ID de cliente para um utilizador em vários pedidos, o Bing poderá atribuir esse utilizador a vários lançamentos sobrepostos. A atribuição a vários lançamentos sobrepostos pode provocar uma experiência de utilizador inconsistente. Por exemplo, se a atribuição de lançamento do segundo pedido for diferente da do primeiro, a experiência poderá ser inesperada. Além disso, o Bing pode utilizar o ID de cliente para personalizar os resultados da Web o histórico de pesquisas desse ID, proporcionando uma experiência mais rica ao utilizador.<br /><br /> O Bing também utiliza este cabeçalho para ajudar a melhorar as classificações dos resultados ao analisar a atividade gerada por um ID de cliente. As melhorias à relevância ajudam a que as APIs do Bing devolvam resultados com melhor qualidade, o que, por sua vez, origina taxas de cliques mais altas para o consumidor da API.<br /><br /> **IMPORTANTE:** embora seja opcional, deve ver este cabeçalho como obrigatório. Persistir o ID de cliente em vários pedidos para a mesma combinação de utilizador final e dispositivo permite 1) que o consumidor da API receba uma experiência de utilizador consistente e 2) taxas de cliques mais altas, através de resultados com melhor qualidade das APIs do Bing.<br /><br /> As regras abaixo são as regras de utilização básicas aplicáveis a este cabeçalho.<br /><ul><li>Cada utilizador que utilize a sua aplicação no dispositivo tem de ter um ID de cliente exclusivo e gerado pelo Bing.<br /><br/>Se não incluir este cabeçalho no pedido, o Bing gera um ID e devolve-o no cabeçalho da resposta X-MSEdge-ClientID. O único momento em que NÃO deve incluir este cabeçalho num pedido é na primeira vez que o utilizador utiliza a sua aplicação nesse dispositivo.<br /><br/></li><li>Utilize o ID de cliente para cada pedido da API do Bing que a sua aplicação fizer para este utilizador no dispositivo.<br /><br/></li><li>**Atenção:** Você deve garantir que essa ID do cliente não seja vinculável a nenhuma informação da conta de usuário do authenticatable.</li><br/><li>Persista o ID de cliente. Para persistir o ID numa aplicação de browser, utilize um cookie HTTP persistente para garantir que esse ID é utilizado em todas as sessões. Não utilize um cookie de sessão. Relativamente a outras aplicações, como aplicações móveis, utilize o armazenamento persistente do dispositivo para persistir o ID.<br /><br/>Da próxima vez que o utilizador utilizar a aplicação nesse dispositivo, recebe o ID de cliente que foi persistido.</li></ul><br /> **NOTA:** as respostas do Bing podem incluir ou não este cabeçalho. Se a resposta incluir este cabeçalho, capture o ID de cliente e utilize-o em todos os pedidos do Bing subsequentes do utilizador nesse dispositivo.<br /><br /> **NOTA:** se incluir X-MSEdge-ClientID, não pode incluir cookies no pedido.|  
|<a name="clientip" />X-MSEdge-ClientIP|Cabeçalho de pedido opcional.<br /><br /> O endereço IPv4 ou IPv6 do dispositivo cliente. O endereço IP é utilizado para detetar a localização do utilizador. O Bing utiliza as informações de localização para determinar o comportamento do safesearch.<br /><br /> **NOTA:** embora seja opcional, recomenda-se que especifique sempre este cabeçalho e o cabeçalho X-Search-Location.<br /><br /> Não ofusque a morada (por exemplo, ao alterar o último octeto para 0). Ofuscar a morada faz com que a localização não esteja perto da localização efetiva do dispositivo, o que pode fazer com que o Bing sirva resultados errados.|  
|<a name="location" />X-Search-Location|Cabeçalho de pedido opcional.<br /><br /> Uma lista separada por ponto e vírgula de pares de chaves/valores que descreve a localização geográfica do cliente. O Bing utiliza as informações de localização para determinar o comportamento do safesearch e para devolver conteúdos locais relevantes. Indique o par chave/valor como \<chave\>:\<valor\>. As chaves seguintes são as chaves utilizadas para especificar a localização do utilizador.<br /><br /><ul><li>Lat&mdash;a latitude do local do cliente, em graus. Tem de ser igual ou superior a -90,0 e inferior ou igual a +90,0. Os valores negativos indicam latitudes no hemisfério sul e os positivos latitudes no hemisfério norte.<br /><br /></li><li>longa&mdash;a longitude do local do cliente, em graus. Tem de ser igual ou superior a -180.0 e inferior ou igual a +180.0. Os valores negativos indicam longitudes a ocidente e os positivos a oriente.<br /><br /></li><li>Re&mdash; o raio, em metros, que especifica a precisão horizontal das coordenadas. Transmita o valor que o serviço de localização do dispositivo devolve. Os valores mais comuns podem ser 22 m para GPS/Wi-Fi, 380 m para triangulação de antenas e 18 000 m para pesquisa de IP inversa.<br /><br /></li><li>TS&mdash; carimbo de data/hora do UNIX UTC de quando o cliente estava no local. (o carimbo de data/hora é o número de segundos desde 1 de janeiro de 1970.)<br /><br /></li><li>head&mdash;Optional. O cabeçalho relativo do cliente ou a direção de deslocamento. Especifique a direção de deslocamento em graus, de 0 a 360, a contar no sentido dos ponteiros do relógio em relação ao norte verdadeiro. Especifique esta chave apenas se a chave `sp` não for zero.<br /><br /></li><li>o SP&mdash; a velocidade horizontal (velocidade), em metros por segundo, que o dispositivo cliente está viajando.<br /><br /></li><li>Alt&mdash; a altitude do dispositivo cliente, em metros.<br /><br /></li><li>are&mdash;Optional. O raio, em metros, que especifica a exatidão vertical das coordenadas. O padrão RADIUS é de 50 quilômetros. Especifique esta chave apenas se especificar a chave `alt`.<br /><br /></li></ul> **Observação:** Embora essas chaves sejam opcionais, quanto mais informações você fornecer, mais precisas serão os resultados da localização.<br /><br /> **Observação:** É recomendável sempre especificar a localização geográfica do usuário. Especificar a localização é particularmente importante se o endereço IP do cliente não refletir, com precisão, a localização física do utilizador (por exemplo, se este utilizar VPN). Para obter os melhores resultados, deve incluir este cabeçalho e o cabeçalho X-MSEdge-ClientIP, mas deve incluir este cabeçalho, no mínimo.|

> [!NOTE] 
> Não se esqueça de que os Termos de Utilização requerem conformidade com toda a legislação aplicável, incluindo a relativa à utilização destes cabeçalhos. Por exemplo, em determinadas jurisdições, como a Europa, há requisitos para obter o consentimento do utilizador antes de colocar certos tipos de dispositivos de monitorização nos dispositivos dos utilizadores.
  

## <a name="query-parameters"></a>Parâmetros de consulta  
A solicitação pode incluir os seguintes parâmetros de consulta. Consulte a coluna necessária para os parâmetros necessários. Você deve codificar a URL dos parâmetros de consulta.  
  
  
|Nome|Valor|Tipo|Necessário|  
|----------|-----------|----------|--------------|
|contagem de <a name="count" />|O número de resultados a serem retornados, começando com o índice especificado pelo parâmetro `offset`.|Cadeia|Não|   
|<a name="localCategories" />localCategories|Lista de opções que definem a pesquisa por categoria de negócios.  Consulte [pesquisa de categorias de negócios locais](local-categories.md)|Cadeia|Não|  
|<a name="mkt" />mkt|O mercado de onde os resultados provêm. <br /><br />Para obter uma lista de possíveis valores de mercado, consulte códigos de mercado.<br /><br /> **Observação:** Atualmente, a API de pesquisa de negócios local dá suporte apenas ao mercado e à linguagem en-US.<br /><br />|Cadeia|Sim|
|deslocamento de <a name="offset"/>|O índice para iniciar os resultados especificados pelo parâmetro `count`.|Número inteiro|Não|  
|<a name="query" />q|O termo de pesquisa do usuário.|Cadeia|Não|  
|<a name="responseformat" />responseFormat|O tipo de mídia a ser usado para a resposta. A seguir estão os valores possíveis que não diferenciam maiúsculas de minúsculas.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> O padrão é JSON. Para obter informações sobre os objetos JSON que a resposta contém, consulte [objetos de resposta](#response-objects).<br /><br />  Se você especificar JsonLd, o corpo da resposta incluirá objetos JSON-LD que contêm os resultados da pesquisa. Para obter informações sobre o JSON-LD, consulte [JSON-LD](https://json-ld.org/).|Cadeia|Não|  
|<a name="safesearch" />safeSearch|É um filtro utilizado para filtrar conteúdo para adultos. Os valores seguintes são os possíveis valores do filtro, não sensíveis a maiúsculas e minúsculas.<br /><ul><li>Desativar&mdash;retornar páginas da Web com texto adulto, imagens ou vídeos.<br /><br/></li><li>O&mdash;moderado retorna páginas da Web com texto adulto, mas não imagens ou vídeos adultos.<br /><br/></li><li>Os&mdash;estritos não retornam páginas da Web com texto adulto, imagens ou vídeos.</li></ul><br /> A predefinição é Moderado.<br /><br /> **Observação:** Se a solicitação vier de um mercado que a política de adulto do Bing exija que `safeSearch` seja definida como estrita, o Bing ignorará o valor de `safeSearch` e usará estrito.<br/><br/>**NOTA:** se utilizar o operador de consulta `site:`, existe a possibilidade de a resposta poder conter conteúdos para adultos, independentemente da definição do parâmetro de consulta `safeSearch`. Utilize `site:` apenas se tiver conhecimento do conteúdo do site e se o seu cenário suportar a possibilidade de conteúdos para adultos. |Cadeia|Não|  
|<a name="setlang" />setLang|O idioma a utilizar nas cadeias da interface de utilizador. Especifique o idioma com o código de idioma ISO 639-1 de duas letras. Por exemplo, o código de idioma de inglês é EN. A predefinição é EN (inglês).<br /><br /> Embora seja opcional, deve indicar sempre o idioma. Normalmente, `setLang` é definido como o mesmo idioma especificado por `mkt`, a não ser que o utilizador queira que as cadeias da interface de utilizador sejam apresentadas noutro idioma.<br /><br /> Este parâmetro e o cabeçalho [Accept-Language](#acceptlanguage) são mutuamente exclusivos &mdash; não os especifique a ambos.<br /><br /> As cadeias da interface de utilizador são cadeias utilizadas como etiquetas na interface de utilizador. Os objetos de resposta JSON têm poucas cadeias de interface de utilizador. Além disso, todas as ligações para propriedades de Bing.com nos objetos de resposta aplicam o idioma especificado.|Cadeia|Não| 


## <a name="response-objects"></a>Objetos de resposta  
A seguir estão os objetos de resposta JSON que a resposta pode incluir. Se a solicitação for realizada com sucesso, o objeto de nível superior na resposta será o objeto [SearchResponse](#searchresponse) . Se a solicitação falhar, o objeto de nível superior será o objeto [ErrorResponse](#errorresponse) .


|Objeto|Descrição|  
|------------|-----------------|  
|[Espaço](#place)|Define informações sobre um negócio local, como um restaurante ou Hotel.|  

  
### <a name="error"></a>Erro  
Define o erro que ocorreu.  
  
|Elemento|Descrição|Tipo|  
|-------------|-----------------|----------|  
|código de <a name="error-code" />|O código de erro que identifica a categoria de erro. Para obter uma lista de códigos possíveis, consulte [códigos de erro](#error-codes).|Cadeia|  
|<a name="error-message" />mensagem|Uma descrição do erro.|Cadeia|  
|<a name="error-moredetails" />moreDetails|Uma descrição que fornece informações adicionais sobre o erro.|Cadeia|  
|<a name="error-parameter" />parâmetro|O parâmetro de consulta na solicitação que causou o erro.|Cadeia|  
|subcódigo <a name="error-subcode" />|O código de erro que identifica o erro. Por exemplo, se `code` for InvalidRequest, `subCode` poderá ser ParameterInvalid ou ParameterInvalidValue. |Cadeia|  
|valor <a name="error-value" />|O valor do parâmetro de consulta que não era válido.|Cadeia|  
  

### <a name="errorresponse"></a>ErrorResponse  
O objeto de nível superior que a resposta inclui quando a solicitação falha.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|_type|Dica de tipo.|Cadeia|  
|erros de <a name="errors" />|Uma lista de erros que descrevem os motivos pelos quais a solicitação falhou.|[Erro](#error)[]|  

  
  
### <a name="license"></a>Licença  
Define a licença sob a qual o texto ou a foto podem ser usados.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|nome|O nome da licença.|Cadeia|  
|url|A URL para um site em que o usuário pode obter mais informações sobre a licença.<br /><br /> Use o nome e a URL para criar um hiperlink.|Cadeia|  


### <a name="link"></a>Ligação  
Define os componentes de um hiperlink.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|_type|Dica de tipo.|Cadeia|  
|texto|O texto de exibição.|Cadeia|  
|url|UMA URL. Use a URL e o texto de exibição para criar um hiperlink.|Cadeia|  
  


  
### <a name="organization"></a>Organização  
Define um Publicador.  
  
Observe que um Publicador pode fornecer seu nome ou seu site ou ambos.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|nome|O nome do editor.|Cadeia|  
|url|A URL para o site do editor.<br /><br /> Observe que o Publicador pode não fornecer um site.|Cadeia|  
  
  

### <a name="place"></a>Local  
Define informações sobre um negócio local, como um restaurante ou Hotel.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|_type|Digite Hint, que pode ser definida como uma das seguintes opções:<br /><br /><ul><li>Hotel</li><li>LocalBusiness<br /></li><li>Restaurante</ul><li>|Cadeia|  
|corrigir|O endereço postal do qual a entidade está localizada.|PostalAddress|  
|entityPresentationInfo|Informações adicionais sobre a entidade, como dicas que você pode usar para determinar o tipo da entidade. Por exemplo, seja um restaurante ou Hotel. O campo `entityScenario` é definido como ListItem.|EntityPresentationInfo|  
|nome|O nome da entidade.|Cadeia|  
|Ligue|O número de telefone da entidade.|Cadeia|  
|url|A URL para o site da entidade.<br /><br /> Use essa URL junto com o nome da entidade para criar um hiperlink que, quando clicado, leva o usuário para o site da entidade.|Cadeia|  
|webSearchUrl|A URL para o resultado da pesquisa do Bing deste local.|Cadeia| 
  
  
### <a name="querycontext"></a>QueryContext  
Define o contexto de consulta que o Bing usou para a solicitação.  
  
|Elemento|Descrição|Tipo|  
|-------------|-----------------|----------|  
|adultIntent|Um valor booliano que indica se a consulta especificada tem uma intenção de adulto. O valor será **true** se a consulta tiver uma intenção de adulto; caso contrário, **false**.|Booleano|  
|alterationOverrideQuery|A cadeia de caracteres de consulta a ser usada para forçar o Bing a usar a cadeia de caracteres original. Por exemplo, se a cadeia de caracteres de consulta for *saling downwind*, a cadeia de caracteres de consulta de substituição será *+ saling downwind*. Lembre-se de codificar a cadeia de caracteres de consulta que resulta em *% 2Bsaling + downwind*.<br /><br /> Esse campo só será incluído se a cadeia de caracteres de consulta original contiver um erro ortográfico.|Cadeia|  
|alteredQuery|A cadeia de caracteres de consulta usada pelo Bing para executar a consulta. O Bing usa a cadeia de caracteres de consulta alterada se a cadeia de caracteres de consulta original contiver erros de ortografia. Por exemplo, se a cadeia de caracteres de consulta for `saling downwind`, a cadeia de caracteres de consulta alterada será `sailing downwind`.<br /><br /> Esse campo só será incluído se a cadeia de caracteres de consulta original contiver um erro ortográfico.|Cadeia|  
|askUserForLocation|Um valor booliano que indica se o Bing exige o local do usuário para fornecer resultados precisos. Se você especificou o local do usuário usando os cabeçalhos [x-MSEdge-clientip](#clientip) e [x-Search-Location](#location) , poderá ignorar esse campo.<br /><br /> Para consultas com reconhecimento de local, como "clima de hoje" ou "restaurantes ao meu redor" que precisam do local do usuário para fornecer resultados precisos, esse campo é definido como **true**.<br /><br /> Para consultas com reconhecimento de local que incluem o local (por exemplo, "Seattle Weather"), esse campo é definido como **false**. Esse campo também é definido como **false** para consultas que não têm reconhecimento de local, como "melhores vendedores".|Booleano|  
|originalQuery|A cadeia de caracteres de consulta conforme especificado na solicitação.|Cadeia|  

### <a name="identifiable"></a>Identificação

|Nome|Valor|Tipo|  
|-------------|-----------------|----------|
|ID|Um identificador de recurso|Cadeia|
 
### <a name="rankinggroup"></a>De classificação
Define um grupo de resultados de pesquisa, como a principal.

|Nome|Valor|Tipo|  
|-------------|-----------------|----------|
|items|Uma lista de resultados da pesquisa a serem exibidos no grupo.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Define um item de resultado de pesquisa a ser exibido.

|Nome|Valor|Tipo|  
|-------------|-----------------|----------|
|resultIndex|Um índice de base zero do item na resposta a ser exibido. Se o item não incluir esse campo, exiba todos os itens na resposta. Por exemplo, exiba todos os artigos de notícias na resposta de notícias.|Número inteiro|
|arquivo_de_resposta|A resposta que contém o item a ser exibido. Por exemplo, notícias.<br /><br />Use o tipo para localizar a resposta no objeto SearchResponse. O tipo é o nome de um campo SearchResponse.<br /><br /> No entanto, use o tipo de resposta somente se esse objeto incluir o campo valor; caso contrário, ignore-o.|Cadeia|
|textualIndex|O índice da resposta em textualAnswers a ser exibido.| Inteiro sem sinal|
|valor|A ID que identifica uma resposta a ser exibida ou um item de uma resposta a ser exibida. Se a ID identificar uma resposta, exiba todos os itens da resposta.|Identificação|

### <a name="rankingresponse"></a>RankingResponse  
Define onde o conteúdo da página de resultados da pesquisa deve ser colocado e em qual ordem.  
  
|Nome|Valor|  
|----------|-----------|  
|<a name="ranking-mainline" />principal|Os resultados da pesquisa a serem exibidos na principal.|  
|pólo de <a name="ranking-pole" />|Os resultados da pesquisa que devem ter o tratamento mais visível (por exemplo, exibido acima da principal e da barra lateral).|  
|barra lateral <a name="ranking-sidebar" />|Os resultados da pesquisa a serem exibidos na barra lateral.| 

### <a name="searchresponse"></a>SearchResponse  
Define o objeto de nível superior que a resposta inclui quando a solicitação é realizada com sucesso.  
  
Observe que, se o serviço suspeitar de um ataque de negação de serviço, a solicitação terá sucesso (o código de status HTTP é 200 OK); no entanto, o corpo da resposta estará vazio.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|_type|Digite Hint, que é definida como SearchResponse.|Cadeia|  
|limita|Uma lista de entidades que são relevantes para a consulta de pesquisa.|Objeto JSON|  
|queryContext|Um objeto que contém a cadeia de caracteres de consulta que o Bing usou para a solicitação.<br /><br /> Este objeto contém a cadeia de caracteres de consulta conforme digitado pelo usuário. Ele também pode conter uma cadeia de caracteres de consulta alterada que o Bing usou para a consulta se a cadeia de caracteres de consulta contivesse um erro ortográfico.|[QueryContext](#querycontext)|  


## <a name="error-codes"></a>Códigos de erro

A seguir estão os códigos de status HTTP possíveis que uma solicitação retorna.  
  
|Código de status|Descrição|  
|-----------------|-----------------|  
|200|Êxito.|  
|400|Um dos parâmetros de consulta está ausente ou não é válido.|  
|401|A chave de assinatura está ausente ou não é válida.|  
|403|O usuário é autenticado (por exemplo, ele usou uma chave de assinatura válida), mas não tem permissão para o recurso solicitado.<br /><br /> O Bing também poderá retornar esse status se o chamador exceder a cota de consultas por mês.|  
|410|A solicitação usou HTTP em vez do protocolo HTTPS. HTTPS é o único protocolo com suporte.|  
|429|O chamador excedeu a cota de consultas por segundo.|  
|500|Erro de servidor inesperado.|

Se a solicitação falhar, a resposta conterá um objeto [ErrorResponse](#errorresponse) , que contém uma lista de objetos de [erro](#error) que descrevem o que causou o erro. Se o erro estiver relacionado a um parâmetro, o campo `parameter` identificará o parâmetro que é o problema. E se o erro estiver relacionado a um valor de parâmetro, o campo `value` identificará o valor que não é válido.

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
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|O Bing retorna InvalidAuthorization quando o Bing não pode autenticar o chamador. Por exemplo, o cabeçalho de `Ocp-Apim-Subscription-Key` está ausente ou a chave de assinatura não é válida.<br/><br/>A redundância ocorrerá se você especificar mais de um método de autenticação.<br/><br/>Se o erro for InvalidAuthorization, o código de status HTTP será 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|O Bing retorna InsufficientAuthorization quando o chamador não tem permissões para acessar o recurso. Isso pode ocorrer se a chave de assinatura tiver sido desabilitada ou tiver expirado. <br/><br/>Se o erro for InsufficientAuthorization, o código de status HTTP será 403.

## <a name="next-steps"></a>Passos seguintes
- [Início rápido de pesquisa comercial local](quickstarts/local-quickstart.md)
- [Início rápido do Java da pesquisa de negócios local](quickstarts/local-search-java-quickstart.md)
- [Início rápido do nó de pesquisa comercial local](quickstarts/local-search-node-quickstart.md)
- [Início rápido do Python de pesquisa comercial local](quickstarts/local-search-python-quickstart.md)
