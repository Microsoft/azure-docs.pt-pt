---
title: Glossário de mapas do Azure | Documentos da Microsoft
description: Um glossário dos termos utilizados frequentemente associados mapas do Azure, Location Based Services e GIS.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 11e49d362508e9720cf65fc20e020930b5f5c3ad
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775038"
---
# <a name="glossary"></a>Glossário

Segue-se uma lista de palavras comuns utilizadas com o Azure Maps.

## <a name="a"></a>A

<a name="address-validation"></a> **Validação de endereço**: O processo de verificar a existência de um endereço.

<a name="advanced-routing"></a> **Encaminhamento avançado**: Uma coleção de serviços que realizam operações de avanço com dados de encaminhamento de trânsito, como calcular intervalos acessíveis (isócranas), as matrizes de distância e pedidos de rota do batch.

<a name="aerial-imagery"></a> **Imagens aéreas**: Ver [imagens de satélite](#satellite-imagery). 

<a name="along-a-route-search"></a> **Ao longo de uma pesquisa de rota**: Uma consulta geográfica que procura por dados que estão dentro de um desvio especificado tempo ou distância a partir de um caminho de rota.

<a name="altitude"></a> **Altitude**: A altura ou a elevação vertical de um ponto de acima uma superfície de referência. Medições de altitude baseiam-se a datum uma determinada referência, como o nível do mar médio. Consulte também a elevação.

<a name="ambiguous"></a> **Ambígua**: Um Estado de incerteza na classificação de dados existe quando um objeto adequadamente pode ser atribuído dois ou mais valores para um atributo específico. Por exemplo, quando geocodificação "CA" duas ambíguos são devolvidos resultados; "Canadá" e "Califórnia" como "CA" é um código de estado e país para cada um, respetivamente. 

<a name="annotation"></a> **Anotação**: Texto ou de gráficos apresentados no mapa para fornecer informações ao usuário. Anotação poderá identificar descrevem uma entidade de mapa específico, fornecer informações gerais sobre uma área do mapa ou fornecer informações sobre o mapa em si.

<a name="antimeridian"></a> **Antimeridian**: Também conhecido como a 180<sup>th</sup> dos meridianos é o ponto em que cumprem os graus de-180 e 180 graus de longitude. Qual é a oposta do meridiano em todo o mundo.

<a name="application-programming-interface-api"></a> **Interface de programação de aplicações (API)**: Uma especificação que permite aos desenvolvedores criar aplicativos.

<a name="api-key"></a> **Chave de API**: Ver a chave de mapas do Azure.

<a name="area-of-interest-aoi"></a> **Área de interesse (AOI)**: A extensão usada para definir uma área de foco para um mapa ou a base de dados de produção.

<a name="asset-tracking"></a> **Controle de ativos**: O processo de controlar a localização de um ativo, como uma pessoa, veículo ou algum outro objeto.

<a name="asynchronous-request"></a> **Solicitação assíncrona**: Um pedido HTTP que abre uma conexão e faz um pedido para o servidor que retorna um identificador para a solicitação assíncrona, em seguida, fecha a ligação. O servidor continua a processar o pedido e o utilizador pode verificar o estado com o identificador. Quando o pedido é terminou o processamento, o utilizador pode, em seguida, a resposta de download. Este tipo de pedido é normalmente utilizado de longa duração processos em execução.

<a name="autocomplete"></a> **Preenchimento automático**: Um recurso num aplicativo prevê o restante de uma palavra que um usuário está digitando. 

<a name="autosuggest"></a> **Sugestão automática**: Um recurso num aplicativo a prevê lógicas possibilidades para o que o usuário está digitando.

<a name="azure-location-based-services-lbs"></a> **O Azure Location Based Services (LBS)**: O nome antigo do Azure Maps quando estava em pré-visualização.

<a name="azure-maps-key"></a> **A chave do Azure Maps**: Uma chave de mapas do Azure é uma cadeia exclusiva que é utilizada para autenticar a aplicação do Azure Maps de um utilizador ou pedido de serviço. 

## <a name="b"></a>B

<a name="base-map"></a> **Mapa base**: A parte de um aplicativo de mapa que apresenta informações de referência do plano de fundo como estradas, pontos de referência e cruzar fronteiras políticas.

<a name="batch-request"></a> **Pedido de lote**: O processo de combinar várias solicitações num único pedido.

<a name="bearing"></a> **Tendo**: A direção horizontal de um ponto em relação a outro ponto. Isso é expresso como um ângulo em relação ao norte, de graus de 0 a 360-graus no sentido horário. 

<a name="boundary"></a> **Limite**: Uma linha ou um polígono separar adjacentes entidades de políticas, como países, escolas e propriedades. Um limite é uma linha que podem ou não poderá seguir características físicas, como rios, montanhas ou paredes.

<a name="bounds"></a> **Limites**: Ver [Bounding caixa](#bounding-box).

<a name="bounding-box"></a> **Caixa delimitadora**: Um conjunto de coordenadas usado para representar uma área retangular no mapa. 

## <a name="c"></a>C

<a name="cadastre"></a> **Cadastre**: Um registo de ' s land registado e propriedades. Consulte também [Parcel](#parcel).

<a name="camera"></a> **Câmara**: No contexto de um controle de mapas interativa, uma câmera define o campo de vista do maps. O visor da câmara é determinado com base em vários parâmetros de mapa; Centro, nível de zoom, pitch, efeito. 

<a name="centroid"></a> **Centróide**: O Centro de geométrico de um recurso. O centroide de uma linha seria o ponto médio, enquanto o centroide de um polígono seria seu centro da área.

<a name="choropleth-map"></a> **Mapa de Coropleto**: Um mapa temática em que áreas estão sombreadas em proporção de uma medida de uma variável de estatística que está a ser apresentada no mapa. Por exemplo, o limite de cada Estado dos Estados Unidos com base na sua população relativa a todos os outros Estados de cores.

<a name="concave-hull"></a> **Casco côncavo**: Uma forma que representa uma geometria de côncavo possíveis que engloba todas as formas no conjunto de dados especificado. A forma gerada é semelhante ao encapsulamento os dados com moldagem de plástica e, em seguida, aquecê-lo, ocasionando portanto grandes abrange entre pontos para cave para outros pontos de dados.

<a name="consumption-model"></a> **Modelo de consumo**: Informações de que define a taxa a que um veículo consome eletricidade ou de combustível. Consulte também os [documentação sobre o modelo de consumo](consumption-model.md).

<a name="control"></a> **Controle**: Um componente autônomo ou reutilizável consistindo numa interface gráfica do usuário que define um conjunto de comportamentos para a interface. Por exemplo, um controle de mapa, geralmente é a parte da interface do usuário que carrega um mapa interativo.

<a name="convex-hull"></a> **Casco convexo**: Um casco convexo é uma forma que representa a geometria convexo mínimo que engloba todas as formas no conjunto de dados especificado. A forma gerada é semelhante a uma banda elástico em todo o conjunto de dados de encapsulamento de aplicações.

<a name="coordinate"></a> **Coordenar**: É composta pelos valores de longitude e latitude usados para representar uma localização num mapa.

<a name="coordinate-system"></a> **Sistema de coordenadas**: Uma estrutura de referência utilizada para definir as posições dos pontos no espaço em duas ou três dimensões.

<a name="country-code"></a> **Indicativo de país**: Um identificador exclusivo para um país com base no padrão ISO. ISO2 é um código de dois caracteres para um país (por exemplo, EUA), que representa ISO3 três caracteres de código (por exemplo, EUA).

<a name="country-subdivision"></a> **Subdivisões do país**: Uma subdivisão de primeiro nível de um país, comumente conhecido como um Estado ou província.

<a name="country-secondary-subdivision"></a> **Subdivisões secundário do país**: Uma subdivisão de segundo nível de um país, comumente conhecido como um CONDADO.

<a name="country-tertiary-subdivision"></a> **Subdivisões terciária do país**: Uma subdivisão de terceiro nível de um país, normalmente, uma área nomeada como um ward.

<a name="cross-street"></a> **Rua entre**: Um ponto em que dois ou mais streets intersect.

<a name="cylindrical-projection"></a> **Projeção cilíndrica**: Uma projeção que transforma os pontos de um spheroid ou esfera para um cilindro de tangente, ou secant. O cilindro, em seguida, é segmentado de cima para baixo e nivelado um plano.

## <a name="d"></a>D

<a name="datum"></a> **Datum**: As especificações de referência de um sistema de medição, posiciona de um sistema de coordenadas numa superfície (uma datum horizontal) ou alturas acima ou abaixo de uma superfície (uma datum vertical).

<a name="dbf-file"></a> **Ficheiro DBF**: Um formato de ficheiro de base de dados que é utilizado em combinação com ficheiros de formas (SHP).

<a name="degree-minutes-seconds-dms"></a> **Grau minutos e segundos (DMS)**: A unidade de medida para descrever a latitude e longitude. Um grau é 1/360<sup>th</sup> de um círculo. Um grau é dividido em 60 minutos e um minuto é dividido em 60 segundos.

<a name="delaunay-triangulation"></a> **Triangulação de Delaunay**: Uma técnica para criar uma malha de triângulos contínuos, não sobrepostas a partir de um conjunto de dados de pontos. Círculo de cada triângulo circumscribing contém sem pontos do conjunto de dados no seu interior.

<a name="demographics"></a> **Dados demográficos**: As características de estatísticas (por exemplo, idade, taxa de nascimento e receitas) de uma população humana.

<a name="destination"></a> **Destino**: Um ponto final ou a localização em que alguém está em uma viagem para.

<a name="digital-elevation-model-dem"></a> **Modelo de elevação digital (DEM)**: Um conjunto de dados de elevação valores relacionados numa superfície, capturadas ao longo de uma área em intervalos regulares através de uma datum comuns. DEMs são tipicamente usadas para representar o alívio terreno.

<a name="dijkstra's-algorithm"></a> **Algoritmo do Dijkstra '**: Um algoritmo que examina a conectividade de uma rede para localizar o caminho mais curto entre dois pontos.

<a name="distance-matrix"></a> **Matriz de distância**: Uma matriz que contém informações de tempo e a distância de viagem entre um conjunto de origens e destinos. 

## <a name="e"></a>E

<a name="elevation"></a> **Elevação**: A distância vertical de um ponto ou um objeto acima ou abaixo de uma superfície de referência ou datum (nível do mar geralmente mean). Elevação normalmente refere-se à altura vertical da terra.

<a name="envelope"></a> **Envelope**: Ver [Bounding caixa](#bounding-box).

<a name="extended-postal-code"></a> **Expandido código postal**: Um código postal, que pode incluir informações adicionais. Por exemplo, nos EUA, códigos postais ter cinco dígitos, mas um código postal expandido, conhecido como de CEP + 4, irá incluir quatro dígitos adicionais. Esses dígitos adicionais são utilizados para identificar um segmento geográfico dentro da área de entrega com cinco dígitos, como um bloco de cidade, um grupo de apartamentos ou uma caixa de office post, que auxilia na classificação de correio eficiente e entrega.

<a name="extent"></a> **Extent**: Ver [Bounding caixa](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a> **Autenticação federada**: Um método de autenticação que permite que um mecanismo de autenticação/início de sessão único para ser utilizada em várias aplicações web e móveis. 

<a name="feature"></a> **Funcionalidade**: Um objeto que combina um geometry com uma informação de metadados adicionais. 

<a name="feature-collection"></a> **Coleção de recursos**: Uma coleção de objetos de recurso.

<a name="find-along-route"></a> **Encontrar ao longo da rota**: Uma consulta geográfica que procura por dados que estão dentro de um desvio especificado tempo ou distância a partir de um caminho de rota.

<a name="find-nearby"></a> **Encontrar próximos**: Uma consulta geográfica que procura uma distância em linha reta fixa (como a crow vôo) de um ponto.

<a name="fleet-management"></a> **Da frota gestão**: A gestão dos veículos comerciais, como carros, autocarros, é fornecido e planos. Gestão de frotas pode incluir uma variedade de funções, como veículo financeiras, manutenção, telemática (rastreio e diagnóstico), bem como gerenciamento de driver, velocidade, combustível e o estado de funcionamento e segurança. Gestão de frotas é um processo usado por empresas que dependem de transportes os negócios para minimizar os riscos e reduzir seus custos globais de transportes e funcionários, garantindo a conformidade com a legislação de governo.

<a name="free-flow-speed"></a> **Gratuito a velocidade do fluxo de**: A velocidade do fluxo livre esperada em condições ideais. Normalmente, o limite de velocidade.

<a name="free-form-address"></a> **Endereço de forma livre**: Endereço completo que é representado como uma única linha de texto.

<a name="fuzzy-search"></a> **A pesquisa difusa**: Uma pesquisa que aceita uma cadeia de caracteres de forma livre de texto que pode ser um endereço ou ponto de interesse. 

## <a name="g"></a>G

<a name="geocode"></a> **Geocode**: Um endereço ou a localização que foi convertida numa coordenada que pode ser utilizada para apresentar essa localização num mapa. 

<a name="geocoding"></a> **Geocoding**: Também conhecido como encaminhamento geocodificação, é o processo de conversão de endereço de dados de localização em coordenadas. 

<a name="geodesic-path"></a> **Caminho geodesic**: O caminho mais curto entre dois pontos numa superfície curva. Quando compostos no Azure Maps este caminho é apresentado como uma linha curva devido a projeção Mercator.

<a name="geofence"></a> **Geofence**: Uma região geográfica definida que pode ser utilizada para acionar eventos quando um dispositivo entra ou existe a região.

<a name="geojson"></a> **GeoJSON**: É um formato comum de arquivo baseados em JSON utilizado para armazenar dados de vetor geográfica, como pontos, linhas e polígonos. **Nota**: Mapas do Azure utiliza uma versão expandida do GeoJSON como [documentadas aqui](extend-geojson.md).

<a name="geometry"></a> **Geometry**: Representa um objeto espacial como um ponto, uma linha ou um polígono.

<a name="geometrycollection"></a> **GeometryCollection**: Uma coleção de objetos de geometria.

<a name="geopol"></a> **GeoPol**: Refere-se aos dados geopolitically confidenciais, como limites questionados e nomes de locais.

<a name="georeference"></a> **Georeference**: O processo de alinhamento de dados geográficos ou imagens para um conhecido sistema de coordenadas. Este processo pode consistir em mudança, girar, dimensionar ou distorcer os dados.

<a name="georss"></a> **GeoRSS**: Uma extensão XML para adicionar dados geográficos RSS feeds.

<a name="gis"></a> **GIS**: Um acrônimo de "Sistema de informações geográficas". Um termo comum usado para descrever o setor de mapeamento.

<a name="gml"></a> **GML**: Também conhecida como linguagem de marcação de geografia. Uma extensão de arquivo XML para armazenar dados geográficos.

<a name="gps"></a> **GPS**: Também conhecido como sistema de Posicionamento Global, é um sistema de satélites utilizadas para determinar uma posição de dispositivos da terra. Os satélites orbiting transmitem sinais que permitem que um GPS recetor em qualquer lugar na terra calcular a sua própria localização por meio de trilateration.

<a name="gpx"></a> **GPX**: Também conhecido como formato de troca GPS, um formato de arquivo XML normalmente criado a partir de dispositivos GPS.  

<a name="great-circle-distance"></a> **Distância de grande ciclo**: A distância mais curta entre dois pontos na superfície de uma esfera.

<a name="greenwich-mean-time-gmt"></a> **Hora de Greenwich (GMT)**: O tempo em que o primeiro meridiano, que percorre a Royal Observatory na Greenwich, Inglaterra.

<a name="guid"></a> **GUID**: Um identificador exclusivo global. Uma cadeia utilizada para identificar exclusivamente uma interface, classe, biblioteca de tipos, categoria de componente ou registo.

## <a name="h"></a>H

<a name="haversine-formula"></a> **Fórmula Haversine**: Uma equação comum usada para calcular a distância de grande ciclo entre dois pontos numa esfera.

<a name="hd-maps"></a> **Mapas de HD**: Também conhecido como alta de definição de mapas, consiste em informações de rede de trânsito de alta fidelidade, como marcas de lane, signage e luzes direção necessários para orientar autónomo.

<a name="heading"></a> **Cabeçalho**: A direção que algo está apontando ou enfrentar. Consulte também [tendo](#heading).

<a name="heatmap"></a> **Heatmap**: Uma visualização de dados em que um intervalo de cores representam a densidade de pontos numa determinada área. Consulte também o mapa temática.

<a name="hybrid-imagery"></a> **Imagens híbridas**: Satélite ou imagens aéreas com dados de estrada e etiquetas sobrepostas sobre ele.

## <a name="i"></a>I

<a name="iana"></a> **IANA**: Um acrônimo para a autoridade de números atribuídos da Internet. Um grupo sem fins lucrativos que supervisiona global alocação de endereços IP.

<a name="isochrone"></a> **Isochrone**: Um isochrone define a área em que alguém pode viajam num período de tempo especificado para um modo de transporte em qualquer direção de uma localização especificada. Consulte também [intervalo acessível](#reachable-range).

<a name="isodistance"></a> **Isodistance**: Devido uma localização, um isochrone define a área em que alguém pode ser transportado dentro de uma distância especificada para um modo de transporte em qualquer direção. Consulte também [intervalo acessível](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a> **KML**: Também conhecido como linguagem de marcação de Keyhole, é um formato de arquivo XML comuns para armazenar dados de vetor geográfica, como pontos, linhas e polígonos. 

## <a name="l"></a>L

<a name="landsat"></a> **Landsat**: Satélites multispectral, orbiting de terra, desenvolvidos pela NASA que recolha imagens de ' s land, que é utilizado em vários setores, tais como agricultura, silvicultura e cartografia.

<a name="latitude"></a> **Latitude**: A distância angular medido em graus a partir do Equador num Norte ou direção Centro-Sul.

<a name="level-of-detail"></a> **Nível de detalhe**: Consulte o Zoom nível.

<a name="lidar"></a> **Lidar**: Acrônimo de deteção de luz e que vão. Uma técnica de leitura de remoto que utiliza fantásticos para medir a distâncias para superfícies reflexiva.

<a name="linear-interpolation"></a> **Interpolação linear**: A estimativa de um valor desconhecido com a distância linear entre os valores conhecidos.

<a name="linestring"></a> **LineString**: Uma geometria usada para representar uma linha. Também conhecido como uma polilinha. 

<a name="localization"></a> **Localização**: Suporte para diferentes idiomas e culturas.

<a name="logistics"></a> **Logística**: O processo de mover as pessoas, veículos, suprimentos ou ativos de uma forma coordenada.

<a name="longitude"></a> **Longitude**: A distância angular medido em graus a partir do meridiano numa direção Leste ou oeste.

## <a name="m"></a>M

<a name="map-tile"></a> **Mosaico do mapa**: Uma imagem retangular que representa uma partição de uma tela de mapa. Para obter mais informações, consulte a [níveis de Zoom e documentação de grelha de mosaico](zoom-levels-and-tile-grid.md).

<a name="marker"></a> **Marker**: Também conhecido como um pin ou uma pushpin, é um ícone que representa uma localização do ponto num mapa.

<a name="mercator-projection"></a> **Projeção Mercator**: Uma projeção de mapa cilíndrico tornou-se a projeção de mapa padrão para fins de nautical devido à sua capacidade para representar as linhas de curso constante, conhecido como linhas rhumb, como segmentos retas que conservam os ângulos com os meridianos. Todas as projeções de mapa simples distorçam as formas ou tamanhos do mapa quando comparado com o layout VERDADEIRO de superfície da terra. A projeção Mercator exaggerates áreas longe de ser o Equador, de forma a que o menores áreas apareçam maiores no mapa, como aborda os poles. 

<a name="multilinestring"></a> **MultiLineString**: Geometry que representa uma coleção de objetos de LineString. 

<a name="multipoint"></a> **MultiPoint**: Geometry que representa uma coleção de objetos Point.

<a name="multipolygon"></a> **MultiPolygon**: Geometry que representa uma coleção de objetos do polígono. Por exemplo, para mostrar os limites do Havai, cada ilha poderia ser descrita com um polígono e limites do Havai, portanto, seria um MultiPolygon.

<a name="municipality"></a> **Município**: Uma cidade ou uma cidade. 

<a name="municipality-subdivision"></a> **Subdivisões município**: Uma subdivisão de um município, como uma vizinhança ou o nome de área local, como de "direção".

## <a name="n"></a>N

<a name="navigation-bar"></a> **Barra de navegação**: O conjunto de controles num mapa para ajustar o nível de zoom, o argumento de venda, a rotação e mudar a camada de mapa base.

<a name="nearby-search"></a> **Nas proximidades de pesquisa**: Uma consulta geográfica que procura uma distância em linha reta fixa (como a crow vôo) de um ponto.

<a name="neutral-ground-truth"></a> **Neutralidade zero verdade**: Um mapa que processa as etiquetas no idioma oficial da região representa e, em scripts locais se estiver disponível.

## <a name="o"></a>O

<a name="origin"></a> **Origin**: Um ponto de início ou a localização em que se um utilizador.

## <a name="p"></a>P

<a name="panning"></a> **Movimento panorâmico**: O processo de mover o mapa em qualquer direção, mantendo um nível de zoom constante.

<a name="parcel"></a> **Parcel**: Um desenho de limite de ' s land ou propriedade.

<a name="pitch"></a> **Pitch**: A quantidade de bem o mapa tem em relação ao vertical em que 0 é observar direto para o mapa.

<a name="point"></a> **Point**: Geometry que representa uma única posição no mapa. 

<a name="points-of-interest-poi"></a> **Pontos de interesse (POI)**: Um negócio, ponto de referência ou lugar comum de interesse.

<a name="polygon"></a> **Polygon**: Uma geometria de sólido que representa uma área num mapa. 

<a name="polyline"></a> **Polyline**: Uma geometria usada para representar uma linha. Também conhecido como LineString. 

<a name="position"></a> **Posição**: A longitude, latitude e altitude (x, y, z coordenadas) de um ponto.

<a name="post-code"></a> **Publicar código**: Ver [caixa postal](#postal-code).

<a name="postal-code"></a> **Código postal**: Uma série de letras ou números ou ambas, num formato específico, utilizado pelo serviço postal de um país para dividir áreas geográficas em zonas para simplificar a entrega de correio.

<a name="prime-meridian"></a> **Meridiano**: Uma linha de longitude que representa 0 graus de longitude. Geralmente, os valores de longitude diminuir quando em transição numa direção westerly até 180 graus e aumentar quando estiverem em deslocação nas direções easterly para -180-graus. 

<a name="prj"></a> **PRJ**: Um arquivo de texto que, muitas vezes, acompanha um arquivo de ficheiro de formas que contém informações sobre o sistema de coordenadas projetada o conjunto de dados está em.

<a name="projection"></a> **Projeção**: Um sistema de coordenadas projetado com base numa projeção de mapa como Mercator atravessa as, Albers igual a área e Robinson. Eles fornecem a capacidade de mapas de projeto da superfície da terra esférica num plano bidimensional cartesianas coordenada. Sistemas de coordenadas projetados são, às vezes, referidos como projeções de mapa.

## <a name="q"></a>P

<a name="quadkey"></a> **Quadkey**: Um índice de endereço base-4 para um mosaico dentro de um sistema de lado a lado quadtree. Para obter mais informações, consulte [níveis de Zoom e grelha de mosaico](zoom-levels-and-tile-grid.md) documentação para obter mais informações.

<a name="quadtree"></a> **Quadtree**: Uma estrutura de dados em que cada nó tem exatamente quatro filhos. O sistema de lado a lado utilizado no Azure Maps utiliza uma estrutura de quadtree, de modo a que como um utilizador amplia num nível, cada mosaico de mapa divide em quatro mosaicos secundários.  Para obter mais informações, consulte [níveis de Zoom e grelha de mosaico](zoom-levels-and-tile-grid.md) documentação para obter mais informações.

<a name="queries-per-second-qps"></a> **Consultas por segundo (QPS)**: O número de consultas ou pedidos que podem ser feitos a um serviço ou a plataforma de um segundo. 

## <a name="r"></a>R

<a name="radial-search"></a> **Pesquisa Radial**: Uma consulta geográfica que procura uma distância em linha reta fixa (como a crow vôo) de um ponto. 

<a name="raster-data"></a> **Dados de varredura**: Uma matriz de células (ou em pixéis) organizadas em linhas e colunas (ou uma grade) em que cada célula contém um valor que representa informações, tais como temperatura. Em padrão incluem digital fotografias aéreas, imagens de satélites, imagens digitais e mapas digitalizados.

<a name="raster-layer"></a> **Camada de varredura**: Uma camada de mosaicos que consiste em quadrícula imagens.

<a name="reachable-range"></a> **Intervalo acessível**: Um intervalo acessível define a área em que alguém pode ser transportado dentro de um período de tempo especificado ou a distância, para um modo de transporte de viajar, em qualquer direção a partir de uma localização. Consulte também [Isochrone](#isochrone) e [Isodistance](#isodistance).

<a name="remote-sensing"></a> **Leitura remota**: O processo de coleta e a interpretação de dados do sensor à distância.

<a name="rest-service"></a> **Serviço REST**: O acrônimo REST significa Representational transferência de estado. Um serviço REST é um serviço web baseado em URL que se baseia na tecnologia da web básico para comunicar, os métodos mais comuns que está a ser pedidos HTTP GET e POST. Esses tipos de serviços tendem a mim muito mais rápido e mais pequeno do que os tradicionais serviços baseados em SOAP.

<a name="reverse-geocode"></a> **Inverter geocode**: Representa o processo de colocar uma coordenada e determinar o endereço no qual está num mapa.

<a name="reproject"></a> **Reproject**: Ver [transformação](#transformation).

<a name="rest-service"></a> **Serviço REST**: Acrônimo do Representational State Transfer. Uma arquitetura para a troca de informações entre itens de mesmo nível num ambiente distribuído, descentralizado. REST permite que os programas em computadores diferentes comunicar, independentemente de um sistema operativo ou plataforma ao enviar um pedido de protocolo HTTP (Hypertext Transfer) para um localizador de recurso uniforme (URL) e obter dados.

<a name="route"></a> **Route**: Um caminho entre dois ou mais localizações, que também pode incluir informações adicionais, como instruções para marcos ao longo da rota.

<a name="requests-per-second-rps"></a> **Pedidos por segundo (RPS)**: Ver [consultas por segundo (QPS)](#queries-per-second-qps). 

<a name="rss"></a> **RSS**: Acrônimo de Really Simple Syndication, resumo de Site do Framework de descrição do recurso (RDF) ou Rich Site resumo, dependendo da origem. Um simples e estruturado formato XML para a partilha de conteúdo entre sites diferentes. Os documentos RSS incluem elementos de metadados chave, como o autor, data, título, uma breve descrição e um link de hipertexto. Esta ajuda de informações que um utilizador (ou um serviço de publicador do RSS) decidir o que materiais valem a pena ainda mais investigação.

## <a name="s"></a>S

<a name="satellite-imagery"></a> **Imagens de satélite**: Imagem for capturada, planos e satélites apontar diretamente para baixo.

<a name="software-development-kit-sdk"></a> **O kit de desenvolvimento de software (SDK)**: Uma coleção de documentação, código de exemplo e aplicações de exemplo para ajudar a uma utilização de programador uma API para criar aplicações.

<a name="shapefile-shp"></a> **Ficheiro de formas (. SHP)**: Também conhecido como um ficheiro de formas ESRI, é um formato de armazenamento de dados de vetor para armazenar a localização, forma e atributos de recursos geográficos. Um ficheiro de formas é armazenado num conjunto de arquivos relacionados.

<a name="spherical-mercator-projection"></a> **Projeção de Mercator esférica**: Ver [Web Mercator](#web-mercator). 

<a name="spatial-query"></a> **Consulta geográfica**: Um pedido efetuado para um serviço que executa uma operação espacial. Tal como uma pesquisa radial ou ao longo de uma pesquisa de rota.

<a name="spatial-reference"></a> **Referência espacial**: Um com base em coordenadas, regional, sistema local ou global utilizado para localizar precisamente entidades geográficas. Define o sistema de coordenadas usado para relacionar as coordenadas do mapa para locais no mundo real. Referências geográficos Certifique-se de que os dados geográficos de diferentes camadas ou origens podem ser integrados para visualização exata ou análise. O Azure utiliza mapas a [EPSG:3857](https://epsg.io/3857) coordenar o sistema de referência e WGS 84 para dados de geometria de entrada. 

<a name="sql-spatial"></a> **SQL geográfico**: Refere-se para a funcionalidade de geográfica incorporada no SQL Azure e SQL Server 2008 e superior. Esta funcionalidade espacial também está disponível como uma biblioteca .NET que pode ser usada de forma independente do SQL Server. Para obter mais informações, consulte a [documentação de dados geográficos (SQL Server)](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) para obter mais informações.

<a name="synchronous-request"></a> **Solicitação síncrona**: Um pedido de HTTP abre uma conexão e aguarda uma resposta. Browsers limitam o número de pedidos HTTP simultâneos que podem ser feitas a partir de uma página. Se várias solicitações síncronas de longa execução são feitas ao mesmo tempo, pode ser atingido este limite e pedidos de atraso até que uma das outras solicitações seja concluída.

## <a name="t"></a>T

<a name="telematics"></a> **Telemática**: Enviar e receber para armazenar informações através de dispositivos de telecomunicação em conjunto com tornam efetivas controle em objetos remotos. 

<a name="temporal-data"></a> **Dados temporais**: Dados que se refere-se especificamente a tempos ou datas. Dados temporais podem referir-se a eventos discretos, como palestras strikes; Mover objetos, tais como trens; ou observações repetidas, tais como contagens de sensores de tráfego.

<a name="terrain"></a> **Terrain**: Uma área de terra ter uma característica específica, como o terreno sandy ou mountainous terreno.

<a name="thematic-maps"></a> **Mapas temáticas**: Um mapa temática é um mapa simple feito para refletir um tema sobre uma área geográfica. É um cenário comum para este tipo de mapa para as regiões administrativas, como países com base em algumas métrica de dados de cores.

<a name="tile-layer"></a> **Camada de mosaicos**: Uma camada apresentada com a montagem de mosaicos de mapa (retangulares secções) para uma camada contínua. Os mosaicos são ambos quadrícula mosaicos de imagem ou vetoriais mosaicos. A camadas em mosaico em padrão normalmente é processada antes do tempo e armazenada como imagens num servidor. Isso pode consumir muito espaço de armazenamento. A camadas em mosaico vetor é processada em tempo real no aplicativo cliente, portanto, os requisitos de armazenamento do lado do servidor são menores.

<a name="time-zone"></a> **Fuso horário**: Uma região de todo o mundo que observa uma hora uniforme de standard para fins legais, comerciais e sociais. Fusos horários tendem a seguir os limites de países e seus subdivisões.

<a name="transaction"></a> **Transação**: Mapas do Azure utiliza um modelo de licenciamento transacional onde;

- É criada uma transação para todos os 15 mosaicos de mapa ou tráfego solicitados.
- É criada uma transação para cada chamada de API para um dos serviços do Azure Maps, por exemplo, pesquisa ou de encaminhamento.

<a name="transformation"></a> **Transformação**: O processo de conversão de dados entre sistemas de coordenadas geográficos diferentes. Pode, por exemplo, ter alguns dados que foi capturados no Reino Unido e com base no sistema de coordenadas geográficas OSGB 1936. O Azure utiliza mapas a [EPSG:3857](https://epsg.io/3857) variante de sistema de coordenadas de referência do WGS84. Como tal, para exibir os dados corretamente, será necessário ter suas coordenadas transformadas a partir de um sistema para outro.

<a name="traveling-salesmen-problem-tsp"></a> **Problema do Caixeiro Salesmen (TSP)**:  Um problema de circuito Hamiltonian em que um vendedor tem de encontrar a maneira mais eficiente para visitar uma série de paradas, em seguida, volte para a localização inicial.  

<a name="trilateration"></a> **Trilateration**: O processo de determinar a posição de um ponto na superfície da terra em relação a dois outros pontos, medindo as distâncias entre todos os três pontos.

<a name="turn-by-turn-navigation"></a> **Ative a folheio navegação**: A próxima manobras se aproxima de uma aplicação que fornece instruções de rota de cada passo de uma rota como os utilizadores.

## <a name="v"></a>V

<a name="vector-data"></a> **Vetoriais dados**: Dados em que são representados como polígonos, linhas ou pontos de coordenada.

<a name="vector-tile"></a> **Mosaico de vetor**: Uma especificação de dados abertos para armazenar dados de vetor geoespacial utilizando o mesmo sistema de mosaicos como o controlo do mapa. Consulte também [camada de mosaicos](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a> **Problema de roteamento de veículos (VRP)**: Uma classe de problemas em que um conjunto de rotas ordenados para uma frota de veículos é calculado ao levar em consideração como conjunto de restrições. Essas restrições podem incluir algo como janelas de tempo de entrega, vários recursos de rota e as restrições de duração de viagem.

<a name="voronoi-diagram"></a> **Diagrama de Voronoi**: Uma partição de espaço em áreas ou células, que envolvem um conjunto de objetos geométricos (normalmente, funcionalidades de ponto). Essas células ou polígonos, tem de cumprir os critérios para os triângulos de Delaunay. Todas as localizações dentro de uma área estão mais próximos o objeto a que rodeie para qualquer outro objeto no conjunto. Diagramas de Voronoi, muitas vezes, são usados para delinear a áreas de influência dos recursos do geográficas. 

## <a name="w"></a>W

<a name="waypoint"></a> **Waypoint**: Um marco é uma localização geográfica especificada definida por longitude e latitude, que é utilizada para fins de navegação. Muitas vezes, utilizado para representar um ponto em que alguém navega uma rota por meio de.

<a name="waypoint-optimization"></a> **Otimização do marco**: O processo de reordenação de um conjunto de marcos para minimizar o tempo ou de distância de viagem deve para passar por todos os marcos fornecidos. Muitas vezes referido como o [problema do Caixeiro Salesmen](#traveling-salesmen-problem-tsp) ou [problema de roteamento de veículos](#vehicle-routing-problem-vrp) dependendo da complexidade da otimização.

<a name="web-map-service-wms"></a> **Mapa de serviço (WMS) Web**: WMS é um padrão aberto geográfica Consortium (OGC) que define os serviços com base em imagem do mapa. Serviços WMS fornecem imagens de mapa para áreas específicas dentro de um mapa a pedido. As imagens incluem previamente processada Simbologia de barras e podem ser processadas em um dos vários estilos com nome, se definidas pelo serviço.

<a name="web-mercator"></a> **Web Mercator**: Também conhecido como esférica Mercator projeção é uma pequena variante da projeção Mercator, um usados principalmente em programas de mapeamento baseado na Web. Ela usa as mesmas fórmulas como a projeção Mercator padrão utilizado para mapas de pequena escala. No entanto, as utilizações de Web Mercator as fórmulas esféricas em todos os dimensiona enquanto Mercator em grande escala mapeia normalmente use o formulário elipsoidais da projeção. A discrepância é imperceptível à escala global, mas faz com que os mapas do locais áreas para desviar um pouco do verdadeiro que Mercator elipsoidais mapeia na mesma escala.

<a name="wgs84"></a> **WGS84**: Um conjunto de constantes usadas para relacionar coordenadas geográficos para localizações na superfície do mapa. O datum WGS84 é o padrão utilizadas por mais fornecedores de mapeamento online e dispositivos GPS. O Azure utiliza mapas a [EPSG:3857](https://epsg.io/3857) variante de sistema de coordenadas de referência do WGS84.

## <a name="z"></a>Z

<a name="z-coordinate"></a> **Coordenada Z**: Ver [Altitude](#altitude). 

<a name="zip-code"></a> **Código postal**: Ver [caixa postal](#postal-code).

<a name="Zoom level"></a> **Nível de zoom**: Especifica o nível de detalhe e quanto do mapa está visível. Quando reduzido a um nível 0, o mapa-mundo completa será sempre na vista, mas irá mostrar detalhes limitados, como nomes de países e bordas e como e nomes de Oceano. Quando ampliado mais próximo para o nível 17, o mapa será apresentada uma área de alguns blocos de cidade com informações detalhadas de viagem. Para obter mais informações, consulte a [níveis de Zoom e grelha de mosaico](zoom-levels-and-tile-grid.md) documentação.

