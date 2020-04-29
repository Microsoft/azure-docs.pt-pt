---
title: 'Características: Ação e contexto - Personalizer'
titleSuffix: Azure Cognitive Services
description: O Personalizer utiliza funcionalidades, informações sobre ações e contexto, para fazer melhores sugestões de classificação. As características podem ser muito genéricas, ou específicas de um item.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 408501232891a7971d03c89acc647d9ed19609b3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77026154"
---
# <a name="features-are-information-about-actions-and-context"></a>Características são informações sobre ações e contexto

O serviço Personalizer funciona aprendendo o que a sua aplicação deve mostrar aos utilizadores num determinado contexto.

O Personalizer utiliza **funcionalidades,** que são informações sobre o **contexto atual** para escolher a melhor **ação.** As funcionalidades representam toda a informação que acha que pode ajudar a personalizar para obter recompensas mais elevadas. As características podem ser muito genéricas, ou específicas de um item. 

Por exemplo, pode ter uma **característica** sobre:

* A _persona utilizadora,_ como a `Sports_Shopper`. Isto não deve ser um ID individual do utilizador. 
* O _conteúdo,_ como se `Documentary`um `Movie`vídeo é `TV Series`um , a , ou a , ou se um artigo de retalho está disponível na loja.
* O _current_ atual período de tempo, como o dia da semana.

O personalizer não prescreve, limita ou corrige quais as funcionalidades que pode enviar para ações e contexto:

* Pode enviar algumas funcionalidades para algumas ações e não para outras, se não as tiver. Por exemplo, as séries de TV podem ter atributos que os filmes não têm.
* Pode ter algumas funcionalidades disponíveis apenas algumas vezes. Por exemplo, uma aplicação móvel pode fornecer mais informações do que uma página web. 
* Com o tempo, poderá adicionar e remover funcionalidades sobre contexto e ações. O personalizer continua a aprender com a informação disponível.
* Deve haver pelo menos uma característica para o contexto. O personalizer não suporta um contexto vazio. Se enviar apenas um contexto fixo todas as vezes, o Personalizer escolherá a ação para classificações apenas no que diz respeito às funcionalidades das ações.
* Para características categóricas, não é necessário definir os valores possíveis, e não precisa de definir as gamas para valores numéricos.

## <a name="supported-feature-types"></a>Tipos de funcionalidades suportados

Personalizer suporta características de tipos de cordas, numéricos e booleanos.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Como a escolha do tipo de funcionalidade afeta machine learning no Personalizer

* **Cadeias**: Para tipos de cordas, cada combinação de chave e valor cria novos pesos no modelo de aprendizagem automática Personalizer. 
* **Numérico:** Deve utilizar valores numéricos quando o número deve afetar proporcionalmente o resultado da personalização. Este é muito dependente do cenário. Num exemplo simplificado, por exemplo, ao personalizar uma experiência de retalho, o NumberOfPetsOwned pode ser uma funcionalidade que é numérica, pois pode querer que pessoas com 2 ou 3 animais de estimação influenciem o resultado da personalização duas ou três vezes tanto quanto ter 1 animal de estimação. As características que são baseadas em unidades numéricas mas onde o significado não é linear - como idade, temperatura ou altura da pessoa - são melhor codificadas como cordas, e a qualidade da funcionalidade pode normalmente ser melhorada usando intervalos. Por exemplo, a Idade pode ser codificada como "Age":"0-5", "Age":"6-10", etc.
* **Valores booleanos** enviados com valor de "falso" ato como se não tivessem sido enviados.

As funcionalidades que não estão presentes devem ser omitidas do pedido. Evite enviar funcionalidades com valor nulo, pois será processado como existente e com um valor de "nulo" na formação do modelo.

## <a name="categorize-features-with-namespaces"></a>Categorize características com espaços de nome

Personalizer acolhe funcionalidades organizadas em espaços de nome. Você determina, na sua aplicação, se os espaços de nome são usados e o que devem ser. Os espaços de nome são usados para agrupar funcionalidades sobre um tópico semelhante, ou características que vêm de uma determinada fonte.

Seguem-se exemplos de espaços de nome de recurso utilizados pelas aplicações:

* User_Profile_from_CRM
* Hora
* Mobile_Device_Info
* http_user_agent
* Resolução de Vídeo
* UserDeviceInfo
* Meteorologia
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

Pode nomear espaços de nome sinuosos seguindo as suas próprias convenções, desde que sejam chaves JSON válidas. Espaços de nome são usados para organizar funcionalidades em conjuntos distintos, e para desambiguar características com nomes semelhantes. Pode pensar nos espaços de nome como um 'prefixo' que é adicionado aos nomes de recurso. Espaços de nome não podem ser aninhados.


No seguinte `user`JSON, `state`e `device` são espaços de nome sinuoso. 

> [!Note]
> Atualmente recomendamos vivamente o uso de nomes para espaços de nome sinuosos que são baseados em UTF-8 e começam com letras diferentes. Por `user`exemplo, `state`e `device` comece `u` `s`com, `d`e . Atualmente ter espaços com nomes com os mesmos primeiros caracteres pode resultar em colisões em índices utilizados para aprendizagem automática.

Os objetos JSON podem incluir objetos JSON aninhados e propriedade/valores simples. Uma matriz só pode ser incluída se os itens da matriz forem números. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "profileType":"AnonymousUser",
                "latlong": [47.6, -122.1]
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
                "Windows":true
            }
        }
    ]
}
```

### <a name="restrictions-in-character-sets-for-namespaces"></a>Restrições em conjuntos de caracteres para espaços de nome

A corda que usa para nomear o espaço de nome deve seguir algumas restrições: 
* Não pode ser unicódigo.
* Pode utilizar alguns dos símbolos imprimíveis com códigos < 256 para os nomes do espaço de nome. 
* Não é possível utilizar símbolos com códigos < 32 (não imprimíveis), 32 (espaço), 58 (cólon), 124 (tubo) e 126-140.
* Não deve começar com um sublinhado "_" ou a funcionalidade será ignorada.

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Como tornar os conjuntos de funcionalidades mais eficazes para o Personalizer

Um bom conjunto de funcionalidades ajuda o Personalizer a aprender a prever a ação que conduzirá a maior recompensa. 

Considere enviar funcionalidades para a API de Classificação Personalizada que seguem estas recomendações:

* Há características suficientes para impulsionar a personalização. Quanto mais precisamente o conteúdo precisa ser, mais funcionalidades são necessárias.

* Existem características *suficientes de diversas densidades.* Uma característica é *densa* se muitos itens estiverem agrupados em alguns baldes. Por exemplo, milhares de vídeos podem ser classificados como "Longo" (mais de 5 min de comprimento) e "Curto" (menos de 5 min de comprimento). Esta é uma característica *muito densa.* Por outro lado, os mesmos milhares de itens podem ter um atributo chamado "Título", que quase nunca terá o mesmo valor de um item para outro. Esta é uma característica muito não densa ou *escassa.*  

Ter características de alta densidade ajuda o Personalizer a extrapolar a aprendizagem de um item para outro. Mas se houver apenas algumas funcionalidades e forem demasiado densas, o Personalizer tentará precisamente direcionar o conteúdo com apenas alguns baldes para escolher.

### <a name="improve-feature-sets"></a>Melhorar conjuntos de funcionalidades 

Analise o comportamento do utilizador fazendo uma Avaliação Offline. Isto permite-lhe olhar para dados passados para ver quais as funcionalidades que estão a contribuir fortemente para recompensas positivas em relação às que estão a contribuir menos. Pode ver quais as funcionalidades que estão a ajudar, e caberá a si e à sua aplicação encontrar melhores funcionalidades para enviar ao Personalizer para melhorar ainda mais os resultados.

Estas secções seguintes são práticas comuns para melhorar as funcionalidades enviadas ao Personalizer.

#### <a name="make-features-more-dense"></a>Tornar as características mais densas

É possível melhorar os seus conjuntos de funcionalidades editando-os para torná-los maiores e mais ou menos densos.

Por exemplo, um carimbo de tempo até ao segundo é uma característica muito escassa. Poderia tornar-se mais denso (eficaz) classificando os tempos em "manhã", "meio-dia", "tarde", etc.

A informação de localização também beneficia tipicamente da criação de classificações mais amplas. Por exemplo, uma coordenada latitude-longitude como Lat: 47.67402° N, Longo: 122.12154° W é muito preciso, e força o modelo a aprender latitude e longitude como dimensões distintas. Quando se está a tentar personalizar com base em informações de localização, ajuda a agrupar informações sobre localização em sectores maiores. Uma maneira fácil de fazer isso é escolher uma precisão de arredondamento apropriada para os números Lat-Long, e combinar latitude e longitude em "áreas" tornando-os em uma corda. Por exemplo, uma boa maneira de representar 47.67402° N, Longo: 122.12154° W em regiões com cerca de alguns quilómetros de largura seria "localização":"34,3 , 12,1".


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Expandir conjuntos de funcionalidades com informações extrapoladas

Também pode obter mais funcionalidades pensando em atributos inexplorados que podem ser derivados da informação que já tem. Por exemplo, numa personalização fictícia da lista de filmes, é possível que um fim de semana vs dia da semana provoque comportamentos diferentes dos utilizadores? O tempo pode ser alargado para ter um atributo de "fim de semana" ou "dia da semana". As férias culturais nacionais atraem a atenção para certos tipos de filmes? Por exemplo, um atributo de "Halloween" é útil em lugares onde é relevante. É possível que o tempo chuvoso tenha um impacto significativo na escolha de um filme para muitas pessoas? Com tempo e lugar, um serviço meteorológico poderia fornecer essa informação e você pode adicioná-lo como uma característica extra. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Expandir conjuntos de funcionalidades com inteligência artificial e serviços cognitivos

Inteligência Artificial e Serviços Cognitivos prontos a funcionar podem ser uma adição muito poderosa ao Personalizer. 

Ao pré-processamento dos seus itens utilizando serviços de inteligência artificial, pode extrair automaticamente informações que são suscetíveis de serem relevantes para a personalização.

Por exemplo:

* Você pode executar um arquivo de filme via [Indexer](https://azure.microsoft.com/services/media-services/video-indexer/) de vídeo para extrair elementos de cena, texto, sentimento e muitos outros atributos. Estes atributos podem então ser tornados mais densos para refletir características que os metadados de item originais não tinham. 
* As imagens podem ser executadas através da deteção de objetos, rostos através do sentimento, etc.
* A informação em texto pode ser aumentada através da extração de entidades, sentimento, ampliação de entidades com gráfico de conhecimento Bing, etc.

Você pode usar vários outros [Serviços Cognitivos Azure,](https://www.microsoft.com/cognitive-services)como

* [Ligar à Entidade](../entitylinking/home.md)
* [Análise de Texto](../text-analytics/overview.md)
* [Emoções](../emotion/home.md)
* [Imagem Digitalizada](../computer-vision/home.md)

## <a name="actions-represent-a-list-of-options"></a>As ações representam uma lista de opções

Cada ação:

* Tem uma identificação de _evento._ Se já tem uma identificação do evento, deve submeter isso. Se não tiver um ID de evento, não envie um, o Personalizer cria um para si e devolve-o na resposta ao pedido de Rank. O ID está associado ao evento Rank, não ao utilizador. Se criar uma identificação, um GUID funciona melhor. 
* Tem uma lista de características.
* A lista de funcionalidades pode ser grande (centenas), mas recomendamos avaliar a eficácia da funcionalidade para remover funcionalidades que não contribuem para obter recompensas. 
* As características das **ações** podem ou não ter qualquer correlação com as características no **contexto** utilizado pela Personalizer.
* As funcionalidades para ações podem estar presentes em algumas ações e não noutras. 
* As funcionalidades para um determinado ID de ação podem estar disponíveis um dia, mas mais tarde ficam indisponíveis. 

Os algoritmos de aprendizagem automática do Personalizer terão um melhor desempenho quando houver conjuntos de funcionalidades estáveis, mas as chamadas rank não falharão se o conjunto de funcionalidades mudar ao longo do tempo.

Não envie mais de 50 ações quando as ações do Ranking. Estas podem ser as mesmas 50 ações todas as vezes, ou podem mudar. Por exemplo, se tiver um catálogo de produtos de 10.000 itens para uma aplicação de e-commerce, poderá utilizar uma recomendação ou motor de filtragem para determinar o top 40 que um cliente pode gostar, e usar o Personalizer para encontrar o que irá gerar mais recompensa (por exemplo, o utilizador irá adicionar ao cabaz) para o contexto atual.


### <a name="examples-of-actions"></a>Exemplos de ações

As ações que enviar para a API de grau dependerá do que está a tentar personalizar.

Eis alguns exemplos:

|Objetivo|Ação|
|--|--|
|Personalize qual o artigo que é destacado num site de notícias.|Cada ação é um artigo de notícias em potencial.|
|Otimizar a colocação de anúncios num site.|Cada ação será um layout ou regras para criar um layout para os anúncios (por exemplo, no topo, à direita, pequenas imagens, grandes imagens).|
|Exiba classificação personalizada de itens recomendados num site de compras.|Cada ação é um produto específico.|
|Sugira que elementos de interface do utilizador, como filtros, se apliquem a uma foto específica.|Cada ação pode ser um filtro diferente.|
|Escolha a resposta de um chat bot para clarificar a intenção do utilizador ou sugerir uma ação.|Cada ação é uma opção de como interpretar a resposta.|
|Escolha o que mostrar no topo de uma lista de resultados de pesquisa|Cada ação é um dos poucos resultados de pesquisa.|


### <a name="examples-of-features-for-actions"></a>Exemplos de funcionalidades para ações

Seguem-se bons exemplos de funcionalidades para ações. Estes dependerão muito de cada aplicação.

* Características com características das ações. Por exemplo, é um filme ou uma série de televisão?
* Funcionalidades sobre como os utilizadores podem ter interagido com esta ação no passado. Por exemplo, este filme é visto principalmente por pessoas na demografia A ou B, é normalmente jogado não mais do que uma vez.
* Características sobre as *características* de como o utilizador vê as ações. Por exemplo, o cartaz do filme mostrado na miniatura inclui rostos, carros ou paisagens?

### <a name="load-actions-from-the-client-application"></a>Ações de carga da aplicação do cliente

As funcionalidades de ações podem normalmente vir de sistemas de gestão de conteúdos, catálogos e sistemas de recomendação. A sua aplicação é responsável pelo carregamento da informação sobre as ações das bases de dados e sistemas relevantes que tem. Se as suas ações não mudarem ou as carregarem sempre que tiver um impacto desnecessário no desempenho, pode adicionar lógica na sua aplicação para cache esta informação.

### <a name="prevent-actions-from-being-ranked"></a>Impedir que as ações sejam classificadas

Em alguns casos, existem ações que não pretende apresentar aos utilizadores. A melhor maneira de evitar que uma ação seja classificada como a mais alta é não incluí-la na lista de ação para a API rank em primeiro lugar.

Em alguns casos, só pode ser determinado mais tarde na sua lógica de negócio se uma _ação_ resultante de uma chamada De API de Rank for mostrada a um utilizador. Para estes casos, deve utilizar _Eventos Inativos._

## <a name="json-format-for-actions"></a>Formato JSON para ações

Ao ligar para rank, enviará múltiplas ações para escolher:

Os objetos JSON podem incluir objetos JSON aninhados e propriedade/valores simples. Uma matriz só pode ser incluída se os itens da matriz forem números. 

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

A informação para o _contexto_ depende de cada aplicação e caso de utilização, mas normalmente pode incluir informações como:

* Informações demográficas e de perfil sobre o seu utilizador.
* Informações extraídas de cabeçalhos HTTP, tais como agente de utilizador, ou derivadas de informações http tais como pesquisas geográficas inversas com base em endereços IP.
* Informações sobre o tempo atual, como dia da semana, fim de semana ou não, manhã ou tarde, época de férias ou não, etc.
* Informações extraídas de aplicações móveis, tais como localização, movimento ou nível de bateria.
* Agregados históricos do comportamento dos utilizadores - como quais são os géneros cinematográficos que este utilizador mais viu.

A sua aplicação é responsável pelo carregamento da informação sobre o contexto a partir das bases de dados, sensores e sistemas relevantes que possa ter. Se as suas informações de contexto não mudarem, pode adicionar lógica na sua aplicação para cache esta informação, antes de enviá-la para a API rank.

## <a name="json-format-for-context"></a>Formato JSON para contexto 

O contexto é expresso como um objeto JSON que é enviado para a API de grau:

Os objetos JSON podem incluir objetos JSON aninhados e propriedade/valores simples. Uma matriz só pode ser incluída se os itens da matriz forem números. 

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
