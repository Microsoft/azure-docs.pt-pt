---
title: 'Adicionar linhas: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo adicionar linhas no Azure Machine Learning para concatenar dois conjuntos de valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 39bbf3b3a38926d696233c2d77bae83ccfc85206
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314560"
---
# <a name="add-rows-module"></a>Adicionar módulo de linhas

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para concatenar dois conjuntos de valores. Na concatenação, as linhas do segundo conjunto de registros são adicionadas ao final do primeiro conjunto de um.  
  
A concatenação de linhas é útil em cenários como estes:  
  
+ Você gerou uma série de estatísticas de avaliação e deseja combiná-las em uma tabela para facilitar o relatório.  
  
+ Você tem trabalhado com conjuntos de os diferentes e deseja combinar os conjuntos de valores para criar um conjunto de um DataSet final.  

## <a name="how-to-use-add-rows"></a>Como usar adicionar linhas  

Para concatenar linhas de dois conjuntos de registros, as linhas devem ter exatamente o mesmo esquema. Isso significa, o mesmo número de colunas e o mesmo tipo de dados nas colunas.

1.  Arraste o módulo **adicionar linhas** para o pipeline, você pode encontrá-lo em **transformação de dados**, na categoria **manipular** .

2. Conecte os conjuntos de dados às duas portas de entrada. O conjunto de os que você deseja acrescentar deve estar conectado à segunda porta (à direita). 
  
3.  Executar o pipeline. O número de linhas no conjunto de dados de saída deve ser igual à soma das linhas de ambos os conjuntos de dados de entrada.

    Se você adicionar o mesmo conjunto de informações a ambas as entradas do módulo **adicionar linhas** , o conjunto de registros será duplicado. 

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 