---
title: Melhores práticas para O Azure Maps Route Service no Microsoft Azure Maps
description: Saiba como encaminhar os veículos utilizando o Serviço de Rota do Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 1c108c79cafb591dced6f6be0dd5c1b353ddac45
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90086407"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Melhores práticas para o serviço Azure Maps Route

As APIs da Rota e da Matriz de Rotas no [Serviço de Rota](https://docs.microsoft.com/rest/api/maps/route) de Mapas Azure podem ser utilizadas para calcular os horários estimados de chegada (ETAs) para cada rota solicitada. As APIs da Rota consideram fatores como informações de tráfego em tempo real e dados históricos de tráfego, como as velocidades típicas da estrada no dia da semana e hora do dia solicitados. As APIs devolvem as rotas mais curtas ou rápidas disponíveis para vários destinos de cada vez em sequência ou em ordem otimizada, com base no tempo ou na distância. Os utilizadores também podem solicitar rotas especializadas e detalhes para caminhantes, ciclistas e veículos comerciais como camiões. Neste artigo, partilharemos as melhores práticas para ligar para o Azure Maps [Route Service](https://docs.microsoft.com/rest/api/maps/route), e aprenderá como:

> [!div class="checklist"]
> * Escolha entre as ApIs de Rota e a API de Encaminhamento De Matriz
> * Solicitar tempos de viagem históricos e previstos, com base nos dados de tráfego históricos e em tempo real
> * Solicite detalhes da rota, como tempo e distância, para todo o percurso e cada etapa do percurso
> * Rota de pedido para um veículo comercial, como um caminhão
> * Solicite informações de tráfego ao longo de uma rota, como engarrafamentos e informações sobre portagens
> * Solicite uma rota que consista em uma ou mais paragens (pontos de passagem)
> * Otimize uma rota de uma ou mais paragens para obter a melhor ordem para visitar cada paragem (waypoint)
> * Otimize rotas alternativas utilizando pontos de apoio. Por exemplo, oferecer rotas alternativas que passam por um posto de carregamento de veículos elétricos.
> * Utilize o [Serviço de Rota](https://docs.microsoft.com/rest/api/maps/route) com o Azure Maps Web SDK

## <a name="prerequisites"></a>Pré-requisitos

1. [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição.

Para obter mais informações sobre a cobertura do Serviço de Rota, consulte a [Cobertura de Encaminhamento](routing-coverage.md).

Este artigo utiliza a [app Postman](https://www.postman.com/downloads/) para construir chamadas REST, mas pode escolher qualquer ambiente de desenvolvimento da API.

## <a name="choose-between-route-directions-and-matrix-routing"></a>Escolha entre Direções de Rota e Encaminhamento de Matriz

As apis de rota, incluindo o tempo de viagem e as coordenadas para um caminho de rota. A Rota Matrix API permite calcular o tempo e distâncias de viagem para um conjunto de rotas que são definidas por origem e locais de destino. Para cada origem, a API Matriz calcula o custo (tempo de viagem e distância) do encaminhamento dessa origem para todos os destinos. Todas estas APIs permitem especificar parâmetros como a hora de partida desejada, os horários de chegada e o tipo de veículo, como carro ou caminhão. Todos eles utilizam dados de tráfego em tempo real ou preditivos em conformidade para devolver as rotas mais ideais.

Considere chamar APIs de Direções de Rota se o seu cenário for:

* Solicite a rota de condução mais curta ou rápida entre dois ou mais locais conhecidos, para obter horários precisos de chegada para os seus veículos de entrega.
* Solicite orientação detalhada da rota, incluindo geometria da rota, para visualizar rotas no mapa
* Dada a lista de localizações do cliente, calcule a rota mais curta possível para visitar cada localização do cliente e regressar à origem. Este cenário é comumente conhecido como o problema do vendedor itinerante. Pode passar até 150 pontos de passagem (paragens) num único pedido.
* Envie lotes de consultas para a API do Lote de Rota usando apenas uma chamada API.

Considere chamar a API de encaminhamento de matriz se o seu cenário for:

* Calcular o tempo de viagem ou distância entre um conjunto de origens e destinos. Por exemplo, você tem 12 motoristas e você precisa encontrar o motorista mais próximo disponível para pegar a entrega de comida no restaurante.
* Ordenar rotas potenciais pela sua distância ou tempo de viagem real. A API Matrix devolve apenas tempos e distâncias de viagem para cada combinação de origem e destino.
* Dados do cluster baseados no tempo de viagem ou distâncias. Por exemplo, a sua empresa tem 50 colaboradores, encontre todos os colaboradores que vivem dentro de 20 minutos de drive time do seu escritório.

Aqui está uma comparação para mostrar algumas capacidades das Direções de Rota e APIs matriz:

| Azure Maps API | Número máximo de consultas no pedido | Evitar áreas | Encaminhamento de caminhão e veículo elétrico | Waypoints e otimização do Vendedor Itinerante | Pontos de apoio |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| Obter Direções de Rota | 1 | | X | X | |
| Direções da Rota dos Correios | 1 | X | X | X | X |
| Lote de Direções de Rota pós-rota | 700 | | X | X | |
| Matriz de rota pós-rota | 700 | | X | | |

Para saber mais sobre as capacidades de encaminhamento de veículos elétricos, consulte o nosso tutorial sobre como [encaminhar veículos elétricos usando Cadernos Azure com Python.](tutorial-ev-routing.md)

## <a name="request-historic-and-real-time-data"></a>Solicitar dados históricos e em tempo real

Por predefinição, o serviço Route assume que o modo de viagem é um carro e a hora de partida é agora. Devolve a rota com base em condições de tráfego em tempo real, a menos que um pedido de cálculo da rota especifique o contrário. As restrições de tráfego dependentes do tempo fixo, como "As curvas à esquerda não são permitidas entre as 16:00 e as 18:00" são capturadas e serão consideradas pelo motor de encaminhamento. Os encerramentos de estradas, como obras rodoviárias, serão considerados a menos que solicite especificamente uma rota que ignore o tráfego vivo atual. Para ignorar o tráfego atual, definido `traffic` no seu pedido de `false` API.

O valor **de cálculo de rotaTimeInSeconds** inclui o atraso devido ao tráfego. É gerado aproveitando os dados atuais e históricos do tempo de viagem, quando a hora de partida está definida para agora. Se a sua hora de partida for definida no futuro, as APIs regressam previstas para os tempos de viagem com base em dados históricos.

Se incluir o **parâmetro computeTravelTimeFor=todos os** parâmetros no seu pedido, então o elemento resumo na resposta terá os seguintes campos adicionais, incluindo condições históricas de tráfego:

| Elemento | Descrição|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | Tempo estimado de viagem calculado como se não houvesse atrasos na rota devido às condições de tráfego, por exemplo, devido ao congestionamento |
| historicTrafficTravelTimeInSeconds | Tempo estimado de viagem calculado com base em dados históricos dependentes do tempo |
| liveTrafficIncidentsTravelTimeInSeconds | Tempo estimado de viagem calculado com base em dados de velocidade em tempo real |

As secções seguintes demonstram como fazer chamadas para as APIs da Rota utilizando os parâmetros discutidos.

### <a name="sample-query"></a>Consulta de exemplo

No primeiro exemplo abaixo, a hora de partida é definida para o futuro, no momento da escrita.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

A resposta contém um elemento sumário, como o abaixo. Como a hora de partida está definida para o futuro, o **valor do TráfegoDelasSeconds** é zero. O valor **de TravelTimeInSeconds** é calculado usando dados históricos dependentes do tempo. Assim, neste caso, o valor **de travelTimeInSeconds** é igual ao valor **históricoTrafficTravelTimeInSeconds.**

```json
"summary": {
    "lengthInMeters": 2131,
    "travelTimeInSeconds": 248,
    "trafficDelayInSeconds": 0,
    "departureTime": "2025-03-29T08:00:20Z",
    "arrivalTime": "2025-03-29T08:04:28Z",
    "noTrafficTravelTimeInSeconds": 225,
    "historicTrafficTravelTimeInSeconds": 248,
    "liveTrafficIncidentsTravelTimeInSeconds": 248
},
```

### <a name="sample-query"></a>Consulta de exemplo

No segundo exemplo abaixo, temos um pedido de encaminhamento em tempo real, onde está agora a hora de partida. Não está explicitamente especificado no URL porque é o valor padrão.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

A resposta contém um resumo, como mostrado abaixo. Devido aos congestionamentos, o **valor do tráfegoDelasSegundos** é maior do que zero. É também maior do que **o históricoTrafficTravelTimeInSeconds.**

```json
"summary": {
    "lengthInMeters": 16637, 
    "travelTimeInSeconds": 2905, 
    "trafficDelayInSeconds": 1604, 
    "departureTime": "2020-02-28T01:00:20+00:00",
    "arrivalTime": "2020-02-28T01:48:45+00:00", 
    "noTrafficTravelTimeInSeconds": 872, 
    "historicTrafficTravelTimeInSeconds": 1976, 
    "liveTrafficIncidentsTravelTimeInSeconds": 2905 
},
```

## <a name="request-route-and-leg-details"></a>Solicitar detalhes da rota e da perna

Por predefinição, o serviço Rota devolverá uma série de coordenadas. A resposta conterá as coordenadas que compõem o caminho numa lista chamada `points` . A resposta da rota também inclui a distância desde o início da rota e o tempo decorrido estimado. Estes valores podem ser usados para calcular a velocidade média de toda a rota.

A imagem a seguir mostra o `points` elemento.

![Elemento de pontos](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

Expandir o `point` elemento para ver a lista de coordenadas para o caminho:

![Elemento de pontos expandido](media/how-to-use-best-practices-for-routing/points-list-img.png)

As APIs de Direções de Rota suportam diferentes formatos de instruções que podem ser utilizadas especificando o parâmetro **de instruçõesType.** Para formatar instruções para um fácil processamento do computador, utilize **instruçõesType=codificado**. Use **instruçõesType=marcado** para apresentar instruções como texto para o utilizador. Além disso, as instruções podem ser formatadas como texto onde alguns elementos das instruções estão marcados, e a instrução é apresentada com formatação especial. Para obter mais informações, consulte a [lista de tipos de instruções suportados.](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routeinstructionstype)

Quando as instruções são solicitadas, a resposta devolve um novo elemento denominado `guidance` . O `guidance` elemento contém duas informações: direções turn-by-turn e instruções resumidas.

![Tipo de instruções](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

O `instructions` elemento mantém as instruções turn-by-turn para a viagem, e as `instructionGroups` instruções resumidas. Cada resumo de instruções cobre um segmento da viagem que pode cobrir várias estradas. As APIs podem devolver detalhes para secções de uma rota. como, por exemplo, a gama de coordenadas de um engarrafamento ou a velocidade atual do tráfego.

![Vire por turnos instruções](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

![Instruções Resumidas](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

## <a name="request-a-route-for-a-commercial-vehicle"></a>Solicite uma rota para um veículo comercial

A Azure Maps Encaminhamento APIs suporta encaminhamento de veículos comerciais, abrangendo o encaminhamento de camiões comerciais. As APIs consideram limites especificados. Tal como a altura e o peso do veículo, e se o veículo estiver a transportar carga perigosa. Por exemplo, se um veículo estiver a transportar inflamáveis, o motor de encaminhamento evita certos túneis que se encontram perto de áreas residenciais.

### <a name="sample-query"></a>Consulta de exemplo

O pedido de amostra abaixo consulta uma rota para um caminhão comercial. O camião transporta resíduos perigosos de classe 1.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

A API rota devolve direções que acomodam as dimensões do caminhão e os resíduos perigosos. Pode ler as instruções de rota expandindo o `guidance` elemento.

![Caminhão com hazwaste classe 1](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

### <a name="sample-query"></a>Consulta de exemplo

Mudar a Classe Hazmat dos EUA, a partir da consulta acima, resultará numa rota diferente para acomodar esta mudança.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

A resposta abaixo é para um caminhão que transporta um material perigoso de classe 9, que é menos perigoso do que um material perigoso de classe 1. Quando expandir o `guidance` elemento para ler as direções, vai notar que as direções não são as mesmas. Há mais instruções de rota para o caminhão que transporta material perigoso classe 1.



![Caminhão com hazwaste classe 9](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)



## <a name="request-traffic-information-along-a-route"></a>Solicite informações de tráfego ao longo de uma rota

Com as APIs da Rota dos Mapas Azure, os desenvolvedores podem solicitar detalhes para cada tipo de secção, incluindo o `sectionType` parâmetro no pedido. Por exemplo, pode solicitar a informação de velocidade para cada segmento de engarrafamento. Consulte a [lista de valores para a secção Enviar](https://docs.microsoft.com/rest/api/maps/route/getroutedirections#sectiontype) por isso, em causa,

### <a name="sample-query"></a>Consulta de exemplo

A seguinte consulta define `sectionType` o `traffic` . Solicita as secções que contêm informações de trânsito de Seattle para San Diego.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

A resposta contém as secções adequadas ao tráfego ao longo das coordenadas.

![Secções de tráfego](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

Esta opção pode ser usada para colorir as secções ao renderizar o mapa, como na imagem abaixo: 

![Secções coloridas renderizadas no mapa](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

## <a name="calculate-and-optimize-a-multi-stop-route"></a>Calcular e otimizar uma rota com várias paragens

A Azure Maps fornece atualmente duas formas de otimização de rotas:

* Otimizações com base no tipo de rota solicitado, sem alterar a ordem dos pontos de passagem. Você pode encontrar os [tipos de rota suportados aqui](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routetype)

* Otimização de vendedores viajantes, que altera a ordem dos pontos de passagem para obter a melhor ordem para visitar cada paragem

Para o encaminhamento multi-stop, podem ser especificados até 150 pontos de passagem num único pedido de rota. Os locais de coordenadas de partida e final podem ser os mesmos, como seria o caso de uma viagem de ida e volta. Mas precisa fornecer pelo menos um ponto de passagem adicional para fazer o cálculo da rota. Os pontos de passagem podem ser adicionados à consulta entre as coordenadas de origem e destino.

Se quiser otimizar a melhor ordem para visitar os pontos de passagem dados, então precisa de especificar **o computeBestOrder=verdadeiro**. Este cenário também é conhecido como o problema de otimização do vendedor itinerante.

### <a name="sample-query"></a>Consulta de exemplo

A seguinte consulta solicita o caminho para seis pontos de passagem, com o `computeBestOrder` parâmetro definido para `false` . É também o valor padrão para o `computeBestOrder` parâmetro.

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

A resposta descreve o comprimento do caminho a ser de 140.851 metros, e que levaria 9.991 segundos para percorrer esse caminho.

![Resposta não otimizada](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

A imagem abaixo ilustra o caminho resultante desta consulta. Este caminho é uma rota possível. Não é o caminho ideal baseado no tempo ou na distância.

![Imagem não otimizada](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)



Esta ordem de rota é: 0, 1, 2, 3, 4, 5 e 6.

### <a name="sample-query"></a>Consulta de exemplo

A seguinte consulta solicita o caminho para os mesmos seis pontos de passagem, como na amostra acima. Desta vez, o `computeBestOrder` parâmetro definido para `true` (a otimização do vendedor itinerante).

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

A resposta descreve o comprimento do caminho a ser de 91.814 metros, e que levaria 7.797 segundos para percorrer esse caminho. A distância de viagem e o tempo de viagem são ambos mais baixos aqui porque a API devolveu a rota otimizada.

![Resposta otimizada](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

A imagem abaixo ilustra o caminho resultante desta consulta.

![Imagem otimizada](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

O percurso ideal tem a seguinte ordem de waypoint: 0, 5, 1, 2, 4, 3 e 6.

>[!TIP]
> A informação otimizada da ordem de waypoint do serviço de encaminhamento fornece um conjunto de índices. Estes excluem a origem e os índices de destino. É necessário aumentar estes valores em 1 para ter em conta a origem. Em seguida, adicione o seu destino ao fim para obter a lista completa de waypoint encomendado.

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>Calcular e distorcer rotas alternativas utilizando pontos de apoio

Você pode ter situações em que você quer reconstruir uma rota para calcular rotas zero ou mais alternativas para uma rota de referência. Por exemplo, pode querer mostrar aos clientes rotas alternativas que passam pela sua loja de retalho. Neste caso, você precisa distorcer um local usando pontos de suporte. Aqui estão os passos para distorcer uma localização:

1. Calcular uma rota como-é e obter o caminho a partir da resposta de rota
2. Utilize o caminho de rota para encontrar os locais desejados ao longo ou perto do caminho da rota. Por exemplo, pode utilizar a Azure Maps [Point of Interest API](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) ou consultar os seus próprios dados na sua base de dados.  
3. Encomende os locais com base na distância desde o início da rota
4. Adicione estas localizações como pontos de apoio num novo pedido de rota para a [API das Direções de Rota dos Correios.](https://docs.microsoft.com/rest/api/maps/route/postroutedirections) Para saber mais sobre os pontos de apoio, consulte a documentação da [API das Direções post route.](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints) 

Ao ligar para a [API das Direções de Rota postal,](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)pode definir o tempo mínimo de desvio ou as restrições de distância, juntamente com os pontos de suporte. Use estes parâmetros se quiser oferecer rotas alternativas, mas também quer limitar o tempo de viagem. Quando estes constrangimentos forem utilizados, as rotas alternativas seguirão a rota de referência a partir do ponto de origem durante o tempo ou distância. Por outras palavras, as outras rotas divergem da rota de referência segundo os constrangimentos.

A imagem abaixo é um exemplo de renderização de rotas alternativas com limites de desvio especificados para o tempo e a distância.

![Rotas alternativas](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

## <a name="use-the-routing-service-in-a-web-app"></a>Utilize o serviço de encaminhamento numa aplicação web

O Azure Maps Web SDK fornece um [módulo de serviço.](https://docs.microsoft.com/javascript/api/azure-maps-rest/) Este módulo é uma biblioteca auxiliar que facilita a utilização das APIs REST do Azure Maps em aplicações web ou Node.js, utilizando JavaScript ou TypeScript. O módulo de Serviço pode ser utilizado para tornar as rotas devolvidas no mapa. O módulo determina automaticamente quais a API a utilizar com pedidos GET e POST.

## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte:

> [!div class="nextstepaction"]
> [Serviço de itinerários do Azure Maps](https://docs.microsoft.com/rest/api/maps/route)

> [!div class="nextstepaction"]
> [Como utilizar o módulo de Serviço](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [Mostrar rota no mapa](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [Pacote NPM Azure Maps](https://www.npmjs.com/package/azure-maps-rest  )
