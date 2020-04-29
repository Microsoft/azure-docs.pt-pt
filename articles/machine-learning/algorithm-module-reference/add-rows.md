---
title: 'Adicionar Linhas: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Add Rows em Azure Machine Learning para concatenar dois conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd9b5f8f182c4deab746d2c41e516a6ac23fb7aa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477736"
---
# <a name="add-rows-module"></a>Adicionar módulo Rows

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para concatenar dois conjuntos de dados. Em concatenação, as linhas do segundo conjunto de dados são adicionadas ao final do primeiro conjunto de dados.  
  
A concatenação de linhas é útil em cenários como estes:  
  
+ Gerou uma série de estatísticas de avaliação, e quer combiná-las numa tabela para uma comunicação mais fácil.  
  
+ Tem trabalhado com diferentes conjuntos de dados e quer combinar os conjuntos de dados para criar um conjunto de dados final.  

## <a name="how-to-use-add-rows"></a>Como utilizar add rows  

Para concatenar linhas de dois conjuntos de dados, as linhas devem ter exatamente o mesmo esquema. Isto significa o mesmo número de colunas e o mesmo tipo de dados nas colunas.

1.  Arraste o módulo **Add Rows** para o seu pipeline, pode encontrá-lo sob **a transformação**de dados .

2. Ligue os conjuntos de dados às duas portas de entrada. O conjunto de dados que pretende anexar deve ser ligado à segunda porta (direita). 
  
3.  Submeta o oleoduto. O número de linhas no conjunto de dados de saída deve igualar a soma das linhas de ambos os conjuntos de dados de entrada.

    Se adicionar o mesmo conjunto de dados a ambas as inputs do módulo **Add Rows,** o conjunto de dados é duplicado. 

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 