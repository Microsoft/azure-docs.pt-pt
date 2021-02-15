---
title: 'Características: Ação e contexto - Personalizador'
titleSuffix: Azure Cognitive Services
description: O personalizador utiliza funcionalidades, informações sobre ações e contexto, para fazer melhores sugestões de ranking. As funcionalidades podem ser muito genéricas, ou específicas de um item.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: f49abd4ca1cc1ccdcb7ba2b0fab3bad953dede5d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380547"
---
# <a name="features-are-information-about-actions-and-context"></a>As funcionalidades são informações sobre ações e contexto

O serviço Personalizer funciona aprendendo o que a sua aplicação deve mostrar aos utilizadores num determinado contexto.

Personalizer usa **funcionalidades**, que é informação sobre o **contexto atual** para escolher a melhor **ação**. As funcionalidades representam toda a informação que acha que pode ajudar a personalizar para obter recompensas mais elevadas. As funcionalidades podem ser muito genéricas, ou específicas de um item. 

Por exemplo, pode ter uma **característica** sobre:

* A _persona do utilizador,_ tal como a `Sports_Shopper` . Este não deve ser um ID de utilizador individual. 
* O _conteúdo,_ como se um vídeo fosse um `Documentary` , ou um , ou um , ou se um produto de retalho está `Movie` `TV Series` disponível na loja.
* O _atual_ período de tempo, como em que dia da semana é.

O personalizador não prescreve, limita ou corrige as funcionalidades que pode enviar para ações e contexto:

* Pode enviar algumas funcionalidades para algumas ações e não para outras, se não as tiver. Por exemplo, as séries de TV podem ter atributos que os filmes não têm.
* Pode ter algumas funcionalidades disponíveis apenas algumas vezes. Por exemplo, uma aplicação móvel pode fornecer mais informações do que uma página web. 
* Com o tempo, pode adicionar e remover funcionalidades sobre contexto e ações. O personalizar continua a aprender com a informação disponível.
* Deve haver pelo menos uma característica para o contexto. O personalizador não suporta um contexto vazio. Se enviar apenas um contexto fixo de cada vez, o Personalizer escolherá a ação para rankings apenas no que diz respeito às funcionalidades das ações.
* Para funcionalidades categóricas, não é necessário definir os valores possíveis e não precisa de pré-definir gamas para valores numéricos.

## <a name="supported-feature-types"></a>Tipos de funcionalidades suportados

O personaler suporta características de tipos de cordas, numéricos e booleanos. É muito provável que a sua aplicação utilize principalmente funcionalidades de cordas, com algumas exceções.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Como a escolha do tipo de recurso afeta machine learning no personalizar

* **Cordas**: Para tipos de cordas, cada combinação de chave e valor é tratada como uma característica One-Hot (por exemplo, género:"ScienceFiction" e género:"Documentário" criaria duas novas funcionalidades de entrada para o modelo de machine learning.
* **Numérico**: Deve utilizar valores numéricos quando o número é uma magnitude que deve afetar proporcionalmente o resultado da personalização. Este é muito dependente do cenário. Num exemplo simplificado, por exemplo, ao personalizar uma experiência de retalho, o NumberOfPetsOwned pode ser uma característica que é numérica, pois pode querer que pessoas com 2 ou 3 animais de estimação influenciem o resultado da personalização duas ou três vezes mais do que ter um animal de estimação. Características que são baseadas em unidades numéricas mas onde o significado não é linear - como Idade, Temperatura ou Altura da Pessoa - são melhor codificadas como cordas. Por exemplo, DayOfMonth seria uma corda com "1",2"..."..."31". Se tiver muitas categorias A qualidade da funcionalidade pode normalmente ser melhorada utilizando gamas. Por exemplo, a idade pode ser codificada como "Idade":"0-5", "Idade":"6-10", etc.
* **Valores booleano** enviados com valor de ato "falso" como se não tivessem sido enviados.

As características que não estão presentes devem ser omitidas do pedido. Evite enviar funcionalidades com um valor nulo, pois será processado como existente e com um valor "nulo" ao treinar o modelo.

## <a name="categorize-features-with-namespaces"></a>Categorizar características com espaços de nome

Personalizer acolhe funcionalidades organizadas em espaços de nome. Você determina, na sua aplicação, se os espaços de nome são usados e o que devem ser. Os espaços de nome são usados para agrupar funcionalidades sobre um tópico semelhante, ou características que vêm de uma determinada fonte.

Seguem-se exemplos de espaços de nome de características utilizados por aplicações:

* User_Profile_from_CRM
* Hora
* Mobile_Device_Info
* http_user_agent
* VideoResolution
* UserDeviceInfo
* Meteorologia
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

Pode nomear espaços de nome de funcionalidades seguindo as suas próprias convenções, desde que sejam chaves JSON válidas. Os espaços de nome são usados para organizar funcionalidades em conjuntos distintos, e para desambiguar características com nomes semelhantes. Pode pensar nos espaços de nome como um 'prefixo' que é adicionado aos nomes de funcionalidades. Os espaços de nome não podem ser aninhados.


No JSON `user` seguinte, `state` e são espaços de nome de `device` recurso. 

> [!Note]
> Atualmente recomendamos vivamente o uso de nomes para espaços de nome de recurso que são baseados em UTF-8 e começam com letras diferentes. Por `user` exemplo, `state` e começar `device` `u` `s` por, e `d` . Atualmente ter espaços de nome com os mesmos primeiros caracteres pode resultar em colisões em índices usados para machine learning.

Os objetos JSON podem incluir objetos JSON aninhados e propriedades/valores simples. Uma matriz só pode ser incluída se os itens de matriz forem números. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "profileType":"AnonymousUser",
                "latlong": ["47.6,-122.1"]
            }
        },
        {
            "environment": {
                "dayOfMonth": "28",
                "monthOfYear": "8",
                "timeOfDay": "13:00",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true
            }
        },
        {
            "userActivity" : {
                "itemsInCart": 3,
                "cartValue": 250,
                "appliedCoupon": true
            }
        }
    ]
}
```

### <a name="restrictions-in-character-sets-for-namespaces"></a>Restrições em conjuntos de caracteres para espaços de nome

A cadeia que usa para nomear o espaço de nome deve seguir algumas restrições: 
* Não pode ser um unicódigo.
* Pode utilizar alguns dos símbolos imprimíveis com códigos < 256 para os nomes do espaço. 
* Não é possível utilizar símbolos com códigos < 32 (não imprimíveis), 32 (espaço), 58 (cólon), 124 (tubo) e 126-140.
* Não deve começar com um "_" sublinhado ou a funcionalidade será ignorada.

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Como tornar os conjuntos de funcionalidades mais eficazes para o Personaler

Um bom conjunto de funcionalidades ajuda o Personaler a aprender a prever a ação que irá impulsionar a maior recompensa. 

Considere o envio de funcionalidades para a API do Ranking Personalizado que segue estas recomendações:

* Utilize tipos categóricos e de cordas para funcionalidades que não sejam de magnitude. 

* Há características suficientes para impulsionar a personalização. Quanto mais precisamente o conteúdo for necessário, mais funcionalidades são necessárias.

* Existem características suficientes de *diversas densidades.* Uma característica é *densa* se muitos itens estiverem agrupados em alguns baldes. Por exemplo, milhares de vídeos podem ser classificados como "Longo" (mais de 5 min de comprimento) e "Curto" (com menos de 5 minutos de comprimento). Esta é uma característica *muito densa.* Por outro lado, os mesmos milhares de itens podem ter um atributo chamado "Título", que quase nunca terá o mesmo valor de um item para outro. Esta é uma característica muito não densa ou *escassa.*  

Ter características de alta densidade ajuda o Personalizer a extrapolar a aprender de um item para outro. Mas se houver apenas algumas funcionalidades e forem demasiado densas, o Personalizer tentará precisamente direcionar o conteúdo com apenas alguns baldes para escolher.

### <a name="improve-feature-sets"></a>Melhorar os conjuntos de funcionalidades 

Analise o comportamento do utilizador fazendo uma Avaliação Offline. Isto permite-lhe olhar para os dados passados para ver quais as funcionalidades que estão a contribuir fortemente para recompensas positivas versus aquelas que estão a contribuir menos. Pode ver quais as funcionalidades que estão a ajudar, e cabe-lhe a si e à sua aplicação encontrar melhores funcionalidades para enviar ao Personalizer para melhorar ainda mais os resultados.

Estas secções seguintes são práticas comuns para melhorar as funcionalidades enviadas ao Personaler.

#### <a name="make-features-more-dense"></a>Tornar as funcionalidades mais densas

É possível melhorar os seus conjuntos de funcionalidades editando-os para torná-los maiores e mais ou menos densos.

Por exemplo, um tempotamp até ao segundo é uma característica muito escassa. Poderia tornar-se mais denso (eficaz) classificando os tempos em "manhã", "meio-dia", "tarde", etc.

A informação de localização também beneficia tipicamente da criação de classificações mais amplas. Por exemplo, uma coordenada Latitude-Longitude como Lat: 47.67402° N, Long: 122.12154° W é demasiado precisa e força o modelo a aprender latitude e longitude como dimensões distintas. Quando está a tentar personalizar com base nas informações de localização, ajuda a agrupar informações de localização em sectores maiores. Uma maneira fácil de fazer isso é escolher uma precisão de arredondamento adequada para os números Lat-Long, e combinar a latitude e a longitude em "áreas" tornando-os numa única corda. Por exemplo, uma boa forma de representar 47.67402° N, Long: 122.12154° W em regiões com aproximadamente alguns quilómetros de largura seria "localização":"34,3 , 12,1".


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Expandir conjuntos de funcionalidades com informações extrapoladas

Também pode obter mais funcionalidades pensando em atributos inexplorados que podem ser derivados de informações que já possui. Por exemplo, numa personalização fictícia de uma lista de filmes, é possível que um fim de semana vs dia da semana provoque comportamentos diferentes dos utilizadores? O tempo pode ser expandido para ter um atributo de "fim de semana" ou "dia de semana". Os feriados culturais nacionais levam a atenção a certos tipos de filmes? Por exemplo, um atributo "Halloween" é útil em locais onde é relevante. É possível que o tempo chuvoso tenha um impacto significativo na escolha de um filme para muitas pessoas? Com o tempo e o local, um serviço meteorológico pode fornecer essa informação e pode adicioná-la como uma característica extra. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Expandir conjuntos de funcionalidades com inteligência artificial e serviços cognitivos

Inteligência Artificial e Serviços Cognitivos prontos a funcionar podem ser uma adição muito poderosa ao Personaler. 

Ao préprocessar os seus itens usando serviços de inteligência artificial, pode extrair automaticamente informações que são suscetíveis de serem relevantes para a personalização.

Por exemplo:

* Pode executar um ficheiro de filme através [do Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/) para extrair elementos de cena, texto, sentimento e muitos outros atributos. Estes atributos podem então ser tornados mais densos para refletir características que os metadados originais do item não tinham. 
* As imagens podem ser executadas através da deteção de objetos, rostos através de sentimentos, etc.
* A informação em texto pode ser aumentada através da extração de entidades, sentimentos, entidades em expansão com gráfico de conhecimento Bing, etc.

Você pode usar vários outros [Serviços Cognitivos Azure,](https://www.microsoft.com/cognitive-services)como

* [Ligar à Entidade](../text-analytics/index.yml)
* [Análise de Texto](../text-analytics/overview.md)
* [Emoções](../face/overview.md)
* [Imagem Digitalizada](../computer-vision/overview.md)

## <a name="actions-represent-a-list-of-options"></a>As ações representam uma lista de opções

Cada ação:

* Tem uma identificação de _evento._ Se já tem uma identificação do evento, deve submetê-la. Se não tiver um ID do evento, não envie um, Personalizer cria um para si e devolve-o na resposta ao pedido do Rank. O ID está associado ao evento Rank, não ao utilizador. Se criar uma identificação, um GUID funciona melhor. 
* Tem uma lista de funcionalidades.
* A lista de funcionalidades pode ser grande (centenas), mas recomendamos avaliar a eficácia do recurso para remover funcionalidades que não estão a contribuir para obter recompensas. 
* As funcionalidades nas **ações** podem ou não ter qualquer correlação com as funcionalidades no **contexto** utilizado pelo Personalizador.
* As características das ações podem estar presentes em algumas ações e não noutras. 
* As funcionalidades para um determinado ID de ação podem estar disponíveis um dia, mas mais tarde ficam indisponíveis. 

Os algoritmos de machine learning do personalizar terão um melhor desempenho quando houver conjuntos de funcionalidades estáveis, mas as chamadas rank não falharão se o conjunto de funcionalidades mudar ao longo do tempo.

Não envie mais de 50 ações quando classificar ações. Estas podem ser as mesmas 50 ações de cada vez, ou podem mudar. Por exemplo, se tiver um catálogo de produtos de 10.000 itens para uma aplicação de e-commerce, poderá utilizar uma recomendação ou motor de filtragem para determinar o top 40 que um cliente pode gostar, e usar o Personalizer para encontrar o que irá gerar mais recompensa (por exemplo, o utilizador irá adicionar ao cesto) para o contexto atual.


### <a name="examples-of-actions"></a>Exemplos de ações

As ações que envia para a API de grau dependerão do que está a tentar personalizar.

Eis alguns exemplos:

|Objetivo|Ação|
|--|--|
|Personalize qual artigo é destacado num site de notícias.|Cada ação é um artigo de notícias potencial.|
|Otimize a colocação de anúncios em um site.|Cada ação será um layout ou regras para criar um layout para os anúncios (por exemplo, no topo, à direita, pequenas imagens, grandes imagens).|
|Exiba o ranking personalizado de itens recomendados num site de compras.|Cada ação é um produto específico.|
|Sugira elementos de interface do utilizador, tais como filtros, para aplicar a uma foto específica.|Cada ação pode ser um filtro diferente.|
|Escolha a resposta de um chat bot para esclarecer a intenção do utilizador ou sugerir uma ação.|Cada ação é uma opção de interpretação da resposta.|
|Escolha o que mostrar no topo de uma lista de resultados de pesquisa|Cada ação é um dos melhores resultados de pesquisa.|


### <a name="examples-of-features-for-actions"></a>Exemplos de características para ações

Seguem-se bons exemplos de funcionalidades para as ações. Estes dependerão muito de cada aplicação.

* Características com características das ações. Por exemplo, é um filme ou uma série de televisão?
* Funcionalidades sobre como os utilizadores podem ter interagido com esta ação no passado. Por exemplo, este filme é visto principalmente por pessoas na demografia A ou B, é normalmente jogado não mais do que uma vez.
* Características sobre as *características* de como o utilizador vê as ações. Por exemplo, o cartaz do filme mostrado na miniatura inclui rostos, carros ou paisagens?

### <a name="load-actions-from-the-client-application"></a>Ações de carga da aplicação do cliente

As funcionalidades provenientes de ações podem normalmente vir de sistemas de gestão de conteúdos, catálogos e sistemas de recomendação. A sua aplicação é responsável por carregar as informações sobre as ações a partir das bases de dados e sistemas relevantes que tem. Se as suas ações não mudarem ou as carregarem sempre que tiver um impacto desnecessário no desempenho, pode adicionar lógica na sua aplicação para cache desta informação.

### <a name="prevent-actions-from-being-ranked"></a>Evitar que as ações sejam classificadas

Em alguns casos, existem ações que não quer exibir aos utilizadores. A melhor maneira de evitar que uma ação seja classificada como a mais alta é não incluí-la na lista de ação para a Rank API em primeiro lugar.

Em alguns casos, só pode ser determinado mais tarde na sua lógica de negócio se uma _ação_ resultante de uma chamada de API de grau for mostrada a um utilizador. Para estes casos, deve utilizar _Eventos Inativos._

## <a name="json-format-for-actions"></a>Formato JSON para ações

Ao ligar para o Rank, enviará várias ações para escolher:

Os objetos JSON podem incluir objetos JSON aninhados e propriedades/valores simples. Uma matriz só pode ser incluída se os itens de matriz forem números. 

```json
{
    "actions": [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium",
          "grams": [400,800]
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [150, 300, 450]
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [300, 600, 900]
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low",
          "grams": [300, 600]
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ]
}
```

## <a name="examples-of-context-information"></a>Exemplos de informação de contexto

As informações para o _contexto_ dependem de cada aplicação e caso de utilização, mas normalmente podem incluir informações como:

* Informações demográficas e de perfil sobre o seu utilizador.
* Informações extraídas de cabeçalhos HTTP, como agente de utilizador, ou derivadas de informações HTTP, tais como buscas geográficas inversas baseadas em endereços IP.
* Informação sobre a hora atual, como o dia da semana, fim de semana ou não, de manhã ou à tarde, época de férias ou não, etc.
* Informação extraída de aplicações móveis, como localização, movimento ou nível de bateria.
* Agregados históricos do comportamento dos utilizadores - como quais são os géneros cinematográficos que este utilizador mais viu.

A sua aplicação é responsável por carregar a informação sobre o contexto a partir das bases de dados, sensores e sistemas relevantes que poderá ter. Se as informações de contexto não mudarem, pode adicionar lógica na sua aplicação para cache desta informação, antes de enviá-la para a API rank.

## <a name="json-format-for-context"></a>Formato JSON para contexto 

O contexto é expresso como um objeto JSON que é enviado para a API de grau:

Os objetos JSON podem incluir objetos JSON aninhados e propriedades/valores simples. Uma matriz só pode ser incluída se os itens de matriz forem números. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug"
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true,
                "screensize": [1680,1050]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Passos seguintes

[Aprendizagem por reforço](concepts-reinforcement-learning.md)
