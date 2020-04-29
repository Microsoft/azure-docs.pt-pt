---
title: 'Tutorial: Migrar serviços web do Google Maps Microsoft Azure Maps'
description: Como migrar os serviços web do Google Maps para o Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: d2f25f2b786686b8af9bad4ea8ce3c8aea9b589f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80371466"
---
# <a name="migrate-web-service-from-google-maps"></a>Migrar serviço web do Google Maps

Tanto o Azure como o Google Maps fornecem acesso a APIs espaciais através de serviços web REST. As interfaces API destas plataformas realizam funcionalidades semelhantes. Mas cada um usa diferentes convenções de nomeação e objetos de resposta.

A tabela mostra as APIs do serviço Azure Maps, que têm uma funcionalidade semelhante à listada do serviço Google Maps APIs.

| Serviço Google Maps API | Serviço Azure Maps API                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Direções              | [Encaminhar](https://docs.microsoft.com/rest/api/maps/route)                                     |
| Matriz de Distância         | [Matriz da Rota](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)       |
| Geocodificação               | [Procurar](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Pesquisa de locais           | [Procurar](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Lugar Autocompleto      | [Procurar](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Snap to Road            | Consulte a secção [de rotas e direções.](#calculate-routes-and-directions)            |
| Limites de velocidade            | Consulte [o geocódigo inverso numa](#reverse-geocode-a-coordinate) secção de coordenadas.                  |
| Mapa estático              | [Composição](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                       |
| Fuso Horário               | [Fuso horário](https://docs.microsoft.com/rest/api/maps/timezone)                              |

As seguintes APIs de serviço não estão atualmente disponíveis no Azure Maps:

- Elevação
- Geolocalização
- Locais detalhes e fotos - Números de telefone e URL do site estão disponíveis na Pesquisa Azure Maps API.
- URLs de mapa
- Estradas Mais Próximas - Isto é alcançável usando o Web SDK como mostrado [aqui](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic
), mas não disponível como um serviço atualmente.
- Vista estática de rua

O Azure Maps dispõe de vários serviços web REST adicionais que podem ser do interesse:

- [Operações espaciais](https://docs.microsoft.com/rest/api/maps/spatial): Descarregar cálculos e operações espaciais complexos, como geofencing, para um serviço.
- [Tráfego](https://docs.microsoft.com/rest/api/maps/traffic): Aceder ao fluxo de tráfego em tempo real e aos dados de incidentes.

## <a name="geocoding-addresses"></a>Endereços de geocodificação

A geocodificação é o processo de conversão de um endereço numa coordenada. Por exemplo, "1 Microsoft way, Redmond, WA" converte-se em longitude: -122.1298, latitude: 47.64005. Em seguida, as coordenadas podem ser usadas para diferentes tipos de propósitos, tais como, posicionando um marcador centralnum mapa.

O Azure Maps fornece vários métodos para endereços de geocodificação:

- [**Geocodificação de endereços gratuitos**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): Especifique uma única cadeia de endereços e processe imediatamente o pedido. "1 Microsoft way, Redmond, WA" é um exemplo de uma única cadeia de endereços. Esta API é recomendada se precisar de geocodificar endereços individuais rapidamente.
- [**Gestão de endereços estruturados**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): Especifique as partes de um único endereço, como o nome de rua, cidade, país e código postal e processe imediatamente o pedido. Esta API é recomendada se precisar de geocodificar endereços individuais rapidamente e os dados já estiverem analisados nas suas partes de endereço individuais.
- [**Geocodificação de endereços**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview)de lote : Crie um pedido que contenha até 10.000 endereços e processe-os durante um período de tempo. Todos os endereços serão geocodificados em paralelo no servidor e quando concluído o conjunto completo de resultados pode ser descarregado. Isto é recomendado para geocodificar grandes conjuntos de dados.
- [**Pesquisa fuzzy**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Esta API combina gecodificação de endereços com procura de ponto de interesse. Esta API leva uma corda em forma livre. Esta cadeia pode ser uma categoria de endereço, lugar, marco, ponto de interesse ou ponto de interesse. Esta API processa o pedido em tempo real. Esta API é recomendada para aplicações onde os utilizadores procuram endereços ou pontos de interesse na mesma caixa de texto.
- [**Pesquisa de lotes fuzzy**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Crie um pedido que contenha até 10.000 endereços, lugares, marcos ou pontode interesses e processe-os durante um período de tempo. Todos os dados serão processados paralelamente no servidor e quando concluídos o conjunto completo de resultados pode ser descarregado.

A tabela que se segue cruza as referências aos parâmetros da API do Google Maps com os parâmetros Comparáveis da API no Azure Maps.

| Parâmetro API do Google Maps | Parâmetro API De Mapas Azure comparáveis  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` e `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality`- cidade/cidade<br/>`municipalitySubdivision`– bairro, sub/super cidade<br/>`countrySubdivision`- estado ou província<br/>`countrySecondarySubdivision`- condado<br/>`countryTertiarySubdivision`- distrito<br/>`countryCode`- duas letras código de país |
| `key`                       | `subscription-key`– Consulte também a Autenticação com documentação [do Azure Maps.](azure-maps-authentication.md) |
| `language`                  | `language`– Consulte documentação de [línguas suportadas.](supported-languages.md)  |
| `region`                    | `countrySet`                       |

Um exemplo de como utilizar o serviço de pesquisa é documentado [aqui](how-to-search-for-address.md). Certifique-se de rever [as melhores práticas de pesquisa.](how-to-use-best-practices-for-search.md)

> [!TIP]
> As APIs de geocodificação de endereços gratuitos e de `&typeahead=true` pesquisa difusa podem ser utilizadas no modo autocompleto adicionando ao URL de pedido. Isto dirá ao servidor que o texto de entrada é provavelmente parcial e que a pesquisa entrará em modo preditivo.

## <a name="reverse-geocode-a-coordinate"></a>Reverter geocódigo uma coordenada

Geocodificação inversa é o processo de conversão de coordenadas geográficas num endereço aproximado. Coordenadas com "longitude: -122.1298, latitude: 47.64005" convertem-se em "1 Microsoft Way, Redmond, WA".

O Azure Maps fornece vários métodos de geocodificação inversa:

- [**Endereço geocodificador inverso**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): Especifique uma única coordenada geográfica para obter o endereço aproximado correspondente a esta coordenada. Processa o pedido em tempo real.
- [**Cross street reverse geocoder**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): Especifique uma única coordenada geográfica para obter informações de cross street nas proximidades e processe imediatamente o pedido. Por exemplo, pode receber as seguintes ruas cruzadas 1ª Ave e Main St.
- Endereço de [**lote geocodificador inverso**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): Crie um pedido contendo até 10.000 coordenadas e processe-as durante um período de tempo. Todos os dados serão processados paralelamente no servidor. Quando o pedido estiver concluído, pode descarregar o conjunto completo de resultados.

Esta tabela cruza referências aos parâmetros DaPI do Google Maps com os parâmetros Comparáveis da API no Azure Maps.

| Parâmetro API do Google Maps   | Parâmetro API De Mapas Azure comparáveis   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key`– Consulte também a Autenticação com documentação [do Azure Maps.](azure-maps-authentication.md) |
| `language`                  | `language`– Consulte documentação de [línguas suportadas.](supported-languages.md)  |
| `latlng`                    | `query`  |
| `location_type`             | *N/D*     |
| `result_type`               | `entityType`    |

Reveja [as melhores práticas de pesquisa.](how-to-use-best-practices-for-search.md)

O Azure Maps inverte a geocodificação API tem algumas funcionalidades adicionais, que não estão disponíveis no Google Maps. Estas funcionalidades podem ser úteis para integrar com a sua aplicação, uma vez que migra a sua aplicação:

- Recuperar dados do limite de velocidade
- Recuperar informações de utilização da estrada: estrada local, arterial, acesso limitado, rampa, e assim por diante
- Recupere o lado da rua onde está localizada uma coordenada

## <a name="search-for-points-of-interest"></a>Procurar pontos de interesse

Os dados de ponto de interesse podem ser pesquisados no Google Maps utilizando a API de Pesquisa de Lugares. Esta API fornece três formas diferentes de procurar pontos de interesse:

- **Encontre lugar a partir do texto:** Procura um ponto de interesse com base no seu nome, endereço ou número de telefone.
- **Pesquisa nas proximidades**: Procura pontos de interesse que estejam a uma certa distância de um local.
- **Pesquisa de texto:** Pesquisas por locais usando um texto de formulário livre, que inclui informações sobre ponto de interesse e localização. Por exemplo, "pizza em Nova Iorque" ou "restaurantes perto da rua principal".

O Azure Maps fornece várias APIs de pesquisa para pontos de interesse:

- [**Pesquisa POI**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): Procure pontos de interesse saem pelo nome. Por exemplo, "Starbucks".
- [**Pesquisa da categoria POI**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): Procurar pontos de interesse por categoria. Por exemplo, "restaurante".
- [**Pesquisa nas proximidades**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): Procura pontos de interesse que estejam a uma certa distância de um local.
- [**Pesquisa fuzzy**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Esta API combina gecodificação de endereços com procura de ponto de interesse. Esta API tem uma cadeia de forma livre que pode ser um endereço, lugar, marco, ponto de interesse ou categoria de ponto de interesse. Processa o pedido em tempo real. Esta API é recomendada para aplicações onde os utilizadores procuram endereços ou pontos de interesse na mesma caixa de texto.
- [**Pesquisa dentro da geometria**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): Procure pontos de interesses que estejam dentro de uma geometria especificada. Por exemplo, procure um ponto de interesse dentro de um polígono.
- [**Pesquisa ao longo do percurso**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): Procure pontos de interesses que estejam ao longo de um percurso específico.
- [**Pesquisa de lotes fuzzy**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Crie um pedido que contenha até 10.000 endereços, lugares, marcos ou ponto de interesse. Processou o pedido durante um período de tempo. Todos os dados serão processados paralelamente no servidor. Quando o pedido completa o processamento, pode descarregar o conjunto completo do resultado.

Atualmente o Azure Maps não tem uma API comparável à API de pesquisa de texto no Google Maps.

> [!TIP]
> A pesquisa poi, a pesquisa da categoria POI e as APIs `&typeahead=true` de pesquisa difusa podem ser usadas no modo autocompleto adicionando ao URL de pedido. Isto dirá ao servidor que o texto de entrada é provavelmente parcial. A API conduzirá a pesquisa em modo preditivo.

Reveja as [melhores práticas para a](how-to-use-best-practices-for-search.md) documentação de pesquisa.

### <a name="find-place-from-text"></a>Encontre lugar a partir do texto

Utilize a [pesquisa POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) do Azure Maps e [a pesquisa fuzzy](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) para procurar pontos de interesse si mesmos pelo nome ou endereço.

A tabela cruza as referências à API do Google Maps com os parâmetros comparáveis da API do Azure Maps.

| Parâmetro API do Google Maps | Parâmetro API De Mapas Azure comparáveis |
|---------------------------|-------------------------------------|
| `fields`                  | *N/D*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/D*                               |
| `key`                     | `subscription-key`– Consulte também a Autenticação com documentação [do Azure Maps.](azure-maps-authentication.md) |
| `language`                | `language`– Consulte documentação de [línguas suportadas.](supported-languages.md)  |
| `locationbias`            | `lat`, `lon` e`radius`<br/>`topLeft` e `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Pesquisa nas proximidades

Utilize a API [de pesquisa nas proximidades](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) para recuperar pontos de interesse nas proximidades, no Azure Maps.

A tabela mostra os parâmetros DaPi do Google Maps com os parâmetros comparáveis da API do Azure Maps.

| Parâmetro API do Google Maps | Parâmetro API De Mapas Azure comparáveis  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key`– Consulte também a Autenticação com documentação [do Azure Maps.](azure-maps-authentication.md) |
| `keyword`                   | `categorySet` e `brandSet`        |
| `language`                  | `language`– Consulte documentação de [línguas suportadas.](supported-languages.md)  |
| `location`                  | `lat` e `lon`                     |
| `maxprice`                  | *N/D*                               |
| `minprice`                  | *N/D*                               |
| `name`                      | `categorySet` e `brandSet`        |
| `opennow`                   | *N/D*                               |
| `pagetoken`                 | `ofs` e `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/D*                               |
| `type`                      | `categorySet –`Consulte a documentação das categorias de [pesquisa suportada.](supported-search-categories.md)   |

## <a name="calculate-routes-and-directions"></a>Calcular rotas e direções

Calcular rotas e direções utilizando o Azure Maps. O Azure Maps tem muitas das mesmas funcionalidades que o serviço de encaminhamento do Google Maps, tais como:

- Horários de chegada e partida.
- Rotas de tráfego em tempo real e preditivas.
- Diferentes modos de transporte. Como conduzir, andar, andar de bicicleta.

> [!NOTE]
> O Azure Maps requer todos os pontos de passagem para serem coordenadas. Os endereços devem ser geocodificados primeiro.

O serviço de encaminhamento Azure Maps fornece as seguintes APIs para o cálculo das rotas:

- [**Calcular a rota**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): Calcular uma rota e processar o pedido imediatamente. Esta API suporta tanto os pedidos do GET como do POST. Os pedidos post são recomendados ao especificar um grande número de pontos de passagem ou quando se utilizam muitas das opções de rota para garantir que o pedido de URL não se torne demasiado longo e cause problemas. A Direção de Rota POST no Azure Maps tem uma opção que pode acolher milhares de [pontos](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints) de apoio e irá usá-los para recriar uma rota lógica entre eles (encaixe na estrada). 
- [**Rota do lote**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): Crie um pedido que contenha até 1.000 pedidos de rota e processe-os durante um período de tempo. Todos os dados serão processados paralelamente no servidor e quando concluídos o conjunto completo de resultados pode ser descarregado.
- [**Serviços de mobilidade**](https://docs.microsoft.com/rest/api/maps/mobility): Calcular rotas e direções utilizando o trânsito público.

A tabela cruza as referências à API do Google Maps com os parâmetros Comparáveis da API no Azure Maps.

| Parâmetro API do Google Maps    | Parâmetro API De Mapas Azure comparáveis  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query`– coordenadas no formato`"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key`– Consulte também a Autenticação com documentação [do Azure Maps.](azure-maps-authentication.md) |
| `language`                     | `language`– Consulte documentação de [línguas suportadas.](supported-languages.md)   |
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
> Por padrão, a rota Azure Maps API apenas devolve um resumo. Devolve a distância e os horários e as coordenadas para o percurso. Utilize `instructionsType` o parâmetro para recuperar as instruções de turn-by-turn. E, use `routeRepresentation` o parâmetro para filtrar o resumo e o caminho da rota.

A API de encaminhamento do Azure Maps tem funcionalidades adicionais, que não estão disponíveis no Google Maps. Ao migrar a sua aplicação, considere utilizar estas funcionalidades, podendo achá-las úteis.

- Suporte para o tipo de rota: mais curto, rápido, trilling e mais eficiente em termos de combustível.
- Suporte para modos de viagem adicionais: autocarro, moto, táxi, caminhão e carrinha.
- Suporte para 150 pontos de passagem.
- Calcular vários tempos de viagem num único pedido; tráfego histórico, tráfego ao vivo, sem tráfego.
- Evite tipos de estradas adicionais: estradas de carpool, estradas não pavimentadas, estradas já utilizadas.
- Especifique áreas personalizadas para evitar.
- Limite a elevação, que a rota pode subir.
- Rota com base nas especificações do motor. Calcular rotas para veículos de combustão ou elétricos com base nas especificações do motor e o combustível ou carga restantes.
- Apoiar os parâmetros da rota do veículo comercial. Tais como dimensões do veículo, peso, número de eixos e tipo de carga.
- Especifique a velocidade máxima do veículo.

Além disso, o serviço de rotas no Azure Maps suporta o cálculo de [gamas de encaminhamento.](https://docs.microsoft.com/rest/api/maps/route/getrouterange) Calcular gamas de encaminhamento também é conhecido como isochrones. Implica gerar um polígono cobrindo uma área que pode ser percorrida em qualquer direção a partir de um ponto de origem. Tudo sob um determinado período de tempo ou quantidade de combustível ou carga.

Reveja as melhores práticas para a documentação [de encaminhamento.](how-to-use-best-practices-for-routing.md)

## <a name="retrieve-a-map-image"></a>Recuperar uma imagem do mapa

O Azure Maps fornece uma API para renderizar as imagens do mapa estático com dados sobrepostos. A [imagem do Mapa renderiza](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) API no Azure Maps é comparável ao mapa estático API no Google Maps.

> [!NOTE]
> O Azure Maps exige que o centro, todos os marcadores, e os locais do caminho sejam coordenados em formato de "longitude, latitude". Enquanto que o Google Maps usa o formato "latitude, longitude". Os endereços terão de ser geocodificados primeiro.

A tabela cruza as referências à API do Google Maps com os parâmetros Comparáveis da API no Azure Maps.

| Parâmetro API do Google Maps | Parâmetro API De Mapas Azure comparáveis  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format`– especificado como parte do caminho url. Atualmente apenas o PNG suportado. |
| `key`                       | `subscription-key`– Consulte também a Autenticação com documentação [do Azure Maps.](azure-maps-authentication.md) |
| `language`                  | `language`– Consulte documentação de [línguas suportadas.](supported-languages.md)  |
| `maptype`                   | `layer`e `style` – Ver documentação de [estilos de mapas suportados.](supported-map-styles.md) |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N/A* – Esta é uma característica relacionada com geocodificação. Utilize `countrySet` o parâmetro quando utilizar a API de geocodificação Do Azure Maps.  |
| `scale`                     | *N/D*                              |
| `size`                      | `width`e `height` – pode ter até 8192x8192 de tamanho. |
| `style`                     | *N/D*                              |
| `visible`                   | *N/D*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> No sistema de azulejos Azure Maps, os azulejos têm o dobro do tamanho de azulejos usados no Google Maps. Como tal, o valor de zoom no Azure Maps aparecerá um nível de zoom mais próximo no Azure Maps em comparação com o Google Maps. Para compensar esta diferença, decreie o nível de zoom nos pedidos que está a migrar.

Para mais informações, consulte o [guia como orientar na imagem do mapa render API](how-to-render-custom-data.md).

Além de ser capaz de gerar uma imagem de mapa estático, o serviço de renderização Do Azure Maps fornece a capacidade de aceder diretamente a azulejos de mapas em formato de raster (PNG) e vetor:

- [**Azulejo do mapa:**](https://docs.microsoft.com/rest/api/maps/render/getmaptile)Recuperar raster (PNG) e azulejos vetoriais para os mapas base (estradas, limites, fundo).
- [**Azulejos de imagens de mapas:**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)Recupere azulejos de imagens aéreas e de satélite.

> [!TIP]
> Muitas aplicações do Google Maps onde passaram de experiências de mapas interativos para imagens de mapas estáticos há alguns anos. Isto foi feito como um método de poupança de custos. No Azure Maps, é geralmente mais rentável utilizar o controlo interativo do mapa no Web SDK. As cargas interativas de controlo do mapa basearam o número de cargas de azulejos. Os azulejos do mapa em Mapas Azure são grandes. Muitas vezes, são precisos apenas alguns azulejos para recriar a mesma vista de mapa que um mapa estático. Os azulejos do mapa são automaticamente cached pelo navegador. Como tal, o controlo interativo do mapa gera frequentemente uma fração de uma transação ao reproduzir uma visão estática do mapa. Panorâmica e zooming carregarão mais azulejos; no entanto, existem opções no controlo do mapa para desativar este comportamento. O controlo interativo do mapa também fornece muito mais opções de visualização do que os serviços de mapas estáticos.

### <a name="marker-url-parameter-format-comparison"></a>Comparação de formato de parâmetro de URL do marcador

**Antes: Google Maps**

Adicione marcadores `markers` utilizando o parâmetro no URL. O `markers` parâmetro leva um estilo e uma lista de locais a serem renderizados no mapa com esse estilo, como mostrado abaixo:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Para adicionar estilos `markers` adicionais, use os parâmetros para o URL com um estilo e conjunto diferente de locais.

Especifique os locais do marcador com o formato "latitude, longitude".

Adicione os estilos de marcador com o `optionName:value` \|formato, com vários estilos\|separados por caracteres de tubo ( ) como este "optionName1:value1 optionName2:value2". Note que os nomes e valores da opção são separados com um cólon (:). Utilize os seguintes nomes da opção de estilo para marcar marcadores de estilo no Google Maps:

- `color`– A cor do ícone do marcador predefinido. Pode ser uma cor hexaque de 24 bits`0xrrggbb`() ou um dos seguintes valores; `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label`– Um único personagem alfanumérico superior para exibir em cima do ícone.
- `size`- O tamanho do marcador. Pode `tiny`ser, `mid` `small`ou .

Utilize os seguintes nomes de opções de estilo para ícones personalizados no Google Maps:

- `anchor`– Especifica como alinhar a imagem do ícone à coordenada. Pode ser um valor pixel (x,y) ou um dos seguintes valores; `top`, `bottom` `right` `center` `topleft` `topright` `bottomleft`, , , , , ou `bottomright` `left`
- `icon`– Um URL que aponta para a imagem do ícone.

Por exemplo, vamos adicionar um marcador vermelho e de tamanho médio ao mapa em longitude: -110, latitude: 45:

```
&markers=color:red|size:mid|45,-110
```

<center>

![Marcador do Google Maps](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**Depois: Mapas Azure**

Adicione marcadores a uma imagem de `pins` mapa estático especificando o parâmetro no URL. Tal como o Google Maps, especifique um estilo e uma lista de localizações no parâmetro. O `pins` parâmetro pode ser especificado várias vezes para suportar marcadores com diferentes estilos.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Para utilizar estilos `pins` adicionais, adicione parâmetros adicionais ao URL com um estilo e conjunto diferente de locais.

No Azure Maps, a localização dos pinos tem de estar no formato "longitude latitude". O Google Maps usa o formato "latitude, longitude". Um espaço, não uma vírposta, separa a longitude e a latitude no formato Azure Maps.

O `iconType` especifica o tipo de pino para criar. Pode ter os seguintes valores:

- `default`– O ícone do pino predefinido.
- `none`– Não é apresentado nenhum ícone, apenas serão renderizadas etiquetas.
- `custom`– Especifica que deve ser utilizado um ícone personalizado. Um URL que aponta para a imagem do `pins` ícone pode ser adicionado à extremidade do parâmetro após a informação de localização do pino.
- `{udid}`– Um ID de dados único (UDID) para um ícone armazenado na plataforma de armazenamento de dados do Azure Maps.

Adicione os estilos pin com o `optionNameValue` formato. Separe vários estilos com os caracteres do tubo.\| Por exemplo: `iconType|optionName1Value1|optionName2Value2`. Os nomes e valores das opções não estão separados. Utilize os seguintes nomes de opção de estilo para marcadores de estilo:

- `al`– Especifica a opacidade (alfa) do marcador. Escolha um número entre 0 e 1.
- `an`– Especifica a âncora do pino. Especifique os valores x e y pixel no formato "x y".
- `co`– A cor do pino. Especifique uma cor hexada de 24 bits: `000000` para `FFFFFF`.
- `la`– Especifica a âncora do rótulo. Especifique os valores x e y pixel no formato "x y".
- `lc`– A cor do rótulo. Especifique uma cor hexada de 24 bits: `000000` para `FFFFFF`.
- `ls`– O tamanho da etiqueta em píxeis. Escolha um número maior que 0.
- `ro`– Um valor em graus para rodar o ícone. Escolha um número entre -360 e 360.
- `sc`– Um valor de escala para o ícone do pino. Escolha um número maior que 0.

Especifique os valores de etiqueta para cada localização do pino. Esta abordagem é mais eficiente do que aplicar um único valor de etiqueta a todos os marcadores na lista de locais. O valor do rótulo pode ser uma série de vários caracteres. Embrulhe a corda com citações únicas para garantir que não é confundida como um estilo ou valor de localização.

Vamos adicionar um ícone`FF0000`predefinido vermelho , com a etiqueta "Space Needle", posicionada abaixo (15 50). O ícone está em longitude: -122.349300, latitude: 47.620180:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Marcador do Azure Maps](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

Adicione três pinos com os valores do rótulo '1', '2' e '3':

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Azure Maps vários marcadores](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Comparação de formato de parâmetro de URL path

**Antes: Google Maps**

Adicione linhas e polígono a `path` uma imagem de mapa estático utilizando o parâmetro no URL. O `path` parâmetro leva um estilo e uma lista de locais a serem prestados no mapa, como mostrado abaixo:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Utilize estilos adicionais adicionando parâmetros adicionais `path` ao URL com um estilo e conjunto diferentes de locais.

As localizações dos caminhos são especificadas com o `latitude1,longitude1|latitude2,longitude2|…` formato. Os caminhos podem ser codificados ou conter endereços para pontos.

Adicione estilos `optionName:value` de caminho com o formato, separe vários estilos pelos caracteres do tubo.\| E, nomes e valores de opção separados com um cólon (:). Assim: `optionName1:value1|optionName2:value2`. Os seguintes nomes de opção de estilo podem ser usados para estilo de caminhos no Google Maps:

- `color`– A cor do contorno do caminho ou do polígono. Pode ser uma cor hexana de 24 bits`0xrrggbb``0xrrggbbbaa`( ), uma cor hexaça de 32 bits ( ) ou um dos seguintes valores: preto, marrom, verde, roxo, amarelo, azul, cinza, laranja, vermelho, branco.
- `fillColor`– A cor para preencher a área do caminho com (polígono). Pode ser uma cor hexana de 24 bits`0xrrggbb``0xrrggbbbaa`( ), uma cor hexaça de 32 bits ( ) ou um dos seguintes valores: preto, marrom, verde, roxo, amarelo, azul, cinza, laranja, vermelho, branco.
- `geodesic`– Indica se o caminho deve ser uma linha que segue a curvatura da terra.
- `weight`– A espessura da linha do caminho em píxeis.

Adicione uma opacidade de linha vermelha e espessura de pixel ao mapa entre as coordenadas, no parâmetro URL. Para o exemplo abaixo, a linha tem uma opacidade de 50% e uma espessura de quatro pixels. As coordenadas são de longitude: -110, latitude: 45 e longitude: -100, latitude: 50.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Poliline do Google Maps](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**Depois: Mapas Azure**

Adicione linhas e polígonos a uma imagem `path` de mapa estático especificando o parâmetro no URL. Tal como o Google Maps, especifique um estilo e uma lista de localizações neste parâmetro. Especifique o `path` parâmetro várias vezes para renderizar vários círculos, linhas e polígonos com diferentes estilos.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

No que diz respeito aos locais de caminho, o Azure Maps exige que as coordenadas estejam em formato de "latitude longitude". O Google Maps usa o formato "latitude, longitude". Um espaço, não uma vírposta, separa a longitude e a latitude no formato Azure Maps. O Azure Maps não suporta caminhos codificados ou endereços para pontos. Faça upload de conjuntos de dados maiores como um ficheiro GeoJSON na API de Armazenamento de Dados do Azure Maps, conforme documentado [aqui](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

Adicione estilos `optionNameValue` de caminho com o formato. Separe vários\|estilos por `optionName1Value1|optionName2Value2`caracteres de tubo, como este. Os nomes e valores das opções não estão separados. Utilize os seguintes nomes de opção de estilo para estilo saqueem os mapas do Azure:

- `fa`- A opacidade da cor de enchimento (alfa) utilizada na renderização de polígonos. Escolha um número entre 0 e 1.
- `fc`- A cor de enchimento usada para tornar a área de um polígono.
- `la`– A opacidade da cor da linha (alfa) utilizada na renderização das linhas e no contorno dos polígôonos. Escolha um número entre 0 e 1.
- `lc`– A cor da linha utilizada para renderizar linhas e o contorno dos políginos.
- `lw`– A largura da linha em píxeis.
- `ra`– Especifica um raio de círculos em metros.

Adicione uma opacidade de linha vermelha e espessura de pixel entre as coordenadas, no parâmetro URL. Para o exemplo abaixo, a linha tem 50% de opacidade e uma espessura de quatro pixels. As coordenadas têm os seguintes valores: longitude: -110, latitude: 45 e longitude: -100, latitude: 50.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Poliline Azure Maps](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Calcular uma matriz de distância

O Azure Maps fornece a matriz de distância API. Utilize esta API para calcular os tempos de viagem e as distâncias entre um conjunto de locais, com uma matriz de distância. É comparável à matriz de distância API no Google Maps.

- [**Matriz**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)de rota : Calcula assincronicamente os tempos e distâncias de viagem para um conjunto de origens e destinos. Suporta até 700 células por pedido. É o número de origens multiplicadas pelo número de destinos. Com esse constrangimento em mente, exemplos de possíveis dimensões matriciais são: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Um pedido à matriz de distância API só pode ser feito através de um pedido post com a origem e informações de destino no corpo do pedido. Além disso, o Azure Maps requer que todas as origens e destinos sejam coordenadas. Os endereços terão de ser geocodificados primeiro.

Esta tabela cruza referências aos parâmetros DaPI do Google Maps com os parâmetros comparáveis da API do Azure Maps.

| Parâmetro API do Google Maps      | Parâmetro API De Mapas Azure comparáveis  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination`– especificar no organismo de pedido de CORREIO como GeoJSON. |
| `key`                          | `subscription-key`– Consulte também a Autenticação com documentação [do Azure Maps.](azure-maps-authentication.md) |
| `language`                     | `language`– Consulte documentação de [línguas suportadas.](supported-languages.md)  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins`– especificar no organismo de pedido de CORREIO como GeoJSON.  |
| `region`                       | *N/A* – Esta funcionalidade está relacionada com geocodificação. Utilize `countrySet` o parâmetro quando utilizar a API de geocodificação Do Azure Maps. |
| `traffic_model`                | *N/A* – Só pode especificar se os `traffic` dados de tráfego devem ser utilizados com o parâmetro. |
| `transit_mode`                 | *N/A* - As matrizes de distância baseadas em trânsito não são suportadas atualmente.  |
| `transit_routing_preference`   | *N/A* - As matrizes de distância baseadas em trânsito não são suportadas atualmente.  |
| `units`                        | *N/A* – O Azure Maps utiliza apenas o sistema métrico. |

> [!TIP]
> Todas as opções avançadas de encaminhamento disponíveis na API de encaminhamento do Azure Maps são suportadas na matriz de distância Azure Maps API. Opções avançadas de encaminhamento incluem: encaminhamento de caminhão, especificações do motor, e assim por diante.

Reveja as melhores práticas para a documentação [de encaminhamento.](how-to-use-best-practices-for-routing.md)

## <a name="get-a-time-zone"></a>Obter um fuso horário

O Azure Maps fornece uma API para recuperar o fuso horário de uma coordenada. O fuso horário Azure Maps API é comparável ao fuso horário API no Google Maps:

- [**Fuso horário por coordenada**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Especifique uma coordenada e receba os detalhes do fuso horário da coordenada.

Esta tabela cruza referências aos parâmetros DaPI do Google Maps com os parâmetros Comparáveis da API no Azure Maps.

| Parâmetro API do Google Maps | Parâmetro API De Mapas Azure comparáveis   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key`– Consulte também a Autenticação com documentação [do Azure Maps.](azure-maps-authentication.md)       |
| `language`                  | `language`– Consulte documentação de [línguas suportadas.](supported-languages.md)    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Além desta API, o Azure Maps fornece uma série de APIs do fuso horário. Estas APIs convertem o tempo com base nos nomes ou iDs do fuso horário:

- [**Fuso horário por ID:**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid)Devoluções informações atuais, históricas e futuras do fuso horário para o ID do fuso horário IANA especificado.
- [**Fuso horário Enum IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): Devolve uma lista completa de IDs do fuso horário IANA. As atualizações para o serviço IANA refletem-se no sistema no prazo de um dia.
- [**Fuso horário Enum Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): Devolve uma lista completa de IDs do Fuso Horário do Windows.
- [**Versão IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion)do fuso horário : Devolve o número atual da versão IANA utilizado pelo Azure Maps.
- [**Fuso horário Windows to IANA:**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana)Devolve um ID IANA correspondente, dado um ID de Fuso Horário do Windows válido. Vários IDs IANA podem ser devolvidos para um único ID do Windows.

## <a name="client-libraries"></a>Bibliotecas de cliente

O Azure Maps fornece bibliotecas de clientes para as seguintes línguas de programação:

- JavaScript, TypeScript, Node.js – [pacote nPM](https://www.npmjs.com/package/azure-maps-rest) de [documentação](how-to-use-services-module.md) \|

Estas bibliotecas de clientes de código aberto são para outras línguas de programação:

- .NET Standard 2.0 – [GitHub project](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>Recursos adicionais

Seguem-se documentação e recursos adicionais para os serviços de REST do Azure Maps.

- [Melhores práticas de pesquisa](how-to-use-best-practices-for-search.md)
- [Pesquisar por um endereço](how-to-search-for-address.md)
- [Boas práticas para o encaminhamento](how-to-use-best-practices-for-routing.md)
- [Documentação de referência da API do serviço de repouso Azure Maps](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os serviços de REST do Azure Maps.

> [!div class="nextstepaction"]
> [Boas práticas para usar o serviço de pesquisa](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Boas práticas para usar o serviço de encaminhamento](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Como utilizar o módulo de serviços (Web SDK)](how-to-use-best-practices-for-routing.md)

> [!div class="nextstepaction"]
> [Amostras de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)
