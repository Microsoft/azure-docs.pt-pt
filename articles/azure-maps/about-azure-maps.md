---
title: Visão geral / Microsoft Azure Maps
description: Saiba mais sobre serviços e capacidades no Microsoft Azure Maps e como usá-los nas suas aplicações.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, references_regions
ms.openlocfilehash: 4b53c4d1a26d8e4b8d784087dcb9b9feba9c7ded
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84191756"
---
# <a name="what-is-azure-maps"></a>O que é o Azure Maps?

O Azure Maps é uma coleção de serviços geoespaciais que utilizam dados de mapeamento frescos para fornecer contexto geográfico a aplicações web e móveis. O Azure Maps fornece:

* REST APIs para renderizar mapas em vários estilos e em imagens de satélite.''
* Serviços de pesquisa para localizar endereços, locais e pontos de interesse em todo o mundo.
* Várias opções de encaminhamento; tais como ponto a ponto, multiponto, otimização de múltiplos pontos, isochrone, veículo comercial, tráfego influenciado e encaminhamento de matriz.
* Vista de fluxo de tráfego e vista para incidentes, para aplicações que requerem informações de tráfego.
* Serviço de mobilidade para solicitar logística de trânsito público, planear rotas em tempo real e solicitar informações para modos de transporte alternativos.
* Serviços de fuso horário e geolocalização e conversão de um local para fusos horários.
* Serviço de geofencing e armazenamento de dados de mapeamento, com informações de localização hospedadas em Azure. 
* Inteligência de localização através de análise geoespacial. 

Além disso, os serviços do Azure Maps estão disponíveis através do Web SDK ou do Android SDK. Estas ferramentas ajudam os desenvolvedores a desenvolver e escalar rapidamente soluções que integram informação de localização em soluções Azure. 

Pode inscrever-se numa [conta gratuita do Azure Maps](https://azure.microsoft.com/services/azure-maps/) e começar a desenvolver-se.

O vídeo seguinte explica o Azure Maps detalhadamente:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Controlos de mapa

### <a name="web-sdk"></a>Web SDK

O Azure Maps Web SDK permite personalizar mapas interativos com o seu próprio conteúdo e imagens. Pode utilizar este mapa interativo tanto para as suas aplicações web como para dispositivos móveis. O controlo do mapa faz uso do WebGL, para que possa renderizar grandes conjuntos de dados com alto desempenho. Desenvolva-se com o SDK utilizando o JavaScript ou o TypeScript.

![Mapa de exemplo da mudança de população](media/about-azure-maps/intro_web_map_control.png)

### <a name="android-sdk"></a>SDK Android

Utilize o Azure Maps Android SDK para criar aplicações de mapeamento móvel. 

![Exemplos de mapas em um dispositivo móvel](media/about-azure-maps/android_sdk.png)

## <a name="services-in-azure-maps"></a>Serviços no Azure Maps

O Azure Maps é composto pelos seguintes nove serviços que podem fornecer contexto geográfico às suas aplicações Azure.

### <a name="data-service"></a>Serviço de Dados

Os dados são imperativos para mapas. Utilize o Serviço de Dados para carregar e armazenar dados geoespaciais para utilização com operações espaciais ou composição de imagem.  Aproximar os dados dos clientes do serviço Azure Maps reduzirá a latência, aumentará a produtividade e criará novos cenários nas suas aplicações. Para mais informações sobre este serviço, consulte a documentação da [API do Serviço de Dados.](https://docs.microsoft.com/rest/api/maps/data)

### <a name="mobility-service"></a>Serviço de Mobilidade

O serviço de Mobilidade Azure Maps permite o planeamento de viagens em tempo real. Ele devolve as melhores opções de rota possível e fornece diferentes tipos de modos de viagem. Para as áreas do metrô (cidade), estes modos podem incluir caminhadas, ciclismo e trânsito público. Você pode solicitar itinerário de trânsito, geometria da linha, listas de paragens, chegadas programadas, chegadas em tempo real e alertas de serviço.

O serviço também permite pesquisas de tipos de objetos específicos em torno de um local. Os utilizadores podem procurar bicicletas partilhadas, scooters ou carros em torno de um local. Os utilizadores podem solicitar o número de bicicletas disponíveis na doca mais próxima e pesquisar veículos disponíveis para partilha de boleias. E, os utilizadores podem encontrar detalhes como a futura disponibilidade de veículos e o nível atual de combustível.

Para saber mais sobre o serviço, consulte a documentação da [API de Mobilidade.](https://docs.microsoft.com/rest/api/maps/mobility)

### <a name="render-service"></a>Serviço de composição

O serviço Render ajuda os desenvolvedores a criar aplicações web e móveis com funcionalidades de mapeamento. O serviço utiliza imagens gráficas em padrão de alta qualidade, disponíveis em 19 níveis de zoom ou imagens de mapa de formato de vetor totalmente personalizáveis.

![Exemplo de um mapa do serviço render](media/about-azure-maps/intro_map.png)

Agora, o serviço de Composição oferece APIs de pré-visualização para permitir que os programadores trabalhem com imagens de satélite. Para mais detalhes, leia a documentação da [Render API.](https://docs.microsoft.com/rest/api/maps/render)

### <a name="route-service"></a>Serviço de encaminhamento

O serviço de Composição contém cálculos de geometria consistentes de infraestruturas do mundo real e múltiplas direções baseadas nos meios de transporte. O serviço permite que os desenvolvedores calculem direções em vários modos de viagem, tais como carro, caminhão, bicicleta ou caminhada. O serviço também considera inputs como condições de tráfego, restrições de peso ou transporte de materiais perigosos.

![Exemplo de um mapa do serviço Rota](media/about-azure-maps/intro_route.png)

O serviço Route oferece uma pré-visualização de funcionalidades avançadas, tais como: 

* Processamento de lote de pedidos de rota múltiplas.
* Matrizes do tempo de viagem e distância entre um conjunto de origens e destinos.
* Encontrar rotas ou distâncias que os utilizadores possam percorrer com base nos requisitos de tempo ou combustível. 

Para mais informações sobre as capacidades de encaminhamento, leia a documentação da [Rota API](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Serviço de pesquisa

O serviço de Pesquisa ajuda os desenvolvedores a procurar endereços, lugares, listas de negócios por nome ou categoria, e outras informações geográficas. O serviço de Pesquisa pode reverter endereços [geocódigos](https://en.wikipedia.org/wiki/Reverse_geocoding) e atravessar ruas com base em latitudes e longitudes.

![Exemplo de uma pesquisa em um mapa](media/about-azure-maps/intro_search.png)

O serviço De pesquisa também fornece funcionalidades avançadas, tais como:

* Procure ao longo de uma rota.
* Procurem dentro de uma área mais ampla.
* Lote de um grupo de pedidos de pesquisa.
* Procure uma área maior em vez de um ponto de localização. 

As APIs de pesquisa de área e batch estão atualmente em pré-visualização. Para mais detalhes sobre as capacidades de pesquisa, leia a [documentação da API de pesquisa.](https://docs.microsoft.com/rest/api/maps/search)

### <a name="spatial-operations-service"></a>Serviço de Operações Espaciais

O serviço de Operações Espaciais Azure Maps requer informações sobre a localização. Analisa rapidamente a informação de localização para ajudar a informar os clientes de eventos em curso que ocorram no tempo e no espaço. Permite a análise quase em tempo real e a modelação preditiva dos eventos. 

O serviço permite que os clientes melhorem a sua inteligência de localização com uma biblioteca de cálculos matemáticos geoespaciais comuns. Os cálculos comuns incluem o ponto mais próximo, grande distância do círculo e tampão. Para saber mais sobre o serviço e as várias funcionalidades, leia a documentação da [API de Operações Espaciais.](https://docs.microsoft.com/rest/api/maps/spatial)

### <a name="time-zone-service"></a>Serviço de Fuso Horário

O serviço Time Zone permite-lhe consultar informações atuais, históricas e futuras do fuso horário. Você pode usar pares de latitude e longitude ou um [ID IANA](https://www.iana.org/). O serviço Time Zone também permite:

* Conversão de IDs de fuso horário do Microsoft Windows para fusos horários IANA.
* Buscar um fuso horário para a UTC.
* Obtenção da hora atual num fuso horário escolhido. 

Uma resposta típica de JSON para uma consulta ao serviço Time Zone parece a seguinte amostra:

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

Para mais informações sobre este serviço, leia a documentação da [API do Fuso Horário.](https://docs.microsoft.com/rest/api/maps/timezone)

### <a name="traffic-service"></a>Serviço de tráfego

O serviço de tráfego é um conjunto de serviços web que os desenvolvedores podem usar para aplicações web ou móveis que requerem informações de tráfego. O serviço fornece dois tipos de dados:

* Fluxo de tráfego: Velocidades e tempos de viagem em tempo real para todas as principais estradas da rede.
* Incidentes de trânsito: Uma visão atualizada dos engarrafamentos e incidentes em torno da rede rodoviária.

![Exemplo de um mapa com informações de tráfego](media/about-azure-maps/intro_traffic.png)

Para mais informações, consulte a documentação da [API de Tráfego.](https://docs.microsoft.com/rest/api/maps/traffic)

### <a name="ip-to-location-service"></a>Serviço IP para Localização

Utilize o serviço IP para Localização para visualizar o código país/região de duas letras recuperado para um endereço IP. Este serviço pode ajudá-lo a melhorar a experiência do utilizador, fornecendo conteúdo de aplicação personalizado baseado na localização geográfica.

Para mais detalhes sobre o serviço IP to Location na API REST, leia a documentação da [API de Geolocalização Azure Maps](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Modelo de programação

O Azure Maps é construído para mobilidade e pode ajudá-lo a desenvolver aplicações multi-plataformas. Usa um modelo de programação que é agnóstico linguístico e suporta a saída JSON através [de REST APIs](https://docs.microsoft.com/rest/api/maps/).

Além disso, o Azure Maps oferece um [conveniente controlo de mapas JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-control) com um modelo de programação simples. O desenvolvimento é rápido e fácil tanto para aplicações web como móveis.

## <a name="usage"></a>Utilização

Aceder aos serviços do Azure Maps é uma questão de ir ao [portal Azure](https://portal.azure.com) e criar uma conta Azure Maps.

O Azure Maps utiliza um esquema de autenticação baseado em chave. A sua conta vem com duas teclas já geradas para si, use ambas as chaves. Comece a integrar estas capacidades de localização na sua aplicação e faça pedidos aos serviços do Azure Maps.

Nota - A Azure Maps partilha consultas de endereço/localização fornecidas pelo cliente ("Consultas") com o TomTom de terceiros para fins de funcionalidade de mapeamento. As consultas não estão ligadas a nenhum cliente ou utilizador final quando partilhadas com o TomTom e não podem ser usadas para identificar indivíduos. A Microsoft está neste momento em processo de adicionar o TomTom à Lista de Subcontratados de Serviços Online. Note que os Serviços de Mobilidade e Meteorologia que incluem integração com a Moovit e a AccuWeather estão atualmente em [PREVIEW](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-regions"></a>Regiões suportadas

As APIs Azure Maps estão atualmente disponíveis em todos os países e regiões, exceto estes:

* China
* Coreia do Sul

Verifique se a localização do seu endereço IP atual se encontra num país/região suportado.

## <a name="next-steps"></a>Passos seguintes

Experimente uma aplicação de amostra que mostra o Azure Maps:

> [!div class="nextstepaction"]
> [Quickstart: Criar uma aplicação web](quick-demo-map-app.md)

Mantenha-se atualizado no Azure Maps: 

> [!div class="nextstepaction"]
> [Blog do Azure Maps](https://azure.microsoft.com/blog/topics/azure-maps/)
