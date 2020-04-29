---
title: Boas práticas para o Serviço de Rota dos Mapas Azure [ Microsoft Azure Maps
description: Saiba como encaminhar-se de forma eficiente utilizando o Serviço de Rota a partir do Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 85ce29d088b8fbd110988db67776d89346215e5a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335406"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Boas práticas para o serviço Azure Maps Route

As APIs da Rota e da Matriz de Rotas no Serviço de [Rota](https://docs.microsoft.com/rest/api/maps/route) dos Mapas Azure podem ser utilizadas para calcular os tempos estimados de chegada (ETAs) para cada rota solicitada. As APIs da Rota consideram fatores como informações de tráfego em tempo real e dados históricos de tráfego, como as velocidades típicas das estradas no dia solicitado da semana e da hora do dia. As APIs devolvem as rotas mais curtas ou rápidas disponíveis para vários destinos de cada vez em sequência ou em ordem otimizada, com base no tempo ou distância. Os utilizadores também podem solicitar rotas especializadas e detalhes para caminhantes, ciclistas e veículos comerciais como camiões. Neste artigo, partilharemos as melhores práticas para ligar para o Serviço de [Rota](https://docs.microsoft.com/rest/api/maps/route)dos Mapas Azure, e aprenderemos a:

* Escolha entre as Direções de Rota APIs e a API de Encaminhamento matriz
* Solicitar tempos de viagem históricos e previstos, com base em dados de tráfego em tempo real e históricos
* Solicite detalhes da rota, como tempo e distância, para todo o percurso e cada perna do percurso
* Rota de pedido para um veículo comercial, como um caminhão
* Solicite informações de tráfego ao longo de uma rota, como engarrafamentos e informações sobre portagens
* Solicite uma rota que consista em uma ou mais paragens (pontos de passagem)
* Otimize uma rota de uma ou mais paragens para obter a melhor ordem para visitar cada paragem (waypoint)
* Otimize rotas alternativas utilizando pontos de apoio. Por exemplo, ofereça rotas alternativas que passem por uma estação de carregamento de veículos elétricos.
* Utilize o [Serviço de Rota](https://docs.microsoft.com/rest/api/maps/route) com o Azure Maps Web SDK

## <a name="prerequisites"></a>Pré-requisitos

Para fazer chamadas para as APIs do Azure Maps, precisa de uma conta Azure Maps e de uma chave. Para mais informações, consulte [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) e obter uma chave [primária](quick-demo-map-app.md#get-the-primary-key-for-your-account). A chave principal também é conhecida como a chave principal de subscrição, ou chave de subscrição.

Para obter informações sobre a autenticação no Azure Maps, consulte [Gerir a autenticação no Azure Maps](./how-to-manage-authentication.md). E para mais informações sobre a cobertura do Serviço de Rota, consulte a [Cobertura de Encaminhamento.](routing-coverage.md)

Este artigo usa a [app Postman](https://www.postman.com/downloads/) para construir chamadas REST, mas você pode escolher qualquer ambiente de desenvolvimento DaPI.

## <a name="choose-between-route-directions-and-matrix-routing"></a>Escolha entre direções de rota e encaminhamento de matriz

As Direções de Rota APIs devolvem as instruções, incluindo o tempo de viagem e as coordenadas para uma rota. A Route Matrix API permite calcular o tempo de viagem e as distâncias para um conjunto de rotas que são definidas pela origem e locais de destino. Para cada origem, a Matrix API calcula o custo (tempo de viagem e distância) do encaminhamento dessa origem para cada destino dado. Todas estas APIs permitem especificar parâmetros como a hora de partida, os horários de chegada e o tipo de veículo, como carro ou caminhão. Todos eles utilizam dados de tráfego em tempo real ou preditivos para devolver as rotas mais ideais.

Considere chamar as Direções de Rota apIs se o seu cenário for:

* Solicite a rota de condução mais curta ou rápida entre dois ou mais locais conhecidos, para obter horários de chegada precisos para os seus veículos de entrega.
* Solicitar orientação detalhada da rota, incluindo geometria da rota, para visualizar rotas no mapa
* Dada a lista de locais do cliente, calcule a rota mais curta possível para visitar cada local do cliente e regressar à origem. Este cenário é comumente conhecido como o problema do vendedor itinerante. Pode passar até 150 pontos de passagem (paragens) num pedido.
* Envie lotes de consultas para a Route Directions Batch API utilizando apenas uma chamada API.

Considere chamar a Matrix Routing API se o seu cenário for:

* Calcular o tempo de viagem ou distância entre um conjunto de origens e destinos. Por exemplo, você tem 12 motoristas e você precisa encontrar o motorista mais próximo disponível para pegar a entrega de comida do restaurante.
* Ordenar rotas potenciais pela sua distância ou tempo de viagem. A Matrix API retorna apenas tempos de viagem e distâncias para cada origem e combinação de destino.
* Dados do cluster com base no tempo de viagem ou distâncias. Por exemplo, a sua empresa tem 50 funcionários, encontra todos os colaboradores que vivem a menos de 20 minutos do tempo de condução do seu escritório.

Aqui está uma comparação para mostrar algumas capacidades das Direções de Rota e APIs matriz:

| API dos Mapas Azure | Número máximo de consultas no pedido | Evitar áreas | Encaminhamento de caminhão e veículo elétrico | waypoints e otimização de Vendedor itinerante | Pontos de apoio |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| Obtenha direções de rota | 1 | | X | X | |
| Direções de rota de post | 1 | X | X | X | X |
| Lote de direções de rota de post | 700 | | X | X | |
| Matriz de pós-rota | 700 | | X | | |

Para saber mais sobre as capacidades de encaminhamento de veículos elétricos, consulte o nosso tutorial sobre como [encaminhar veículos elétricos utilizando cadernos Azure com Python](tutorial-ev-routing.md).

## <a name="request-historic-and-real-time-data"></a>Solicitar dados históricos e em tempo real

Por padrão, o serviço Rota assume que o modo de viagem é um carro e a hora de saída é agora. Devolve a rota com base em condições de tráfego em tempo real, a menos que um pedido de cálculo da rota especifique o contrário. As restrições fixas de tráfego dependentes do tempo, como "As curvas à esquerda não são permitidas entre as 16:00 e as 18:00" e serão capturadas e serão consideradas pelo motor de encaminhamento. Os encerramentos de estradas, como obras rodoviárias, serão considerados a menos que solicite especificamente uma rota que ignore o tráfego ao vivo atual. Para ignorar o tráfego `traffic` `false` atual, definido no seu pedido de API.

O valor do cálculo da rota **TimeInSeconds** inclui o atraso devido ao tráfego. É gerado alavancando os dados atuais e históricos do tempo de viagem, quando o tempo de partida está definido para agora. Se o seu tempo de partida for definido no futuro, as APIs regressam tempos de viagem previstos com base em dados históricos.

Se incluir o **computeTravelTimeFor=todo** o parâmetro no seu pedido, então o elemento resumo na resposta terá os seguintes campos adicionais, incluindo condições históricas de tráfego:

| Elemento | Descrição|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | Tempo estimado de viagem calculado como se não houvesse atrasos na rota devido às condições de tráfego, por exemplo, devido ao congestionamento |
| históricoTrafficTravelTimeInSeconds | Tempo estimado de viagem calculado com base em dados históricos de tráfego dependentes do tempo |
| liveTrafficIncidentsTravelTimeInSeconds | Tempo estimado de viagem calculado com base em dados de velocidade em tempo real |

As secções seguintes demonstram como fazer chamadas para as APIs da Rota utilizando os parâmetros discutidos.

### <a name="sample-query"></a>Consulta de exemplo

No primeiro exemplo abaixo da hora de partida está definido para o futuro, no momento da escrita.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

A resposta contém um elemento resumo, como o de baixo. Como o tempo de saída está definido para o futuro, o valor do **tráfegoDelayInSeconds** é zero. O valor do tempo de **viagemTimeInSeconds** é calculado utilizando dados históricos de tráfego dependentes do tempo. Assim, neste caso, o valor do **tempo de viagemTimeInSeconds** é igual ao **valor histórico DoTráfegoTravelTimeInSeconds.**

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

No segundo exemplo abaixo, temos um pedido de encaminhamento em tempo real, onde a hora de partida é agora. Não está explicitamente especificado no URL porque é o valor padrão.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

A resposta contém um resumo, como mostrado abaixo. Devido a congestionamentos, o valor **do tráfegoDelaysInSeconds** é superior a zero. É também maior do que **o históricoTrafficTravelTimeInSeconds**.

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

Por padrão, o serviço Rota retornará uma série de coordenadas. A resposta conterá as coordenadas que compõem `points`o caminho numa lista chamada . A resposta da rota também inclui a distância desde o início da rota e o tempo decorrido estimado. Estes valores podem ser usados para calcular a velocidade média de toda a rota.

A imagem que `points` se segue mostra o elemento.

<center>

![lista de pontos](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

</center>

Expanda `point` o elemento para ver a lista de coordenadas para o caminho:

<center>

![lista de pontos](media/how-to-use-best-practices-for-routing/points-list-img.png)

</center>

As Direções de Rota APIs suportam diferentes formatos de instruções que podem ser utilizados especificando o parâmetro tipo de **instruções.** Para formatar instruções para um fácil processamento do computador, utilize **instruçõesType=coded**. Utilize **instruçõesType=marcados** para visualizar instruções como texto para o utilizador. Além disso, as instruções podem ser formatadas como texto onde alguns elementos das instruções estão marcados, e a instrução é apresentada com formatação especial. Para mais informações, consulte a [lista de tipos de instruções suportados](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routeinstructionstype).

Quando as instruções são solicitadas, a `guidance`resposta devolve um novo elemento chamado . O `guidance` elemento contém duas informações: direções turn-by-turn e instruções resumidas.

<center>

![Tipo de instruções](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

</center>

O `instructions` elemento contém direções turn-by-turn para `instructionGroups` a viagem, e as instruções resumidas. Cada resumo de instrução abrange um segmento da viagem que pode abranger várias estradas. As APIs podem devolver detalhes para secções de uma rota. tais como a gama de coordenadas de um engarrafamento ou a velocidade atual do tráfego.

<center>

![Vire por volta instruções](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

</center>

<center>

![Instruções resumidas](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

</center>

## <a name="request-a-route-for-a-commercial-vehicle"></a>Solicitar uma rota para um veículo comercial

As APIs de encaminhamento de mapas Azure maps apoiam o encaminhamento de veículos comerciais, cobrindo o encaminhamento de camiões comerciais. As APIs consideram limites especificados. Tal como a altura e o peso do veículo, e se o veículo transporta carga perigosa. Por exemplo, se um veículo estiver a transportar inflamáveis, o motor de encaminhamento evita certos túneis que se encontram perto de áreas residenciais.

### <a name="sample-query"></a>Consulta de exemplo

O pedido de amostra abaixo questiona uma rota para um caminhão comercial. O camião transporta resíduos perigosos de classe 1.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

A Rota API retorna direções que acomodam as dimensões do camião e os resíduos perigosos. Pode ler as instruções da `guidance` rota expandindo o elemento.

<center>

![Caminhão com hazwaste classe 1](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

</center>

### <a name="sample-query"></a>Consulta de exemplo

Mudar a Classe Hazmat dos EUA, a partir da consulta acima, resultará numa rota diferente para acomodar esta mudança.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

A resposta abaixo é para um caminhão que transporta um material perigoso de classe 9, que é menos perigoso do que um material perigoso de classe 1. Quando expandir `guidance` o elemento para ler as direções, vai notar que as direções não são as mesmas. Há mais instruções de rota para o camião que transporta material perigoso de classe 1.

<center>

![Caminhão com hazwaste de classe 9](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)

</center>

## <a name="request-traffic-information-along-a-route"></a>Solicite informações de tráfego ao longo de uma rota

Com as APIs de Direção de Rota dos Mapas Azure, os desenvolvedores podem solicitar detalhes para cada tipo de secção, incluindo o `sectionType` parâmetro no pedido. Por exemplo, pode solicitar a informação de velocidade para cada segmento de engarrafamento. Consulte a [lista de valores para a chave secçãoType](https://docs.microsoft.com/rest/api/maps/route/getroutedirections#sectiontype) para saber sobre os vários detalhes que pode solicitar.

### <a name="sample-query"></a>Consulta de exemplo

A seguinte consulta define `sectionType` `traffic`o . Solicita as secções que contêm informações de tráfego de Seattle para San Diego.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

A resposta contém as secções adequadas ao tráfego ao longo das coordenadas indicadas.

<center>

![secções de tráfego](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

</center>

Esta opção pode ser usada para colorir as secções ao renderizar o mapa, como na imagem abaixo: 

<center>

![secções de tráfego](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

</center>

## <a name="calculate-and-optimize-a-multi-stop-route"></a>Calcular e otimizar uma rota multi-stop

O Azure Maps fornece atualmente duas formas de otimização de rotas:

* Otimizações com base no tipo de rota solicitado, sem alterar a ordem dos pontos de passagem. Você pode encontrar os tipos de [rota suportados aqui](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routetype)

* Otimização de vendedoritinerante, que altera a ordem dos pontos de passagem para obter a melhor ordem para visitar cada paragem

Para o encaminhamento multi-stop, até 150 pontos de passagem podem ser especificados num único pedido de rota. Os locais de coordenadas de partida e de fim podem ser os mesmos, como seria o caso de uma viagem de ida e volta. Mas precisa fornecer pelo menos um ponto de passagem adicional para fazer o cálculo da rota. Os pontos de passagem podem ser adicionados à consulta entre as coordenadas de origem e destino.

Se pretende otimizar a melhor encomenda para visitar os pontos de passagem dados, então precisa especificar **computeBestOrder=true**. Este cenário também é conhecido como o problema de otimização do vendedor itinerante.

### <a name="sample-query"></a>Consulta de exemplo

A seguinte consulta solicita o caminho para seis `computeBestOrder` pontos `false`de passagem, com o parâmetro definido para . É também o valor padrão `computeBestOrder` para o parâmetro.

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

A resposta descreve o comprimento do percurso como de 140.851 metros, e que levaria 9.991 segundos para percorrer esse caminho.

<center>

![Resposta não otimizada](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

</center>

A imagem abaixo ilustra o caminho resultante desta consulta. Este caminho é uma rota possível. Não é o caminho ideal baseado no tempo ou na distância.

<center>

![Imagem não otimizada](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)

</center>

Esta ordem de passagem de rota é: 0, 1, 2, 3, 4, 5 e 6.

### <a name="sample-query"></a>Consulta de exemplo

A consulta seguinte solicita o caminho para os mesmos seis pontos de passagem, como na amostra acima. Desta vez, `computeBestOrder` o parâmetro `true` definido para (a otimização do vendedor itinerante).

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

A resposta descreve o comprimento do percurso como de 91.814 metros, e que levaria 7.797 segundos para percorrer esse caminho. A distância de viagem e o tempo de viagem são ambos mais baixos aqui porque a API devolveu a rota otimizada.

<center>

![Resposta não otimizada](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

</center>

A imagem abaixo ilustra o caminho resultante desta consulta.

<center>

![Imagem não otimizada](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

</center>

O percurso ideal tem a seguinte ordem de ponto de passagem: 0, 5, 1, 2, 4, 3 e 6.

>[!TIP]
> A informação otimizada de encomendas de waypoint do serviço de Encaminhamento fornece um conjunto de índices. Estes excluem a origem e os índices de destino. É necessário aumentar estes valores em 1 para ter em conta a origem. Em seguida, adicione o seu destino até ao fim para obter a lista completa de waypoint encomendado.

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>Calcular e bias rotas alternativas utilizando pontos de apoio

Pode ter situações em que pretende reconstruir uma rota para calcular rotas alternativas zero ou mais para uma rota de referência. Por exemplo, pode querer mostrar aos clientes rotas alternativas que passam pela sua loja de retalho. Neste caso, você precisa distorcer um local usando pontos de apoio. Aqui estão os passos para distorcer um local:

1. Calcular uma rota como está e obter o caminho a partir da resposta da rota
2. Utilize o percurso para encontrar os locais desejados ao longo ou perto do percurso. Por exemplo, pode utilizar a [API](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) do Ponto de Interesse do Ponto de Interesse do Azure Maps ou consultar os seus próprios dados na sua base de dados.  
3. Encomende os locais com base na distância desde o início do percurso
4. Adicione estes locais como pontos de apoio num novo pedido de rota para a API de [Direções postais](https://docs.microsoft.com/rest/api/maps/route/postroutedirections). Para saber mais sobre os pontos de apoio, consulte a [documentação da API das Direções Post Route.](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints) 

Ao ligar para a API de [Direções postais,](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)pode definir o tempo mínimo de desvio ou os constrangimentos de distância, juntamente com os pontos de apoio. Utilize estes parâmetros se quiser oferecer rotas alternativas, mas também quer limitar o tempo de viagem. Quando estes constrangimentos forem utilizados, as rotas alternativas seguirão a rota de referência a partir do ponto de origem durante o tempo ou distância. Por outras palavras, as outras rotas divergem da rota de referência de acordo com os constrangimentos dados.

A imagem abaixo é um exemplo de renderização de rotas alternativas com limites de desvio especificados para o tempo e a distância.

<center>

![Rotas alternativas](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

</center>

## <a name="use-the-routing-service-in-a-web-app"></a>Use o serviço de Encaminhamento numa aplicação web

O Azure Maps Web SDK fornece um [módulo de serviço](https://docs.microsoft.com/javascript/api/azure-maps-rest/?view=azure-maps-typescript-latest). Este módulo é uma biblioteca de ajudantes que facilita a utilização das APIs DO REST do Azure Maps na web ou node.js, utilizando javaScript ou TypeScript. O módulo de serviço pode ser utilizado para renderizar as rotas devolvidas no mapa. O módulo determina automaticamente qual a API a utilizar com pedidos GET e POST.

## <a name="next-steps"></a>Passos seguintes

Para saber mais, por favor veja:

> [!div class="nextstepaction"]
> [Serviço de Rota dos Mapas Azure](https://docs.microsoft.com/rest/api/maps/route)

> [!div class="nextstepaction"]
> [Como utilizar o módulo de serviço](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [Mostrar rota no mapa](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [Pacote NPM Azure Maps](https://www.npmjs.com/package/azure-maps-rest  )
