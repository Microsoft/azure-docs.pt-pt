---
title: Aprendizado de máquina e ia com ONNX na visualização de borda do banco de dados SQL do Azure | Microsoft Docs
description: O Machine Learning na visualização de borda do banco de dados SQL do Azure dá suporte a modelos no formato de troca de rede neural aberto (ONNX). ONNX é um formato aberto que você pode usar para trocar modelos entre várias estruturas e ferramentas de aprendizado de máquina.
keywords: implantar borda do banco de dados SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/07/2019
ms.openlocfilehash: 976c849f9cb48e1c197f70d10e911216a6a7425c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822837"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Aprendizado de máquina e ia com ONNX na visualização de borda do banco de dados SQL

O Machine Learning na visualização de borda do banco de dados SQL do Azure dá suporte a modelos no formato de [troca de rede neural aberto (ONNX)](https://onnx.ai/) . ONNX é um formato aberto que você pode usar para trocar modelos entre várias [estruturas e ferramentas de aprendizado de máquina](https://onnx.ai/supported-tools).

## <a name="overview"></a>Descrição geral

Para inferir modelos de aprendizado de máquina na borda do banco de dados SQL do Azure, primeiro você precisará obter um modelo. Isso pode ser um modelo pretreinado ou um modelo personalizado treinado com sua estrutura de escolha. A borda do banco de dados SQL do Azure dá suporte ao formato ONNX e você precisará converter o modelo para esse formato. Não deve haver nenhum impacto na precisão do modelo e, depois de ter o modelo ONNX, você pode implantar o modelo na borda do banco de dados SQL do Azure e usar [a pontuação nativa com a função prever T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Obter modelos do ONNX

Há várias maneiras pelas quais você pode obter um modelo no formato ONNX:

- [Modelo de ONNX Zoo](https://github.com/onnx/models): contém muitos modelos de ONNX pré-treinados para diferentes tipos de tarefas que podem ser baixadas e estão prontas para uso.

- [Exportação nativa das estruturas de treinamento do ml](https://onnx.ai/supported-tools): várias estruturas de treinamento dão suporte à funcionalidade de exportação nativa para o ONNX, que permite que você salve seu modelo treinado em uma versão específica do formato ONNX, incluindo [PyTorch](https://pytorch.org/docs/stable/onnx.html), chainer e Caffe2. Além disso, os serviços de criação de modelos, como o [recurso de Machine Learning automatizado no Azure Machine Learning e no](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) [Azure serviço de visão personalizada](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) fornecem a exportação de ONNX.

- [Converter modelos existentes](https://github.com/onnx/tutorials#converting-to-onnx-format): para estruturas que não dão suporte à exportação nativa, há pacotes autônomos para converter modelos no formato ONNX. Para obter exemplos e tutoriais, consulte [convertendo para o formato ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format). 

### <a name="supported-frameworks"></a>Estruturas suportadas

Os conversores ONNX permitem que você converta modelos treinados de diferentes estruturas de Machine Learning para o formato ONNX. Os conversores populares incluem: 

* [PyTorch](http://pytorch.org/docs/master/onnx.html)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [Scikit-learn](https://github.com/onnx/sklearn-onnx)
* [Do coreml](https://github.com/onnx/onnxmltools)

Para obter a lista completa de estruturas com suporte, consulte [convertendo para o formato ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Limitações

Atualmente, nem todos os modelos ONNX têm suporte da borda do banco de dados SQL do Azure. O suporte é limitado a modelos com **tipos de dados numéricos**:

- [int e bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [real e float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Outros tipos numéricos podem ser convertidos em tipos com suporte usando [cast e Convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

As entradas do modelo devem ser estruturadas de forma que cada entrada para o modelo corresponda a uma única coluna em uma tabela. Por exemplo, se você estiver usando uma estrutura de dados pandas para treinar um modelo, cada entrada deverá ser uma coluna separada para o modelo.

## <a name="next-steps"></a>Passos seguintes

- [Implantar a borda do banco de dados SQL por meio do portal do Azure](deploy-portal.md)
- [Implantar um modelo ONNX na visualização de borda do banco de dados SQL do Azure](deploy-onnx.md)
