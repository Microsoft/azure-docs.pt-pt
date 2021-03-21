---
title: 'Tutorial: Migrar serviços web da Bing Maps | Microsoft Azure Maps'
description: Tutorial sobre como migrar serviços web de Bing Maps para Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6024aae68183fbe02125ef4207e9fbce8abd6a2b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97679077"
---
# <a name="tutorial-migrate-web-service-from-bing-maps"></a>Tutorial: Migrar o serviço web a partir de Bing Maps

Tanto o Azure como o Bing Maps fornecem acesso a APIs espaciais através dos serviços web REST. As interfaces API para estas plataformas executam funcionalidades semelhantes, mas utilizam diferentes convenções de nomeação e objetos de resposta. Neste tutorial, vai aprender a:

> * Geocoding para a frente e reverso
> * Procurar pontos de interesse
> * Calcular rotas e direções
> * Recupere uma imagem de mapa
> * Calcular uma matriz de distância
> * Obtenha detalhes do fuso horário

A tabela seguinte fornece as APIs de serviço Azure Maps que fornecem uma funcionalidade semelhante às APIs do serviço Bing Maps listado.

| Serviço Bing Maps API                 | A azure Maps serviço API      |
|---------------------------------------|-----------------------------|
| Sugestão Automática                           | [Pesquisa](/rest/api/maps/search)     |
| Instruções (incluindo o camião)          | [Direções de rota](/rest/api/maps/route/getroutedirections)                          |
| Matriz de distância                       | [Matriz de Rota](/rest/api/maps/route/postroutematrixpreview)                          |
| Imagens - Mapa Estático                  | [Composição](/rest/api/maps/render/getmapimage)                                   |
| Isochrones                            | [Gama de Rotas](/rest/api/maps/route/getrouterange)                                    |
| Insights locais                        | [Pesquisar](/rest/api/maps/search)  +  [Gama de Rotas](/rest/api/maps/route/getrouterange)    |
| Pesquisa local                          | [Pesquisa](/rest/api/maps/search)     |
| Reconhecimento de Localização (POIs)           | [Pesquisa](/rest/api/maps/search)     |
| Localizações (geocoding para a frente/reverso) | [Pesquisa](/rest/api/maps/search)                                               |
| Estalar para a estrada                          | [Direções da rota POST](/rest/api/maps/route/postroutedirections)                         |
| Serviços de Dados Espaciais (SDS)           | [Pesquisar](/rest/api/maps/search)  +  [Rota](/rest/api/maps/route) + outros Serviços Azure |
| Fuso Horário                             | [Fuso Horário](/rest/api/maps/timezone)  |
| Incidentes de Trânsito                     | [Detalhes do incidente de tráfego](/rest/api/maps/traffic/gettrafficincidentdetail)                     |
| Elevação                             | [Elevação (Pré-visualização)](/rest/api/maps/elevation)

As apis de serviço a seguir não estão atualmente disponíveis no Azure Maps:

-   Itinerário otimizado - Planejado. A Azure Maps Route API suporta a otimização de vendedores de viagens para um único veículo.
-   Metadados de imagens – Usado principalmente para obter URLs de azulejos em Bing Maps. O Azure Maps tem um serviço autónomo para aceder diretamente aos azulejos do mapa.

O Azure Maps dispõe de vários serviços web REST adicionais que podem ser de interesse;

-   [Azure Maps Creator (Preview) ](./creator-indoor-maps.md) – Crie um gémeo digital privado personalizado de edifícios e espaços.
-   [Operações espaciais](/rest/api/maps/spatial) – Descarregar cálculos e operações espaciais complexos, como geofencing, para um serviço.
-   [Map Tiles](/rest/api/maps/render/getmaptile) – Acesso a azulejos rodoviários e de imagens do Azure Maps como raster e telhas vetoriais.
-   [Encaminhamento de lote](/rest/api/maps/route/postroutedirectionsbatchpreview) – Permite que até 1.000 pedidos de rota sejam feitos num único lote durante um período de tempo. As rotas são calculadas paralelamente no servidor para um processamento mais rápido.
-   [Tráfego](/rest/api/maps/traffic) Fluxo – Aceda aos dados de fluxo de tráfego em tempo real como azulejos raster e vetor.
-   [Geolocalização API (Pré-visualização)](/rest/api/maps/geolocation/getiptolocationpreview) – Obtenha a localização de um endereço IP.
-   [Serviços meteorológicos](/rest/api/maps/weather) – Obtenha acesso a dados meteorológicos em tempo real e previsão.

Certifique-se de rever também os seguintes guias de boas práticas:

-   [Melhores práticas de pesquisa](./how-to-use-best-practices-for-search.md)
-   [Melhores práticas para o encaminhamento](./how-to-use-best-practices-for-routing.md)

## <a name="prerequisites"></a>Pré-requisitos

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
2. [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps.](how-to-manage-authentication.md)

## <a name="geocoding-addresses"></a>Endereços de geocoding

Geocoding é o processo de conversão de um endereço `"1 Microsoft way, Redmond, WA"` (como) numa coordenada (como longitude: -122.1298, latitude: 47.64005). As coordenadas são então usadas frequentemente para posicionar um pino de pressão em um mapa ou centrar um mapa.

O Azure Maps fornece vários métodos para endereços de geocoding;

-   [Geocoding de endereço de formulário gratuito](/rest/api/maps/search/getsearchaddress): Especifique uma única cadeia de endereços (como) e `"1 Microsoft way, Redmond, WA"` processe o pedido imediatamente. Este serviço é recomendado se precisar de geocodificar os endereços individuais rapidamente.
-   [Endereço estruturado geocoding](/rest/api/maps/search/getsearchaddressstructured): Especificar as partes de um único endereço, como o nome de rua, cidade, país e código postal e processar o pedido imediatamente. Este serviço é recomendado se necessitar de geocodificar rapidamente os endereços individuais e os dados já estiverem analisados nas suas partes de endereços individuais.
-   [Geocoding do endereço do lote:](/rest/api/maps/search/postsearchaddressbatchpreview)Crie um pedido que contenha até 10.000 endereços e processe-os durante um período de tempo. Todos os endereços serão geocodificados em paralelo no servidor e quando concluído o conjunto de resultados completo pode ser descarregado. Este serviço é recomendado para geocoding grandes conjuntos de dados.
-   [Pesquisa fuzzy](/rest/api/maps/search/getsearchfuzzy): Esta API combina geocoding de endereço com pesquisa de ponto de interesse. Esta API recebe uma cadeia de formulário livre que pode ser um endereço, lugar, marco, ponto de interesse, ou categoria de ponto de interesse e processar o pedido imediatamente. Esta API é recomendada para aplicações onde os utilizadores podem pesquisar endereços ou pontos de interesse a partir da mesma caixa de texto.
-   [Pesquisa de lote felpudo](/rest/api/maps/search/postsearchfuzzybatchpreview): Crie um pedido que contenha até 10.000 endereços, locais, marcos ou pontos de interesse e processe-os durante um período de tempo. Todos os dados serão processados em paralelo no servidor e quando concluídos o conjunto de resultados completos podem ser descarregados.

Os quadros que se seguem cruzam os parâmetros da API bing Maps com os parâmetros API comparáveis no Azure Maps para geocoding de endereços estruturados e de forma livre.

**Localização por Endereço (endereço estruturado)**

| Bing Maps API parâmetro              | Parâmetro API de Mapas Azuis comparáveis                 |
|--------------------------------------|-----------------------------------------------------|
| `addressLine`                      | `streetNumber`, `streetName` ou `crossStreet` |
| `adminDistrict`                    | `countrySubdivision`                              |
| `countryRegion`                    | `country` e `countryCode`                     |
| `locality`                         | `municipality` ou `municipalitySubdivision`     |
| `postalCode`                       | `postalCode`                                      |
| `maxResults` (`maxRes`)          | `limit`                                           |
| `includeNeighborhood` (`inclnb`) | N/A – Sempre devolvido pela Azure Maps se disponível.   |
| `include` (`incl`)               | N/A – Código COUNTRY ISO2 sempre devolvido pelo Azure Maps. |
| `key`                              | `subscription-key`– Consulte também a [documentação autenticação com Azure Maps.](./azure-maps-authentication.md) |
| `culture` (`c`)                  | `language`– Consulte a documentação [de línguas suportadas.](./supported-languages.md) |
| `userRegion` (`ur`)              | `view`– Consulte a documentação [de vistas suportadas.](./supported-languages.md#azure-maps-supported-views) |

O Azure Maps também apoia;

* `countrySecondarySubdivision` – Condado, distritos
* `countryTertiarySubdivision` - Áreas nomeadas; bairros, cantões, comunas
* `ofs` - Página através dos resultados em combinação com `maxResults` parâmetro.

**Localização por Consulta (cadeia de endereço de formulário livre)**

| Bing Maps API parâmetro              | Parâmetro API de Mapas Azuis comparáveis      |
|--------------------------------------|------------------------------------------|
| `query`                            | `query`                                |
| `maxResults` (`maxRes`)          | `limit`                                |
| `includeNeighborhood` (`inclnb`) | N/A – Sempre devolvido pela Azure Maps se disponível.  |
| `include` (`incl`)               | N/A – Código COUNTRY ISO2 sempre devolvido pelo Azure Maps.  |
| `key`                              | `subscription-key`– Consulte também a [documentação autenticação com Azure Maps.](./azure-maps-authentication.md) |
| `culture` (`c`)                  | `language`– Consulte a documentação [de línguas suportadas.](./supported-languages.md)  |
| `userRegion` (`ur`)              | `view`– Consulte a documentação [de vistas suportadas.](./supported-languages.md#azure-maps-supported-views) |

O Azure Maps também apoia;

* `typeahead` - Espécies se a consulta for interpretada como uma entrada parcial e a pesquisa entrará no modo preditivo (autosuggest/autocomplete).
* `countrySet` – Uma lista separada por vírgula de códigos de países ISO2 para limitar a procura.
* `lat`/`lon`, `topLeft` / `btmRight` `radius` – Especifique a localização e a área do utilizador para tornar os resultados mais relevantes localmente.
* `ofs` - Página através dos resultados em combinação com `maxResults` parâmetro.

Um exemplo de como utilizar o serviço de pesquisa é documentado [aqui.](./how-to-search-for-address.md) Certifique-se de rever as [melhores práticas para](./how-to-use-best-practices-for-search.md) documentação de pesquisa.

## <a name="reverse-geocode-a-coordinate-find-a-location-by-point"></a>Reverter geocódigo uma coordenada (Encontrar uma localização por ponto)

A geocodagem inversa é o processo de conversão de coordenadas geográficas (como a longitude: -122.1298, latitude: 47.64005) no seu endereço aproximado `"1 Microsoft way, Redmond, WA"` (como).

O Azure Maps fornece vários métodos de geocodagem inversa;

-   [Endereço de geocodificador inverso](/rest/api/maps/search/getsearchaddressreverse): Especifique uma única coordenada geográfica para obter o seu endereço aproximado e processar o pedido imediatamente.
-   [Cross street reverse geocoder](/rest/api/maps/search/getsearchaddressreversecrossstreet): Especifique uma única coordenada geográfica para obter informações de rua transversal próxima (por exemplo, 1º & principal) e processar o pedido imediatamente.
-   [Endereço de lote reverso geocodificador](/rest/api/maps/search/postsearchaddressreversebatchpreview): Crie um pedido que contenha até 10.000 coordenadas e processe-os durante um período de tempo. Todos os dados serão processados em paralelo no servidor e quando concluídos o conjunto de resultados completos podem ser descarregados.

A tabela seguinte cruza referências aos parâmetros API Bing Maps com os parâmetros API comparáveis em Azure Maps.

| Bing Maps API parâmetro              | Parâmetro API de Mapas Azuis comparáveis       |
|--------------------------------------|-------------------------------------------|
| `point`                              | `query`                                   |
| `includeEntityTypes`                 | `entityType` – Ver tabela de comparação de tipo de entidade abaixo.    |
| `includeNeighborhood` (`inclnb`)     | N/A – Sempre devolvido pela Azure Maps se disponível.         |
| `include` (`incl`)                   | N/A – Código COUNTRY ISO2 sempre devolvido pelo Azure Maps.    |
| `key`                                | `subscription-key`– Consulte também a [documentação autenticação com Azure Maps.](./azure-maps-authentication.md) |
| `culture` (`c`)                      | `language`– Consulte a documentação [de línguas suportadas.](./supported-languages.md)   |
| `userRegion` (`ur`)                  | `view`– Consulte a documentação [de vistas suportadas.](./supported-languages.md#azure-maps-supported-views) |

Certifique-se de rever as [melhores práticas para](./how-to-use-best-practices-for-search.md) documentação de pesquisa.

A Azure Maps reverse geocoding API tem algumas funcionalidades adicionais não disponíveis no Bing Maps que podem ser úteis para integrar ao migrar a sua app:

* Recupere os dados do limite de velocidade.
* Recuperar informações sobre o uso da estrada; estrada local, arterial, acesso limitado, rampa, etc.
* Do lado da rua, a coordenada cai.

**Tabela de comparação de tipo de entidade**

A tabela seguinte cruza referências aos valores de tipo de entidade Bing Maps aos nomes de propriedade equivalentes no Azure Maps.

| Tipo de Entidade Bing Maps | Tipo de entidade Azure Maps comparável               | Description                                |
|-----------------------|-------------------------------------------------|--------------------------------------------|
| `Address`             |                                                 | *Endereço*                                  |
| `Neighborhood`        | `Neighbourhood`                                 | *Bairro*                             |
| `PopulatedPlace`      | `Municipality` ou `MunicipalitySubdivision`     | *Cidade*, *Cidade ou Sub,* ou *Super Cidade*     |
| `Postcode1`           | `PostalCodeArea`                                | *Código Postal* ou *Código Postal*                |
| `AdminDivision1`      | `CountrySubdivision`                            | *Estado* ou *Província*                      |
| `AdminDivision2`      | `CountrySecondarySubdivison`                    | *Condado* ou *distritos*                    |
| `CountryRegion`       | `Country`                                       | *Nome do país*                             |
|                       | `CountryTertiarySubdivision`                    | *Bairros,* *Cantões,* *Comunas*          |

## <a name="get-location-suggestions-autosuggest"></a>Obtenha sugestões de localização (Autosuggest)

Vários dos Azure Maps pesquisam o modo preditivo de suporte da API que pode ser usado para cenários de autosusuggest. A Azure Maps [pesquisa fuzzy API](/rest/api/maps/search/getsearchfuzzy) é a mais parecida com a Bing Maps Autosuggest API. O modo preditivo de suporte da API também, adicione `&typeahead=true` à consulta;

-   [Geocoding de endereço de formulário gratuito](/rest/api/maps/search/getsearchaddress): Especifique uma única cadeia de endereços (como) e `"1 Microsoft way, Redmond, WA"` processe o pedido imediatamente. Este serviço é recomendado se precisar de geocodificar os endereços individuais rapidamente.
-   [Pesquisa fuzzy](/rest/api/maps/search/getsearchfuzzy): Esta API combina geocoding de endereço com pesquisa de ponto de interesse. Esta API recebe uma cadeia de formulário livre que pode ser um endereço, lugar, marco, ponto de interesse, ou categoria de ponto de interesse e processar o pedido imediatamente. Esta API é recomendada para aplicações onde os utilizadores podem pesquisar endereços ou pontos de interesse a partir da mesma caixa de texto.
-   [Pesquisa de POI](/rest/api/maps/search/getsearchpoi): Procure pontos de interesse pelo nome. Por exemplo; `"starbucks"`.
-   [Pesquisa na categoria POI](/rest/api/maps/search/getsearchpoicategory): Procure pontos de interesse por categoria. Por exemplo; "Restaurante".

## <a name="calculate-routes-and-directions"></a>Calcular rotas e direções

O Azure Maps pode ser usado para calcular rotas e direções. O Azure Maps tem muitas das mesmas funcionalidades que o serviço de encaminhamento Bing Maps, como;

* tempos de chegada e partida
* rotas de tráfego em tempo real e preditivas
* diferentes modos de transporte; condução, andar, caminhão
* otimização de ordem de waypoint (vendedores de viagens)

> [!NOTE]
> O Azure Maps requer que todos os pontos de passagem sejam coordenadas. Os endereços terão de ser geocodificados primeiro.

O serviço de encaminhamento Azure Maps fornece as seguintes APIs para o cálculo das rotas;

-   [Calcular rota:](/rest/api/maps/route/getroutedirections)Calcular uma rota e ter o pedido processado imediatamente. Esta API suporta pedidos GET e POST. Os pedidos de POST são recomendados ao especificar um grande número de pontos de passagem ou quando se utilizam muitas das opções de rota para garantir que o pedido de URL não se torna muito longo e causa problemas.
-   [Rota do lote](/rest/api/maps/route/postroutedirectionsbatchpreview): Crie um pedido que contenha até 1.000 pedidos de rota e processe-os durante um período de tempo. Todos os dados serão processados em paralelo no servidor e quando concluídos o conjunto de resultados completos podem ser descarregados.
-   [Serviços de mobilidade (Pré-visualização) ](/rest/api/maps/mobility): Calcular rotas e direções utilizando o trânsito público.

A tabela seguinte cruza referências aos parâmetros API Bing Maps com os parâmetros API comparáveis em Azure Maps.

| Bing Maps API parâmetro                                    | Parâmetro API de Mapas Azuis comparáveis               |
|------------------------------------------------------------|---------------------------------------------------|
| `avoid`                                                    | `avoid`                                           |
| `dateTime` (`dt`)                                          | `departAt` ou `arriveAt`                          |
| `distanceBeforeFirstTurn` (`dbft`)                         | N/D                                               |
| `distanceUnit` (`du`)                                      | N/A – Azure Maps utiliza apenas o sistema métrico.     |
| `heading` (`hd`)                                           | `vehicleHeading`                                  |
| `maxSolutions` (`maxSolns`)                                | `maxAlternatives`, `alternativeType` `minDeviationDistance` e `minDeviationTime`  |
| `optimize` (`optwz`)                                       | `routeType` e `traffic`                         |
| `optimizeWaypoints` (`optWp`)                              | `computeBestOrder`                                |
| `routeAttributes` (`ra`)                                   | `instructionsType`                                |
| `routePathOutput` (`rpo`)                                  | `routeRepresentation`                             |
| `timeType` (`tt`)                                          | `departAt` ou `arriveAt`                          |
| `tolerances` (`tl`)                                        | N/D                                               |
| `travelMode`                                               | `travelMode`                                      |
| `waypoint.n` () `wp.n` ou `viaWaypoint.n` (`vwp.n`)         | `query` – coordenadas no formato `lat0,lon0:lat1,lon1….`   |
| `key`                                                      | `subscription-key`– Consulte também a [documentação autenticação com Azure Maps.](./azure-maps-authentication.md) |
| `culture` (`c`)                                            | `language`– Consulte a documentação [de línguas suportadas.](./supported-languages.md) |
| `userRegion` (`ur`)                                        | `view`– Consulte a documentação [de vistas suportadas.](./supported-languages.md#azure-maps-supported-views) |

A Azure Maps encaminhamento API também suporta o encaminhamento de camiões dentro da mesma API. A tabela seguinte cruza referências aos parâmetros adicionais de encaminhamento do caminhão Bing Maps com os parâmetros API comparáveis no Azure Maps.

| Bing Maps API parâmetro                  | Parâmetro API de Mapas Azuis comparáveis        |
|------------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                 | N/A – Dimensões em metros apenas suportados. |
| `weightUnit` (`wu`)                      | N/A – Pesos em quilogramas suportados apenas. |
| `vehicleHeight` (`height`)               | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                 | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                   | `vehicleLength`                            |
| `vehicleWeight` (`weight`)               | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                 | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                 | **N/A**                                    |
| `vehicleSemi` (`semi`)                   | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)             | **N/A**                                    |
| `vehicleMinTurnRadius` (`vmtr`)          | **N/A**                                    |
| `vehicleAvoidCrossWind` (`vacw`)         | **N/A**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)     | **N/A**                                    |
| `vehicleHazardousMaterials` (`vhm`)      | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)        | `vehicleLoadType`                          |

> [!TIP]
> Por predefinição, a rota Azure Maps API apenas devolve um resumo (distância e tempos) e as coordenadas para o caminho de rota. Utilize o `instructionsType` parâmetro para recuperar instruções de turn-by-turn. O `routeRepresentation` parâmetro pode ser utilizado para filtrar o resumo e o percurso.

Certifique-se de também rever as [melhores práticas para a](./how-to-use-best-practices-for-routing.md) documentação de encaminhamento.

A Azure Maps encaminhando a API tem muitas funcionalidades adicionais não disponíveis no Bing Maps que podem ser úteis para integrar ao migrar a sua app:

* Suporte para o tipo de rota: mais curto, mais rápido, trilha e mais eficiente em combustível.
* Suporte para modos de viagem adicionais: bicicleta, autocarro, motocicleta, táxi, caminhão e van.
* Apoio para 150 pontos de passagem.
* Calcular vários tempos de viagem num único pedido; tráfego histórico, tráfego ao vivo, sem tráfego.
* Evite tipos de estradas adicionais: estradas de carpool, estradas não pavimentadas, estradas já utilizadas.
* Encaminhamento baseado em especificações do motor. Calcular as rotas para a combustão ou veículos elétricos com base nas restantes especificações de combustível/carga e motor.
* Especifique a velocidade máxima do veículo.

## <a name="snap-coordinates-to-road"></a>Coordenadas de encaixe para a Estrada

Há várias formas de captar coordenadas para as estradas em Azure Maps.

* Utilize as direções de rota API para encaixar as coordenadas para uma rota lógica ao longo da rede rodoviária.
* Use o Azure Maps Web SDK para encaixar as coordenadas individuais na estrada mais próxima nos azulejos vetoriais.
* Use os azulejos vetoriais Azure Maps diretamente para estalar as coordenadas individuais.

**Usando a direção de rota API para estalar coordenadas**

O Azure Maps pode captar coordenadas para as estradas utilizando as [direções](/rest/api/maps/route/postroutedirections) de rota API. Este serviço pode ser usado para reconstruir uma rota lógica entre um conjunto de coordenadas e é comparável ao Bing Maps Snap para a Road API.

Existem duas maneiras diferentes de usar as direções de rota API para tirar coordenadas para as estradas.

* Se houver 150 coordenadas ou menos, podem ser passadas como pontos de passagem na rota GET direções API. Utilizando esta abordagem, podem ser recuperados dois tipos diferentes de dados adquiridos; as instruções de rota conterão os pontos de passagem individuais, enquanto a rota terá um conjunto interpolado de coordenadas que preenchem todo o caminho entre as coordenadas.
* Se existirem mais de 150 coordenadas, a rota POST pode ser utilizada. As coordenadas iniciam e terminam as coordenadas têm de ser passadas para o parâmetro de consulta, mas todas as coordenadas podem ser passadas para o `supportingPoints` parâmetro no corpo do pedido POST e formatadas uma coleção de pontos geoJSON geometria. Os únicos dados disponíveis usando esta abordagem serão o caminho de rota que é um conjunto interpolado de coordenadas que preenchem todo o caminho entre as coordenadas. [Aqui está um exemplo](https://azuremapscodesamples.azurewebsites.net/?sample=Snap%20points%20to%20logical%20route%20path) desta abordagem usando o módulo de serviços no Azure Maps Web SDK.

A tabela seguinte cruza referências aos parâmetros API Bing Maps com os parâmetros API comparáveis em Azure Maps.

| Bing Maps API parâmetro    | Parâmetro API de Mapas Azuis comparáveis                                 |
|----------------------------|---------------------------------------------------------------------|
| `points`                   | `supportingPoints` – passar estes pontos para o corpo do pedido do correio  |
| `interpolate`              | N/D                                                                 |
| `includeSpeedLimit`        | N/D                                                                 |
| `includeTruckSpeedLimit`   | N/D                                                                 |
| `speedUnit`                | N/D                                                                 |
| `travelMode`               | `travelMode`                                                        |
| `key`                      | `subscription-key`– Consulte também a [documentação autenticação com Azure Maps.](./azure-maps-authentication.md) |
| `culture` (`c`)            | `language`– Consulte a documentação [de línguas suportadas.](./supported-languages.md)   |
| `userRegion` (`ur`)        | `view`– Consulte a documentação [de vistas suportadas.](./supported-languages.md#azure-maps-supported-views)   |

A Azure Maps encaminhamento API também suporta o parâmetro de encaminhamento de camiões dentro da mesma API para garantir que os caminhos lógicos são calculados. A tabela seguinte cruza referências aos parâmetros adicionais de encaminhamento do caminhão Bing Maps com os parâmetros API comparáveis no Azure Maps.

| Bing Maps API parâmetro                 | Parâmetro API de Mapas Azuis comparáveis        |
|-----------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                | N/A – Dimensões em metros apenas suportados. |
| `weightUnit` (`wu`)                     | N/A – Pesos em quilogramas suportados apenas. |
| `vehicleHeight` (`height`)              | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                  | `vehicleLength`                            |
| `vehicleWeight` (`weight`)              | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                | **N/A**                                    |
| `vehicleSemi` (`semi`)                  | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)            | **N/A**                                    |
| `vehicleMinTurnRadius` (`vmtr`)         | **N/A**                                    |
| `vehicleAvoidCrossWind` (`vacw`)        | **N/A**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)    | **N/A**                                    |
| `vehicleHazardousMaterials` (`vhm`)     | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)       | `vehicleLoadType`                          |

Uma vez que esta abordagem utiliza as direções de rota API, todo o conjunto de opções nesse serviço pode ser usado para personalizar a lógica usada para encaixar a coordenada nas estradas. Por exemplo, especificar uma hora de partida resultaria na tomada em consideração de dados históricos de tráfego.

A rota Azure Maps direções API não devolve atualmente dados de limite de velocidade, no entanto que podem ser recuperados usando a API de geocodagem inversa AZURE Maps.

**Usando o Web SDK para estalar coordenadas**

O Azure Maps Web SDK usa azulejos vetoriais para renderizar os mapas. Estes azulejos vetoriais contêm a informação de geometria da estrada bruta e podem ser usados para calcular a estrada mais próxima para uma coordenada para simples estalar as coordenadas individuais. Isto é útil quando quer que as coordenadas apareçam visualmente nas estradas e já está a usar o Azure Maps Web SDK para visualizar os dados.

No entanto, esta abordagem apenas se aproximará dos segmentos rodoviários que estão carregados dentro da vista do mapa. Quando ampliado a nível nacional pode não haver dados de estrada, por isso não é possível fazer estaladiços, no entanto, a esse nível de zoom, um único pixel pode representar a área de vários blocos da cidade, pelo que não é necessário estalar. Para resolver isto, a lógica de estalar pode ser aplicada sempre que o mapa terminar de se mover. [Aqui está uma amostra de código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic) que demonstra isto.

**Usando os azulejos vetoriais Azure Maps diretamente para estalar coordenadas**

Os azulejos vetoriais Azure Maps contêm os dados de geometria da estrada crus que podem ser usados para calcular o ponto mais próximo de uma estrada para uma coordenada para fazer o estalar básico de coordenadas individuais. Todos os segmentos de estrada aparecem nos sectores no zoom nível 15, por isso você vai querer recuperar azulejos de lá. Em seguida, pode usar a [pirâmide de azulejos quadtree](./zoom-levels-and-tile-grid.md) para determinar que os azulejos são necessários e converter os azulejos em geometrias. A partir daí, uma biblioteca de matemática espacial, como [o relvado js](http://turfjs.org/) ou [o NetTopologySuite,](https://github.com/NetTopologySuite/NetTopologySuite) pode ser usado para calcular os segmentos de linha mais próximos.

## <a name="retrieve-a-map-image-static-map"></a>Recupere uma imagem de mapa (Mapa Estático)

O Azure Maps fornece uma API para tornar as imagens estáticas do mapa com dados sobrepostos. A imagem do mapa Azure Maps [render](/rest/api/maps/render/getmapimagerytile) API é comparável ao mapa estático API em Bing Maps.

> [!NOTE]
> O Azure Maps requer que o centro, todos os pinos de pressão e locais do caminho sejam coordenadas em `longitude,latitude` formato, enquanto o Bing Maps utiliza o `latitude,longitude` formato.</p>
<p>Os endereços terão de ser geocodificados primeiro.

A tabela seguinte cruza referências aos parâmetros API Bing Maps com os parâmetros API comparáveis em Azure Maps.

| Bing Maps API parâmetro  | Parâmetro API de Mapas Azuis comparáveis            |
|--------------------------|------------------------------------------------|
| `centerPoint`            | `center`                                       |
| `format`                 | `format` – especificado como parte do caminho url. Atualmente apenas o PNG suportado.  |
| `heading`                | N/A – Streetside não suportado.                |
| `imagerySet`             | `layer`e `style` – Consulte a documentação dos [estilos de mapa suportados.](./supported-map-styles.md)   |
| `mapArea` (`ma`)         | `bbox`                                         |
| `mapLayer` (`ml`)        | N/D                                            |
| `mapSize` (`ms`)         | `width` e `height` – pode ser até 8192x8192 em tamanho. |
| `declutterPins` (`dcl`)  | N/D                                            |
| `dpi`                    | N/D                                            |
| `drawCurve`              | `path`                                         |
| `mapMetadata`            | N/D                                            |
| `pitch`                  | N/A – Streetside não suportado.                |
| `pushpin` (`pp`)         | `pins`                                         |
| `zoomLevel`              | `zoom`                                         |
| `query`                  | N/A – caixa central ou de delimitação deve ser utilizada.     |
| `highlightEntity` (`he`) | N/D                                            |
| `style`                  | N/D                                            |
| parâmetros de rota         | N/D                                            |
| `key`                    | `subscription-key`– Consulte também a [documentação autenticação com Azure Maps.](./azure-maps-authentication.md) |
| `culture` (`c`)          | `language`– Consulte a documentação [de línguas suportadas.](./supported-languages.md)   |
| `userRegion` (`ur`)      | `view`– Consulte a documentação [de vistas suportadas.](./supported-languages.md#azure-maps-supported-views) |

> [!NOTE]
> O Azure Maps usa um sistema de azulejos com azulejos que têm o dobro do tamanho dos azulejos do mapa utilizados no Bing Maps. Como tal, o valor de nível de zoom no Azure Maps aparecerá um nível de zoom mais próximo no Azure Maps em comparação com bing Maps. Baixe o nível de zoom nos pedidos que está a migrar em 1 para compensar isso.

Consulte o [guia de como fazer na imagem do mapa para obter](./how-to-render-custom-data.md) mais informações.

Além de ser capaz de gerar uma imagem de mapa estático, o serviço de renderização Azure Maps também fornece a capacidade de aceder diretamente aos azulejos de mapas em formato raster (PNG) e vetor;

-   [Azulejo de mapa](/rest/api/maps/render/getmaptile) – Recuperar raster (PNG) e telhas vetoriais para os mapas base (estradas, limites, fundo).
-   [Azulejo de imagens de mapa](/rest/api/maps/render/getmapimagerytile) – Recupere azulejos de imagens aéreas e de satélite.

### <a name="pushpin-url-parameter-format-comparison"></a>Comparação do formato do parâmetro de URL do pushpin

**Antes: Bing Maps**

Em Bing Maps, os pinos podem ser adicionados a uma imagem estática do mapa usando o `pushpin` parâmetro no URL. O `pushpin` parâmetro ocupa uma localização em `latitude,longitude` formato, um estilo de ícone e etiqueta de texto (até três caracteres) como mostrado abaixo:

> `&pushpin=latitude,longitude;iconStyle;label`

Os pinos de pressão adicionais podem ser adicionados adicionando parâmetros adicionais `pushpin` ao URL com um conjunto diferente de valores. Os estilos de ícones pushpin estão limitados a um dos estilos predefinidos disponíveis na API Bing Maps.

Por exemplo, em Bing Maps, um pressão vermelho com a etiqueta "AB" pode ser adicionado ao mapa nas coordenadas (longitude: -110, latitude: 45) com o seguinte parâmetro URL:

> `&pushpin=45,-110;7;AB`

![Pino de mapa estático Bing Maps](media/migrate-bing-maps-web-service/bing-maps-static-map-pin.jpg)

**Depois: Mapas Azure**

No Azure Maps, os pinos de pressão também podem ser adicionados a uma imagem estática do mapa especificando o `pins` parâmetro no URL. Os pinos de push em Azure Maps são definidos especificando um estilo de ícone e uma lista de locais que usam esse estilo de ícone. Esta informação é então transmitida para o `pins` parâmetro pode ser especificada várias vezes para suportar pinos com diferentes estilos.

> `&pins=iconType|pinStyles||pinLocation1|pinLocation2|...`

Estilos adicionais podem ser usados adicionando parâmetros adicionais `pins` ao URL com um estilo e conjunto diferente de locais.

No que diz respeito às localizações dos pinos, o Azure Maps requer que as coordenadas estejam em `longitude latitude` formato, enquanto o Bing Maps utiliza `latitude,longitude` o formato. Note também que **há um espaço, não uma vírgula** que separa a longitude e a latitude em Azure Maps.

O `iconType` valor especifica o tipo de pino para criar e pode ter os seguintes valores:

* `default` – O ícone do pino padrão.
* `none` – Não é apresentado nenhum ícone, apenas serão apresentadas etiquetas.
* `custom` – Especifica um ícone personalizado para ser usado. Um URL que aponta para a imagem do ícone pode ser adicionado ao fim do `pins` parâmetro após a informação da localização do pino.
* `{udid}` – Um ID de dados único (UDID) para um ícone armazenado na plataforma de armazenamento de dados Azure Maps.

Os estilos pin em Azure Maps são adicionados com o `optionNameValue` formato, com vários estilos separados por tubos ( `|` ) caracteres como este `iconType|optionName1Value1|optionName2Value2` . Note que os nomes e valores das opções não estão separados. Os seguintes nomes de opção de estilo podem ser usados para modelar pinos em Azure Maps:

* `al` – Especifica a opacidade (alfa) dos pinos. Pode ser um número entre 0 e 1.
* `an` – Especifica a âncora do pino. Valores X e y pixel especificados no formato `x y` .
* `co` – A cor do pino. Deve ser uma cor hexaduxa de 24 bits: `000000` para `FFFFFF` .
* `la` – Especifica a âncora da etiqueta. Valores X e y pixel especificados no formato `x y` .
* `lc` – A cor do rótulo. Deve ser uma cor de 24 mas hexadisso: `000000` para `FFFFFF` .
* `ls` – O tamanho da etiqueta em pixels. Pode ser um número maior que 0.
* `ro` – Um valor em graus para rodar o ícone. Pode ser um número entre -360 e 360.
* `sc` – Um valor de escala para o ícone do pino. Pode ser um número maior que 0.

Os valores da etiqueta são especificados para cada localização do pino em vez de ter um único valor de etiqueta que se aplica a todos os pinos na lista de locais. O valor da etiqueta pode ser uma cadeia de múltiplos caracteres e ser embrulhado com citações individuais para garantir que não é confundido como um estilo ou valor de localização.

Por exemplo, no Azure Maps, adicionando um ícone padrão vermelho `FF0000` () com a etiqueta "Space Needle" posicionada abaixo (15 50) o ícone nas coordenadas (longitude: -122.349300, latitude: 47.620180) pode ser feito com o seguinte parâmetro URL:

> `&pins=default|coFF0000|la15 50||'Space Needle'-122.349300 47.620180`

![Pino de mapa estático Azure Maps](media/migrate-bing-maps-web-service/azure-maps-static-map-pin.jpg)

O exemplo a seguir adiciona três pinos com os valores da etiqueta '1', '2' e '3':

> `&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12`

![Mapas Azure mapas gráficos múltiplos pinos](media/migrate-bing-maps-web-service/azure-maps-static-map-multiple-pins.jpg)

### <a name="draw-curve-url-parameter-format-comparison"></a>Desenhe a comparação do formato do parâmetro url da curva

**Antes: Bing Maps**

Em Bing Maps, linhas e polígonos podem ser adicionados a uma imagem estática do mapa usando o `drawCurve` parâmetro no URL. O `drawCurve` parâmetro toma um tipo de forma, um tipo de estilo e uma lista de locais a serem renderizados no mapa como mostrado abaixo:

> `&drawCurve=shapeType,styleType,location1,location2...`

Estilos adicionais podem ser usados adicionando parâmetros adicionais `drawCurve` ao URL com um estilo e conjunto diferente de locais.

As localizações em Bing Maps são especificadas com o formato `latitude1,longitude1_latitude2,longitude2_…` . As localizações também podem ser codificadas.

Os tipos de forma em Bing Maps incluem linhas, polígonos, círculo e curva. Os tipos de estilo incluem cor de linha, espessura de linha, cor de contorno, cor de preenchimento, espessura do contorno e raio circular.

Por exemplo, em Bing Maps, uma linha azul com 50% de opacidade e uma espessura de quatro pixels podem ser adicionados ao mapa entre coordenadas (longitude: -110, latitude: 45 e longitude: -100, latitude: 50) com o seguinte parâmetro URL:

`&drawCurve=l,FF000088,4;45,-110_50,-100`

![Linha de mapa estático Bing Maps](media/migrate-bing-maps-web-service/bing-maps-static-map-line.jpg)

**Depois: Mapas Azure**

Nos Mapas Azure, linhas e polígonos também podem ser adicionados a uma imagem estática do mapa, especificando o parâmetro do *caminho* no URL. Como Bing Maps, um estilo e uma lista de locais podem ser especificados neste parâmetro, e o parâmetro do *caminho* pode ser especificado várias vezes para tornar múltiplos círculos, linhas e polígonos com diferentes estilos.

> `&path=pathStyles||pathLocation1|pathLocation2|...`

No que diz respeito às localizações dos caminhos, o Azure Maps requer que as coordenadas estejam em `longitude latitude` formato, enquanto o Bing Maps utiliza `latitude,longitude` o formato. Note também que **há um espaço, não uma vírgula que separa a** longitude e a latitude em Azure Maps. O Azure Maps não suporta caminhos codificados atualmente. Conjuntos de dados maiores podem ser carregados à medida que um GeoJSON preenche a API de armazenamento de dados do Azure Maps, conforme documentado [aqui.](./how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)

Os estilos de caminho em Azure Maps são adicionados com o `optionNameValue` formato, com vários estilos separados por tubos ( `|` ) personagens como `optionName1Value1|optionName2Value2` este. Note que os nomes e valores das opções não estão separados. Os seguintes nomes de opção de estilo podem ser usados para estilo caminhos em Azure Maps:

* `fa` – A opacidade da cor de enchimento (alfa) utilizada na renderização de polígonos. Pode ser um número entre 0 e 1.
* `fc` – A cor de preenchimento utilizada para tornar a área de um polígono.
* `la` – A opacidade da cor da linha (alfa) utilizada na renderização de linhas e o contorno dos polígonos. Pode ser um número entre 0 e 1.
* `lc` – A cor da linha utilizada para renderizar linhas e o contorno dos polígonos.
* `lw` – A largura da linha em pixels.
* `ra` – Especifica um raio de círculos em metros.

Por exemplo, no Azure Maps, uma linha azul com 50% de opacidade e uma espessura de quatro pixels podem ser adicionados ao mapa entre coordenadas (longitude: -110, latitude: 45 e longitude: -100, latitude: 50) com o seguinte parâmetro URL:

> `&path=lc0000FF|la.5|lw4||-110 45|-100 50`

![Linha de mapa estático Azure Maps](media/migrate-bing-maps-web-service/azure-maps-static-map-line.jpg)

## <a name="calculate-a-distance-matrix"></a>Calcular uma matriz de distância

O Azure Maps fornece uma API para calcular os tempos de viagem e distâncias entre um conjunto de locais como matriz de distância. A matriz de distância Azure Maps API é comparável à matriz de distância API em Bing Maps;

-   [Matriz de rota](/rest/api/maps/route/postroutematrixpreview): Calcula assíncroticamente os tempos e distâncias de viagem para um conjunto de origens e destinos. Suportam-se até 700 células por pedido (o número de origens multiplicadas pelo número de destinos). Com esse constrangimento em mente, exemplos de possíveis dimensões matricias são: `700x1` `50x10` . . . `10x10` `28x25` . `10x70`

> [!NOTE]
> Um pedido para a matriz de distância API só pode ser feito usando um pedido POST com a informação de origem e destino no corpo do pedido.</p>
<p>Além disso, o Azure Maps requer que todas as origens e destinos sejam coordenadas. Os endereços terão de ser geocodificados primeiro.

A tabela seguinte cruza referências aos parâmetros API Bing Maps com os parâmetros API comparáveis em Azure Maps.

| Bing Maps API parâmetro | Parâmetro API de Mapas Azuis comparáveis                         |
|-------------------------|-------------------------------------------------------------|
| `origins`               | `origins` – especificar no órgão de pedido DO POST como GeoJSON.    |
| `destinations`          | `destination` – especificar no órgão de pedido DO POST como GeoJSON. |
| `endTime`               | `arriveAt`                                                  |
| `startTime`             | `departAt`                                                  |
| `travelMode`            | `travelMode`                                                |
| `resolution`            | N/D                                                         |
| `distanceUnit`          | N/A – Todas as distâncias nos metros.                              |
| `timeUnit`              | N/A - Todas as vezes em segundos.                                 |
| `key`                   | `subscription-key`– Consulte também a [documentação autenticação com Azure Maps.](./azure-maps-authentication.md) |
| `culture` (`c`)         | `language`– Consulte a documentação [de línguas suportadas.](./supported-languages.md)  |
| `userRegion` (`ur`)     | `view`– Consulte a documentação [de vistas suportadas.](./supported-languages.md#azure-maps-supported-views)     |

> [!TIP]
> Todas as opções avançadas de encaminhamento disponíveis na API de encaminhamento de encaminhamento AZure Maps (encaminhamento de camiões, especificações do motor, evite...) é suporte na matriz de distância AZure Maps API.

## <a name="calculate-an-isochrone"></a>Calcular um isocrone

O Azure Maps fornece uma API para o cálculo de um isocrono, um polígono que cobre uma área que pode ser percorrida em qualquer direção a partir de um ponto de origem dentro de uma quantidade de tempo ou quantidade de combustível/carga especificada. A gama de rotas Azure Maps API é comparável à API isochrone em Bing Maps;

-   [Rota](/rest/api/maps/route/getrouterange) Gama**: Calcular um polígono que cubra uma área que possa ser percorrida em qualquer direção a partir de um ponto de origem dentro de uma determinada quantidade de tempo, distância ou quantidade de combustível/carga disponível.

> [!NOTE]
> O Azure Maps requer que a origem da consulta seja uma coordenada. Os endereços terão de ser geocodificados primeiro.</p>
<p>Além disso, bing Maps pode calcular isochrones com base no tempo ou na distância, enquanto a Azure Maps pode calcular isochrones com base no tempo, distância ou quantidade de combustível/carga disponível.

A tabela seguinte cruza referências aos parâmetros API Bing Maps com os parâmetros API comparáveis em Azure Maps.

| Bing Maps API parâmetro      | Parâmetro API de Mapas Azuis comparáveis            |
|------------------------------|------------------------------------------------|
| `waypoint` (`wp`)            | `query`                                        |
| `dateTime` (`dt`)            | `departAt`                                     |
| `maxTime`                    | `timeBudgetInSec`                              |
| `timeUnit` (`tu`)            | N/A - Todas as vezes em segundos.                    |
| `travelMode` (`mode`)        | `travelMode`                                   |
| `maxDistance` (`maxDis`)     | `distanceBudgetInMeters`                       |
| `distanceUnit` (`du`)        | N/A – Todas as distâncias nos metros.                 |
| `optimize` (`optmz`)         | `routeType`                                    |
| `key`                        | `subscription-key`– Consulte também a [documentação autenticação com Azure Maps.](./azure-maps-authentication.md) |
| `culture` (`c`)              | `language`– Consulte a documentação [de línguas suportadas.](./supported-languages.md)  |
| `userRegion` (`ur`)          | `view`– Consulte a documentação [de vistas suportadas.](./supported-languages.md#azure-maps-supported-views) |

> [!TIP]
> Todas as opções avançadas de encaminhamento disponíveis na AZure Maps encaminhamento API (encaminhamento de camiões, especificações do motor, evite...) é suporte na API isochrone do Azure Maps.

## <a name="search-for-points-of-interest"></a>Procurar pontos de interesse

Os dados de pontos de interesse podem ser pesquisados no Bing Maps utilizando as seguintes APIs:

-   **Pesquisa local:** Procura pontos de interesse que estejam nas proximidades (pesquisa radial), por nome ou por tipo de entidade (categoria). A [pesquisa de POI Azure](/rest/api/maps/search/getsearchpoi) Maps e as APIs de pesquisa de [categorias POI](/rest/api/maps/search/getsearchpoicategory) são mais como esta API.
-   **Reconhecimento de localização**: Procura pontos de interesse que estejam a uma certa distância de um local. A Azure Maps [pesquisa perto](/rest/api/maps/search/getsearchnearby) API é mais como esta API.
-   **Insights locais:** Procura pontos de interesse que estejam dentro de um tempo de condução máximo especificado ou distância de uma coordenada específica. Isto é alcançável com Azure Maps calculando primeiro um isochrone e, em seguida, passando-o para a pesquisa dentro da [API de geometria.](/rest/api/maps/search/postsearchinsidegeometry)

A Azure Maps fornece várias APIs de pesquisa para pontos de interesse:

-   [Pesquisa de POI](/rest/api/maps/search/getsearchpoi): Procure pontos de interesse pelo nome. Por exemplo; `"starbucks"`.
-   [Pesquisa na categoria POI](/rest/api/maps/search/getsearchpoicategory): Procure pontos de interesse por categoria. Por exemplo; "Restaurante".
-   [Pesquisa nas proximidades](/rest/api/maps/search/getsearchnearby): Procure pontos de interesse que estejam a uma certa distância de um local.
-   [Pesquisa fuzzy](/rest/api/maps/search/getsearchfuzzy): Esta API combina geocoding de endereço com pesquisa de ponto de interesse. Esta API recebe uma cadeia de formulário livre que pode ser um endereço, lugar, marco, ponto de interesse, ou categoria de ponto de interesse e processar o pedido imediatamente. Esta API é recomendada para aplicações onde os utilizadores podem pesquisar endereços ou pontos de interesse a partir da mesma caixa de texto.
-   [Pesquisa em geometria](/rest/api/maps/search/postsearchinsidegeometry): Procure pontos de interesses dentro de uma geometria especificada (polígono).
-   [Pesse ao longo do percurso](/rest/api/maps/search/postsearchalongroute): Procure pontos de interesse que estejam ao longo de um caminho de rota especificado.
-   [Pesquisa de lote felpudo](/rest/api/maps/search/postsearchfuzzybatchpreview): Crie um pedido que contenha até 10.000 endereços, locais, marcos ou pontos de interesse e processe-os durante um período de tempo. Todos os dados serão processados em paralelo no servidor e quando concluídos o conjunto de resultados completos podem ser descarregados.

Certifique-se de rever as [melhores práticas para](./how-to-use-best-practices-for-search.md) documentação de pesquisa.

## <a name="get-traffic-incidents"></a>Obter incidentes de trânsito

O Azure Maps fornece várias APIs para recuperar dados de tráfego. Existem dois tipos de dados de tráfego disponíveis;

* **Dados de** fluxo – fornece métricas sobre o fluxo de tráfego em troços de estradas. Isto é frequentemente usado para colorir estradas de código. Estes dados são atualizados a cada 2 minutos.
* **Dados de incidentes** – fornece dados sobre construção, encerramentos de estradas, acidentes e outros incidentes que possam afetar o tráfego. Estes dados são atualizados a cada minuto.

O Bing Maps fornece dados de fluxo de tráfego e incidentes nos seus controlos de mapas interativos, e também disponibiliza dados de incidentes como um serviço.

Os dados de tráfego também estão integrados nos controlos interativos do mapa Azure Maps. Os mapas Azure também fornecem as seguintes APIs de serviços de tráfego;

-   [Segmentos de fluxo de tráfego](/rest/api/maps/traffic/gettrafficflowsegment): Fornece informações sobre as velocidades e os tempos de viagem do fragmento da estrada mais próximo das coordenadas dadas.
-   [Telhas de fluxo de tráfego](/rest/api/maps/traffic/gettrafficflowtile): Fornece azulejos raster e vetor que contêm dados de fluxo de tráfego. Estes podem ser utilizados com os controlos Azure Maps ou em controlos de mapas de terceiros, como o Folheto. Os azulejos vetoriais também podem ser usados para análise avançada de dados.
-   [Detalhes do incidente de](/rest/api/maps/traffic/gettrafficincidentdetail)trânsito : Fornece detalhes de incidentes de tráfego que estão dentro de uma caixa de limites, nível de zoom e modelo de tráfego.
-   [Telhas de incidentes de trânsito](/rest/api/maps/traffic/gettrafficincidenttile): Fornece azulejos raster e vetor contendo dados de incidentes de tráfego.
-   [Vista de incidentes de trânsito :](/rest/api/maps/traffic/gettrafficincidentviewport)Recupera as informações legais e técnicas para o viewport descrito no pedido, como o ID do modelo de tráfego.

O quadro seguinte cruza referências aos parâmetros de API de tráfego Bing Maps com os parâmetros de incidente de tráfego comparáveis detalha os parâmetros da API em Azure Maps.

| Bing Maps API parâmetro  | Parâmetro API de Mapas Azuis comparáveis   |
|--------------------------|---------------------------------------|
| `mapArea`                | `boundingBox` e `boundingZoom`      |
| `includeLocationCodes`   | N/D                                   |
| `severity` (`s`)         | N/A – todos os dados devolvidos               |
| `type` (`t`)             | N/A – todos os dados devolvidos               |
| `key`                    | `subscription-key`– Consulte também a [documentação autenticação com Azure Maps.](./azure-maps-authentication.md) |
| `culture` (`c`)          | `language`– Consulte a documentação [de línguas suportadas.](./supported-languages.md) |
| `userRegion` (`ur`)      | `view`– Consulte a documentação [de vistas suportadas.](./supported-languages.md#azure-maps-supported-views) |

## <a name="get-a-time-zone"></a>Obter um fuso horário

O Azure Maps fornece uma API para recuperar o fuso horário em que está uma coordenada. O fuso horário Azure Maps API é comparável ao fuso horário API em Bing Maps;

-   [Fuso horário por coordenada:](/rest/api/maps/timezone/gettimezonebycoordinates)Especifique uma coordenada e obtenha os detalhes do fuso horário em que cai.

A tabela seguinte cruza referências aos parâmetros API Bing Maps com os parâmetros API comparáveis em Azure Maps.

| Bing Maps API parâmetro | Parâmetro API de Mapas Azuis comparáveis          |
|-------------------------|----------------------------------------------|
| `point`                 | `query`                                      |
| `query`                 | N/A - as localizações devem ser geocodificadas primeiro.      |
| `dateTime`              | `timeStamp`                                  |
| `includeDstRules`       | N/A – Sempre incluído em resposta pelo Azure Maps. |
| `key`                   | `subscription-key`– Consulte também a [documentação autenticação com Azure Maps.](./azure-maps-authentication.md) |
| `culture` (`c`)         | `language`– Consulte a documentação [de línguas suportadas.](./supported-languages.md)  |
| `userRegion` (`ur`)     | `view`– Consulte a documentação [de vistas suportadas.](./supported-languages.md#azure-maps-supported-views)  |

Além disso, a plataforma Azure Maps também fornece uma série de APIs de fuso horário adicional para ajudar nas conversões com nomes de fusos horários e IDs;

-   [Fuso horário por ID](/rest/api/maps/timezone/gettimezonebyid): Devolve informações atuais, históricas e futuras do fuso horário para o ID do fuso horário IANA especificado.
-   [Fuso horário Enum IANA](/rest/api/maps/timezone/gettimezoneenumiana): Devolve uma lista completa de IDs do fuso horário IANA. As atualizações para o serviço IANA refletem-se no sistema dentro de um dia. 
-   [Time zone Enum Windows](/rest/api/maps/timezone/gettimezoneenumwindows): Devolve uma lista completa de IDs do Windows Time Zone.
-   [Versão IANA do fuso horário](/rest/api/maps/timezone/gettimezoneianaversion): Devolve o número atual da versão IANA utilizado pelo Azure Maps. 
-   [Fuso horário Windows to IANA](/rest/api/maps/timezone/gettimezonewindowstoiana): Devolve um ID IANA correspondente, dado um ID válido do Fuso Horário do Windows. Vários IDs IANA podem ser devolvidos para um único ID do Windows.

## <a name="spatial-data-services-sds"></a>Serviços de Dados Espaciais (SDS)

Os serviços de dados espaciais em Bing Maps fornecem três funcionalidades-chave:

* Geocoding de lote – Processar um grande lote de geocódigos de endereço com um único pedido.
* Recupere os dados de fronteira administrativa – Utilize uma coordenada e obtenha um limite de intersecção para um tipo de entidade especificado.
* Hospedar e consultar dados de negócios espaciais – Carrequim uma simples tabela de dados 2D e aceda-os utilizando algumas simples consultas espaciais.

### <a name="batch-geocode-data"></a>Dados de geocódigo de lote

A geocodagem do lote é o processo de tomada de um grande número de endereços ou lugares, passando-os todos num único pedido para um serviço, e tendo todos eles geocodificados em paralelo e os resultados devolvidos numa única resposta.

O Bing Maps permite que até 200.000 endereços sejam passados num único pedido de geocódigo de lote. Este pedido entra em fila e geralmente processa ao longo de um período de tempo, em qualquer lugar de alguns minutos a algumas horas, dependendo do tamanho do conjunto de dados e da carga no serviço. Cada endereço no pedido gerou uma transação.

O Azure Maps tem um serviço de geocoding de lote, no entanto permite que até 10.000 endereços sejam passados num único pedido e é processado em poucos minutos, dependendo do tamanho do conjunto de dados e da carga no serviço. Cada endereço no pedido gerou uma transação. No Azure Maps, o serviço de geocodagem de lote só está disponível no nível S1.

Outra opção para geocoding um grande número de endereços com Azure Maps é fazer pedidos paralelos às APIs de pesquisa padrão. Estes serviços apenas aceitam um único endereço por pedido, mas podem ser utilizados com o nível S0 que também fornece limites de utilização gratuitos. O nível S0 permite até 50 pedidos por segundo para a plataforma Azure Maps a partir de uma única conta. Portanto, se os limitarmos a permanecer dentro desse limite, é possível geocodificar mais de 180.000 endereços por hora. O nível S1 não tem um limite documentado no número de consultas por segundo que podem ser feitas a partir de uma conta, pelo que muito mais dados podem ser processados mais rapidamente ao utilizar esse nível de preços, no entanto a utilização do serviço de geocodagem de lote ajudará a reduzir a quantidade total de dados transferidos e reduzirá drasticamente o tráfego da rede.

-   [Geocoding de endereço de formulário gratuito](/rest/api/maps/search/getsearchaddress): Especifique uma única cadeia de endereços (como) e `"1 Microsoft way, Redmond, WA"` processe o pedido imediatamente. Este serviço é recomendado se precisar de geocodificar os endereços individuais rapidamente.
-   [Endereço estruturado geocoding](/rest/api/maps/search/getsearchaddressstructured): Especificar as partes de um único endereço, como o nome de rua, cidade, país e código postal e processar o pedido imediatamente. Este serviço é recomendado se necessitar de geocodificar rapidamente os endereços individuais e os dados já estiverem analisados nas suas partes de endereços individuais.
-   [Geocoding do endereço do lote:](/rest/api/maps/search/postsearchaddressbatchpreview)Crie um pedido que contenha até 10.000 endereços e processe-os durante um período de tempo. Todos os endereços serão geocodificados em paralelo no servidor e quando concluído o conjunto de resultados completo pode ser descarregado. Este serviço é recomendado para geocoding grandes conjuntos de dados.
-   [Pesquisa fuzzy](/rest/api/maps/search/getsearchfuzzy): Esta API combina geocoding de endereço com pesquisa de ponto de interesse. Esta API recebe uma cadeia de formulário livre que pode ser um endereço, lugar, marco, ponto de interesse, ou categoria de ponto de interesse e processar o pedido imediatamente. Esta API é recomendada para aplicações onde os utilizadores podem pesquisar endereços ou pontos de interesse a partir da mesma caixa de texto.
-   [Pesquisa de lote felpudo](/rest/api/maps/search/postsearchfuzzybatchpreview): Crie um pedido que contenha até 10.000 endereços, locais, marcos ou pontos de interesse e processe-os durante um período de tempo. Todos os dados serão processados em paralelo no servidor e quando concluídos o conjunto de resultados completos podem ser descarregados.

### <a name="get-administrative-boundary-data"></a>Obtenha dados de fronteira administrativa

Em Bing Maps, as fronteiras administrativas para países, estados, condados, cidades e códigos postais são disponibilizadas através da API geodata. Esta API leva uma coordenada ou consulta para geocódigo. Se uma consulta for transmitida, é geocodificada e as coordenadas do primeiro resultado são utilizadas. Esta API pega nas coordenadas e recupera o limite do tipo de entidade especificada que intersecta a coordenada. Note-se que esta API não devolveu necessariamente o limite para a consulta que foi passada. Se uma consulta `"Seattle, WA"` for aprovada, mas o valor do tipo de entidade for definido para a região do país, a fronteira para os EUA seria devolvida.

O Azure Maps também fornece acesso a fronteiras administrativas (países, estados, condados, cidades e códigos postais). Para recuperar um limite, deve consultar uma das APIs de pesquisa para o limite que pretende (ou `Seattle, WA` seja). Se o resultado da pesquisa tiver um limite associado, será fornecido um ID de geometria na resposta ao resultado. A API do polígono de pesquisa pode então ser usada para recuperar os limites exatos para um ou mais IDs de geometria. Isto é um pouco diferente do Bing Maps, uma vez que o Azure Maps devolve o limite para o que foi procurado, enquanto o Bing Maps devolve um limite para um tipo de entidade especificado numa coordenada especificada. Além disso, os dados de fronteira devolvidos pelo Azure Maps estão no formato GeoJSON.

Para recapitular:

1.  Passe uma consulta para o limite que pretende receber numa das seguintes APIs de pesquisa.
    -   [Geocoding de endereço de formulário gratuito](/rest/api/maps/search/getsearchaddress)
    -   [Geocoding de endereço estruturado](/rest/api/maps/search/getsearchaddressstructured)
    -   [Geocoding de endereço de lote](/rest/api/maps/search/postsearchaddressbatchpreview)
    -   [Pesquisa difusa](/rest/api/maps/search/getsearchfuzzy)
    -   [Pesquisa de lote felpudo](/rest/api/maps/search/postsearchfuzzybatchpreview)
2.  Se os resultados desejados menterem um ID de geometria, passe-o para a [API do Poligão de Busca](/rest/api/maps/search/getsearchpolygon).

### <a name="host-and-query-spatial-business-data"></a>Dados de negócios espaciais de hosped e consulta

Os serviços de dados espaciais em Bing Maps fornecem uma solução simples de armazenamento de dados espaciais para hospedar dados empresariais e expondo-os como um serviço espacial REST. Este serviço fornece quatro consultas principais; encontrar por propriedade, encontrar nas proximidades, encontrar na caixa de delimitação, e encontrar com 1,6 km de uma rota. Muitas empresas que utilizam este serviço, muitas vezes já têm os seus dados de negócio armazenados numa base de dados algures e têm vindo a enviar um pequeno subconjunto do mesmo para este serviço para aplicações de energia como localizadores de lojas. Uma vez que a autenticação baseada em chaves proporciona segurança básica, foi recomendado que este serviço só fosse utilizado com dados virados para o público.

A maioria dos dados de localização de negócios começa numa base de dados. Como tal, recomenda-se a utilização de soluções de armazenamento Azure existentes, tais como Azure SQL ou Azure PostgreSQL (com o plugin PostGIS). Ambas as soluções de armazenamento suportam dados espaciais e fornecem um rico conjunto de capacidades de consulta espacial. Uma vez que os seus dados estejam numa solução de armazenamento adequada, pode ser integrado na sua aplicação criando um serviço web personalizado, ou utilizando uma estrutura como ASP.NET ou Quadro de Entidades. A utilização desta abordagem proporciona mais capacidades de consulta e opções de segurança muito mais elevadas.

A Azure Cosmos DB também fornece um conjunto limitado de capacidades espaciais que, dependendo do seu cenário, podem ser suficientes.

Aqui estão alguns recursos úteis em torno do hospedagem e consulta de dados espaciais em Azure.

-   [Visão geral dos tipos de dados espaciais Azure SQL](/sql/relational-databases/spatial/spatial-data-types-overview)
-   [Azure SQL Spatial – Vizinho mais próximo da consulta](/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor)
-   [Visão geral das capacidades geoespaciais Azure Cosmos DB](../cosmos-db/sql-query-geospatial-intro.md)

## <a name="client-libraries"></a>Bibliotecas de cliente

O Azure Maps fornece bibliotecas de clientes para as seguintes linguagens de programação;

-   JavaScript, TypeScript, Node.js – [](./how-to-use-services-module.md) \| [pacote NPM](https://www.npmjs.com/package/azure-maps-rest) de documentação

Bibliotecas de clientes de código aberto para outras linguagens de programação;

* .NET Standard 2.0 – [](https://github.com/perfahlen/AzureMapsRestServices) \| [Pacote NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/) do projeto GitHub

## <a name="clean-up-resources"></a>Limpar os recursos

Não há recursos para ser limpo.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os serviços Azure Maps REST.

> [!div class="nextstepaction"]
> [Melhores práticas para a utilização do serviço de pesquisa](how-to-use-best-practices-for-search.md)
