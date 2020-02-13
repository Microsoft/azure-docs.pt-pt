---
title: Visão geral  Microsoft Azure Maps
description: Saiba mais sobre os serviços e funcionalidades do Microsoft Azure Maps e como usá-los em seus aplicativos.
author: walsehgal
ms.author: v-musehg
ms.date: 02/11/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e01bfec9edbe1e868a3e3244e3cb6d1e1a46561d
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190342"
---
# <a name="what-is-azure-maps"></a>O que é o Azure Maps?

O mapas do Azure é uma coleção de serviços geoespaciais que usam dados de mapeamento atualizados para fornecer contexto geográfico para aplicativos Web e móveis. O Azure Maps fornece:

* REST APIs para renderizar mapas em vários estilos e em imagens de satélite.
* Serviços de pesquisa para localizar endereços, lugares e pontos de interesse em todo o mundo.
* Várias opções de encaminhamento; tais como o itinerário ponto a ponto, multiponto, multiponto, isocronona, veículo comercial, influência do tráfego e encaminhamento matricial.
* Vista de fluxo de tráfego e visualização de incidentes, para aplicações que requerem informações de tráfego.
* Serviço de mobilidade para solicitar logística de trânsito público, planear rotas em tempo real e solicitar informações para modos alternativos de transporte.
* Serviços de fuso horário e geolocalização e conversão de localização para fusos horários.
* Serviço de geofencing e armazenamento de dados de mapeamento, com informações de localização hospedadas em Azure. 
* Inteligência de localização através da análise geoespacial. 

Além disso, os serviços do Azure Maps estão disponíveis por meio do SDK da Web ou do SDK do Android. Estas ferramentas ajudam os desenvolvedores a desenvolver rapidamente e escalar soluções que integram a informação de localização em soluções Azure. 

Pode inscrever-se para uma [conta Azure Maps](https://azure.microsoft.com/services/azure-maps/) gratuita e começar a desenvolver-se.

O vídeo seguinte explica o Azure Maps detalhadamente:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Controlos de mapas

### <a name="web-sdk"></a>Web SDK

O Azure Maps Web SDK permite personalizar mapas interativos com o seu próprio conteúdo e imagens. Pode utilizar este mapa interativo tanto para as suas aplicações web como móveis. O controlo do mapa faz uso do WebGL, para que possa render grandes conjuntos de dados com alto desempenho. Desenvolva-se com o SDK utilizando o JavaScript ou o TypeScript.

![Mapa de exemplo da mudança de população](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>SDK Android

Utilize o Azure Maps Android SDK para criar aplicações de mapeamento móvel. 

![Exemplos de mapa em um dispositivo móvel](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Serviços no Azure Maps

O Azure Maps é composto pelos seguintes nove serviços que podem fornecer contexto geográfico às suas aplicações Azure.

### <a name="data-service"></a>Serviço de Dados

Os dados são imperativos para mapas. Utilize o Serviço de Dados para carregar e armazenar dados geoespaciais para utilização com operações espaciais ou composição de imagem.  Aproximar os dados dos clientes do serviço Azure Maps reduzirá a latência, aumentará a produtividade e criará novos cenários nas suas aplicações. Para mais informações sobre este serviço, consulte a documentação da [API do Serviço](https://docs.microsoft.com/rest/api/maps/data)de Dados.

### <a name="mobility-service"></a>Serviço de Mobilidade

O serviço de Mobilidade Azure Maps permite o planeamento de viagens em tempo real. Ele retorna as melhores opções de rota possíveis e fornece tipos diferentes de modos de viagem. Para as áreas de metro (cidade), estes modos podem incluir caminhadas, ciclismo e trânsito público. Pode solicitar itinerário de trânsito, geometria de linha, listas de paragens, chegadas programadas, chegadas em tempo real e alertas de serviço.

O serviço também permite pesquisas de tipos específicos de objetos em torno de um local. Os utilizadores podem procurar bicicletas partilhadas, scooters ou carros em torno de um local. Os usuários podem solicitar o número de bicicletas disponíveis no encaixe mais próximo e Pesquisar veículos disponíveis para compartilhamento de Rides. E, os utilizadores podem encontrar detalhes como a disponibilidade futura de veículos e o nível atual de combustível.

Para saber mais sobre o serviço, consulte a documentação da [Mobility API.](https://docs.microsoft.com/rest/api/maps/mobility)

### <a name="render-service"></a>Serviço de composição

O serviço Render ajuda os desenvolvedores a criar aplicações web e móveis com funcionalidades de mapeamento. O serviço utiliza imagens gráficas em padrão de alta qualidade, disponíveis em 19 níveis de zoom ou imagens de mapa de formato de vetor totalmente personalizáveis.

![Exemplo de um mapa do serviço Render](media/about-azure-maps/Introduction_Map.png)

Agora, o serviço de Composição oferece APIs de pré-visualização para permitir que os programadores trabalhem com imagens de satélite. Para mais detalhes, leia a [documentação render da API.](https://docs.microsoft.com/rest/api/maps/render)

### <a name="route-service"></a>Serviço de encaminhamento

O serviço de Composição contém cálculos de geometria consistentes de infraestruturas do mundo real e múltiplas direções baseadas nos meios de transporte. O serviço permite que os desenvolvedores calculem direções em vários modos de viagem, como carro, caminhão, bicicleta ou caminhada. O serviço também considera inputs como condições de trânsito, restrições de peso ou transporte de materiais perigosos.

![Exemplo de um mapa do serviço Route](media/about-azure-maps/Introduction_Route.png)

O serviço Route oferece uma pré-visualização de funcionalidades avançadas, tais como: 

* Processamento de lote de vários pedidos de rota.
* Matrizes de tempo de viagem e distância entre um conjunto de origens e destinos.
* Encontrar rotas ou distâncias que os utilizadores possam viajar com base nos requisitos de tempo ou combustível. 

Para mais detalhes sobre as capacidades de encaminhamento, leia a documentação da [Rota API](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Serviço de pesquisa

O serviço de Pesquisa ajuda os desenvolvedores a procurar endereços, lugares, listas de negócios por nome ou categoria, e outras informações geográficas. O serviço de pesquisa pode reverter endereços [de geocódigo](https://en.wikipedia.org/wiki/Reverse_geocoding) e atravessar ruas com base em latitudes e longitudes.

![Exemplo de uma pesquisa em um mapa](media/about-azure-maps/Introduction_Search.png)

O serviço de Pesquisa também fornece funcionalidades avançadas tais como:

* Procure ao longo de um percurso.
* Procure dentro de uma área mais ampla.
* Emita um grupo de pedidos de pesquisa.
* Procure uma área maior em vez de um ponto de localização. 

As APIs de pesquisa de área e batch estão atualmente em pré-visualização. Para mais detalhes sobre as capacidades de pesquisa, leia a documentação da [API de pesquisa](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations-service"></a>Serviço de Operações Espaciais

O serviço de Operações Espaciais Azure Maps retira informações sobre a localização. Rapidamente analisa informações de localização para ajudar a informar os clientes de eventos em curso que acontecem no tempo e no espaço. Permite análises em tempo real e modelação preditiva de eventos. 

O serviço permite que os clientes aprimorem sua inteligência de local com uma biblioteca de cálculos matemáticos geoespaciais comuns. Cálculos comuns incluem ponto mais próximo, distância de círculo grande e buffers. Para saber mais sobre o serviço e as várias funcionalidades, leia a documentação da API de [Operações Espaciais.](https://docs.microsoft.com/rest/api/maps/spatial)

### <a name="time-zone-service"></a>Serviço de Fuso Horário

O serviço de fuso horário permite consultar informações atuais, históricas e futuras de fuso horário. Você pode usar pares de latitude e longitude ou um [ID IANA](https://www.iana.org/). O serviço Time Zone também permite:

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

O serviço de tráfego é um pacote de serviços Web que os desenvolvedores podem usar para aplicativos Web ou móveis que exigem informações de tráfego. O serviço fornece dois tipos de dados:

* Fluxo de tráfego: Velocidades e tempos de viagem observados em tempo real para todas as principais estradas da rede.
* Incidentes de trânsito: Uma visão atualizada dos engarrafamentos e incidentes em torno da rede rodoviária.

![Exemplo de um mapa com informações de tráfego](media/about-azure-maps/Introduction_Traffic.png)

Para mais informações, consulte a documentação da API de [Trânsito.](https://docs.microsoft.com/rest/api/maps/traffic)

### <a name="ip-to-location-service"></a>Serviço IP to Location

Utilize o serviço IP to Location para pré-visualizar o código de duas letras recuperado para um endereço IP. Esse serviço pode ajudá-lo a melhorar a experiência do usuário fornecendo conteúdo de aplicativo personalizado com base na localização geográfica.

Para mais detalhes sobre o serviço IP to Location na Rest API, leia a [documentação da API de Geolocalização Do Mapa Azure](https://docs.microsoft.com/rest/api/maps/geolocation)Maps.

## <a name="programming-model"></a>Modelo de programação

O Azure Maps é construído para mobilidade e pode ajudá-lo a desenvolver aplicações cross-platform. Usa um modelo de programação que é agnóstico linguístico e suporta a saída jSON através de [APIs REST](https://docs.microsoft.com/rest/api/maps/).

Além disso, o Azure Maps oferece um conveniente controlo de [mapas JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-control) com um modelo de programação simples. O desenvolvimento é rápido e fácil para aplicativos Web e móveis.

## <a name="usage"></a>Utilização

Aceder aos serviços do Azure Maps é uma questão de ir ao [portal Azure](https://portal.azure.com) e criar uma conta Azure Maps.

O Azure Maps utiliza um esquema de autenticação baseado em chave. A sua conta vem com duas chaves já geradas para si, use qualquer uma das teclas. Comece a integrar estas capacidades de localização na sua aplicação e faça pedidos aos serviços do Azure Maps.

## <a name="supported-regions"></a>Regiões suportadas

As APIs do Azure Maps estão disponíveis atualmente em todos os países e regiões, exceto os seguintes:

* China
* Coreia do Sul

Verifique se o local do seu endereço IP atual está em um país com suporte.

## <a name="next-steps"></a>Passos seguintes

Experimente uma aplicação de amostras que mostre o Azure Maps:

> [!div class="nextstepaction"]
> [Quickstart: Criar uma aplicação web](quick-demo-map-app.md)

Mantenha-se atualizado no Azure Maps: 

> [!div class="nextstepaction"]
> [Blog Azure Maps](https://azure.microsoft.com/blog/topics/azure-maps/)
