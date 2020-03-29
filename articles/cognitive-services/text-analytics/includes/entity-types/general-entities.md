---
title: Entidades nomeadas em geral
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: ba516a548fc8385ca86526a7f2dd082b27e53208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77211439"
---
## <a name="general-entity-types"></a>Tipos de entidades gerais:

### <a name="person"></a>Pessoa

Reconhecer nomes de pessoas em texto.

Idiomas:
* Pré-visualização `Czech` `Chinese-Simplified`pública, `Dutch` `English` `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` , `Danish` `Korean`, `Norwegian (Bokmål)` `Polish`, , , , , , , , , , , `Portuguese (Portugal)` `Arabic``Turkish`

| Nome do subtipo | Descrição                                                      | Disponível a partir da versão modelo |
|--------------|------------------------------------------------------------------|---------------------------------------|
| N/D          | Nomes de pessoas reconhecidas, por exemplo, `Bill Gates``Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>PersonType
Tipo de trabalho ou papel detido por uma pessoa.

Idiomas:
* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                                                                | Disponível a partir da versão modelo |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| N/D          | Tipos de `civil engineer`trabalho, por exemplo, `salesperson`, `chef`, `librarian``nursing aide` | `2020-02-01`                           |

### <a name="location"></a>Localização

Marcos naturais e humanos, estruturas, características geográficas e entidades geopolíticas.

Idiomas:

* Pré-visualização `Czech` `Chinese-Simplified`pública, `Dutch` `English` `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` , `Danish` `Korean`, `Norwegian (Bokmål)` `Polish`, , , , , , , , , , , `Portuguese (Portugal)` `Arabic``Turkish`

| Nome do subtipo              | Descrição                                                                              | Disponível a partir da versão modelo |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| N/D                       | locais, por `Atlantic Ocean` `library`exemplo, , `Eiffel Tower``Statue of Liberty`  | `2019-10-01`                           |
| Entidade Geopolítica (GPE) - Apenas em inglês| Cidades, países, estados, `South Africa`por exemplo, `Seattle` `Pennsylvania``Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>Organização  

Organizações reconhecidas, corporações, agências e outros grupos de pessoas. Por exemplo: empresas, grupos políticos, bandas musicais, clubes desportivos, órgãos governamentais e organizações públicas. Nacionalidades e religiões não estão incluídas neste tipo de entidade. 

Idiomas: 

* Pré-visualização `Czech` `Chinese-Simplified`pública, `Dutch` `English` `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish` , `Danish` `Korean`, `Norwegian (Bokmål)` `Polish`, , , , , , , , , , , `Portuguese (Portugal)` `Arabic``Turkish`

| Nome do subtipo | Descrição                                                                                             | Disponível a partir da versão modelo |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| N/D          | organizações, por `Microsoft` `NASA`exemplo, , `National Oceanic and Atmospheric Administration``VOA` | `2019-10-01`                           |

### <a name="event"></a>Evento  

Eventos históricos, sociais e naturais.  

Idiomas: 

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                                            | Disponível a partir da versão modelo |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| N/D          | Eventos `wedding` `hurricane`como, `car accident` `solar eclipse`, ,`American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Produto  

Objetos físicos de várias categorias.  

Idiomas: 

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                                                        | Disponível a partir da versão modelo |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| N/D          | Por `Microsoft Surface laptop`exemplo, `sunglasses` `motorcycle`, `bag`,`Xbox` | `2020-02-01`                           |
| Computação    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>Habilidade  

Uma entidade que descreve uma capacidade ou experiência.  

Idiomas: 

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                                                 | Disponível a partir da versão modelo |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| N/D          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>Número de Telefone

Números de telefone (apenas números de telefone dos EUA). 

Idiomas:

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                    | Disponível a partir da versão modelo |
|--------------|------------------------------------------------|----------------------------------------|
| N/D          | Números de telefone dos EUA, por exemplo`(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>Email

Endereço de e-mail. 

Idiomas:

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                      | Disponível a partir da versão modelo |
|--------------|--------------------------------------------------|----------------------------------------|
| N/D          | Endereço de e-mail, por exemplo`support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>do IdP

URLs de Internet.

Idiomas:

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                          | Disponível a partir da versão modelo |
|--------------|------------------------------------------------------|----------------------------------------|
| N/D          | URLs para websites, por exemplo`https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>Endereço IP

Endereço de protocolo de Internet

Idiomas:

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                              | Disponível a partir da versão modelo |
|--------------|------------------------------------------|----------------------------------------|
| N/D          | Endereço de rede, por exemplo,`10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>DateTime

Entidades de data e hora. 

* Disponível a partir da versão modelo`2019-10-01`

Idiomas:

* Pré-visualização `English` `French`pública: `Chinese-Simplified`, , e `German``Spanish`

| Nome do subtipo    | Exemplos                     |
|-------------|------------------------------|
| N/D         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Date  | `May 2nd, 2017`, `05/02/2017`       |
| Hora     | `8:15`, `6AM`              |
| DataRange    | `August 2nd to August 5th`         |
| Intervalo de tempo   | `4-6PM`, `10:00AM to Noon`          |
| Duração | `2.5 minutes`, `one and a half hours`         |
| Definir | `every Saturday`         |

###  <a name="quantity"></a>Quantidade

Números e quantidades numéricas. 

* Disponível a partir da versão modelo`2019-10-01`

Idiomas:

* Pré-visualização `English` `French`pública: `Chinese-Simplified`, , e `German``Spanish`

| Nome do subtipo    | Exemplos                     |
|-------------|------------------------------|
| Número         | `6`, `six`                   |
| Percentagem  | `50%`, `fifty percent`       |
| Ordinal     | `2nd`, `second`              |
| Idade         | `90 day old`, `30 years old` |
| Moeda    | `$10.99`, `€30.00`           |
| Dimensão   | `10 miles`, `40 cm`          |
| Temperatura | `32 degrees`, `10°C`         |
