---
title: Enviar consultas de pesquisa para o API da Pesquisa Visual do Bing
titleSuffix: Azure Cognitive Services
description: Este artigo descreve os parâmetros e atributos das solicitações enviadas ao API da Pesquisa Visual do Bing, bem como o objeto de resposta.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: aahi
ms.openlocfilehash: 5d27aa80a63232694e1c9951f98b2191ba575e74
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913075"
---
# <a name="sending-search-queries-to-the-bing-visual-search-api"></a>Enviar consultas de pesquisa para o API da Pesquisa Visual do Bing

Este artigo descreve os parâmetros e atributos das solicitações enviadas ao API da Pesquisa Visual do Bing, bem como o objeto de resposta. 

Você pode obter informações sobre uma imagem de três maneiras:

- Usar um token de insights que você obtém de uma imagem em uma chamada anterior para um dos pontos de extremidade de [API de pesquisa de imagem do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) .
- Enviando a URL de uma imagem.
- Carregando uma imagem (em formato binário).

## <a name="bing-visual-search-requests"></a>Solicitações de Pesquisa Visual do Bing

Se você enviar Pesquisa Visual um token de imagem ou uma URL, o trecho a seguir mostrará o objeto JSON que você deve incluir no corpo da POSTAgem:

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

O objeto `imageInfo` tem de incluir o campo `url` ou `imageInsightsToken`, não ambos. Defina o campo `url` como a URL de uma imagem acessível pela Internet. O tamanho máximo de imagem suportado é 1 MB.

`imageInsightsToken` tem de ser definido como um token de informações. Para obter um token de informações, chame a API de Imagens do Bing. A resposta contém uma lista de objetos `Image`. Cada objeto `Image` inclui um campo `imageInsightsToken`, que contém o token.

O campo `cropArea` é opcional. A área de corte especifica o canto superior esquerdo e o canto inferior direito de uma região de interesse. Especifique os valores no intervalo de 0,0 a 1,0. Os valores são uma percentagem da largura ou da altura geral. O exemplo acima assinala a metade do lado direito da imagem como sendo a região de interesse. Inclua-a se pretender limitar o pedido de informações a essa região.

O objeto `filters` contém um filtro de locais (veja o campo `site`) que pode utilizar para restringir os resultados de imagens e produtos semelhantes a um domínio específico. Por exemplo, se a imagem for de um livro de superfície, você poderá definir `site` como `www.microsoft.com`.

Se quiser receber informações sobre uma cópia local de uma imagem, carregue a imagem como dados binários.

Para obter detalhes sobre como incluir estas opções no corpo de POST, veja [Tipos de formatos de conteúdos](#content-form-types).

### <a name="search-endpoint"></a>Ponto de extremidade de pesquisa

O ponto final da Pesquisa Visual é https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch.

Os pedidos só podem ser enviados como HTTP POST.

### <a name="query-parameters"></a>Parâmetros de consulta

Os parâmetros seguintes são os parâmetros de consulta que o pedido deve especificar. No mínimo, você deve incluir o parâmetro de consulta `mkt`:

| Nome | Valor | Tipo | Obrigatório |
| --- | --- | --- | --- |
| <a name="cc" />cc  | Um código de país de dois caracteres que representa de onde vêm os resultados.<br /><br /> Se definir este parâmetro, também tem de especificar o cabeçalho [Accept-Language](#acceptlanguage). O Bing utiliza o primeiro idioma suportado que encontra na lista de idiomas e combina-o com o código de país que especificou, de modo a determinar o mercado do qual devolver resultados. Se a lista de idiomas não incluir um idioma suportado, o Bing encontra o idioma e o mercado mais parecidos que suportem o pedido. Em vez do mercado especificado, o Bing também poderá utilizar um mercado agregado ou predefinido para os resultados.<br /><br /> Deve utilizar este parâmetro de consulta e o parâmetro `Accept-Language` apenas se indicar vários idiomas; caso contrário, utilize os parâmetros de consulta `mkt` e `setLang`.<br /><br /> Este parâmetro e o parâmetro de consulta [mkt](#mkt) são mutuamente exclusivos &mdash; não os especifique a ambos. | String | Não       |
| <a name="mkt" />mkt   | O mercado de onde os resultados provêm. <br /><br /> **Observação:** Você sempre deve especificar o mercado, se for conhecido. Desta forma, ajuda o Bing a encaminhar o pedido e a devolver a resposta adequada e ideal.<br /><br /> Este parâmetro e o parâmetro de consulta [cc](#cc) são mutuamente exclusivos &mdash; não os especifique a ambos. | String | Sim      |
| <a name="safesearch" />safeSearch | Um filtro para conteúdo adulto. Os valores seguintes são os possíveis valores do filtro, não sensíveis a maiúsculas e minúsculas.<br /><ul><li>Desativado &mdash; devolve páginas Web com conteúdos em textos ou imagens para adultos.<br /><br/></li><li>Moderado &mdash; devolve páginas Web com conteúdos para adultos em texto, mas não em imagens.<br /><br/></li><li>Estrito &mdash; não devolve páginas Web com conteúdos em textos ou imagens para adultos.</li></ul><br /> A predefinição é Moderado.<br /><br /> **NOTA:** se o pedido vier de um mercado no qual a política de conteúdos para adultos do Bing exija que `safeSearch` esteja Estrito, o Bing ignora o valor `safeSearch` e utiliza Estrito.<br/><br/>**Observação:** Se você usar o operador de consulta `site:`, haverá uma chance de que a resposta possa conter conteúdo adulto, independentemente de como o parâmetro de consulta `safeSearch` está definido como. Utilize `site:` apenas se tiver conhecimento do conteúdo do site e se o seu cenário suportar a possibilidade de conteúdos para adultos.  | String | Não       |
| <a name="setlang" />setLang  | O idioma a utilizar nas cadeias da interface de utilizador. Especifique o idioma usando o código de linguagem ISO 639-1 de duas letras. Por exemplo, o código de idioma de inglês é EN. A predefinição é EN (inglês).<br /><br /> Embora seja opcional, deve indicar sempre o idioma. Normalmente, `setLang` é definido como o mesmo idioma especificado por `mkt`, a não ser que o utilizador queira que as cadeias da interface de utilizador sejam apresentadas noutro idioma.<br /><br /> Este parâmetro e o cabeçalho [Accept-Language](#acceptlanguage) são mutuamente exclusivos &mdash; não os especifique a ambos.<br /><br /> As cadeias da interface de utilizador são cadeias utilizadas como etiquetas na interface de utilizador. Os objetos de resposta JSON têm poucas cadeias de interface de utilizador. Além disso, todas as ligações para propriedades de Bing.com nos objetos de resposta aplicam o idioma especificado. | String | Não   |

## <a name="headers"></a>Cabeçalhos

Os cabeçalhos seguintes são os cabeçalhos que o pedido deve especificar. Os cabeçalhos `Content-Type` e `Ocp-Apim-Subscription-Key` são os únicos cabeçalhos necessários, mas você também deve incluir `User-Agent`, `X-MSEdge-ClientID`, `X-MSEdge-ClientIP`e `X-Search-Location`.

| Cabeçalho | Descrição |
| --- | --- |
| <a name="acceptlanguage" />Accept-Language  | Cabeçalho de pedido opcional.<br /><br /> Uma lista separada por vírgulas dos idiomas que vão ser utilizados nas cadeias da interface de utilizador. A lista está em ordem decrescente de preferência. Para obter mais informações, incluindo o formato esperado, veja [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Este cabeçalho e o parâmetro de consulta [setLang](#setlang) são mutuamente exclusivos &mdash; não os especifique a ambos.<br /><br /> Se definir este cabeçalho, também tem de especificar o parâmetro de consulta [cc](#cc). Para determinar o mercado do qual devolver resultados, o Bing utiliza o primeiro idioma suportado que encontra na lista e combina-o com o valor do parâmetro `cc`. Se a lista não incluir um idioma suportado, o Bing encontra o idioma e o mercado mais parecidos que suportem o pedido ou utiliza um mercado agregado ou predefinido para devolver os resultados. Para determinar o mercado usado pelo Bing, consulte o cabeçalho `BingAPIs-Market`.<br /><br /> Utilize este cabeçalho o parâmetro de consulta `cc` apenas se especificar vários idiomas. Caso contrário, utilize os parâmetros de consulta [mkt](#mkt) e [setLang](#setlang).<br /><br /> As cadeias da interface de utilizador são cadeias utilizadas como etiquetas na interface de utilizador. Os objetos de resposta JSON têm poucas cadeias de interface de utilizador. Todas as ligações para propriedades de Bing.com nos objetos de resposta aplicam o idioma especificado.  |
| <a name="contenttype" />Content-Type  |     |
| <a name="market" />BingAPIs-Market    | O cabeçalho da resposta.<br /><br /> O mercado que o pedido utiliza. O formato é \<languageCode\>-\<códigodeidioma\>. Por exemplo: en-US.  |
| <a name="traceid" />BingAPIs-TraceId  | O cabeçalho da resposta.<br /><br /> O ID da entrada de registo que contém os detalhes do pedido. Quando ocorrer um erro, capture este ID. Se não conseguir determinar nem resolver o problema, inclua o ID juntamente com as outras informações que enviar à equipa de suporte. |
| <a name="subscriptionkey" />Ocp-Apim-Subscription-Key | Cabeçalho de pedido obrigatório.<br /><br /> A chave de subscrição que recebeu quando se inscreveu neste serviço nos [Serviços Cognitivos](https://www.microsoft.com/cognitive-services/). |
| <a name="pragma" />Pragma |   |
| <a name="useragent" />User-Agent  | Cabeçalho de pedido opcional.<br /><br /> O agente de utilizador que origina o pedido. O Bing utiliza o agente de utilizador para proporcionar uma experiência otimizada aos utilizadores de dispositivos móveis. Embora seja opcional, recomenda-se que especifique sempre este cabeçalho.<br /><br /> O agente de utilizador deve ser a mesma cadeia que qualquer browser geralmente utilizado envia. Para obter informações sobre agentes de usuário, consulte [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Pode ver exemplos de cadeias de agente de utilizador abaixo:<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>      |
| <a name="clientid" />X-MSEdge-ClientID  | Cabeçalho de pedido e resposta opcional.<br /><br /> O Bing utiliza este cabeçalho para proporcionar um comportamento consistente nas chamada à API do Bing aos utilizadores. O Bing lança, muitas vezes, funcionalidades e melhorias novas e utiliza o ID de cliente como uma chave para atribuir tráfego aos diferentes lançamentos. Se não utilizar o mesmo ID de cliente para um utilizador em vários pedidos, o Bing poderá atribuir esse utilizador a vários lançamentos sobrepostos. A atribuição a vários lançamentos sobrepostos pode provocar uma experiência de utilizador inconsistente. Por exemplo, se a atribuição de lançamento do segundo pedido for diferente da do primeiro, a experiência poderá ser inesperada. Além disso, o Bing pode utilizar o ID de cliente para personalizar os resultados da Web o histórico de pesquisas desse ID, proporcionando uma experiência mais rica ao utilizador.<br /><br /> O Bing também utiliza este cabeçalho para ajudar a melhorar as classificações dos resultados ao analisar a atividade gerada por um ID de cliente. As melhorias à relevância ajudam a que as APIs do Bing devolvam resultados com melhor qualidade, o que, por sua vez, origina taxas de cliques mais altas para o consumidor da API.<br /><br /> **IMPORTANTE:** embora seja opcional, deve ver este cabeçalho como obrigatório. Persistir o ID de cliente em vários pedidos para a mesma combinação de utilizador final e dispositivo permite 1) que o consumidor da API receba uma experiência de utilizador consistente e 2) taxas de cliques mais altas, através de resultados com melhor qualidade das APIs do Bing.<br /><br /> As regras abaixo são as regras de utilização básicas aplicáveis a este cabeçalho.<br /><ul><li>Cada utilizador que utilize a sua aplicação no dispositivo tem de ter um ID de cliente exclusivo e gerado pelo Bing.<br /><br/>Se não incluir este cabeçalho no pedido, o Bing gera um ID e devolve-o no cabeçalho da resposta X-MSEdge-ClientID. O único momento em que NÃO deve incluir este cabeçalho num pedido é na primeira vez que o utilizador utiliza a sua aplicação nesse dispositivo.<br /><br/></li><li>**ATENÇÃO:** tem de garantir que este ID de cliente não pode ser ligado às informações de nenhuma conta de utilizador autenticada.</li><li>Utilize o ID de cliente para cada pedido da API do Bing que a sua aplicação fizer para este utilizador no dispositivo.<br /><br/></li><li>Persista o ID de cliente. Para persistir o ID numa aplicação de browser, utilize um cookie HTTP persistente para garantir que esse ID é utilizado em todas as sessões. Não utilize um cookie de sessão. Relativamente a outras aplicações, como aplicações móveis, utilize o armazenamento persistente do dispositivo para persistir o ID.<br /><br/>Da próxima vez que o utilizador utilizar a aplicação nesse dispositivo, recebe o ID de cliente que foi persistido.</li></ul><br /> **NOTA:** as respostas do Bing podem incluir ou não este cabeçalho. Se a resposta incluir este cabeçalho, capture o ID de cliente e utilize-o em todos os pedidos do Bing subsequentes do utilizador nesse dispositivo.<br /><br /> **NOTA:** se incluir X-MSEdge-ClientID, não pode incluir cookies no pedido. |
| <a name="clientip" />X-MSEdge-ClientIP   | Cabeçalho de pedido opcional.<br /><br /> O endereço IPv4 ou IPv6 do dispositivo cliente. O endereço IP é utilizado para detetar a localização do utilizador. O Bing utiliza as informações de localização para determinar o comportamento do safesearch.<br /><br /> **NOTA:** embora seja opcional, recomenda-se que especifique sempre este cabeçalho e o cabeçalho X-Search-Location.<br /><br /> Não ofusque a morada (por exemplo, ao alterar o último octeto para 0). Ofuscar a morada faz com que a localização não esteja perto da localização efetiva do dispositivo, o que pode fazer com que o Bing sirva resultados errados. |
| <a name="location" />X-Search-Location   | Cabeçalho de pedido opcional.<br /><br /> Uma lista separada por ponto e vírgula de pares de chaves/valores que descreve a localização geográfica do cliente. O Bing utiliza as informações de localização para determinar o comportamento do safesearch e para devolver conteúdos locais relevantes. Indique o par chave/valor como \<chave\>:\<valor\>. As chaves seguintes são as chaves utilizadas para especificar a localização do utilizador.<br /><br /><ul><li>lat&mdash;Required. A latitude da localização do cliente, em graus. Tem de ser igual ou superior a -90,0 e inferior ou igual a +90,0. Os valores negativos indicam latitudes no hemisfério sul e os positivos latitudes no hemisfério norte.<br /><br /></li><li>long&mdash;Required. A longitude da localização do cliente, em graus. Tem de ser igual ou superior a -180.0 e inferior ou igual a +180.0. Os valores negativos indicam longitudes a ocidente e os positivos a oriente.<br /><br /></li><li>re&mdash;Required. O raio, em metros, que especifica a exatidão horizontal das coordenadas. Transmita o valor que o serviço de localização do dispositivo devolve. Os valores típicos podem ser 22 m para GPS/Wi-Fi, 380 m para a triangulação de torre de célula e 18.000 m para pesquisa de IP reverso.<br /><br /></li><li>ts&mdash;Optional. O carimbo de data/hora UNIX UTC de quando o cliente estava na localização (o carimbo de data/hora é o número de segundos desde 1 de janeiro de 1970.)<br /><br /></li><li>head&mdash;Optional. O cabeçalho relativo do cliente ou a direção de deslocamento. Especifique a direção de deslocamento em graus, de 0 a 360, a contar no sentido dos ponteiros do relógio em relação ao norte verdadeiro. Especifique esta chave apenas se a chave `sp` não for zero.<br /><br /></li><li>sp&mdash;Optional. A velocidade horizontal, em metros por segundo, a que o dispositivo cliente se está a deslocar.<br /><br /></li><li>alt&mdash;Optional. A altitude do dispositivo cliente, em metros.<br /><br /></li><li>are&mdash;Optional. O raio, em metros, que especifica a exatidão vertical das coordenadas. Especifique esta chave apenas se especificar a chave `alt`.<br /><br /></li></ul> **NOTA:** embora muitas das chaves sejam opcionais, quanto mais informações fornecer, mais precisos serão os resultados da localização.<br /><br /> **NOTA:** embora seja opcional, recomenda-se que especifique sempre a localização geográfica do utilizador. Especificar a localização é particularmente importante se o endereço IP do cliente não refletir, com precisão, a localização física do utilizador (por exemplo, se este utilizar VPN). Para obter resultados ideais, você deve incluir esse cabeçalho e o cabeçalho `X-MSEdge-ClientIP`, mas, no mínimo, você deve incluir esse cabeçalho.       |

> [!NOTE]
> Lembre-se de que os [requisitos de pesquisa do Bing de uso e exibição da API](../../bing-web-search/use-display-requirements.md) exigem conformidade com todas as leis aplicáveis, incluindo o uso desses cabeçalhos. Por exemplo, em determinadas jurisdições, como a Europa, há requisitos para obter o consentimento do utilizador antes de colocar certos tipos de dispositivos de monitorização nos dispositivos dos utilizadores.

<a name="content-form-types" />

### <a name="content-form-types"></a>Tipos de formatos de conteúdos

Cada solicitação deve incluir o cabeçalho `Content-Type`. O cabeçalho deve ser definido como: `multipart/form-data; boundary=\<boundary string\>`, em que \<cadeia de caracteres de limite\> é uma cadeia de caracteres opaca exclusiva que identifica o limite dos dados do formulário. Por exemplo, `boundary=boundary_1234-abcd`.

Se você enviar Pesquisa Visual um token de imagem ou uma URL, o trecho a seguir mostrará os dados de formulário que você deve incluir no corpo da POSTAgem. Os dados do formulário devem incluir o cabeçalho `Content-Disposition` e você deve definir seu parâmetro `name` como "knowledgeRequest". Para obter detalhes sobre o objeto `imageInfo`, consulte a solicitação.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "url" : "https://contoso.com/2018/05/fashion/red.jpg"
    }
}

--boundary_1234-abcd--
```

Opcionalmente, você pode definir o atributo `enableEntityData` no cabeçalho como `true` para obter informações detalhadas sobre a entidade principal na imagem que você carrega, incluindo links para as informações da Web e de atribuição. Esse campo é `false` por padrão.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
  "imageInfo" : {
      "url" : "https://contoso.com/2018/05/fashion/red.jpg"
  },
  "knowledgeRequest" : {
    "invokedSkillsRequestData" : {
        "enableEntityData" : "true"
    }
  }
}

--boundary_1234-abcd--
```

Se você carregar uma imagem local, o trecho a seguir mostrará os dados do formulário que você deve incluir no corpo da POSTAgem. Os dados do formulário devem incluir o cabeçalho `Content-Disposition`. O respetivo parâmetro `name` tem de ser definido como “image” e o parâmetro `filename` pode ser definido como qualquer cadeia. O cabeçalho `Content-Type` pode ser definido como qualquer tipo MIME de imagem usado com frequência. O conteúdo do formulário são os dados binários da imagem. O tamanho máximo de imagem que pode carregar é 1 MB. A largura ou altura máxima deve ser 1500 píxeis ou menos.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

O trecho a seguir mostra como especificar a região de interesse de uma imagem carregada:

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "cropArea" : {
            "top" : 0.2,
            "left" : 0.3,
            "bottom" : 0.7,
            "right" : 0.6
        }
    }
}

--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="image"
Content-Type: image/jpeg


ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

### <a name="example-request"></a>Pedido de exemplo

O trecho de código a seguir mostra uma solicitação completa de informações de imagem que passa um token de imagem e uma região de interesse. Você Obtém o token de informações de uma chamada anterior para/images/Search:

```  
POST https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=en-us HTTP/1.1  
Content-Type: multipart/form-data; boundary=boundary_1234-abcd
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com 

--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.2,
            "bottom" : 0.7,
            "right" : 0.5
        }
    }
}

--boundary_1234-abcd--
```

## <a name="bing-visual-search-responses"></a>Pesquisa Visual do Bing respostas


[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

Se houver informações disponíveis para a imagem, a resposta incluirá uma ou mais `tags` que contêm as informações. O campo `image` contém o token de informações para a imagem de entrada:

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {...},
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

O campo `tags` contém um nome a apresentar e uma lista de ações (informações). Uma das etiquetas contém um campo `displayName`, que está definido como cadeia vazia. Esta etiqueta contém as informações predefinidas, como as páginas Web que incluem a imagem, imagens visualmente semelhantes e origens de compras relativas a artigos na imagem. Como a imagem inteira é de interesse, a marca de informações padrão não inclui caixas delimitadoras para as regiões de interesse:

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {
      "displayName" : "",
      "actions" : [
        {...},
        {...},
        {...},
        {...}
      ]
    },
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

Para obter uma lista de informações padrão, consulte marca de [informações padrão](../default-insights-tag.md).

As restantes etiquetas incluem outras informações que possam ser do interesse do utilizador. Por exemplo, se tiver texto, uma das etiquetas poderá incluir uma informação TextResults, que contém o texto reconhecido. Ou, se o Bing reconhecer uma entidade (ou seja, uma pessoa, lugar ou coisa culturalmente conhecida/popular) na imagem, uma das marcas poderá identificar a entidade. A Pesquisa Visual também devolve um conjunto de diversos termos (etiquetas) que derivam da imagem introduzida. Essas marcas permitem que os usuários explorem os conceitos encontrados na imagem. Por exemplo, se a imagem introduzida for de um atleta famoso, uma das etiquetas pode ser Desporto e incluir ligações para imagens de desporto.

Cada etiqueta inclui um nome a apresentar que pode ser utilizado para categorizar a informação, uma caixa delimitadora que identifica a região de interesse à qual a informação se aplica, as informações propriamente ditas e uma miniatura da imagem. Por exemplo, se for uma imagem de uma pessoa a utilizar uma t-shirt de desporto, uma das etiquetas poderá incluir uma caixa delimitadora que limite a t-shirt e que inclui as informações VisualSearch e ProductVisualSearch. E outra etiqueta poderá incluir uma informação ImageResults que contém um URL para um pedido de API /images/search para obter imagens cujos temas estejam relacionados com o URL de uma pesquisa em Bing.com que direciona o utilizador para os resultados da pesquisa de imagens de Bing.com.

Todas as etiquetas, para além da etiqueta de informações predefinida, incluem caixas delimitadoras que identificam regiões de interesse na imagem. Por exemplo, se a imagem incluir várias pessoas reconhecidas, as etiquetas podem ter caixas delimitadoras para cada uma delas ou, se tiver diversos artigos de vestuário reconhecidos, as etiquetas podem incluir caixas delimitadoras para cada artigo de vestuário. Pode utilizar as caixas delimitadoras para criar pontos ativos na imagem. Quando o utilizador clica num desses pontos, recebe detalhes dos conteúdos nessa região da imagem. Não deve incluir pontos ativos em caixas delimitadoras que identifiquem a imagem inteira.

### <a name="text-recognition"></a>Reconhecimento de texto

Se a imagem tiver texto que o serviço reconhece, uma das etiquetas incluirá uma informação TextResults (ação). A `displayName` do Insight contém o texto reconhecido:

```json
    {
        "image" : {
            "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23Text..."
        },
        "displayName" : "##TextRecognition",
        "boundingBox" : {
            "queryRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            },
            "displayRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            }
        },
        "actions" : [{
            "displayName" : "WALK BIKE ACROSS BRIDGE",
            "actionType" : "TextResults"
        }],
        "sources" : ["OCR"]
    }
```

Uma vez que o campo `displayName` da etiqueta tem #TextRecognition, não o utilize num nome de categoria na experiência do utilizador. O mesmo se aplica a todos os nomes a apresentar que comecem por ##. Em vez disso, use o nome de exibição da ação.

O reconhecimento de texto também pode reconhecer as informações de contacto em cartões de visita, como números de telefone e endereços de e-mail. A caixa delimitadora identifica a localização das informações de contacto no cartão.

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        }
      },
      "actions" : [
        {
          "url" : "tel:888%20555%201212",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        }
      },
      "actions" : [
        {
          "url" : "mailto:someone@outlook.com",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        }
      },
      "actions" : [
        {
          "displayName" : "CHARLENE WHITNEY Graphic Designer 888 555 1212 someone@outlook.com www.contoso.com",
          "actionType" : "TextResults"
        }
      ],
      "sources" : ["OCR"]
    }
```

Se a imagem contiver uma entidade reconhecida como uma pessoa, lugar ou coisa culturalmente conhecida/popular, uma das marcas poderá incluir um insight de entidade. Os campos `mainEntity` e `data` só estarão disponíveis se o atributo `enableEntityData` no cabeçalho `Content-Type` estiver definido como `true`.

```json
{
  "image" : {
    "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Statue+of+Liberty..."
  },
  "displayName" : "Statue of Liberty",
  "boundingBox" : {
    "queryRectangle" : {
      "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
      "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
      "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
      "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
    },
    "displayRectangle" : {
      "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
      "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
      "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
      "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
    }
  },
  "actions" : [
    {
      "_type" : "ImageEntityAction",
      "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Statue+of+Liberty",
      "displayName" : "Statue of Liberty",
      "actionType" : "Entity",
      "mainEntity" : {
        "name" = "Statue of liberty",
        "bingId" : "..."
      },
      "data" : {
        "id" : "https://api.cognitive.microsoft.com/api/v7/entities/...",
        "readLink": "https://www.bingapis.com/api/v7/search?q=...",
        "readLinkPingSuffix": "...",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
                "name": "CC-BY-SA",
                "url": "http://creativecommons.org/licenses/by-sa/3.0/",
                "urlPingSuffix": "..."
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "http://en.wikipedia.org/wiki/...",
            "urlPingSuffix": "..."
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q=...",
        "webSearchUrlPingSuffix": "...",
        "name": "Statue of Liberty",
        "image": {
          "thumbnailUrl": "https://tse1.mm.bing.net/th?id=...",
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/...",
          "hostPageUrlPingSuffix": "...",
          "width": 50,
          "height": 50,
          "sourceWidth": 474,
          "sourceHeight": 598
        },
        "description" : "...",
        "bingId": "..."
        }
      }
  ]
}
```

## <a name="see-also"></a>Ver também

- [O que é o API da Pesquisa Visual do Bing?](../overview.md)
- [Tutorial: criar um aplicativo Web de página única Pesquisa Visual](../tutorial-bing-visual-search-single-page-app.md)
