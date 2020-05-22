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
ms.openlocfilehash: 60cd6b8ba2cbfca497ae1b92113b12e85a34bf26
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83778297"
---
## <a name="general-entity-types"></a>Tipos de entidades gerais:

### <a name="person"></a>Pessoa

Reconhecer nomes de pessoas em texto.

Idiomas:
* Pré-visualização pública, , , , , , , , , , , , , `Arabic` `Czech` , `Chinese-Simplified` `Danish` `Dutch` `English` `Finnish` `French` , `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` , `Spanish` `Swedish``Turkish`

| Nome do subtipo | Descrição                                                      | Disponível a partir da versão modelo |
|--------------|------------------------------------------------------------------|---------------------------------------|
| N/D          | Nomes de pessoas reconhecidas, por `Bill Gates` exemplo,`Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>PersonType
Tipo de trabalho ou papel detido por uma pessoa.

Idiomas:
* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                                                                | Disponível a partir da versão modelo |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| N/D          | Tipos de trabalho, por `civil engineer` `salesperson` exemplo, `chef` , `librarian` ,`nursing aide` | `2020-02-01`                           |

### <a name="location"></a>Localização

Marcos naturais e humanos, estruturas, características geográficas e entidades geopolíticas.

Idiomas:

* Pré-visualização pública, , , , , , , , , , , , , `Arabic` `Czech` , `Chinese-Simplified` `Danish` `Dutch` `English` `Finnish` `French` , `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` , `Spanish` `Swedish``Turkish`

| Nome do subtipo              | Descrição                                                                              | Disponível a partir da versão modelo |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| N/D                       | locais, por `Atlantic Ocean` `library` exemplo, `Eiffel Tower` ,`Statue of Liberty`  | `2019-10-01`                           |
| Entidade Geopolítica (GPE) - Apenas em inglês| Cidades, países/regiões, estados, por `Seattle` `Pennsylvania` exemplo, `South Africa``Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>Organização  

Organizações reconhecidas, corporações, agências e outros grupos de pessoas. Por exemplo: empresas, grupos políticos, bandas musicais, clubes desportivos, órgãos governamentais e organizações públicas. Nacionalidades e religiões não estão incluídas neste tipo de entidade. 

Idiomas: 

* Pré-visualização pública, , , , , , , , , , , , , `Arabic` `Czech` , `Chinese-Simplified` `Danish` `Dutch` `English` `Finnish` `French` , `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` , `Spanish` `Swedish``Turkish`

| Nome do subtipo | Descrição                                                                                             | Disponível a partir da versão modelo |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| N/D          | organizações, por `Microsoft` `NASA` exemplo, `National Oceanic and Atmospheric Administration` ,`VOA` | `2019-10-01`                           |

### <a name="event"></a>Evento  

Eventos históricos, sociais e naturais.  

Idiomas: 

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                                            | Disponível a partir da versão modelo |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| N/D          | Eventos `wedding` `hurricane` como, `car accident` , , `solar eclipse``American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Produto  

Objetos físicos de várias categorias.  

Idiomas: 

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                                                        | Disponível a partir da versão modelo |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| N/D          | Por exemplo, `Microsoft Surface laptop` `sunglasses` , `motorcycle` , `bag``Xbox` | `2020-02-01`                           |
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

### <a name="email"></a>E-mail

Endereço de e-mail. 

Idiomas:

* Pré-visualização pública:`English`

| Nome do subtipo | Descrição                                      | Disponível a partir da versão modelo |
|--------------|--------------------------------------------------|----------------------------------------|
| N/D          | Endereço de e-mail, por exemplo`support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>URL

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

* Pré-visualização pública: `Chinese-Simplified` `English` , , `French` `German` e`Spanish`

| Nome do subtipo    | Exemplos                     |
|-------------|------------------------------|
| N/D         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Data  | `May 2nd, 2017`, `05/02/2017`       |
| Hora     | `8:15`, `6AM`              |
| DataRange    | `August 2nd to August 5th`         |
| Intervalo de tempo   | `4-6PM`, `10:00AM to Noon`          |
| Duração | `2.5 minutes`, `one and a half hours`         |
| Definir | `every Saturday`         |

###  <a name="quantity"></a>Quantidade

Números e quantidades numéricas. 

* Disponível a partir da versão modelo`2019-10-01`

Idiomas:

* Pré-visualização pública: `Chinese-Simplified` `English` , , `French` `German` e`Spanish`

| Nome do subtipo    | Exemplos                     |
|-------------|------------------------------|
| Número         | `6`, `six`                   |
| Percentagem  | `50%`, `fifty percent`       |
| Ordinal     | `2nd`, `second`              |
| Idade         | `90 day old`, `30 years old` |
| Moeda    | `$10.99`, `&euro;30.00`           |
| Dimensão   | `10 miles`, `40 cm`          |
| Temperatura | `32 degrees`, `10°C`         |
