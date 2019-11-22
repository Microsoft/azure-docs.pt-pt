---
title: Entidades nomeadas gerais
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: aahi
ms.openlocfilehash: a4c911fa077c2ec332974fb2f0c5abcdef21307c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284150"
---
## <a name="general-entity-types"></a>Tipos de entidade geral:

### <a name="person"></a>Pessoa
Nomes reconhecidos e outras pessoas em texto.
Idiomas:
* Visualização pública: `English`

| Nome do subtipo | Descrição             |
|--------------|-------------------------|
| N/D          | Nomes reconhecidos, por exemplo `Bill Gates`, `Marie Curie` |

### <a name="location"></a>Localização

Pontos de referência naturais e humanos, estruturas e recursos geográficos.

Idiomas:


* Visualização pública: `English`

| Nome do subtipo | Descrição                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| N/D          | locais, por exemplo `Atlantic Ocean`, `library`, `Eiffel Tower``Statue of Liberty` |

### <a name="organization"></a>Organização  

Organizações reconhecidas, corporações, agências e outros grupos de pessoas. Por exemplo: empresas, grupos políticos, faixas musicais, clubes esporte, órgãos governamentais e organizações públicas. As nacionalidades e Religions não são incluídas nesse tipo de entidade. Idiomas: 

* Visualização pública: `English`

| Nome do subtipo | Descrição                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| N/D          | organizações, por exemplo `Microsoft`, `NASA` `National Oceanic and Atmospheric Administration` |

### <a name="phone-number"></a>Número de telefone

Números de telefone (somente números de telefone dos EUA). 

Idiomas:


* Visualização pública: `English`

| Nome do subtipo | Descrição                                  |
|----------|----------------------------------------------|
| N/D         | Números de telefone dos EUA, por exemplo `(312) 555-0176`. |

### <a name="email"></a>Email

Endereço de e-mail. 

Idiomas:


* Visualização pública: `English`

| Nome do subtipo | Descrição                                  |
|----------|----------------------------------------------|
| N/D         | Endereço de email, por exemplo `support@contoso.com` |

### <a name="url"></a>do IdP

URLs da Internet.

Idiomas:


* Visualização pública: `English`

| Nome do subtipo | Descrição                                           |
|----------|-------------------------------------------------------|
| N/D         | URLs para sites, por exemplo `https://www.bing.com`. |

###  <a name="number"></a>Número

Números e quantidades numéricas. 

Idiomas:


* Visualização pública: `English`

| Nome do subtipo    | Exemplos                     |
|-------------|------------------------------|
| N/D         | `6`, `six`                   |
| Percentagem  | `50%`, `fifty percent`       |
| Ordinal     | `2nd`, `second`              |
| Moeda    | `$10.99`, `€30.00`           |
| Dimensão   | `10 miles`, `40 cm`          |
| Temperatura | `32 degrees`, `10°C`         |
