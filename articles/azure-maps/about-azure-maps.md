---
title: Descrição Geral do Azure Maps | Microsoft Docs
description: Uma introdução ao Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 02/04/2019
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 5f41263113568cf9f3771119135be8db37119181
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442949"
---
# <a name="what-is-azure-maps"></a>O que é o Azure Maps?

Mapas do Azure é uma coleção de serviços geoespaciais que utilizam dados de mapeamento atualizados para fornecer contexto geográfico preciso para aplicações web e móveis. O Azure Maps fornece:

* APIs REST para o processamento de mapas em vários estilos e em imagens de satélite.
* Procura de endereços, locais e pontos de interesse em todo o mundo.
* Encaminhamento ponto a ponto, multipoint, otimização de multipoint, isochrone, veículos comerciais, tráfego influenciada e roteamento de matriz; visualizar o fluxo de tráfego e incidentes.
* Serviços de mobilidade para solicitar trânsito público e alternativos modos de transporte (como a partilha de bicicletas, partilha de scooter e partilha de carro) e planear rotas em tempo real. 
* Estabelecimento de localização do utilizador através da localização geográfica e conversão de localização em fusos horários. 
* Serviços de armazenamento de dados de barreira geográfica e o mapa, com informações de localização alojados no Azure. 
* Inteligência local através da análise de dados geoespaciais. 

Para além das REST APIs, serviços do Azure Maps estão disponíveis através do SDK de Web ou o SDK do Android. Essas ferramentas ajudam os desenvolvedores rapidamente desenvolver e dimensionar soluções que integram informações de localização em soluções do Azure. 

Pode inscrever-se numa [conta do Azure Maps](https://azure.microsoft.com/services/azure-maps/) e começar a desenvolver.

O vídeo seguinte explica o Azure Maps detalhadamente:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Controles de mapa

### <a name="web-sdk"></a>Web SDK

O SDK de Web de mapas do Azure permite-lhe personalizar mapas interativos com o seu conteúdo e imagens para exibição nas suas aplicações móveis ou web. Esse controle faz uso de WebGL, portanto, pode processar grandes conjuntos de dados com elevado desempenho. Desenvolva com o SDK com o JavaScript ou TypeScript.

![Alterar o mapa de exemplo da população](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>SDK Android

Utilize o SDK Android do Azure Maps para criar aplicações móveis de mapeamento. 

![Exemplos de mapa num dispositivo móvel](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Serviços no Azure Maps

Mapas do Azure é composta pelos serviços de nove seguintes que podem fornecer contexto geográfico para as aplicações do Azure.

### <a name="data-service"></a>Serviço de Dados

Dados são obrigatório para o maps. Utilize o serviço de dados para carregar e armazenar dados geoespaciais para utilização com operações geográficos ou de composição de imagem.  Trazer dados mais perto dos clientes para o serviço do Azure Maps irá reduzir a latência, aumentar a produtividade e criar novos cenários em seus aplicativos. Para obter detalhes sobre este serviço, consulte a [documentação da API de serviço de dados](https://docs.microsoft.com/rest/api/maps/data).

### <a name="mobility-service"></a>Serviço de Mobilidade

O serviço de mobilidade de mapas do Azure permite o planejamento de viagens em tempo real. Ele retorna as melhores opções de rota possível e fornece uma variedade de modos de viagem. Para áreas de metro (cidade), esses modos podem incluir movimentação, andar de bicicleta e trânsito público. Os desenvolvedores podem solicitar detalhes itinerário do trânsito como geometria de linha, listas de paradas, agendadas e entradas em tempo real e alertas de serviço.

O serviço também permite que procura por tipos de objeto específico, como bicicletas partilhadas, scooters ou carros em torno de uma localização. Os utilizadores podem pedir quantos bicicletas partilhadas disponíveis restantes na estação de ancoragem mais próxima. Podem pesquisar veículos de partilha de carro disponíveis e obter detalhes, como disponibilidade futura e nível atual de combustível.

Para saber mais sobre o serviço, consulte a [documentação da API de mobilidade](https://docs.microsoft.com/rest/api/maps/mobility).

### <a name="render-service"></a>Serviço de composição

O serviço de composição ajuda os desenvolvedores a criar aplicações web e móveis baseadas em mapeamento. O serviço utiliza imagens gráficas em padrão de alta qualidade, disponíveis em 19 níveis de zoom ou imagens de mapa de formato de vetor totalmente personalizáveis.

![Exemplo de um mapa do serviço de processamento](media/about-azure-maps/Introduction_Map.png)

Agora, o serviço de Composição oferece APIs de pré-visualização para permitir que os programadores trabalhem com imagens de satélite. Para obter mais detalhes, leia os [documentação da API de composição](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Serviço de encaminhamento

O serviço de Composição contém cálculos de geometria consistentes de infraestruturas do mundo real e múltiplas direções baseadas nos meios de transporte. O serviço permite que os programadores calculem as direções tendo em conta os diversos meios de transporte, como carros ligeiros, pesados, bicicletas ou a pé. O serviço também consegue levar em linha conta dados como condições de tráfego, restrições de peso ou o transporte de substâncias perigosas.

![Exemplo de um mapa do serviço Route](media/about-azure-maps/Introduction_Route.png)

O Route service oferece uma pré-visualização das funcionalidades avançadas, tais como: 

* Processamento de lotes de vários pedidos de rota.
* As matrizes de tempo e a distância de viagem entre um conjunto de origens e destinos.
* A localizar rotas ou distâncias que os utilizadores podem ser transferidos com base nos requisitos de tempo ou de combustível. 

Para obter detalhes sobre as capacidades de encaminhamento, leia os [documentação da API de rota](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Serviço de pesquisa

O serviço de pesquisa ajuda os desenvolvedores a procurar endereços, locais, listas de empresas por nome ou categoria e outras informações geográficas. O Search Service [inverter geocode](https://en.wikipedia.org/wiki/Reverse_geocoding) endereços e cruzamentos com base em latitudes e longitudes.

![Exemplo de uma pesquisa num mapa](media/about-azure-maps/Introduction_Search.png)

O serviço de pesquisa também fornece recursos avançados, tais como:

* Procurar ao longo de uma rota.
* Procurar dentro de uma área mais ampla.
* Um grupo de pedidos de pesquisa do batch.
* Procurar por área maior em vez de um ponto de localização. 

As APIs de pesquisa de área e batch estão atualmente em pré-visualização. Para obter mais detalhes sobre os recursos de pesquisa, leia os [documentação da API de pesquisa](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations-service"></a>Serviço de operações espacial

O serviço Operations espaciais do Azure Maps usa informações de localização e analisa-os em tempo real para ajudar a informar os clientes em curso eventos que ocorrem no tempo e espaço. Permite que quase análise em tempo real e modelagem de previsão de eventos. 

O serviço permite aos clientes aprimorar seus inteligência local com uma biblioteca de geoespacial matemáticos cálculos comuns, incluindo o mais próximo ponto, a distância de grande ciclo e buffers. Para saber mais sobre o serviço e os vários recursos, veja a [documentação da API de operações geográficos](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Serviço de Fuso Horário

Time Zone service permite-lhe consultar as informações atuais, históricas e futuras fuso de horário ao utilizar qualquer um dos pares de latitude/longitude ou uma [IANA ID](https://www.iana.org/). Time Zone service também permite:

* Converter os IDs de fuso horário do Microsoft Windows para fusos horários IANA.
* A obter um desvio de fuso horário UTC.
* Obter a hora atual num fuso horário. 

Uma resposta JSON típica para uma consulta para o serviço de fuso horário é semelhante ao exemplo seguinte:

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

Para obter detalhes sobre este serviço, leia os [documentação da API de fuso horário](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Serviço de tráfego

O serviço de tráfego é um conjunto de serviços da web que os desenvolvedores podem usar para criar aplicações web e móveis que necessitam de informações de tráfego. O serviço fornece dois tipos de dados:

* Fluxo de tráfego: Em tempo real observada velocidades e as horas de viagem para todas as principais estradas na rede.
* Incidentes de tráfego: Uma exibição atualizada dos engarrafamentos e incidentes na rede de trânsito.

![Exemplo de um mapa com informações de tráfego](media/about-azure-maps/Introduction_Traffic.png)

Para obter mais informações, consulte a [documentação da API de tráfego](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="ip-to-location-service"></a>IP para o serviço de localização

Utilize o IP para o serviço de localização para visualizar o código de país de duas letras obtidos para um endereço IP. Este serviço pode ajudá-lo a adaptar e melhorar a experiência de usuário, fornecendo o aplicativo personalizado conteúdo com base na localização geográfica.

Para obter mais detalhes sobre as APIs REST para o IP para o serviço de localização, leia os [documentação de API de localização geográfica do Azure Maps](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Modelo de programação

Mapas do Azure baseia-se para a mobilidade e pode ajudá-lo a desenvolver aplicações de várias plataformas. Ele usa um modelo de programação que é agnóstico quanto a idioma e oferece suporte à saída JSON através de [REST APIs](https://docs.microsoft.com/rest/api/maps/).

Além disso, o Azure Maps disponibiliza um prático [controlo de mapas em JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-control) com um modelo de programação simples tendo em vista um desenvolvimento fácil e rápido de aplicações Web e móveis.

## <a name="usage"></a>Utilização

Aceder aos serviços do Azure Maps é uma questão de aceder a [portal do Azure](https://portal.azure.com) e criar uma conta do Azure Maps.

O Azure Maps utiliza um esquema de autenticação baseado em chave. A conta inclui duas chaves já geradas para. Comece a integrar estas capacidades de localização na sua aplicação através de qualquer uma das chaves e a fazer um pedido para o serviço do Azure Maps.

## <a name="supported-regions"></a>Regiões suportadas

As APIs de mapas do Azure estão atualmente disponíveis em todos os países/regiões, exceto estes:

* Argentina
* China
* Índia
* Marrocos
* Paquistão
* Coreia do Sul

Certifique-se de que a localização do seu endereço IP atual não está dos países/regiões não suportados.

## <a name="next-steps"></a>Passos Seguintes

Experimente uma aplicação de exemplo que demonstra o Azure Maps:

> [!div class="nextstepaction"]
> [Quickstart: Criar uma aplicação web](quick-demo-map-app.md)

Mantenha-se atualizado sobre o Azure Maps: 

> [!div class="nextstepaction"]
> [Blogue de mapas do Azure](https://azure.microsoft.com/blog/topics/azure-maps/)
