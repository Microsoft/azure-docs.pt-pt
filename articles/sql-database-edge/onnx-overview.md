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
ms.openlocfilehash: 744ac9f8526b7d65709d3627a5f90b31d234b2cd
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74009086"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Aprendizado de máquina e ia com ONNX na visualização de borda do banco de dados SQL

O Machine Learning na visualização de borda do banco de dados SQL do Azure dá suporte a modelos no formato de [troca de rede neural aberto (ONNX)](https://onnx.ai/) . ONNX é um formato aberto que você pode usar para trocar modelos entre várias [estruturas e ferramentas de aprendizado de máquina](https://onnx.ai/supported-tools).

## <a name="overview"></a>Descrição geral

Para inferir modelos de aprendizado de máquina na borda do banco de dados SQL do Azure, primeiro você precisará obter um modelo. Isso pode ser um modelo pretreinado ou um modelo personalizado treinado com sua estrutura de escolha. A borda do banco de dados SQL do Azure dá suporte ao formato ONNX e você precisará converter o modelo para esse formato. Não deve haver nenhum impacto na precisão do modelo e, depois de ter o modelo ONNX, você pode implantar o modelo na borda do banco de dados SQL do Azure e usar [a pontuação nativa com a função prever T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Obter modelos ONNX

Para obter um modelo no formato ONNX:

- **Serviços de criação de modelo**: serviços como o [recurso de Machine Learning automatizado no Azure Machine Learning e no](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) [Azure serviço de visão personalizada](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) dão suporte à exportação direta do modelo treinado no formato ONNX.

- [**Converter e/ou exportar modelos existentes**](https://github.com/onnx/tutorials#converting-to-onnx-format): várias estruturas de treinamento (por exemplo, [PyTorch](https://pytorch.org/docs/stable/onnx.html), chainer e Caffe2) dão suporte à funcionalidade de exportação nativa para o ONNX, que permite que você salve seu modelo treinado em uma versão específica do formato ONNX. Para estruturas que não dão suporte à exportação nativa, há pacotes instaláveis do conversor de ONNX autônomo que permitem converter modelos treinados de diferentes estruturas de Machine Learning para o formato ONNX.

     **Estruturas com suporte**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [Do coreml](https://github.com/onnx/onnxmltools)
    
    Para obter a lista completa de estruturas e exemplos com suporte, consulte [convertendo para o formato ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Limitações

Atualmente, nem todos os modelos ONNX têm suporte da borda do banco de dados SQL do Azure. O suporte é limitado a modelos com **tipos de dados numéricos**:

- [int e bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [real e float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Outros tipos numéricos podem ser convertidos em tipos com suporte usando [cast e Convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

As entradas do modelo devem ser estruturadas de forma que cada entrada para o modelo corresponda a uma única coluna em uma tabela. Por exemplo, se você estiver usando uma estrutura de dados pandas para treinar um modelo, cada entrada deverá ser uma coluna separada para o modelo.

## <a name="next-steps"></a>Passos seguintes

- [Implantar a borda do banco de dados SQL por meio do portal do Azure](deploy-portal.md)
- [Implantar um modelo ONNX na visualização de borda do banco de dados SQL do Azure](deploy-onnx.md)
