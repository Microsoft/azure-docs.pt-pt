---
title: 'Adicionar Linhas: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Add Rows no designer Azure Machine Learning para concatenar dois conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 2c2a74ee88e7161ab8dd1701ef4d105df67c0938
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421945"
---
# <a name="add-rows-module"></a>Adicionar módulo Rows

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para concatenar dois conjuntos de dados. Na concatenação, as linhas do segundo conjunto de dados são adicionadas ao final do primeiro conjunto de dados.  
  
A concatenação de linhas é útil em cenários como estes:  
  
+ Gerou uma série de estatísticas de avaliação, e quer combiná-las numa só tabela para uma comunicação mais fácil.  
  
+ Tem trabalhado com diferentes conjuntos de dados e pretende combinar os conjuntos de dados para criar um conjunto de dados final.  

## <a name="how-to-use-add-rows"></a>Como utilizar Add Rows  

Para concatenar linhas de dois conjuntos de dados, as linhas devem ter exatamente o mesmo esquema. Isto significa o mesmo número de colunas, e o mesmo tipo de dados nas colunas.

1.  Arraste o módulo **Add Rows** para o seu oleoduto, pode encontrá-lo sob **a Transformação de Dados.**

2. Ligue os conjuntos de dados às duas portas de entrada. O conjunto de dados que pretende anexar deve ser ligado à segunda porta (à direita). 
  
3.  Envie o oleoduto. O número de linhas no conjunto de dados de saída deve ser igual à soma das linhas de ambos os conjuntos de dados de entrada.

    Se adicionar o mesmo conjunto de dados a ambas as entradas do módulo **Add Rows,** o conjunto de dados é duplicado. 

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 