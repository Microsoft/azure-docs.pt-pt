---
title: Glossário do Azure Maps | Microsoft Docs
description: Um glossário de termos comumente usados associados aos mapas do Azure, serviços baseados na localização e GIS.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 02bf5ba30a1fc7b4ee739cb0a591ffe084269541
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75408862"
---
# <a name="glossary"></a>Glossário

Veja a seguir uma lista de palavras comuns usadas com mapas do Azure.

## <a name="a"></a>A

<a name="address-validation"></a>**Validação de endereço**: o processo de verificar a existência de um endereço.

<a name="advanced-routing"></a>**Roteamento avançado**: uma coleção de serviços que executam operações avançadas usando dados de roteamento de estrada, como calcular intervalos acessíveis (isócronas), matrizes de distância e solicitações de rota de lote.

<a name="aerial-imagery"></a>**Imagens aéreas**: consulte [imagens satélite](#satellite-imagery). 

<a name="along-a-route-search"></a>**Ao longo de uma pesquisa de rota**: uma consulta espacial que procura dados que estão dentro de um determinado tempo de desvio ou distância de um caminho de rota.

<a name="altitude"></a>**Altitude**: a altura ou a elevação vertical de um ponto acima de uma superfície de referência. As medidas de altitude baseiam-se em uma determinada Datum de referência, como nível médio do mar. Consulte também elevação.

<a name="ambiguous"></a>**Ambíguo**: um estado de incerteza na classificação de dados que existe quando um objeto pode ser atribuído adequadamente a dois ou mais valores para um determinado atributo. Por exemplo, ao geocodificar "CA", dois resultados ambíguos são retornados; "Canadá" e "Califórnia" como "CA" é um código de país e estado para cada um, respectivamente. 

<a name="annotation"></a>**Anotação**: texto ou elementos gráficos exibidos no mapa para fornecer informações ao usuário. A anotação pode identificar ou descrever uma entidade de mapa específica, fornecer informações gerais sobre uma área no mapa ou fornecer informações sobre o próprio mapa.

<a name="antimeridian"></a>**Antimeridiano**: também conhecido como<sup>o meridiano 180</sup> , é o ponto em que-180 graus e 180 graus de longitude se encontram. Que é oposto ao principal meridiano do globo.

<a name="application-programming-interface-api"></a>**API (interface de programação de aplicativo)** : uma especificação que permite aos desenvolvedores criar aplicativos.

<a name="api-key"></a>**Chave de API**: consulte [autenticação de chave compartilhada](#shared-key-authentication).

<a name="area-of-interest-aoi"></a>**Área de interesse (Aoi)** : a extensão usada para definir uma área de foco para uma produção de mapa ou de banco de dados.

<a name="asset-tracking"></a>**Acompanhamento de ativos**: o processo de rastrear o local de um ativo, como uma pessoa, um veículo ou algum outro objeto.

<a name="asynchronous-request"></a>**Solicitação assíncrona**: uma solicitação HTTP que abre uma conexão e faz uma solicitação ao servidor que retorna um identificador para a solicitação assíncrona e, em seguida, fecha a conexão. O servidor continua processando a solicitação e o usuário pode verificar o status usando o identificador. Quando a solicitação terminar o processamento, o usuário poderá baixar a resposta. Esse tipo de solicitação é usado para processos de longa execução.

<a name="autocomplete"></a>**Preenchimento automático**: um recurso em um aplicativo prevê o restante de uma palavra que um usuário está digitando. 

<a name="autosuggest"></a>**Sugestão**automática: um recurso em um aplicativo o prevê possibilidades lógicas para o que o usuário está digitando.

<a name="azure-location-based-services-lbs"></a>**Serviços do Azure baseados na localização (lbs)** : o nome anterior do Azure Maps quando ele estava em versão prévia.

<a name="azure-active-directory"></a>**Azure Active Directory (AD do Azure)** : o Azure AD é o serviço de gerenciamento de identidade e acesso baseado em nuvem da Microsoft. A integração do Azure AD com o Azure Maps está disponível atualmente em versão prévia para todas as APIs do Azure Maps. O Azure AD dá suporte ao RBAC (controle de acesso baseado em função) para permitir o acesso refinado aos recursos do Azure Maps. Para saber mais sobre a integração do Azure AD do Azure, consulte [Azure Maps e Azure ad](azure-maps-authentication.md) e [gerenciar a autenticação no Azure Maps](how-to-manage-authentication.md).

<a name="azure-maps-key"></a>**Chave do Azure Maps**: consulte [autenticação de chave compartilhada](#shared-key-authentication).

## <a name="b"></a>B

<a name="base-map"></a>**Mapa base**: a parte de um aplicativo de mapa que exibe informações de referência de plano de fundo, como estradas, pontos de referência e limites políticos.

<a name="batch-request"></a>**Solicitação em lote**: o processo de combinar várias solicitações em uma única solicitação.

<a name="bearing"></a>**Rumo**: a direção horizontal de um ponto em relação a outro ponto. Isso é expresso como um ângulo em relação ao norte, de 0 a 360 graus em uma direção horária. 

<a name="boundary"></a>**Limite**: uma linha ou polígono que separa entidades políticas adjacentes, como países/regiões, distritos e propriedades. Um limite é uma linha que pode ou não seguir recursos físicos, como rios, montanhas ou paredes.

<a name="bounds"></a>**Limites**: consulte a [caixa delimitadora](#bounding-box).

<a name="bounding-box"></a>**Caixa delimitadora**: um conjunto de coordenadas usado para representar uma área retangular no mapa. 

## <a name="c"></a>C

<a name="cadastre"></a>**Cadastre**: um registro de Land e propriedades registradas. Confira também [remessa](#parcel).

<a name="camera"></a>**Câmera**: no contexto de um controle de mapa interativo, uma câmera define o campo mapas da exibição. O visor da câmera é determinado com base em vários parâmetros de mapa; Centro, nível de zoom, inclinação, rumo. 

<a name="centroid"></a>**Centróide**: o centro geométrico de um recurso. O centróide de uma linha seria o ponto médio, enquanto o centróide de um polígono seria seu centro de área.

<a name="choropleth-map"></a>**Mapa de coropléticos**: um mapa de temática em que as áreas são sombreadas em proporção à medida de uma variável estatística que está sendo exibida no mapa. Por exemplo, colorir o limite de cada Estado dos EUA com base em sua população relativa para todos os outros Estados.

<a name="concave-hull"></a>**Envoltória côncavo**: uma forma que representa uma possível geometria côncavo que inclui todas as formas no conjunto de dados especificado. A forma gerada é semelhante a encapsular os dados com quebra de plástico e, em seguida, Calefação-los, causando, assim, grandes intervalos entre pontos para os côncavos em direção a outros pontos de dados.

<a name="consumption-model"></a>**Modelo de consumo**: informações que definem a taxa na qual um veículo consome combustível ou eletricidade. Consulte também a [documentação do modelo de consumo](consumption-model.md).

<a name="control"></a>**Controle**: um componente independente ou reutilizável que consiste em uma interface gráfica do usuário que define um conjunto de comportamentos para a interface. Por exemplo, um controle de mapa, geralmente é a parte da interface do usuário que carrega um mapa interativo.

<a name="convex-hull"></a>**Convexa envoltória**: uma convexa envoltória é uma forma que representa a geometria mínima de convexa que inclui todas as formas no conjunto de dados especificado. A forma gerada é semelhante a encapsular uma faixa elástica ao redor do conjunto de dados.

<a name="coordinate"></a>**Coordenada**: consiste nos valores de longitude e latitude usados para representar um local em um mapa.

<a name="coordinate-system"></a>**Sistema de coordenadas**: uma estrutura de referência usada para definir as posições de pontos no espaço em duas ou três dimensões.

<a name="country-code"></a>**Código do país**: um identificador exclusivo para um país/região com base no padrão ISO. ISO2 é um código de dois caracteres para um país (por exemplo, EUA), que ISO3 representa um código de três caracteres (por exemplo, EUA).

<a name="country-subdivision"></a>**Subdivisão do país**: uma subdivisão de primeiro nível de um país/região, normalmente conhecida como estado ou província.

<a name="country-secondary-subdivision"></a>**Subdivisão secundária do país**: uma subdivisão de segundo nível de um país/região, normalmente conhecida como um município.

<a name="country-tertiary-subdivision"></a>**Subdivisão terciário de país**: uma subdivisão de terceiro nível de um país/região, normalmente uma área nomeada, como uma à frente.

<a name="cross-street"></a>**Entre ruas**: um ponto em que duas ou mais ruas se cruzam.

<a name="cylindrical-projection"></a>**Projeção cilíndrica**: uma projeção que transforma pontos de uma preheroid ou esfera em um cilindro tangente ou secante. O cilindro é então dividido de cima para baixo e achatado em um plano.

## <a name="d"></a>D

<a name="datum"></a>**Datum**: as especificações de referência de um sistema de medidas, um sistema de posições de coordenadas em uma superfície (uma referência horizontal) ou alturas acima ou abaixo de uma superfície (uma referência vertical).

<a name="dbf-file"></a>**Arquivo DBF**: um formato de arquivo de banco de dados que é usado em combinação com forma (SHP).

<a name="degree-minutes-seconds-dms"></a>**Graus minutos segundos (DMS)** : a unidade de medida para descrever a latitude e a longitude. Um grau é 1/360<sup>th</sup> de um círculo. Um grau é dividido em 60 minutos e um minuto é dividido em 60 segundos.

<a name="delaunay-triangulation"></a>**Triangulação Delaunay**: uma técnica para criar uma malha de triângulos contíguos e não sobrepostos a partir de um conjunto de pontos. O círculo circumscribing de cada triângulo não contém pontos do conjunto de um em seu interior.

<a name="demographics"></a>Dados **demográficos**: as características estatísticas (como idade, taxa de nascimento e renda) de uma população humana.

<a name="destination"></a>**Destino**: um ponto de extremidade ou local no qual alguém está viajando.

<a name="digital-elevation-model-dem"></a>**DEM (modelo de elevação digital)** : um conjunto de dado de valores de elevação relacionados a uma superfície, capturados em uma área em intervalos regulares usando uma Datum comum. DEMs normalmente são usados para representar a relevo do terreno.

<a name="dijkstra's-algorithm"></a>**Algoritmo do Dijkstra**: um algoritmo que examina a conectividade de uma rede para localizar o caminho mais curto entre dois pontos.

<a name="distance-matrix"></a>**Matriz de distância**: uma matriz que contém informações de tempo de viagem e distância entre um conjunto de origens e destinos. 

## <a name="e"></a>E

<a name="elevation"></a>**Elevação**: a distância vertical de um ponto ou objeto acima ou abaixo de uma superfície de referência ou Datum (geralmente significa nível de mar). A elevação geralmente se refere à altura vertical do terreno.

<a name="envelope"></a>**Envelope**: consulte a [caixa delimitadora](#bounding-box).

<a name="extended-postal-code"></a>CEP **estendido**: um código postal que pode incluir informações adicionais. Por exemplo, nos EUA, CEPs têm cinco dígitos, mas um CEP estendido, conhecido como zip + 4, incluirá quatro dígitos adicionais. Esses dígitos adicionais são usados para identificar um segmento geográfico dentro da área de entrega de cinco dígitos, como um bloco de cidade, um grupo de Apartments ou uma caixa de postagem, que ajuda em classificação e entrega de email eficientes.

<a name="extent"></a>**Extensão**: consulte a [caixa delimitadora](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a>**Autenticação federada**: um método de autenticação que permite que um único mecanismo de logon/autenticação seja usado em vários aplicativos Web e móveis. 

<a name="feature"></a>**Feature**: um objeto que combina uma geometria com informações de metadados adicionais. 

<a name="feature-collection"></a>**Coleção de recursos**: uma coleção de objetos de recurso.

<a name="find-along-route"></a>**Localizar ao longo da rota**: uma consulta espacial que procura dados que estão dentro de uma hora ou distância de desvio especificada de um caminho de rota.

<a name="find-nearby"></a>**Localizar próximo**: uma consulta espacial que pesquisa uma distância de linha reta fixa (à medida que as galinha surge) de um ponto.

<a name="fleet-management"></a>**Gerenciamento de frota**: o gerenciamento de veículos comerciais, como carros, caminhões, veículos e aviões. O gerenciamento de frota pode incluir uma variedade de funções, como financiamento de veículo, manutenção, telecondutores (rastreamento e diagnóstico), bem como driver, velocidade, combustível e gerenciamento de integridade e segurança. O gerenciamento de frota é um processo usado por empresas que dependem de transporte em seus negócios para minimizar os riscos e reduzir os custos gerais com o transporte e a equipe, garantindo a conformidade com a legislação governamental.

<a name="free-flow-speed"></a>**Velocidade de fluxo livre**: a velocidade de fluxo livre esperada sob condições ideais. Geralmente o limite de velocidade.

<a name="free-form-address"></a>**Endereço de formulário livre**: um endereço completo que é representado como uma única linha de texto.

<a name="fuzzy-search"></a>**Pesquisa difusa**: uma pesquisa que usa uma cadeia de caracteres de forma livre de texto que pode ser um endereço ou um ponto de interesse. 

## <a name="g"></a>G

<a name="geocode"></a>**Geocode**: um endereço ou local que foi convertido em uma coordenada que pode ser usada para exibir esse local em um mapa. 

<a name="geocoding"></a>**Geocodificação**: também conhecido como a geocodificação direta, é o processo de converter o endereço de dados de localização em coordenadas. 

<a name="geodesic-path"></a>**Caminho de poliedro**: o caminho mais curto entre dois pontos em uma superfície curva. Quando renderizado no Azure Maps, esse caminho aparece como uma linha curva devido à projeção Mercator.

<a name="geofence"></a>**Limite**geograficamente: uma região geográfica definida que pode ser usada para disparar eventos quando um dispositivo entra ou existe na região.

<a name="geojson"></a>**Geojson**: é um formato de arquivo comum baseado em JSON usado para armazenar dados de vetor geográfico, como pontos, linhas e polígonos. **Observação**: o Azure Maps usa uma versão estendida do geojson, conforme [documentado aqui](extend-geojson.md).

<a name="geometry"></a>**Geometry**: representa um objeto espacial, como um ponto, uma linha ou um polígono.

<a name="geometrycollection"></a>**GeometryCollection**: uma coleção de objetos Geometry.

<a name="geopol"></a>**GeoPol**: refere-se a dados confidenciais georelacionados, como bordas contestadas e nomes de lugares.

<a name="georeference"></a>**Referência**geográfica: o processo de alinhar dados geográficos ou imagens a um sistema de coordenadas conhecido. Esse processo pode consistir em mudar, girar, dimensionar ou distorcer os dados.

<a name="georss"></a>**GeoRSS**: uma extensão XML para adicionar dados espaciais a RSS feeds.

<a name="gis"></a>**GIS**: um acrônimo para "sistema de informações geográficas". Um termo comum usado para descrever o setor de mapeamento.

<a name="gml"></a>**GML**: também conhecido como linguagem de marcação de Geografia. Uma extensão de arquivo XML para armazenar dados espaciais.

<a name="gps"></a>**GPS**: também conhecido como GPS, é um sistema de satélites usado para determinar uma posição de dispositivos na terra. Os satélites de órbitas transmitem sinais que permitem que um receptor de GPS em qualquer lugar da terra Calcule seu próprio local por meio de uma última.

<a name="gpx"></a>**GPX**: também conhecido como formato de intercâmbio GPS, é um formato de arquivo XML normalmente criado a partir de dispositivos GPS.  

<a name="great-circle-distance"></a>**Distância de grande círculo**: a distância mais curta entre dois pontos na superfície de uma esfera.

<a name="greenwich-mean-time-gmt"></a>**Greenwich Mean Time (GMT)** : a hora no meridiano principal, que é executado por meio do Royal Observatório na Greenwich, Inglaterra.

<a name="guid"></a>**GUID**: um identificador global exclusivo. Uma cadeia de caracteres usada para identificar exclusivamente uma interface, classe, biblioteca de tipos, categoria de componente ou registro.

## <a name="h"></a>H

<a name="haversine-formula"></a>**Fórmula Haversine**: uma equação comum usada para calcular a distância de grande círculo entre dois pontos em uma esfera.

<a name="hd-maps"></a>**Mapas de HD**: também conhecidos como mapas de alta definição, consistem em informações de rede de estrada de alta fidelidade, como marcações de raias, pôsteres e luzes de direção necessárias para a condução autônoma.

<a name="heading"></a>**Título**: a direção que algo está apontando ou virando. Veja também a [influência](#heading).

<a name="heatmap"></a>**Calor**: uma visualização de dados na qual um intervalo de cores representa a densidade de pontos em uma área específica. Consulte também temática Map.

<a name="hybrid-imagery"></a>**Imagens híbridas**: imagens satélite ou aéreas que têm dados de estrada e rótulos sobrepostos sobre ele.

## <a name="i"></a>I

<a name="iana"></a>**IANA**: um acrônimo para a autoridade de números de Internet atribuídos. Um grupo sem fins lucrativos que supervisiona a alocação de endereço IP global.

<a name="isochrone"></a>**Isochrone**: um Isochrone define a área na qual alguém pode viajar dentro de um tempo especificado para um modo de transporte em qualquer direção a partir de um determinado local. Consulte também [intervalo acessível](#reachable-range).

<a name="isodistance"></a>**Isodistance**: dado um local, um isochrone define a área na qual alguém pode viajar dentro de uma distância especificada para um modo de transporte em qualquer direção. Consulte também [intervalo acessível](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a>**KML**: também conhecido como linguagem de marcação Keyhole, é um formato de arquivo XML comum para armazenar dados vetoriais geográficos, como pontos, linhas e polígonos. 

## <a name="l"></a>L

<a name="landsat"></a>**Landsat**: Multispectral, satélites de alta-órbita desenvolvidos pela NASA que coletam imagens do Land que são usadas em muitos setores, como agricultura, florestal e cartografia.

<a name="latitude"></a>**Latitude**: a distância angular medida em graus de Equador em uma direção norte ou Sul.

<a name="level-of-detail"></a>**Nível de detalhe**: consulte nível de zoom.

<a name="lidar"></a>**Lidar**: acrônimo para detecção de luz e variação. Uma técnica de detecção remota que usa lasers para medir as distâncias para superfícies refletidas.

<a name="linear-interpolation"></a>**Interpolação linear**: a estimativa de um valor desconhecido usando a distância linear entre os valores conhecidos.

<a name="linestring"></a>**LineString**: uma geometria usada para representar uma linha. Também conhecido como uma polilinha. 

<a name="localization"></a>**Localização**: suporte para diferentes idiomas e culturas.

<a name="logistics"></a>**Logística**: o processo de mover pessoas, veículos, suprimentos ou ativos de forma coordenada.

<a name="longitude"></a>**Longitude**: a distância angular medida em graus a partir do meridiano principal em uma direção leste ou oeste.

## <a name="m"></a>M

<a name="map-tile"></a>**Bloco de mapa**: uma imagem retangular que representa uma partição de uma tela de mapa. Para obter mais informações, consulte a [documentação níveis de zoom e grade de blocos](zoom-levels-and-tile-grid.md).

<a name="marker"></a>**Marcador**: também conhecido como PIN ou pino, é um ícone que representa um local de ponto em um mapa.

<a name="mercator-projection"></a>**Projeção de Mercator**: uma projeção de mapa cilíndrico que se tornou a projeção de mapa padrão para fins náuticas devido à sua capacidade de representar linhas de curso constante, conhecidas como linhas Rhumb, como segmentos retos que conservam os ângulos com o meridianos. Todas as projeções de mapa simples distorcem as formas ou os tamanhos do mapa quando comparados ao layout verdadeiro da superfície da terra. A projeção Mercator exagera as áreas longe do Equador, de modo que as áreas menores pareçam maiores no mapa à medida que você se aproxima do polos. 

<a name="multilinestring"></a>**MultiLineString**: uma geometria que representa uma coleção de objetos LineString. 

<a name="multipoint"></a>**MultiPoint**: uma geometria que representa uma coleção de objetos Point.

<a name="multipolygon"></a>**MultiPolygon**: uma geometria que representa uma coleção de objetos Polygon. Por exemplo, para mostrar o limite do Havaí, cada ilha seria descrita com um polígono, e o limite do Havaí seria, portanto, um MultiPolygon.

<a name="municipality"></a>**Município**: uma cidade ou cidade. 

<a name="municipality-subdivision"></a>**Subdivisão de municipalidade**: uma subdivisão de um município, como um ambiente ou um nome de área local, como "centro".

## <a name="n"></a>N

<a name="navigation-bar"></a>**Barra de navegação**: o conjunto de controles em um mapa usado para ajustar o nível de zoom, a densidade, a rotação e a alternância da camada do mapa base.

<a name="nearby-search"></a>**Pesquisa próxima**: uma consulta espacial que pesquisa uma distância de linha reta fixa (à medida que as galinha surge) de um ponto.

<a name="neutral-ground-truth"></a>Linguagem de **aterramento neutra**: um mapa que renderiza rótulos no idioma oficial da região que ele representa e em scripts locais, se disponíveis.

## <a name="o"></a>O

<a name="origin"></a>**Origem**: um ponto de partida ou local em que um usuário é.

## <a name="p"></a>P

<a name="panning"></a>**Panorâmica**: o processo de mover o mapa em qualquer direção enquanto mantém um nível de zoom constante.

<a name="parcel"></a>**Remessa**: um gráfico de terra ou limite de propriedade.

<a name="pitch"></a>**Pitch**: a quantidade de inclinação que o mapa tem em relação à vertical em que 0 está olhando diretamente para baixo no mapa.

<a name="point"></a>**Point**: uma geometria que representa uma única posição no mapa. 

<a name="points-of-interest-poi"></a>**POI (pontos de interesse)** : um negócio, um ponto de referência ou um local comum de interesse.

<a name="polygon"></a>**Polygon**: uma geometria sólida que representa uma área em um mapa. 

<a name="polyline"></a>**Polilinha**: uma geometria usada para representar uma linha. Também conhecido como uma LineString. 

<a name="position"></a>**Posição**: a longitude, a latitude e a altitude (coordenadas x, y, z) de um ponto.

<a name="post-code"></a>**Código post**: consulte o [CEP](#postal-code).

<a name="postal-code"></a>**CEP**: uma série de letras ou números, ou ambos, em um formato específico, usado pelo serviço postal de um país/região para dividir áreas geográficas em zonas a fim de simplificar a entrega de emails.

<a name="primary-key"></a>**Chave primária**: a primeira de duas chaves de assinaturas fornecidas para a autenticação de chave compartilhada do Azure Maps. Consulte [autenticação de chave compartilhada](#shared-key-authentication).

<a name="prime-meridian"></a>**Meridiano principal**: uma linha de longitude que representa a longitude de 0 graus. Em geral, os valores de longitude diminuem quando viajam em uma direção de WESTERLY até 180 graus e aumentam quando viajam em direções de ponta a 180 graus. 

<a name="prj"></a>**PRJ**: um arquivo de texto que geralmente acompanha um arquivo de formafile que contém informações sobre o sistema de coordenadas projetado no qual o conjunto de dados está.

<a name="projection"></a>**Projeção**: um sistema de coordenadas projetado com base em uma projeção de mapa, como transversal Mercator, área igual a Albers e Robinson. Eles fornecem a capacidade de projetar mapas da superfície esférica da terra em um plano de coordenadas cartesianas bidimensional. Os sistemas de coordenadas projetados às vezes são chamados de projeções de mapa.

## <a name="q"></a>PERGUNTAS E

<a name="quadkey"></a>**Quadkey**: um índice de endereço de base 4 para um bloco dentro de um sistema de divisão de quadtree. Para obter mais informações, consulte [níveis de zoom e](zoom-levels-and-tile-grid.md) documentação da grade de blocos para obter mais informações.

<a name="quadtree"></a>**Quadtree**: uma estrutura de dados na qual cada nó tem exatamente quatro filhos. O sistema de divisão usada no Azure Maps usa uma estrutura quadtree de modo que, como um usuário se aplique zoom em um nível, cada bloco de mapa é dividido em quatro subblocos.  Para obter mais informações, consulte [níveis de zoom e](zoom-levels-and-tile-grid.md) documentação da grade de blocos para obter mais informações.

<a name="queries-per-second-qps"></a>**Consultas por segundo (QPS)** : o número de consultas ou solicitações que podem ser feitas em um serviço ou plataforma dentro de um segundo. 

## <a name="r"></a>R.

<a name="radial-search"></a>**Pesquisa radial**: uma consulta espacial que pesquisa uma distância de linha reta fixa (à medida que as galinha surge) de um ponto. 

<a name="raster-data"></a>**Dados de varredura**: uma matriz de células (ou pixels) organizada em linhas e colunas (ou em uma grade) em que cada célula contém um valor que representa informações, como temperatura. A rasterização inclui fotografias aéreas digitais, imagens de satélites, fotos digitais e mapas digitalizados.

<a name="raster-layer"></a>**Camada de rasterização**: uma camada de bloco que consiste em imagens rasterizadas.

<a name="reachable-range"></a>**Intervalo acessível**: um intervalo acessível define a área na qual alguém pode viajar dentro de uma determinada hora ou distância, para que um modo de transporte percorra, em qualquer direção, de um local. Consulte também [Isochrone](#isochrone) e [Isodistance](#isodistance).

<a name="remote-sensing"></a>**Detecção remota**: o processo de coleta e interpretação de dados de sensor de uma distância.

<a name="rest-service"></a>**Serviço REST**: o acrônimo REST significa transferência de estado de reapresentação. Um serviço REST é um serviço Web baseado em URL que depende da tecnologia básica da Web para se comunicar, os métodos mais comuns sendo solicitações HTTP GET e POST. Esses tipos de serviços tendem a me muito mais rápidos e menores do que os serviços tradicionais baseados em SOAP.

<a name="reverse-geocode"></a>**Reverter código**: o processo de fazer uma coordenada e determinar o endereço em que é representado em um mapa.

<a name="reproject"></a>**Reprojetar**: consulte [transformação](#transformation).

<a name="rest-service"></a>**Serviço REST**: acrônimo para transferência de estado de reapresentação. Uma arquitetura para trocar informações entre os pares em um ambiente distribuído e descentralizado. O REST permite que programas em computadores diferentes se comuniquem independentemente de um sistema operacional ou plataforma enviando uma solicitação HTTP (Hypertext Transfer Protocol) para um Uniform Resource Locator (URL) e obter dados de volta.

<a name="route"></a>**Rota**: um caminho entre dois ou mais locais, que também podem incluir informações adicionais, como instruções para Marcos ao longo da rota.

<a name="requests-per-second-rps"></a>**Solicitações por segundo (RPS)** : consulte [consultas por segundo (QPS)](#queries-per-second-qps). 

<a name="rss"></a>**RSS**: acrônimo para distribuição realmente simples, Resumo do site de estrutura de descrição de recursos (RDF) ou Resumo de site avançado, dependendo da origem. Um formato XML simples e estruturado para compartilhar conteúdo entre diferentes sites da Web. Os documentos RSS incluem elementos de metadados-chave, como autor, data, título, uma breve descrição e um link de hipertexto. Essas informações ajudam um usuário (ou um serviço de Publicador RSS) a decidir quais materiais valem a pena investigar.

## <a name="s"></a>S

<a name="satellite-imagery"></a>**Imagens satélite**: imagens que foram capturadas por planos e satélites apontando diretamente para baixo.

<a name="secondary-key"></a>**Chave secundária**: a segunda de duas chaves de assinaturas fornecidas para a autenticação de chave compartilhada do Azure Maps. Consulte [autenticação de chave compartilhada](#shared-key-authentication).

<a name="shapefile-shp"></a>**Shapefile (SHP)** : também conhecido como um Reformat de forma ESRI, é um formato de armazenamento de dados de vetor para armazenar o local, a forma e os atributos de recursos geográficos. Um arquivo de forma é armazenado em um conjunto de arquivos relacionados.

<a name="shared-key-authentication"></a>**Autenticação de chave compartilhada**: a autenticação de chave compartilhada depende da passagem de chaves geradas pela conta do Azure Maps com cada solicitação para mapas do Azure. Essas chaves costumam ser chamadas de chaves de assinatura. É recomendável que as chaves sejam regeneradas regularmente para segurança. Duas chaves são fornecidas para que você possa manter conexões usando uma chave ao regenerar a outra. Ao regenerar suas chaves, você deve atualizar todos os aplicativos que acessam essa conta para usar as novas chaves. Para saber mais sobre a autenticação do Azure Maps, consulte [Azure Maps e Azure ad](azure-maps-authentication.md) e [gerenciar a autenticação no Azure Maps](how-to-manage-authentication.md).

<a name="software-development-kit-sdk"></a>**SDK (Software Development Kit)** : uma coleção de documentação, código de exemplo e aplicativos de exemplo para ajudar um desenvolvedor a usar uma API para criar aplicativos.

<a name="spherical-mercator-projection"></a>**Projeção de Mercator esférica**: consulte [Mercator Web](#web-mercator). 

<a name="spatial-query"></a>**Consulta espacial**: uma solicitação feita a um serviço que executa uma operação espacial. Como uma pesquisa radial ou ao longo de uma pesquisa de rota.

<a name="spatial-reference"></a>**Referência espacial**: um sistema local, regional ou global baseado em coordenadas usado para localizar precisamente as entidades geográficas. Ele define o sistema de coordenadas usado para relacionar coordenadas de mapa a locais no mundo real. As referências espaciais garantem que os dados espaciais de diferentes camadas ou fontes possam ser integrados para exibição ou análise precisa. O Azure Maps usa o sistema de referência de coordenadas [EPSG: 3857](https://epsg.io/3857) e WGS 84 para dados de geometria de entrada. 

<a name="sql-spatial"></a>**Spatial SQL**: refere-se à funcionalidade espacial interna do SQL Azure e SQL Server 2008 e acima. Essa funcionalidade espacial também está disponível como uma biblioteca .NET que pode ser usada independentemente do SQL Server. Para obter mais informações, consulte a [documentação de dados espaciais (SQL Server)](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) para obter mais informações.

<a name="subscription-key"></a>**Chave de assinatura**: consulte [autenticação de chave compartilhada](#shared-key-authentication).

<a name="synchronous-request"></a>**Solicitação síncrona**: uma solicitação HTTP abre uma conexão e aguarda uma resposta. Os navegadores limitam o número de solicitações HTTP simultâneas que podem ser feitas em uma página. Se várias solicitações síncronas de execução longa forem feitas ao mesmo tempo, esse limite poderá ser atingido e as solicitações serão atrasadas até que uma das outras solicitações seja concluída.

## <a name="t"></a>T

<a name="telematics"></a>**Teledados: envio**, recebimento e armazenamento de informações por meio de dispositivos de telecomunicação em conjunto com controle de efeito em objetos remotos. 

<a name="temporal-data"></a>**Dados temporais**: dados que se referem especificamente a horas ou datas. Os dados temporais podem se referir a eventos discretos, como o relâmpago, movendo objetos, como trens; ou observações repetidas, como contagens de sensores de tráfego.

<a name="terrain"></a>**Terreno**: uma área de terra que tem uma característica específica, como o terreno arenoso ou o terreno de montanha.

<a name="thematic-maps"></a>**Mapas do temática**: um mapa do temática é um mapa simples feito para refletir um tema sobre uma área geográfica. Um cenário comum para esse tipo de mapa é colorir as regiões administrativas, como países/regiões com base em alguma métrica de dados.

<a name="tile-layer"></a>**Camada de peça**: uma camada exibida ao montar blocos de mapa (seções retangulares) em uma camada contínua. Os blocos são blocos de imagem de rasterização ou blocos de vetor. Camadas de bloco de varredura normalmente são renderizadas antecipadamente e armazenadas como imagens em um servidor. Isso pode ocupar muito espaço de armazenamento. As camadas de bloco de vetor são renderizadas em tempo real dentro do aplicativo cliente, portanto, os requisitos de armazenamento do lado do servidor são menores.

<a name="time-zone"></a>**Fuso horário**: uma região do globo que observa um horário padrão uniforme para fins jurídicos, comerciais e sociais. Os fusos horários tendem a seguir os limites de países/regiões e suas subdivisãos.

<a name="transaction"></a>**Transação**: o Azure Maps usa um modelo de licenciamento transacional em que;

- Uma transação é criada para cada 15 blocos de mapeamento ou de tráfego solicitados.
- Uma transação é criada para cada chamada à API para um dos serviços no Azure Maps, como pesquisa ou roteamento.

<a name="transformation"></a>**Transformação**: o processo de converter dados entre diferentes sistemas de coordenadas geográficas. Você pode, por exemplo, ter alguns dados que foram capturados no Reino Unido e com base no sistema de coordenadas geográficas do OSGB 1936. O Azure Maps usa a variante do sistema de referência de coordenadas [EPSG: 3857](https://epsg.io/3857) de WGS84. Assim, para exibir os dados corretamente, será necessário que suas coordenadas sejam transformadas de um sistema para outro.

<a name="traveling-salesmen-problem-tsp"></a>**Problema de venda de vendas de viagens (TSP)** : um problema de circuito Hamiltonian no qual um vendedor deve encontrar a maneira mais eficiente de visitar uma série de paradas e, em seguida, retornar para o local inicial.  

<a name="trilateration"></a>**Trirecentes**: o processo de determinar a posição de um ponto na superfície da terra em relação a dois outros pontos, medindo as distâncias entre os três pontos.

<a name="turn-by-turn-navigation"></a>**Navegação ativada por ativação**: um aplicativo que fornece instruções de rota para cada etapa de uma rota à medida que os usuários se aproximam da próxima manobra.

## <a name="v"></a>V

<a name="vector-data"></a>**Dados vetoriais**: dados baseados em coordenadas que são representados como pontos, linhas ou polígonos.

<a name="vector-tile"></a>**Bloco de vetor**: uma especificação de dados abertos para armazenar dados de vetor geoespaciais usando o mesmo sistema de peças que o controle de mapa. Consulte também [camada de bloco](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a>**VRP (problema de roteamento de veículo)** : uma classe de problemas em que um conjunto de rotas ordenadas para uma frota de veículos é calculado enquanto leva em consideração como um conjunto de restrições. Essas restrições podem incluir coisas como janelas de tempo de entrega, várias capacidades de rota e restrições de duração de viagem.

<a name="voronoi-diagram"></a>**Diagrama de Voronoi**: uma partição de espaço em áreas, ou células, que envolvem um conjunto de objetos geométricos (geralmente os recursos de ponto). Essas células, ou polígonos, devem satisfazer os critérios para triângulos Delaunay. Todos os locais dentro de uma área estão mais próximos do objeto que circunda para qualquer outro objeto no conjunto. Os diagramas de Voronoi geralmente são usados para delinear áreas de influência em torno de recursos geográficos. 

## <a name="w"></a>W

<a name="waypoint"></a>**Waypoint**: um waypoint é uma localização geográfica especificada definida por longitude e latitude que é usada para fins de navegação. Geralmente usado para representar um ponto em que alguém navega uma rota.

<a name="waypoint-optimization"></a>**Otimização de waypoint**: o processo de reordenação de um conjunto de Marcos para minimizar o tempo de viagem ou a distância necessária para passar por todos os marcos fornecidos. Geralmente referido como problema de [Venda de vendas](#traveling-salesmen-problem-tsp) ou [problema de roteamento de veículo](#vehicle-routing-problem-vrp) em viagem, dependendo da complexidade da otimização.

<a name="web-map-service-wms"></a>**Serviço de mapa da Web (WMS)** : o WMS é um padrão de OGC (consórcio geográfica aberta) que define serviços de mapa baseados em imagem. Os serviços do WMS fornecem imagens de mapa para áreas específicas dentro de um mapa sob demanda. As imagens incluem simbologia previamente renderizada e podem ser renderizadas em um dos vários estilos nomeados, se definido pelo serviço.

<a name="web-mercator"></a>**Web Mercator**: também conhecida como projeção de Mercator esférica é uma ligeira variante da projeção Mercator, uma usada principalmente em programas de mapeamento baseados na Web. Ele usa as mesmas fórmulas que a projeção Mercator padrão, conforme usado para mapas de pequena escala. No entanto, o Mercator da Web usa as fórmulas esféricas em todas as escalas, enquanto mapas de Mercator de grande escala normalmente usam a forma dados elipsoidais da projeção. A discrepância é imperceptível na escala global, mas faz com que os mapas de áreas locais se desviem ligeiramente do verdadeiro dados elipsoidais Mercator Maps na mesma escala.

<a name="wgs84"></a>**WGS84**: um conjunto de constantes usado para relacionar coordenadas espaciais a locais na superfície do mapa. A WGS84 Datum é a padrão usada pela maioria dos provedores de mapeamento online e dispositivos GPS. O Azure Maps usa a variante do sistema de referência de coordenadas [EPSG: 3857](https://epsg.io/3857) de WGS84.

## <a name="z"></a>Z

<a name="z-coordinate"></a>**Coordenada Z**: consulte [altitude](#altitude). 

<a name="zip-code"></a>**CEP**: consulte [código postal](#postal-code).

<a name="Zoom level"></a>**Nível de zoom**: especifica o nível de detalhes e quanto do mapa é visível. Quando ampliado até um nível 0, o mapa do mundo inteiro geralmente estará no modo de exibição, mas mostrará detalhes limitados, como nomes de país/região e bordas, bem como nomes de oceano. Quando o zoom for mais próximo do nível 17, o mapa exibirá uma área de alguns blocos de cidades com informações detalhadas sobre a estrada. Para obter mais informações, consulte a documentação [níveis de zoom e grade de blocos](zoom-levels-and-tile-grid.md) .

