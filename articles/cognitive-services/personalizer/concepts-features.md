---
title: 'Funcionalidades: Ação e o contexto - Personalizer'
titleSuffix: Azure Cognitive Services
description: Personalizer utiliza recursos, informações sobre ações e contexto, para fazer uma melhor classificação sugestões. Recursos podem ser muito genérica ou específica para um item.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: edjez
ms.openlocfilehash: 94eaeb6e34e74e1a0f1a3958c23cf33b86c4adcd
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620287"
---
# <a name="features-are-information-about-actions-and-context"></a>Recursos são informações sobre ações e contexto

O serviço de Personalizer funciona ao aprender o que seu aplicativo deve ser apresentado aos utilizadores num determinado contexto.

Utiliza personalizer **funcionalidades**, que é informações sobre o **contexto atual** para escolher o melhor **ação**. As funcionalidades representam todas as informações que acha que podem ajudar a personalizar para alcançar maior remunerações. Recursos podem ser muito genérica ou específica para um item. 

Por exemplo, pode ter uma **funcionalidade** sobre:

* O _usuário_ como um `UserID`. 
* O _conteúdo_ por exemplo, se um vídeo é um `Documentary`, um `Movie`, ou um `TV Series`, ou se um item de varejo está disponível na loja.
* O _atual_ período de tempo, como o dia da semana é.

Personalizer não prescrevem, limitar ou corrigir que funcionalidades pode enviar para ações e contexto:

* Pode enviar alguns recursos para algumas ações e não para outras pessoas, se não os tiver. Por exemplo, as séries de TV podem ter atributos não tem de filmes.
* Pode ter alguns recursos disponíveis apenas algumas vezes. Por exemplo, um aplicativo móvel pode fornecer mais informações do que uma página da web. 
* Ao longo do tempo, pode adicionar e remover recursos sobre o contexto e ações. Personalizer continua a aprender com as informações disponíveis.
* Tem de existir pelo menos um recurso para o contexto. Personalizer não suporta um contexto vazio. Se enviar apenas um contexto fixo sempre, Personalizer irá escolher a ação para classificações só sobre as funcionalidades nas ações. 
* Personalizer irá tentar escolher as ações que funcionam melhor para todos os utilizadores em qualquer altura.

## <a name="supported-feature-types"></a>Tipos de recurso suportados

Personalizer oferece suporte a recursos de tipos de cadeia, numéricos e booleanos.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Como escolher o tipo de recurso afeta o Machine Learning na Personalizer

* **Cadeias de caracteres**: Para tipos de cadeia de caracteres, cada combinação de chave e valor cria pesos de novo no Personalizer modelo de aprendizagem. 
* **Numérico**: Quando o número proporcionalmente deve afetar o resultado de personalização, deve usar valores numéricos. Este é o cenário muito dependente. Um exemplo simplificado por exemplo, quando personalizar um varejo experiência, NumberOfPetsOwned poderia ser um recurso que é numérico, como pode desejar que as pessoas com 2 ou 3 animais de estimação influenciar o resultado de personalização duas vezes ou três vezes por tanto quanto ter 1 animal de estimação. Recursos que se baseiam em unidades numéricas, mas em que o significado não é linear - como idade, temperatura ou pessoa Height - melhor são codificados como cadeias de caracteres e a qualidade da funcionalidade normalmente podem ser melhorada através da utilização de intervalos. Por exemplo, idade poderia ser codificada como "Idade": "0-5", "Idade": "6-10", etc.
* **Booleano** valores enviados com o valor do act "false", como se ainda não haviam sido enviadas em todos os.

Devem ser omitidas funcionalidades que não estão presentes no pedido. Evite o envio de recursos com um valor nulo, porque ele será processado como existente e com um valor de "null" quando o modelo de formação.

## <a name="categorize-features-with-namespaces"></a>Categorizar recursos com espaços de nomes

Personalizer aceita recursos organizados em namespaces. Determinar, no seu aplicativo, se forem utilizados espaços de nomes e o que eles devem ser. Espaços de nomes são utilizados para agrupar recursos sobre um tópico semelhante ou funcionalidades que vêm de uma determinada origem.

Seguem-se exemplos de espaços de nomes de recursos utilizados pelas aplicações:

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

Pode nomear a funcionalidade espaços de nomes seguindo as suas próprias convenções, desde que eles são chaves JSON válidas.

No seguinte JSON `user`, `state`, e `device` são espaços de nomes do recurso.

Objetos JSON podem incluir objetos JSON aninhados e simples/valores de propriedade. Uma matriz pode ser incluída apenas se os itens de matriz são números. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug",
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

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Como tornar a funcionalidade define mais eficiente para Personalizer

Um conjunto de funcionalidades boa ajuda Personalizer Saiba como prever a ação que irá orientar a recompensa mais alta. 

Considere o envio de funcionalidades para a API de classificação de Personalizer que siga as seguintes recomendações:

* Há suficiente recursos à personalização de unidade. Quanto mais direcionada precisamente o conteúdo tem de estar, quanto mais recursos são necessários.

* Há suficiente recursos de diversas *densities*. Uma funcionalidade está *densa* se o número de itens é agrupado em alguns registos. Por exemplo, milhares de vídeos podem ser classificados como "Longo" (mais de 5 min longos) e "Breve" (em 5 min longos). Este é um *muito densa* funcionalidade. Por outro lado, os mesmo milhares de itens podem ter um atributo chamado "Title", que quase nunca terá o mesmo valor de um item para outro. Este é um muito não com muitas ou *dispersas* funcionalidade.  

Contar com recursos de alta densidade ajuda Personalizer extrapolar learning a partir de um item para outro. Mas se há apenas alguns recursos e são muito densos, o Personalizer tentará precisamente conteúdo com apenas alguns registos à sua escolha de destino.

### <a name="improve-feature-sets"></a>Melhorar a conjuntos de recursos 

Analise o comportamento do utilizador ao fazer uma avaliação Offline. Isto permite-lhe ver nos últimos dados para ver o que funcionalidades são bastante que contribuem para positivas remunerações versus aquelas que estiver a contribuir menor. Pode ver o que funcionalidades estão ajudando e caberá a e a sua aplicação para encontrar funcionalidades do melhor para enviar para Personalizer para melhorar os resultados ainda mais.

Estas secções seguintes são as práticas comuns para melhorar as funcionalidades enviadas para Personalizer.

#### <a name="make-features-more-dense"></a>Tornar as funcionalidades mais densa

É possível melhorar seus conjuntos de recursos através da edição dos mesmos para torná-los maiores e mais ou menos densa.

Por exemplo, um carimbo para o segundo é um recurso muito disperso. Que poderia ficar mais densa (em vigor) classificando vezes em "manhã", "midday", "tarde", etc.


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Expanda os conjuntos de recursos com informações extrapolados

Também pode obter mais recursos pensar unexplored atributos que podem derivar de informações que já tem. Por exemplo, numa personalização da lista de filmes fictícia, é possível que um dia da semana do fim de semana vs elicits um comportamento diferente dos utilizadores? Tempo poderia ser expandido para ter um atributo "semana" ou "dia da semana". Feriados nacionais norte-americanos culturais unidade atenção a determinados tipos de filme? Por exemplo, um atributo "Halloween" é útil em locais onde é relevante. É possível que a meteorologia chuvosa tem um impacto significativo na escolha de um filme para muitas pessoas? Com o momento e local, um serviço meteorológico poderia fornecer que as informações e pode adicioná-la como uma funcionalidade extra. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Expanda os conjuntos de recursos com inteligência artificial e os serviços cognitivos

Inteligência artificial e prontos para executar os serviços cognitivos podem ser uma adição muito poderosa para o Personalizer. 

Ao processamento prévio de seus itens com os serviços de inteligência artificial, pode extrair automaticamente as informações que é provável que seja relevante para personalização.

Por exemplo:

* Pode executar um arquivo de filme via [Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/) para extrair elementos de cena, texto, sentimento e muitos outros atributos. Esses atributos, em seguida, podem ser feitos mais densos para refletir as características que os metadados do item original não tinham. 
* Imagens podem ser executadas por meio da detecção de objeto, rostos através de sentimentos, etc.
* Informações em texto podem ser aumentadas mediante a extração de entidades, sentimentos, expandir as entidades com grafo de conhecimento do Bing, etc.

Pode utilizar vários outros [serviços cognitivos do Azure](https://www.microsoft.com/cognitive-services), como

* [Entidade de ligação](../entitylinking/home.md)
* [Análise de Texto](../text-analytics/overview.md)
* [Emoções](../emotion/home.md)
* [Imagem Digitalizada](../computer-vision/home.md)

## <a name="actions-represent-a-list-of-options"></a>Ações representam uma lista de opções

Cada ação:

* Tem um ID.
* Tem uma lista de recursos.
* A lista de recursos pode ser grandes (centenas), mas recomendamos avaliar a eficácia de funcionalidade para remover recursos que não estão a contribuir para obtenção de remunerações. 
* Os recursos da **ações** pode ou não ter qualquer correlação com os recursos da **contexto** utilizado pelo Personalizer.
* Recursos para ações podem estar presentes em algumas ações e não para outros. 
* Recursos para um determinado ID de ação podem ser disponível, um dia, mas mais tarde se tornar indisponíveis. 

Algoritmos de machine do personalizer learning terá melhor desempenho quando existem conjuntos de recursos estável, mas as chamadas de classificação não irão falhar se a funcionalidade de conjunto de alterações ao longo do tempo.

Não enviar em mais de 50 ações quando classificação ações. Estes podem ser as mesmas 50 ações sempre ou elas podem ser alterados. Por exemplo, se tiver um catálogo de produtos de 10 000 itens para uma aplicação de comércio eletrônico, pode usar uma recomendação ou o mecanismo de filtragem para determinar a parte superior 40 pode, como um cliente e utilizar Personalizer para encontrar aquele que irão gerar a maioria dos recompensa (por exemplo o utilizador irá adicionar ao cesto) para o contexto atual.


### <a name="examples-of-actions"></a>Exemplos de ações

As ações que são enviados para a API de classificação dependem o que está a tentar personalizar.

Eis alguns exemplos:

|Objetivo|Ação|
|--|--|
|Personalize o artigo é realçado num Web site de notícias.|Cada ação seja um artigo de notícias potenciais.|
|Otimize a colocação do ad num Web site.|Cada ação será um esquema ou de regras para criar um esquema para o ads (por exemplo, na parte superior, nas imagens pequenas, certas, grandes imagens).|
|Apresente uma classificação personalizada de itens recomendados num site de compra.|Cada ação é um produto específico.|
|Sugerir elementos de interface do usuário, como filtros a aplicar a uma foto específica.|Cada ação pode ser um filtro de diferente.|
|Escolha a resposta de um chatbot para esclarecer a intenção do utilizador ou sugerir uma ação.|Cada ação é uma opção de como interpretar a resposta.|
|Escolha o que mostrar na parte superior de uma lista de resultados da pesquisa|Cada ação é um dos resultados da pesquisa de alguns superior.|


### <a name="examples-of-features-for-actions"></a>Exemplos de recursos para ações

Seguem-se bons exemplos das funcionalidades de ações. Estes dependerá muito cada aplicativo.

* Recursos com características das ações. Por exemplo, é um filme ou uma série de tv?
* Recursos sobre como os utilizadores podem interagido com esta ação no passado. Por exemplo, este filme é principalmente vistos por pessoas na dados demográficos A ou B, é normalmente jogada não mais do que uma vez.
* Recursos sobre as características de como o usuário *vê* as ações. Por exemplo, é que o cartaz para o filme, mostrado os rostos de inclusão em miniatura, em carros ou panoramas?

### <a name="load-actions-from-the-client-application"></a>Ações de carga da aplicação cliente

As funcionalidades de ações, normalmente, podem ter sistemas de recomendador, catálogos e sistemas de gerenciamento de conteúdo. A aplicação é responsável por carregar as informações sobre as ações de bancos de dados relevantes e tiver de sistemas. Se não alterar suas ações ou colocá-los carregados sempre tem um impacto desnecessário no desempenho, pode adicionar lógica na sua aplicação para colocar em cache estas informações.

### <a name="prevent-actions-from-being-ranked"></a>Impedir ações de que está a ser classificados

Em alguns casos, há ações que não pretende apresentar aos utilizadores. A melhor forma de impedir que uma ação que está a ser classificados como superior é não incluí-lo em primeiro lugar na lista de ações para a API de classificação.

Em alguns casos, ele pode ser determinado apenas mais tarde na sua lógica de negócio se um resultando _ação_ de uma API de classificação chamada é para ser mostrado a um utilizador. Nesses casos, deve usar _eventos Inativos_.

## <a name="json-format-for-actions"></a>Formato JSON para ações

Ao chamar a classificação, vai enviar várias ações à sua escolha:

Objetos JSON podem incluir objetos JSON aninhados e simples/valores de propriedade. Uma matriz pode ser incluída apenas se os itens de matriz são números. 

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

## <a name="examples-of-context-information"></a>Exemplos de informações de contexto

Informações para o _contexto_ depende de cada caso de aplicativos e a utilização, mas normalmente pode incluir informações como:

* Informações sobre o utilizador demográficos e perfil.
* Informações extraídas de cabeçalhos HTTP, como o agente de utilizador, ou sejam derivados de informações de HTTP, como pesquisas geográficas inversas, com base em endereços IP.
* Informações sobre a hora atual, tais como o dia da semana, a fim de semana ou não, manhã ou tarde, época festiva ou não, etc.
* Informações extraídas de aplicações móveis, como localização, o movimento ou o nível da bateria.
* Agregações históricas do comportamento de utilizadores – por exemplo, quais são os géneros de filme este utilizador tem mais visualizados.

A aplicação é responsável por carregar as informações sobre o contexto da bases de dados relevantes, sensores e sistemas, que pode ter. Se as informações de contexto não é alterado, pode adicionar lógica na sua aplicação para colocar em cache estas informações, antes de enviá-la para a API de classificação.

## <a name="json-format-for-context"></a>Formato JSON para o contexto 

Contexto é expresso como um objeto JSON que é enviado para a API de classificação:

Objetos JSON podem incluir objetos JSON aninhados e simples/valores de propriedade. Uma matriz pode ser incluída apenas se os itens de matriz são números. 

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

## <a name="next-steps"></a>Passos Seguintes

[Aprendizagem por reforço](concepts-reinforcement-learning.md) 
