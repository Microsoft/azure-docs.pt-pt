---
title: Machine learning e IA com ONNX em Azure SQL Edge (Pré-visualização)
description: A aprendizagem automática em Azure SQL Edge (Preview) suporta modelos no formato Open Neural Network Exchange (ONNX). O ONNX é um formato aberto que pode utilizar para trocar modelos entre várias estruturas e ferramentas de machine learning.
keywords: implementar SQL Edge
services: sql-edge
ms.service: sql-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 05/19/2020
ms.openlocfilehash: 2db6c728ac35c6fc2f1fee1a602725371e448104
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85368061"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-edge-preview"></a>Machine learning e IA com ONNX em SQL Edge (Pré-visualização)

A aprendizagem automática em Azure SQL Edge (Preview) suporta modelos no formato [Open Neural Network Exchange (ONNX).](https://onnx.ai/) ONNX é um formato aberto que pode utilizar para trocar modelos entre [várias estruturas e ferramentas de machine learning.](https://onnx.ai/supported-tools)

## <a name="overview"></a>Descrição geral

Para inferir modelos de machine learning em Azure SQL Edge, primeiro terá de obter um modelo. Este pode ser um modelo pré-treinado ou um modelo personalizado treinado com a sua estrutura de eleição. O Azure SQL Edge suporta o formato ONNX e terá de converter o modelo neste formato. Não deve haver impacto na precisão do modelo, e uma vez que tenha o modelo ONNX, pode implantar o modelo em Azure SQL Edge e utilizar [a pontuação nativa com a função PREDICT T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Obtenha modelos ONNX

Para obter um modelo no formato ONNX:

- **Model Building Services**: Serviços como a [funcionalidade automatizada de Machine Learning em Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) e suporte [Azure Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) exportando diretamente o modelo treinado no formato ONNX.

- [**Converter e/ou exportar modelos existentes**](https://github.com/onnx/tutorials#converting-to-onnx-format): Vários quadros de formação (por exemplo, [PyTorch,](https://pytorch.org/docs/stable/onnx.html)Chainer e Caffe2) suportam a funcionalidade de exportação nativa para o ONNX, o que lhe permite guardar o seu modelo treinado para uma versão específica do formato ONNX. Para estruturas que não suportam a exportação nativa, existem pacotes instalados onnx conversíveis autónomos que permitem converter modelos treinados de diferentes estruturas de aprendizagem automática para o formato ONNX.

     **Estruturas suportadas**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorfluor](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    Para obter a lista completa de quadros e exemplos suportados, consulte [converter para formato ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Limitações

Atualmente, nem todos os modelos ONNX são suportados pela Azure SQL Edge. O suporte é limitado a modelos com **tipos de dados numéricos:**

- [int e bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [real e flutuante](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Outros tipos numéricos podem ser convertidos em tipos suportados utilizando [CAST e CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

As entradas do modelo devem ser estruturadas de modo a que cada entrada do modelo corresponda a uma única coluna numa tabela. Por exemplo, se estiver a utilizar um dataframe de pandas para treinar um modelo, então cada entrada deve ser uma coluna separada do modelo.

## <a name="next-steps"></a>Próximos passos

- [Implementar sql edge através do portal Azure](deploy-portal.md)
- [Implementar um modelo ONNX no Azure SQL Edge (Pré-visualização)](deploy-onnx.md)
