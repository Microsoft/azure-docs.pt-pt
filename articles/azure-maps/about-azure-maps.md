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
ms.openlocfilehash: 2368d097cc0bfe82d0b869c3c8eb48760cc41616
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275121"
---
# <a name="what-is-azure-maps"></a>O que é o Azure Maps?

Mapas do Azure é uma coleção de serviços geoespaciais, underpinned pelos dados de mapeamento mais recentes disponíveis dar contexto geográfico preciso para as suas aplicações web e móveis. Mapas do Azure consiste em REST APIs para a composição **Maps** em vários estilos e imagens de satélite, **pesquisa** para endereços, locais e pontos de interesse em todo o mundo; **Encaminhamento** ponto a ponto, multipoint, otimização de multipoint, isochrone, veículos comerciais, tráfego influenciada e matriz encaminhamento; ver o fluxo de tráfego de líderes do setor e incidentes; **Mobilidade** serviços pedir trânsito público, partilha de bicicletas, partilha scooter e carro partilhar informações para planear a tirar partido da alternativos modos de transporte e dados em tempo real; estabelecimento de localização do utilizador através de rotas **Geolocalização**; e a localização para a converter **fusos horários**, bem como a obter a hora num local. Além disso, o Azure Maps oferece serviços para **barreira geográfica**, mapa **dados** armazenamento - informações de localização no Azure; de alojamento e **operações geográficos** fornecendo local inteligência através da análise de dados geoespaciais. Serviços de mapas do Azure estão disponíveis diretamente como REST APIs ou através de um nosso robusto **Web SDK** ou **Android SDK**. Essas ferramentas permitem que os desenvolvedores para desenvolver rapidamente e soluções de escala que integram informações de localização em soluções do Azure a partir da cloud do Azure. Inscreva-se sua livre [conta do Azure Maps](https://azure.microsoft.com/services/azure-maps/) hoje e no desenvolvimento de início!

O vídeo seguinte explica o Azure Maps detalhadamente:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Controlos de Mapas

### <a name="web-sdk"></a>Web SDK

O SDK de Web de mapas do Azure permite-lhe personalizar mapas interativos com o seu conteúdo e imagens para exibição nas suas aplicações móveis ou web. Este controlo utiliza o WebGL, permitindo-lhe compor grandes conjuntos de dados com elevado desempenho. Programe com o SDK com JavaScript ou TypeScript.

![SDK de Web do Azure Maps](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>SDK Android

O Azure Maps Android SDK permite-lhe criar aplicações de mapeamento móveis poderosas. 

![SDK Android do Azure Maps](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Serviços no Azure Maps

Mapas do Azure é composta pelos serviços de nove seguintes que podem fornecer contexto geográfico para as aplicações do Azure.

### <a name="data-service"></a>Serviço de Dados

Dados são obrigatório para mapas e trazer mais perto dos dados do cliente para o serviço do Azure Maps irá reduzir a latência, aumentar a produtividade e criar poderosos e novos cenários de aprimorar cópia de segurança em seus aplicativos. Serviço de dados permite-lhe carregar e armazenar dados de geoespacial para utilizam com operações geográficos ou de composição de imagem para reduzir a latência, aumentar a produtividade e permitem novos cenários nas suas aplicações. Para obter detalhes sobre este serviço, visite o [APIs de serviço de dados](https://docs.microsoft.com/rest/api/maps/data) página.

### <a name="mobility-service"></a>Serviço de mobilidade

Serviços de mobilidade de mapas do Azure fornecem inteligência local em tempo real sobre nas proximidades de serviços de trânsito pública, incluindo paradas, informações de rotas e estimativas de tempo de viagem. Permite que o serviço para procurar tipos de objeto específico, como o deixa de trânsito público, compartilhados bicicletas/scooters/carros em torno de uma determinada localização retornar um conjunto de objeto de trânsito com detalhes do objeto. O serviço permite aos programadores também detalhes da linha de trânsito de solicitação que aborda informações básicas e detalhes adicionais, como a geometria de linha, a lista de paradas, agendada e entradas de trânsito em tempo real e alertas de serviço. Os utilizadores também podem pedir quantos bicicletas partilhadas disponíveis ficam na estação de ancoragem mais próxima ao solicitar informações de estações ancoragem. O serviço de mobilidade também tem a capacidade de pesquisar veículos de partilha de carro disponíveis, retornando detalhes como a disponibilidade de futura e nível atual de combustível.
O serviço de mobilidade de mapas do Azure permite que o planejamento de viagens em tempo real, retornando as melhores opções de rota possível e fornecer uma variedade de modos de deslocação, incluindo o trânsito de movimentação, andar e público disponível dentro da área metro (cidade). Além disso, os desenvolvedores podem solicitar detalhes itinerário do trânsito com informações adicionais desse tipo geometry da rota e agendas de itinerário detalhadas.

Para saber mais sobre o serviço e os vários recursos, consulte nosso [documentação da API](https://docs.microsoft.com/rest/api/maps/mobility)

### <a name="render-service"></a>Serviço de composição

O serviço de Composição foi concebido para os programadores criarem aplicações Web e móveis baseadas em mapeamento. O serviço utiliza imagens gráficas em padrão de alta qualidade, disponíveis em 19 níveis de zoom ou imagens de mapa de formato de vetor totalmente personalizáveis.

![Azure Maps Map.png](media/about-azure-maps/Introduction_Map.png)

Agora, o serviço de Composição oferece APIs de pré-visualização para permitir que os programadores trabalhem com imagens de satélite. Para obter mais detalhes, leia as [APIs de Composição do Azure Maps](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Serviço de encaminhamento

O serviço de Composição contém cálculos de geometria consistentes de infraestruturas do mundo real e múltiplas direções baseadas nos meios de transporte. O serviço permite que os programadores calculem as direções tendo em conta os diversos meios de transporte, como carros ligeiros, pesados, bicicletas ou a pé. O serviço também consegue levar em linha conta dados como condições de tráfego, restrições de peso ou o transporte de substâncias perigosas.

![Azure Maps Route.png](media/about-azure-maps/Introduction_Route.png)

O serviço de Composição oferece agora uma pré-visualização das funcionalidades avançadas, como o processamento em lotes de vários pedidos de encaminhamento, matrizes de tempo de deslocação e distância entre um conjunto de origens e destinos, e encontrar rotas ou distâncias em que pode viajar com base nos requisitos de tempo ou combustível. Para obter detalhes sobre as capacidades de encaminhamento, leia as [APIs de Rota do Azure Maps](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Serviço de pesquisa

O serviço de Pesquisa foi concebido para os programadores procurarem endereços, locais, listas de empresas por nome ou categoria e outras informações geográficas. O Serviço de Pesquisa também permite aplicar um [geocódigo inverso](https://en.wikipedia.org/wiki/Reverse_geocoding) aos endereços e cruzamentos com base nas latitudes e longitudes.

![Azure Maps Search.png](media/about-azure-maps/Introduction_Search.png)

O serviço de Pesquisa também oferece funcionalidades avançadas, como a pesquisa ao longo de uma rota, a pesquisa numa área mais ampla, colocação de um grupo de pedidos de pesquisa em lote, bem como pesquisa por área maior em vez de um ponto de localização. As APIs de pesquisa de área e batch estão atualmente em pré-visualização. Para obter mais detalhes sobre as capacidades de pesquisa, leia a página [APIs de Pesquisa do Azure Maps](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations"></a>Operações Geográficas

Operações de geográficos de mapas do Azure irá demorar informações de localização e analisá-los em tempo real para ajudar a informar aos nossos clientes em curso eventos que ocorrem no tempo e espaço, permitindo perto de análise em tempo real e modelagem de previsão de eventos. Permite que os clientes do Azure Maps nativamente aprimorar seus inteligência local com uma biblioteca de geoespacial matemáticos cálculos comuns, incluindo serviços como o ponto mais próximo, grande ciclo distância e buffers. Para saber mais sobre o serviço e os vários recursos, consulte nosso [documentação da API](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Serviço de Fuso Horário

O serviço de Fuso Horário permite-lhe consultar informações de fuso horário atuais, históricas e futuras através de pares de latitude/longitude ou de um [ID IANA](https://www.iana.org/). O serviço Time Zone Service também permite converter IDs de fuso horário do Microsoft Windows para fusos horários IANA, obter um desvio de fuso horário UTC e obter a hora atual num fuso horário específico. Segue-se o exemplo de uma resposta de JSON típica para uma consulta ao Time Zone Service:

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

Para obter detalhes sobre este serviço, visite a página [APIs de Fuso Horário do Azure Maps](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Serviço de tráfego

O serviço de Tráfego é um conjunto de serviços Web concebido para os programadores criarem aplicações Web e móveis que precisem de dados de tráfego. O serviço fornece dois tipos de dados:

* Fluxo de tráfego – velocidades observadas em tempo real e durações das viagens para todas as estradas principais da rede.
* Incidentes de tráfego - uma exibição atualizada sobre engarrafamentos e incidentes na rede de trânsito.

![Tráfego no Azure Maps](media/about-azure-maps/Introduction_Traffic.png)

Visite a página [APIs de Tráfego do Azure Maps](https://docs.microsoft.com/rest/api/maps/traffic) para obter mais detalhes.

### <a name="ip-to-location"></a>IP para Localização

O serviço IP para Localização permite-lhe pré-visualizar o código de país de duas letras obtido para um determinado endereço IP. Este serviço pode ajudar a personalizar e melhorar a experiência do utilizador ao capacitar conteúdo da aplicação personalizado com base na localização geográfica.

Para obter informações sobre as APIs REST para o IP do serviço de localização, visite a página [APIs de Geolocalização do Azure Maps](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Modelo de programação

O Azure Maps foi criado para oferecer mobilidade e pode potenciar aplicações de várias plataformas. Ele utiliza um modelo de programação que é agnóstico quanto ao idioma e oferece suporte à saída JSON através de [APIs REST](https://docs.microsoft.com/rest/api/maps/).

Além disso, o Azure Maps disponibiliza um prático [controlo de mapas em JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-control) com um modelo de programação simples tendo em vista um desenvolvimento fácil e rápido de aplicações Web e móveis.

## <a name="usage"></a>Utilização

Para aceder aos serviços do Maps, navegue para o [portal do Azure](https://portal.azure.com) e crie uma conta do Azure Maps.

O Azure Maps utiliza um esquema de autenticação baseado em chave. A conta inclui duas chaves pré-geradas para si. Comece a integrar estas capacidades de localização na sua aplicação através de qualquer uma das chaves e a fazer um pedido para o serviço do Azure Maps.

## <a name="supported-regions"></a>Regiões suportadas

A API de mapas do Azure está atualmente disponível em todos os países/regiões, exceto para as regiões seguintes:

* Argentina
* China
* Índia
* Marrocos
* Paquistão
* Coreia do Sul

Certifique-se de que a localização do seu endereço IP atual não está dos não suportados países/regiões acima.

## <a name="next-steps"></a>Passos Seguintes

Experimente uma aplicação de exemplo que mostra os mapas do Azure:

> [!div class="nextstepaction"]
> [Quickstart: Criar uma aplicação web](quick-demo-map-app.md)

Mantenha-se atualizado sobre o Azure Maps: 

> [!div class="nextstepaction"]
> [Blogue de mapas do Azure](https://azure.microsoft.com/blog/topics/azure-maps/)