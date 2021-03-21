---
title: Azure Maps Glossário | Microsoft Docs
description: Um glossário de termos comumente usados associados a Azure Maps, Location-Based Services e GIS.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 05071be4fc1705787a649d331613e8d9399f983f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92897182"
---
# <a name="glossary"></a>Glossário

A lista que se segue descreve palavras comuns usadas com os serviços Azure Maps.

## <a name="a"></a>A

<a name="address-validation"></a>**Validação do endereço**: O processo de verificação da existência de um endereço.

<a name="advanced-routing"></a>**Encaminhamento avançado**: Recolha de serviços que efetuam operações antecipadas utilizando dados de encaminhamento rodoviário; tais como, calcular as gamas alcançáveis (isochrones), matrizes de distância e pedidos de rota de lote.

<a name="aerial-imagery"></a>**Imagens aéreas**: Ver [imagens de satélite.](#satellite-imagery) 

<a name="along-a-route-search"></a>**Ao longo de uma pesquisa** de rota : Uma consulta espacial que procura dados dentro de um tempo de desvio especificado ou distância de um caminho de rota.

<a name="altitude"></a>**Altitude**: Altura ou elevação vertical de um ponto acima de uma superfície de referência. As medições de altitude baseiam-se num dado de referência, como o nível médio do mar. Consulte também a elevação.

<a name="ambiguous"></a>**Ambíguo**: Estado de incerteza na classificação de dados que existe quando um objeto pode ser adequadamente atribuído a dois ou mais valores para um determinado atributo. Por exemplo, quando geocoding "CA", dois resultados ambíguos são devolvidos: "Canadá" e "Califórnia". "CA" é um país/região e um código de Estado, para "Canadá" e "Califórnia", respectivamente. 

<a name="annotation"></a>**Anotação**: Texto ou gráficos apresentados no mapa para fornecer informações ao utilizador. A anotação pode identificar ou descrever uma entidade de mapa específico, fornecer informações gerais sobre uma área no mapa ou fornecer informações sobre o mapa em si.

<a name="antimeridian"></a>**Antimeridiano:** Também conhecido como o <sup></sup> 180º Meridiano. Este é o ponto onde -180 graus e 180 graus de longitude se encontram. Que é o oposto do meridiano principal do globo.

<a name="application-programming-interface-api"></a>**Interface de Programação de Aplicações (API)**: Uma especificação que permite aos desenvolvedores criar aplicações.

<a name="api-key"></a>**Tecla API**: Ver [autenticação de chave partilhada.](#shared-key-authentication)

<a name="area-of-interest-aoi"></a>**Área de Interesse (AOI)**: A medida utilizada para definir uma área de foco para um mapa ou uma produção de base de dados.

<a name="asset-tracking"></a>**Rastreio de** ativos : O processo de rastreio da localização de um ativo, como uma pessoa, veículo ou qualquer outro objeto.

<a name="asynchronous-request"></a>**Pedido assíncrona**: Um pedido HTTP que abre uma ligação e faz um pedido ao servidor que devolve um identificador para o pedido assíncrona, fechando a ligação. O servidor continua a processar o pedido e o utilizador pode verificar o estado utilizando o identificador. Quando o pedido estiver concluído, o utilizador pode então descarregar a resposta. Este tipo de pedido é utilizado para processos de longa duração.

<a name="autocomplete"></a>**Precontagem automática**: Uma funcionalidade numa aplicação que prevê o resto de uma palavra que um utilizador está a escrever. 

<a name="autosuggest"></a>**Autosuggest**: Uma funcionalidade numa aplicação que prevê possibilidades lógicas para o que o utilizador está a escrever.

<a name="azure-location-based-services-lbs"></a>**Azure Location Based Services (LBS)**: O nome anterior de Azure Maps quando estava em pré-visualização.

<a name="azure-active-directory"></a>**Azure Ative Directory (Azure AD)**: Azure AD é o serviço de gestão de identidade e acesso baseado na nuvem da Microsoft. A integração Azure Maps AD está atualmente disponível em pré-visualização para todas as APIs do Azure Maps. A Azure AD suporta o controlo de acesso baseado em funções Azure (Azure RBAC) para permitir o acesso de grãos finos aos recursos do Azure Maps. Para saber mais sobre a integração Azure Maps AD, consulte [Azure Maps e AZure AD](azure-maps-authentication.md) e [Gerir a autenticação em Azure Maps.](how-to-manage-authentication.md)

<a name="azure-maps-key"></a>**Tecla Azure Maps**: Consulte [a autenticação da chave partilhada.](#shared-key-authentication)

## <a name="b"></a>B

<a name="base-map"></a>**Mapa base**: A parte de uma aplicação de mapa que exibe informações de referência de fundo, tais como estradas, marcos e fronteiras políticas.

<a name="batch-request"></a>**Pedido de lote**: O processo de combinar múltiplos pedidos num único pedido.

<a name="bearing"></a>**Rolamento**: A direção horizontal de um ponto em relação a outro ponto. Isto é expresso como um ângulo relativo ao norte, de 0 graus a 360 graus no sentido horário. 

<a name="boundary"></a>**Limite**: Uma linha ou polígono que separa entidades políticas adjacentes, tais como países/regiões, distritos e propriedades. Um limite é uma linha que pode ou não seguir características físicas, como rios, montanhas ou muros.

<a name="bounds"></a>**Limites**: Ver [caixa limite](#bounding-box).

<a name="bounding-box"></a>**Caixa limite**: Um conjunto de coordenadas utilizadas para representar uma área retangular no mapa. 

## <a name="c"></a>C

<a name="cadastre"></a>**Cadastre**: Registo de terrenos e propriedades registados. Consulte também [a Parcela.](#parcel)

<a name="camera"></a>**Câmara**: No contexto de um controlo de mapas interativos, uma câmara define o campo de visão dos mapas. A vista da câmara é determinada com base em vários parâmetros do mapa: centro, nível de zoom, tom, rolamento. 

<a name="centroid"></a>**Centroid**: O centro geométrico de uma característica. O centroíid de uma linha seria o ponto médio enquanto o centroíid de um polígono seria o seu centro de área.

<a name="choropleth-map"></a>**Mapa do choropleth**: Um mapa temático em que as áreas são sombreadas proporcionalmente a uma medição de uma variável estatística. Esta variável estatística é exibida no mapa. Por exemplo, colorir a fronteira de cada estado dos EUA com base na sua população relativa a todos os outros estados.

<a name="concave-hull"></a>**Casco côncavo**: Uma forma que representa uma possível geometria côncava que encerra todas as formas no conjunto de dados especificado. A forma gerada é semelhante a embrulhar os dados com plástico e depois aquecê-lo, causando grandes extensões entre pontos para ceder em direção a outros pontos de dados.

<a name="consumption-model"></a>**Modelo de consumo**: Informação que define a taxa a que um veículo consome combustível ou eletricidade. Consulte também a documentação do [modelo de consumo.](consumption-model.md)

<a name="control"></a>**Controlo**: Um componente autossuficiente ou reutilizável constituído por uma interface gráfica do utilizador que define um conjunto de comportamentos para a interface. Por exemplo, um controlo de mapas é geralmente a parte da interface do utilizador que carrega um mapa interativo.

<a name="convex-hull"></a>**Casco convexo**: Um casco convexo é uma forma que representa a geometria mínima convexa que encerra todas as formas no conjunto de dados especificado. A forma gerada é semelhante a envolver uma banda elástica em torno do conjunto de dados.

<a name="coordinate"></a>**Coordenada**: Consiste nos valores de longitude e latitude utilizados para representar uma localização num mapa.

<a name="coordinate-system"></a>**Sistema de coordenadas**: Quadro de referência utilizado para definir as posições dos pontos no espaço em duas ou três dimensões.

<a name="country-code"></a>**Código do país**: Um identificador único para um país/região com base na norma ISO. ISO2 é um código de dois caracteres para um país/região (por exemplo, EUA), que a ISO3 representa um código de três caracteres (por exemplo, EUA).

<a name="country-subdivision"></a>**Subdivisão do** país : Uma subdivisão de primeiro nível de um país/região, vulgarmente conhecido como estado ou província.

<a name="country-secondary-subdivision"></a>**Subdivisão secundária do país**: Uma subdivisão de segundo nível de um país/região, vulgarmente conhecido como um condado.

<a name="country-tertiary-subdivision"></a>**Subdivisão terciária do país**: Uma subdivisão de terceiro nível de um país/região, tipicamente uma área nomeada como uma ala.

<a name="cross-street"></a>**Cross street**: Um ponto onde duas ou mais ruas se cruzam.

<a name="cylindrical-projection"></a>**Projeção cilíndrica**: Uma projeção que transforma pontos de um esferoide ou esfera em um cilindro tangente ou secant. O cilindro é então cortado de cima para baixo e achatado num avião.

## <a name="d"></a>D

<a name="datum"></a>**Datum**: As especificações de referência de um sistema de medição, um sistema de posições de coordenadas sobre uma superfície (um datum horizontal) ou alturas acima ou abaixo de uma superfície (um datum vertical).

<a name="dbf-file"></a>**Ficheiro DBF**: Um formato de ficheiro de base de dados que é utilizado em combinação com Shapefiles (SHP).

<a name="degree-minutes-seconds-dms"></a>**Grau Minutos (DMS)**: A unidade de medida para descrever a latitude e a longitude. Um grau é 1/360<sup>de</sup> um círculo. Um grau é dividido em 60 minutos, e um minuto é dividido em 60 segundos.

<a name="delaunay-triangulation"></a>**Triangulação delaunay**: Uma técnica para criar uma malha de triângulos contíguos e não-penetrantes a partir de um conjunto de pontos. O círculo de circunscrição de cada triângulo não contém pontos do conjunto de dados no seu interior.

<a name="demographics"></a>Demografia : As **características** estatísticas (como a idade, a natalidade e o rendimento) de uma população humana.

<a name="destination"></a>**Destino**: Ponto final ou local para onde alguém está viajando.

<a name="digital-elevation-model-dem"></a>**Modelo de elevação digital (DEM)**: Um conjunto de dados de valores de elevação relacionados com uma superfície, capturados sobre uma área em intervalos regulares utilizando um dado comum. Os DEMs são normalmente usados para representar o relevo do terreno.

<a name="dijkstra's-algorithm"></a>**Algoritmo de Dijkstra**: Um algoritmo que examina a conectividade de uma rede para encontrar o caminho mais curto entre dois pontos.

<a name="distance-matrix"></a>**Matriz de distância**: Uma matriz que contém informações sobre o tempo de viagem e distância entre um conjunto de origens e destinos. 

## <a name="e"></a>E

<a name="elevation"></a>**Elevação**: A distância vertical de um ponto ou de um objeto acima ou abaixo de uma superfície de referência ou de um datum. Geralmente, a superfície de referência é o nível médio do mar. A elevação geralmente refere-se à altura vertical da terra.

<a name="envelope"></a>**Envelope**: Ver [caixa limite](#bounding-box).

<a name="extended-postal-code"></a>**Código postal alargado**: Um código postal que pode incluir informações adicionais. Por exemplo, nos EUA, os códigos postais têm cinco dígitos. Mas, um código postal estendido, conhecido como zip+4, inclui quatro dígitos adicionais. Estes dígitos adicionais são usados para identificar um segmento geográfico dentro da área de entrega de cinco dígitos, como um quarteirão da cidade, um grupo de apartamentos, ou uma caixa postal. Conhecer o segmento geográfico ajuda na triagem e entrega eficientes de correio.

<a name="extent"></a>**Extensão**: Ver [caixa limite](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a>**Autenticação federada**: Um método de autenticação que permite a utilização de um único mecanismo de logon/autenticação em várias aplicações web e móveis. 

<a name="feature"></a>**Característica**: Um objeto que combina uma geometria com uma informação adicional de metadados. 

<a name="feature-collection"></a>**Coleção de características**: Uma coleção de objetos de características.

<a name="find-along-route"></a>**Encontre ao longo da rota**: Uma consulta espacial que procure dados que se encontrem dentro de um tempo de desvio especificado ou distância de um caminho de rota.

<a name="find-nearby"></a>**Encontre nas proximidades**: Uma consulta espacial que procure uma distância fixa de linha reta (à medida que o corvo voa) a partir de um ponto.

<a name="fleet-management"></a>**Gestão da frota**: Gestão de veículos comerciais como automóveis, camiões, navios e aviões. A gestão da frota pode incluir uma gama de funções, tais como financiamento de veículos, manutenção, telemática (rastreio e diagnóstico) bem como gestão do condutor, velocidade, combustível e saúde e segurança. A Gestão da Frota é um processo utilizado por empresas que dependem do transporte nos seus negócios. As empresas querem minimizar os riscos e reduzir os seus custos globais de transporte e pessoal, garantindo ao mesmo tempo o cumprimento da legislação governamental.

<a name="free-flow-speed"></a>**Velocidade de fluxo livre**: A velocidade de fluxo livre esperada em condições ideais. Normalmente o limite de velocidade.

<a name="free-form-address"></a>**Endereço de formulário gratuito**: Um endereço completo que é representado como uma única linha de texto.

<a name="fuzzy-search"></a>**Pesquisa duvidosa**: Uma pesquisa que requer uma série de textos de forma gratuita que pode ser um endereço ou ponto de interesse. 

## <a name="g"></a>G

<a name="geocode"></a>**Geocódigo**: Um endereço ou localização que foi convertido numa coordenada que pode ser usada para exibir essa localização num mapa. 

<a name="geocoding"></a>**Geocoding**: Também conhecido como geocoding avançado, é o processo de conversão do endereço dos dados de localização em coordenadas. 

<a name="geodesic-path"></a>**Caminho geodésico**: O caminho mais curto entre dois pontos numa superfície curva. Quando prestado no Azure Maps este caminho aparece como uma linha curva devido à projeção do Mercator.

<a name="geofence"></a>**Geofence**: Uma região geográfica definida que pode ser usada para desencadear eventos quando um dispositivo entra ou existe na região.

<a name="geojson"></a>**GeoJSON**: É um formato comum de ficheiro baseado em JSON usado para armazenar dados de vetores geográficos tais como pontos, linhas e polígonos. **Nota:** A Azure Maps utiliza uma versão estendida da GeoJSON, como [documentado aqui.](extend-geojson.md)

<a name="geometry"></a>**Geometria**: Representa um objeto espacial como um ponto, linha ou polígono.

<a name="geometrycollection"></a>**GeometriaCollection**: Uma coleção de objetos de geometria.

<a name="geopol"></a>**GeoPol**: Refere-se a dados geopoliticamente sensíveis, tais como fronteiras disputadas e nomes de locais.

<a name="georeference"></a>**Georeferência**: O processo de alinhamento de dados geográficos ou imagens para um sistema de coordenadas conhecido. Este processo pode consistir em deslocar, rodar, escalar ou distorcer os dados.

<a name="georss"></a>**GeoRSS**: Uma extensão XML para a adição de dados espaciais aos feeds RSS.

<a name="gis"></a>**GIS**: Um acrónimo para "Geographic Information System". Um termo comum usado para descrever a indústria do mapeamento.

<a name="gml"></a>**GML**: Também conhecido como Linguagem de Marcação de Geografia. Uma extensão de ficheiro XML para armazenar dados espaciais.

<a name="gps"></a>**GPS**: Também conhecido como Global Positioning System, é um sistema de satélites utilizado para determinar a posição de um dispositivo na Terra. Os satélites em órbita transmitem sinais que permitem a um recetor de GPS em qualquer lugar da Terra calcular a sua própria localização através da trilateração.

<a name="gpx"></a>**GPX**: Também conhecido como formato GPS eXchange, é um formato de ficheiro XML comumente criado a partir de dispositivos GPS.  

<a name="great-circle-distance"></a>**Distância do grande círculo**: A distância mais curta entre dois pontos na superfície de uma esfera.

<a name="greenwich-mean-time-gmt"></a>**Greenwich Mean Time (GMT)**: O tempo no meridiano principal, que passa pelo Observatório Real em Greenwich, Inglaterra.

<a name="guid"></a>**GUID**: Um identificador globalmente único. Uma corda usada para identificar exclusivamente uma interface, classe, biblioteca tipo, categoria de componente ou registo.

## <a name="h"></a>H

<a name="haversine-formula"></a>**Fórmula haversina**: Uma equação comum usada para calcular a distância do grande círculo entre dois pontos numa esfera.

<a name="hd-maps"></a>**Mapas HD**: Também conhecido como Mapas de Alta Definição, consiste em informações de rede rodoviária de alta fidelidade, tais como marcações de faixas, sinalização e luzes de direção necessárias para a condução autónoma.

<a name="heading"></a>**Direção**: A direção que algo está a apontar ou virado. Ver também [rolamento](#heading).

<a name="heatmap"></a>**Mapa térmico**: Visualização de dados em que uma gama de cores representa a densidade de pontos numa determinada área. Consulte também o mapa temático.

<a name="hybrid-imagery"></a>**Imagens híbridas**: Imagens de satélite ou aéreas que têm dados de estrada e etiquetas sobrepostas em cima.

## <a name="i"></a>I

<a name="iana"></a>**IANA**: Um acrónimo para a Internet Atribuída Números Autoridade. Um grupo sem fins lucrativos que supervisiona a atribuição global de endereços IP.

<a name="isochrone"></a>**Isochrone**: Um isochrone define a área em que alguém pode viajar dentro de um tempo especificado para um modo de transporte em qualquer direção a partir de um determinado local. Ver também [alcance acessível](#reachable-range).

<a name="isodistance"></a>**Isodistância**: Dada a localização, um isocrone define a área em que alguém pode viajar a uma distância especificada para um modo de transporte em qualquer direção. Ver também [alcance acessível](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a>**KML**: Também conhecido como Linguagem de Marcação de Buracos de Fechadura, é um formato comum de ficheiro XML para armazenar dados de vetores geográficos tais como pontos, linhas e polígonos. 

## <a name="l"></a>L

<a name="landsat"></a>**Landsat**: Satélites multiespectrais, em órbita terrestre desenvolvidos pela NASA que recolhem imagens de terra. Esta imagem é usada em muitas indústrias como a agricultura, a silvicultura e a cartografia.

<a name="latitude"></a>**Latitude**: A distância angular medida em graus do equador no sentido norte ou sul.

<a name="level-of-detail"></a>**Nível de detalhe**: Ver nível de zoom.

<a name="lidar"></a>**Lidar**: Acrónimo para deteção de luz e variação. Uma técnica de deteção remota que usa lasers para medir distâncias a superfícies reflexivas.

<a name="linear-interpolation"></a>**Interpolação linear**: A estimativa de um valor desconhecido utilizando a distância linear entre valores conhecidos.

<a name="linestring"></a>**LineString**: Uma geometria usada para representar uma linha. Também conhecido como polilina. 

<a name="localization"></a>**Localização**: Apoio a diferentes línguas e culturas.

<a name="logistics"></a>**Logística**: Processo de deslocação de pessoas, veículos, mantimentos ou bens de forma coordenada.

<a name="longitude"></a>**Longitude**: A distância angular medida em graus do meridiano primário na direção leste ou oeste.

## <a name="m"></a>M

<a name="map-tile"></a>**Azulejo do mapa**: Uma imagem retangular que representa uma partição de uma tela de mapa. Para obter mais informações, consulte os [níveis de Zoom e a documentação da grelha de azulejos.](zoom-levels-and-tile-grid.md)

<a name="marker"></a>**Marcador**: Também conhecido como pino ou pino, é um ícone que representa uma localização de ponto num mapa.

<a name="mercator-projection"></a>**Projeção do Mercator**: Uma projeção cilíndrica do mapa que se tornou a projeção padrão do mapa para fins náuticos devido à sua capacidade de representar linhas de curso constante, conhecidas como linhas de rhumb, como segmentos retos que conservam os ângulos com os meridianos. Todas as projeções de mapas planos distorcem as formas ou tamanhos do mapa quando comparadas com o verdadeiro layout da superfície da Terra. A projeção do Mercator exagera áreas longe do equador, de tal forma que áreas mais pequenas aparecem maiores no mapa à medida que se aproxima dos polos. 

<a name="multilinestring"></a>**MultiLineString**: Uma geometria que representa uma coleção de objetos LineString. 

<a name="multipoint"></a>**MultiPoint**: Uma geometria que representa uma coleção de objetos Point.

<a name="multipolygon"></a>**MultiPolygon**: Uma geometria que representa uma coleção de objetos do Polígono. Por exemplo, para mostrar a fronteira do Havaí, cada ilha seria delineada com um polígono. Assim, a fronteira do Havaí seria, assim, um MultiPolygon.

<a name="municipality"></a>**Município**: Uma cidade ou cidade. 

<a name="municipality-subdivision"></a>**Subdivisão do município**: Subdivisão de um município, como um nome de bairro ou de área local como "centro da cidade".

## <a name="n"></a>N

<a name="navigation-bar"></a>**Barra de navegação**: O conjunto de controlos num mapa utilizado para ajustar o nível de zoom, o tom, a rotação e a mudança da camada do mapa base.

<a name="nearby-search"></a>**Pesquisa nas proximidades**: Uma consulta espacial que procura uma distância fixa de linha reta (como o corvo voa) a partir de um ponto.

<a name="neutral-ground-truth"></a>**Neutral Ground Truth**: Um mapa que torna os rótulos na língua oficial da região que representa e em scripts locais, se disponível.

## <a name="o"></a>O

<a name="origin"></a>**Origem**: Ponto de partida ou local em que o utilizador se encontra.

## <a name="p"></a>P

<a name="panning"></a>**Panorâmica**: O processo de mover o mapa em qualquer direção, mantendo um nível de zoom constante.

<a name="parcel"></a>**Parcela**: Um lote de terreno ou limite de propriedade.

<a name="pitch"></a>**Pitch**: A quantidade de inclinação do mapa tem em relação à vertical onde 0 está olhando diretamente para o mapa.

<a name="point"></a>**Ponto**: Uma geometria que representa uma única posição no mapa. 

<a name="points-of-interest-poi"></a>**Pontos de interesse (POI)**: Um negócio, marco ou local de interesse comum.

<a name="polygon"></a>**Polígono**: Uma geometria sólida que representa uma área num mapa. 

<a name="polyline"></a>**Polilina**: Uma geometria usada para representar uma linha. Também conhecido como LineString. 

<a name="position"></a>**Posição**: Longitude, latitude e altitude (coordenadas x,y,z) de um ponto.

<a name="post-code"></a>**Código postal**: Ver [código postal](#postal-code).

<a name="postal-code"></a>**Código postal**: Uma série de letras ou números, ou ambos, num formato específico. O código postal é utilizado pelo serviço postal de um país/região para dividir as zonas geográficas em zonas, a fim de simplificar a entrega de correio.

<a name="primary-key"></a>**Tecla principal**: A primeira de duas chaves de subscrição fornecidas para a autenticação partilhada do Azure Maps. Consulte [a autenticação da chave partilhada.](#shared-key-authentication)

<a name="prime-meridian"></a>**Meridiano primário**: Uma linha de longitude que representa 0 graus de longitude. Geralmente, os valores de longitude diminuem quando viajam em direção oeste até 180 graus e aumentam quando viajam em direções orientais para -180 graus. 

<a name="prj"></a>**PRJ**: Um ficheiro de texto que acompanha frequentemente um ficheiro Shapefile que contém informações sobre o sistema de coordenadas projetado em que o conjunto de dados está.

<a name="projection"></a>**Projeção**: Um sistema de coordenadas projetado baseado numa projeção de mapas como mercator transversal, área igual de Albers e Robinson. Estes fornecem a capacidade de projetar mapas da superfície esférica da Terra num plano de coordenadas cartesiano bidimensional. Os sistemas de coordenadas projetados são por vezes referidos como projeções de mapas.

## <a name="q"></a>Q

<a name="quadkey"></a>**Quadkey**: Um índice de endereço base-4 para um azulejo dentro de um sistema de inclinação quadtree. Para mais informações, consulte [os níveis de Zoom e](zoom-levels-and-tile-grid.md) a documentação da grelha de azulejos para obter mais informações.

<a name="quadtree"></a>**Quadtree**: Uma estrutura de dados na qual cada nó tem exatamente quatro filhos. O sistema de inclinação utilizado no Azure Maps utiliza uma estrutura quadrátil, tal como um utilizador faz zooms num só nível, cada azulejo do mapa divide-se em quatro subtiles.  Para mais informações, consulte [os níveis de Zoom e](zoom-levels-and-tile-grid.md) a documentação da grelha de azulejos para obter mais informações.

<a name="queries-per-second-qps"></a>**Consultas Por Segundo (QPS)**: O número de consultas ou pedidos que podem ser feitos a um serviço ou plataforma dentro de um segundo. 

## <a name="r"></a>R

<a name="radial-search"></a>**Busca radial**: Uma consulta espacial que procura uma distância fixa de linha reta (à medida que o corvo voa) a partir de um ponto. 

<a name="raster-data"></a>**Dados do raster**: Uma matriz de células (ou pixels) organizada em linhas e colunas (ou uma grelha) onde cada célula contém um valor que representa informações, como a temperatura. Raster's inclui fotografias aéreas digitais, imagens de satélites, imagens digitais e mapas digitalizados.

<a name="raster-layer"></a>**Camada raster**: Uma camada de azulejos que consiste em imagens raster.

<a name="reachable-range"></a>**Gama acessível**: Uma gama acessível define a área em que alguém pode viajar dentro de um tempo ou distância especificado, para um modo de transporte viajar, em qualquer direção a partir de um local. Ver também [Isochrone](#isochrone) e [Isodistance.](#isodistance)

<a name="remote-sensing"></a>**Deteção remota**: O processo de recolha e interpretação de dados do sensor à distância.

<a name="rest-service"></a>**Serviço REST**: O acrónimo REST significa Transferência do Estado Representativo. Um serviço REST é um serviço web baseado em URL que se baseia na tecnologia web básica para comunicar, sendo os métodos mais comuns os pedidos HTTP GET e POST. Este tipo de serviços tendem-me muito mais rápido e menor do que os serviços tradicionais baseados em SOAP.

<a name="reverse-geocode"></a>**Geocódigo inverso**: O processo de tomada de uma coordenada e determinação do endereço em que está representado num mapa.

<a name="reproject"></a>**Reprojecto**: Ver [Transformação.](#transformation)

<a name="rest-service"></a>**Serviço REST**: Acrónimo para Transferência de Estado Representativo. Uma arquitetura para o intercâmbio de informações entre pares num ambiente descentralizado e distribuído. Rest permite que programas em diferentes computadores se comuniquem independentemente de um sistema operativo ou plataforma. Um serviço pode enviar um pedido de Protocolo de Transferência de Hipertexto (HTTP) a um localizador de recursos uniforme (URL) e obter dados de volta.

<a name="route"></a>**Rota**: Um caminho entre dois ou mais locais, que também pode incluir informações adicionais, tais como instruções para pontos de passagem ao longo do percurso.

<a name="requests-per-second-rps"></a>**Pedidos por segundo (RPS)**: Ver [consultas por segundo (QPS)](#queries-per-second-qps). 

<a name="rss"></a>**RSS**: Acrónimo para Síndication Realmente Simples, Quadro de Descrição de Recursos (RDF) Resumo do Site ou Resumo do Sítio Rico, dependendo da fonte. Um formato XML simples e estruturado para a partilha de conteúdos entre diferentes Web sites. Os documentos RSS incluem elementos-chave de metadados tais como autor, data, título, uma breve descrição e uma ligação de hipertexto. Esta informação ajuda um utilizador (ou um serviço de editores RSS) a decidir quais os materiais que valem a pena investigar.

## <a name="s"></a>S

<a name="satellite-imagery"></a>**Imagens de satélite**: Imagens que foram captadas por aviões e satélites apontando diretamente para baixo.

<a name="secondary-key"></a>**Chave secundária**: A segunda de duas chaves de subscrição fornecidas para a autenticação partilhada do Azure Maps. Consulte [a autenticação da chave partilhada.](#shared-key-authentication)

<a name="shapefile-shp"></a>**Shapefile (SHP)**: Também conhecido como ficheiro ESRI Shapefile, é um formato de armazenamento de dados vetoriais para armazenar a localização, forma e atributos de características geográficas. Um filtro de forma é armazenado num conjunto de ficheiros relacionados.

<a name="shared-key-authentication"></a>**Autenticação de chaves partilhadas**: A autenticação da chave partilhada baseia-se na passagem das chaves geradas pelo Azure Maps a cada pedido ao Azure Maps. Estas chaves são frequentemente referidas como chaves de subscrição. Recomenda-se que as chaves sejam regularmente regeneradas por segurança. São fornecidas duas teclas para que possa manter as ligações utilizando uma chave enquanto regenera a outra. Ao regenerar as suas chaves, tem de atualizar quaisquer aplicações que acedam a esta conta para utilizar as novas chaves. Para saber mais sobre a autenticação do Azure Maps, consulte [Azure Maps e Azure AD](azure-maps-authentication.md) e [Gerir a autenticação em Azure Maps.](how-to-manage-authentication.md)

<a name="software-development-kit-sdk"></a>**Kit de desenvolvimento de software (SDK)**: Uma coleção de documentação, código de amostra e aplicações de amostra para ajudar um desenvolvedor a usar uma API para construir apps.

<a name="spherical-mercator-projection"></a>**Projeção do Mercator Esférico**: Ver [Web Mercator](#web-mercator). 

<a name="spatial-query"></a>**Consulta espacial**: Um pedido feito a um serviço que executa uma operação espacial. Como uma pesquisa radial, ou ao longo de uma busca de rota.

<a name="spatial-reference"></a>**Referência espacial**: Um sistema local, regional ou global baseado em coordenadas utilizado para localizar precisamente entidades geográficas. Define o sistema de coordenadas usado para relacionar coordenadas de mapas com localizações no mundo real. As referências espaciais garantem que dados espaciais de diferentes camadas, ou fontes, podem ser integrados para uma visualização ou análise precisa. O Azure Maps utiliza o sistema de referência de coordenadas [EPSG:3857](https://epsg.io/3857) e o WGS 84 para dados de geometria de entrada.

<a name="sql-spatial"></a>**SQL espacial**: Refere-se à funcionalidade espacial incorporada no SQL Azure e no SQL Server 2008 e acima. Esta funcionalidade espacial também está disponível como uma biblioteca .NET que pode ser usada independentemente do SQL Server. Para obter mais informações, consulte a [documentação de Dados Espaciais (SQL Server)](/sql/relational-databases/spatial/spatial-data-sql-server) para obter mais informações.

<a name="subscription-key"></a>**Chave de subscrição**: Ver [autenticação de chave partilhada](#shared-key-authentication).

<a name="synchronous-request"></a>**Pedido sincronizado**: Um pedido HTTP abre uma ligação e aguarda uma resposta. Os navegadores limitam o número de pedidos HTTP simultâneos que podem ser feitos a partir de uma página. Se vários pedidos sincronizados de longa duração forem feitos ao mesmo tempo, então este limite pode ser atingido. Os pedidos serão adiados até que um dos outros pedidos esteja concluído.

## <a name="t"></a>T

<a name="telematics"></a>**Telemática**: Envio, receção e armazenamento de informações através de dispositivos de telecomunicações em conjunto com o controlo efetuante de objetos remotos. 

<a name="temporal-data"></a>**Dados temporais**: Dados que se referem especificamente a horas ou datas. Os dados temporais podem referir-se a eventos discretos, tais como relâmpagos; objetos móveis, como comboios; ou observações repetidas, tais como contagens de sensores de tráfego.

<a name="terrain"></a>**Terreno**: Zona de terreno com uma característica particular, como terreno arenoso ou terreno montanhoso.

<a name="thematic-maps"></a>**Mapas temáticos**: Um mapa temático é um mapa simples feito para refletir um tema sobre uma área geográfica. Um cenário comum para este tipo de mapa é colorir as regiões administrativas, como países/regiões, com base em algumas métricas de dados.

<a name="tile-layer"></a>**Camada de azulejo**: Uma camada exibida por montagem de telhas de mapa (secções retangulares) numa camada contínua. Os azulejos são ou azulejos de imagem raster ou azulejos vetoriais. As camadas de azulejos raster são normalmente renderizadas antes do tempo, e são armazenadas como imagens num servidor. As camadas de azulejos raster podem usar um grande espaço de armazenamento. As camadas de azulejos do vetor são renderizadas perto do tempo real dentro da aplicação do cliente. Assim, os requisitos de armazenamento do lado do servidor são menores para camadas de azulejos vetoriais.

<a name="time-zone"></a>**Fuso horário**: Uma região do globo que observa um tempo padrão uniforme para fins legais, comerciais e sociais. Os fusos horários tendem a seguir os limites dos países/regiões e das suas subdivisões.

<a name="transaction"></a>**Transação**: A Azure Maps utiliza um modelo de licenciamento transacional onde;

- Uma transação é criada para cada 15 mapas ou azulejos de tráfego solicitados.
- Uma transação é criada para cada chamada da API para um dos serviços no Azure Maps. Pesquisa e encaminhamento são exemplos do serviço Azure Maps.

<a name="transformation"></a>**Transformação**: Processo de conversão de dados entre diferentes sistemas de coordenadas geográficas. Pode, por exemplo, ter alguns dados que foram capturados no Reino Unido e baseados no sistema de coordenadas geográficas OSGB 1936. O Azure Maps utiliza a variante do sistema de referência de [coordenadas EPSG:3857](https://epsg.io/3857) do WGS84. Como tal, para exibir os dados corretamente, terá de ter as suas coordenadas transformadas de um sistema para outro.

<a name="traveling-salesmen-problem-tsp"></a>**Problema dos Vendedores Viajantes (TSP)**: Um problema de circuito hamiltoniano no qual um vendedor deve encontrar a maneira mais eficiente de visitar uma série de paragens e, em seguida, voltar ao local de partida.  

<a name="trilateration"></a>**Trilateração**: O processo de determinação da posição de um ponto na superfície da Terra, no que diz respeito a dois outros pontos, medindo as distâncias entre os três pontos.

<a name="turn-by-turn-navigation"></a>**Navegação por turnos**: Uma aplicação que fornece instruções de rota para cada passo de uma rota à medida que os utilizadores se aproximam da próxima manobra.

## <a name="v"></a>V

<a name="vector-data"></a>**Dados do vetor**: Coordenar dados baseados que são representados como pontos, linhas ou polígonos.

<a name="vector-tile"></a>**Azulejo vetorial**: Uma especificação de dados aberta para armazenar dados de vetores geoespaciais utilizando o mesmo sistema de azulejos que o controlo do mapa. Consulte também [a camada de azulejos.](#tile-layer)

<a name="vehicle-routing-problem-vrp"></a>**Problema de encaminhamento de veículos (VRP)**: Uma classe de problemas, em que um conjunto de rotas ordenadas para uma frota de veículos é calculado, tendo em conta como conjunto de restrições. Estes constrangimentos podem incluir janelas de tempo de entrega, múltiplas capacidades de rota e restrições de duração da viagem.

<a name="voronoi-diagram"></a>**Diagrama voronoi**: Uma divisória do espaço em áreas, ou células, que rodeiam um conjunto de objetos geométricos, geralmente apontam características. Estas células, ou polígonos, devem satisfazer os critérios para os triângulos de Delaunay. Todas as localizações dentro de uma área estão mais próximas do objeto que rodeia do que de qualquer outro objeto no conjunto. Os diagramas de Voronoi são frequentemente usados para delinear áreas de influência em torno das características geográficas. 

## <a name="w"></a>W

<a name="waypoint"></a>**Waypoint**: Um ponto de passagem é uma localização geográfica especificada definida por longitude e latitude que é utilizada para fins de navegação. Muitas vezes representava um ponto em que alguém navega numa rota.

<a name="waypoint-optimization"></a>**Otimização do waypoint**: O processo de reordenar um conjunto de pontos de passagem para minimizar o tempo de viagem ou distância necessário para passar por todos os pontos de passagem fornecidos. Dependendo da complexidade da otimização, esta otimização é frequentemente referida como o Problema dos [Vendedores Viajantes](#traveling-salesmen-problem-tsp) ou [Problema de Encaminhamento de Veículos](#vehicle-routing-problem-vrp).

<a name="web-map-service-wms"></a>**Web Map Service (WMS)**: WMS é um padrão do Open Geographic Consortium (OGC) que define serviços de mapas baseados em imagem. Os serviços WMS fornecem imagens de mapas para áreas específicas dentro de um mapa a pedido. As imagens incluem simbologia pré-renderizada e podem ser renderizadas num dos vários estilos nomeados se definidos pelo serviço.

<a name="web-mercator"></a>**Web Mercator**: Também conhecida como projeção de Mercator Esférico. É uma pequena variante da projeção do Mercator, usada principalmente em programas de mapeamento baseados na Web. Utiliza as mesmas fórmulas que a projeção mercator padrão usada para mapas de pequena escala. No entanto, o Web Mercator usa as fórmulas esféricas em todas as escalas, mas mapas mercator em larga escala normalmente usam a forma elipsoidal da projeção. A discrepância é impercetível à escala global, mas faz com que mapas das áreas locais se desviem ligeiramente dos verdadeiros mapas elipsoidais do Mercator, na mesma escala.

<a name="wgs84"></a>**WGS84**: Um conjunto de constantes usadas para relacionar coordenadas espaciais com locais na superfície do mapa. O datum WGS84 é o padrão usado pela maioria dos fornecedores de mapeamento on-line e dispositivos GPS. O Azure Maps utiliza a variante do sistema de referência de [coordenadas EPSG:3857](https://epsg.io/3857) do WGS84.

## <a name="z"></a>Z

<a name="z-coordinate"></a>**Z-coordenada:** Ver [Altitude](#altitude). 

<a name="zip-code"></a>**Código postal**: Ver [código postal](#postal-code).

<a name="Zoom level"></a>**Nível de zoom**: Especifica o nível de detalhe e a quantidade de mapa visível. Quando ampliado todo o caminho para o nível 0, o mapa do mundo inteiro será muitas vezes visível. Mas, o mapa mostrará detalhes limitados, como nomes de país/região, fronteiras e nomes do oceano. Quando ampliado mais perto do nível 17, o mapa mostrará uma área de alguns quarteirões da cidade com informações detalhadas sobre a estrada. Em Azure Maps, o nível de zoom mais alto é 22. Para obter mais informações, consulte os [níveis de Zoom e](zoom-levels-and-tile-grid.md) a documentação da grelha de azulejos.