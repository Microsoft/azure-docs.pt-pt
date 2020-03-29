---
title: Mapeando fluxo de dados Transformação Unpivot
description: Fluxo de dados de mapeamento da fábrica de dados azure Data Factory Unpivot Transformation
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: b207012335e68d389a07b54408e840dbb305a30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930142"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Transformação unpivot da fábrica de dados azure



Utilize o Despivot no fluxo de dados de mapeamento ADF como forma de transformar um conjunto de dados não normalizado numa versão mais normalizada, expandindo valores de várias colunas num único registo em vários registos com os mesmos valores numa única coluna.

![Transformação Despivot](media/data-flow/unpivot1.png "Opções de despivot 1")

## <a name="ungroup-by"></a>Ungroup By

![Transformação Despivot](media/data-flow/unpivot5.png "Opções de despivot 2")

Primeiro, detete as colunas que deseja agrupar para a sua agregação de pivô. Detete uma ou mais colunas para desagrupar com o sinal + ao lado da lista de colunas.

## <a name="unpivot-key"></a>Chave despivot

![Transformação Despivot](media/data-flow/unpivot6.png "Opções de pivô 3")

A Chave Pivot é a coluna que a ADF irá girar de linha em coluna. Por predefinição, cada valor único no conjunto de dados para este campo girará para uma coluna. No entanto, pode introduzir opcionalmente os valores a partir do conjunto de dados que pretende orientar para valores de coluna.

## <a name="unpivoted-columns"></a>Colunas não-pivô

![Transformação Despivot](media/data-flow//unpivot7.png "Opções de sonsão 4")

Por último, escolha a agregação que deseja utilizar para os valores mais girados e como gostaria que as colunas fossem exibidas na nova projeção de saída da transformação.

(Opcional) Pode definir um padrão de nomeação com prefixo, meio e sufixo a ser adicionado a cada novo nome de coluna a partir dos valores da linha.

Por exemplo, a aposta em "Vendas" por "Região" simplesmente lhe daria novos valores de coluna de cada valor de venda. Por exemplo: "25", "50", "1000", ... No entanto, se definir um prefixo de valor de "Vendas", então "Vendas" será pré-fixado aos valores.

<img src="media/data-flow/unpivot3.png" width="400">

A definição do Arranjo da Coluna para "Normal" juntará todas as colunas pivotadas com os seus valores agregados. A definição do arranjo das colunas para "Lateral" alternará entre coluna e valor.

![Transformação Despivot](media/data-flow//unpivot7.png "Opções de sonsão 5")

O conjunto final de resultados dedados não orientado sem pivô mostra os totais da coluna agora desorientados em valores de linha separados.

## <a name="next-steps"></a>Passos seguintes

Utilize a [transformação pivot](data-flow-pivot.md) para girar linhas para colunas.
