---
title: Transformação não dinâmica do fluxo de dados de mapeamento de Azure Data Factory
description: Transformação não dinâmica do fluxo de dados de mapeamento de Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 28fedf96ec178aae0615129421bdae7721a66105
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029943"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Azure Data Factory transformação não dinâmica



Use o fluxo de dados de mapeamento do AAD no ADF como uma maneira de transformar um conjunto de dado não normalizado em uma versão mais normalizada expandindo valores de várias colunas em um único registro em vários registros com os mesmos valores em uma única coluna.

![](media/data-flow/unpivot1.png "Opções não dinâmicas") de transformação não dinâmica 1

## <a name="ungroup-by"></a>Desagrupar por

![](media/data-flow/unpivot5.png "Opções não dinâmicas") de transformação não dinâmica 2

Primeiro, defina as colunas que você deseja agrupar para a agregação dinâmica. Defina uma ou mais colunas para desagrupar com o sinal de + ao lado da lista de colunas.

## <a name="unpivot-key"></a>Chave não dinâmica

![](media/data-flow/unpivot6.png "Opções não dinâmicas") de transformação não dinâmica 3

A chave dinâmica é a coluna que o ADF dinamizará da linha para a coluna. Por padrão, cada valor exclusivo no conjunto de valores para esse campo será dinamizado para uma coluna. No entanto, opcionalmente, você pode inserir os valores do conjunto de um que você deseja dinamizar para valores de coluna.

## <a name="unpivoted-columns"></a>Colunas não dinâmicas

![](media/data-flow//unpivot7.png "Opções não dinâmicas") de transformação não dinâmica 4

Por fim, escolha a agregação que você deseja usar para os valores dinâmicos e como deseja que as colunas sejam exibidas na nova projeção de saída da transformação.

Adicional Você pode definir um padrão de nomenclatura com um prefixo, meio e sufixo a ser adicionado a cada novo nome de coluna dos valores de linha.

Por exemplo, dinamizar "vendas" por "Region" simplesmente lhe dará novos valores de coluna de cada valor de vendas. Por exemplo: "25", "50", "1000",... No entanto, se você definir um valor de prefixo de "vendas", então "vendas" será prefixado para os valores.

<img src="media/data-flow/unpivot3.png" width="400">

Definir a organização da coluna como "normal" agrupará todas as colunas dinâmicas com seus valores agregados. A definição da organização das colunas como "lateral" será alternada entre a coluna e o valor.

![](media/data-flow//unpivot7.png "Opções não dinâmicas") de transformação não dinâmica 5

O conjunto de resultados de dados não dinâmicos final mostra os totais de coluna agora não dinâmicos em valores de linha separados.

## <a name="next-steps"></a>Passos seguintes

Use a [transformação dinâmica](data-flow-pivot.md) para dinamizar linhas para colunas.
