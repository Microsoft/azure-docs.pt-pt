---
title: Mapeando fluxo de dados ordenar transformação
description: Fábrica de dados azure mapeia dados classificam a transformação
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/08/2018
ms.openlocfilehash: c09439c5f54ae4b0884e9e25ae9a5a488f935bac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930214"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Transformações de fluxo de dados da fábrica de dados azure



![Ordenar configurações](media/data-flow/sort.png "Ordenar")

A transformação do Sort permite-lhe classificar as linhas de entrada no fluxo de dados atual. As filas de saída da Transformação de Sort seguirão posteriormente as regras de encomenda que definiu. Pode escolher colunas individuais e selá-las ASC ou DEC, utilizando o indicador de seta ao lado de cada campo. Se precisar de modificar a coluna antes de aplicar o tipo, clique em "Colunas Computadas" para lançar o editor de expressão. Isto proporcionará uma oportunidade de construir uma expressão para a operação de tipo em vez de simplesmente aplicar uma coluna para o tipo.

## <a name="case-insensitive"></a>Não sensível a maiúsculas e minúsculas
Pode ligar "Caso insensível" se desejar ignorar o caso ao classificar os campos de cordas ou texto.

"Ordenar apenas dentro das divisórias" alavanca a divisão de dados da Faísca. Ao classificar os dados de entrada apenas em cada divisória, os Fluxos de Dados podem classificar dados divididos em vez de classificar todo o fluxo de dados.

Cada uma das condições de tipo na Transformação de Classificação pode ser reorganizada. Por isso, se precisar de mover uma coluna mais alto na precedência do tipo, agarre essa linha com o rato e mova-a mais ou menos na lista de classificação.

Efeitos de divisão no Sort

ADF Data Flow é executado em grandes dados Clusters Spark com dados distribuídos por vários nós e divisórias. É importante ter isso em mente quando se arquiteto o fluxo de dados se estiver dependente da transformação do Sort para manter os dados nessa mesma ordem. Se optar por repartipartição dos seus dados numa transformação subsequente, poderá perder a sua triagem devido a essa remodelação de dados.

## <a name="next-steps"></a>Passos seguintes

Após a triagem, pode querer usar a [Transformação Agregada](data-flow-aggregate.md)
