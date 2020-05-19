---
title: Machine learning e IA com ONNX em Borda SQL Azure (Pré-visualização)
description: Machine learning in Azure SQL Edge (Preview) suporta modelos no formato Open Neural Network Exchange (ONNX). O ONNX é um formato aberto que pode utilizar para trocar modelos entre vários quadros e ferramentas de aprendizagem automática.
keywords: implementar SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 05/19/2020
ms.openlocfilehash: 12c1294a804c2063d405c5ec08440865283d51d3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597367"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-edge-preview"></a>Machine learning e IA com ONNX em SQL Edge (Pré-visualização)

Machine learning in Azure SQL Edge (Preview) suporta modelos no formato [Open Neural Network Exchange (ONNX).](https://onnx.ai/) O ONNX é um formato aberto que pode utilizar para trocar modelos entre [vários quadros e ferramentas de aprendizagem automática.](https://onnx.ai/supported-tools)

## <a name="overview"></a>Descrição geral

Para inferir modelos de machine learning no Azure SQL Edge, primeiro terá de obter um modelo. Este pode ser um modelo pré-treinado ou um modelo personalizado treinado com o seu quadro de eleição. O Azure SQL Edge suporta o formato ONNX e terá de converter o modelo para este formato. Não deve haver impacto na precisão do modelo, e uma vez que tenha o modelo ONNX, pode implantar o modelo em Azure SQL Edge e utilizar [a pontuação nativa com a função PREDICT T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Obtenha modelos ONNX

Para obter um modelo no formato ONNX:

- **Serviços de Construção**de Modelos : Serviços como a [funcionalidade automatizada de Machine Learning no Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) e [azure Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) suportam exportar diretamente o modelo treinado no formato ONNX.

- [**Converter e/ou exportar modelos existentes**](https://github.com/onnx/tutorials#converting-to-onnx-format): Vários quadros de formação (por [exemplo, PyTorch,](https://pytorch.org/docs/stable/onnx.html)Chainer e Caffe2) suportam a funcionalidade de exportação nativa para a ONNX, o que lhe permite guardar o seu modelo treinado para uma versão específica do formato ONNX. Para estruturas que não suportam a exportação nativa, existem pacotes instaladores onNX convertíveis autónomos que permitem converter modelos treinados a partir de diferentes estruturas de aprendizagem automática para o formato ONNX.

     **Estruturas suportadas**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    Para obter a lista completa de quadros e exemplos suportados, consulte [converter para o formato ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Limitações

Atualmente, nem todos os modelos ONNX são suportados pela Azure SQL Edge. O suporte limita-se a modelos com tipos de **dados numéricos:**

- [int e bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [real e flutuar.](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql)
  
Outros tipos numéricos podem ser convertidos para tipos suportados utilizando [O CAST e o CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

As inputs do modelo devem ser estruturadas de modo a que cada entrada do modelo corresponda a uma única coluna numa tabela. Por exemplo, se estiver a utilizar um quadro de dados pandas para treinar um modelo, cada entrada deve ser uma coluna separada do modelo.

## <a name="next-steps"></a>Passos seguintes

- [Implementar sQL Edge através do portal Azure](deploy-portal.md)
- [Implementar um modelo ONNX no Azure SQL Edge (Pré-visualização)](deploy-onnx.md)
