---
title: Tutorial - Migrar serviços web do Google Maps Microsoft Azure Maps
description: Tutorial sobre como migrar serviços web do Google Maps para o Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 08/19/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: f97d04ca40e69ba2516744adfc9f1f455cba97c0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896349"
---
# <a name="tutorial---migrate-web-service-from-google-maps"></a>Tutorial - Migrar o serviço web do Google Maps

Tanto o Azure como o Google Maps fornecem acesso a APIs espaciais através de serviços web REST. As interfaces API destas plataformas executam funcionalidades similares. Mas cada um usa diferentes convenções de nomeação e objetos de resposta.

Neste tutorial, vai aprender a:

> * Geocoding para a frente e reverso
> * Procurar pontos de interesse
> * Calcular rotas e direções
> * Recupere uma imagem de mapa
> * Calcular uma matriz de distância
> * Obtenha detalhes do fuso horário

Também vai aprender: 

> [!div class="checklist"]
> * Que serviço Azure Maps REST ao migrar de um Serviço Web do Google Maps
> * Dicas sobre como tirar o máximo partido dos serviços Azure Maps
> * Insights sobre outros serviços relacionados Azure Maps

A tabela mostra as APIs do serviço Azure Maps, que têm uma funcionalidade semelhante às APIs do serviço do Google Maps listados.

| Serviço Google Maps API | A azure Maps serviço API                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Instruções              | [Rota](/rest/api/maps/route)                                     |
| Matriz de distância         | [Matriz de Rota](/rest/api/maps/route/postroutematrixpreview)       |
| Geocodificação               | [Pesquisa](/rest/api/maps/search)                                   |
| Pesquisa de lugares           | [Pesquisa](/rest/api/maps/search)                                   |
| Colocar Autocompleto      | [Pesquisa](/rest/api/maps/search)                                   |
| Estalar para a estrada            | Consulte a secção [rotas e direções.](#calculate-routes-and-directions)            |
| Limites de velocidade            | Consulte [o geocódigo inverso de uma secção de coordenadas.](#reverse-geocode-a-coordinate)                  |
| Mapa estático              | [Composição](/rest/api/maps/render/getmapimage)                       |
| Fuso Horário               | [Fuso Horário](/rest/api/maps/timezone)                              |

As apis de serviço a seguir não estão atualmente disponíveis no Azure Maps:

- Elevação
- Geolocalização
- Locais detalhes e fotos - Os números de telefone e URL do site estão disponíveis na API de pesquisa de Mapas Azure.
- URLs de mapa
- Estradas mais próximas - Isto é possível usando o Web SDK como mostrado [aqui](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic
), mas não disponível como um serviço atualmente.
- Vista de rua estática

A Azure Maps dispõe de vários serviços web REST adicionais que podem ser de interesse:

- [Operações espaciais](/rest/api/maps/spatial): Descarrega cálculos e operações espaciais complexos, como geofencing, para um serviço.
- [Tráfego](/rest/api/maps/traffic): Aceda ao fluxo de tráfego em tempo real e aos dados dos incidentes.

## <a name="prerequisites"></a>Pré-requisitos 

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
2. [Faça uma conta Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Obtenha uma chave de subscrição primária,](quick-demo-map-app.md#get-the-primary-key-for-your-account)também conhecida como a chave primária ou a chave de subscrição. Para obter mais informações sobre a autenticação no Azure Maps, consulte [a autenticação de gestão no Azure Maps.](how-to-manage-authentication.md)

## <a name="geocoding-addresses"></a>Endereços de geocoding

Geocoding é o processo de conversão de um endereço numa coordenada. Por exemplo, "1 Microsoft way, Redmond, WA" converte-se em longitude: -122.1298, latitude: 47.64005. Em seguida, as coordenadas podem ser usadas para diferentes tipos de propósitos, tais como, posicionando um marcador centrado um marcador em um mapa.

O Azure Maps fornece vários métodos para endereços de geocoding:

- [**Geocoding de endereço de formulário gratuito**](/rest/api/maps/search/getsearchaddress): Especifique uma única cadeia de endereços e processe o pedido imediatamente. "1 Microsoft way, Redmond, WA" é um exemplo de uma única cadeia de endereços. Esta API é recomendada se precisar de geocodificar os endereços individuais rapidamente.
- [**Endereço estruturado geocoding**](/rest/api/maps/search/getsearchaddressstructured): Especificar as partes de um único endereço, tais como o nome de rua, cidade, país/região, e código postal e processar imediatamente o pedido. Esta API é recomendada se necessitar de geocodificar rapidamente os endereços individuais e os dados já estiverem analisados nas suas partes de endereços individuais.
- [**Geocoding do endereço do lote:**](/rest/api/maps/search/postsearchaddressbatchpreview)Crie um pedido que contenha até 10.000 endereços e processe-os durante um período de tempo. Todos os endereços serão geocodificados em paralelo no servidor e quando concluído o conjunto de resultados completo pode ser descarregado. Isto é recomendado para geocoding grandes conjuntos de dados.
- [**Pesquisa fuzzy**](/rest/api/maps/search/getsearchfuzzy): Esta API combina geocoding de endereço com pesquisa de ponto de interesse. Esta API tem uma corda de forma livre. Esta cadeia pode ser um endereço, lugar, marco, ponto de interesse ou categoria de ponto de interesse. Este processo da API processa o pedido quase em tempo real. Esta API é recomendada para aplicações onde os utilizadores procuram endereços ou pontos de interesse na mesma caixa de texto.
- [**Pesquisa de lote felpudo**](/rest/api/maps/search/postsearchfuzzybatchpreview): Crie um pedido que contenha até 10.000 endereços, locais, marcos ou pontos de interesse e processe-os durante um período de tempo. Todos os dados serão processados em paralelo no servidor e quando concluídos o conjunto de resultados completos podem ser descarregados.

A tabela seguinte cruza referências aos parâmetros API do Google Maps com os parâmetros API comparáveis no Azure Maps.

| Parâmetro API do Google Maps | Parâmetro API de Mapas Azuis comparáveis  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` e `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` - cidade / cidade<br/>`municipalitySubdivision` – bairro, sub/super cidade<br/>`countrySubdivision` - estado ou província<br/>`countrySecondarySubdivision` - concelho<br/>`countryTertiarySubdivision` - distrito<br/>`countryCode` - código país/região de duas letras |
| `key`                       | `subscription-key`– Consulte também a [documentação autenticação com Azure Maps.](azure-maps-authentication.md) |
| `language`                  | `language`– Consulte a documentação [de línguas suportadas.](supported-languages.md)  |
| `region`                    | `countrySet`                       |

Um exemplo de como utilizar o serviço de pesquisa é documentado [aqui.](how-to-search-for-address.md) Certifique-se de rever as [melhores práticas de pesquisa.](how-to-use-best-practices-for-search.md)

> [!TIP]
> O endereço de forma gratuita geocoding e as APIs de pesquisa difusa podem ser usados no modo autocompleto adicionando `&typeahead=true` ao URL de pedido. Isto irá dizer ao servidor que o texto de entrada é provavelmente parcial, e a pesquisa entrará em modo preditivo.

## <a name="reverse-geocode-a-coordinate"></a>Reverter geocódigo uma coordenada

A geocodagem inversa é o processo de conversão das coordenadas geográficas num endereço aproximado. Coordenadas com "longitude: -122.1298, latitude: 47.64005" convertem-se em "1 Microsoft way, Redmond, WA".

A Azure Maps fornece vários métodos de geocodagem inversa:

- [**Endereço de geocodificador inverso**](/rest/api/maps/search/getsearchaddressreverse): Especifique uma única coordenada geográfica para obter o endereço aproximado correspondente a esta coordenada. Processa o pedido perto do tempo real.
- [**Cross street reverse geocoder**](/rest/api/maps/search/getsearchaddressreversecrossstreet): Especifique uma única coordenada geográfica para obter informações de rua transversal próxima e processar o pedido imediatamente. Por exemplo, você pode receber as seguintes ruas cruzadas 1ª Ave e Rua Principal.
- [**Endereço de lote reverso geocodificador**](/rest/api/maps/search/postsearchaddressreversebatchpreview): Crie um pedido que contenha até 10.000 coordenadas e processe-os durante um período de tempo. Todos os dados serão processados em paralelo no servidor. Quando o pedido estiver concluído, pode descarregar o conjunto completo de resultados.

Esta tabela cruza referências aos parâmetros API do Google Maps com os parâmetros API comparáveis no Azure Maps.

| Parâmetro API do Google Maps   | Parâmetro API de Mapas Azuis comparáveis   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key`– Consulte também a [documentação autenticação com Azure Maps.](azure-maps-authentication.md) |
| `language`                  | `language`– Consulte a documentação [de línguas suportadas.](supported-languages.md)  |
| `latlng`                    | `query`  |
| `location_type`             | *N/D*     |
| `result_type`               | `entityType`    |

Reveja as [melhores práticas de pesquisa.](how-to-use-best-practices-for-search.md)

A Azure Maps a axure Maps geocoding API tem algumas funcionalidades adicionais, que não estão disponíveis no Google Maps. Estas funcionalidades podem ser úteis para integrar-se com a sua aplicação, uma vez que migra a sua aplicação:

- Recuperar dados-limite de velocidade
- Recuperar informações sobre o uso da estrada: estrada local, arterial, acesso limitado, rampa, e assim por diante
- Recupere o lado da rua onde está localizada uma coordenada

## <a name="search-for-points-of-interest"></a>Procurar pontos de interesse

Os dados de pontos de interesse podem ser pesquisados no Google Maps utilizando a API de Pesquisa de Lugares. Esta API fornece três maneiras diferentes de procurar pontos de interesse:

- **Encontre o lugar a partir do texto:** Procura um ponto de interesse com base no seu nome, endereço ou número de telefone.
- **Pesquisa nas proximidades** : Procure pontos de interesse que estejam a uma certa distância de um local.
- **Pesquisa de texto:** Procure por locais que utilizem um texto de forma gratuita, que inclui informações de ponto de interesse e localização. Por exemplo, "pizza em Nova Iorque" ou "restaurantes perto da rua principal".

A Azure Maps fornece várias APIs de pesquisa para pontos de interesse:

- [**Pesquisa de POI**](/rest/api/maps/search/getsearchpoi): Procure pontos de interesse pelo nome. Por exemplo, "Starbucks".
- [**Pesquisa na categoria POI**](/rest/api/maps/search/getsearchpoicategory): Procure pontos de interesse por categoria. Por exemplo, "restaurante".
- [**Pesquisa nas proximidades**](/rest/api/maps/search/getsearchnearby): Procure pontos de interesse que estejam a uma certa distância de um local.
- [**Pesquisa fuzzy**](/rest/api/maps/search/getsearchfuzzy): Esta API combina geocoding de endereço com pesquisa de ponto de interesse. Esta API tem uma cadeia de forma livre que pode ser um endereço, lugar, marco, ponto de interesse ou categoria de ponto de interesse. Processa o pedido quase em tempo real. Esta API é recomendada para aplicações onde os utilizadores procuram endereços ou pontos de interesse na mesma caixa de texto.
- [**Pesquisa em geometria**](/rest/api/maps/search/postsearchinsidegeometry): Procure pontos de interesses dentro de uma geometria especificada. Por exemplo, procure um ponto de interesse dentro de um polígono.
- [**Pesse ao longo do percurso**](/rest/api/maps/search/postsearchalongroute): Procure pontos de interesse que estejam ao longo de um caminho de rota especificado.
- [**Pesquisa de lote felpudo**](/rest/api/maps/search/postsearchfuzzybatchpreview): Crie um pedido que contenha até 10.000 endereços, locais, marcos ou pontos de interesse. Processou o pedido durante um período de tempo. Todos os dados serão processados em paralelo no servidor. Quando o pedido estiver concluído, pode descarregar o conjunto completo de resultados.

Atualmente, o Azure Maps não tem uma API comparável à API de pesquisa de texto no Google Maps.

> [!TIP]
> A pesquisa de POI, a pesquisa de categorias POI e apis de pesquisa difusa podem ser usadas no modo autocompleto adicionando `&typeahead=true` ao URL de pedido. Isto irá dizer ao servidor que o texto de entrada é provavelmente parcial. A API conduzirá a pesquisa em modo preditivo.

Reveja as [melhores práticas para](how-to-use-best-practices-for-search.md) documentação de pesquisa.

### <a name="find-place-from-text"></a>Encontre o lugar a partir do texto

Utilize a pesquisa [POI](/rest/api/maps/search/getsearchpoi) do Azure Maps e [procure por pontos](/rest/api/maps/search/getsearchfuzzy) de interesse por nome ou endereço.

A tabela cruza referências aos parâmetros API do Google Maps com os parâmetros API comparáveis do Azure Maps.

| Parâmetro API do Google Maps | Parâmetro API de Mapas Azuis comparáveis |
|---------------------------|-------------------------------------|
| `fields`                  | *N/D*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/D*                               |
| `key`                     | `subscription-key`– Consulte também a [documentação autenticação com Azure Maps.](azure-maps-authentication.md) |
| `language`                | `language`– Consulte a documentação [de línguas suportadas.](supported-languages.md)  |
| `locationbias`            | `lat`, `lon` e `radius`<br/>`topLeft` e `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Pesquisa nas proximidades

Utilize a API [de pesquisa nas proximidades](/rest/api/maps/search/getsearchnearby) para recuperar pontos de interesse próximos, no Azure Maps.

A tabela mostra os parâmetros API do Google Maps com os parâmetros API comparáveis do Azure Maps.

| Parâmetro API do Google Maps | Parâmetro API de Mapas Azuis comparáveis  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key`– Consulte também a [documentação autenticação com Azure Maps.](azure-maps-authentication.md) |
| `keyword`                   | `categorySet` e `brandSet`        |
| `language`                  | `language`– Consulte a documentação [de línguas suportadas.](supported-languages.md)  |
| `location`                  | `lat` e `lon`                     |
| `maxprice`                  | *N/D*                               |
| `minprice`                  | *N/D*                               |
| `name`                      | `categorySet` e `brandSet`        |
| `opennow`                   | *N/D*                               |
| `pagetoken`                 | `ofs` e `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/D*                               |
| `type`                      | `categorySet –`Consulte a documentação [das categorias de pesquisa suportadas.](supported-search-categories.md)   |

## <a name="calculate-routes-and-directions"></a>Calcular rotas e direções

Calcular rotas e direções usando mapas Azure. O Azure Maps tem muitas das mesmas funcionalidades que o serviço de encaminhamento do Google Maps, tais como:

- Horário de chegada e partida.
- Rotas de tráfego em tempo real e preditivas.
- Diferentes modos de transporte. Como conduzir, andar, andar de bicicleta.

> [!NOTE]
> O Azure Maps requer que todos os pontos de passagem sejam coordenadas. Os endereços devem ser geocodificados primeiro.

O serviço de encaminhamento Azure Maps fornece as seguintes APIs para o cálculo das rotas:

- [**Calcular rota:**](/rest/api/maps/route/getroutedirections)Calcular uma rota e ter o pedido processado imediatamente. Esta API suporta pedidos GET e POST. Os pedidos de POST são recomendados ao especificar um grande número de pontos de passagem ou quando se utilizam muitas das opções de rota para garantir que o pedido de URL não se torna muito longo e causa problemas. O Post Route Direction in Azure Maps tem uma opção que pode acolher milhares de [pontos](/rest/api/maps/route/postroutedirections#supportingpoints) de apoio e irá usá-los para recriar um caminho lógico entre eles (snap to road). 
- [**Rota do lote**](/rest/api/maps/route/postroutedirectionsbatchpreview): Crie um pedido que contenha até 1.000 pedidos de rota e processe-os durante um período de tempo. Todos os dados serão processados em paralelo no servidor e quando concluídos o conjunto de resultados completos podem ser descarregados.
- [**Serviços de mobilidade**](/rest/api/maps/mobility): Calcular rotas e direções utilizando o trânsito público.

A tabela cruza referências aos parâmetros API do Google Maps com os parâmetros API comparáveis no Azure Maps.

| Parâmetro API do Google Maps    | Parâmetro API de Mapas Azuis comparáveis  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` – coordenadas no formato `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key`– Consulte também a [documentação autenticação com Azure Maps.](azure-maps-authentication.md) |
| `language`                     | `language`– Consulte a documentação [de línguas suportadas.](supported-languages.md)   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N/A* – Esta característica está relacionada com a geocodagem. Utilize o parâmetro *countrySet* quando utilizar a API de geocodagem Azure Maps.  |
| `traffic_model`               | *N/A* – Só pode especificar se os dados de tráfego devem ser utilizados com o parâmetro *de tráfego.* |
| `transit_mode`                | Ver [documentação dos serviços de mobilidade](/rest/api/maps/mobility) |
| `transit_routing_preference` | Ver [documentação dos serviços de mobilidade](/rest/api/maps/mobility) |
| `units`                        | *N/A* – Azure Maps utiliza apenas o sistema métrico.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Por predefinição, a rota Azure Maps API apenas retorna um resumo. Devolve a distância e os tempos e as coordenadas para o percurso. Utilize o `instructionsType` parâmetro para recuperar instruções de turn-by-turn. E, use o `routeRepresentation` parâmetro para filtrar o resumo e o caminho da rota.

O Azure Maps a API de encaminhamento tem funcionalidades adicionais que não estão disponíveis no Google Maps. Ao migrar a sua aplicação, considere usar estas funcionalidades, pode ache-as úteis.

- Suporte para o tipo de rota: mais curto, mais rápido, trilha e mais eficiente em combustível.
- Suporte para modos de viagem adicionais: autocarro, motocicleta, táxi, caminhão e van.
- Apoio para 150 pontos de passagem.
- Calcular vários tempos de viagem num único pedido; tráfego histórico, tráfego ao vivo, sem tráfego.
- Evite tipos de estradas adicionais: estradas de carpool, estradas não pavimentadas, estradas já utilizadas.
- Especifique áreas personalizadas para evitar.
- Limite a elevação, que a rota pode ascender.
- Rota baseada nas especificações do motor. Calcular as rotas para a combustão ou veículos elétricos com base nas especificações do motor e no combustível ou carga restantes.
- Apoie os parâmetros da rota do veículo comercial. Tais como, dimensões do veículo, peso, número de eixos e tipo de carga.
- Especifique a velocidade máxima do veículo.

Além disso, o serviço de rotas em Azure Maps suporta [o cálculo das gamas de encaminhamento.](/rest/api/maps/route/getrouterange) Calcular gamas de encaminhamento também é conhecido como isochrones. Implica gerar um polígono que cobre uma área que pode ser percorrida em qualquer direção a partir de um ponto de origem. Tudo em uma quantidade especificada de tempo ou quantidade de combustível ou carga.

Reveja as [melhores práticas para a](how-to-use-best-practices-for-routing.md) documentação de encaminhamento.

## <a name="retrieve-a-map-image"></a>Recupere uma imagem de mapa

O Azure Maps fornece uma API para tornar as imagens estáticas do mapa com dados sobrepostos. A [imagem do Mapa render](/rest/api/maps/render/getmapimagerytile) a API em Azure Maps é comparável ao mapa estático API no Google Maps.

> [!NOTE]
> O Azure Maps requer que o centro, todo o marcador e as localizações do caminho sejam coordenadas em formato "longitude, latitude". Enquanto que o Google Maps utiliza o formato "latitude, longitude". Os endereços terão de ser geocodificados primeiro.

A tabela cruza referências aos parâmetros API do Google Maps com os parâmetros API comparáveis no Azure Maps.

| Parâmetro API do Google Maps | Parâmetro API de Mapas Azuis comparáveis  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` – especificado como parte do caminho url. Atualmente apenas o PNG suportado. |
| `key`                       | `subscription-key`– Consulte também a [documentação autenticação com Azure Maps.](azure-maps-authentication.md) |
| `language`                  | `language`– Consulte a documentação [de línguas suportadas.](supported-languages.md)  |
| `maptype`                   | `layer`e `style` – Consulte a documentação dos [estilos de mapa suportados.](supported-map-styles.md) |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N/A* – Esta é uma característica relacionada com a geocodagem. Utilize o `countrySet` parâmetro quando utilizar a API de geocodagem Azure Maps.  |
| `scale`                     | *N/D*                              |
| `size`                      | `width` e `height` – pode ser até 8192x8192 em tamanho. |
| `style`                     | *N/D*                              |
| `visible`                   | *N/D*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> No sistema de azulejos Azure Maps, os azulejos têm o dobro do tamanho dos azulejos de mapas utilizados no Google Maps. Como tal, o valor de nível de zoom no Azure Maps aparecerá um nível de zoom mais próximo no Azure Maps em comparação com o Google Maps. Para compensar esta diferença, decretar o nível de zoom nos pedidos que está a migrar.

Para obter mais informações, consulte o [guia de como fazer na imagem do mapa render a API](how-to-render-custom-data.md).

Além de ser capaz de gerar uma imagem de mapa estático, o serviço de renderização Azure Maps fornece a capacidade de aceder diretamente aos azulejos de mapas em formato raster (PNG) e vetor:

- [**Azulejo do**](/rest/api/maps/render/getmaptile)mapa : Recupere raster (PNG) e azulejos vetoriais para os mapas base (estradas, limites, fundo).
- [**Azulejo de imagens do mapa**](/rest/api/maps/render/getmapimagerytile): Recupere azulejos de imagens aéreas e de satélite.

> [!TIP]
> Muitas aplicações do Google Maps onde mudaram de experiências de mapas interativos para imagens estáticas de mapas há alguns anos. Isto foi feito como um método de poupança de custos. No Azure Maps, é geralmente mais rentável utilizar o controlo de mapas interativos na Web SDK. As cargas interativas de controlo do mapa basearam o número de cargas de azulejos. Os azulejos do mapa em Azure Maps são grandes. Muitas vezes, são precisos apenas alguns azulejos para recriar a mesma vista do mapa que um mapa estático. Os azulejos do mapa são automaticamente em cache pelo navegador. Como tal, o controlo do mapa interativo gera frequentemente uma fração de uma transação ao reproduzir uma visão estática do mapa. Panning e zooming carregarão mais azulejos; no entanto, existem opções no controlo do mapa para desativar este comportamento. O controlo de mapas interativos também fornece muito mais opções de visualização do que os serviços de mapa estático.

### <a name="marker-url-parameter-format-comparison"></a>Comparação do formato do parâmetro url do marcador

**Antes: Google Maps**

Adicione marcadores utilizando o `markers` parâmetro no URL. O `markers` parâmetro requer um estilo e uma lista de locais a serem renderizados no mapa com esse estilo como mostrado abaixo:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Para adicionar estilos adicionais, use os `markers` parâmetros para o URL com um estilo e conjunto diferente de locais.

Especifique as localizações do marcador com o formato "latitude, longitude".

Adicione estilos de marcador com o `optionName:value` formato, com vários estilos separados por tubo ( \| ) caracteres como este "optionName1:value1 \| optionName2:value2". Note que os nomes e valores das opções são separados com um cólon (:). Utilize os seguintes nomes de opção de estilo para marcadores de estilo no Google Maps:

- `color` – A cor do ícone do marcador padrão. Pode ser uma cor hexapóxi de 24 `0xrrggbb` bits ou um dos seguintes valores; , , , , , `black` , `brown` , , , , `green` , `purple` `yellow` `blue` `gray` `orange` `red` . `white`
- `label` – Um único personagem alfanumérico maiúscula para exibir em cima do ícone.
- `size` - O tamanho do marcador. Pode `tiny` `mid` ser, ou `small` . .

Utilize os seguintes nomes de opções de estilo para ícones personalizados no Google Maps:

- `anchor` – Especifica como alinhar a imagem do ícone com a coordenada. Pode ser um valor pixel (x,y) ou um dos seguintes valores; `top`, `bottom` `left` , , , `right` , , , , , ou `center` `topleft` `topright` `bottomleft` `bottomright` .
- `icon` – Um URL que aponta para a imagem do ícone.

Por exemplo, vamos adicionar um marcador vermelho, de tamanho médio, ao mapa em longitude: -110, latitude: 45:

```
&markers=color:red|size:mid|45,-110
```


![Marcador do Google Maps](media/migrate-google-maps-web-services/google-maps-marker.png)

**Depois: Mapas Azure**

Adicione marcadores a uma imagem de mapa estático especificando o `pins` parâmetro no URL. Tal como o Google Maps, especifique um estilo e uma lista de locais no parâmetro. O `pins` parâmetro pode ser especificado várias vezes para suportar marcadores com diferentes estilos.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Para utilizar estilos adicionais, adicione parâmetros adicionais `pins` ao URL com um estilo e conjunto diferentes de locais.

No Azure Maps, a localização do pino tem de estar no formato "longitude latitude". O Google Maps utiliza o formato "latitude, longitude". Um espaço, não uma vírgula, separa a longitude e a latitude no formato Azure Maps.

O `iconType` especificado o tipo de pino para criar. Pode ter os seguintes valores:

- `default` – O ícone do pino padrão.
- `none` – Não é apresentado nenhum ícone, apenas serão apresentadas etiquetas.
- `custom` – Especifica um ícone personalizado para ser usado. Um URL que aponta para a imagem do ícone pode ser adicionado ao fim do `pins` parâmetro após a informação da localização do pino.
- `{udid}` – Um ID de dados único (UDID) para um ícone armazenado na plataforma de armazenamento de dados Azure Maps.

Adicione estilos pinos com o `optionNameValue` formato. Separe vários estilos com os \| caracteres do tubo. Por exemplo: `iconType|optionName1Value1|optionName2Value2`. Os nomes e valores das opções não estão separados. Utilize os seguintes nomes de opção de estilo para marcadores de estilo:

- `al` – Especifica a opacidade (alfa) do marcador. Escolha um número entre 0 e 1.
- `an` – Especifica a âncora do pino. Especifique os valores X e y pixel no formato "x y".
- `co` – A cor do pino. Especifique uma cor hexaxada de 24 bits: `000000` para `FFFFFF` .
- `la` – Especifica a âncora da etiqueta. Especifique os valores X e y pixel no formato "x y".
- `lc` – A cor do rótulo. Especifique uma cor hexaxada de 24 bits: `000000` para `FFFFFF` .
- `ls` – O tamanho da etiqueta em pixels. Escolha um número superior a 0.
- `ro` – Um valor em graus para rodar o ícone. Escolha um número entre -360 e 360.
- `sc` – Um valor de escala para o ícone do pino. Escolha um número superior a 0.

Especifique os valores da etiqueta para cada localização do pino. Esta abordagem é mais eficiente do que aplicar um valor único de etiqueta a todos os marcadores da lista de locais. O valor da etiqueta pode ser uma série de múltiplos caracteres. Embrulhe a cadeia com citações simples para garantir que não se engana como um valor de estilo ou localização.

Adicionemos um ícone vermelho `FF0000` () padrão, com a etiqueta "Space Needle", posicionada abaixo (15 50). O ícone está em longitude: -122.349300, latitude: 47.620180:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

![Marcador de Mapas Azure](media/migrate-google-maps-web-services/azure-maps-marker.png)

Adicione três pinos com os valores da etiqueta '1', '2' e '3':

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

![Azure Maps múltiplos marcadores](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)

### <a name="path-url-parameter-format-comparison"></a>Comparação do formato do parâmetro URL do caminho

**Antes: Google Maps**

Adicione linhas e polígono a uma imagem de mapa estático usando o `path` parâmetro no URL. O `path` parâmetro requer um estilo e uma lista de locais a serem renderizados no mapa, como mostrado abaixo:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Use estilos adicionais adicionando parâmetros adicionais `path` ao URL com um estilo e conjunto diferentes de locais.

As localizações do caminho são especificadas com o `latitude1,longitude1|latitude2,longitude2|…` formato. Os caminhos podem ser codificados ou conter endereços para pontos.

Adicione estilos de caminho com o `optionName:value` formato, separe vários estilos pelo tubo \| () caracteres. E, nomes e valores de opção separados com um cólon (:). Assim: `optionName1:value1|optionName2:value2` . Os seguintes nomes de opção de estilo podem ser usados para estilo caminhos no Google Maps:

- `color` – A cor do caminho ou do contorno do polígono. Pode ser uma cor hexa negra de 24 bits `0xrrggbb` , uma cor hexa negra de 32 `0xrrggbbbaa` bits ou um dos seguintes valores: preto, marrom, verde, roxo, amarelo, azul, cinza, laranja, vermelho, branco.
- `fillColor` – A cor para preencher a área do caminho com (polígono). Pode ser uma cor hexa negra de 24 bits `0xrrggbb` , uma cor hexa negra de 32 `0xrrggbbbaa` bits ou um dos seguintes valores: preto, marrom, verde, roxo, amarelo, azul, cinza, laranja, vermelho, branco.
- `geodesic` – Indica se o caminho deve ser uma linha que segue a curvatura da terra.
- `weight` – A espessura da linha do caminho em pixels.

Adicione uma opacidade de linha vermelha e espessura de pixel ao mapa entre as coordenadas, no parâmetro URL. Para o exemplo abaixo, a linha tem uma opacidade de 50% e uma espessura de quatro píxeis. As coordenadas são de longitude: -110, latitude: 45 e longitude: -100, latitude: 50.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

![Poliline do Google Maps](media/migrate-google-maps-web-services/google-maps-polyline.png)

**Depois: Mapas Azure**

Adicione linhas e polígonos a uma imagem de mapa estático especificando o `path` parâmetro no URL. Tal como o Google Maps, especifique um estilo e uma lista de localizações neste parâmetro. Especifique o `path` parâmetro várias vezes para tornar vários círculos, linhas e polígonos com diferentes estilos.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

No que diz respeito a localizações de caminhos, o Azure Maps requer que as coordenadas estejam em formato de "latitude de longitude". O Google Maps utiliza o formato "latitude, longitude". Um espaço, não uma vírgula, separa a longitude e a latitude no formato Azure Maps. O Azure Maps não suporta caminhos ou endereços codificados para pontos. Faça o upload de conjuntos de dados maiores como um ficheiro GeoJSON na API de armazenamento de dados Azure Maps, conforme documentado [aqui.](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)

Adicione estilos de caminho com o `optionNameValue` formato. Separe vários estilos por tubo \| () caracteres, como `optionName1Value1|optionName2Value2` este. Os nomes e valores das opções não estão separados. Use os seguintes nomes de opção de estilo para estilo caminhos em Azure Maps:

- `fa` - A opacidade da cor de enchimento (alfa) utilizada na renderização de polígonos. Escolha um número entre 0 e 1.
- `fc` - A cor de preenchimento usada para tornar a área de um polígono.
- `la` – A opacidade da cor da linha (alfa) utilizada na renderização de linhas e o contorno dos polígonos. Escolha um número entre 0 e 1.
- `lc` – A cor da linha utilizada para renderizar linhas e o contorno dos polígonos.
- `lw` – A largura da linha em pixels.
- `ra` – Especifica um raio de círculos em metros.

Adicione uma opacidade de linha vermelha e espessura de pixel entre as coordenadas, no parâmetro URL. Para o exemplo abaixo, a linha tem 50% de opacidade e uma espessura de quatro píxeis. As coordenadas têm os seguintes valores: longitude: -110, latitude: 45 e longitude: -100, latitude: 50.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

![Polilina Azure Maps](media/migrate-google-maps-web-services/azure-maps-polyline.png)

## <a name="calculate-a-distance-matrix"></a>Calcular uma matriz de distância

O Azure Maps fornece a matriz de distância API. Utilize esta API para calcular os tempos de viagem e as distâncias entre um conjunto de locais, com uma matriz de distância. É comparável à matriz de distância API no Google Maps.

- [**Matriz de rota**](/rest/api/maps/route/postroutematrixpreview): Calcula assíncroticamente os tempos e distâncias de viagem para um conjunto de origens e destinos. Suporta até 700 células por pedido. É o número de origens multiplicadas pelo número de destinos. Com este constrangimento em mente, exemplos de possíveis dimensões matricias são: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Um pedido para a matriz de distância API só pode ser feito usando um pedido POST com a informação de origem e destino no corpo do pedido. Além disso, o Azure Maps requer que todas as origens e destinos sejam coordenadas. Os endereços terão de ser geocodificados primeiro.

Esta tabela cruza referências aos parâmetros API do Google Maps com os parâmetros API comparáveis do Azure Maps.

| Parâmetro API do Google Maps      | Parâmetro API de Mapas Azuis comparáveis  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` – especificar no órgão de pedido DO POST como GeoJSON. |
| `key`                          | `subscription-key`– Consulte também a [documentação autenticação com Azure Maps.](azure-maps-authentication.md) |
| `language`                     | `language`– Consulte a documentação [de línguas suportadas.](supported-languages.md)  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` – especificar no órgão de pedido DO POST como GeoJSON.  |
| `region`                       | *N/A* – Esta característica está relacionada com a geocodagem. Utilize o `countrySet` parâmetro quando utilizar a API de geocodagem Azure Maps. |
| `traffic_model`                | *N/A* – Só pode especificar se os dados de tráfego devem ser utilizados com o `traffic` parâmetro. |
| `transit_mode`                 | *N/A* - As matrizes de distância baseadas em trânsito não são suportadas atualmente.  |
| `transit_routing_preference`   | *N/A* - As matrizes de distância baseadas em trânsito não são suportadas atualmente.  |
| `units`                        | *N/A* – Azure Maps utiliza apenas o sistema métrico. |

> [!TIP]
> Todas as opções avançadas de encaminhamento disponíveis na API de encaminhamento Azure Maps são suportadas na matriz de distância Azure Maps API. As opções avançadas de encaminhamento incluem: encaminhamento de camiões, especificações do motor, e assim por diante.

Reveja as [melhores práticas para a](how-to-use-best-practices-for-routing.md) documentação de encaminhamento.

## <a name="get-a-time-zone"></a>Obter um fuso horário

O Azure Maps fornece uma API para recuperar o fuso horário de uma coordenada. O fuso horário Azure Maps API é comparável ao fuso horário API no Google Maps:

- [**Fuso horário por coordenada:**](/rest/api/maps/timezone/gettimezonebycoordinates)Especifique uma coordenada e receba os detalhes do fuso horário da coordenada.

Esta tabela cruza referências aos parâmetros API do Google Maps com os parâmetros API comparáveis no Azure Maps.

| Parâmetro API do Google Maps | Parâmetro API de Mapas Azuis comparáveis   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key`– Consulte também a [documentação autenticação com Azure Maps.](azure-maps-authentication.md)       |
| `language`                  | `language`– Consulte a documentação [de línguas suportadas.](supported-languages.md)    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Além desta API, o Azure Maps fornece uma série de APIs de fuso horário. Estas APIs convertem o tempo com base nos nomes ou nos IDs do fuso horário:

- [**Fuso horário por ID**](/rest/api/maps/timezone/gettimezonebyid): Devolve informações atuais, históricas e futuras do fuso horário para o ID do fuso horário IANA especificado.
- [**Fuso horário Enum IANA**](/rest/api/maps/timezone/gettimezoneenumiana): Devolve uma lista completa de IDs do fuso horário IANA. As atualizações para o serviço IANA refletem-se no sistema dentro de um dia.
- [**Time zone Enum Windows**](/rest/api/maps/timezone/gettimezoneenumwindows): Devolve uma lista completa de IDs do Windows Time Zone.
- [**Versão IANA do fuso horário**](/rest/api/maps/timezone/gettimezoneianaversion): Devolve o número atual da versão IANA utilizado pelo Azure Maps.
- [**Fuso horário Windows to IANA**](/rest/api/maps/timezone/gettimezonewindowstoiana): Devolve um ID IANA correspondente, dado um ID válido do Fuso Horário do Windows. Vários IDs IANA podem ser devolvidos para um único ID do Windows.

## <a name="client-libraries"></a>Bibliotecas de cliente

O Azure Maps fornece bibliotecas de clientes para as seguintes linguagens de programação:

- JavaScript, TypeScript, Node.js – [documentation](how-to-use-services-module.md) \| [pacote NPM](https://www.npmjs.com/package/azure-maps-rest) de documentação

Estas bibliotecas de clientes de código aberto são para outras linguagens de programação:

- .NET Standard 2.0 – [GitHub project](https://github.com/perfahlen/AzureMapsRestServices) \| [Pacote NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/) do projeto GitHub

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os serviços Azure Maps REST:

> [!div class="nextstepaction"]
> [Melhores práticas de pesquisa](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Pesquisar por um endereço](how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Melhores práticas para o encaminhamento](how-to-use-best-practices-for-routing.md)

> [!div class="nextstepaction"]
> [Documentação de referência do Serviço API do Azure Maps REST](https://docs.microsoft.com/rest/api/maps/)

> [!div class="nextstepaction"]
> [Amostras de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)

> [!div class="nextstepaction"]
> [Como utilizar o módulo de serviços (Web SDK)](how-to-use-best-practices-for-routing.md)
