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
ms.date: 11/04/2019
ms.openlocfilehash: 4771db21a0ea5e841dbe12c8ce915b3833a8a30d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692319"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Aprendizado de máquina e ia com ONNX na visualização de borda do banco de dados SQL

O Machine Learning na visualização de borda do banco de dados SQL do Azure dá suporte a modelos no formato de [troca de rede neural aberto (ONNX)](https://onnx.ai/) . ONNX é um formato aberto que você pode usar para trocar modelos entre várias [estruturas e ferramentas de aprendizado de máquina](https://onnx.ai/supported-tools).

## <a name="supported-tool-kits"></a>Kits de ferramentas com suporte

O ONNXMLTools permite que você converta modelos de diferentes kits da ferramenta de aprendizado de máquina para um modelo ONNX. Atualmente, para tipos de dados numéricos e entradas de coluna única, há suporte para os seguintes kits de ferramenta:

* [scikit-learn](https://github.com/onnx/sklearn-onnx)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [Do coreml](https://github.com/onnx/onnxmltools)
* [ML do Spark (experimental)](https://github.com/onnx/onnxmltools/tree/master/onnxmltools/convert/sparkml)
* [LightGBM](https://github.com/onnx/onnxmltools)
* [libsvm](https://github.com/onnx/onnxmltools)
* [XGBoost](https://github.com/onnx/onnxmltools)

## <a name="get-onnx-models"></a>Obter modelos do ONNX

Há várias maneiras pelas quais você pode obter um modelo no formato ONNX:

- [Modelo de ONNX Zoo](https://github.com/onnx/models): contém vários modelos de ONNX pré-treinados para diferentes tipos de tarefas. Você pode baixar e usar versões com suporte no Windows Machine Learning.

- [Exportação nativa de estruturas de treinamento do Machine Learning](https://onnx.ai/supported-tools): várias estruturas de treinamento dão suporte à funcionalidade de exportação nativa para o ONNX, que permite que você salve seu modelo treinado em uma versão específica do formato ONNX. Por exemplo, Chainer, Caffee2 e PyTorch. Além disso, serviços como o [Azure Machine Learning Service](https://azure.microsoft.com/services/machine-learning-service/) e o [Azure visão personalizada](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) também fornecem a exportação ONNX nativa.

  - Para saber como treinar e exportar um modelo ONNX na nuvem usando Visão Personalizada, consulte [tutorial: usar um modelo ONNX de visão personalizada com o Windows ml (versão prévia)](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/custom-vision-onnx-windows-ml).

- [Converter modelos existentes usando WinMLTools](https://docs.microsoft.com/windows/ai/windows-ml/convert-model-winmltools): Este pacote Python permite que modelos sejam convertidos de vários formatos de estrutura de treinamento para ONNX. Você pode especificar para qual versão do ONNX você gostaria de converter seu modelo, dependendo de quais compilações do seu aplicativo se destina. Se você não estiver familiarizado com o Python, poderá usar o [painel baseado na interface do usuário do Windows Machine Learning](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLDashboard) para converter seus modelos.

> [!IMPORTANT]
> Nem todas as versões do ONNX são suportadas pela borda do banco de dados SQL do Azure. No momento, só há suporte para a previsão de tipos de dados numéricos por meio do modelo ONNX.

Depois de ter um modelo ONNX, você pode implantar o modelo na borda do banco de dados SQL do Azure. Em seguida, você pode usar [a pontuação nativa com a função de previsão do T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="limitations"></a>Limitações

Atualmente, esse suporte é limitado a modelos com **tipos de dados numéricos**:

- [int e bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [real e float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Outros tipos numéricos podem ser convertidos em tipos com suporte usando CAST e CONVERT [cast e Convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

As entradas do modelo devem ser estruturadas de forma que cada entrada para o modelo corresponda a uma única coluna em uma tabela. Por exemplo, se você estiver usando uma estrutura de dados pandas para treinar um modelo, cada entrada deverá ser uma coluna separada para o modelo.

## <a name="next-steps"></a>Passos seguintes

- [Implantar a borda do banco de dados SQL por meio do portal do Azure](deploy-portal.md)
- [Implantar um modelo ONNX na visualização de borda do banco de dados SQL do Azure](deploy-onnx.md)
