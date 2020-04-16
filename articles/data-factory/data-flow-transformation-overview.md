---
title: Mapeando a visão geral da transformação do fluxo de dados
description: Uma visão geral das diferentes transformações disponíveis no fluxo de dados de mapeamento
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 68423bee6096357add9b5d4b107c984ac67c9cab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81412972"
---
# <a name="mapping-data-flow-transformation-overview"></a>Mapeando a visão geral da transformação do fluxo de dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Abaixo está uma lista das transformações atualmente suportadas no fluxo de dados de mapeamento. Clique em cada transformação para aprender os seus detalhes de configuração.

| Nome | Categoria | Descrição |
| ---- | -------- | ----------- |
| [Agregação](data-flow-aggregate.md) | Modificador de esquemas | Defina diferentes tipos de agregados tais como SUM, MIN, MAX e COUNT agrupados por colunas existentes ou computadas. | 
| [Alterar linha](data-flow-alter-row.md) | Modificador de remo | Definir as políticas de inserção, eliminar, atualizar e aumentar as políticas nas linhas. |
| [Divisão condicional](data-flow-conditional-split.md) | Múltiplas entradas/saídas | Direciona as linhas de dados para diferentes fluxos com base em condições correspondentes. |
| [Coluna derivada](data-flow-derived-column.md) | Modificador de esquemas | gerar novas colunas ou modificar os campos existentes utilizando a linguagem de expressão do fluxo de dados. | 
| [Existe](data-flow-exists.md) | Múltiplas entradas/saídas | Verifique se os seus dados existem noutra fonte ou fluxo. | 
| [Filtro](data-flow-filter.md) | Modificador de remo | Filtre uma linha com base numa condição. |
| [Aplanar](data-flow-flatten.md) | Modificador de esquemas |  Tome os valores de matriz dentro de estruturas hierárquicas, como jSON e desenrolar-os em linhas individuais. |
| [Aderir](data-flow-join.md) | Múltiplas entradas/saídas |  Combine dados de duas fontes ou streams. |
| [Pesquisa](data-flow-lookup.md) | Múltiplas entradas/saídas | Dados de referência de outra fonte. |
| [Novo ramo](data-flow-new-branch.md) | Múltiplas entradas/saídas | Aplique vários conjuntos de operações e transformações contra o mesmo fluxo de dados. |
| [Dinamizar](data-flow-pivot.md) | Modificador de esquemas | Uma agregação onde uma ou mais colunas de agrupamento têm os seus distintos valores de linha transformados em colunas individuais. |
| [Selecionar](data-flow-select.md) | Modificador de esquemas | Colunas de pseudónimos e nomes de streaming, e colunas drop ou reorder |
| [Sink](data-flow-sink.md) | - | Um destino final para os seus dados |
| [Ordenar](data-flow-sort.md) | Modificador de remo | Ordenar linhas de entrada no fluxo de dados atual |
| [Origem](data-flow-source.md) | - | Uma fonte de dados para o fluxo de dados |
| [Chave de substituição](data-flow-surrogate-key.md) | Modificador de esquemas | Adicione um valor-chave arbitrário não-empresarial incrementante |
| [União](data-flow-union.md) | Múltiplas entradas/saídas | Combine vários fluxos de dados verticalmente |
| [Anular dinamização](data-flow-unpivot.md) | Modificador de esquemas | Colunas de pivô nos valores da linha |
| [Janela](data-flow-window.md) | Modificador de esquemas |  Defina agregações baseadas em janelas de colunas nos seus fluxos de dados. |
