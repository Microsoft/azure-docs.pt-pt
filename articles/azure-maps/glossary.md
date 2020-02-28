---
title: Glossário Azure Maps Microsoft Docs
description: Um glossário de termos comumente usados associados com Mapas Azure, Serviços baseados em localização e GIS.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: c9d5c2efc14dec5beee0eaf81a4f628421ef3d49
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657049"
---
# <a name="glossary"></a>Glossário

A lista seguinte descreve palavras comuns usadas com os serviços Azure Maps.

## <a name="a"></a>A

<a name="address-validation"></a>**Validação do endereço**: O processo de verificação da existência de um endereço.

<a name="advanced-routing"></a>**Encaminhamento avançado**: Uma recolha de serviços que realizam operações antecipadas utilizando dados de encaminhamento rodoviário; tais como, calculando gamas acessíveis (isochrones), matrizes de distância e pedidos de rota de lote.

<a name="aerial-imagery"></a>**Imagens aéreas**: Ver [imagens de satélite](#satellite-imagery). 

<a name="along-a-route-search"></a>Ao longo de **uma pesquisa de percursos**: Uma consulta espacial que procura dados dentro de um tempo ou distância de desvio especificado de um percurso.

<a name="altitude"></a>**Altitude**: A altura ou elevação vertical de um ponto acima de uma superfície de referência. As medições de altitude baseiam-se num datum de referência, como o nível médio do mar. Ver também elevação.

<a name="ambiguous"></a>**Ambíguo**: Um estado de incerteza na classificação de dados que existe quando um objeto pode ser adequadamente atribuído dois ou mais valores para um determinado atributo. Por exemplo, quando a geocodificação "CA", são devolvidos dois resultados ambíguos: "Canadá" e "Califórnia". "CA" é um país e um código de Estado, para "Canadá" e "Califórnia", respectivamente. 

<a name="annotation"></a>**Anotação**: Texto ou gráficos apresentados no mapa para fornecer informações ao utilizador. A anotação pode identificar ou descrever uma entidade de mapa específica, fornecer informações gerais sobre uma área no mapa, ou fornecer informações sobre o próprio mapa.

<a name="antimeridian"></a>**Antimeridian**: Também conhecido como<sup></sup> o Meridian 180 th. Este é o ponto onde -180 graus e 180 graus de longitude se encontram. Que é o oposto do meridiano principal do mundo.

<a name="application-programming-interface-api"></a>Interface de Programação de **Aplicações (API)** : Uma especificação que permite aos desenvolvedores criar aplicações.

<a name="api-key"></a>**Tecla API**: Ver [autenticação da chave partilhada](#shared-key-authentication).

<a name="area-of-interest-aoi"></a>**Área de Interesse (AOI)** : A extensão utilizada para definir uma área de foco para um mapa ou uma produção de base de dados.

<a name="asset-tracking"></a>**Rastreio de ativos**: O processo de rastreio da localização de um ativo, como uma pessoa, veículo ou qualquer outro objeto.

<a name="asynchronous-request"></a>**Pedido assíncrono**: Um pedido HTTP que abre uma ligação e faz um pedido ao servidor que devolve um identificador para o pedido assíncrono, em seguida, fecha a ligação. O servidor continua a processar o pedido e o utilizador pode verificar o estado utilizando o identificador. Quando o pedido estiver concluído, o utilizador pode então descarregar a resposta. Este tipo de pedido é utilizado para processos de longo curso.

<a name="autocomplete"></a>**Autocomplete**: Uma funcionalidade numa aplicação que prevê o resto de uma palavra que um utilizador está a escrever. 

<a name="autosuggest"></a>**Sugestão automática**: Uma funcionalidade numa aplicação que prevê possibilidades lógicas para o que o utilizador está a escrever.

<a name="azure-location-based-services-lbs"></a>**Serviços baseados em localização Azure (LBS)** : O antigo nome do Azure Maps quando estava em pré-visualização.

<a name="azure-active-directory"></a>**Azure Ative Directory (Azure AD)** : Azure AD é o serviço de gestão de identidade e acesso baseado na nuvem da Microsoft. A integração da Azure Maps Azure AD está atualmente disponível em pré-visualização de todas as APIs do Azure Maps. A Azure AD apoia o controlo de acesso baseado em papéis (RBAC) para permitir o acesso fino aos recursos do Azure Maps. Para saber mais sobre a integração da Azure Maps Azure AD, consulte [o Azure Maps e o Azure AD](azure-maps-authentication.md) e [gerea a autenticação em Azure Maps.](how-to-manage-authentication.md)

<a name="azure-maps-key"></a>**Chave Azure Maps**: Ver [autenticação de chaves partilhadas](#shared-key-authentication).

## <a name="b"></a>B

<a name="base-map"></a>**Mapa base**: A parte de uma aplicação de mapa que exibe informações de referência de fundo, tais como estradas, marcos e fronteiras políticas.

<a name="batch-request"></a>**Pedido**de lote : O processo de combinar múltiplos pedidos num único pedido.

<a name="bearing"></a>**Rolamento**: A direção horizontal de um ponto em relação a outro ponto. Isto é expresso como um ângulo em relação ao norte, de 0 graus a 360 graus no sentido horário. 

<a name="boundary"></a>**Limite**: Linha ou polígono que separa entidades políticas adjacentes, tais como países/regiões, distritos e propriedades. Um limite é uma linha que pode ou não seguir características físicas, como rios, montanhas ou paredes.

<a name="bounds"></a>**Limites**: Ver [caixa de delimitação](#bounding-box).

<a name="bounding-box"></a>**Caixa**de delimitação : Um conjunto de coordenadas utilizadas para representar uma área retangular no mapa. 

## <a name="c"></a>C

<a name="cadastre"></a>**Cadastre**: Um registo de terrenos e propriedades registados. Ver também [Parcela.](#parcel)

<a name="camera"></a>**Câmara**: No contexto de um controlo interativo do mapa, uma câmara define o campo de visão dos mapas. O viewport da câmara é determinado com base em vários parâmetros do mapa: centro, nível de zoom, arremesso, rolamento. 

<a name="centroid"></a>**Centroid**: O centro geométrico de uma característica. O centroid de uma linha seria o ponto médio, enquanto o centroid de um polígono seria o seu centro de área.

<a name="choropleth-map"></a>Mapa de **choropleth**: Um mapa temático em que as áreas são sombreadas proporcionalmente a uma medição de uma variável estatística. Esta variável estatística é exibida no mapa. Por exemplo, colorir a fronteira de cada estado dos EUA com base na sua população relativa para todos os outros estados.

<a name="concave-hull"></a>**Casco côncavo**: Uma forma que representa uma possível geometria côncava que encerra todas as formas no conjunto de dados especificado. A forma gerada é semelhante a embrulhar os dados com plástico e depois aquecê-lo, causando assim grandes períodos entre pontos para ceder em direção a outros pontos de dados.

<a name="consumption-model"></a>**Modelo de consumo**: Informação que define a taxa a que um veículo consome combustível ou eletricidade. Consulte também a documentação do modelo de [consumo.](consumption-model.md)

<a name="control"></a>**Controlo**: Um componente autossuficiente ou reutilizável constituído por uma interface gráfica do utilizador que define um conjunto de comportamentos para a interface. Por exemplo, um controlo de mapas, é geralmente a parte da interface do utilizador que carrega um mapa interativo.

<a name="convex-hull"></a>**Casco convexo**: Um casco convexo é uma forma que representa a geometria convexa mínima que encerra todas as formas no conjunto de dados especificado. A forma gerada é semelhante a envolver uma banda elástica em torno do conjunto de dados.

<a name="coordinate"></a>**Coordenada**: Consiste nos valores de longitude e latitude utilizados para representar uma localização num mapa.

<a name="coordinate-system"></a>**Sistema de coordenadas**: Um quadro de referência utilizado para definir as posições dos pontos no espaço em duas ou três dimensões.

<a name="country-code"></a>**Código do país**: Um identificador único para um país/região com base na norma ISO. ISO2 é um código de dois caracteres para um país (por exemplo, EUA), que o ISO3 representa um código de três caracteres (por exemplo, EUA).

<a name="country-subdivision"></a>**Subdivisão**do país : Uma subdivisão de primeiro nível de um país/região, vulgarmente conhecida como estado ou província.

<a name="country-secondary-subdivision"></a>**Subdivisão secundária**do país : Uma subdivisão de segundo nível de um país/região, vulgarmente conhecido como um concelho.

<a name="country-tertiary-subdivision"></a>**Subdivisão terciária**do país : Uma subdivisão de terceiro nível de um país/região, tipicamente uma área nomeada, como uma ala.

<a name="cross-street"></a>**Cross street**: Um ponto onde duas ou mais ruas se cruzam.

<a name="cylindrical-projection"></a>**Projeção cilíndrica**: Uma projeção que transforma pontos de um esferoide ou esfera para um cilindro tangente ou secante. O cilindro é então cortado de cima para baixo e achatado num plano.

## <a name="d"></a>D

<a name="datum"></a>**Datum**: As especificações de referência de um sistema de medição, um sistema de posições de coordenadas sobre uma superfície (um datum horizontal) ou alturas acima ou abaixo de uma superfície (um datum vertical).

<a name="dbf-file"></a>**Ficheiro DBF**: Um formato de ficheiro de base de dados que é utilizado em combinação com O Shapefiles (SHP).

<a name="degree-minutes-seconds-dms"></a>**Graus Minutos Segundos (DMS)** : A unidade de medida para descrever a latitude e a longitude. Um diploma é 1/360<sup>de</sup> um círculo. Um diploma é ainda dividido em 60 minutos, e um minuto é dividido em 60 segundos.

<a name="delaunay-triangulation"></a>**Triangulação delaunay**: Uma técnica para criar uma malha de triângulos contíguos e não sobrepostos a partir de um conjunto de dados de pontos. O círculo circunscrito de cada triângulo não contém pontos do conjunto de dados no seu interior.

<a name="demographics"></a>**Demografia**: As características estatísticas (como a idade, a taxa de natalidade e o rendimento) de uma população humana.

<a name="destination"></a>**Destino**: Um ponto final ou local para onde alguém está viajando.

<a name="digital-elevation-model-dem"></a>**Modelo de Elevação Digital (DEM)** : Um conjunto de dados de valores de elevação relacionados com uma superfície, capturado sobre uma área em intervalos regulares utilizando um datum comum. Os DEMs são normalmente usados para representar o relevo do terreno.

<a name="dijkstra's-algorithm"></a>**Algoritmo de Dijkstra**: Um algoritmo que examina a conectividade de uma rede para encontrar o caminho mais curto entre dois pontos.

<a name="distance-matrix"></a>**Matriz de distância**: Uma matriz que contém informações sobre o tempo de viagem e a distância entre um conjunto de origens e destinos. 

## <a name="e"></a>E

<a name="elevation"></a>**Elevação**: Distância vertical de um ponto ou de um objeto acima ou abaixo de uma superfície de referência ou datum. Geralmente, a superfície de referência é o nível médio do mar. A elevação geralmente refere-se à altura vertical da terra.

<a name="envelope"></a>**Envelope**: Ver [caixa de delimitação](#bounding-box).

<a name="extended-postal-code"></a>**Código postal alargado**: Um código postal que pode incluir informações adicionais. Por exemplo, nos EUA, os códigos postais têm cinco dígitos. Mas, um código postal estendido, conhecido como zip+4, inclui quatro dígitos adicionais. Estes dígitos adicionais são usados para identificar um segmento geográfico dentro da área de entrega de cinco dígitos, como um quarteirão da cidade, um grupo de apartamentos, ou uma caixa de correios. Conhecer o segmento geográfico ajuda na triagem e entrega eficientes de correio.

<a name="extent"></a>**Extensão**: Ver [caixa de delimitação](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a>**Autenticação federada**: Um método de autenticação que permite a utilização de um único mecanismo de logon/autenticação em várias aplicações web e móveis. 

<a name="feature"></a>**Recurso**: Um objeto que combina uma geometria com uma informação adicional de metadados. 

<a name="feature-collection"></a>**Coleção de recursos**: Uma coleção de objetos de recurso.

<a name="find-along-route"></a>**Encontre ao longo do percurso**: Uma consulta espacial que procura dados que se encontrem dentro de um tempo ou distância de desvio especificado de um percurso.

<a name="find-nearby"></a>**Encontre nas proximidades**: Uma consulta espacial que procura uma distância fixa de linha reta (como o corvo voa) a partir de um ponto.

<a name="fleet-management"></a>**Gestão da frota**: Gestão de veículos comerciais como carros, camiões, navios e aviões. A gestão da frota pode incluir uma série de funções, tais como financiamento de veículos, manutenção, telemática (rastreio e diagnóstico), bem como gestão do condutor, velocidade, combustível e gestão da saúde e segurança. Fleet Management é um processo usado por empresas que dependem do transporte no seu negócio. As empresas querem minimizar os riscos e reduzir os seus custos globais de transporte e pessoal, garantindo simultaneamente o cumprimento da legislação governamental.

<a name="free-flow-speed"></a>**Velocidade de fluxo livre**: A velocidade de fluxo livre esperada em condições ideais. Normalmente, o limite de velocidade.

<a name="free-form-address"></a>**Endereço de formulário gratuito**: Um endereço completo que é representado como uma única linha de texto.

<a name="fuzzy-search"></a>**Pesquisa difusa**: Uma pesquisa que adrequer uma série de texto sem forma toda a liberdade de texto. 

## <a name="g"></a>G

<a name="geocode"></a>**Geocódigo**: Um endereço ou local que tenha sido convertido numa coordenada que pode ser usada para exibir essa localização num mapa. 

<a name="geocoding"></a>**Geocoding**: Também conhecido como geocodificação avançada, é o processo de conversão do endereço dos dados de localização em coordenadas. 

<a name="geodesic-path"></a>**Caminho geodésico**: O caminho mais curto entre dois pontos numa superfície curva. Quando renderizado no Azure Maps este caminho aparece como uma linha curva devido à projeção do Mercator.

<a name="geofence"></a>**Geofence**: Uma região geográfica definida que pode ser usada para desencadear eventos quando um dispositivo entra ou existe a região.

<a name="geojson"></a>**GeoJSON**: É um formato de ficheiro baseado em JSON comum usado para armazenar dados geográficos de vetoriais, tais como pontos, linhas e polígonos. **Nota:** O Azure Maps utiliza uma versão estendida da GeoJSON como [aqui documentado](extend-geojson.md).

<a name="geometry"></a>**Geometria**: Representa um objeto espacial como um ponto, linha ou polígono.

<a name="geometrycollection"></a>**GeometryCollection**: Uma coleção de objetos de geometria.

<a name="geopol"></a>**GeoPol**: Refere-se a dados geopoliticamente sensíveis, tais como fronteiras disputadas e nomes de locais.

<a name="georeference"></a>**Georeferência**: O processo de alinhamento de dados geográficos ou imagens a um sistema de coordenadas conhecido. Este processo pode consistir em deslocar, rodar, escalar ou distorcer os dados.

<a name="georss"></a>**GeoRSS**: Uma extensão XML para adicionar dados espaciais aos feeds RSS.

<a name="gis"></a>**GIS**: Um acrónimo para "Sistema de Informação Geográfica". Um termo comum usado para descrever a indústria de mapeamento.

<a name="gml"></a>**GML**: Também conhecido como Linguagem de Marcação de Geografia. Uma extensão de ficheiro XML para armazenar dados espaciais.

<a name="gps"></a>**GPS**: Também conhecido como Sistema de Posicionamento Global, é um sistema de satélites utilizado para determinar uma posição de dispositivos na Terra. Os satélites em órbita transmitem sinais que permitem que um recetor GPS em qualquer lugar da Terra calcule a sua própria localização através de uma triposterioração.

<a name="gpx"></a>**GPX**: Também conhecido como formato GPS eXchange, é um formato de ficheiro XML geralmente criado a partir de dispositivos GPS.  

<a name="great-circle-distance"></a>**Grande distância:** A distância mais curta entre dois pontos na superfície de uma esfera.

<a name="greenwich-mean-time-gmt"></a>Hora média de **Greenwich (GMT)** : O tempo no meridiano principal, que atravessa o Observatório Real em Greenwich, Inglaterra.

<a name="guid"></a>**GUID**: Um identificador globalmente único. Uma cadeia usada para identificar exclusivamente uma interface, classe, biblioteca de tipo, categoria de componente ou registo.

## <a name="h"></a>H

<a name="haversine-formula"></a>**Fórmula haversina**: Uma equação comum usada para calcular a distância de grande círculo entre dois pontos numa esfera.

<a name="hd-maps"></a>**Mapas HD**: Também conhecidos como Mapas de Alta Definição, consiste em informações de rede rodoviária de alta fidelidade, tais como marcas de faixas de rodagem, sinalização e luzes de direção necessárias para a condução autónoma.

<a name="heading"></a>**Sentido**: A direção aponta ou está virada. Ver também [Rolamento](#heading).

<a name="heatmap"></a>**Mapa de calor**: Uma visualização de dados em que uma gama de cores representa a densidade de pontos numa determinada área. Ver também mapa temático.

<a name="hybrid-imagery"></a>**Imagens híbridas**: Imagens de satélite ou aéreas que têm dados de estrada e etiquetas sobrepostas em cima.

## <a name="i"></a>I

<a name="iana"></a>**IANA**: Um acrónimo para a Internet Assigned Numbers Authority. Um grupo sem fins lucrativos que supervisiona a atribuição global de endereços IP.

<a name="isochrone"></a>**Isochrone**: Uma isocromona define a área em que alguém pode viajar dentro de um tempo especificado para um modo de transporte em qualquer direção a partir de um determinado local. Ver também [Alcance Acessível](#reachable-range).

<a name="isodistance"></a>**Isodistance**: Dada a localização, uma isochrone define a área em que alguém pode viajar a uma distância especificada para um modo de transporte em qualquer direção. Ver também [Alcance Acessível](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a>**KML**: Também conhecido como Keyhole Markup Language, é um formato de ficheiro XML comum para armazenar dados de vetoriais geográficos, tais como pontos, linhas e polígonos. 

## <a name="l"></a>l

<a name="landsat"></a>**Landsat**: Satélites multiespectrais e orbitais terrestres desenvolvidos pela NASA que recolhem imagens de terra. Esta imagem é usada em muitas indústrias como a agricultura, a silvicultura e a cartografia.

<a name="latitude"></a>**Latitude**: A distância angular medida em graus do equador na direção norte ou sul.

<a name="level-of-detail"></a>**Nível de detalhe**: Ver nível de zoom.

<a name="lidar"></a>**Lidar**: Acrónimo para deteção e variação de luz. Uma técnica de deteção remota que usa lasers para medir distâncias a superfícies refletoras.

<a name="linear-interpolation"></a>**Interpolação linear**: A estimativa de um valor desconhecido utilizando a distância linear entre valores conhecidos.

<a name="linestring"></a>**LineString**: Uma geometria usada para representar uma linha. Também conhecido como poliline. 

<a name="localization"></a>**Localização**: Apoio a diferentes línguas e culturas.

<a name="logistics"></a>**Logística**: O processo de deslocação de pessoas, veículos, fornecimentos ou ativos de forma coordenada.

<a name="longitude"></a>**Longitude**: A distância angular medida em graus do primeiro meridiano na direção leste ou oeste.

## <a name="m"></a>M

<a name="map-tile"></a>**Azulejo do mapa**: Uma imagem retangular que representa uma partição de uma tela de mapa. Para mais informações, consulte os níveis de Zoom e documentação da [grelha de azulejos.](zoom-levels-and-tile-grid.md)

<a name="marker"></a>**Marcador**: Também conhecido como pino ou pino, é um ícone que representa uma localização pontual num mapa.

<a name="mercator-projection"></a>**Projeção do Mercator**: Uma projeção de mapa cilíndrico que se tornou a projeção padrão do mapa para fins náuticos devido à sua capacidade de representar linhas de curso constante, conhecidas como linhas de rumb, como segmentos retos que conservam os ângulos com os meridianos. Todas as projeções planas do mapa distorcem as formas ou tamanhos do mapa quando comparados com o verdadeiro layout da superfície da Terra. A projeção do Mercator exagera em áreas longe do equador, de tal forma que áreas mais pequenas aparecem maiores no mapa à medida que se aproxima dos polos. 

<a name="multilinestring"></a>**MultiLineString**: Uma geometria que representa uma coleção de objetos LineString. 

<a name="multipoint"></a>**MultiPoint**: Uma geometria que representa uma coleção de objetos Point.

<a name="multipolygon"></a>**MultiPolygon**: Uma geometria que representa uma coleção de objetos Polygon. Por exemplo, para mostrar o limite do Havaí, cada ilha seria delineada com um polígono. Assim, a fronteira do Havaí seria, assim, um MultiPolygon.

<a name="municipality"></a>**Município**: Uma cidade ou cidade. 

<a name="municipality-subdivision"></a>**Subdivisão municipal**: Uma subdivisão de um município, como um bairro ou nome de área local como "centro".

## <a name="n"></a>N

<a name="navigation-bar"></a>Barra de **navegação**: O conjunto de controlos num mapa utilizado para ajustar o nível de zoom, o pitch, a rotação e a troca da camada de mapa base.

<a name="nearby-search"></a>**Pesquisa nas proximidades**: Uma consulta espacial que procura uma distância fixa de linha reta (como o corvo voa) a partir de um ponto.

<a name="neutral-ground-truth"></a>**Neutral Ground Truth**: Um mapa que torna os rótulos na língua oficial da região que representa e em scripts locais, se disponível.

## <a name="o"></a>O

<a name="origin"></a>**Origem**: Um ponto de partida ou local em que um utilizador se encontra.

## <a name="p"></a>P

<a name="panning"></a>**Panorâmica**: O processo de mover o mapa em qualquer direção mantendo um nível de zoom constante.

<a name="parcel"></a>**Parcela**: Um lote de terreno ou limite de propriedade.

<a name="pitch"></a>**Pitch**: A quantidade de inclinação que o mapa tem em relação à vertical onde 0 está olhando diretamente para baixo para o mapa.

<a name="point"></a>**Ponto**: Uma geometria que representa uma única posição no mapa. 

<a name="points-of-interest-poi"></a>**Pontos de interesse (POI)** : Um negócio, marco ou local de interesse comum.

<a name="polygon"></a>**Polígono**: Uma geometria sólida que representa uma área num mapa. 

<a name="polyline"></a>**Poliline**: Uma geometria usada para representar uma linha. Também conhecido como LineString. 

<a name="position"></a>**Posição**: Longitude, latitude e altitude (x,y,z coordenadas) de um ponto.

<a name="post-code"></a>**Código postal**: Ver [código postal](#postal-code).

<a name="postal-code"></a>**Código postal**: Uma série de letras ou números, ou ambos, num formato específico. O código postal é utilizado pelo serviço postal de um país/região para dividir as zonas geográficas em zonas, a fim de simplificar a entrega de correio.

<a name="primary-key"></a>**Chave principal**: A primeira de duas teclas de subscrição fornecidas para a autenticação chave partilhada do Azure Maps. Ver [Autenticação da chave partilhada](#shared-key-authentication).

<a name="prime-meridian"></a>**Primeiro meridiano:** Uma linha de longitude que representa uma longitude de 0 graus. Geralmente, os valores de longitude diminuem quando viajam em direção oeste até 180 graus e aumentam quando viajam em direções orientais para -180 graus. 

<a name="prj"></a>**PRJ**: Um ficheiro de texto que acompanha frequentemente um ficheiro Shapefile que contém informações sobre o sistema de coordenadas projetado em que se encontra o conjunto de dados.

<a name="projection"></a>**Projeção**: Um sistema de coordenadas projetado baseado numa projeção de mapa como mercator transversal, área igual albers e Robinson. Estes fornecem a capacidade de projetar mapas da superfície esférica da Terra em um plano de coordenadabia cartesiana bidimensional. Os sistemas de coordenadas projetados são por vezes referidos como projeções de mapas.

## <a name="q"></a>Q

<a name="quadkey"></a>**Quadkey**: Um índice de endereço base-4 para um azulejo dentro de um sistema de azulejos quadtree. Para mais informações, consulte os níveis de Zoom e a documentação da grelha de [azulejos](zoom-levels-and-tile-grid.md) para obter mais informações.

<a name="quadtree"></a>**Quadtree**: Uma estrutura de dados em que cada nó tem exatamente quatro filhos. O sistema de tiling utilizado no Azure Maps utiliza uma estrutura quadtree tal como um utilizador zooms em um nível, cada azulejo de mapa se divide em quatro subtiles.  Para mais informações, consulte os níveis de Zoom e a documentação da grelha de [azulejos](zoom-levels-and-tile-grid.md) para obter mais informações.

<a name="queries-per-second-qps"></a>**Consultas por Segundo (QPS)** : O número de consultas ou pedidos que podem ser feitos a um serviço ou plataforma num segundo. 

## <a name="r"></a>R

<a name="radial-search"></a>**Pesquisa radial**: Uma consulta espacial que procura uma distância fixa de linha reta (como o corvo voa) a partir de um ponto. 

<a name="raster-data"></a>**Dados raster**: Uma matriz de células (ou pixels) organizada em linhas e colunas (ou uma grelha) onde cada célula contém um valor que representa informações, como a temperatura. As de Raster incluem fotografias aéreas digitais, imagens de satélites, imagens digitais e mapas digitalizados.

<a name="raster-layer"></a>**Camada raster**: Uma camada de azulejo que consiste em imagens mais raster.

<a name="reachable-range"></a>**Alcance acessível**: Uma gama alcançável define a área em que alguém pode viajar dentro de um tempo ou distância especificado, para um modo de transporte para viajar, em qualquer direção a partir de um local. Consulte também [Isochrone](#isochrone) e [Isodistance.](#isodistance)

<a name="remote-sensing"></a>**Deteção remota**: O processo de recolha e interpretação dos dados do sensor à distância.

<a name="rest-service"></a>**Serviço DE REPOUSO**: O acrónimo REST significa Transferência De Estado Representativa. Um serviço REST é um serviço web baseado em URL que se baseia em tecnologia web básica para comunicar, sendo os métodos mais comuns os pedidos HTTP GET e POST. Este tipo de serviços tendem-me muito mais rápido e menor do que os serviços tradicionais baseados em SABÃO.

<a name="reverse-geocode"></a>**Geocódigo inverso**: O processo de tomar uma coordenada e determinar o endereço em que está representado num mapa.

<a name="reproject"></a>**Reprojectar**: Ver [Transformação](#transformation).

<a name="rest-service"></a>**Serviço DE REPOUSO**: Acrónimo para Transferência de Estado Representativa. Uma arquitetura para trocar informação entre pares num ambiente descentralizado e distribuído. O REST permite que programas em diferentes computadores se comuniquem independentemente de um sistema operativo ou plataforma. Um serviço pode enviar um pedido de Protocolo de Transferência de Hipertextos (HTTP) a um localizador de recursos uniforme (URL) e obter dados de volta.

<a name="route"></a>**Rota**: Um caminho entre dois ou mais locais, que também pode incluir informações adicionais, tais como instruções para pontos de passagem ao longo do percurso.

<a name="requests-per-second-rps"></a>**Pedidos por Segundo (RPS)** : Ver [consultas por segundo (QPS)](#queries-per-second-qps). 

<a name="rss"></a>**RSS**: Acrónimo para Sindicância Realmente Simples, Quadro de Descrição de Recursos (RDF) Resumo do Sítio, ou Resumo do Sítio Rico, dependendo da fonte. Um formato XML simples e estruturado para a partilha de conteúdos entre diferentes Web sites. Os documentos RSS incluem elementos-chave de metadados, tais como autor, data, título, uma breve descrição e uma ligação de hipertexto. Esta informação ajuda um utilizador (ou um serviço de editor RSS) a decidir quais os materiais que valem a pena investigar mais aprofundadamente.

## <a name="s"></a>S

<a name="satellite-imagery"></a>**Imagens de satélite**: Imagens captadas por aviões e satélites apontando diretamente para baixo.

<a name="secondary-key"></a>**Chave secundária**: A segunda de duas teclas de subscrição fornecidas para a autenticação chave partilhada do Azure Maps. Ver [Autenticação da chave partilhada](#shared-key-authentication).

<a name="shapefile-shp"></a>**Shapefile (SHP)** : Também conhecido como um Shapefile ESRI, é um formato de armazenamento de dados vetores para armazenar a localização, forma e atributos das características geográficas. Um ficheiro de forma é armazenado num conjunto de ficheiros relacionados.

<a name="shared-key-authentication"></a>**Autenticação de chave partilhada**: A autenticação da chave partilhada baseia-se na passagem das chaves geradas pela conta Azure Maps com cada pedido ao Azure Maps. Estas chaves são frequentemente referidas como chaves de subscrição. Recomenda-se que as teclas sejam regularmente regeneradas para segurança. São fornecidas duas teclas para que possa manter as ligações utilizando uma tecla enquanto regenera a outra. Quando regenerar as chaves, tem de atualizar quaisquer aplicações que acedam a esta conta para utilizar as novas teclas. Para saber mais sobre a autenticação do Azure Maps, consulte [o Azure Maps e o Azure AD](azure-maps-authentication.md) e [gerea a autenticação no Azure Maps.](how-to-manage-authentication.md)

<a name="software-development-kit-sdk"></a>Kit de desenvolvimento de **software (SDK)** : Uma coleção de documentação, código de amostra e aplicações de amostra para ajudar um desenvolvedor a usar uma API para construir aplicações.

<a name="spherical-mercator-projection"></a>**Projeção esférica do Mercator**: Ver [Web Mercator](#web-mercator). 

<a name="spatial-query"></a>**Consulta espacial**: Um pedido feito a um serviço que realiza uma operação espacial. Como uma busca radial, ou ao longo de uma busca de rota.

<a name="spatial-reference"></a>**Referência espacial**: Um sistema local, regional ou global baseado em coordenadas utilizado para localizar precisamente entidades geográficas. Define o sistema de coordenadas usado para relacionar as coordenadas do mapa com locais no mundo real. As referências espaciais garantem que os dados espaciais de diferentes camadas, ou fontes, podem ser integrados para visualização ou análise precisas. O Azure Maps utiliza o sistema de referência de coordenadas [EPSG:3857](https://epsg.io/3857) e o WGS 84 para dados de geometria de entrada.

<a name="sql-spatial"></a>**Espacial SQL**: Refere-se à funcionalidade espacial incorporada no SQL Azure e no SQL Server 2008 ou acima. Esta funcionalidade espacial também está disponível como uma biblioteca .NET que pode ser usada independentemente do SQL Server. Para mais informações, consulte a documentação de [Dados Espaciais (SQL Server)](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) para obter mais informações.

<a name="subscription-key"></a>**Chave de subscrição**: Ver [autenticação da chave partilhada](#shared-key-authentication).

<a name="synchronous-request"></a>**Pedido sincronizado**: Um pedido http abre uma ligação e aguarda uma resposta. Os navegadores limitam o número de pedidos http simultâneos que podem ser feitos a partir de uma página. Se vários pedidos sincronizados de longa duração forem feitos ao mesmo tempo, então este limite pode ser atingido. Os pedidos serão adiados até que um dos outros pedidos esteja concluído.

## <a name="t"></a>T

<a name="telematics"></a>**Telemática**: Envio, receção e armazenamento de informações através de dispositivos de telecomunicações em conjunto com o controlo de efetuar objetos remotos. 

<a name="temporal-data"></a>**Dados temporais**: Dados que se referem especificamente a tempos ou datas. Os dados temporais podem referir-se a eventos discretos, tais como relâmpagos; objetos em movimento, como os comboios; ou observações repetidas, tais como contagens de sensores de tráfego.

<a name="terrain"></a>**Terreno**: Uma área de terreno com uma característica particular, como terreno arenoso ou terreno montanhoso.

<a name="thematic-maps"></a>**Mapas temáticos**: Um mapa temático é um mapa temático simples feito para refletir um tema sobre uma área geográfica. Um cenário comum para este tipo de mapa é colorir as regiões administrativas, como países/regiões com base em alguma métrica de dados.

<a name="tile-layer"></a>**Camada de azulejos**: Uma camada exibida através da montagem de azulejos de mapa (secções retangulares) numa camada contínua. Os azulejos são ou azulejos de imagem raster ou azulejos vetoriais. As camadas de azulejos raster são normalmente renderizadas antes do tempo, e são armazenadas como imagens num servidor. Camadas de azulejos raster podem usar um grande espaço de armazenamento. As camadas de azulejos vetores são renderizadas perto do tempo real dentro da aplicação do cliente. Assim, os requisitos de armazenamento do lado do servidor são menores para camadas de azulejos vetores.

<a name="time-zone"></a>**Fuso horário**: Uma região do globo que observa um tempo padrão uniforme para fins legais, comerciais e sociais. Os fusos horários tendem a seguir os limites dos países/regiões e das suas subdivisões.

<a name="transaction"></a>**Transação**: O Azure Maps utiliza um modelo de licenciamento transacional onde;

- Uma transação é criada por cada 15 mapas ou telhas de tráfego solicitadas.
- Uma transação é criada para cada chamada da API para um dos serviços no Azure Maps. Pesquisa e encaminhamento são exemplos do serviço Azure Maps.

<a name="transformation"></a>**Transformação**: O processo de conversão de dados entre diferentes sistemas de coordenadas geográficas. Pode, por exemplo, ter alguns dados que foram capturados no Reino Unido e baseados no sistema de coordenadas geográficas OSGB de 1936. O Azure Maps utiliza a variante do sistema de referência de coordenadas [EPSG:3857](https://epsg.io/3857) do WGS84. Como tal, para exibir corretamente os dados, terá de ter as suas coordenadas transformadas de um sistema para outro.

<a name="traveling-salesmen-problem-tsp"></a>**Problema dos Vendedores Itinerantes (TSP)** : Um problema no circuito Hamiltoniano em que um vendedor deve encontrar a forma mais eficiente de visitar uma série de paragens e, em seguida, voltar ao local de partida.  

<a name="trilateration"></a>**Tritardeation**: O processo de determinação da posição de um ponto na superfície da Terra, no que diz respeito a dois outros pontos, medindo as distâncias entre os três pontos.

<a name="turn-by-turn-navigation"></a>**Navegação por turnos**: Uma aplicação que fornece instruções de rota para cada passo de uma rota à medida que os utilizadores se aproximam da próxima manobra.

## <a name="v"></a>V

<a name="vector-data"></a>**Dados vetores**: Coordenar dados baseados que são representados como pontos, linhas ou polígonos.

<a name="vector-tile"></a>**Azulejo vetorial**: Uma especificação de dados aberta para armazenar dados geoespaciais de vetor usando o mesmo sistema de azulejos que o controlo do mapa. Ver também [camada de azulejos.](#tile-layer)

<a name="vehicle-routing-problem-vrp"></a>Problema de **Encaminhamento de Veículos (VRP)** : Uma classe de problemas, em que um conjunto de rotas ordenadas para uma frota de veículos é calculado, tendo em conta como conjunto de restrições. Estes constrangimentos podem incluir janelas de tempo de entrega, múltiplas capacidades de rota e restrições de duração de viagem.

<a name="voronoi-diagram"></a>**Diagrama voronoi**: Uma divisão do espaço em áreas, ou células, que rodeiam um conjunto de objetos geométricos, geralmente apontam características. Estas células, ou polígonos, devem satisfazer os critérios para os triângulos delaunay. Todas as localizações dentro de uma área estão mais próximas do objeto que rodeia do que de qualquer outro objeto no conjunto. Os diagramas de Voronoi são frequentemente usados para delinear áreas de influência em torno de características geográficas. 

## <a name="w"></a>W

<a name="waypoint"></a>**Ponto de passagem**: Um ponto de passagem é uma localização geográfica especificada definida pela longitude e latitude que é utilizada para fins de navegação. Muitas vezes costumava representar um ponto em que alguém navega por uma rota.

<a name="waypoint-optimization"></a>**Otimização**do Waypoint : O processo de reordenar um conjunto de pontos de passagem para minimizar o tempo de viagem ou a distância necessário para passar por todos os pontos de passagem fornecidos. Dependendo da complexidade da otimização, esta otimização é muitas vezes referida como o problema dos [vendedores ambulantes](#traveling-salesmen-problem-tsp) ou problema de [encaminhamento](#vehicle-routing-problem-vrp)de veículos.

<a name="web-map-service-wms"></a>**Web Map Service (WMS)** : WMS é um padrão do Open Geographic Consortium (OGC) que define serviços de mapas baseados em imagem. Os serviços wMS fornecem imagens de mapas para áreas específicas dentro de um mapa a pedido. As imagens incluem simbologia pré-renderizada e podem ser renderizadas num dos vários estilos nomeados se definidos pelo serviço.

<a name="web-mercator"></a>**Web Mercator**: Também conhecido como projeção esférica mercator. É uma ligeira variante da projeção do Mercator, usada principalmente em programas de mapeamento baseados na Web. Usa as mesmas fórmulas que a projeção padrão do Mercator usada para mapas de pequena escala. No entanto, o Web Mercator usa as fórmulas esféricas em todas as escalas, mas os mapas mercator em larga escala normalmente usam a forma elipsoidal da projeção. A discrepância é impercetível à escala global, mas faz com que mapas de áreas locais se desviem ligeiramente dos verdadeiros mapas do Mercator elipsoidal, na mesma escala.

<a name="wgs84"></a>**WGS84**: Um conjunto de constantes usadas para relacionar as coordenadas espaciais com as localizações na superfície do mapa. O datum WGS84 é o padrão usado pela maioria dos fornecedores de mapeamento on-line e dispositivos GPS. O Azure Maps utiliza a variante do sistema de referência de coordenadas [EPSG:3857](https://epsg.io/3857) do WGS84.

## <a name="z"></a>Z

<a name="z-coordinate"></a>**Coordenada Z**: Ver [Altitude](#altitude). 

<a name="zip-code"></a>**Código postal**: Ver [código postal](#postal-code).

<a name="Zoom level"></a>**Nível**de zoom: Especifica o nível de detalhe e a quantidade do mapa visível. Quando zoomed todo o caminho para o nível 0, o mapa do mundo completo será muitas vezes visível. Mas, o mapa mostrará detalhes limitados, como nomes de país/região, fronteiras e nomes oceânicos. Quando ampliado mais perto do nível 17, o mapa mostrará uma área de alguns quarteirões da cidade com informações detalhadas sobre a estrada. Nos mapas azuis, o nível de zoom mais alto é 22. Para mais informações, consulte os níveis de Zoom e documentação da [grelha de azulejos.](zoom-levels-and-tile-grid.md)

