---
title: Visão geral para Microsoft Azure Maps
description: Saiba mais sobre serviços e capacidades no Microsoft Azure Maps e como usá-los nas suas aplicações.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, references_regions
ms.openlocfilehash: b53af0fda8bb24bf03d0e02f61ce7d195ae69503
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680829"
---
# <a name="what-is-azure-maps"></a>O que é o Azure Maps?

O Azure Maps é uma coleção de serviços geoespaciais e SDKs que utilizam dados de mapeamento frescos para fornecer contexto geográfico a aplicações web e móveis. O Azure Maps fornece:

* REST APIs para renderizar mapas de vetores e raster em vários estilos e imagens de satélite.
* Serviços de criador (Preview) para criar e renderizar mapas com base em dados de mapas interiores privados.
* Serviços de pesquisa para localizar endereços, locais e pontos de interesse em todo o mundo.
* Várias opções de encaminhamento; tais como ponto a ponto, multiponto, otimização de múltiplos pontos, isochrone, veículo elétrico, veículo comercial, tráfego influenciado e encaminhamento de matriz.
* Vista de fluxo de tráfego e vista para incidentes, para aplicações que requerem informações de tráfego em tempo real.
* Serviços de mobilidade (Pré-visualização) para solicitar informações de trânsito público, planear rotas, misturando diferentes modos de viagem e chegadas em tempo real.
* Serviços de fuso horário e geolocalização (pré-visualização).
* Serviços de elevação (Pré-visualização) com Modelo de Elevação Digital
* Serviço de geofencing e armazenamento de dados de mapeamento, com informações de localização hospedadas em Azure.
* Inteligência de localização através de análise geoespacial.

Além disso, os serviços do Azure Maps estão disponíveis através do Web SDK e do Android SDK. Estas ferramentas ajudam os desenvolvedores a desenvolver e escalar rapidamente soluções que integram informação de localização em soluções Azure.

Pode inscrever-se numa [conta gratuita do Azure Maps](https://azure.microsoft.com/services/azure-maps/) e começar a desenvolver-se.

O vídeo seguinte explica o Azure Maps detalhadamente:

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny]

## <a name="map-controls"></a>Controlos de mapa

### <a name="web-sdk"></a>Web SDK

O Azure Maps Web SDK permite personalizar mapas interativos com o seu próprio conteúdo e imagens. Pode utilizar este mapa interativo tanto para as suas aplicações web como para dispositivos móveis. O controlo do mapa faz uso do WebGL, para que possa renderizar grandes conjuntos de dados com alto desempenho. Pode desenvolver-se com o SDK utilizando o JavaScript ou o TypeScript.

:::image type="content" source="./media/about-azure-maps/intro_web_map_control.png" alt-text="Mapa de exemplo da mudança de população criado usando Azure Maps Web SDK":::

### <a name="android-sdk"></a>SDK Android

Utilize o Azure Maps Android SDK para criar aplicações de mapeamento móvel.

:::image type="content" source="./media/about-azure-maps/android_sdk.png" border="false" alt-text="Exemplos de mapas em um dispositivo móvel":::

## <a name="services-in-azure-maps"></a>Serviços no Azure Maps

O Azure Maps é composto pelos seguintes serviços que podem fornecer contexto geográfico às suas aplicações Azure.

### <a name="data-service-preview"></a>Serviço de dados (Pré-visualização)

Os dados são imperativos para mapas. Utilize o serviço Data para carregar e armazenar dados geoespaciais para utilização com operações espaciais ou composição de imagem.  Aproximar os dados dos clientes do serviço Azure Maps reduzirá a latência, aumentará a produtividade e criará novos cenários nas suas aplicações. Para mais informações sobre este serviço, consulte a documentação do [serviço de Dados.](/rest/api/maps/data)

### <a name="geolocation-service-preview"></a>Serviço de geolocalização (Pré-visualização)

Utilize o serviço de Geolocalização para visualizar o código país/região de duas letras recuperado para um endereço IP. Este serviço pode ajudá-lo a melhorar a experiência do utilizador, fornecendo conteúdo de aplicação personalizado baseado na localização geográfica.

Para mais detalhes, leia a documentação do [serviço de Geolocalização.](/rest/api/maps/geolocation)

### <a name="mobility-services-preview"></a>Serviços de mobilidade (Pré-visualização) 

Os serviços de Mobilidade Azure Maps melhoram o tempo de desenvolvimento de aplicações com características de trânsito público, como o encaminhamento de trânsito e a procura de paragens de trânsito público nas proximidades. Os utilizadores podem obter informações detalhadas sobre paragens, linhas e horários de trânsito. O serviço de Mobilidade permite ainda aos utilizadores recuperar geometrias de paragem e linha, alertas para paragens, linhas e áreas de serviço, bem como chegadas e alertas de serviço em tempo real de trânsito público. Além disso, os serviços de Mobilidade fornecem capacidades de encaminhamento com opções de planeamento de viagens multimodais. O planeamento de viagens multimodais incorpora opções de caminhada, ciclismo e trânsito público, tudo numa viagem. Os utilizadores também podem aceder a itinerários multimodais detalhados passo a passo.

Para saber mais sobre o serviço, consulte a documentação dos [serviços de Mobilidade.](/rest/api/maps/mobility)

### <a name="render-service"></a>Serviço de composição

O [serviço de renderização V2 (Preview)](/rest/api/maps/renderv2) introduz uma nova versão do [Get Map Tile V2 API](/rest/api/maps/renderv2/getmaptilepreview). A API Get Map Tile V2 permite agora que os clientes solicitem azulejos de estrada Azure Maps, azulejos meteorológicos ou os azulejos de mapas criados com o Azure Maps Creator. Recomenda-se que utilize o novo Get Map Tile V2 API.  

:::image type="content" source="./media/about-azure-maps/intro_map.png" border="false" alt-text="Exemplo de um mapa do serviço render V2":::

Para mais detalhes, leia a [documentação do serviço render V2.](/rest/api/maps/renderv2)

Para saber mais sobre o serviço de renderização V1 que está em GA (Disponibilidade Geral), consulte a [documentação do serviço de renderização V1](/rest/api/maps/render).  

### <a name="route-service"></a>Serviço de encaminhamento

Os serviços de rota podem ser utilizados para calcular os horários estimados de chegada (AE) para cada rota solicitada. As APIs da Rota consideram fatores, como informações de tráfego em tempo real e dados históricos de tráfego, como as velocidades típicas da estrada no dia da semana e hora do dia solicitados. As APIs devolvem as rotas mais curtas ou rápidas disponíveis para vários destinos de cada vez em sequência ou em ordem otimizada, com base no tempo ou na distância. O serviço permite que os desenvolvedores calculem direções em vários modos de viagem, tais como carro, caminhão, bicicleta, ou passeio, e veículo elétrico. O serviço também considera inputs, como a hora de partida, restrições de peso ou transporte de materiais perigosos.

:::image type="content" source="./media/about-azure-maps/intro_route.png" border="false" alt-text="Exemplo de um mapa do serviço Rota":::

O serviço Route oferece funcionalidades avançadas de conjunto, tais como:

* Processamento de lote de pedidos de rota múltiplas.
* Matrizes do tempo de viagem e distância entre um conjunto de origens e destinos.
* Encontrar rotas ou distâncias que os utilizadores possam percorrer com base nos requisitos de tempo ou combustível.

Para mais informações sobre as capacidades de encaminhamento, leia a [documentação](/rest/api/maps/route)do serviço Rota .

### <a name="search-service"></a>Serviço de pesquisa

O serviço de Pesquisa ajuda os desenvolvedores a procurar endereços, lugares, listas de negócios por nome ou categoria, e outras informações geográficas. Além disso, os serviços podem reverter endereços [de geocódigo](https://en.wikipedia.org/wiki/Reverse_geocoding) e atravessar ruas com base em latitudes e longitudes.

:::image type="content" source="./media/about-azure-maps/intro_search.png" border="false" alt-text="Exemplo de uma pesquisa em um mapa":::

O serviço De pesquisa também fornece funcionalidades avançadas, tais como:

* Procure ao longo de uma rota.
* Procurem dentro de uma área mais ampla.
* Lote de um grupo de pedidos de pesquisa.
* Procure postos de carregamento de veículos elétricos e dados do Ponto de Interesse (POI) pela marca.

Para obter mais detalhes sobre as capacidades de pesquisa, leia a documentação do [serviço de busca.](/rest/api/maps/search)

### <a name="spatial-service"></a>Serviço espacial

O serviço Spatial analisa rapidamente as informações de localização para ajudar a informar os clientes sobre os eventos em curso que ocorram no tempo e no espaço. Permite a análise quase em tempo real e a modelação preditiva dos eventos.

O serviço permite que os clientes melhorem a sua inteligência de localização com uma biblioteca de cálculos matemáticos geoespaciais comuns. Os cálculos comuns incluem o ponto mais próximo, grande distância do círculo e tampão. Para saber mais sobre o serviço e as várias funcionalidades, leia a documentação do [serviço Espacial.](/rest/api/maps/spatial)

### <a name="timezone-service"></a>Serviço de fuso horário

O serviço de fuso horário permite-lhe consultar informações atuais, históricas e futuras do fuso horário. Pode utilizar pares de latitude e longitude ou um [ID IANA](https://www.iana.org/) como entrada. O serviço de fuso horário também permite:

* Conversão de IDs de fuso horário do Microsoft Windows para fusos horários IANA.
* Buscar um fuso horário para a UTC.
* Obtenção da hora atual num fuso horário escolhido.

Uma resposta típica de JSON para uma consulta ao serviço de fuso horário parece a seguinte amostra:

```JSON
{
  "Version": "2020a",
  "ReferenceUtcTimestamp": "2020-07-31T19:15:14.4570053Z",
  "TimeZones": [
    {
      "Id": "America/Los_Angeles",
      "Names": {
        "ISO6391LanguageCode": "en",
        "Generic": "Pacific Time",
        "Standard": "Pacific Standard Time",
        "Daylight": "Pacific Daylight Time"
      },
      "ReferenceTime": {
        "Tag": "PDT",
        "StandardOffset": "-08:00:00",
        "DaylightSavings": "01:00:00",
        "WallTime": "2020-07-31T12:15:14.4570053-07:00",
        "PosixTzValidYear": 2020,
        "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
      }
    }
  ]
}
```

Para mais informações sobre este serviço, leia a documentação do [serviço de fuso horário.](/rest/api/maps/timezone)

### <a name="traffic-service"></a>Serviço de tráfego

O serviço de tráfego é um conjunto de serviços web que os desenvolvedores podem usar para aplicações web ou móveis que requerem informações de tráfego. O serviço fornece dois tipos de dados:

* Fluxo de tráfego: Velocidades e tempos de viagem em tempo real para todas as principais estradas da rede.
* Incidentes de trânsito: Uma visão atualizada dos engarrafamentos e incidentes em torno da rede rodoviária.

![Exemplo de um mapa com informações de tráfego](media/about-azure-maps/intro_traffic.png)

Para mais informações, consulte a [documentação do serviço de tráfego.](/rest/api/maps/traffic)

### <a name="weather-services-preview"></a>Serviços meteorológicos (Pré-visualização) 

Os serviços meteorológicos oferecem APIs que os desenvolvedores podem usar para recuperar informações meteorológicas para um determinado local. As informações contêm detalhes como data e hora de observação, breve descrição das condições meteorológicas, ícone meteorológico, bandeiras indicadoras de precipitação, temperatura e informações sobre velocidade do vento. Detalhes adicionais como RealFeel™ Temperatura e índice UV também são devolvidos.

Os desenvolvedores podem usar o [Get Weather ao longo da rota API](/rest/api/maps/weather/getweatheralongroutepreview) para recuperar informações meteorológicas ao longo de uma determinada rota. Além disso, o serviço suporta a geração de notificações meteorológicas para os pontos de passagem que são afetados por perigos meteorológicos, como inundações ou chuvas fortes.

A [API Get Map Tile V2](/rest/api/maps/renderv2/getmaptilepreview) permite-lhe solicitar azulejos de radar e satélite passados, atuais e futuros.

![Exemplo de mapa com azulejos de radar meteorológico em tempo real](media/about-azure-maps/intro_weather.png)

### <a name="maps-creator-service-preview"></a>Serviço Maps Creator (Pré-visualização) 

O serviço Maps Creator é um conjunto de serviços web que os desenvolvedores podem usar para criar aplicações com funcionalidades de mapas baseadas em dados de mapas internos.

O Maps Creator fornece três serviços principais:

* [Serviço dataset](/rest/api/maps/dataset). Utilize o serviço Dataset para criar um conjunto de dados a partir de dados de pacotes de desenho convertidos. Para obter informações sobre os requisitos do pacote de desenho, consulte os requisitos do pacote de desenho.

* [Serviço de conversão](/rest/api/maps/dataset). Utilize o serviço de Conversão para converter um ficheiro de design DWG em dados de pacote de desenho para mapas interiores.

* [Serviço de azulejos.](/rest/api/maps/tileset) Utilize o serviço Tileset para criar uma representação baseada em vetores de um conjunto de dados. As aplicações podem usar um teesto para apresentar uma visão baseada em azulejos visuais do conjunto de dados.

* [Serviço estado de recurso](/rest/api/maps/featurestate). Utilize o serviço Estado recurso para suportar o estilo dinâmico do mapa. O estilo dinâmico do mapa permite que as aplicações reflitam eventos em tempo real em espaços fornecidos por sistemas IoT.

* [Serviço WFS](/rest/api/maps/featurestate). Utilize o serviço WFS para consultar os dados do mapa interno. O serviço WFS segue os padrões API do [Consórcio Geoespacial Aberto](http://docs.opengeospatial.org/is/17-069r3/17-069r3.html) para consulta de um único conjunto de dados.

### <a name="elevation-service-preview"></a>Serviço de elevação (Pré-visualização)

O serviço Azure Maps Elevation é um serviço web que os desenvolvedores podem usar para recuperar dados de elevação de qualquer lugar na superfície da Terra.

O serviço elevação permite-lhe recuperar dados de elevação em dois formatos:

* **Formato de raster GeoTIFF.** Utilize o [Render V2 - Obtenha a API de Azulejos do Mapa](/rest/api/maps/renderv2) para recuperar dados de elevação em formato de azulejo.

* **Formato GeoJSON**. Utilize as APIs de [elevação](/rest/api/maps/elevation) para solicitar dados de elevação amostrados ao longo de caminhos, dentro de uma caixa de delimitação definida, ou em coordenadas específicas. 

:::image type="content" source="./media/about-azure-maps/elevation.png" alt-text="Exemplo de mapa com dados de elevação":::


## <a name="programming-model"></a>Modelo de programação

O Azure Maps é construído para mobilidade e pode ajudá-lo a desenvolver aplicações multi-plataformas. Usa um modelo de programação que é agnóstico linguístico e suporta a saída JSON através [de REST APIs](/rest/api/maps/).

Além disso, o Azure Maps oferece um [conveniente controlo de mapas JavaScript](/javascript/api/azure-maps-control) com um modelo de programação simples. O desenvolvimento é rápido e fácil tanto para aplicações web como móveis.





## <a name="power-bi-visual"></a>Elemento visual do Power BI

O Azure Maps visual para Power BI fornece um rico conjunto de visualizações de dados para dados espaciais em cima de um mapa. Estima-se que mais de 80% dos dados do negócio têm um contexto de localização. O visual Azure Maps oferece uma solução sem código para obter informações sobre como este contexto de localização se relaciona e influencia os dados do seu negócio.

:::image type="content" source="./media/about-azure-maps/intro-power-bi.png" border="false" alt-text="Power BI desktop com o Azure Maps visualizando dados de negócio":::

Para mais informações, consulte a Obtenção começou com a documentação [visual do Azure Maps Power BI.](power-bi-visual-getting-started.md)

## <a name="usage"></a>Utilização

Para aceder aos serviços do Azure Maps, vá ao [portal Azure](https://portal.azure.com) e crie uma conta Azure Maps.

O Azure Maps utiliza um esquema de autenticação baseado em chave. Quando cria a sua conta, duas chaves são geradas. Para autenticar os serviços do Azure Maps, pode utilizar qualquer uma das teclas.

Nota - A Azure Maps partilha consultas de endereço/localização fornecidas pelo cliente ("Consultas") com o TomTom de terceiros para fins de funcionalidade de mapeamento. As consultas não estão ligadas a nenhum cliente ou utilizador final quando partilhadas com o TomTom e não podem ser usadas para identificar indivíduos. Os serviços de Mobilidade e Meteorologia, que incluem integração com a Moovit, e a AccuWeather estão atualmente em [PREVIEW](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Microsoft está neste momento em processo de adicionar TomTom, Moovit e AccuWeather à Lista de Subcontratados de Serviços Online.

## <a name="supported-regions"></a>Regiões suportadas

Os serviços Azure Maps estão atualmente disponíveis, exceto nos seguintes países/regiões:

* China
* Coreia do Sul

Verifique se a localização do seu endereço IP atual se encontra num país/região suportado.

## <a name="next-steps"></a>Passos seguintes

Experimente uma aplicação de amostra que mostra o Azure Maps:

[Quickstart: Criar uma aplicação web](quick-demo-map-app.md)

Mantenha-se atualizado no Azure Maps:

[Blog do Azure Maps](https://azure.microsoft.com/blog/topics/azure-maps/)