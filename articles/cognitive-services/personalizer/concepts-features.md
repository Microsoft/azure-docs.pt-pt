---
title: 'Recursos: ação e contexto-personalizador'
titleSuffix: Azure Cognitive Services
description: O personalizador usa recursos, informações sobre ações e contexto, para fazer sugestões de classificação melhores. Os recursos podem ser muito genéricos ou específicos de um item.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: diberry
ms.openlocfilehash: 2147ca2565d5977e3e47d5182627483aa3d8d1b2
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756111"
---
# <a name="features-are-information-about-actions-and-context"></a>Recursos são informações sobre ações e contexto

O serviço personalizador funciona aprendendo o que seu aplicativo deve mostrar aos usuários em um determinado contexto.

O personalizador usa **recursos**, que são informações sobre o **contexto atual** para escolher a melhor **ação**. Os recursos representam todas as informações que você acredita que podem ajudar a personalizar o para obter recompensas mais altas. Os recursos podem ser muito genéricos ou específicos de um item. 

Por exemplo, você pode ter um **recurso** sobre:

* O _usuário persona_ , como um `Sports_Shopper`. Isso não deve ser uma ID de usuário individual. 
* O _conteúdo_ , como se um vídeo é um `Documentary`, um `Movie` ou um `TV Series`, ou se um item de varejo está disponível no repositório.
* O período de tempo _atual_ , como em qual dia da semana é.

O personalizador não prescreve, limita ou corrige quais recursos você pode enviar para ações e contexto:

* Você pode enviar alguns recursos para algumas ações e não para outros, se você não os tiver. Por exemplo, a série de TV pode ter atributos de filmes não.
* Você pode ter alguns recursos disponíveis apenas algumas vezes. Por exemplo, um aplicativo móvel pode fornecer mais informações do que uma página da Web. 
* Ao longo do tempo, você pode adicionar e remover recursos sobre contexto e ações. O personalizador continua a aprender com as informações disponíveis.
* Deve haver pelo menos um recurso para o contexto. O personalizador não oferece suporte a um contexto vazio. Se você só enviar um contexto fixo toda vez, o personalizador escolherá a ação para classificações somente em relação aos recursos nas ações.
* Para recursos categóricos, não é necessário definir os valores possíveis, e você não precisa definir os intervalos predefinidos para valores numéricos.

## <a name="supported-feature-types"></a>Tipos de recursos com suporte

O personalizador dá suporte a recursos de tipos de cadeia de caracteres, numéricos e boolianos.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Como a escolha do tipo de recurso afeta Machine Learning no Personalizador

* **Strings**: para tipos de cadeia de caracteres, cada combinação de chave e valor cria novos pesos no modelo personalizado de aprendizado de máquina. 
* **Numeric**: você deve usar valores numéricos quando o número deve afetar proporcionalmente o resultado da personalização. Isso depende muito do cenário. Em um exemplo simplificado, por exemplo, ao personalizar uma experiência de varejo, o NumberOfPetsOwned pode ser um recurso que é numérico, pois você pode querer que as pessoas com 2 ou 3 animais de estimação influenciem o resultado da personalização duas vezes ou três vezes por até um animal de estimação. Recursos que são baseados em unidades numéricas, mas em que o significado não é linear, como idade, temperatura ou altura da pessoa, são mais bem codificados como cadeias de caracteres, e a qualidade do recurso pode ser normalmente melhorada usando intervalos. Por exemplo, age pode ser codificada como "Age": "0-5", "Age": "6-10", etc.
* Valores **boolianos** enviados com o valor "false" funcionam como se não tivessem sido enviados.

Os recursos que não estão presentes devem ser omitidos da solicitação. Evite enviar recursos com um valor nulo, pois ele será processado como existente e com um valor de "NULL" ao treinar o modelo.

## <a name="categorize-features-with-namespaces"></a>Categorizar recursos com namespaces

O personalizador leva em conta os recursos organizados em namespaces. Você determina, em seu aplicativo, se os namespaces são usados e o que devem ser. Os namespaces são usados para agrupar recursos sobre um tópico semelhante ou recursos provenientes de uma determinada fonte.

Veja a seguir exemplos de namespaces de recurso usados por aplicativos:

* User_Profile_from_CRM
* Tempo
* Mobile_Device_Info
* http_user_agent
* VideoResolution
* UserDeviceInfo
* MSNBC
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

Você pode nomear namespaces de recursos seguindo suas próprias convenções, desde que elas sejam chaves JSON válidas. Os namespaces são usados para organizar recursos em conjuntos distintos e para desambiguar recursos com nomes semelhantes. Você pode considerar os namespaces como um ' prefix ' que é adicionado aos nomes de recursos. Namespaces não podem ser aninhados.


No JSON, `user`, `state` e `device` a seguir são namespaces de recurso. Observação de visualização pública: no momento, é altamente recomendável usar nomes para namespaces de recurso que são baseados em UTF-8 e começam com letras diferentes. Por exemplo, `user`, `state` e `device` começar com `u`, `s` e `d`. Atualmente, ter namespaces com os mesmos primeiros caracteres pode resultar em colisões em índices usados para aprendizado de máquina.

Os objetos JSON podem incluir objetos JSON aninhados e propriedades/valores simples. Uma matriz só poderá ser incluída se os itens de matriz forem números. 

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

### <a name="restrictions-in-character-sets-for-namespaces"></a>Restrições em conjuntos de caracteres para namespaces

A cadeia de caracteres usada para nomear o namespace deve seguir algumas restrições: 
* Não pode ser Unicode.
* Você pode usar alguns dos símbolos imprimíveis com códigos < 256 para os nomes de namespace. 
* Você não pode usar símbolos com códigos < 32 (não imprimível), 32 (espaço), 58 (dois-pontos), 124 (pipe) e 126 – 140.

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Como tornar os conjuntos de recursos mais eficazes para o personalizador

Um bom conjunto de recursos ajuda o personalizador a saber como prever a ação que orientará o maior prêmio. 

Considere o envio de recursos para a API de classificação do personalizador que seguem estas recomendações:

* Há recursos suficientes para impulsionar a personalização. Quanto mais precisamente o conteúdo for necessário, mais recursos serão necessários.

* Há recursos suficientes de diferentes *densidades*. Um recurso será *denso* se muitos itens estiverem agrupados em alguns buckets. Por exemplo, milhares de vídeos podem ser classificados como "Long" (mais de 5 minutos de duração) e "short" (menos de 5 min). Esse é um recurso *muito denso* . Por outro lado, os mesmos milhares de itens podem ter um atributo chamado "title", que quase nunca terá o mesmo valor de um item para outro. Esse é um recurso muito não denso ou *esparso* .  

Ter recursos de alta densidade ajuda o personalizado a extrapolar o aprendizado de um item para outro. Mas se houver apenas alguns recursos e eles forem muito densos, o personalizador tentará direcionar com precisão o conteúdo com apenas alguns buckets para sua escolha.

### <a name="improve-feature-sets"></a>Melhorar os conjuntos de recursos 

Analise o comportamento do usuário fazendo uma avaliação offline. Isso permite que você examine os dados passados para ver quais recursos estão muito contribuindo para recompensas positivas em comparação com os que estão contribuindo menos. Você pode ver quais recursos estão ajudando e será o seu aplicativo para encontrar recursos melhores a serem enviados ao personalizado para melhorar os resultados ainda mais.

As seções a seguir são práticas comuns para melhorar os recursos enviados ao personalizador.

#### <a name="make-features-more-dense"></a>Tornar os recursos mais densos

É possível melhorar seus conjuntos de recursos editando-os para torná-los maiores e mais ou menos densos.

Por exemplo, um carimbo de data/hora até o segundo é um recurso muito esparso. Ele poderia se tornar mais denso (eficaz) classificando os horários em "manhã", "meio-dia", "à tarde", etc.


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Expandir conjuntos de recursos com informações extrapoladas

Você também pode obter mais recursos pensando em atributos não explorados que podem ser derivados das informações que você já tem. Por exemplo, em uma personalização de lista de filmes fictícias, é possível que um fim de semana em vez de um dia final dos usuários seja diferente do comportamento? O tempo pode ser expandido para ter um atributo "fim de semana" ou "dia da semana". Os feriados culturais nacionais impulsionam a atenção para determinados tipos de filmes? Por exemplo, um atributo "dia das bruxas" é útil em locais onde é relevante. É possível que o clima de chuva tenha um impacto significativo na escolha de um filme para muitas pessoas? Com o tempo e o local, um serviço meteorológico pode fornecer essas informações e você pode adicioná-las como um recurso extra. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Expanda conjuntos de recursos com inteligência artificial e serviços cognitivas

A inteligência artificial e serviços cognitivas prontos para execução podem ser uma adição muito avançada ao personalizador. 

Ao pré-processar seus itens usando serviços de inteligência artificial, você pode extrair automaticamente as informações que provavelmente serão relevantes para personalização.

Por exemplo:

* Você pode executar um arquivo de filme por meio de [Video indexer](https://azure.microsoft.com/services/media-services/video-indexer/) para extrair elementos de cena, texto, sentimentos e muitos outros atributos. Esses atributos podem então ser mais densos para refletir as características que os metadados do item original não tinham. 
* As imagens podem ser executadas por meio de detecção de objetos, rostos por meio de sentimentos, etc.
* As informações no texto podem ser aumentadas extraindo entidades, sentimentos, expandindo entidades com o grafo de conhecimento do Bing etc.

Você pode usar vários outros [Serviços cognitivas do Azure](https://www.microsoft.com/cognitive-services), como

* [Vinculação de entidade](../entitylinking/home.md)
* [Análise de Texto](../text-analytics/overview.md)
* [Emoção](../emotion/home.md)
* [Imagem Digitalizada](../computer-vision/home.md)

## <a name="actions-represent-a-list-of-options"></a>As ações representam uma lista de opções

Cada ação:

* Tem uma ID de _evento_ . Se você já tiver uma ID de evento, deverá enviá-la. Se você não tiver uma ID de evento, não enviar uma, o personalizador criará uma para você e a retornará na resposta da solicitação de classificação. A ID é associada ao evento de classificação, não ao usuário. Se você criar uma ID, um GUID funcionará melhor. 
* Tem uma lista de recursos.
* A lista de recursos pode ser grande (centenas), mas é recomendável avaliar a eficácia do recurso para remover recursos que não estão contribuindo para obter recompensas. 
* Os recursos nas **ações** podem ou não ter qualquer correlação com recursos no **contexto** usado pelo personalizador.
* Recursos para ações podem estar presentes em algumas ações e não em outros. 
* Os recursos para uma determinada ID de ação podem estar disponíveis um dia, mas posteriormente se tornarem indisponíveis. 

Os algoritmos de aprendizado de máquina do personalizador terão melhor desempenho quando houver conjuntos de recursos estáveis, mas as chamadas de classificação não falharão se o conjunto de recursos mudar ao longo do tempo.

Não envie mais de 50 ações ao classificar ações. Essas podem ser as mesmas 50 ações a cada vez ou podem ser alteradas. Por exemplo, se você tiver um catálogo de produtos de 10.000 itens para um aplicativo de comércio eletrônico, você poderá usar um mecanismo de recomendação ou de filtragem para determinar os primeiros 40 a que um cliente pode gostar e usar personalizador para encontrar aquele que gerará mais recompensa (por exemplo, , o usuário será adicionado ao carrinho) para o contexto atual.


### <a name="examples-of-actions"></a>Exemplos de ações

As ações que você envia para a API de classificação dependerão do que você está tentando Personalizar.

Eis alguns exemplos:

|Finalidade|Ação|
|--|--|
|Personalize o artigo que está realçado em um site de notícias.|Cada ação é um artigo de notícias em potencial.|
|Otimizar o posicionamento do AD em um site.|Cada ação será um layout ou regras para criar um layout para os anúncios (por exemplo, na parte superior, à direita, imagens pequenas, imagens grandes).|
|Exiba a classificação personalizada de itens recomendados em um site de compras.|Cada ação é um produto específico.|
|Sugira elementos de interface do usuário, como filtros, a serem aplicados a uma foto específica.|Cada ação pode ser um filtro diferente.|
|Escolha uma resposta de bot de chat para esclarecer a intenção do usuário ou sugerir uma ação.|Cada ação é uma opção de como interpretar a resposta.|
|Escolha o que mostrar na parte superior de uma lista de resultados da pesquisa|Cada ação é um dos primeiros resultados da pesquisa.|


### <a name="examples-of-features-for-actions"></a>Exemplos de recursos para ações

Estes são bons exemplos de recursos para ações. Eles dependerão muito de cada aplicativo.

* Recursos com características das ações. Por exemplo, é um filme ou uma série de TV?
* Recursos sobre como os usuários podem ter interagido com essa ação no passado. Por exemplo, esse filme é visto principalmente por pessoas em dados demográficos A ou B, normalmente não é mais executado do que uma vez.
* Recursos sobre as características de como o usuário *vê* as ações. Por exemplo, o pôster do filme mostrado na miniatura inclui rostos, carros ou cenários?

### <a name="load-actions-from-the-client-application"></a>Carregar ações do aplicativo cliente

Os recursos das ações normalmente podem vir de sistemas de gerenciamento de conteúdo, catálogos e sistemas de recomendação. Seu aplicativo é responsável por carregar as informações sobre as ações dos bancos de dados e sistemas relevantes que você tem. Se suas ações não forem alteradas ou se elas forem carregadas toda vez tiverem um impacto desnecessário no desempenho, você poderá adicionar lógica em seu aplicativo para armazenar essas informações em cache.

### <a name="prevent-actions-from-being-ranked"></a>Impedir que as ações sejam classificadas

Em alguns casos, há ações que você não deseja exibir para os usuários. A melhor maneira de impedir que uma ação seja classificada como superior não é incluí-la na lista de ações para a API de classificação em primeiro lugar.

Em alguns casos, ele só poderá ser determinado posteriormente em sua lógica de negócios se uma _ação_ resultante de uma chamada à API de classificação for ser mostrada para um usuário. Nesses casos, você deve usar _eventos inativos_.

## <a name="json-format-for-actions"></a>Formato JSON para ações

Ao chamar a classificação, você enviará várias ações para escolher:

Os objetos JSON podem incluir objetos JSON aninhados e propriedades/valores simples. Uma matriz só poderá ser incluída se os itens de matriz forem números. 

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

As informações para o _contexto_ dependem de cada aplicativo e caso de uso, mas normalmente podem incluir informações como:

* Informações demográficas e de perfil sobre o usuário.
* Informações extraídas de cabeçalhos HTTP, como agente do usuário, ou derivadas de informações de HTTP, como pesquisas geográficas inversas com base em endereços IP.
* Informações sobre a hora atual, como dia da semana, fim de semana ou não, manhã ou tarde, época de Natal ou não, etc.
* Informações extraídas de aplicativos móveis, como local, movimento ou nível de bateria.
* Agregações históricas do comportamento de usuários – como quais são os gêneros do filme que esse usuário exibiu mais.

Seu aplicativo é responsável por carregar as informações sobre o contexto dos bancos de dados, sensores e sistemas relevantes que você pode ter. Se as informações de contexto não forem alteradas, você poderá adicionar lógica em seu aplicativo para armazenar essas informações em cache antes de enviá-las para a API de classificação.

## <a name="json-format-for-context"></a>Formato JSON para contexto 

O contexto é expresso como um objeto JSON que é enviado para a API de classificação:

Os objetos JSON podem incluir objetos JSON aninhados e propriedades/valores simples. Uma matriz só poderá ser incluída se os itens de matriz forem números. 

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

[Aprendizado de reforço](concepts-reinforcement-learning.md) 
