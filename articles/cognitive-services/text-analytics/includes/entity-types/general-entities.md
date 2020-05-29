---
title: Entidades nomeadas em geral
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 32e80c50ff6f543679852cbd7e5ce9bda92d01e1
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140948"
---
As seguintes categorias de entidades são devolvidas ao enviar pedidos para o `/entities/recognition/general` ponto final.

| Categoria   | Subcategory | Descrição                          | Versão do modelo inicial                                                    | Notas |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| Pessoa     | N/D         | Nomes de pessoas.  | `2019-10-01`  | Também devolvido por NER v2.1 |
| PersonType | N/D         | Tipos de emprego ou funções detidas por uma pessoa. | `2020-02-01` | |
|Localização    | N/D         | Marcos naturais e humanos, estruturas, características geográficas e entidades geopolíticas     |  `2019-10-01` | Também devolvido por NER v2.1 |
|Localização     | Entidade Geopolítica (GPE)        | Cidades, países/regiões, estados.      | `2020-02-01` | |
|Localização     | Estrutural                       | Estruturas artificiais. | `2020-04-01` | |
|Localização     | Geográfica       | Características geográficas e naturais como rios, oceanos e desertos. |  `2020-04-01` | |
|Organização  | N/D | Empresas, grupos políticos, bandas musicais, clubes desportivos, organismos governamentais e organizações públicas.  | `2019-10-01` | As nacionalidades e religiões não estão incluídas neste tipo de entidade. Também devolvido por NER v2.1 |
|Organização | Médico | Empresas médicas e grupos. | `2020-04-01` |  |
|Organização | Bolsa de valores | Grupos de bolsa. | `2020-04-01` | |
| Organização | Desportos | Organizações relacionadas com o desporto. | `2020-04-01` |  |
| Evento  | N/D | Eventos históricos, sociais e naturais. | `2020-02-01` |  |
| Evento  | Cultural | Eventos culturais e feriados. | `2020-04-01` | |
| Evento  | Natural | Eventos naturais. | `2020-04-01` |  |
| Evento  | Desportos | Eventos desportivos.  | `2020-04-01` | |
| Produto | N/D | Objetos físicos de várias categorias. | `2020-02-01` | |
| Produto | Produtos de computação | Produtos de computação. |  `2020-02-01 ` | |
| Habilidade | N/D | Uma capacidade, habilidade ou perícia. | `2020-02-01` |  |
| Endereço | N/D | Endereços de correio completos.  | `2020-04-01` |  |
| PhoneNumber | N/D | Números de telefone (apenas números de telefone dos EUA e da UE). | `2019-10-01` | Também devolvido por NER v2.1 |
| E-mail | N/D | Endereços de e-mail. | `2019-10-01` | Também devolvido por NER v2.1 |
| URL | N/D | URLs para sites. | `2019-10-01` | Também devolvido por NER v2.1  |
| IP | N/D | Endereços IP de rede. | `2019-10-01` | Também devolvido por NER v2.1 |
| DateTime | N/D | Datas e horas do dia. | `2019-10-01` | Também devolvido por NER v2.1 | 
| DateTime | Data | Datas de Calender. | `2019-10-01` | Também devolvido por NER v2.1 |
| DateTime | Hora | Horas do dia | `2019-10-01` | Também devolvido por NER v2.1 |
| DateTime | DateRange | Intervalos de data. | `2019-10-01` | Também devolvido por NER v2.1 |
| DateTime | TimeRange | Intervalos de tempo. | `2019-10-01` | Também devolvido por NER v2.1 |
| DateTime | Duração | As durações. | `2019-10-01` | Também devolvido por NER v2.1 |
| DateTime | Definir | Set, vezes repetidas. |  `2019-10-01` | Também devolvido por NER v2.1 |
| Quantidade | N/D | Números e quantidades numéricas. | `2019-10-01` | Também devolvido por NER v2.1  |
| Quantidade | Número | Os números. | `2019-10-01` | Também devolvido por NER v2.1 |
| Quantidade | Percentagem | Percentagens.| `2019-10-01` | Também devolvido por NER v2.1 |
| Quantidade | Ordinal | Números ordinais. | `2019-10-01` | Também devolvido por NER v2.1 |
| Quantidade | Idade | Idades. | `2019-10-01` |  Também devolvido por NER v2.1 |
| Quantidade | Moeda | Moedas, moedas. | `2019-10-01` | Também devolvido por NER v2.1 |
| Quantidade | Dimensão | Dimensões e medições. | `2019-10-01` | Também devolvido por NER v2.1 |
| Quantidade | Temperatura | As temperaturas. | `2019-10-01` | Também devolvido por NER v2.1 |
