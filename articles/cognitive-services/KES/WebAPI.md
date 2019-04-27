---
title: Interface de Web API - API de serviço de exploração de conhecimento
titlesuffix: Azure Cognitive Services
description: Utilize a interface da API web para criar uma experiência de pesquisa ricas e semântica no conhecimento exploração de serviço (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 970db5984eedebf98bbb087cfd0b4a35a21a0f54
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60814429"
---
# <a name="web-api-interface"></a>Interface da API Web

Os arquivos de modelo criados pelo serviço de exploração de conhecimento podem ser hospedados e acedidos através de um conjunto de web APIs.  As APIs podem ser hospedadas no computador local com o [ `host_service` ](CommandLine.md#host_service-command) comando ou podem ser implementadas para um serviço cloud do Azure com o [ `deploy_service` ](CommandLine.md#deploy_service-command) comando.  Ambas as técnicas expõem os seguintes pontos finais da API:

* [*interpretar* ](interpretMethod.md) – interpreta uma cadeia de caracteres de consulta de linguagem natural. Devolve interpretações anotadas para permitir experiências de conclusão automática de caixa de pesquisa detalhada que antecipam o que o utilizador está a escrever.
* [*avaliar* ](evaluateMethod.md) – Evaluates e devolve o resultado de uma expressão de consulta estruturadas.
* [*calchistogram* ](calchistogramMethod.md) – calcula um histograma de valores de atributo para objetos devolvidos por uma expressão de consulta estruturadas.

Usados juntos, esses métodos de API permitem a criação de uma experiência de pesquisa de semântica avançada.  Uma cadeia de caracteres de consulta de linguagem natural, tendo em conta a *interpretar* método fornece versões anotadas da consulta de entrada com expressões de consulta estruturadas, com base nos dados subjacentes de gramática e índice.  O *avaliar* método avalia a expressão de consulta estruturadas e retorna os objetos de índice correspondente para exibição.  O *calchistogram* método computa as distribuições de valor de atributo para ativar a filtragem e o refinamento.

**Exemplo**

Num domínio publicações acadêmicas, se um utilizador escrever a cadeia de caracteres "latente s", o *interpretar* método pode fornecer um conjunto de interpretações classificadas, sugerindo que o utilizador pode procurar a palavra-chave "análise de semântica latente", o título "estrutura latente análise", ou outras expressões a partir do "latente s".  Estas informações podem ser utilizadas para orientar rapidamente o utilizador para os resultados de pesquisa pretendidos.

Para este domínio, o *avaliar* método pode ser utilizado para obter um conjunto de correspondência de publicações do índice para instituições académicas e o *calchistogram* método pode ser utilizado para calcular a distribuição do atributo valores para as publicações correspondentes, que podem ser utilizado para filtrar ainda mais e refinar os resultados da pesquisa.

Tenha em atenção que, para melhorar a legibilidade dos exemplos, as chamadas de REST API contêm carateres (por exemplo, espaços), que não possuem codificação URL. O seu código terá de aplicar as codificações URL apropriadas.
