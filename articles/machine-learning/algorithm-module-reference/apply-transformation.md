---
title: 'Aplicar Transformação: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de transformação aplicada em Aprendizagem automática Azure para modificar um conjunto de dados de entrada baseado numa transformação previamente calculada.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/05/2020
ms.openlocfilehash: ccf9d0c3eef50c7dfd838f1929e52506e8984879
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395254"
---
# <a name="apply-transformation-module"></a>Aplicar o módulo de transformação

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para modificar um conjunto de dados de entrada com base numa transformação previamente calculada.

Por exemplo, se usasse z-scores para normalizar os seus dados de treino utilizando o módulo **Normalize Data,** também quereria utilizar o valor de pontuação z que foi calculado para treinar durante a fase de pontuação. No Azure Machine Learning, pode sossecar o método de normalização como uma transformação e, em seguida, usar a **Apply Transformation** para aplicar a pontuação z nos dados de entrada antes de marcar.

## <a name="how-to-save-transformations"></a>Como salvar transformações

O designer permite-lhe guardar transformações de dados como **conjuntos** de dados para que possa usá-los em outros pipelines.

1. Selecione um módulo de transformação de dados que tenha executado com sucesso.

1. Selecione o separador **Saídas + logs.**

1. Selecione o **ícone Guardar** para salvar a **Transformação**do Resultado .

## <a name="how-to-use-apply-transformation"></a>Como usar a Transformação aplicar  
  
1. Adicione o módulo **de Transformação aplicar** ao seu pipeline. Pode encontrar este módulo na secção **Model Scoring & Evaluation** da paleta de módulos. 
  
1. Encontre a transformação guardada que pretende utilizar em **Conjuntos** de Dados > **My Datasets** na paleta de módulos.

1. Ligue a saída da transformação guardada à porta de entrada esquerda do módulo **de Transformação aplicável.**

    O conjunto de dados deve ter exatamente o mesmo esquema (número de colunas, nomes de colunas, tipos de dados) como o conjunto de dados para o qual a transformação foi desenhada pela primeira vez.  
  
1. Ligue a saída do conjunto de dados do módulo pretendido à porta de entrada certa do módulo **de Transformação aplicável.**
  
1. Para aplicar uma transformação ao novo conjunto de dados, executar o pipeline.  

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 