---
title: Visão geral | Mapas do Microsoft Azure
description: Saiba mais sobre os serviços e funcionalidades do Microsoft Azure Maps e como usá-los em seus aplicativos.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 2909dbebc7531be0e45b321cc31b599b0f63ee4a
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262576"
---
# <a name="what-is-azure-maps"></a>O que é o Azure Maps?

O mapas do Azure é uma coleção de serviços geoespaciais que usam dados de mapeamento atualizados para fornecer contexto geográfico para aplicativos Web e móveis. O Azure Maps fornece:

* APIs REST para renderizar mapas em vários estilos e em imagens de satélite.
* Procura endereços, lugares e pontos de interesse em todo o mundo.
* Tipos diferentes de roteamento: ponto a ponto, MultiPoint, otimização do MultiPoint, isochrone, veículo comercial, tráfego influenciado e roteamento de matriz
* Fluxo de tráfego e exibição de incidentes.
* Serviços de mobilidade para solicitar o trânsito público e modos alternativos de transporte (como compartilhamento de bicicletas, compartilhamento de motocicleta e compartilhamento de carros) e planejamento de rotas em tempo real. 
* Estabelecer o local de usuário por meio de localização geográfica e converter o local em fusos horários. 
* Serviços para isolamento geográfico e mapeamento de dados de armazenamento, com informações de local hospedadas no Azure. 
* Inteligência de local por meio de análise geoespacial. 

Além disso, os serviços do Azure Maps estão disponíveis por meio do SDK da Web ou do SDK do Android. Essas ferramentas ajudam os desenvolvedores a desenvolver e dimensionar rapidamente soluções que integram informações de local em soluções do Azure. 

Você pode se inscrever para uma [conta gratuita do Azure Maps](https://azure.microsoft.com/services/azure-maps/) e começar a desenvolver.

O vídeo seguinte explica o Azure Maps detalhadamente:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Controlos de mapa

### <a name="web-sdk"></a>Web SDK

O SDK da Web do Azure Maps permite que você personalize mapas interativos com seu próprio conteúdo e imagens para seus aplicativos móveis ou Web. Esse controle usa WebGL para que você possa renderizar grandes conjuntos de dados com alto desempenho. Desenvolva com o SDK usando JavaScript ou TypeScript.

![Mapa de exemplo de alteração de população](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>SDK Android

Use o SDK do Android do Azure Maps para criar aplicativos de mapeamento móvel. 

![Exemplos de mapa em um dispositivo móvel](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Serviços no Azure Maps

O mapas do Azure consiste nos nove serviços a seguir que podem fornecer contexto geográfico para seus aplicativos do Azure.

### <a name="data-service"></a>Serviço de Dados

Os dados são imperativos para mapas. Use o serviço de dados para carregar e armazenar dados geoespaciais para uso com operações espaciais ou composição de imagem.  Trazer os dados do cliente mais perto do serviço mapas do Azure reduzirá a latência, aumentará a produtividade e criará novos cenários em seus aplicativos. Para obter detalhes sobre esse serviço, consulte a [documentação da API do serviço de dados](https://docs.microsoft.com/rest/api/maps/data).

### <a name="mobility-service"></a>Serviço de Mobilidade

O serviço de mobilidade do Azure Maps permite o planejamento de viagens em tempo real. Ele retorna as melhores opções de rota possíveis e fornece tipos diferentes de modos de viagem. Para áreas metro (cidade), esses modos podem incluir movimentação, semiking e trânsito público. Você pode solicitar itinerário de trânsito, geometria de linha, listas de interrupções, entradas agendadas e em tempo real e alertas de serviço.

O serviço também permite pesquisas de tipos de objetos específicos, como bicicletas compartilhadas, scooters ou carros em um local. Os usuários podem solicitar o número de bicicletas disponíveis no encaixe mais próximo e Pesquisar veículos disponíveis para compartilhamento de Rides. Além disso, os usuários podem encontrar detalhes como disponibilidade futura de veículos e nível de combustível atual.

Para saber mais sobre o serviço, consulte a [documentação da API de mobilidade](https://docs.microsoft.com/rest/api/maps/mobility).

### <a name="render-service"></a>Serviço de composição

O serviço de renderização ajuda os desenvolvedores a criar aplicativos Web e móveis em relação ao mapeamento. O serviço utiliza imagens gráficas em padrão de alta qualidade, disponíveis em 19 níveis de zoom ou imagens de mapa de formato de vetor totalmente personalizáveis.

![Exemplo de um mapa do serviço de renderização](media/about-azure-maps/Introduction_Map.png)

Agora, o serviço de Composição oferece APIs de pré-visualização para permitir que os programadores trabalhem com imagens de satélite. Para obter mais detalhes, leia a [documentação da API de renderização](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Serviço de encaminhamento

O serviço de Composição contém cálculos de geometria consistentes de infraestruturas do mundo real e múltiplas direções baseadas nos meios de transporte. O serviço permite que os programadores calculem as direções tendo em conta os diversos meios de transporte, como carros ligeiros, pesados, bicicletas ou a pé. O serviço também consegue levar em linha conta dados como condições de tráfego, restrições de peso ou o transporte de substâncias perigosas.

![Exemplo de um mapa do serviço de rota](media/about-azure-maps/Introduction_Route.png)

O serviço de rota oferece uma visualização de recursos avançados, como: 

* Processamento em lotes de várias solicitações de rota.
* Matrizes de tempo de viagem e distância entre um conjunto de origens e destinos.
* Localizar rotas ou distâncias que os usuários podem viajar com base em requisitos de tempo ou de combustível. 

Para obter detalhes sobre os recursos de roteamento, leia a [documentação da API de rota](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Serviço de pesquisa

O serviço de pesquisa ajuda os desenvolvedores a procurar endereços, locais, listagens de negócios por nome ou categoria e outras informações geográficas. O serviço de pesquisa pode [reverter endereços geocódigo](https://en.wikipedia.org/wiki/Reverse_geocoding) e entre ruas com base em latitudes e longitudes.

![Exemplo de uma pesquisa em um mapa](media/about-azure-maps/Introduction_Search.png)

O serviço de pesquisa também fornece recursos avançados, como:

* Pesquisar ao longo de uma rota.
* Pesquisar dentro de uma área mais ampla.
* Lote um grupo de solicitações de pesquisa.
* Pesquise área maior, em vez de um ponto de localização. 

As APIs de pesquisa de área e batch estão atualmente em pré-visualização. Para obter mais detalhes sobre os recursos de pesquisa, leia a [documentação da API de pesquisa](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations-service"></a>Serviço de operações espaciais

O serviço de operações espaciais do Azure Maps Obtém informações de localização e as analisa rapidamente para ajudar a informar os clientes de eventos contínuos que ocorrem em tempo e espaço. Ele permite a análise em tempo real e a modelagem preditiva de eventos. 

O serviço permite que os clientes aprimorem sua inteligência de local com uma biblioteca de cálculos matemáticos geoespaciais comuns. Cálculos comuns incluem ponto mais próximo, distância de círculo grande e buffers. Para saber mais sobre o serviço e os vários recursos, leia a [documentação da API de operações espaciais](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Serviço de Fuso Horário

O serviço de fuso horário permite consultar informações atuais, históricas e futuras de fuso horário. Você pode usar pares de latitude e longitude ou uma [ID da IANA](https://www.iana.org/). O serviço de fuso horário também permite:

* Convertendo IDs de fuso horário do Microsoft Windows em fusos horários IANA.
* Buscando um deslocamento de fuso horário para UTC.
* Obtendo a hora atual em um fuso horário. 

Uma resposta JSON típica para uma consulta ao serviço de fuso horário é semelhante ao exemplo a seguir:

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

Para obter detalhes sobre esse serviço, leia a [documentação da API de fuso horário](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Serviço de tráfego

O serviço de tráfego é um pacote de serviços Web que os desenvolvedores podem usar para aplicativos Web ou móveis que exigem informações de tráfego. O serviço fornece dois tipos de dados:

* Fluxo de tráfego: velocidades observadas em tempo real e tempos de viagem para todas as estradas chave na rede.
* Incidentes de tráfego: uma exibição atualizada dos congestionamentos de tráfego e dos incidentes em toda a rede de viagens.

![Exemplo de um mapa com informações de tráfego](media/about-azure-maps/Introduction_Traffic.png)

Para obter mais informações, consulte a [documentação da API de tráfego](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="ip-to-location-service"></a>IP para o serviço de localização

Use o serviço IP para localização para visualizar o código do país de duas letras recuperado para um endereço IP. Esse serviço pode ajudá-lo a melhorar a experiência do usuário fornecendo conteúdo de aplicativo personalizado com base na localização geográfica.

Para obter mais detalhes sobre o serviço IP para localização na API REST, leia a [documentação da API de geolocalização do Azure Maps](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Modelo de programação

O Azure Maps foi criado para mobilidade e pode ajudá-lo a desenvolver aplicativos de plataforma cruzada. Ele usa um modelo de programação independente de linguagem e dá suporte à saída JSON por meio de [APIs REST](https://docs.microsoft.com/rest/api/maps/).

Além disso, o mapas do Azure oferece um [controle de mapa JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-control) conveniente com um modelo de programação simples. O desenvolvimento é rápido e fácil para aplicativos Web e móveis.

## <a name="usage"></a>Utilização

Acessar os serviços do Azure Maps é uma questão de ir para a [portal do Azure](https://portal.azure.com) e criar uma conta do Azure Maps.

O Azure Maps utiliza um esquema de autenticação baseado em chave. Sua conta vem com duas chaves já geradas para você. Comece a integrar esses recursos de localização em seu aplicativo, usando as chaves e faça uma solicitação para o serviço Azure Maps.

## <a name="supported-regions"></a>Regiões suportadas

As APIs do Azure Maps estão disponíveis atualmente em todos os países e regiões, exceto os seguintes:

* China
* Coreia do Sul

Verifique se o local do seu endereço IP atual está em um país com suporte.

## <a name="next-steps"></a>Passos seguintes

Experimente um aplicativo de exemplo que demonstra os mapas do Azure:

> [!div class="nextstepaction"]
> [Início rápido: criar um aplicativo Web](quick-demo-map-app.md)

Mantenha-se atualizado sobre o Azure Maps: 

> [!div class="nextstepaction"]
> [Blog do Azure Maps](https://azure.microsoft.com/blog/topics/azure-maps/)
