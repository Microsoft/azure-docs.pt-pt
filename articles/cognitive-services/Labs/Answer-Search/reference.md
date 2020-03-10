---
title: Referência da Pesquisa de Respostas do Projeto
titlesuffix: Azure Cognitive Services
description: Referência para ponto final de pesquisa de resposta ao projeto.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: reference
ms.date: 04/13/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 28449435479aef0d6a1d8aee3e53de1a78f401b3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396794"
---
# <a name="project-answer-search-v7-reference"></a>Referência de pesquisa de resposta ao projeto v7

Bing Answer SearchAPI pega num parâmetro de consulta e devolve um `searchResponse` com `answerType`: `facts` ou `entities`. 

As aplicações que utilizam a API de Pesquisa de Resposta enviam pedidos para o ponto final com um URL para pré-visualizar num parâmetro de consulta.  O pedido deve incluir o parâmetro `q=searchTerm` e o *cabeçalho Ocp-Apim-Subscription-Key.*   

A resposta da JSON pode ser analisada por factos e entidades que contenham detalhes sobre o objeto de pesquisa.

## <a name="endpoint"></a>Ponto Final
Para solicitar resultados de Pesquisa de Resposta, envie um pedido para o seguinte ponto final. Utilize os cabeçalhos e os parâmetros url para definir especificações adicionais.

Ponto final GET: 
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=<searchTerm>&subscription-key=0123456789ABCDEF&mkt=en-us

```

O pedido deve utilizar o protocolo HTTPS e incluir o seguinte parâmetro de consulta:
-  `q=<URL>` - A consulta que identifica o objeto de pesquisa

Por exemplo, que mostram como fazer pedidos, ver [ C# quickstart](c-sharp-quickstart.md) ou [Java quickstart](java-quickstart.md). 

As seguintes secções fornecem detalhes técnicos sobre os objetos de resposta, parâmetros de consulta e cabeçalhos que afetam os resultados da pesquisa. 
  
Para obter informações sobre cabeçalhos que os pedidos devem incluir, consulte [Cabeçalhos](#headers).  
  
Para obter informações sobre parâmetros de consulta que os pedidos devem incluir, consulte [os parâmetros de consulta](#query-parameters).  
  
Para obter informações sobre os objetos JSON que a resposta inclui, consulte [objetos de resposta](#response-objects).

O comprimento máximo de URL de consulta é de 2.048 caracteres. Para garantir que o comprimento do URL não exceda o limite, o comprimento máximo dos seus parâmetros de consulta deve ser inferior a 1.500 caracteres. Se o URL exceder 2.048 caracteres, o servidor retorna 404 Não encontrados.  

Para obter informações sobre a utilização e exibição permitidas de resultados, consulte os requisitos de [utilização e visualização](use-display-requirements.md). 

> [!NOTE]
> Alguns cabeçalhos de pedido que são significativos para outras APIs de pesquisa não afetam a pré-visualização de URL
> - Pragma – o chamador não tem controlo sobre se a pré-visualização de URL utiliza cache
> - Cache-Control – o chamador não tem controlo sobre se a pré-visualização de URL utiliza cache
> - Agente-utilizador
> 
> Além disso, alguns parâmetros não são atualmente significativos para a API de pré-visualização de URL, mas podem ser usados no futuro para uma melhor globalização. 
 
## <a name="headers"></a>Cabeçalhos  
Seguem-se os cabeçalhos que um pedido e resposta podem incluir.  
  
|Cabeçalho|Descrição|  
|------------|-----------------|  
|Aceitar|Cabeçalho de pedido opcional.<br /><br /> O tipo de mídia padrão é aplicação/json. Para especificar que a utilização da resposta [JSON-LD,](https://json-ld.org/)coloque o cabeçalho Aceitar para aplicação/ld+json.|  
|<a name="acceptlanguage" />Accept-Language|Cabeçalho de pedido opcional.<br /><br /> Uma lista separada por vírgulas dos idiomas que vão ser utilizados nas cadeias da interface de utilizador. A lista está em ordem decrescente de preferência. Para obter mais informações, incluindo o formato esperado, veja [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Este cabeçalho e o parâmetro de consulta [setLang](#setlang) são mutuamente exclusivos &mdash; não os especifique a ambos.<br /><br /> Se definir este cabeçalho, também deve especificar o parâmetro de consulta cc. Para determinar o mercado do qual devolver resultados, o Bing utiliza o primeiro idioma suportado que encontra na lista e combina-o com o valor do parâmetro `cc`. Se a lista não incluir um idioma suportado, o Bing encontra o idioma e o mercado mais parecidos que suportem o pedido ou utiliza um mercado agregado ou predefinido para devolver os resultados. Para determinar o mercado que o Bing utiliza, veja o cabeçalho BingAPIs-Market.<br /><br /> Utilize este cabeçalho o parâmetro de consulta `cc` apenas se especificar vários idiomas. Caso contrário, utilize os parâmetros de consulta [mkt](#mkt) e [setLang](#setlang).<br /><br /> As cadeias da interface de utilizador são cadeias utilizadas como etiquetas na interface de utilizador. Os objetos de resposta JSON têm poucas cadeias de interface de utilizador. Todas as ligações para propriedades de Bing.com nos objetos de resposta aplicam o idioma especificado.|  
|<a name="market" />BingAPIs-Market|O cabeçalho da resposta.<br /><br /> O mercado que o pedido utiliza. O formato é \<languageCode\>-\<códigodeidioma\>. Por exemplo: en-US.|  
|<a name="traceid" />BingAPIs-TraceId|O cabeçalho da resposta.<br /><br /> O ID da entrada de registo que contém os detalhes do pedido. Quando ocorrer um erro, capture este ID. Se não conseguir determinar nem resolver o problema, inclua o ID juntamente com as outras informações que enviar à equipa de suporte.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Cabeçalho de pedido obrigatório.<br /><br /> A chave de subscrição que recebeu quando se inscreveu neste serviço nos [Serviços Cognitivos](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Cabeçalho de pedido opcional<br /><br /> Por predefinição, o Bing devolve conteúdo em cache, se disponível. Para impedir que o Bing devolva conteúdo em cache, defina o cabeçalho Pragma como no-cache (por exemplo, Pragma: no-cache).
|<a name="useragent" />User-Agent|Cabeçalho de pedido opcional.<br /><br /> O agente de utilizador que origina o pedido. O Bing utiliza o agente de utilizador para proporcionar uma experiência otimizada aos utilizadores de dispositivos móveis. Embora seja opcional, recomenda-se que especifique sempre este cabeçalho.<br /><br /> O agente de utilizador deve ser a mesma cadeia que qualquer browser geralmente utilizado envia. Para obter informações sobre os agentes de utilizador, veja [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Pode ver exemplos de cadeias de agente de utilizador abaixo:<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Cabeçalho de pedido e resposta opcional.<br /><br /> O Bing utiliza este cabeçalho para proporcionar um comportamento consistente nas chamada à API do Bing aos utilizadores. O Bing lança, muitas vezes, funcionalidades e melhorias novas e utiliza o ID de cliente como uma chave para atribuir tráfego aos diferentes lançamentos. Se não utilizar o mesmo ID de cliente para um utilizador em vários pedidos, o Bing poderá atribuir esse utilizador a vários lançamentos sobrepostos. A atribuição a vários lançamentos sobrepostos pode provocar uma experiência de utilizador inconsistente. Por exemplo, se a atribuição de lançamento do segundo pedido for diferente da do primeiro, a experiência poderá ser inesperada. Além disso, o Bing pode utilizar o ID de cliente para personalizar os resultados da Web o histórico de pesquisas desse ID, proporcionando uma experiência mais rica ao utilizador.<br /><br /> O Bing também utiliza este cabeçalho para ajudar a melhorar as classificações dos resultados ao analisar a atividade gerada por um ID de cliente. As melhorias à relevância ajudam a que as APIs do Bing devolvam resultados com melhor qualidade, o que, por sua vez, origina taxas de cliques mais altas para o consumidor da API.<br /><br /> **IMPORTANTE:** embora seja opcional, deve ver este cabeçalho como obrigatório. Persistir o ID de cliente em vários pedidos para a mesma combinação de utilizador final e dispositivo permite 1) que o consumidor da API receba uma experiência de utilizador consistente e 2) taxas de cliques mais altas, através de resultados com melhor qualidade das APIs do Bing.<br /><br /> As regras abaixo são as regras de utilização básicas aplicáveis a este cabeçalho.<br /><ul><li>Cada utilizador que utilize a sua aplicação no dispositivo tem de ter um ID de cliente exclusivo e gerado pelo Bing.<br /><br/>Se não incluir este cabeçalho no pedido, o Bing gera um ID e devolve-o no cabeçalho da resposta X-MSEdge-ClientID. O único momento em que NÃO deve incluir este cabeçalho num pedido é na primeira vez que o utilizador utiliza a sua aplicação nesse dispositivo.<br /><br/></li><li>Utilize o ID de cliente para cada pedido da API do Bing que a sua aplicação fizer para este utilizador no dispositivo.<br /><br/></li><li>**ATENÇÃO:** Deve certificar-se de que este ID do Cliente não é vinculado a qualquer informação autenticada da conta de utilizador.</li><br/><li>Persista o ID de cliente. Para persistir o ID numa aplicação de browser, utilize um cookie HTTP persistente para garantir que esse ID é utilizado em todas as sessões. Não utilize um cookie de sessão. Relativamente a outras aplicações, como aplicações móveis, utilize o armazenamento persistente do dispositivo para persistir o ID.<br /><br/>Da próxima vez que o utilizador utilizar a aplicação nesse dispositivo, recebe o ID de cliente que foi persistido.</li></ul><br /> **NOTA:** as respostas do Bing podem incluir ou não este cabeçalho. Se a resposta incluir este cabeçalho, capture o ID de cliente e utilize-o em todos os pedidos do Bing subsequentes do utilizador nesse dispositivo.<br /><br /> **NOTA:** se incluir X-MSEdge-ClientID, não pode incluir cookies no pedido.|  
|<a name="clientip" />X-MSEdge-ClientIP|Cabeçalho de pedido opcional.<br /><br /> O endereço IPv4 ou IPv6 do dispositivo cliente. O endereço IP é utilizado para detetar a localização do utilizador. O Bing utiliza as informações de localização para determinar o comportamento do safesearch.<br /><br /> **NOTA:** embora seja opcional, recomenda-se que especifique sempre este cabeçalho e o cabeçalho X-Search-Location.<br /><br /> Não ofusque a morada (por exemplo, ao alterar o último octeto para 0). Ofuscar a morada faz com que a localização não esteja perto da localização efetiva do dispositivo, o que pode fazer com que o Bing sirva resultados errados.|  
|<a name="location" />X-Search-Location|Cabeçalho de pedido opcional.<br /><br /> Uma lista separada por ponto e vírgula de pares de chaves/valores que descreve a localização geográfica do cliente. O Bing utiliza as informações de localização para determinar o comportamento do safesearch e para devolver conteúdos locais relevantes. Indique o par chave/valor como \<chave\>:\<valor\>. As chaves seguintes são as chaves utilizadas para especificar a localização do utilizador.<br /><br /><ul><li>Lat&mdash;A latitude da localização do cliente, em graus. Tem de ser igual ou superior a -90,0 e inferior ou igual a +90,0. Os valores negativos indicam latitudes no hemisfério sul e os positivos latitudes no hemisfério norte.<br /><br /></li><li>longo&mdash;A longitude da localização do cliente, em graus. Tem de ser igual ou superior a -180.0 e inferior ou igual a +180.0. Os valores negativos indicam longitudes a ocidente e os positivos a oriente.<br /><br /></li><li>re&mdash; O raio, em metros, que especifica a precisão horizontal das coordenadas. Transmita o valor que o serviço de localização do dispositivo devolve. Os valores mais comuns podem ser 22 m para GPS/Wi-Fi, 380 m para triangulação de antenas e 18 000 m para pesquisa de IP inversa.<br /><br /></li><li>ts&mdash; O carimbo de tempo UTC UNIX de quando o cliente estava no local. (o carimbo de data/hora é o número de segundos desde 1 de janeiro de 1970.)<br /><br /></li><li>head&mdash;Optional. O cabeçalho relativo do cliente ou a direção de deslocamento. Especifique a direção de deslocamento em graus, de 0 a 360, a contar no sentido dos ponteiros do relógio em relação ao norte verdadeiro. Especifique esta chave apenas se a chave `sp` não for zero.<br /><br /></li><li>sp&mdash; A velocidade horizontal (velocidade), em metros por segundo, que o dispositivo cliente está viajando.<br /><br /></li><li>alt&mdash; A altitude do dispositivo cliente, em metros.<br /><br /></li><li>are&mdash;Optional. O raio, em metros, que especifica a exatidão vertical das coordenadas. O raio falha a 50 Km. Especifique esta chave apenas se especificar a chave `alt`.<br /><br /></li></ul> **NOTA:** Embora estas chaves sejam opcionais, quanto mais informações fornecer, mais precisos são os resultados da localização.<br /><br /> **NOTA:** É encorajado a especificar sempre a localização geográfica do utilizador. Especificar a localização é particularmente importante se o endereço IP do cliente não refletir, com precisão, a localização física do utilizador (por exemplo, se este utilizar VPN). Para obter os melhores resultados, deve incluir este cabeçalho e o cabeçalho X-MSEdge-ClientIP, mas deve incluir este cabeçalho, no mínimo.|

> [!NOTE] 
> Não se esqueça de que os Termos de Utilização requerem conformidade com toda a legislação aplicável, incluindo a relativa à utilização destes cabeçalhos. Por exemplo, em determinadas jurisdições, como a Europa, há requisitos para obter o consentimento do utilizador antes de colocar certos tipos de dispositivos de monitorização nos dispositivos dos utilizadores.
  

## <a name="query-parameters"></a>Parâmetros de consulta  
O pedido pode incluir os seguintes parâmetros de consulta. Consulte a coluna necessária para os parâmetros necessários. Tem de codificar os parâmetros de consulta.  
  
  
|Nome|Valor|Tipo|Necessário|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|O mercado de onde os resultados provêm. <br /><br />Para obter uma lista de possíveis valores de mercado, consulte códigos de mercado.<br /><br /> **NOTA:** A API de pré-visualização de URL atualmente apenas suporta o mercado e a linguagem en-us.<br /><br />|String|Sim|  
|<a name="query" />q|O URL para pré-visualizar|String|Sim|  
|<a name="responseformat" />respostaFormat|O tipo de mídia a utilizar para a resposta. Seguem-se os possíveis valores insensíveis aos casos.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> O padrão é JSON. Para obter informações sobre os objetos JSON que a resposta contém, consulte [Objetos de Resposta](#response-objects).<br /><br />  Se especificar jsonLd, o corpo de resposta inclui objetos JSON-LD que contêm os resultados da pesquisa. Para obter informações sobre o JSON-LD, consulte [JSON-LD](https://json-ld.org/).|String|Não|  
|<a name="safesearch" />safeSearch|É um filtro utilizado para filtrar conteúdo para adultos. Os valores seguintes são os possíveis valores do filtro, não sensíveis a maiúsculas e minúsculas.<br /><ul><li>Fora&mdash;Devolver páginas web com texto, imagens ou vídeos para adultos.<br /><br/></li><li>Moderar&mdash;Páginas web de retorno com texto adulto, mas não imagens ou vídeos para adultos.<br /><br/></li><li>&mdash;Estritanão devolva páginas web com texto, imagens ou vídeos para adultos.</li></ul><br /> A predefinição é Moderado.<br /><br /> **NOTA:** Se o pedido vier de um mercado que a política de bing para adultos exige que `safeSearch` seja definido para O Rigor, Bing ignora o valor `safeSearch` e usa O Estrito.<br/><br/>**NOTA:** se utilizar o operador de consulta `site:`, existe a possibilidade de a resposta poder conter conteúdos para adultos, independentemente da definição do parâmetro de consulta `safeSearch`. Utilize `site:` apenas se tiver conhecimento do conteúdo do site e se o seu cenário suportar a possibilidade de conteúdos para adultos. |String|Não|  
|<a name="setlang" />setLang|O idioma a utilizar nas cadeias da interface de utilizador. Especifique o idioma com o código de idioma ISO 639-1 de duas letras. Por exemplo, o código de idioma de inglês é EN. A predefinição é EN (inglês).<br /><br /> Embora seja opcional, deve indicar sempre o idioma. Normalmente, `setLang` é definido como o mesmo idioma especificado por `mkt`, a não ser que o utilizador queira que as cadeias da interface de utilizador sejam apresentadas noutro idioma.<br /><br /> Este parâmetro e o cabeçalho [Accept-Language](#acceptlanguage) são mutuamente exclusivos &mdash; não os especifique a ambos.<br /><br /> As cadeias da interface de utilizador são cadeias utilizadas como etiquetas na interface de utilizador. Os objetos de resposta JSON têm poucas cadeias de interface de utilizador. Além disso, todas as ligações para propriedades de Bing.com nos objetos de resposta aplicam o idioma especificado.|String|Não| 


## <a name="response-objects"></a>Objetos de resposta  
O esquema de resposta é ou uma [WebPage] ou ErrorResponse, como na API de pesquisa web. Se o pedido falhar, o objeto de nível superior é o objeto [ErrorResponse.](#errorresponse)


|Object|Descrição|  
|------------|-----------------|  
|[Página Web]|Objeto JSON de nível superior que contém atributos de pré-visualização.|  
|[Facto]|Objeto JSON de nível superior que contém factos.| 
|[Entidades|Objeto JSON de nível superior que contém detalhes da entidade.| 

  
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
|erros <a name="errors" />|Uma lista de erros que descrevem as razões pelas quais o pedido falhou.|[Erro](#error)|  

  
  
### <a name="license"></a>Licença  
Define a licença sob a qual o texto ou a foto podem ser utilizados.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|nome|O nome da licença.|String|  
|url|O URL para um site onde o utilizador pode obter mais informações sobre a licença.<br /><br /> Use o nome e o URL para criar uma hiperligação.|String|  
  

### <a name="licenseattribution"></a>Atribuição de Licença  
Define uma regra contratual para atribuição de licença.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|_type|Uma dica tipo, que está definida para a Atribuição de Licenças.|String|  
|licença|A licença sob a qual o conteúdo pode ser utilizado.|[Licença](#license)|  
|licenseNotice|A licença para exibir ao lado do campo alvo. Por exemplo, "Texto sob licença CC-BY-SA".<br /><br /> Use o nome da licença e URL no campo `license` para criar uma hiperligação para o site que descreve os detalhes da licença. Em seguida, substitua o nome da licença na cadeia `licenseNotice` (por exemplo, CC-BY-SA) pela hiperligação que acabou de criar.|String|  
|mustBeCloseToContent|Um valor booleano que determina se o conteúdo da regra deve ser colocado nas proximidades do campo a que a regra se aplica. Se **for verdade,** o conteúdo deve ser colocado nas proximidades. Se não existir **falso,** ou se este campo não existir, o conteúdo pode ser colocado a critério do autor da chamada.|Booleano|  
|targetPropertyName|O nome do campo a que a regra se aplica.|String|  
  

### <a name="link"></a>Ligação  
Define os componentes de uma hiperligação.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|_type|Digite a dica.|String|  
|texto|O texto do visor.|String|  
|url|Uma URL. Utilize o URL e o texto de visualização para criar uma hiperligação.|String|  
  

### <a name="linkattribution"></a>Atribuição de Ligações  
Define uma regra contratual para a atribuição de ligações.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|_type|Uma dica de tipo, que é definida para LinkAttribution.|String|  
|mustBeCloseToContent|Um valor booleano que determina se o conteúdo da regra deve ser colocado nas proximidades do campo a que a regra se aplica. Se **for verdade,** o conteúdo deve ser colocado nas proximidades. Se não existir **falso,** ou se este campo não existir, o conteúdo pode ser colocado a critério do autor da chamada.|Booleano|  
|targetPropertyName|O nome do campo a que a regra se aplica.<br /><br /> Se um alvo não for especificado, a atribuição aplica-se à entidade como um todo e deve ser exibida imediatamente após a apresentação da entidade. Se existirem múltiplas regras de atribuição de texto e ligação que não especifiquem um alvo, deve concatená-las e exibi-las utilizando uma etiqueta "Dados a partir de: " . Por exemplo, "Dados do <provider name1\> &#124; <provider name2\>".|String|  
|texto|O texto de atribuição.|String|  
|url|O URL para o site do fornecedor. Utilize `text` e URL para criar hiperligação.|String|  
  
  
### <a name="mediaattribution"></a>MediaAtribuição  
Define uma regra contratual para a atribuição dos meios de comunicação social.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|_type|Uma dica tipo, que é definida para MediaAttribution.|String|  
|mustBeCloseToContent|Um valor booleano que determina se o conteúdo da regra deve ser colocado nas proximidades do campo a que a regra se aplica. Se **for verdade,** o conteúdo deve ser colocado nas proximidades. Se não existir **falso,** ou se este campo não existir, o conteúdo pode ser colocado a critério do autor da chamada.|Booleano|  
|targetPropertyName|O nome do campo a que a regra se aplica.|String|  
|url|O URL que utiliza para criar hiperligação do conteúdo dos meios de comunicação. Por exemplo, se o alvo for uma imagem, utilizaria o URL para tornar a imagem clicável.|String|  
  
  
  
### <a name="organization"></a>Organização  
Define um editor.  
  
Note que um editor pode fornecer o seu nome ou o seu website ou ambos.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|nome|O nome da editora.|String|  
|url|O URL para o site da editora.<br /><br /> Note que a editora pode não fornecer um website.|String|  
  
  

### <a name="webpage"></a>Página Web  
Define informações sobre uma página Web na pré-visualização.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|
|nome|O título da página, não necessariamente o título HTML|String|
|url|O URL que foi realmente rastejado (pedido pode ter seguido redirecionamentos)|String|  
|descrição|Breve descrição da página e do conteúdo|String|  
|isFamilyFriendly|Mais preciso para itens no índice web; atreca-se em tempo real fazer esta deteção com base apenas no URL e não no conteúdo da página|boolean|
|primaryImageOfPage/contentUrl|O URL para uma imagem representativa a incluir na pré-visualização|String| 
  
  
### <a name="querycontext"></a>QueryContext  
Define o contexto de consulta que Bing usou para o pedido.  
  
|Elemento|Descrição|Tipo|  
|-------------|-----------------|----------|  
|adultintent|Um valor booleano que indica se a consulta especificada tem intenção adulta. O valor é **verdadeiro** se a consulta tiver intenção adulta; caso contrário, **falso.**|Booleano|  
|alterationOverrideQuery|A corda de consulta para usar para forçar Bing a usar a corda original. Por exemplo, se a corda de consulta estiver *a abrandar,* a corda de sobreposição será *+saling downwind*. Lembre-se de codificar a corda de consulta que resulta em *%2Bsaling+downwind*.<br /><br /> Este campo só está incluído se a cadeia de consulta original contiver um erro ortográfico.|String|  
|alteradoQuery|A corda de consulta usada por Bing para realizar a consulta. Bing usa a corda de consulta alterada se a corda de consulta original contivesse erros ortográficos. Por exemplo, se a corda de consulta for `saling downwind`, a corda de consulta alterada será `sailing downwind`.<br /><br /> Este campo só está incluído se a cadeia de consulta original contiver um erro ortográfico.|String|  
|askUserForLocation|Um valor booleano que indica se bing requer a localização do utilizador para fornecer resultados precisos. Se especificou a localização do utilizador utilizando os cabeçalhos [X-MSEdge-ClientIP](#clientip) e [X-Search-Location,](#location) pode ignorar este campo.<br /><br /> Para consultas conscientes de localização, como "o tempo de hoje" ou "restaurantes perto de mim" que precisam da localização do utilizador para fornecer resultados precisos, este campo está definido para **verdade.**<br /><br /> Para consultas conscientes de localização que incluem a localização (por exemplo, "Seattle weather"), este campo está definido como **falso**. Este campo também está definido como **falso** para consultas que não são conscientes da localização, como "best sellers".|Booleano|  
|originalQuery|A corda de consulta, conforme especificado no pedido.|String|  

### <a name="identifiable"></a>Identificável

|Nome|Valor|Tipo|  
|-------------|-----------------|----------|
|ID|Um identificador de recursos|String|
 
### <a name="rankinggroup"></a>RankingGroup
Define um grupo de resultados de pesquisa, como o mainline.

|Nome|Valor|Tipo|  
|-------------|-----------------|----------|
|items|Uma lista de resultados de pesquisa para exibir no grupo.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Define um item de resultado de pesquisa para exibir.

|Nome|Valor|Tipo|  
|-------------|-----------------|----------|
|resultadoÍndice|Um índice de base zero do item na resposta a exibir. Se o item não incluir este campo, exiba todos os itens na resposta. Por exemplo, exibir todos os artigos noticiosos na resposta do Notícias.|Número inteiro|
|respostaTipo|A resposta que contém o item a exibir. Por exemplo, Notícias.<br /><br />Utilize o tipo para encontrar a resposta no objeto SearchResponse. O tipo é o nome de um campo SearchResponse.<br /><br /> No entanto, utilize o tipo de resposta apenas se este objeto incluir o campo de valor; caso contrário, ignorá-lo.|String|
|textualIndex|O índice da resposta em textualAnswers para exibir.| Inteiro não assinado|
|valor|O ID que identifica uma resposta para exibir ou um item de uma resposta para exibir. Se o ID identificar uma resposta, exiba todos os itens da resposta.|Identificável|

### <a name="rankingresponse"></a>RankingResponse  
Define onde deve ser colocado o conteúdo da página de resultados da pesquisa e em que ordem.  
  
|Nome|Valor|  
|----------|-----------|  
|<a name="ranking-mainline" />linha principal|Os resultados da pesquisa para exibir na linha principal.|  
|polo <a name="ranking-pole" />|Os resultados de pesquisa que devem ser oferecidos o tratamento mais visível (por exemplo, apresentados acima da linha principal e da barra lateral).|  
|<a name="ranking-sidebar" />barra lateral|Os resultados da pesquisa para exibir na barra lateral.| 


### <a name="searchresponse"></a>Resposta de pesquisa  
Define o objeto de alto nível que a resposta inclui quando o pedido for bem sucedido.  
  
Note que se o serviço suspeitar de um ataque de negação de serviço, o pedido será bem sucedido (o código de estado HTTP é de 200 OK); no entanto, o corpo da resposta estará vazio.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|_type|Digite a dica, que está definida para SearchResponse.|String|  
|Página Web|Um objeto JSON que define a pré-visualização|string|  
  
  
### <a name="textattribution"></a>Atribuição de texto  
Define uma regra contratual para atribuição de texto simples.  
  
|Nome|Valor|Tipo|  
|----------|-----------|----------|  
|_type|Uma dica tipo, que é definida para TextAttribution.|String|  
|texto|O texto de atribuição.<br /><br /> A atribuição de texto aplica-se à entidade como um todo e deve ser exibida imediatamente após a apresentação da entidade. Se existirem múltiplas regras de atribuição de texto ou de ligação que não especifiquem um alvo, deve concatená-las e exibi-las utilizando uma etiqueta "Dados a partir de: " .|String| 


## <a name="error-codes"></a>Códigos de erro

Seguem-se os possíveis códigos de estado http que um pedido devolve.  
  
|Código de Estado|Descrição|  
|-----------------|-----------------|  
|200|Êxito.|  
|400|Falta um dos parâmetros de consulta ou não é válido.|  
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
- [Início Rápido do C#](c-sharp-quickstart.md)
- [Início rápido do Java](java-quickstart.md)
- [Início rápido do Node](node-quickstart.md)
- [Início Rápido do Python](python-quickstart.md)

