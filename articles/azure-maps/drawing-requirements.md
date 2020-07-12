---
title: Requisitos de pacote de desenho no Azure Maps Creator
description: Saiba mais sobre os requisitos do pacote de desenho para converter os seus ficheiros de design de instalações para mapear dados usando o serviço de conversão Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 6/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: 1ba9edba97ce89cede54287076e50eb587af10f3
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86242479"
---
# <a name="drawing-package-requirements"></a>Requisitos de pacote do desenho

O [serviço de Conversão Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) permite converter pacotes de desenho carregados em dados de mapas. Este artigo descreve os requisitos do pacote de desenho para a API de conversão. Para visualizar um pacote de amostras, você pode baixar o [pacote de desenho](https://github.com/Azure-Samples/am-creator-indoor-data-examples)de amostras .

## <a name="prerequisites"></a>Pré-requisitos

O pacote Drawing inclui desenhos guardados em formato DWG, que é o formato de ficheiro nativo do software AutoCAD® da Autodesk, uma [marca registada da Autodesk,Inc](https://www.autodesk.com/company/legal-notices-trademarks/trademarks/guidelines-for-use#section12).

Pode escolher qualquer software CAD para produzir os desenhos no pacote Drawing.  

O [serviço de conversão Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) converte o pacote Drawing em dados de mapa.  O serviço de Conversão foi desenvolvido e testado utilizando o formato de ficheiro DWG AutoCAD. `AC1032`é a versão em formato interno para os ficheiros DWG. É encorajado a selecionar `AC1032` para a versão interna do formato de ficheiro DWG.  

Glossário de termos utilizados neste documento.

| Termo  | Definição |
|:-------|:------------|
| Camada | Uma camada AutoCAD DWG.|
| Level | Uma área de um edifício a uma elevação definida. Por exemplo, o chão de um prédio. |
| Xref  |Um ficheiro em formato de ficheiro AutoCAD DWG (.dwg) anexado ao desenho primário como referência externa.  |
| Funcionalidade | Um objeto que combina uma geometria com informações adicionais de metadados. |
| Aulas de Recurso | Uma planta comum para características. Por exemplo, uma Unidade é uma classe de recurso, e um escritório é uma característica. |

## <a name="drawing-package-structure"></a>Estrutura do pacote de desenho

Um pacote de desenho é um arquivo .zip que contém os seguintes ficheiros:

* Ficheiros DWG no formato de ficheiro AutoCAD DWG.
* Um _manifest.jsa_ arquivar uma única instalação.

Os ficheiros DWG podem ser organizados de qualquer forma dentro da pasta, mas o ficheiro manifesto deve viver no diretório de raiz da pasta. A pasta deve ser fechada num único ficheiro de arquivo, com uma extensão .zip. As secções seguintes detalham os requisitos para os ficheiros DWG, o ficheiro manifesto e o conteúdo destes ficheiros.  

## <a name="dwg-files-requirements"></a>Requisitos de ficheiros DWG

É necessário um único ficheiro DWG para cada nível da instalação. Os dados do nível devem ser contidos num único ficheiro DWG. Quaisquer referências externas _(xrefs)_ devem estar ligadas ao desenho dos pais. Adicionalmente, cada ficheiro DWG:

* Deve definir as _camadas exterior e_ _unitária._ Pode definir opcionalmente as seguintes camadas opcionais: _Parede,_ _Porta,_ _UnitLabel,_ _Zona_e _ZoneLabel._
* Não deve conter características de vários níveis.
* Não deve conter recursos de várias instalações.

O [serviço de conversão Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) pode extrair as seguintes classes de funcionalidades de um ficheiro DWG:

* Níveis
* Unidades
* Zonas
* Aberturas
* Paredes
* Penetrações Verticais

Todos os trabalhos de conversão resultam num conjunto mínimo de categorias predefinidos: sala, estrutura.parede, abertura.porta, zona e facilidade. Categorias adicionais são para cada categoria Nome referenciado por objetos.  

Uma camada DWG deve conter características de uma única classe. As aulas não devem partilhar uma camada. Por exemplo, unidades e paredes não podem partilhar uma camada.

As camadas DWG também devem seguir os seguintes critérios:

* As origens dos desenhos para todos os ficheiros DWG devem alinhar-se com a mesma latitude e longitude.
* Cada nível deve estar na mesma orientação que os outros níveis.
* Os polígonos auto-interseccionados serão automaticamente reparados e o [serviço de Conversão de Mapas Azure](https://docs.microsoft.com/rest/api/maps/conversion) levantará um aviso. Recomenda-se inspecionar manualmente os resultados reparados, uma vez que podem não corresponder aos resultados esperados.

Todas as entidades de camadas devem ser um dos seguintes tipos: Linha, Poliline, Polígono, Arco Circular, Círculo, Texto (linha única). Quaisquer outros tipos de entidades serão ignorados.

O quadro abaixo descreve os tipos de entidades suportadas e as funcionalidades suportadas para cada camada. Se uma camada contiver tipos de entidades não suportadas, então o [serviço de Conversão de Mapas Azure](https://docs.microsoft.com/rest/api/maps/conversion) ignorará estas entidades.  

| Camada | Tipos de entidade | Funcionalidades |
| :----- | :-------------------| :-------
| [Exterior](#exterior-layer) | Polígono, Poliline (fechado), Circle | Níveis
| [Unidade](#unit-layer) |  Polígono, Poliline (fechado), Circle | Penetrações Verticais, Unidades
| [Parede](#wall-layer)  | Polígono, Poliline (fechado), Circle | Não aplicável. Para obter mais informações, consulte a [camada de Parede.](#wall-layer)
| [Porta](#door-layer) | Polígono, Poliline, Linha, CircularArc, Circle | Aberturas
| [Zona](#zone-layer) | Polígono, Poliline (fechado), Circle | Zona
| [UnitLabel](#unitlabel-layer) | Texto (linha única) | Não aplicável. Esta camada só pode adicionar propriedades às características da unidade a partir da camada Unidades. Para obter mais informações, consulte a [camada UnitLabel](#unitlabel-layer).
| [ZoneLabel](#zonelabel-layer) | Texto (linha única) | Não aplicável. Esta camada só pode adicionar propriedades às características da zona a partir do ZonesLayer. Para mais informações, consulte a [camada ZoneLabel](#zonelabel-layer)

As secções seguintes detalham os requisitos para cada camada.

### <a name="exterior-layer"></a>Camada exterior

O ficheiro DWG para cada nível deve conter uma camada para definir o perímetro desse nível. Esta camada é referida como a camada exterior. Por exemplo, se uma instalação contiver dois níveis, então precisa ter dois ficheiros DWG, com uma camada exterior para cada ficheiro.

Agora, importa quantos desenhos de entidades estão na camada exterior, o [conjunto de dados de instalação resultante](tutorial-creator-indoor-maps.md#create-a-feature-stateset) conterá apenas uma funcionalidade **de** nível para cada ficheiro DWG. Além disso,

* Os exteriores devem ser desenhados como Polygon, PolyLine (fechado), Circle.

* Os exteriores podem sobrepor-se, mas serão dissolvidos numa geometria.

Se a camada contiver vários PoliLines sobrepostos, então os PolyLines serão dissolvidos numa única funcionalidade de Nível. Alternativamente, se a camada contiver vários non_overlapping PolyLines, a função nível resultante terá uma representação multi-poligonal.

Um exemplo da camada exterior pode ser visto como a camada DE CONTORNO no pacote de desenho da [amostra.](https://github.com/Azure-Samples/am-creator-indoor-data-examples)

### <a name="unit-layer"></a>Camada de unidade

O ficheiro DWG para cada nível deve definir uma camada contendo unidades.  As unidades são espaços navegáveis no edifício, tais como escritórios, corredores, escadas e elevadores. A camada unidade deve respeitar os seguintes requisitos:

* As unidades devem ser desenhadas como Polygon, PolyLine (fechada), Circle.
* As unidades devem cair dentro dos limites do perímetro exterior da instalação.
* As unidades não devem sobrepor-se parcialmente.
* As unidades não devem conter qualquer geometria auto-intersecção.

 Nomeie uma unidade criando um objeto de texto na camada _unitLabel_ e, em seguida, coloque o objeto dentro dos limites da unidade. Para obter mais informações, consulte a [camada UnitLabel](#unitlabel-layer).

Um exemplo da camada unidades pode ser visto como a camada UNITS na [embalagem de desenho](https://github.com/Azure-Samples/am-creator-indoor-data-examples)da amostra .

### <a name="wall-layer"></a>Camada de parede

O ficheiro DWG para cada nível pode conter uma camada que define as extensões físicas de paredes, colunas e outra estrutura de construção.

* As paredes devem ser desenhadas como Polygon, PolyLine (fechado), Circle.
* A camada de parede(s) só deve conter geometria que é interpretada como estrutura de construção.

Um exemplo da camada de paredes pode ser visto como a camada WALLS no pacote de desenho da [amostra.](https://github.com/Azure-Samples/am-creator-indoor-data-examples)

### <a name="door-layer"></a>Camada da porta

Pode incluir uma camada DWG contendo portas. Cada porta deve sobrepor-se à borda de uma unidade a partir da camada da unidade.

As aberturas de portas num conjunto de dados Azure Maps são representadas como um segmento de linha única que sobrepõe vários limites de unidade. São dados os seguintes passos para converter a geometria na camada da porta para abrir características num conjunto de dados.

![Passos para gerar aberturas](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Camada de zona

O ficheiro DWG para cada nível pode conter uma camada de zona que define as extensões físicas das zonas. Uma zona pode ser um espaço vazio interior ou um quintal.

* As zonas devem ser desenhadas como Polygon, PolyLine (fechado), Circle.
* As zonas podem sobrepor-se.
* As zonas podem cair dentro ou fora do perímetro exterior da instalação.

Nomeie uma zona criando um objeto de texto na camada _de zonaLabel_ e colocando o objeto de texto dentro dos limites da zona. Para mais informações, consulte [a camada ZoneLabel.](#zonelabel-layer)

Um exemplo da camada zonas pode ser visto como a camada ZONES no pacote de desenho da [amostra.](https://github.com/Azure-Samples/am-creator-indoor-data-examples)

### <a name="unitlabel-layer"></a>Camada unitLabel

O ficheiro DWG para cada nível pode conter uma camada de etiqueta de unidade. A camada de etiqueta da unidade adiciona uma propriedade de nome às unidades extraídas da camada Unit. As unidades com uma propriedade de nome podem ter detalhes adicionais especificados no ficheiro manifesto.

* As etiquetas unitárias devem ser entidades de texto de linha única.
* As etiquetas da unidade devem cair dentro dos limites da sua unidade.
* As unidades não devem conter várias entidades de texto na camada de etiquetas de unidade.

Um exemplo da camada UnitLabel pode ser visto como a camada UNITLABELS na [embalagem de desenho](https://github.com/Azure-Samples/am-creator-indoor-data-examples)da amostra .

### <a name="zonelabel-layer"></a>Camada ZoneLabel

O ficheiro DWG para cada nível pode conter uma camada de etiqueta de zona. Esta camada adiciona uma propriedade de nome às zonas extraídas da camada zone. Zonas com uma propriedade de nome podem ter detalhes adicionais especificados no arquivo manifesto.

* As etiquetas das zonas devem ser entidades de texto de linha única.
* As etiquetas das zonas devem cair dentro dos limites da sua zona.
* As zonas não devem conter várias entidades de texto na camada de etiquetas de zona.

Um exemplo da camada zonelabel pode ser visto como a camada ZONELABELS no pacote de desenho da [amostra.](https://github.com/Azure-Samples/am-creator-indoor-data-examples)

## <a name="manifest-file-requirements"></a>Requisitos manifestos de ficheiros

A pasta zip deve conter um ficheiro manifesto ao nível da raiz do diretório, e o ficheiro deve ser nomeado **manifest.jsem**. Descreve os ficheiros DWG para permitir que o [serviço de Conversão de Mapas Azure](https://docs.microsoft.com/rest/api/maps/conversion) analise o seu conteúdo. Apenas os ficheiros identificados pelo manifesto serão ingeridos. Os ficheiros que estão na pasta zip, mas que não estão devidamente listados no manifesto, serão ignorados.

Os caminhos de arquivo, no **edifícioSovels** objeto do ficheiro manifesto, devem ser relativos à raiz da pasta zip. O nome do ficheiro DWG deve corresponder exatamente ao nome do nível de instalação. Por exemplo, um ficheiro DWG para o nível "Cave" seria "Cave.dwg". Um ficheiro DWG para o nível 2 seria nomeado como "level_2.dwg". Use um sublinhado, se o seu nome de nível tiver um espaço.

Embora existam requisitos na utilização dos objetos manifestos, nem todos os objetos são necessários. A tabela abaixo mostra os objetos necessários e opcionais para a versão 1.1 do [serviço de Conversão Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion).

| Objeto | Obrigatório | Descrição |
| :----- | :------- | :------- |
| versão | true |Versão de esquema manifesto. Atualmente, apenas a versão 1.1 é suportada.|
| directyInfo | true | Descreve as informações geográficas e de contacto das instalações. Também pode ser usado para delinear informações geográficas e de contacto dos ocupantes. |
| edifícioLevels | true | Especifica os níveis dos edifícios e os ficheiros que contêm a conceção dos níveis. |
| georeferência | true | Contém informações geográficas numéricas para o desenho das instalações. |
| dwgLayers | true | Lista os nomes das camadas, e cada camada lista os nomes das suas próprias características. |
| unidadeProperties | false | Pode ser utilizado para inserir metadados adicionais para as funcionalidades da unidade. |
| zonaProperties | false | Pode ser usado para inserir metadados adicionais para as características da zona. |

As secções seguintes detalham os requisitos para cada objeto.

### <a name="directoryinfo"></a>directyInfo

| Propriedade  | tipo | Obrigatório | Descrição |
|-----------|------|----------|-------------|
| name      | string | true   |  Nome do prédio. |
| streetAddress|    string |    false    | Endereço do edifício. |
|unit     | string    |  false    |  Unidade no edifício. |
| localidade |    string |    false |    Nome de uma área, bairro ou região. Por exemplo, "Overlake" ou "Distrito Central". A localidade não faz parte do endereço de correio. |
| adminDivisions |    Matriz de cordas JSON |    false     | Um conjunto contendo designações de endereços (País, Estado, Cidade) ou (País, Prefeitura, Cidade, Cidade). Utilize códigos de país ISO 3166 e códigos iso 3166-2 estado/território. |
| código postal |    string    | false    | O código de triagem do correio. |
| horasOfOperação |    string |     false | Adere ao formato [ASM Opening Hours.](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification) |
| telefone    | string |    false |    Número de telefone associado ao edifício. Deve incluir o código do país. |
| site    | string |    false    | Site associado ao edifício. M começa com http ou https. |
| não Público |    bool    | false | Bandeira especificando se o edifício está aberto ao público. |
| anchorAtitude | numeric |    false | Latitude de uma âncora de instalação (pino). |
| anchorLongitude | numeric |    false | Longitude de uma âncora de instalação (pino). |
| anchorHeightAboveSeaLevel  | numeric | false | Altura do piso térreo da instalação acima do nível do mar, em metros. |
| defaultLevelVerticalExtent | numeric | false | Altura padrão (espessura) de um nível desta facilidade para usar quando um nível `verticalExtent` é indefinido. |

### <a name="buildinglevels"></a>edifícioLevels

O `buildingLevels` objeto contém uma matriz JSON de níveis de edifícios.

| Propriedade  | Tipo | Necessário | Descrição |
|-----------|------|----------|-------------|
|nome de nível    |string    |true |    Nome de nível descritivo. Por exemplo: Piso 1, Lobby, Estacionamento Azul, Cave, e assim por diante.|
|ordinal | número inteiro |    true | Ordinal é usado para determinar a ordem vertical dos níveis. Todas as instalações devem ter um nível com ordinal 0. |
|heightAboveFacilityAnchor | numeric | false |    Altura de nível acima da âncora em metros. |
| verticalExtent | numeric | false | Altura do chão ao teto (espessura) do nível dos metros. |
|filename |    string |    true |    O caminho do sistema de arquivo do desenho cad para um nível de construção. Deve ser relativo à raiz do arquivo zip do edifício. |

### <a name="georeference"></a>georeferência

| Propriedade  | Tipo | Necessário | Descrição |
|-----------|------|----------|-------------|
|lat    | numeric |    true |    Representação decimal de graus de latitude na origem do desenho da instalação. As coordenadas de origem devem estar no WGS84 Web Mercator `EPSG:3857` ().|
|lon    |numeric|    true|    Representação decimal de graus de longitude na origem do desenho da instalação. As coordenadas de origem devem estar no WGS84 Web Mercator `EPSG:3857` (). |
|angle|    numeric|    true|   O ângulo no sentido dos ponteiros do relógio, em graus, entre o verdadeiro norte e o eixo vertical (Y) do desenho.   |

### <a name="dwglayers"></a>dwgLayers

| Propriedade  | Tipo | Necessário | Descrição |
|-----------|------|----------|-------------|
|exterior    |Matriz de cordas|    true|    Nomes de camadas(s) que definem o perfil de construção exterior.|
|unit|    Matriz de cordas|    true|    Nomes de camadas que definem unidades.|
|parede|    Matriz de cordas    |false|    Nomes de camadas que definem paredes.|
|porta    |Matriz de cordas|    false   | Nomes de camadas que definem portas.|
|unitLabel    |Matriz de cordas|    false    |Nomes de camadas que definem nomes de unidades.|
|zona | Matriz de cordas    | false    | Nomes de camadas que definem zonas.|
|zonaLabel | Matriz de cordas |     false |    Nomes de camadas que definem nomes de zonas.|

### <a name="unitproperties"></a>unidadeProperties

O `unitProperties` objeto contém uma matriz JSON de propriedades de unidade.

| Propriedade  | Tipo | Necessário | Descrição |
|-----------|------|----------|-------------|
|unitName    |string    |true    |Nome da unidade para associar a este `unitProperty` registo. Este registo só é válido quando uma etiqueta correspondente `unitName` é encontrada `unitLabel` na(s) camadas. |
|categoriaName|    string|    false    |Nome da categoria. Para obter uma lista completa de categorias, consulte [as categorias](https://aka.ms/pa-indoor-spacecategories). |
|navegávelBy| Matriz de cordas |    false    |Indica os tipos de agentes de navegação que podem atravessar a unidade. Por exemplo, "pedestre". Esta propriedade informará as capacidades de wayfinding.  Os valores permitidos `pedestrian` são, `wheelchair` , , , , , , , , `machine` , , , , `bicycle` e `automobile` `hiredAuto` `bus` `railcar` `emergency` `ferry` `boat` `disallowed` .|
|routeThroughBehavior|    string|    false    |A rota através do comportamento para a unidade. Os valores permitidos são `disallowed` `allowed` , e `preferred` . O valor predefinido é `allowed` .|
|ocupantes    |Matriz de objetos De directórioInfo |false    |Lista de ocupantes para a unidade. |
|nomeAlt|    string|    false|    Nome alternativo da unidade. |
|nomeSubtário|    string    |false|    Legenda da unidade. |
|addressRoomNumber|    string|    false|    Número do quarto/unidade/apartamento/suite da unidade.|
|verticalPenetrationCategoria|    string|    false| Quando esta propriedade for definida, a característica resultante será uma Penetração Vertical (VRT) em vez de uma unidade. Os VRTs podem ser utilizados para navegar para outras funcionalidades de VRT nos níveis acima ou abaixo. A Penetração Vertical é um nome [de categoria.](https://aka.ms/pa-indoor-spacecategories) Se esta propriedade for definida, a propriedade categoriaName é ultrapassada com verticalPenetrationCategoria. |
|verticalPenetrationDirection|    string|    false    |Se `verticalPenetrationCategory` for definido, definir opcionalmente a direção válida do viagens. Os valores permitidos `lowToHigh` `highToLow` `both` são, `closed` e. O valor predefinido é `both` .|
| não Público | bool | false | Indica se a unidade está aberta ao público. |
| isroutable | bool | false | Quando programado para `false` , a unidade não pode ser navegada para, ou através. O valor predefinido é `true` . |
| isOpenArea | bool | false | Permite que o agente de navegação entre na unidade sem a necessidade de uma abertura ligada à unidade. Por predefinição, este valor é definido `true` para unidades sem aberturas; `false` para unidades com aberturas.  A regulação manual `isOpenArea` para uma unidade sem `false` aberturas resulta num aviso. Isto porque a unidade resultante não será acessível por um agente de navegação.|

### <a name="the-zoneproperties-object"></a>O objeto de zonaProperties

O `zoneProperties` objeto contém uma matriz JSON de propriedades de zona.

| Propriedade  | Tipo | Necessário | Descrição |
|-----------|------|----------|-------------|
|zoneName        |string    |true    |Nome da zona para associar ao `zoneProperty` registo. Este registo só é válido quando uma etiqueta correspondente `zoneName` é encontrada na camada da `zoneLabel` zona.  |
|categoriaName|    string|    false    |Nome da categoria. Para obter uma lista completa de categorias, consulte [as categorias](https://aka.ms/pa-indoor-spacecategories). |
|zoneNameAlt|    string|    false    |Nome alternativo da zona.  |
|zoneNameSubtitle|    string |    false    |Legenda da zona. |
|zoneSetId|    string |    false    | Deslote o ID para estabelecer a relação entre várias zonas para que possam ser consultadas ou selecionadas como um grupo. Por exemplo, zonas que se estendem por vários níveis. |

### <a name="sample-drawing-package-manifest"></a>Manifesto de pacote de desenho de amostra

Abaixo está um ficheiro manifesto de amostra para o pacote de desenho da amostra. Para descarregar todo o pacote, clique em [amostras O pacote de desenho](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

#### <a name="manifest-file"></a>Arquivo Manifesto

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddresss": "Contoso Way", 
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
            "nonWheelchairAccessible": false, 
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

Uma vez que o seu pacote De desenho satisfaça os requisitos, poderá utilizar o [serviço de Conversão Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) para converter o pacote num conjunto de dados do mapa. Em seguida, pode utilizar o conjunto de dados para gerar um mapa interior utilizando o módulo De Mapas Interiores. Saiba mais sobre a utilização do módulo Mapas Interiores lendo os seguintes artigos:

> [!div class="nextstepaction"]
>[Criador para mapas interiores](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Tutorial: Criar um mapa interior do Criador](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Estilo dinâmico de Mapas Interiores](indoor-map-dynamic-styling.md)
