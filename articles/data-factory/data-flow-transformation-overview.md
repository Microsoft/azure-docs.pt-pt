---
title: Visão geral da transformação do fluxo de dados de mapeamento
description: Uma visão geral das diferentes transformações disponíveis no fluxo de dados de mapeamento
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: bb5021c0125c3140ef44a1ec3304b9d0ac40c30f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060232"
---
# <a name="mapping-data-flow-transformation-overview"></a>Visão geral da transformação do fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Abaixo está uma lista das transformações atualmente suportadas no fluxo de dados de mapeamento. Clique em cada transformação para aprender os seus detalhes de configuração.

| Name | Categoria | Descrição |
| ---- | -------- | ----------- |
| [Agregação](data-flow-aggregate.md) | Modificador de esquemas | Defina diferentes tipos de agregações tais como SUM, MIN, MAX e COUNT agrupados por colunas existentes ou computadas. | 
| [Alterar linha](data-flow-alter-row.md) | Modificador de linha | Desação, eliminação, atualização e atualização das políticas em linhas. |
| [Divisão condicional](data-flow-conditional-split.md) | Múltiplas entradas/saídas | Encaminhar linhas de dados para diferentes fluxos com base em condições de correspondência. |
| [Coluna derivada](data-flow-derived-column.md) | Modificador de esquemas | gerar novas colunas ou modificar os campos existentes utilizando a linguagem de expressão de fluxo de dados. | 
| [Existe](data-flow-exists.md) | Múltiplas entradas/saídas | Verifique se os seus dados existem noutra fonte ou fluxo. | 
| [Filtro](data-flow-filter.md) | Modificador de linha | Filtre uma linha com base numa condição. |
| [Aplanar](data-flow-flatten.md) | Modificador de esquemas |  Pegue os valores de matriz dentro de estruturas hierárquicas como json e desenrolar-os em linhas individuais. |
| [Join](data-flow-join.md) | Múltiplas entradas/saídas |  Combine dados de duas fontes ou fluxos. |
| [Pesquisa](data-flow-lookup.md) | Múltiplas entradas/saídas | Dados de referência de outra fonte. |
| [Novo ramo](data-flow-new-branch.md) | Múltiplas entradas/saídas | Aplicar vários conjuntos de operações e transformações contra o mesmo fluxo de dados. |
| [Parse](data-flow-new-branch.md) | Formatter | Parse as colunas de texto no seu fluxo de dados que são cadeias de texto JSON, texto delimitado ou texto formatado XML. |
| [Dinamizar](data-flow-pivot.md) | Modificador de esquemas | Uma agregação onde uma ou mais colunas de agrupamento têm os seus valores de linha distintos transformados em colunas individuais. |
| [Posição](data-flow-rank.md) | Modificador de esquemas | Gerar um ranking ordenado com base em condições de classificação |
| [Selecionar](data-flow-select.md) | Modificador de esquemas | Colunas de pseudónimos e nomes de fluxo, e colunas de queda ou reencomenda |
| [Sink](data-flow-sink.md) | - | Um destino final para os seus dados |
| [Ordenar](data-flow-sort.md) | Modificador de linha | Ordenar linhas de entrada no fluxo de dados atual |
| [Origem](data-flow-source.md) | - | Uma fonte de dados para o fluxo de dados |
| [Chave de substituição](data-flow-surrogate-key.md) | Modificador de esquemas | Adicione um valor-chave arbitrário não-negócio incrementante |
| [União](data-flow-union.md) | Múltiplas entradas/saídas | Combine vários fluxos de dados verticalmente |
| [Anular dinamização](data-flow-unpivot.md) | Modificador de esquemas | Colunas de pivô em valores de linha |
| [Janela](data-flow-window.md) | Modificador de esquemas |  Defina agregações baseadas em janelas de colunas nos seus fluxos de dados. |
| [Parse](data-flow-parse.md) | Modificador de esquemas |  Dados da coluna de análise a Json ou texto delimitado |
