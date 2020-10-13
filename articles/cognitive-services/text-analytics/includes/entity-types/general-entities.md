---
title: Entidades nomeadas em geral
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 630f04bf2cc9e7de6331f9d25754a20fe2327d76
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779880"
---
As seguintes categorias de entidades são devolvidas ao enviar pedidos para o `/entities/recognition/general` ponto final.

| Categoria   | Subcategoria | Descrição                          | Versão do modelo inicial                                                    | Notas |
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
| Data e Hora | Data | Datas de Calender. | `2019-10-01` | Também devolvido por NER v2.1 |
| Data e Hora | Hora | Horas do dia | `2019-10-01` | Também devolvido por NER v2.1 |
| Data e Hora | Intervalo de Datas | Intervalos de data. | `2019-10-01` | Também devolvido por NER v2.1 |
| Data e Hora | Intervalo de Tempo | Intervalos de tempo. | `2019-10-01` | Também devolvido por NER v2.1 |
| Data e Hora | Duração | As durações. | `2019-10-01` | Também devolvido por NER v2.1 |
| Data e Hora | Definido para | Set, vezes repetidas. |  `2019-10-01` | Também devolvido por NER v2.1 |
| Quantidade | N/D | Números e quantidades numéricas. | `2019-10-01` | Também devolvido por NER v2.1  |
| Quantidade | Número | Números. | `2019-10-01` | Também devolvido por NER v2.1 |
| Quantidade | Percentagem | Percentagens.| `2019-10-01` | Também devolvido por NER v2.1 |
| Quantidade | Ordinal | Números ordinais. | `2019-10-01` | Também devolvido por NER v2.1 |
| Quantidade | Idade | Idades. | `2019-10-01` |  Também devolvido por NER v2.1 |
| Quantidade | Moeda | Moedas, moedas. | `2019-10-01` | Também devolvido por NER v2.1 |
| Quantidade | Dimensão | Dimensões e medições. | `2019-10-01` | Também devolvido por NER v2.1 |
| Quantidade | Temperatura | As temperaturas. | `2019-10-01` | Também devolvido por NER v2.1 |
