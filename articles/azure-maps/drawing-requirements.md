---
title: Requisitos de pacote de desenho no Criador do Azure Maps
description: Conheça os requisitos do pacote Desacato para converter os seus ficheiros de design de instalações para mapear dados usando o serviço de conversão do Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 5/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: dad9bb40161a2adc8654f50de5c1d876e3344e59
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598802"
---
# <a name="drawing-package-requirements"></a>Requisitos de pacote de desenho

O serviço de [conversão Do Azure Maps](https://docs.microsoft.com/rest/api/maps/data/conversion) permite converter pacotes de desenho carregados em dados de mapas. Este artigo descreve os requisitos do pacote de desenho para a API de conversão. Para visualizar um pacote de amostras, pode descarregar o pacote de [desenho](https://github.com/Azure-Samples/am-creator-indoor-data-examples)de amostras.

## <a name="prerequisites"></a>Pré-requisitos

O pacote De desenho inclui desenhos guardados em formato DWG, que é o formato de ficheiro nativo para o software AutoCAD® da [Autodesk, uma marca registada da Autodesk,Inc.](https://www.autodesk.com/company/legal-notices-trademarks/trademarks/guidelines-for-use#section12)

Pode escolher qualquer software CAD para produzir os desenhos no pacote Desacato.  

O [serviço de conversão Do Azure Maps](https://docs.microsoft.com/rest/api/maps/data/conversion) converte o pacote de desenho em dados de mapas.  O serviço de conversão foi desenvolvido e testado utilizando o formato de ficheiro AutoCAD DWG. `AC1032`é a versão de formato interno para os ficheiros DWG. É encorajado a selecionar `AC1032` para a versão interna do formato de ficheiro DWG.  

Glossário de termos utilizados neste documento.

| Termo  | Definição |
|:-------|:------------|
| Camada | Uma camada AutoCAD DWG.|
| Nível | Uma área de um edifício em uma elevação definida. Por exemplo, o chão de um edifício. |
| Xref  |Um ficheiro no formato de ficheiro AutoCAD DWG (.dwg) ligado ao desenho primário como referência externa.  |
| Funcionalidade | Um objeto que combina uma geometria com informações adicionais de metadados. |
| Aulas de Recurso | Um plano comum para características. Por exemplo, uma Unidade é uma classe de recurso, e um escritório é uma característica. |

## <a name="drawing-package-structure"></a>Estrutura do pacote de desenho

Um pacote de desenho é um arquivo .zip que contém os seguintes ficheiros:

* Ficheiros DWG em formato de ficheiro AutoCAD DWG.
* Um _manifesto.json_ para uma única instalação.

Os ficheiros DWG podem ser organizados de qualquer forma dentro da pasta, mas o ficheiro manifesto deve viver no diretório raiz da pasta. A pasta deve ser fechada num único ficheiro de arquivo, com uma extensão .zip. As secções seguintes detalham os requisitos para os ficheiros DWG, ficheiro manifesto e o conteúdo destes ficheiros.  

## <a name="dwg-files-requirements"></a>Requisitos de ficheiros DWG

É necessário um único ficheiro DWG para cada nível da instalação. Os dados do nível devem ser contidos num único ficheiro DWG. Quaisquer referências_externas (xrefs)_ devem estar ligadas ao desenho dos pais. Adicionalmente, cada ficheiro DWG:

* Deve definir as camadas _Exterior_ e _Unidade._ Pode definir opcionalmente as seguintes camadas opcionais: _Parede,_ _Porta,_ _Rótulo Unitlabel,_ _Zona_, e _Rótulo de Zona_.
* Não deve conter características de vários níveis.
* Não deve conter características de várias instalações.

O [serviço de conversão Do Azure Maps](https://docs.microsoft.com/rest/api/maps/data/conversion) pode extrair as seguintes classes de recurso de um ficheiro DWG:

* Níveis
* Unidades
* Zonas
* Aberturas
* Paredes
* Penetrações verticais

Todos os trabalhos de conversão resultam num conjunto mínimo de categorias padrão: sala, estrutura.parede, abertura.porta, zona e instalação. Categorias adicionais são para cada nome de categoria referenciado por objetos.  

Uma camada DWG deve conter características de uma única classe. As aulas não devem partilhar uma camada. Por exemplo, unidades e paredes não podem partilhar uma camada.

As camadas DWG devem também seguir os seguintes critérios:

* As origens dos desenhos para todos os ficheiros DWG devem alinhar-se à mesma latitude e longitude.
* Cada nível deve estar na mesma orientação que os outros níveis.
* Os polígonos auto-interseccionados serão reparados automaticamente, e o serviço de [conversão do Azure Maps](https://docs.microsoft.com/rest/api/maps/data/conversion) levantará um aviso. Recomenda-se inspecionar manualmente os resultados reparados, uma vez que podem não corresponder aos resultados esperados.

Todas as entidades de camada devem ser um dos seguintes tipos: Linha, PolyLine, Polygon, Arco Circular, Círculo, Texto (linha única). Qualquer outro tipo de entidade será ignorado.

A tabela abaixo descreve os tipos de entidades suportadas e as características suportadas para cada camada. Se uma camada contiver tipos de entidades não suportadas, então o serviço de [conversão do Azure Maps](https://docs.microsoft.com/rest/api/maps/data/conversion) ignorará estas entidades.  

| Camada | Tipos de entidade | Funcionalidades |
| :----- | :-------------------| :-------
| [Exterior](#exterior-layer) | Polígono, PolyLine (fechado), Círculo | Níveis
| [Unidade](#unit-layer) |  Polígono, PolyLine (fechado), Círculo | Penetrações verticais, Unidades
| [Muro](#wall-layer)  | Polígono, PolyLine (fechado), Círculo | Não aplicável. Para mais informações, consulte a [camada de Parede.](#wall-layer)
| [Porta](#door-layer) | Polígono, PolyLine, Linha, CircularArc, Círculo | Aberturas
| [Zona](#zone-layer) | Polígono, PolyLine (fechado), Círculo | Zona
| [Rótulo unitário](#unitlabel-layer) | Texto (linha única) | Não aplicável. Esta camada só pode adicionar propriedades às características da unidade a partir da camada De Unidades. Para mais informações, consulte a [camada UnitLabel](#unitlabel-layer).
| [ZoneLabel](#zonelabel-layer) | Texto (linha única) | Não aplicável. Esta camada só pode adicionar propriedades às características da zona a partir da ZonesLayer. Para mais informações, consulte a [camada ZoneLabel](#zonelabel-layer)

As secções seguintes detalham os requisitos para cada camada.

### <a name="exterior-layer"></a>Camada exterior

O ficheiro DWG para cada nível deve conter uma camada para definir o perímetro desse nível. Esta camada é referida como a camada exterior. Por exemplo, se uma instalação contiver dois níveis, então precisa de dois ficheiros DWG, com uma camada exterior para cada ficheiro.

Agora, importa quantos desenhos de entidades estão na camada exterior, o conjunto de [dados de instalação resultante](tutorial-creator-indoor-maps.md#create-a-feature-stateset) conterá apenas **uma** característica de nível para cada ficheiro DWG. Além disso,

* Os exteriores devem ser desenhados como Polygon, PolyLine (fechado), Circle.

* Os exteriores podem sobrepor-se, mas serão dissolvidos numa geometria.

Se a camada contiver múltiplas PoliLines sobrepostas, então o PolyLines será dissolvido numa única função de Nível. Alternativamente, se a camada contiver vários non_overlapping PolyLines, a função de Nível resultante terá uma representação multi-poligonal.

Um exemplo da camada exterior pode ser visto como a camada DECONTORNO no [pacote de desenho da amostra](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unit-layer"></a>Camada de unidade

O ficheiro DWG para cada nível deve definir uma camada contendo unidades.  As unidades são espaços navegáveis no edifício, tais como escritórios, corredores, escadas e elevadores. A camada unidadedeve respeitar os seguintes requisitos:

* As unidades devem ser desenhadas como Polygon, PolyLine (fechada), Circle.
* As unidades devem cair dentro dos limites do perímetro exterior da instalação.
* As unidades não devem sobrepor-se parcialmente.
* As unidades não devem conter qualquer geometria auto-interseccionada.

 Nomeie uma unidade criando um objeto de texto na camada _unitLabel_ e, em seguida, coloque o objeto dentro dos limites da unidade. Para mais informações, consulte a [camada UnitLabel](#unitlabel-layer).

Um exemplo da camada Unidades pode ser visto como a camada UNIDADES no [pacote de desenho da amostra](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="wall-layer"></a>Camada de parede

O ficheiro DWG para cada nível pode conter uma camada que define as extensões físicas de paredes, colunas e outra estrutura de construção.

* As paredes devem ser desenhadas como Polygon, PolyLine (fechada), Circle.
* A camada de parede deve conter apenas geometria que é interpretada como estrutura de construção.

Um exemplo da camada parede pode ser visto como a camada PAREDE no [pacote de desenho da amostra](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="door-layer"></a>Camada da porta

Pode incluir uma camada DWG contendo portas. Cada porta deve sobrepor-se à borda de uma unidade a partir da camada unitária.

As aberturas de portas num conjunto de dados do Azure Maps são representadas como um segmento de linha única que se sobrepõe a vários limites da unidade. São dados os seguintes passos para converter geometria na camada da porta para abrir características num conjunto de dados.

![Passos para gerar aberturas](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Camada de zona

O ficheiro DWG para cada nível pode conter uma camada de zona que define as extensões físicas das zonas. Uma zona pode ser um espaço vazio interior ou um quintal.

* As zonas devem ser desenhadas como Polygon, PolyLine (fechada), Circle.
* As zonas podem sobrepor-se.
* As zonas podem cair dentro ou fora do perímetro exterior da instalação.

Nomeie uma zona criando um objeto de texto na camada _zoneLabel_ e colocando o objeto de texto dentro dos limites da zona. Para mais informações, consulte [a camada ZoneLabel](#zonelabel-layer).

Um exemplo da camada de Zonas pode ser visto como a camada ZONES no [pacote de desenho da amostra](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unitlabel-layer"></a>Camada UnitLabel

O ficheiro DWG para cada nível pode conter uma camada de etiqueta de unidade. A camada de etiqueta de unidade adiciona uma propriedade de nome às unidades extraídas da camada Unit. Unidades com propriedade de nome podem ter detalhes adicionais especificados no ficheiro manifesto.

* As etiquetas unitárias devem ser entidades de texto de linha única.
* As etiquetas de unidade devem cair dentro dos limites da sua unidade.
* As unidades não devem conter várias entidades de texto na camada de etiquetas unitárias.

Um exemplo da camada UnitLabel pode ser visto como a camada UNITLABELS na [embalagem de desenho](https://github.com/Azure-Samples/am-creator-indoor-data-examples)da amostra .

### <a name="zonelabel-layer"></a>Camada ZoneLabel

O ficheiro DWG para cada nível pode conter uma camada de etiqueta de zona. Esta camada adiciona uma propriedade de nome às zonas extraídas da camada zone. As zonas com uma propriedade de nome podem ter detalhes adicionais especificados no ficheiro manifesto.

* As etiquetas das zonas devem ser entidades de texto de linha única.
* As etiquetas das zonas devem cair dentro dos limites da sua zona.
* As zonas não devem conter múltiplas entidades de texto na camada de etiquetas de zona.

Um exemplo da camada de marca de zona pode ser visto como a camada ZONELABELS no [pacote de desenho da amostra](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="manifest-file-requirements"></a>Requisitos de ficheiros manifestos

A pasta zip deve conter um ficheiro manifesto no nível raiz do diretório, e o ficheiro deve ser nomeado **manifesto.json**. Descreve os ficheiros DWG para permitir que o serviço de [conversão do Azure Maps](https://docs.microsoft.com/rest/api/maps/data/conversion) analise o seu conteúdo. Só os ficheiros identificados pelo manifesto serão ingeridos. Os ficheiros que estão na pasta zip, mas que não estão devidamente listados no manifesto, serão ignorados.

Os caminhos dos ficheiros, no objeto de **construçãoLevels** do ficheiro manifesto, devem ser relativos à raiz da pasta zip. O nome do ficheiro DWG deve corresponder exatamente ao nome do nível de instalação. Por exemplo, um ficheiro DWG para o nível "Cave" seria "Cave.dwg". Um ficheiro DWG para o nível 2 seria nomeado como "level_2.dwg". Use um sublinhado, se o seu nome de nível tiver um espaço. 

Embora existam requisitos ao utilizar os objetos manifestos, nem todos os objetos são necessários. A tabela abaixo mostra os objetos necessários e opcionais para a versão 1.1 do serviço de [conversão do Azure Maps](https://docs.microsoft.com/rest/api/maps/data/conversion).

| Objeto | Obrigatório | Descrição |
| :----- | :------- | :------- |
| direyInfo | true | Delineia as informações geográficas e de contacto das instalações. Também pode ser usado para delinear uma informação geográfica e de contacto do ocupante. |
| edifícioNíveis | true | Especifica os níveis dos edifícios e os ficheiros que contêm a conceção dos níveis. |
| georeferência | true | Contém informações geográficas numéricas para o desenho das instalações. |
| dwgLayers | true | Lista os nomes das camadas, e cada camada lista os nomes das suas próprias características. |
| unitProperties | false | Pode ser usado para inserir metadados adicionais para as funcionalidades da unidade. |
| zonaPropriedades | false | Pode ser usado para inserir metadados adicionais para as características da zona. |

As secções seguintes detalham os requisitos para cada objeto.

### <a name="directoryinfo"></a>direyInfo

| Propriedade  | tipo | Obrigatório | Descrição |
|-----------|------|----------|-------------|
| name      | cadeia/int | true   |  Nome do prédio. |
| streetAddress|    cadeia/int |    false    | Endereço do edifício. |
|unit     | cadeia/int    |  false    |  Unidade no edifício. |
| localidade |    cadeia/int |    false |    Nome de uma área, bairro ou região. Por exemplo, "Overlake" ou "Distrito Central". A localidade não faz parte do endereço de correio. |
| adminDivisions |    Matriz json de cordas |    false     | Um conjunto contendo designações de endereço (País, Estado, Cidade) ou (País, Prefeitura, Cidade, Cidade). Utilize códigos de país ISO 3166 e códigos de estado/território ISO 3166-2. |
| Código postal |    cadeia/int    | false    | O código de classificação do correio. |
| horas Ofoperação |    string |     false | Adere ao formato [AsM Opening Hours.](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification) |
| telefone    | cadeia/int |    false |    Número de telefone associado ao edifício. Deve incluir o código do país. |
| site    | string |    false    | Site associado ao edifício. M comece com http ou https. |
| não Público |    bool    | false | Bandeira especificando se o edifício está aberto ao público. |
| âncoraLatitude | numeric |    false | Latitude de uma âncora de instalação (pino). |
| âncoraLongitude | numeric |    false | Longitude de uma âncora de instalação (pino). |
| âncoraHeightAboveSeaLevel  | numeric | false | Altura do rés do chão da instalação acima do nível do mar, em metros. |
| padrãoLevelLevelVerticalExtent | numeric | false | Altura padrão (espessura) de um nível desta instalação para utilizar quando um nível `verticalExtent` é indefinido. |

### <a name="buildinglevels"></a>edifícioNíveis

O `buildingLevels` objeto contém uma gama json de níveis de edifícios.

| Propriedade  | Tipo | Necessário | Descrição |
|-----------|------|----------|-------------|
|nome de nível    |cadeia/int    |true |    Nome de nível descritivo. Por exemplo: Piso 1, Lobby, Estacionamento Azul, Cave, e assim por diante.|
|ordinal | número inteiro |    true | O ordinal é utilizado para determinar a ordem vertical dos níveis. Todas as instalações devem ter um nível com o Ordinal 0. |
|alturaAboveFacilityAnchor | numeric |    false |    Altura de nível acima do rés do chão em metros. |
| verticalExtent | numeric | false | Altura do chão ao teto (espessura) do nível dos contadores. |
|filename |    cadeia/int |    true |    Caminho do sistema de ficheiros do desenho CAD para um nível de construção. Deve ser relativo à raiz do ficheiro zip do edifício. |

### <a name="georeference"></a>georeferência

| Propriedade  | Tipo | Necessário | Descrição |
|-----------|------|----------|-------------|
|lat    | numeric |    true |    Representação decimal de graus de latitude na origem do desenho das instalações. As coordenadas de origem devem estar no WGS84 Web Mercator `EPSG:3857` ().|
|Lon    |numeric|    true|    Representação decimal de graus de longitude na origem do desenho das instalações. As coordenadas de origem devem estar no WGS84 Web Mercator `EPSG:3857` (). |
|angle|    numeric|    true|   O ângulo no sentido dos ponteiros do relógio, em graus, entre o verdadeiro norte e o eixo vertical (Y) do desenho.   |

### <a name="dwglayers"></a>dwgLayers

| Propriedade  | Tipo | Necessário | Descrição |
|-----------|------|----------|-------------|
|exterior    |Conjunto de cordas/ints|    true|    Nomes de camadas que definem o perfil exterior do edifício.|
|unit|    Conjunto de cordas/ints|    true|    Nomes de camadas que definem unidades.|
|parede|    Conjunto de cordas/ints    |false|    Nomes de camadas que definem paredes.|
|porta    |Conjunto de cordas/ints|    false   | Nomes de camadas que definem portas.|
|unitLabel    |Conjunto de cordas/ints|    false    |Nomes de camadas que definem nomes de unidades.|
|zona | Conjunto de cordas/ints    | false    | Nomes de camadas que definem zonas.|
|zonaRótulo | Conjunto de cordas/ints |     false |    Nomes de camadas que definem nomes de zonas.|

### <a name="unitproperties"></a>unitProperties

O `unitProperties` objeto contém um conjunto json de propriedades unitárias.

| Propriedade  | Tipo | Necessário | Descrição |
|-----------|------|----------|-------------|
|nome unit    |cadeia/int    |true    |Nome de unidade para associar a este `unitProperty` registo. Este registo só é válido quando se encontra uma etiqueta correspondente `unitName` na `unitLabel` ou nas camadas. |
|categoriaNome|    cadeia/int|    false    |Nome da categoria. Para obter uma lista completa de categorias, consulte [categorias](https://aka.ms/pa-indoor-spacecategories). |
|navegávelBy| Conjunto de cordas |    false    |Indica os tipos de agentes de navegação que podem atravessar a unidade. Por exemplo, "pedestre". Esta propriedade informará as capacidades de localização.  Os valores permitidos são, , , , `pedestrian` `wheelchair` , , , , `machine` e `bicycle` `automobile` `hiredAuto` `bus` `railcar` `emergency` `ferry` `boat` `disallowed` .|
|routeThroughBehavior|    string|    false    |A rota através do comportamento para a unidade. Os valores permitidos `disallowed` `allowed` são, e `preferred` . O valor predefinido é `allowed` .|
|ocupantes    |Conjunto de objetos directyInfo |false    |Lista de ocupantes para a unidade. |
|nomeAlt|    cadeia/int|    false|    Nome alternativo da unidade. |
|nomeSubtítulo|    cadeia/int    |false|    Legenda da unidade. |
|endereçoRoomNumber|    cadeia/int|    false|    Quarto/Unidade/Apartamento/Suite número da unidade.|
|categoria vertical de penetração|    cadeia/int|    false| Quando esta propriedade for definida, a característica resultante será uma Penetração Vertical (VRT) em vez de uma unidade. Os VRTs podem ser usados para navegar para outras funcionalidades vrt nos níveis acima ou abaixo dele. Penetração vertical é um nome [de categoria.](https://aka.ms/pa-indoor-spacecategories) Se esta propriedade for definida, a propriedade de categoriaName é sobrerovercom categoria vertical DePenetração. |
|verticalPenetraçãoDirection|    string|    false    |Se `verticalPenetrationCategory` estiver definido, defina opcionalmente a direção válida da viagem. Os valores permitidos `lowToHigh` `highToLow` são, `both` e `closed` . O valor predefinido é `both` .|
| não Público | bool | false | Indica se a unidade está aberta ao público. |
| isRoutable | bool | false | Quando definido para `false` , a unidade não pode ser navegada para, ou através. O valor predefinido é `true` . |
| isOpenArea | bool | false | Permite que o agente de navegação entre no aparelho sem a necessidade de uma abertura ligada à unidade. Por predefinição, este valor é definido para `true` a menos que a unidade tenha uma abertura. |

### <a name="the-zoneproperties-object"></a>O objeto zonaPropriedades

O `zoneProperties` objeto contém um conjunto JSON de propriedades de zona.

| Propriedade  | Tipo | Necessário | Descrição |
|-----------|------|----------|-------------|
|zoneName        |cadeia/int    |true    |Nome da zona para associar com `zoneProperty` o registo. Este registo só é válido quando uma etiqueta correspondente `zoneName` é encontrada na camada da `zoneLabel` zona.  |
|categoriaNome|    cadeia/int|    false    |Nome da categoria. Para obter uma lista completa de categorias, consulte [categorias](https://aka.ms/pa-indoor-spacecategories). |
|zoneNameAlt|    cadeia/int|    false    |Nome alternativo da zona.  |
|zoneNameSubtitle|    cadeia/int |    false    |Legenda da zona. |

### <a name="sample-drawing-package-manifest"></a>Manifesto de pacote de desenho de amostra

Abaixo está um arquivo manifesto de amostra para o pacote de desenho de amostras. Para descarregar todo o pacote, clique em [pacote de desenho de amostras](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

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

Assim que o seu pacote de desenho satisfizer os requisitos, poderá utilizar o serviço de [conversão do Azure Maps](https://docs.microsoft.com/rest/api/maps/data/conversion) para converter o pacote num conjunto de dados de mapas. Em seguida, pode utilizar o conjunto de dados para gerar um mapa interior utilizando o módulo Indoor Maps. Saiba mais sobre a utilização do módulo Indoor Maps lendo os seguintes artigos:

> [!div class="nextstepaction"]
>[Criador de mapas interiores](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Tutorial: Criar um mapa interior do Criador](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Estilo dinâmico do Mapa Interior](indoor-map-dynamic-styling.md)
