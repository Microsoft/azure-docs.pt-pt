---
title: Visão geral  Microsoft Azure Maps
description: Saiba mais sobre serviços e capacidades no Microsoft Azure Maps e como usá-los nas suas aplicações.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/15/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 411ffb9c05b0f6b4d8b7d37a11d0838c8734ec5e
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79079800"
---
# <a name="what-is-azure-maps"></a>O que é o Azure Maps?

O Azure Maps é uma coleção de serviços geoespaciais que utilizam dados de mapeamento frescos para fornecer contexto geográfico a aplicações web e móveis. O Azure Maps fornece:

* REST APIs para renderizar mapas em vários estilos e em imagens de satélite.
* Serviços de pesquisa para localizar endereços, lugares e pontos de interesse em todo o mundo.
* Várias opções de encaminhamento; tais como o itinerário ponto a ponto, multiponto, multiponto, isocronona, veículo comercial, influência do tráfego e encaminhamento matricial.
* Vista de fluxo de tráfego e visualização de incidentes, para aplicações que requerem informações de tráfego.
* Serviço de mobilidade para solicitar logística de trânsito público, planear rotas em tempo real e solicitar informações para modos alternativos de transporte.
* Serviços de fuso horário e geolocalização e conversão de localização para fusos horários.
* Serviço de geofencing e armazenamento de dados de mapeamento, com informações de localização hospedadas em Azure. 
* Inteligência de localização através da análise geoespacial. 

Além disso, os serviços Do Azure Maps estão disponíveis através do Web SDK ou do Android SDK. Estas ferramentas ajudam os desenvolvedores a desenvolver rapidamente e escalar soluções que integram a informação de localização em soluções Azure. 

Pode inscrever-se para uma [conta Azure Maps](https://azure.microsoft.com/services/azure-maps/) gratuita e começar a desenvolver-se.

O vídeo seguinte explica o Azure Maps detalhadamente:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Controlos de mapas

### <a name="web-sdk"></a>Web SDK

O Azure Maps Web SDK permite personalizar mapas interativos com o seu próprio conteúdo e imagens. Pode utilizar este mapa interativo tanto para as suas aplicações web como móveis. O controlo do mapa faz uso do WebGL, para que possa render grandes conjuntos de dados com alto desempenho. Desenvolva-se com o SDK utilizando o JavaScript ou o TypeScript.

![Mapa de exemplo da mudança de população](media/about-azure-maps/intro_web_map_control.png)

### <a name="android-sdk"></a>SDK Android

Utilize o Azure Maps Android SDK para criar aplicações de mapeamento móvel. 

![Exemplos de mapa em um dispositivo móvel](media/about-azure-maps/android_sdk.png)

## <a name="services-in-azure-maps"></a>Serviços no Azure Maps

O Azure Maps é composto pelos seguintes nove serviços que podem fornecer contexto geográfico às suas aplicações Azure.

### <a name="data-service"></a>Serviço de Dados

Os dados são imperativos para mapas. Utilize o Serviço de Dados para carregar e armazenar dados geoespaciais para utilização com operações espaciais ou composição de imagem.  Aproximar os dados dos clientes do serviço Azure Maps reduzirá a latência, aumentará a produtividade e criará novos cenários nas suas aplicações. Para mais informações sobre este serviço, consulte a documentação da [API do Serviço](https://docs.microsoft.com/rest/api/maps/data)de Dados.

### <a name="mobility-service"></a>Serviço de Mobilidade

O serviço de Mobilidade Azure Maps permite o planeamento de viagens em tempo real. Devolve as melhores opções de rota possíveis e oferece diferentes tipos de modos de viagem. Para as áreas de metro (cidade), estes modos podem incluir caminhadas, ciclismo e trânsito público. Pode solicitar itinerário de trânsito, geometria de linha, listas de paragens, chegadas programadas, chegadas em tempo real e alertas de serviço.

O serviço também permite pesquisas de tipos específicos de objetos em torno de um local. Os utilizadores podem procurar bicicletas partilhadas, scooters ou carros em torno de um local. Os utilizadores podem solicitar o número de bicicletas disponíveis na doca mais próxima e pesquisar veículos disponíveis para partilha de boleias. E, os utilizadores podem encontrar detalhes como a disponibilidade futura de veículos e o nível atual de combustível.

Para saber mais sobre o serviço, consulte a documentação da [Mobility API.](https://docs.microsoft.com/rest/api/maps/mobility)

### <a name="render-service"></a>Serviço de composição

O serviço Render ajuda os desenvolvedores a criar aplicações web e móveis com funcionalidades de mapeamento. O serviço utiliza imagens gráficas em padrão de alta qualidade, disponíveis em 19 níveis de zoom ou imagens de mapa de formato de vetor totalmente personalizáveis.

![Exemplo de um mapa do serviço Render](media/about-azure-maps/intro_map.png)

Agora, o serviço de Composição oferece APIs de pré-visualização para permitir que os programadores trabalhem com imagens de satélite. Para mais detalhes, leia a [documentação render da API.](https://docs.microsoft.com/rest/api/maps/render)

### <a name="route-service"></a>Serviço de encaminhamento

O serviço de Composição contém cálculos de geometria consistentes de infraestruturas do mundo real e múltiplas direções baseadas nos meios de transporte. O serviço permite que os desenvolvedores calculem direções em vários modos de viagem, como carro, caminhão, bicicleta ou caminhada. O serviço também considera inputs como condições de trânsito, restrições de peso ou transporte de materiais perigosos.

![Exemplo de um mapa do serviço Route](media/about-azure-maps/intro_route.png)

O serviço Route oferece uma pré-visualização de funcionalidades avançadas, tais como: 

* Processamento de lote de vários pedidos de rota.
* Matrizes de tempo de viagem e distância entre um conjunto de origens e destinos.
* Encontrar rotas ou distâncias que os utilizadores possam viajar com base nos requisitos de tempo ou combustível. 

Para mais detalhes sobre as capacidades de encaminhamento, leia a documentação da [Rota API](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Serviço de pesquisa

O serviço de Pesquisa ajuda os desenvolvedores a procurar endereços, lugares, listas de negócios por nome ou categoria, e outras informações geográficas. O serviço de pesquisa pode reverter endereços [de geocódigo](https://en.wikipedia.org/wiki/Reverse_geocoding) e atravessar ruas com base em latitudes e longitudes.

![Exemplo de uma pesquisa em um mapa](media/about-azure-maps/intro_search.png)

O serviço de Pesquisa também fornece funcionalidades avançadas tais como:

* Procure ao longo de um percurso.
* Procure dentro de uma área mais ampla.
* Emita um grupo de pedidos de pesquisa.
* Procure uma área maior em vez de um ponto de localização. 

As APIs de pesquisa de área e batch estão atualmente em pré-visualização. Para mais detalhes sobre as capacidades de pesquisa, leia a documentação da [API de pesquisa](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations-service"></a>Serviço de Operações Espaciais

O serviço de Operações Espaciais Azure Maps retira informações sobre a localização. Rapidamente analisa informações de localização para ajudar a informar os clientes de eventos em curso que acontecem no tempo e no espaço. Permite análises em tempo real e modelação preditiva de eventos. 

O serviço permite que os clientes melhorem a sua inteligência de localização com uma biblioteca de cálculos matemáticos geoespaciais comuns. Os cálculos comuns incluem o ponto mais próximo, grande distância do círculo e tampão. Para saber mais sobre o serviço e as várias funcionalidades, leia a documentação da API de [Operações Espaciais.](https://docs.microsoft.com/rest/api/maps/spatial)

### <a name="time-zone-service"></a>Serviço de Fuso Horário

O serviço Time Zone permite-lhe consultar informações atuais, históricas e futuras do fuso horário. Você pode usar pares de latitude e longitude ou um [ID IANA](https://www.iana.org/). O serviço Time Zone também permite:

* Converter iDs do fuso horário do Microsoft Windows para fusos horários da IANA.
* Buscar um fuso horário à UTC.
* Obter o tempo atual em um fuso horário escolhido. 

Uma resposta típica da JSON para uma consulta ao serviço time zone parece ser a seguinte amostra:

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

Para mais informações sobre este serviço, leia a documentação da API do [Fuso Horário.](https://docs.microsoft.com/rest/api/maps/timezone)

### <a name="traffic-service"></a>Serviço de tráfego

O serviço de tráfego é um conjunto de serviços web que os desenvolvedores podem usar para aplicações web ou móveis que requerem informações de tráfego. O serviço fornece dois tipos de dados:

* Fluxo de tráfego: Velocidades e tempos de viagem observados em tempo real para todas as principais estradas da rede.
* Incidentes de trânsito: Uma visão atualizada dos engarrafamentos e incidentes em torno da rede rodoviária.

![Exemplo de um mapa com informações de tráfego](media/about-azure-maps/intro_traffic.png)

Para mais informações, consulte a documentação da API de [Trânsito.](https://docs.microsoft.com/rest/api/maps/traffic)

### <a name="ip-to-location-service"></a>Serviço IP to Location

Utilize o serviço IP to Location para pré-visualizar o código de duas letras recuperado para um endereço IP. Este serviço pode ajudá-lo a melhorar a experiência do utilizador fornecendo conteúdo de aplicação personalizado com base na localização geográfica.

Para mais detalhes sobre o serviço IP to Location na Rest API, leia a [documentação da API de Geolocalização Do Mapa Azure](https://docs.microsoft.com/rest/api/maps/geolocation)Maps.

## <a name="programming-model"></a>Modelo de programação

O Azure Maps é construído para mobilidade e pode ajudá-lo a desenvolver aplicações cross-platform. Usa um modelo de programação que é agnóstico linguístico e suporta a saída jSON através de [APIs REST](https://docs.microsoft.com/rest/api/maps/).

Além disso, o Azure Maps oferece um conveniente controlo de [mapas JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-control) com um modelo de programação simples. O desenvolvimento é rápido e fácil tanto para aplicações web como móveis.

## <a name="usage"></a>Utilização

Aceder aos serviços do Azure Maps é uma questão de ir ao [portal Azure](https://portal.azure.com) e criar uma conta Azure Maps.

O Azure Maps utiliza um esquema de autenticação baseado em chave. A sua conta vem com duas chaves já geradas para si, use qualquer uma das teclas. Comece a integrar estas capacidades de localização na sua aplicação e faça pedidos aos serviços do Azure Maps.

Nota - O Azure Maps partilha consultas de endereço/localização fornecidas pelo cliente ("Consultas") com tomTom de terceiros para efeitos de funcionalidade de mapeamento. As consultas não estão ligadas a nenhum cliente ou utilizador final quando partilhadas com o TomTom e não podem ser usadas para identificar indivíduos. A Microsoft encontra-se neste momento em processo de adição do TomTom à Lista de Subempreiteiros de Serviços Online. Note que os Serviços de Mobilidade e Meteorologia que incluem integração com moovit e AccuWeather estão atualmente na [PREVIEW](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-regions"></a>Regiões suportadas

As APIs do Azure Maps estão atualmente disponíveis em todos os países e regiões, com exceção destes:

* China
* Coreia do Sul

Verifique se a localização do seu endereço IP atual está num país apoiado.

## <a name="next-steps"></a>Passos seguintes

Experimente uma aplicação de amostras que mostre o Azure Maps:

> [!div class="nextstepaction"]
> [Quickstart: Criar uma aplicação web](quick-demo-map-app.md)

Mantenha-se atualizado no Azure Maps: 

> [!div class="nextstepaction"]
> [Blog Azure Maps](https://azure.microsoft.com/blog/topics/azure-maps/)
