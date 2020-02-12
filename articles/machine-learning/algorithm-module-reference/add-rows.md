---
title: 'Adicionar linhas: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo adicionar linhas no Azure Machine Learning para concatenar dois conjuntos de valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 71f15d959bf9d42e67cd7c35ca91d6cd2caa718d
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152470"
---
# <a name="add-rows-module"></a>Adicionar módulo de linhas

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para concatenar dois conjuntos de valores. Na concatenação, as linhas do segundo conjunto de registros são adicionadas ao final do primeiro conjunto de um.  
  
A concatenação de linhas é útil em cenários como estes:  
  
+ Você gerou uma série de estatísticas de avaliação e deseja combiná-las em uma tabela para facilitar o relatório.  
  
+ Você tem trabalhado com conjuntos de os diferentes e deseja combinar os conjuntos de valores para criar um conjunto de um DataSet final.  

## <a name="how-to-use-add-rows"></a>Como usar adicionar linhas  

Para concatenar linhas de dois conjuntos de registros, as linhas devem ter exatamente o mesmo esquema. Isso significa, o mesmo número de colunas e o mesmo tipo de dados nas colunas.

1.  Arraste o módulo **Add Rows** para o seu pipeline, pode encontrá-lo na transformação de **dados,** na categoria **Manipula.**

2. Conecte os conjuntos de dados às duas portas de entrada. O conjunto de os que você deseja acrescentar deve estar conectado à segunda porta (à direita). 
  
3.  Executar o pipeline. O número de linhas no conjunto de dados de saída deve ser igual à soma das linhas de ambos os conjuntos de dados de entrada.

    Se adicionar o mesmo conjunto de dados a ambas as inputs do módulo **Add Rows,** o conjunto de dados é duplicado. 

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 