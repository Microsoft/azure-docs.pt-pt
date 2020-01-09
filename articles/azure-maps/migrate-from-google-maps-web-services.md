---
title: Migrar serviços Web do Google Maps | Microsoft Docs
description: Um tutorial sobre como migrar serviços Web do Google Maps para mapas de Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 8fc3270d15f77576ff353a110d8a3a9a17c7144d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480024"
---
# <a name="migrate-web-service-from-google-maps"></a>Migrar serviço Web do Google Maps

O Azure e o Google Maps fornecem acesso a APIs espaciais por meio de serviços Web REST. As interfaces de API para essas plataformas executam funcionalidades semelhantes, mas usam diferentes convenções de nomenclatura e objetos de resposta.

A tabela a seguir fornece as APIs de serviço do Azure Maps que fornecem funcionalidade semelhante às APIs de serviço do Google Maps listadas.

| API de serviço do Google Maps | API de serviço do Azure Maps                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Direções              | [Rota](https://docs.microsoft.com/rest/api/maps/route)                               |
| Matriz de Distância         | [Matriz de rota](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| Programação geográfica               | [Pesquisa](https://docs.microsoft.com/rest/api/maps/search)                             |
| Pesquisa de locais           | [Pesquisa](https://docs.microsoft.com/rest/api/maps/search)                             |
| Preenchimento automático      | [Pesquisa](https://docs.microsoft.com/rest/api/maps/search)                             |
| Mapa estático              | [Composição](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| Fuso Horário               | [Fuso horário](https://docs.microsoft.com/rest/api/maps/timezone)                        |

As seguintes APIs de serviço não estão disponíveis atualmente no Azure Maps:

- Elevação
- Geolocalização
- Insere detalhes e fotos. Números de telefone e URL do site disponíveis na API de pesquisa do Azure Maps.
- Mapear URLs
- Estradas – os dados de limite de velocidade estão disponíveis por meio das APIs de rota e de geocodificação inversa no Azure Maps.
- Exibição de rua estática

O mapas do Azure tem vários serviços Web REST adicionais que podem ser interessantes:

- [Operações espaciais](https://docs.microsoft.com/rest/api/maps/spatial): Descarregue operações e cálculos espaciais complexos, como a cerca geográfica, para um serviço.
- [Tráfego](https://docs.microsoft.com/rest/api/maps/traffic): acesse o fluxo de tráfego em tempo real e os dados de incidentes.

## <a name="geocoding-addresses"></a>Endereços geocodificados

A geocodificação é o processo de converter um endereço (como "1 Microsoft Way, Redmond, WA") em uma coordenada (como Longitude:-122,1298, Latitude: 47,64005). As coordenadas são geralmente usadas para posicionar um marcador em um mapa ou centralizar um mapa.

O Azure Maps fornece vários métodos para endereços geocodificados:

- [**Geocodificação de endereço de forma livre**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): especifique uma cadeia de caracteres de endereço único (como "1 Microsoft Way, Redmond, WA") e processe a solicitação imediatamente. Isso é recomendado se você precisar geocodificar endereços individuais rapidamente.
- [**Geocodificação de endereço estruturado**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): especifique as partes de um único endereço, como o nome da rua, a cidade, o país e o CEP e processe a solicitação imediatamente. Isso é recomendado se você precisar geocodificar endereços individuais rapidamente e os dados já estiverem analisados em suas partes de endereço individuais.
- [**Geocodificação de endereço em lotes**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): Crie uma solicitação contendo até 10.000 endereços e faça com que elas sejam processadas durante um período de tempo. Todos os endereços serão geocodificados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado. Isso é recomendado para geocodificação de grandes conjuntos de dados.
- [**Pesquisa difusa**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): essa API combina a geocodificação de endereço com a pesquisa de ponto de interesse. Essa API usa uma cadeia de caracteres de forma livre que pode ser um endereço, local, ponto de referência, pontos de interesse ou categoria de ponto de interesse e processar a solicitação imediatamente. Essa API é recomendada para aplicativos em que os usuários podem procurar endereços ou pontos de interesse da mesma caixa de texto.
- [**Pesquisa de lote difusa**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Crie uma solicitação contendo até 10.000 endereços, locais, pontos de referência ou pontos de interesse e faça com que eles sejam processados durante um período de tempo. Todos os dados serão processados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado.

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros de API comparáveis no Azure Maps.

| Parâmetro da API do Google Maps | Parâmetro de API comparável do Azure Maps  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` e `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality`-cidade/município<br/>`municipalitySubdivision` – vizinhança, sub/super City<br/>`countrySubdivision`-estado ou província<br/>`countrySecondarySubdivision`-município<br/>`countryTertiarySubdivision`-distrito<br/>código do país com `countryCode` duas letras |
| `key`                       | `subscription-key` – consulte também a documentação de [autenticação com o Azure Maps](azure-maps-authentication.md) . |
| `language`                  | `language` – consulte a documentação de [idiomas com suporte](supported-languages.md) .  |
| `region`                    | `countrySet`                       |

Um exemplo de como usar o serviço de pesquisa está documentado [aqui](how-to-search-for-address.md). Certifique-se de examinar as [práticas recomendadas para a documentação de pesquisa](how-to-use-best-practices-for-search.md) .

> [!TIP]
> As APIs de pesquisa difusa e geocodificação de endereço de forma livre podem ser usadas no modo de preenchimento automático adicionando `&amp;typeahead=true` à URL de solicitação. Isso informará ao servidor que o texto de entrada é provavelmente parcial e entrará em modo de previsão.

## <a name="reverse-geocode-a-coordinate"></a>Reverter código a uma coordenada

A geocodificação inversa é o processo de conversão de coordenadas geográficas (como Longitude:-122,1298, Latitude: 47,64005) em seu endereço aproximado (como "1 Microsoft Way, Redmond, WA").

O mapas do Azure fornece vários métodos de geocodificação inversa:

- [**Endereço geograficamente inverso**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): especifique uma coordenada geográfica única para obter seu endereço aproximado e processar a solicitação imediatamente.
- Geoprocessador [**cruzado invertido**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): especifique uma coordenada geográfica única para obter informações de rua cruzada próximas (por exemplo, 1º & principal) e processar a solicitação imediatamente.
- [**Geocodificador inverso de endereço de lote**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): Crie uma solicitação contendo até 10.000 coordenadas e faça com que elas sejam processadas durante um período de tempo. Todos os dados serão processados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado.

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros de API comparáveis no Azure Maps.

| Parâmetro da API do Google Maps   | Parâmetro de API comparável do Azure Maps   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – consulte também a documentação de [autenticação com o Azure Maps](azure-maps-authentication.md) . |
| `language`                  | `language` – consulte a documentação de [idiomas com suporte](supported-languages.md) .  |
| `latlng`                    | `query`  |
| `location_type`             | *N/D*     |
| `result_type`               | `entityType`    |

Certifique-se de examinar as [práticas recomendadas para a documentação de pesquisa](how-to-use-best-practices-for-search.md) .

A API de geocodificação inversa do Azure Maps tem alguns recursos adicionais não disponíveis no Google Maps que podem ser úteis para integrar ao migrar seu aplicativo:

- Recuperar dados de limite de velocidade.
- Recuperar informações de uso de estrada: estrada local, arterial, acesso limitado, rampa etc.
- O lado da rua em que a coordenada cai.

## <a name="search-for-points-of-interest"></a>Procurar pontos de interesse

Dados de ponto de interesse podem ser pesquisados no Google Maps usando sua API de pesquisa de locais. Essa API fornece três maneiras diferentes de procurar pontos de interesse:

- **Localizar local do texto:** Procura um ponto de interesse com base em seu nome, endereço ou número de telefone.
- **Pesquisa próxima**: procura pontos de interesse que estejam dentro de uma determinada distância de um local.
- **Pesquisa de texto:** Pesquisa locais usando um texto de forma livre que inclui informações de ponto de interesse e local. Por exemplo, "pizza em Nova York" ou "restaurantes perto do Main St".

O mapas do Azure fornece várias APIs de pesquisa para pontos de interesse:

- [**Pesquisa do POI**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): procurar pontos de interesse por nome. Por exemplo, "Starbucks".
- [**Pesquisa de categoria de POI**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): Pesquise pontos de interesse por categoria. Por exemplo, "restaurante".
- [**Pesquisa próxima**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): procura pontos de interesse que estejam dentro de uma determinada distância de um local.
- [**Pesquisa difusa**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): essa API combina a geocodificação de endereço com a pesquisa de ponto de interesse. Essa API usa uma cadeia de caracteres de forma livre que pode ser um endereço, local, ponto de referência, pontos de interesse ou categoria de ponto de interesse e processar a solicitação imediatamente. Essa API é recomendada para aplicativos em que os usuários podem procurar endereços ou pontos de interesse da mesma caixa de texto.
- [**Pesquisar em geometria**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): Procure pontos de interesse que estejam dentro de uma geometria especificada (polígono).
- [**Pesquisar ao longo do roteiro**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): Procure pontos de interesse que estejam ao longo de um caminho de rota especificado.
- [**Pesquisa de lote difusa**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Crie uma solicitação contendo até 10.000 endereços, locais, pontos de referência ou pontos de interesse e faça com que eles sejam processados durante um período de tempo. Todos os dados serão processados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado.

Atualmente, o Azure Maps não tem uma API comparável para a API de pesquisa de texto do Google Maps.

> [!TIP]
> As APIs de pesquisa de POI, pesquisa de categoria POI e pesquisa difusa podem ser usadas no modo de preenchimento automático adicionando `&amp;typeahead=true` à URL de solicitação. Isso informará ao servidor que o texto de entrada é provavelmente parcial e entrará em modo de previsão.

Certifique-se de examinar as [práticas recomendadas para a documentação de pesquisa](how-to-use-best-practices-for-search.md) .

### <a name="find-place-from-text"></a>Localizar local do texto

Para pesquisar pontos de interesse por nome ou endereço, a [pesquisa POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) do Azure Maps e as APIs de [pesquisa difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) podem ser usadas.

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros de API comparáveis no Azure Maps.

| Parâmetro da API do Google Maps | Parâmetro de API comparável do Azure Maps |
|---------------------------|-------------------------------------|
| `fields`                  | *N/D*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/D*                               |
| `key`                     | `subscription-key` – consulte também a documentação de [autenticação com o Azure Maps](azure-maps-authentication.md) . |
| `language`                | `language` – consulte a documentação de [idiomas com suporte](supported-languages.md) .  |
| `locationbias`            | `lat`, `lon` e `radius`<br/>`topLeft` e `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Pesquisa próxima

Pontos de interesse próximos podem ser recuperados no Azure Maps usando a API de [pesquisa próxima](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) .

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros de API comparáveis no Azure Maps.

| Parâmetro da API do Google Maps | Parâmetro de API comparável do Azure Maps  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` – consulte também a documentação de [autenticação com o Azure Maps](azure-maps-authentication.md) . |
| `keyword`                   | `categorySet` e `brandSet`        |
| `language`                  | `language` – consulte a documentação de [idiomas com suporte](supported-languages.md) .  |
| `location`                  | `lat` e `lon`                     |
| `maxprice`                  | *N/D*                               |
| `minprice`                  | *N/D*                               |
| `name`                      | `categorySet` e `brandSet`        |
| `opennow`                   | *N/D*                               |
| `pagetoken`                 | `ofs` e `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/D*                               |
| `type`                      | `categorySet –` consulte a documentação [categorias de pesquisa com suporte](supported-search-categories.md) .   |

## <a name="calculate-routes-and-directions"></a>Calcular rotas e direções

Os mapas do Azure podem ser usados para calcular rotas e direções. O mapas do Azure tem muitas das mesmas funcionalidades que o serviço de roteamento do Google Maps, como:

- horas de chegada e saída.
- rotas de tráfego com base em tempo real e em previsão.
- modos diferentes de transporte; dirigir, andar, encaminhar.

> [!NOTE]
> O mapas do Azure exige que todas as Marcos sejam coordenadas. Os endereços precisarão ser geocodificados primeiro.

O serviço de roteamento do Azure Maps fornece as seguintes APIs para calcular as rotas:

- [**Calcular rota**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): calcular uma rota e fazer com que a solicitação seja processada imediatamente. Essa API dá suporte a solicitações GET e POST. Solicitações POST são recomendadas ao especificar um grande número de Marcos ou ao usar muitas das opções de rota para garantir que a solicitação de URL não se torne muito longa e cause problemas.
- [**Rota do lote**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): Crie uma solicitação contendo até 1.000 solicitação de rota e faça com que elas sejam processadas durante um período de tempo. Todos os dados serão processados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado.
- [**Serviços de mobilidade**](https://docs.microsoft.com/rest/api/maps/mobility): Calcule rotas e direções usando o trânsito público.

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros de API comparáveis no Azure Maps.

| Parâmetro da API do Google Maps    | Parâmetro de API comparável do Azure Maps  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` – coordenadas no formato `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key` – consulte também a documentação de [autenticação com o Azure Maps](azure-maps-authentication.md) . |
| `language`                     | `language` – consulte a documentação de [idiomas com suporte](supported-languages.md) .   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N/A* – este é um recurso relacionado geocodificação. Use o parâmetro *countryset* ao usar a API de geocodificação do Azure Maps.  |
| `traffic_model`               | *N/A* – só é possível especificar se os dados de tráfego devem ser usados com o parâmetro de *tráfego* . |
| `transit_mode`                | Consulte a [documentação dos serviços de mobilidade](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Consulte a [documentação dos serviços de mobilidade](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *N/A* – o Azure Maps usa apenas o sistema de métrica.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Por padrão, a API de rotas do Azure Maps retorna apenas um resumo (distância e vezes) e as coordenadas para o caminho da rota. Use o parâmetro `instructionsType` para recuperar as instruções Ativar/desativar. O parâmetro `routeRepresentation` pode ser usado para filtrar o resumo e o caminho da rota.

A API de roteamento do Azure Maps tem muitos recursos adicionais não disponíveis no Google Maps que podem ser úteis para integrar ao migrar seu aplicativo:

- Suporte para tipo de rota: mais curto, mais rápido, Trilling e mais eficiente de combustível.
- Suporte para modos de viagem adicionais: barramento, Motorcycle, táxi, caminhão e Van.
- Suporte para 150 marcos.
- Calcular vários tempos de viagem em uma única solicitação; tráfego histórico, tráfego ao vivo, sem tráfego.
- Evite tipos de estrada adicionais: estradas andas, estradas unpaveds e estradas já usadas.
- Especifique áreas personalizadas a serem evitadas.
- Limite a elevação em que a rota pode ser ascendente.
- Roteamento baseado na especificação do mecanismo. Calcule as rotas para a comdisponibilidade ou veículos elétricos com base nas especificações restantes de combustível/encargo e mecanismo.
- Suporte ao parâmetro de roteiro de veículo comercial; dimensões do veículo, peso, número de Axels e tipo de carga.
- Especifique a velocidade máxima do veículo.

Além disso, o serviço de rota no Azure Maps também dá suporte ao [cálculo de intervalos roteáveis](https://docs.microsoft.com/rest/api/maps/route/getrouterange), também conhecidos como isócronas que geram um polígono que abrange uma área que pode ser percorrida em qualquer direção de um ponto de origem dentro de um período especificado de tempo ou quantidade de combustível/encargo.

## <a name="retrieve-a-map-image"></a>Recuperar uma imagem do mapa

O mapas do Azure fornece uma API para renderizar as imagens de mapa estático com dados sobrepostos. A API de [renderização de imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) do Azure Maps é comparável à API do mapa estático no Google Maps.

> [!NOTE]
> O mapas do Azure exige que o centro, todos os locais de marcador e caminho sejam coordenados no formato de "longitude, Latitude", enquanto o Google Maps usa o formato "latitude, longitude". Os endereços precisarão ser geocodificados primeiro.

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros de API comparáveis no Azure Maps.

| Parâmetro da API do Google Maps | Parâmetro de API comparável do Azure Maps  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` – especificado como parte do caminho da URL. Atualmente, apenas o PNG tem suporte. |
| `key`                       | `subscription-key` – consulte também a documentação de [autenticação com o Azure Maps](azure-maps-authentication.md) . |
| `language`                  | `language` – consulte a documentação de [idiomas com suporte](supported-languages.md) .  |
| `maptype`                   | `layer` e `style` – consulte a documentação de [estilos de mapa com suporte](supported-map-styles.md) . |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N/A* – este é um recurso relacionado geocodificação. Use o parâmetro `countrySet` ao usar a API de geocodificação do Azure Maps.  |
| `scale`                     | *N/D*                              |
| `size`                      | `width` e `height` – pode ter até 8192x8192 de tamanho. |
| `style`                     | *N/D*                              |
| `visible`                   | *N/D*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> O mapas do Azure usa um sistema de peças com blocos que são duas vezes o tamanho dos blocos de mapa usados no Google Maps. Assim, o valor do nível de zoom no mapas do Azure aparecerá um nível de zoom mais próximo do mapas do Azure em comparação com o Google Maps. Reduza o nível de zoom nas solicitações que você está migrando por um para compensar isso.

Para obter mais informações, consulte o [Guia de instruções na API de renderização de imagem do mapa](how-to-render-custom-data.md).

Além de poder gerar uma imagem de mapa estático, o serviço de renderização do Azure Maps também fornece a capacidade de acessar diretamente os blocos de mapa em raster (PNG) e no formato de vetor:

- [**Bloco de mapa**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): recuperar blocos de rasterização (png) e de vetor para os mapas base (estradas, limites, plano de fundo).
- [**Bloco de imagens do mapa**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): recuperar blocos de imagens aéreas e satélite.

> [!TIP]
> Muitos aplicativos do Google Maps, onde mudou de experiências de mapa interativas para imagens de mapa estáticos há alguns anos, como um método de economia de custo. No Azure Maps, geralmente é muito mais econômico usar o controle de mapa interativo no SDK da Web, pois ele cobra com base em cargas de bloco do mapa. Os blocos de mapa nos mapas do Azure são grandes e, muitas vezes, só demora alguns para recriar a mesma exibição de mapa como um mapa estático e os blocos de mapa são armazenados em cache automaticamente pelo navegador. Assim, o controle de mapa interativo geralmente gera apenas uma fração de uma transação ao reproduzir uma exibição de mapa estático. O movimento panorâmico e o zoom carregarão mais blocos, no entanto, há opções no controle de mapa para desabilitar esse comportamento, se desejado. O controle de mapa interativo também fornece muito mais opções de visualização do que os serviços de mapa estáticos.

### <a name="marker-url-parameter-format-comparison"></a>Comparação de formato de parâmetro de URL de marcador

**Antes: Google Maps**

Nos marcadores do Google Maps podem ser adicionados a uma imagem de mapa estático usando o parâmetro `markers` na URL. O parâmetro `markers` usa um estilo e uma lista de locais a serem renderizados no mapa com esse estilo, conforme mostrado abaixo:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Estilos adicionais podem ser usados pela adição de parâmetros `markers` adicionais à URL com um estilo diferente e um conjunto de locais.

Os locais de marcador são especificados com o formato "latitude, longitude".

Os estilos de marcador no Google Maps são adicionados com o formato `optionName:value`, com vários estilos separados por caracteres de pipe (\|) como este "optionName1: value1\|optionName2: value2". Observe que os nomes e valores de opção são separados por dois-pontos (:). Os seguintes nomes de opção de estilo podem ser usados para marcadores de estilo no Google Maps:

- `color` – a cor do ícone de marcador padrão. Pode ser uma cor hex de 24 bits (`0xrrggbb`) ou um dos valores a seguir; `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label` – um único caractere alfanumérico maiúsculo para exibir na parte superior do ícone.
- `size`-o tamanho do marcador. Pode ser `tiny`, `mid`ou `small`.

Os ícones personalizados também podem ser usados no Google Maps usando os seguintes nomes de opção de estilo:

- `anchor` – especifica como alinhar a imagem do ícone à coordenada. Pode ser um valor de pixel (x, y) ou um dos valores a seguir; `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft`ou `bottomright`.
- `icon` – uma URL apontando para a imagem do ícone.

Por exemplo, no Google Maps, um marcador vermelho de médio porte pode ser adicionado ao mapa em coordenadas (Longitude:-110, Latitude: 45) com o seguinte parâmetro de URL:

```
&markers=color:red|size:mid|45,-110
```

<center>

![](media/migrate-google-maps-web-services/google-maps-marker.png)</center> de marcador do Google Maps

**Após: mapas do Azure**

Nos marcadores do Azure Maps também podem ser adicionados a uma imagem de mapa estático especificando o parâmetro `pins` na URL. Como o Google Maps, um estilo e uma lista de locais podem ser especificados nesse parâmetro, e o parâmetro `pins` pode ser especificado várias vezes para dar suporte a marcadores com diferentes estilos.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Estilos adicionais podem ser usados pela adição de parâmetros `pins` adicionais à URL com um estilo diferente e um conjunto de locais.

Quando se trata de fixar locais, o mapas do Azure exige que as coordenadas estejam no formato de "latitude de longitude", enquanto o Google Maps usa o formato "latitude, longitude". Observe também que há um espaço, não uma vírgula separando a longitude e a latitude no Azure Maps.

O valor de `iconType` especifica o tipo de PIN a ser criado e pode ter os seguintes valores:

- `default` – o ícone de PIN padrão.
- `none` – nenhum ícone é exibido, somente os rótulos serão renderizados.
- `custom` – especifica que um ícone personalizado deve ser usado. Uma URL que aponta para a imagem do ícone pode ser adicionada ao final do parâmetro `pins` após as informações de localização do PIN.
- `{udid}` – uma UDID (ID de dados exclusiva) para um ícone armazenado na plataforma de armazenamento de dados do Azure Maps.

Os estilos de PIN nos mapas do Azure são adicionados com o formato `optionNameValue`, com vários estilos separados por caracteres de pipe (\|) como esse `iconType|optionName1Value1|optionName2Value2`. Observe que os nomes e valores de opção não são separados. Os seguintes nomes de opção de estilo podem ser usados para marcadores de estilo no Azure Maps:

- `al` – especifica a opacidade (alfa) do marcador. Pode ser um número entre 0 e 1.
- `an` – especifica a âncora do PIN. Valores de pixel X e y especificados no formato "x y".
- `co` – a cor do PIN. Deve ser uma cor hexadecimal de 24 bits: `000000` para `FFFFFF`.
- `la` – especifica a âncora do rótulo. Valores de pixel X e y especificados no formato "x y".
- `lc` – a cor do rótulo. Deve ser uma cor de 24, mas hex: `000000` para `FFFFFF`.
- `ls` – o tamanho do rótulo em pixels. Pode ser um número maior que 0.
- `ro` – um valor em graus para girar o ícone. Pode ser um número entre-360 e 360.
- `sc` – um valor de escala para o ícone de pino. Pode ser um número maior que 0.

Os valores de rótulo são especificados para cada local de fixação em vez de ter um único valor de rótulo que se aplica a todos os marcadores na lista de locais. O valor do rótulo pode ser uma cadeia de vários caracteres e ser encapsulado com aspas simples para garantir que não seja confundido como um valor de estilo ou local.

Por exemplo, no Azure Maps, a adição de um ícone padrão vermelho (`FF0000`) com o rótulo "agulha de espaço" posicionado abaixo (15 50) o ícone em coordenadas (Longitude:-122,349300, Latitude: 47,620180) pode ser feito com o seguinte parâmetro de URL:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![](media/migrate-google-maps-web-services/azure-maps-marker.png)</center> de marcador do Azure Maps

O exemplo a seguir adiciona três pinos com os valores de rótulo ' 1 ', ' 2 ' e ' 3 ':

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![vários marcadores do Azure Maps](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Comparação de formato de parâmetro de URL de caminho

**Antes: Google Maps**

Em linhas e polígonos do Google Maps podem ser adicionados a uma imagem de mapa estático usando o parâmetro `path` na URL. O parâmetro `path` usa um estilo e uma lista de locais a serem renderizados no mapa com esse estilo, conforme mostrado abaixo:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Estilos adicionais podem ser usados pela adição de parâmetros `path` adicionais à URL com um estilo diferente e um conjunto de locais.

Os locais de caminho no Google Maps são especificados com o formato `latitude1,longitude1|latitude2,longitude2|…`. Os caminhos podem ser codificados ou conter endereços para pontos.

Os estilos de caminho no Google Maps são adicionados com o formato `optionName:value`, com vários estilos separados por caracteres de pipe (\|) como esse `optionName1:value1|optionName2:value2`. Observe que os nomes e valores de opção são separados por dois-pontos (:). Os seguintes nomes de opção de estilo podem ser usados para caminhos de estilo no Google Maps:

- `color` – a cor do caminho ou da estrutura de tópicos do polígono. Pode ser uma cor hexadecimal de 24 bits (`0xrrggbb`), uma cor hexadecimal de 32 bits (`0xrrggbbbaa`) ou um dos valores a seguir; preto, marrom, verde, roxo, amarelo, azul, cinza, laranja, vermelho, branco.
- `fillColor` – a cor para preencher a área de caminho com (polígono). Pode ser uma cor hexadecimal de 24 bits (`0xrrggbb`), uma cor hexadecimal de 32 bits (`0xrrggbbbaa`) ou um dos valores a seguir; preto, marrom, verde, roxo, amarelo, azul, cinza, laranja, vermelho, branco.
- `geodesic` – indica se o caminho deve ser uma linha que segue a curvatura da terra.
- `weight` – a espessura da linha do caminho em pixels.

Por exemplo, no Google Maps, uma linha vermelha com opacidade de 50% e uma espessura de 4 pixels podem ser adicionadas ao mapa entre as coordenadas (Longitude:-110, Latitude: 45 e Longitude:-100, Latitude: 50) com o seguinte parâmetro de URL:

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![](media/migrate-google-maps-web-services/google-maps-polyline.png)</center> de polilinha do Google Maps

**Após: mapas do Azure**

Nas linhas e polígonos do Azure Maps também podem ser adicionados a uma imagem de mapa estático especificando o parâmetro `path` na URL. Como o Google Maps, um estilo e uma lista de locais podem ser especificados nesse parâmetro, e o parâmetro `path` pode ser especificado várias vezes para renderizar vários círculos, linhas e polígonos com estilos diferentes.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Quando se trata de locais de caminho, o mapas do Azure exige que as coordenadas estejam no formato de "latitude de longitude", enquanto o Google Maps usa o formato "latitude, longitude". Observe também que há um espaço, não uma vírgula separando a longitude e a latitude no Azure Maps. O mapas do Azure não oferece suporte a caminhos ou endereços codificados para pontos. Conjuntos de dados maiores podem ser carregados como um geojson que preenche a API de armazenamento de dados do Azure Maps, conforme documentado [aqui](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

Os estilos de caminho nos mapas do Azure são adicionados com o formato `optionNameValue`, com vários estilos separados por caracteres de pipe (\|) como esse `optionName1Value1|optionName2Value2`. Observe que os nomes e valores de opção não são separados. Os seguintes nomes de opção de estilo podem ser usados para caminhos de estilo no Azure Maps:

- `fa`-a opacidade de cor de preenchimento (alfa) usada ao renderizar polígonos. Pode ser um número entre 0 e 1.
- `fc`-a cor de preenchimento usada para renderizar a área de um polígono.
- `la` – a opacidade de cor de linha (alfa) usada ao renderizar linhas e a estrutura de polígonos. Pode ser um número entre 0 e 1.
- `lc` – a cor da linha usada para renderizar linhas e a estrutura de polígonos.
- `lw` – a largura da linha em pixels.
- `ra` – especifica um raio de círculos em metros.

Por exemplo, no Azure Maps, uma linha vermelha com opacidade de 50% e uma espessura de 4 pixels podem ser adicionadas ao mapa entre coordenadas (Longitude:-110, Latitude: 45 e Longitude:-100, Latitude: 50) com o seguinte parâmetro de URL:

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center> de polilinha do Azure Maps

## <a name="calculate-a-distance-matrix"></a>Calcular uma matriz de distância

O mapas do Azure fornece uma API para calcular os tempos de viagem e as distâncias entre um conjunto de locais como uma matriz de distância. A API de matriz de distância do Azure Maps é comparável à API de matriz de distância no Google Maps;

- [**Matriz de rota**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): calcula de forma assíncrona os tempos de viagem e distâncias para um conjunto de origens e destinos. Há suporte para até 700 células por solicitação (o número de origens multiplicado pelo número de destinos). Com essa restrição em mente, exemplos de dimensões de matriz possíveis são: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Uma solicitação para a API de matriz de distância só pode ser feita usando uma solicitação POST com as informações de origem e destino no corpo da solicitação. Além disso, o Azure Maps requer que todas as origens e destinos sejam coordenadas. Os endereços precisarão ser geocodificados primeiro.

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros de API comparáveis no Azure Maps.

| Parâmetro da API do Google Maps      | Parâmetro de API comparável do Azure Maps  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` – especifique no corpo da solicitação POST como geojson. |
| `key`                          | `subscription-key` – consulte também a documentação de [autenticação com o Azure Maps](azure-maps-authentication.md) . |
| `language`                     | `language` – consulte a documentação de [idiomas com suporte](supported-languages.md) .  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` – especifique no corpo da solicitação POST como geojson.  |
| `region`                       | *N/A* – este é um recurso relacionado geocodificação. Use o parâmetro `countrySet` ao usar a API de geocodificação do Azure Maps. |
| `traffic_model`                | *N/A* – só é possível especificar se os dados de tráfego devem ser usados com o parâmetro `traffic`. |
| `transit_mode`                 | Atualmente não há suporte para matrizes de distância com base em trânsito *N/A* .  |
| `transit_routing_preference`   | Atualmente não há suporte para matrizes de distância com base em trânsito *N/A* .  |
| `units`                        | *N/A* – o Azure Maps usa apenas o sistema de métrica. |

> [!TIP]
> Todas as opções de roteamento avançadas disponíveis na API de roteamento do Azure Maps (roteamento de caminhão, especificações do mecanismo, evitar...) são suportadas na API da matriz de distância do Azure Maps.

## <a name="get-a-time-zone"></a>Obter um fuso horário

O mapas do Azure fornece uma API para recuperar o fuso horário em que se encontra uma coordenada. A API de fuso horário do Azure Maps é comparável à API de fuso horário no Google Maps:

- [**Fuso horário por coordenada**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): especifique uma coordenada e obtenha os detalhes do fuso horário em que se encontra.

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros de API comparáveis no Azure Maps.

| Parâmetro da API do Google Maps | Parâmetro de API comparável do Azure Maps   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – consulte também a documentação de [autenticação com o Azure Maps](azure-maps-authentication.md) .       |
| `language`                  | `language` – consulte a documentação de [idiomas com suporte](supported-languages.md) .    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Além disso, a plataforma Azure Maps também fornece várias APIs de fuso horário adicionais para ajudar com conversões com nomes e IDs de fuso horário:

- [**Fuso horário por ID**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): retorna informações de fuso horário atual, histórica e futuras para a ID de fuso horário da IANA especificada.
- [**Enumeração de fuso horário do IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): retorna uma lista completa de IDs de fuso horário da IANA. As atualizações para o serviço IANA são refletidas no sistema dentro de um dia.
- [**Janelas de enumeração de fuso horário**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): retorna uma lista completa de IDs de fuso horário do Windows.
- [**Versão do IANA do fuso horário**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): retorna o número de versão atual da IANA usado pelo Azure Maps.
- [**Janelas de fuso horário para IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): retorna uma ID da IANA correspondente, dada uma ID de fuso horário do Windows válida. Várias IDs do IANA podem ser retornadas para uma única ID do Windows.

## <a name="client-libraries"></a>Bibliotecas de cliente

O mapas do Azure fornece bibliotecas de cliente para as seguintes linguagens de programação:

- JavaScript, TypeScript, Node. js – [documentação](how-to-use-services-module.md) \| [pacote NPM](https://www.npmjs.com/package/azure-maps-rest)

Bibliotecas de cliente de código aberto para outras linguagens de programação:

- .NET Standard 2,0 – [projeto do GitHub](https://github.com/perfahlen/AzureMapsRestServices) \| [pacote NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>Recursos adicionais

Veja a seguir algumas documentações e recursos adicionais sobre os serviços REST do Azure Maps.

- [Práticas recomendadas para pesquisa](how-to-use-best-practices-for-search.md)
- [Procurar um endereço](how-to-search-for-address.md)
- [Documentação de referência da API do serviço REST do Azure Maps](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os serviços REST do Azure Maps.

> [!div class="nextstepaction"]
> [Práticas recomendadas para usar o serviço de pesquisa](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Como usar o módulo de serviços (SDK Web)](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)