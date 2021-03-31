---
title: Requisitos de pacote de desenho no Microsoft Azure Maps Creator (Preview)
description: Saiba mais sobre os requisitos do pacote de desenho para converter os seus ficheiros de design de instalações para mapear dados
author: anastasia-ms
ms.author: v-stharr
ms.date: 1/08/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: 2a37e716b7804b11ab396909f746af84294bb4e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98895276"
---
# <a name="drawing-package-requirements"></a>Requisitos de pacote do desenho


> [!IMPORTANT]
> Os serviços do Azure Maps Creator estão atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode converter pacotes de desenho carregados em dados de mapas utilizando o [serviço de conversão Azure Maps.](/rest/api/maps/conversion) Este artigo descreve os requisitos do pacote de desenho para a API de conversão. Para visualizar um pacote de amostras, você pode baixar o [pacote de desenho](https://github.com/Azure-Samples/am-creator-indoor-data-examples)de amostras .

## <a name="prerequisites"></a>Pré-requisitos

O pacote Drawing inclui desenhos guardados em formato DWG, que é o formato de ficheiro nativo para o software autoCAD® da Autodesk.

Pode escolher qualquer software CAD para produzir os desenhos no pacote Drawing.  

O [serviço de conversão Azure Maps](/rest/api/maps/conversion) converte o pacote Drawing em dados de mapa. O serviço de Conversão funciona com o formato de ficheiro DWG AutoCAD. `AC1032` é a versão em formato interno para os ficheiros DWG, e é uma boa ideia selecionar `AC1032` para a versão interna do formato de ficheiro DWG.  

## <a name="glossary-of-terms"></a>Glossário de termos

Para uma referência fácil, aqui estão alguns termos e definições que são importantes ao ler este artigo.

| Termo  | Definição |
|:-------|:------------|
| Camada | Uma camada AutoCAD DWG.|
| Level | Uma área de um edifício a uma elevação definida. Por exemplo, o chão de um prédio. |
| Xref  |Um ficheiro em formato de ficheiro AutoCAD DWG (.dwg), anexado ao desenho primário como referência externa.  |
| Funcionalidade | Um objeto que combina uma geometria com mais informações de metadados. |
| Aulas de recurso | Uma planta comum para características. Por exemplo, uma *unidade* é uma classe de recurso, e um *escritório* é uma característica. |

## <a name="drawing-package-structure"></a>Estrutura de pacote de desenho

Um pacote de desenho é um arquivo .zip que contém os seguintes ficheiros:

* Ficheiros DWG no formato de ficheiro AutoCAD DWG.
* Um _manifest.jsem_ ficheiro que descreve os ficheiros DWG no pacote Desenhista.

A embalagem de desenho deve ser fechada num único ficheiro de arquivo, com a extensão .zip. Os ficheiros DWG podem ser organizados de qualquer forma dentro do pacote, mas o ficheiro manifesto deve viver no diretório de raiz do pacote zipped. As secções seguintes detalham os requisitos para os ficheiros DWG, o ficheiro manifesto e o conteúdo destes ficheiros.

## <a name="dwg-files-requirements"></a>Requisitos de ficheiros DWG

É necessário um único ficheiro DWG para cada nível da instalação. Os dados do nível devem ser contidos num único ficheiro DWG. Quaisquer referências externas _(xrefs)_ devem estar ligadas ao desenho dos pais. Adicionalmente, cada ficheiro DWG:

* Deve definir as _camadas exterior e_ _unitária._ Pode definir opcionalmente as seguintes camadas opcionais: _Parede,_ _Porta,_ _UnitLabel,_ _Zona_ e _ZoneLabel._
* Não deve conter características de vários níveis.
* Não deve conter recursos de várias instalações.
* Deve fazer referência ao mesmo sistema de medição e unidade de medição que outros ficheiros DWG na embalagem de desenho.

O [serviço de conversão Azure Maps](/rest/api/maps/conversion) pode extrair as seguintes classes de funcionalidades de um ficheiro DWG:

* Níveis
* Unidades
* Zonas
* Aberturas
* Paredes
* Penetrações verticais

Todos os trabalhos de conversão resultam num conjunto mínimo de categorias predefinidos: sala, estrutura.parede, abertura.porta, zona e facilidade. Categorias adicionais são para cada categoria nome referenciado por objetos.  

Uma camada DWG deve conter características de uma única classe. As aulas não devem partilhar uma camada. Por exemplo, unidades e paredes não podem partilhar uma camada.

As camadas DWG também devem seguir os seguintes critérios:

* As origens dos desenhos para todos os ficheiros DWG devem alinhar-se com a mesma latitude e longitude.
* Cada nível deve estar na mesma orientação que os outros níveis.
* Os polígonos auto-interseccionados são automaticamente reparados e o [serviço de conversão Azure Maps](/rest/api/maps/conversion) levanta um aviso. É aconselhável inspecionar manualmente os resultados reparados, porque podem não corresponder aos resultados esperados.

Todas as entidades de camadas devem ser um dos seguintes tipos: Linha, PolyLine, Polygon, Circular Arc, Circle, Elipse (fechado) ou Texto (linha única). Quaisquer outros tipos de entidades são ignorados.

A tabela abaixo descreve os tipos de entidades suportadas e as características do mapa convertidos para cada camada. Se uma camada contiver tipos de entidades não suportadas, então o [serviço de Conversão Azure Maps](/rest/api/maps/conversion) ignora estas entidades.  

| Camada | Tipos de entidade | Características convertidas |
| :----- | :-------------------| :-------
| [Exterior](#exterior-layer) | Polígono, PolyLine (fechado), Circle, Elipse (fechado) | Níveis
| [Unidade](#unit-layer) |  Polígono, PolyLine (fechado), Circle, Elipse (fechado) | Penetrações verticais, Unidade
| [Parede](#wall-layer)  | Polígono, PolyLine (fechado), Circle, Elipse (fechado) | Não aplicável. Para obter mais informações, consulte a [camada de Parede.](#wall-layer)
| [Porta](#door-layer) | Polígono, Poliline, Linha, CircularArc, Circle | Aberturas
| [Zona](#zone-layer) | Polígono, PolyLine (fechado), Circle, Elipse (fechado) | Zona
| [UnitLabel](#unitlabel-layer) | Texto (linha única) | Não aplicável. Esta camada só pode adicionar propriedades às características da unidade a partir da camada Unidades. Para obter mais informações, consulte a [camada UnitLabel](#unitlabel-layer).
| [ZoneLabel](#zonelabel-layer) | Texto (linha única) | Não aplicável. Esta camada só pode adicionar propriedades às características da zona a partir do ZonesLayer. Para mais informações, consulte a [camada ZoneLabel.](#zonelabel-layer)

As secções seguintes detalham os requisitos para cada camada.

### <a name="exterior-layer"></a>Camada exterior

O ficheiro DWG para cada nível deve conter uma camada para definir o perímetro desse nível. Esta camada é referida como a camada *exterior.* Por exemplo, se uma instalação contiver dois níveis, então precisa ter dois ficheiros DWG, com uma camada exterior para cada ficheiro.

Não importa quantos desenhos de entidades estejam na camada exterior, o [conjunto de dados de instalação resultante](tutorial-creator-indoor-maps.md#create-a-feature-stateset) conterá apenas uma funcionalidade de nível para cada ficheiro DWG. Além disso,

* Os exteriores devem ser desenhados como Polygon, PolyLine (fechado), Circle ou Elipse (fechado).
* Os exteriores podem sobrepor-se, mas são dissolvidos numa geometria.
* A característica de nível resultante deve ser de pelo menos 4 metros quadrados.
* A característica de nível resultante não deve ser superior a 400.000 metros quadrados.

Se a camada contiver polilines sobrepostos múltiplos, os PolyLines são dissolvidos numa única função de Nível. Alternativamente, se a camada contiver múltiplos PoliLines não sobrepostos, a função nível resultante tem uma representação multi-poligonal.

Pode ver um exemplo da camada exterior como a camada de contorno no pacote de desenho da [amostra.](https://github.com/Azure-Samples/am-creator-indoor-data-examples)

### <a name="unit-layer"></a>Camada de unidade

O ficheiro DWG para cada nível define uma camada contendo unidades. As unidades são espaços navegáveis no edifício, tais como escritórios, corredores, escadas e elevadores. Se a `VerticalPenetrationCategory` propriedade for definida, unidades navegáveis que se estendem por vários níveis, como elevadores e escadas, são convertidas para características de Penetração Vertical. Características de penetração vertical que se sobrepõem uma à outra são atribuídas a uma `setid` .

A camada unidade deve respeitar os seguintes requisitos:

* As unidades devem ser desenhadas como Polygon, PolyLine (fechada), Circle ou Elipse (fechada).
* As unidades devem cair dentro dos limites do perímetro exterior da instalação.
* As unidades não devem sobrepor-se parcialmente.
* As unidades não devem conter qualquer geometria auto-intersecção.

Nomeie uma unidade criando um objeto de texto na camada UnitLabel e, em seguida, coloque o objeto dentro dos limites da unidade. Para obter mais informações, consulte a [camada UnitLabel](#unitlabel-layer).

Pode ver um exemplo da camada unidades no pacote de desenho da [amostra.](https://github.com/Azure-Samples/am-creator-indoor-data-examples)

### <a name="wall-layer"></a>Camada de parede

O ficheiro DWG para cada nível pode conter uma camada que define as extensões físicas de paredes, colunas e outra estrutura de construção.

* As paredes devem ser desenhadas como Polygon, PolyLine (fechado), Circle ou Elipse (fechado).
* A camada ou camadas de parede só devem conter geometria que é interpretada como estrutura de construção.

Pode ver um exemplo da camada de Paredes no pacote de desenho da [amostra.](https://github.com/Azure-Samples/am-creator-indoor-data-examples)

### <a name="door-layer"></a>Camada da porta

Pode incluir uma camada DWG que contém portas. Cada porta deve sobrepor-se à borda de uma unidade da camada Unit.

As aberturas das portas num conjunto de dados Azure Maps são representadas como um segmento de linha única que sobrepõe-se a vários limites unitários. As imagens que se seguem mostram como converter geometria na camada da porta para abrir características num conjunto de dados.

![Quatro gráficos que mostram os passos para gerar aberturas](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Camada de zona

O ficheiro DWG para cada nível pode conter uma camada de Zona que define as extensões físicas das zonas. Uma zona é um espaço não navegável que pode ser nomeado e renderizado. As zonas podem abranger vários níveis e são agrupadas usando a propriedade zoneSetId.

* As zonas devem ser desenhadas como Polygon, PolyLine (fechado) ou Elipse (fechada).
* As zonas podem sobrepor-se.
* As zonas podem cair dentro ou fora do perímetro exterior da instalação.

Nomeie uma zona criando um objeto de texto na camada ZoneLabel e colocando o objeto de texto dentro dos limites da zona. Para mais informações, consulte [a camada ZoneLabel.](#zonelabel-layer)

Pode ver um exemplo da camada zona no pacote de desenho da [amostra.](https://github.com/Azure-Samples/am-creator-indoor-data-examples)

### <a name="unitlabel-layer"></a>Camada unitLabel

O ficheiro DWG para cada nível pode conter uma camada UnitLabel. A camada UnitLabel adiciona uma propriedade de nome às unidades extraídas da camada Unit. As unidades com uma propriedade de nome podem ter mais detalhes especificados no ficheiro manifesto.

* As etiquetas unitárias devem ser entidades de texto de linha única.
* As etiquetas da unidade devem cair dentro dos limites da sua unidade.
* As unidades não devem conter várias entidades de texto na camada UnitLabel.

Pode ver um exemplo da camada UnitLabel na [amostra Desenho.](https://github.com/Azure-Samples/am-creator-indoor-data-examples)

### <a name="zonelabel-layer"></a>Camada ZoneLabel

O ficheiro DWG para cada nível pode conter uma camada ZoneLabel. Esta camada adiciona uma propriedade de nome às zonas extraídas da camada zone. Zonas com uma propriedade de nome podem ter mais detalhes especificados no arquivo manifesto.

* As etiquetas das zonas devem ser entidades de texto de linha única.
* As etiquetas das zonas devem cair dentro dos limites da sua zona.
* As zonas não devem conter várias entidades de texto na camada ZoneLabel.

Pode ver um exemplo da camada ZoneLabel no pacote de desenho da [amostra.](https://github.com/Azure-Samples/am-creator-indoor-data-examples)

## <a name="manifest-file-requirements"></a>Requisitos manifestos de ficheiros

A pasta zip deve conter um ficheiro manifesto ao nível da raiz do diretório, e o ficheiro deve ser nomeado **manifest.jsem**. Descreve os ficheiros DWG para permitir que o [serviço de Conversão de Mapas Azure](/rest/api/maps/conversion) analise o seu conteúdo. Apenas os ficheiros identificados pelo manifesto são ingeridos. Os ficheiros que estão na pasta zip, mas que não estão devidamente listados no manifesto, são ignorados.

Os caminhos de ficheiro no `buildingLevels` objeto do ficheiro manifesto devem ser relativos à raiz da pasta zip. O nome do ficheiro DWG deve corresponder exatamente ao nome do nível de instalação. Por exemplo, um ficheiro DWG para o nível "Cave" é "Cave.dwg". Um ficheiro DWG para o nível 2 é nomeado como "level_2.dwg". Use um sublinhado, se o seu nome de nível tiver um espaço.

Embora existam requisitos quando se utilizam os objetos manifestos, nem todos os objetos são necessários. A tabela a seguir mostra os objetos necessários e opcionais para a versão 1.1 do serviço de Conversão de [Mapas Azure](/rest/api/maps/conversion).

| Objeto | Obrigatório | Descrição |
| :----- | :------- | :------- |
| `version` | true |Versão de esquema manifesto. Atualmente, apenas a versão 1.1 é suportada.|
| `directoryInfo` | true | Descreve as informações geográficas e de contacto das instalações. Também pode ser usado para delinear informações geográficas e de contacto dos ocupantes. |
| `buildingLevels` | true | Especifica os níveis dos edifícios e os ficheiros que contêm a conceção dos níveis. |
| `georeference` | true | Contém informações geográficas numéricas para o desenho das instalações. |
| `dwgLayers` | true | Lista os nomes das camadas, e cada camada lista os nomes das suas próprias características. |
| `unitProperties` | false | Pode ser usado para inserir mais metadados para as funcionalidades da unidade. |
| `zoneProperties` | false | Pode ser usado para inserir mais metadados para as características da zona. |

As secções seguintes detalham os requisitos para cada objeto.

### `directoryInfo`

| Propriedade  | Tipo | Necessário | Description |
|-----------|------|----------|-------------|
| `name`      | cadeia (de carateres) | true   |  Nome do prédio. |
| `streetAddress`|    string |    false    | Endereço do edifício. |
|`unit`     | string    |  false    |  Unidade no edifício. |
| `locality` |    string |    false |    Nome de uma área, bairro ou região. Por exemplo, "Overlake" ou "Distrito Central". A localidade não faz parte do endereço de correio. |
| `adminDivisions` |    Conjunto de cordas JSON |    false     | Um conjunto contendo designações de endereços (País, Estado, Cidade) ou (País, Prefeitura, Cidade, Cidade). Utilize códigos de país ISO 3166 e códigos iso 3166-2 estado/território. |
| `postalCode` |    string    | false    | O código de triagem do correio. |
| `hoursOfOperation` |    string |     false | Adere ao formato [ASM Opening Hours.](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification) |
| `phone`    | string |    false |    Número de telefone associado ao edifício. Deve incluir o código do país. |
| `website`    | string |    false    | Site associado ao edifício. Deve começar com http ou https. |
| `nonPublic` |    bool    | false | Bandeira especificando se o edifício está aberto ao público. |
| `anchorLatitude` | numeric |    false | Latitude de uma âncora de instalação (pino). |
| `anchorLongitude` | numeric |    false | Longitude de uma âncora de instalação (pino). |
| `anchorHeightAboveSeaLevel`  | numeric | false | Altura do piso térreo da instalação acima do nível do mar, em metros. |
| `defaultLevelVerticalExtent` | numeric | false | Altura padrão (espessura) de um nível desta facilidade para usar quando um nível `verticalExtent` é indefinido. |

### `buildingLevels`

O `buildingLevels` objeto contém uma matriz JSON de níveis de edifícios.

| Propriedade  | Tipo | Necessário | Description |
|-----------|------|----------|-------------|
|`levelName`    |cadeia (de carateres)    |true |    Nome de nível descritivo. Por exemplo: Piso 1, Lobby, Estacionamento Azul ou Cave.|
|`ordinal` | número inteiro |    true | Determina a ordem vertical dos níveis. Todas as instalações devem ter um nível com ordinal 0. |
|`heightAboveFacilityAnchor` | numeric | false |    Altura de nível acima da âncora em metros. |
| `verticalExtent` | numeric | false | Altura do chão ao teto (espessura) do nível dos contadores. |
|`filename` |    string |    true |    O caminho do sistema de arquivo do desenho cad para um nível de construção. Deve ser relativo à raiz do arquivo zip do edifício. |

### `georeference`

| Propriedade  | Tipo | Necessário | Descrição |
|-----------|------|----------|-------------|
|`lat`    | numeric |    true |    Representação decimal de graus de latitude na origem do desenho da instalação. As coordenadas de origem devem estar no WGS84 Web Mercator `EPSG:3857` ().|
|`lon`    |numeric|    true|    Representação decimal de graus de longitude na origem do desenho da instalação. As coordenadas de origem devem estar no WGS84 Web Mercator `EPSG:3857` (). |
|`angle`|    numeric|    true|   O ângulo no sentido dos ponteiros do relógio, em graus, entre o verdadeiro norte e o eixo vertical (Y) do desenho.   |

### `dwgLayers`

| Propriedade  | Tipo | Necessário | Descrição |
|-----------|------|----------|-------------|
|`exterior`    |matriz de cadeias (de carateres)|    true|    Nomes de camadas que definem o perfil de construção exterior.|
|`unit`|    matriz de cadeias (de carateres)|    true|    Nomes de camadas que definem unidades.|
|`wall`|    matriz de cadeias (de carateres)    |false|    Nomes de camadas que definem paredes.|
|`door`    |matriz de cadeias (de carateres)|    false   | Nomes de camadas que definem portas.|
|`unitLabel`    |matriz de cadeias (de carateres)|    false    |Nomes de camadas que definem nomes de unidades.|
|`zone` | matriz de cadeias (de carateres)    | false    | Nomes de camadas que definem zonas.|
|`zoneLabel` | matriz de cadeias (de carateres) |     false |    Nomes de camadas que definem nomes de zonas.|

### `unitProperties`

O `unitProperties` objeto contém uma matriz JSON de propriedades de unidade.

| Propriedade  | Tipo | Necessário | Description |
|-----------|------|----------|-------------|
|`unitName`    |cadeia (de carateres)    |true    |Nome da unidade para associar a este `unitProperty` registo. Este registo só é válido quando uma etiqueta correspondente `unitName` é encontrada nas `unitLabel` camadas. |
|`categoryName`|    string|    false    |Nome da categoria. Para obter uma lista completa de categorias, consulte [as categorias](https://aka.ms/pa-indoor-spacecategories). |
|`navigableBy`| matriz de cadeias (de carateres) |    false    |Indica os tipos de agentes de navegação que podem atravessar a unidade. Esta propriedade informa as capacidades de wayfinding. Os valores permitidos são: `pedestrian` , , , , , , , , `wheelchair` , , `machine` , e `bicycle` `automobile` `hiredAuto` `bus` `railcar` `emergency` `ferry` `boat` `disallowed` .|
|`routeThroughBehavior`|    string|    false    |A rota através do comportamento para a unidade. Os valores permitidos são `disallowed` `allowed` , e `preferred` . O valor predefinido é `allowed`.|
|`occupants`    |matriz de objetos De directórioInfo |false    |Lista de ocupantes para a unidade. |
|`nameAlt`|    string|    false|    Nome alternativo da unidade. |
|`nameSubtitle`|    string    |false|    Legenda da unidade. |
|`addressRoomNumber`|    string|    false|    Quarto, unidade, apartamento ou número de suite da unidade.|
|`verticalPenetrationCategory`|    string|    false| Quando esta propriedade é definida, a característica resultante é uma penetração vertical (VRT) em vez de uma unidade. Pode utilizar VRTs para ir a outras funcionalidades vrt nos níveis acima ou abaixo. Penetração vertical é um nome [de categoria.](https://aka.ms/pa-indoor-spacecategories) Se esta propriedade for definida, a `categoryName` propriedade é ultrapassada `verticalPenetrationCategory` com. |
|`verticalPenetrationDirection`|    string|    false    |Se `verticalPenetrationCategory` for definido, definir opcionalmente a direção válida do viagens. Os valores permitidos são: `lowToHigh` `highToLow` , , e `both` `closed` . O valor predefinido é `both`.|
| `nonPublic` | bool | false | Indica se a unidade está aberta ao público. |
| `isRoutable` | bool | false | Quando esta propriedade está definida `false` para, você não pode ir ou através da unidade. O valor predefinido é `true`. |
| `isOpenArea` | bool | false | Permite que o agente de navegação entre na unidade sem a necessidade de uma abertura ligada à unidade. Por predefinição, este valor é definido `true` para unidades sem aberturas, e `false` para unidades com aberturas. A regulação manual `isOpenArea` para uma unidade sem `false` aberturas resulta num aviso, porque a unidade resultante não será acessível por um agente de navegação.|

### `zoneProperties`

O `zoneProperties` objeto contém uma matriz JSON de propriedades de zona.

| Propriedade  | Tipo | Necessário | Descrição |
|-----------|------|----------|-------------|
|zoneName        |string    |true    |Nome da zona para associar ao `zoneProperty` registo. Este registo só é válido quando uma etiqueta correspondente `zoneName` é encontrada na camada da `zoneLabel` zona.  |
|categoriaName|    string|    false    |Nome da categoria. Para obter uma lista completa de categorias, consulte [as categorias](https://aka.ms/pa-indoor-spacecategories). |
|zoneNameAlt|    string|    false    |Nome alternativo da zona.  |
|zoneNameSubtitle|    string |    false    |Legenda da zona. |
|zoneSetId|    string |    false    | Deslote o ID para estabelecer uma relação entre várias zonas para que possam ser consultados ou selecionados como um grupo. Por exemplo, zonas que se estendem por vários níveis. |

### <a name="sample-drawing-package-manifest"></a>Manifesto de pacote de desenho de amostra

Abaixo está o ficheiro manifesto para o pacote de desenho da amostra. Para descarregar todo o pacote, consulte [o pacote de desenho de amostras.](https://github.com/Azure-Samples/am-creator-indoor-data-examples)

#### <a name="manifest-file"></a>Arquivo manifesto

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddress": "Contoso Way", 
        "unit": "1", 
        "locality": "Contoso eastside", 
        "postalCode": "98052", 
        "adminDivisions": [ 
            "Contoso city", 
            "Contoso state", 
            "Contoso country" 
        ], 
        "hoursOfOperation": "Mo-Fr 08:00-17:00 open", 
        "phone": "1 (425) 555-1234", 
        "website": "www.contoso.com", 
        "nonPublic": false, 
        "anchorLatitude": 47.636152, 
        "anchorLongitude": -122.132600, 
        "anchorHeightAboveSeaLevel": 1000, 
        "defaultLevelVerticalExtent": 3  
    }, 
    "buildingLevels": { 
        "levels": [ 
            { 
                "levelName": "Basement", 
                "ordinal": -1, 
                "filename": "./Basement.dwg" 
            }, { 
                "levelName": "Ground", 
                "ordinal": 0, 
                "verticalExtent": 5, 
                "filename": "./Ground.dwg" 
            }, { 
                "levelName": "Level 2", 
                "ordinal": 1, 
                "heightAboveFacilityAnchor": 3.5, 
                "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
    "georeference": { 
        "lat": 47.636152, 
        "lon": -122.132600, 
        "angle": 0 
    }, 
    "dwgLayers": { 
        "exterior": [ 
            "OUTLINE", "WINDOWS" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    }, 
    "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
        { 
            "unitName": "B02" 
        }, 
        { 
            "unitName": "B05", 
            "categoryName": "room.office" 
        }, 
        { 
            "unitName": "STRB01", 
            "verticalPenetrationCategory": "verticalPenetration.stairs", 
            "verticalPenetrationDirection": "both" 
        }, 
        { 
            "unitName": "ELVB01", 
            "verticalPenetrationCategory": "verticalPenetration.elevator", 
            "verticalPenetrationDirection": "high_to_low" 
        } 
    ], 
    "zoneProperties": 
    [ 
        { 
            "zoneName": "WifiB01", 
            "categoryName": "Zone", 
            "zoneNameAlt": "MyZone", 
            "zoneNameSubtitle": "Wifi", 
            "zoneSetId": "1234" 
        }, 
        { 
            "zoneName": "Wifi101",
            "categoryName": "Zone",
            "zoneNameAlt": "MyZone",
            "zoneNameSubtitle": "Wifi",
            "zoneSetId": "1234"
        }
    ]
}
```

## <a name="next-steps"></a>Passos seguintes

Quando o seu pacote Desenhá-lo satisfaz os requisitos, pode utilizar o [serviço de Conversão Azure Maps](/rest/api/maps/conversion) para converter o pacote num conjunto de dados do mapa. Em seguida, pode utilizar o conjunto de dados para gerar um mapa interior utilizando o módulo de mapas interiores.

> [!div class="nextstepaction"]
>[Criador (Pré-visualização) para mapas interiores](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Tutorial: Criação de um mapa interior do Criador (Preview)](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Estilo dinâmico de mapas interiores](indoor-map-dynamic-styling.md)