---
title: 'Adicionar colunas: referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo adicionar colunas no serviço de Azure Machine Learning para concatenar dois conjuntos de valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: cbb1249b529fde1368bfaff0c6c251c93fa6c309
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693865"
---
# <a name="add-columns-module"></a>Adicionar módulo de colunas

Este artigo descreve um módulo da interface visual (visualização) para Azure Machine Learning serviço.

Use este módulo para concatenar dois conjuntos de valores. Você combina todas as colunas dos dois conjuntos de valores que você especifica como entradas para criar um único conjunto de informações. Se você precisar concatenar mais de dois conjuntos de os, use várias instâncias de **adicionar colunas**.



## <a name="how-to-configure-add-columns"></a>Como configurar adicionar colunas
1. Adicione o módulo **adicionar colunas** ao seu pipeline.

2. Conecte os dois conjuntos de os que você deseja concatenar. Se você quiser combinar mais de dois conjuntos de os, poderá encadear várias combinações de **adicionar colunas**.

    - É possível combinar duas colunas que têm um número diferente de linhas. O conjunto de resultados de saída é preenchido com valores ausentes para cada linha na coluna de origem menor.

    - Você não pode escolher colunas individuais para adicionar. Todas as colunas de cada conjunto de linhas são concatenadas quando você usa **adicionar colunas**. Portanto, se você quiser adicionar apenas um subconjunto das colunas, use selecionar colunas no conjunto de conjuntos para criar um conjunto de um DataSet com as colunas desejadas.

3. Executar o pipeline.

### <a name="results"></a>Resultados
Após a execução do pipeline:

- Para ver as primeiras linhas do novo conjunto de registros, clique com o botão direito do mouse na saída de **adicionar colunas** e selecione Visualizar.

O número de colunas no novo DataSet é igual à soma das colunas de ambos os conjuntos de dados de entrada.

Se houver duas colunas com o mesmo nome nos conjuntos de dados de entrada, um sufixo numérico será adicionado ao nome da coluna. Por exemplo, se houver duas instâncias de uma coluna denominadas TargetOutcome, a coluna à esquerda será renomeada como TargetOutcome_1 e a coluna direita será renomeada como TargetOutcome_2.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 