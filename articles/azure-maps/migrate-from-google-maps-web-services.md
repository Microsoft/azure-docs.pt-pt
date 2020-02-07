---
title: 'Tutorial: migrar serviços Web do Google Maps | Mapas do Microsoft Azure'
description: Como migrar serviços Web do Google Maps para mapas de Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 50bdc0722328f857279b2cbd9a6e4cee740b9df8
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048938"
---
# <a name="migrate-web-service-from-google-maps"></a>Migrar serviço Web do Google Maps

O Azure Maps e o Google Maps fornecem acesso a APIs espaciais através de serviços web REST. As interfaces API destas duas plataformas executam funcionalidades semelhantes, mas cada uma utiliza diferentes convenções de nomeação e objetos de resposta.

A tabela seguinte mostra o serviço Azure Maps API, que fornece uma funcionalidade semelhante a um Serviço Google Maps API.

| API de serviço do Google Maps | API de serviço do Azure Maps                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Direções              | [Rota](https://docs.microsoft.com/rest/api/maps/route)                               |
| Matriz de Distância         | [Matriz da Rota](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| Geocodificação               | [Pesquisa](https://docs.microsoft.com/rest/api/maps/search)                             |
| Pesquisa de locais           | [Pesquisa](https://docs.microsoft.com/rest/api/maps/search)                             |
| Preenchimento automático      | [Pesquisa](https://docs.microsoft.com/rest/api/maps/search)                             |
| Mapa estático              | [Render](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| Fuso Horário               | [Fuso horário](https://docs.microsoft.com/rest/api/maps/timezone)                        |

As seguintes APIs de serviço não estão atualmente disponíveis no Azure Maps:

- Elevação
- Geolocalização
- Coloque detalhes e coloque fotos. Os números de telefone e o URL do site estão disponíveis na API de pesquisa do Azure Maps.
- Mapear URLs
- Estradas, estradas. Os dados do limite de velocidade estão disponíveis através da rota e revertem as APIs geocodificadoras em Mapas Azure.
- Exibição de rua estática

O mapas do Azure tem vários serviços Web REST adicionais que podem ser interessantes:

- [Operações espaciais](https://docs.microsoft.com/rest/api/maps/spatial): Descarregar cálculos e operações espaciais complexos, como geofencing, para um serviço.
- [Tráfego](https://docs.microsoft.com/rest/api/maps/traffic): Aceder ao fluxo de tráfego em tempo real e aos dados de incidentes.

## <a name="geocoding-addresses"></a>Endereços geocodificados

A geocodificação é o processo de conversão de um endereço numa coordenada. Por exemplo, "1 Microsoft way, Redmond, WA" converte-se em "longitude: -122.1298, latitude: 47.64005". As coordenadas são necessárias para posicionar um marcador num mapa ou centrar um mapa.

O Azure Maps fornece vários métodos para endereços geocodificados:

- [**Geocodificação de endereços gratuitos**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): Especifique uma única cadeia de endereços e processe imediatamente o pedido. Um único endereço de corda é "1 Microsoft Way, Redmond, WA". Este método é recomendado quando precisa de geocodificar endereços individuais rapidamente.
- [**Geocodificação de endereçoestruturado**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): Especifique as partes de um único endereço e processe o pedido em tempo real. Partes de um endereço incluem o nome de rua, cidade, país e código postal. Este método é recomendado para dois cenários principais. Os dados já estão analisados como peças de endereço individuais. Ou, precisa de geocodificar endereços individuais rapidamente.
- [**Geocodificação de endereços**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview)de lote : Crie um pedido que contenha até 10.000 endereços e processe o pedido durante um período de tempo. Todos os endereços serão geocodificados em paralelo no servidor. Quando a geocodificação completa, todo o conjunto de resultados torna-se transferível. Este método é recomendado para geocodificar grandes conjuntos de dados.
- [**Pesquisa fuzzy**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Esta API combina gecodificação de endereços com procura de ponto de interesse. Esta API recebe uma corda de forma livre e processa o pedido em tempo real. Uma cadeia de forma livre pode ser um endereço, lugar, marco, ponto de interesse ou categoria de ponto de interesse. Esta API é recomendada quando a mesma caixa de texto é utilizada para consultar endereços e pontos de interesse.
- [**Pesquisa de lote fuzzy**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Crie um pedido que contenha até 10.000 endereços e processe o pedido durante um período de tempo. Você pode solicitar lugares, marcos ou pontos de interesse. Todos os dados serão processados no servidor e em paralelo. Quando concluído o conjunto de resultados torna-se transferível.

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros de API comparáveis no Azure Maps.

| Parâmetro da API do Google Maps | Parâmetro de API comparável do Azure Maps  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` e `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` - cidade/cidade<br/>`municipalitySubdivision` – bairro, sub/super cidade<br/>`countrySubdivision` - estado ou província<br/>`countrySecondarySubdivision` - concelho<br/>`countryTertiarySubdivision` - distrito<br/>`countryCode` - código de país de duas letras |
| `key`                       | `subscription-key` – Consulte também a Autenticação com documentação [do Azure Maps.](azure-maps-authentication.md) |
| `language`                  | `language` – Ver documentação de [línguas suportadas.](supported-languages.md)  |
| `region`                    | `countrySet`                       |

Um exemplo de como utilizar o serviço de pesquisa é documentado [aqui](how-to-search-for-address.md). Certifique-se de rever [as melhores práticas de pesquisa.](how-to-use-best-practices-for-search.md)

> [!TIP]
> As APIs de geocodificação de endereços gratuitos e de pesquisa difusa podem ser utilizadas no modo autocompleto, adicionando `&amp;typeahead=true` ao URL de pedido. Isto dirá ao servidor que o texto de entrada é provavelmente parcial e que a pesquisa entrará em modo preditivo.

## <a name="reverse-geocode-a-coordinate"></a>Reverter código a uma coordenada

Geocodificação inversa é o processo de conversão de coordenadas geográficas num endereço aproximado. Coordenadas com "longitude: -122.1298, latitude: 47.64005" convertem-se em "1 Microsoft Way, Redmond, WA".

O mapas do Azure fornece vários métodos de geocodificação inversa:

- [**Endereço geocodificador inverso**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): Especifique uma única coordenada geográfica para obter o endereço aproximado correspondente a esta coordenada. Processe o pedido em tempo real.
- [**Cross street reverse geocoder**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): Especifique uma única coordenada geográfica e recupere informações sobre a rua transversal próxima. Processe o pedido em tempo real.
- Endereço de [**lote geocodificador inverso**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): Crie um pedido contendo até 10.000 coordenadas e processe o pedido durante um período de tempo. Todos os dados serão processados paralelamente no servidor. Quando concluído todo o conjunto de resultados torna-se transferível.

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros de API comparáveis no Azure Maps.

| Parâmetro da API do Google Maps   | Parâmetro de API comparável do Azure Maps   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – Consulte também a Autenticação com documentação [do Azure Maps.](azure-maps-authentication.md) |
| `language`                  | `language` – Ver documentação de [línguas suportadas.](supported-languages.md)  |
| `latlng`                    | `query`  |
| `location_type`             | *N/D*     |
| `result_type`               | `entityType`    |

Reveja [as melhores práticas de pesquisa.](how-to-use-best-practices-for-search.md)

O Azure Maps inverte a geocodificação API tem algumas funcionalidades adicionais, que não estão disponíveis no Google Maps. Estas funcionalidades podem ser úteis para integrar com a sua aplicação, uma vez que migra a sua aplicação:

- Recuperar dados do limite de velocidade
- Recuperar informações de utilização da estrada: estrada local, arterial, acesso limitado, rampa, e assim por diante
- Recupere o lado da rua onde está localizada uma coordenada

## <a name="search-for-points-of-interest"></a>Procurar pontos de interesse

Os dados de ponto de interesse podem ser pesquisados no Google Maps utilizando a API de Pesquisa de Lugares. Essa API fornece três maneiras diferentes de procurar pontos de interesse:

- **Encontre lugar a partir do texto:** Procura um ponto de interesse com base no seu nome, endereço ou número de telefone.
- **Pesquisa nas proximidades**: Procura pontos de interesse que estejam a uma certa distância de um local.
- **Pesquisa de texto:** Pesquisas por locais usando um texto de formulário livre, que inclui informações sobre ponto de interesse e localização. Por exemplo, "pizza em Nova York" ou "restaurantes perto do Main St".

O mapas do Azure fornece várias APIs de pesquisa para pontos de interesse:

- [**Pesquisa POI**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): Procure pontos de interesse saem pelo nome. Por exemplo, "Starbucks".
- [**Pesquisa da categoria POI**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): Procurar pontos de interesse por categoria. Por exemplo, "restaurante".
- [**Pesquisa nas proximidades**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): Procura pontos de interesse que estejam a uma certa distância de um local.
- [**Pesquisa fuzzy**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Esta API combina gecodificação de endereços com procura de ponto de interesse. Esta API leva uma corda em forma livre. Um endereço, um lugar, um marco, um ponto de interesse, uma categoria de ponto de interesse, e assim por diante. Esta API pode processar o pedido em tempo real. Esta API é recomendada quando os utilizadores procuram endereços ou pontos de interesse utilizando a mesma caixa de texto.
- Pesquisa dentro da [**geometria**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): Procure pontos de interesses que estejam dentro de uma geometria especificada (polígono).
- [**Pesquisa ao longo do percurso**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): Procure pontos de interesses que estejam ao longo de um percurso específico.
- [**Pesquisa de lotes fuzzy**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Crie um pedido que contenha até 10.000 endereços, lugares, marcos ou pontode interesses e processe-os durante um período de tempo. Todos os dados serão processados paralelamente no servidor. Quando o pedido concluir o processamento, faça o download do conjunto completo de resultados.

Atualmente, o Azure Maps não tem uma API comparável à API de pesquisa de texto no Google Maps.

> [!TIP]
> A pesquisa POI API, a categoria POI pesquisa MAE e APIs de pesquisa difusa podem ser usados em modo autocompleto. Adicione `&amp;typeahead=true` ao URL de pedido. Isto dirá ao servidor que o texto de entrada é provavelmente parcial e que a pesquisa será concluída em modo preditivo.

Reveja [as melhores práticas de pesquisa.](how-to-use-best-practices-for-search.md)

### <a name="find-place-from-text"></a>Localizar local do texto

Utilize o Azure Maps a API de [pesquisa poi,](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) ou a API de [pesquisa fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) para procurar pontos de interesse por nome ou por endereço.

A tabela seguinte mostra os parâmetros da API do Google Maps com os respetivos parâmetros DaPI Do Azure Maps.

| Parâmetro da API do Google Maps | Parâmetro de API comparável do Azure Maps |
|---------------------------|-------------------------------------|
| `fields`                  | *N/D*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/D*                               |
| `key`                     | `subscription-key` – Consulte também a Autenticação com documentação [do Azure Maps.](azure-maps-authentication.md) |
| `language`                | `language` – Ver documentação de [línguas suportadas.](supported-languages.md)  |
| `locationbias`            | `lat`, `lon` e `radius`<br/>`topLeft` e `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Pesquisa próxima

No Azure Maps, recupere pontos de interesse nas proximidades utilizando a API de [pesquisa próxima.](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)

A tabela que se segue cruza as referências aos parâmetros da API do Google Maps com parâmetros API do Azure Maps.

| Parâmetro da API do Google Maps | Parâmetro de API comparável do Azure Maps  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` – Consulte também a Autenticação com documentação [do Azure Maps.](azure-maps-authentication.md) |
| `keyword`                   | `categorySet` e `brandSet`        |
| `language`                  | `language` – Ver documentação de [línguas suportadas.](supported-languages.md)  |
| `location`                  | `lat` e `lon`                     |
| `maxprice`                  | *N/D*                               |
| `minprice`                  | *N/D*                               |
| `name`                      | `categorySet` e `brandSet`        |
| `opennow`                   | *N/D*                               |
| `pagetoken`                 | `ofs` e `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/D*                               |
| `type`                      | `categorySet –` Ver documentação [de categorias de pesquisa suportadas.](supported-search-categories.md)   |

## <a name="calculate-routes-and-directions"></a>Calcular rotas e direções

Calcular rotas e direções utilizando o Azure Maps. O mapas do Azure tem muitas das mesmas funcionalidades que o serviço de roteamento do Google Maps, como:

- horas de chegada e saída.
- rotas de tráfego com base em tempo real e em previsão.
- Diferentes modos de transporte. Como conduzir, andar, andar de bicicleta.

> [!NOTE]
> O mapas do Azure exige que todas as Marcos sejam coordenadas. Os endereços têm de ser geocodificados primeiro.

O serviço de roteamento do Azure Maps fornece as seguintes APIs para calcular as rotas:

- [**Calcular a rota**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): Calcular uma rota e processar o pedido em tempo real. Essa API dá suporte a solicitações GET e POST. Os pedidos post são recomendados para especificar um grande número de pontos de passagem, ou usando muitas opções de rota. A utilização do POST garante que o pedido de URL não se torna demasiado longo e causa problemas.
- [**Rota do lote**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): Crie um pedido que contenha até 1.000 pedidos de rota. O pedido processa durante um período de tempo. Todos os processos de dados em paralelo no servidor. Faça o download do conjunto assim que o pedido estiver concluído e os resultados estejam prontos.
- [**Serviços de mobilidade**](https://docs.microsoft.com/rest/api/maps/mobility): Calcular rotas e direções utilizando o trânsito público.

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros de API comparáveis no Azure Maps.

| Parâmetro da API do Google Maps    | Parâmetro de API comparável do Azure Maps  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` – coordenadas no formato `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key` – Consulte também a Autenticação com documentação [do Azure Maps.](azure-maps-authentication.md) |
| `language`                     | `language` – Ver documentação de [línguas suportadas.](supported-languages.md)   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N/A* – Esta funcionalidade está relacionada com geocodificação. Utilize o parâmetro *countrySet* ao utilizar a API de geocodificação do Azure Maps.  |
| `traffic_model`               | *N/A* – Só pode especificar se os dados de tráfego devem ser utilizados com o parâmetro de *tráfego.* |
| `transit_mode`                | Consulte [a documentação dos serviços de mobilidade](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Consulte [a documentação dos serviços de mobilidade](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *N/A* – O Azure Maps utiliza apenas o sistema métrico.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Por padrão, a API de rotas do Azure Maps retorna apenas um resumo (distância e vezes) e as coordenadas para o caminho da rota. Utilize o parâmetro `instructionsType` para recuperar instruções de turn-by-turn. Utilize o parâmetro `routeRepresentation` para filtrar o resumo e o percurso de rota.

O API de encaminhamento do Azure Maps tem muitas funcionalidades adicionais, que não estão disponíveis no Google Maps. Pode ser útil integrar estas funcionalidades, à medida que migra a sua aplicação:

- Suporte para tipo de rota: mais curto, mais rápido, Trilling e mais eficiente de combustível.
- Suporte para modos de viagem adicionais: barramento, Motorcycle, táxi, caminhão e Van.
- Suporte para 150 marcos.
- Calcular vários tempos de viagem em uma única solicitação; tráfego histórico, tráfego ao vivo, sem tráfego.
- Evite tipos de estrada adicionais: estradas andas, estradas unpaveds e estradas já usadas.
- Especifique áreas personalizadas a serem evitadas.
- Limite a elevação, que a rota pode subir.
- Rota com base nas especificações do motor. Calcular rotas para veículos de combustão ou elétricos com base nas especificações do motor e o combustível ou carga restantes.
- Apoiar os parâmetros da rota do veículo comercial. Tais como dimensões do veículo, peso, número de eixos e tipo de carga.
- Especifique a velocidade máxima do veículo.

Além destas funcionalidades, o serviço de rotas no Azure Maps suporta o cálculo de [gamas de encaminhamento.](https://docs.microsoft.com/rest/api/maps/route/getrouterange) Calcular gamas de encaminhamento também é conhecido como isochrones. Implica gerar uma área coberta com um polígono. Então, calcular a viagem em qualquer direção a partir de um ponto de origem. Tudo considerando uma quantidade de tempo especificada, e a quantidade de combustível ou carga.

## <a name="retrieve-a-map-image"></a>Recuperar uma imagem do mapa

O mapas do Azure fornece uma API para renderizar as imagens de mapa estático com dados sobrepostos. A [imagem do Mapa renderiza](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) API no Azure Maps é comparável ao mapa estático API no Google Maps.

> [!NOTE]
> O Azure Maps exige que o centro, todos os marcadores, e os locais do caminho sejam coordenados em formato de "longitude, latitude". Por outro lado, o Google Maps utiliza o formato "latitude, longitude". Os endereços precisarão ser geocodificados primeiro.

A tabela seguinte mostra os parâmetros DaPi do Google Maps com os parâmetros comparáveis da API do Azure Maps.

| Parâmetro da API do Google Maps | Parâmetro de API comparável do Azure Maps  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` – especificado como parte do caminho url. Atualmente, apenas o PNG tem suporte. |
| `key`                       | `subscription-key` – Consulte também a Autenticação com documentação [do Azure Maps.](azure-maps-authentication.md) |
| `language`                  | `language` – Ver documentação de [línguas suportadas.](supported-languages.md)  |
| `maptype`                   | `layer` e `style` – Ver documentação de [estilos de mapas suportados.](supported-map-styles.md) |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N/A* – Esta é uma característica relacionada com geocodificação. Utilize o parâmetro `countrySet` quando utilizar a API de geocodificação Do Azure Maps.  |
| `scale`                     | *N/D*                              |
| `size`                      | `width` e `height` – pode ter até 8192x8192 de tamanho. |
| `style`                     | *N/D*                              |
| `visible`                   | *N/D*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> O mapas do Azure usa um sistema de peças com blocos que são duas vezes o tamanho dos blocos de mapa usados no Google Maps. Como tal, o valor de zoom aparece um nível de zoom mais próximo no Azure Maps em comparação com o Google Maps. Decreção do nível de zoom por um, nos pedidos que está a migrar. A decreção do valor do nível de zoom compensa a variação dos sistemas de azulejos.

Para mais informações, consulte o [guia como orientar na imagem do mapa render API](how-to-render-custom-data.md).

Além de gerar uma imagem de mapa estático, o serviço de renderização Azure Maps fornece a capacidade de aceder diretamente a azulejos de mapas em formato de raster (PNG) e vetor:

- [**Azulejo do mapa:** ](https://docs.microsoft.com/rest/api/maps/render/getmaptile)Recuperar raster (PNG) e azulejos vetoriais para os mapas base (estradas, limites, fundo).
- [**Azulejos de imagens de mapas:** ](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)Recupere azulejos de imagens aéreas e de satélite.

> [!TIP]
> Há alguns anos, muitas aplicações do Google Maps mudaram de experiências de mapas interativos para imagens de mapas estáticos, como um método de poupança de custos. No Azure Maps, muitas vezes é muito mais rentável utilizar o controlo interativo do mapa no Web SDK. A taxa de serviço baseia-se no número de telhas de mapa que a aplicação carrega. Os azulejos do mapa em Mapas Azure são grandes. Muitas vezes, são precisos apenas alguns azulejos para recriar a mesma vista de mapa que um mapa estático. Os azulejos do mapa são automaticamente cached pelo navegador. Como tal, o controlo interativo do mapa gera frequentemente uma fração de uma transação ao reproduzir uma visão estática do mapa. A panorâmica e o zoomcarregarão mais azulejos, no entanto existem opções no controlo do mapa para desativar este comportamento. O controlo interativo do mapa também fornece muito mais opções de visualização do que serviços de mapas estáticos.

### <a name="marker-url-parameter-format-comparison"></a>Comparação de formato de parâmetro de URL de marcador

**Antes: Google Maps**

No Google Maps, pode adicionar marcadores a uma imagem de mapa estático utilizando o parâmetro `markers` no URL. O parâmetro `markers` leva um estilo e uma lista de locais a serem renderizados no mapa com esse estilo, como mostrado abaixo:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Estilos adicionais podem ser usados adicionando parâmetros adicionais `markers` ao URL com um estilo e conjunto diferente de locais.

Os locais de marcador são especificados com o formato "latitude, longitude".

Os estilos de marcador no Google Maps são adicionados com o formato `optionName:value`, com vários estilos separados por caracteres de tubos (\|). Assim: "optionName1:value1\|optionName2:value2". Observe que os nomes e valores de opção são separados por dois-pontos (:). Os seguintes nomes de opção de estilo podem ser usados para marcadores de estilo no Google Maps:

- `color` – A cor do ícone do marcador predefinido. Pode ser uma cor hexaque de 24 bits (`0xrrggbb`) ou um dos seguintes valores; `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label` – Um único personagem alfanumérico superior para exibir em cima do ícone.
- `size` - O tamanho do marcador. Pode ser `tiny`, `mid`ou `small`.

Ícones personalizados podem ser adicionados no Google Maps usando os seguintes nomes de opções de estilo:

- `anchor` – Especifica como alinhar a imagem do ícone à coordenada. Pode ser um valor pixel (x,y) ou um dos seguintes valores; `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft`ou `bottomright`.
- `icon` – Um URL que aponta para a imagem do ícone.

Por exemplo, no Google Maps, um marcador vermelho de médio porte pode ser adicionado ao mapa em coordenadas (Longitude:-110, Latitude: 45) com o seguinte parâmetro de URL:

```
&markers=color:red|size:mid|45,-110
```

<center>

![](media/migrate-google-maps-web-services/google-maps-marker.png)</center> de marcadores do Google Maps

**Depois: Mapas Azure**

No Azure Maps, adicione marcadores a uma imagem de mapa estático especificando o parâmetro `pins` no URL. Tal como o Google Maps, especifique um estilo e uma lista de localizações neste parâmetro. Especifique o parâmetro `pins` várias vezes para suportar marcadores com diferentes estilos.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Para utilizar estilos adicionais, adicione parâmetros adicionais `pins` ao URL com um estilo e conjunto diferentes de locais.

Para a localização do pino, o Azure Maps exige que as coordenadas estejam em formato de "latitude longitude". O Google Maps usa o formato "latitude, longitude". Um espaço, não uma vírposta, separa a longitude e a latitude no formato Azure Maps.

O `iconType` especifica o tipo de pino para criar. Pode ter os seguintes valores:

- `default` – O ícone do pino predefinido.
- `none` – Não é apresentado nenhum ícone, apenas serão renderizadas etiquetas.
- `custom` – Especifica que deve ser utilizado um ícone personalizado. Um URL que aponta para a imagem do ícone pode ser adicionado à extremidade do parâmetro `pins` após a informação de localização do pino.
- `{udid}` – Um ID de dados único (UDID) para um ícone armazenado na plataforma de armazenamento de dados do Azure Maps.

Adicione os estilos pin os estilos Azure Maps com o formato `optionNameValue`. Separe vários estilos com os caracteres do tubo (\|). Por exemplo: `iconType|optionName1Value1|optionName2Value2`. Os nomes e valores das opções não estão separados. Utilize os seguintes nomes de opção de estilo para marcadores de estilo em Mapas Azure:

- `al` – Especifica a opacidade (alfa) do marcador. Escolha um número entre 0 e 1.
- `an` – Especifica a âncora do pino. Valores de pixel X e y especificados no formato "x y".
- `co` – A cor do pino. Deve ser uma cor hexada de 24 bits: `000000` para `FFFFFF`.
- `la` – Especifica a âncora do rótulo. Valores de pixel X e y especificados no formato "x y".
- `lc` – A cor do rótulo. Deve ser uma cor hexada de 24 bits: `000000` para `FFFFFF`.
- `ls` – O tamanho da etiqueta em píxeis. Escolha um número maior que 0.
- `ro` – Um valor em graus para rodar o ícone. Escolha um número entre -360 e 360.
- `sc` – Um valor de escala para o ícone do pino. Escolha um número maior que 0.

Os valores da etiqueta são especificados para cada localização do pino. Esta abordagem é mais eficiente do que aplicar um único valor de etiqueta a todos os marcadores na lista de locais. O valor do rótulo pode ser uma série de vários caracteres. Embrulhe a corda com citações únicas para garantir que não é confundida como um estilo ou valor de localização.

Por exemplo, no Azure Maps, adicionando um ícone predefinido vermelho (`FF0000`), com a etiqueta "Space Needle", posicionada abaixo (15 50), com ícone em coordenadas (longitude: -122.349300, latitude: 47.620180) é feito com o seguinte parâmetro URL:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![azure Maps](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

O exemplo a seguir adiciona três pinos com os valores de rótulo ' 1 ', ' 2 ' e ' 3 ':

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Azure Maps vários marcadores](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Comparação de formato de parâmetro de URL de caminho

**Antes: Google Maps**

No Google Maps, as linhas e os polígonos podem ser adicionados a uma imagem de mapa estático utilizando o parâmetro `path` no URL. O parâmetro `path` leva um estilo e uma lista de locais a serem prestados no mapa, como mostrado abaixo:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Estilos adicionais podem ser usados adicionando parâmetros adicionais `path` ao URL com um estilo e conjunto diferente de locais.

As localizações de caminhos no Google Maps são especificadas com o formato `latitude1,longitude1|latitude2,longitude2|…`. Os caminhos podem ser codificados ou conter endereços para pontos.

Os estilos de percurso no Google Maps são adicionados com o formato `optionName:value`, com vários estilos separados por caracteres de tubos (\|). Assim: `optionName1:value1|optionName2:value2`. Observe que os nomes e valores de opção são separados por dois-pontos (:). Os seguintes nomes de opção de estilo podem ser usados para caminhos de estilo no Google Maps:

- `color` – A cor do caminho ou do contorno do polígono. Pode ser uma cor hexana de 24 bits (`0xrrggbb`), uma cor hexaça de 32 bits (`0xrrggbbbaa`) ou um dos seguintes valores: preto, castanho, verde, roxo, amarelo, azul, cinza, laranja, vermelho, branco.
- `fillColor` – A cor para preencher a área do caminho com (polígono). Pode ser uma cor hexana de 24 bits (`0xrrggbb`), uma cor hexaça de 32 bits (`0xrrggbbbaa`) ou um dos seguintes valores: preto, castanho, verde, roxo, amarelo, azul, cinza, laranja, vermelho, branco.
- `geodesic` – Indica se o caminho deve ser uma linha que segue a curvatura da terra.
- `weight` – A espessura da linha do caminho em píxeis.

No Google Maps, uma opacidade de linha vermelha e espessura de pixel soa podem ser adicionadas ao mapa entre as coordenadas, no parâmetro URL. Para o exemplo abaixo, a linha tem uma opacidade de 50% e uma espessura de quatro pixels. As coordenadas são de longitude: -110, latitude: 45 e longitude: -100, latitude: 50.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![](media/migrate-google-maps-web-services/google-maps-polyline.png)</center> poliline do Google Maps

**Depois: Mapas Azure**

No Azure Maps, adicione linhas e polígonos a uma imagem de mapa estático, especificando o parâmetro `path` no URL. Tal como o Google Maps, especifique um estilo e uma lista de localizações neste parâmetro. Especifique o parâmetro `path` várias vezes para renderizar vários círculos, linhas e polígonos com diferentes estilos.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

No que diz respeito aos locais de caminho, o Azure Maps exige que as coordenadas estejam em formato de "latitude longitude". O Google Maps usa o formato "latitude, longitude". Um espaço, não uma vírposta, separa a longitude e a latitude no formato Azure Maps. O Azure Maps não suporta caminhos codificados ou endereços para pontos. Faça upload de conjuntos de dados maiores como um GeoJSON preenche na API de armazenamento de dados do Azure Maps como documentado [aqui](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

No Azure Maps, adicione estilos de caminho com o formato `optionNameValue`. Separe vários estilos por tubo (\|) caracteres como este `optionName1Value1|optionName2Value2`. Os nomes e valores das opções não estão separados. Utilize os seguintes nomes de opção de estilo para estilo saqueem os mapas do Azure:

- `fa` - A opacidade da cor de preenchimento (alfa) utilizada na renderização de polígonos. Escolha um número entre 0 e 1.
- `fc` - A cor de enchimento utilizada para tornar a área de um polígono.
- `la` – A opacidade da cor da linha (alfa) utilizada na renderização das linhas e no contorno dos polígôonos. Escolha um número entre 0 e 1.
- `lc` – A cor da linha utilizada para renderizar linhas e o contorno dos polígonos.
- `lw` – A largura da linha em píxeis.
- `ra` – Especifica um raio de círculos em metros.

No Azure Maps, adicione uma opacidade de linha vermelha e espessura de pixel entre as coordenadas, no parâmetro URL. Para o exemplo abaixo, a linha tem 50% de opacidade e uma espessura de quatro pixels. As coordenadas têm os seguintes valores: longitude: -110, latitude: 45 e longitude: -100, latitude: 50.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure Maps](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center> poliline

## <a name="calculate-a-distance-matrix"></a>Calcular uma matriz de distância

O Azure Maps fornece a matriz de distância API. Utilize esta API para calcular os tempos de viagem e as distâncias entre um conjunto de locais, com uma matriz de distância. É comparável à matriz de distância API no Google Maps.

- [**Matriz**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)de rota : Calcula assincronicamente os tempos e distâncias de viagem para um conjunto de origens e destinos. Suporta até 700 células por pedido (o número de origens multiplicado pelo número de destinos. Com essa restrição em mente, exemplos de dimensões de matriz possíveis são: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Uma solicitação para a API de matriz de distância só pode ser feita usando uma solicitação POST com as informações de origem e destino no corpo da solicitação. Além disso, o Azure Maps requer que todas as origens e destinos sejam coordenadas. Os endereços precisarão ser geocodificados primeiro.

A tabela que se segue cruza as referências aos parâmetros DaPI do Google Maps com os parâmetros API do Azure Maps comparáveis.

| Parâmetro da API do Google Maps      | Parâmetro de API comparável do Azure Maps  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` – especificar no organismo de pedido post como GeoJSON. |
| `key`                          | `subscription-key` – Consulte também a Autenticação com documentação [do Azure Maps.](azure-maps-authentication.md) |
| `language`                     | `language` – Ver documentação de [línguas suportadas.](supported-languages.md)  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` – especificar no organismo de pedido post como GeoJSON.  |
| `region`                       | *N/A* – Esta funcionalidade está relacionada com geocodificação. Utilize o parâmetro `countrySet` quando utilizar a API de geocodificação Do Azure Maps. |
| `traffic_model`                | *N/A* – Só pode especificar se os dados de tráfego devem ser utilizados com o parâmetro `traffic`. |
| `transit_mode`                 | *N/A* - As matrizes de distância baseadas em trânsito não são suportadas atualmente.  |
| `transit_routing_preference`   | *N/A* - As matrizes de distância baseadas em trânsito não são suportadas atualmente.  |
| `units`                        | *N/A* – O Azure Maps utiliza apenas o sistema métrico. |

> [!TIP]
> Todas as opções avançadas de encaminhamento disponíveis na API de encaminhamento do Azure Maps são suportadas na matriz de distância Azure Maps API. Opções avançadas de encaminhamento incluem: encaminhamento de ruck, especificações do motor, e assim por diante.

## <a name="get-a-time-zone"></a>Obter um fuso horário

O Azure Maps fornece uma API para recuperar o fuso horário de uma coordenada. A API de fuso horário do Azure Maps é comparável à API de fuso horário no Google Maps:

- [**Fuso horário por coordenada**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Especifique uma coordenada e receba os detalhes do fuso horário da coordenada.

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros de API comparáveis no Azure Maps.

| Parâmetro da API do Google Maps | Parâmetro de API comparável do Azure Maps   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – Consulte também a Autenticação com documentação [do Azure Maps.](azure-maps-authentication.md)       |
| `language`                  | `language` – Ver documentação de [línguas suportadas.](supported-languages.md)    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Além desta API, a plataforma Azure Maps fornece uma série de APIs do fuso horário. Estas APIs convertem o tempo com base nos nomes ou iDs do fuso horário:

- [**Fuso horário por ID:** ](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid)Devoluções informações atuais, históricas e futuras do fuso horário para o ID do fuso horário IANA especificado.
- [**Fuso horário Enum IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): Devolve uma lista completa de IDs do fuso horário IANA. As atualizações para o serviço IANA são refletidas no sistema dentro de um dia.
- [**Fuso horário Enum Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): Devolve uma lista completa de IDs do Fuso Horário do Windows.
- [**Versão IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion)do fuso horário : Devolve o número atual da versão IANA utilizado pelo Azure Maps.
- [**Fuso horário Windows to IANA:** ](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana)Devolve um ID IANA correspondente, dado um ID de Fuso Horário do Windows válido. Várias IDs do IANA podem ser retornadas para uma única ID do Windows.

## <a name="client-libraries"></a>Bibliotecas de cliente

O mapas do Azure fornece bibliotecas de cliente para as seguintes linguagens de programação:

- JavaScript, TypeScript, Node.js – [documentação](how-to-use-services-module.md) \| [pacote NPM](https://www.npmjs.com/package/azure-maps-rest)

Bibliotecas de clientes de código aberto para outras línguas de programação:

- .NET Standard 2.0 – [Projeto GitHub](https://github.com/perfahlen/AzureMapsRestServices) \| [pacote NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>Recursos adicionais

Seguem-se algumadocumentação e recursos adicionais para os serviços de REST do Azure Maps.

- [Boas práticas de pesquisa](how-to-use-best-practices-for-search.md)
- [Procurar um endereço](how-to-search-for-address.md)
- [Documentação de referência da API do serviço de repouso Azure Maps](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os serviços REST do Azure Maps.

> [!div class="nextstepaction"]
> [Boas práticas para usar o serviço de pesquisa](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Como utilizar o módulo de serviços (Web SDK)](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)