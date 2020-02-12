---
title: 'Aplicar transformação: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo aplicar transformação em Azure Machine Learning para modificar um conjunto de dados de entrada com base em uma transformação computada anteriormente.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 443b021b266a202775e94e44acac3a91a2b70617
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137587"
---
# <a name="apply-transformation-module"></a>Aplicar o módulo de transformação

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para modificar um conjunto de dados de entrada com base em uma transformação computada anteriormente.  
  
Por exemplo, se usasse z-scores para normalizar os seus dados de treino utilizando o módulo **Normalize Data,** também quereria utilizar o valor de pontuação z que foi calculado para treinar durante a fase de pontuação. No Azure Machine Learning, pode sossecar o método de normalização como uma transformação e, em seguida, usar a **Apply Transformation** para aplicar a pontuação z nos dados de entrada antes de marcar.
  
Azure Machine Learning fornece suporte para criar e, em seguida, aplicar vários tipos diferentes de transformações personalizadas. Por exemplo, talvez você queira salvar e reutilizar transformações em:  
  
- Remova ou substitua os valores em falta, utilizando **dados em falta limpos**
- Normalizar os dados, utilizando **dados normalizar**
  

## <a name="how-to-use-apply-transformation"></a>Como usar aplicar transformação  
  
1. Adicione o módulo **de Transformação aplicar** ao seu pipeline. Pode encontrar este módulo na categoria **Model Scoring & Evaluation.** 
  
2. Localize uma transformação existente para usar como uma entrada. Transformações previamente guardadas podem ser encontradas no grupo **My Datasets** na categoria **Datasets** na árvore do módulo esquerdo.  
  
   
  
3. Conecte o conjunto de um que você deseja transformar. O DataSet deve ter exatamente o mesmo esquema (número de colunas, nomes de coluna, tipos de dados) que o conjunto para o qual a transformação foi criada primeiro.  
  
4. Nenhum outro parâmetro precisa ser definido, pois toda a personalização é feita ao definir a transformação.  
  
5. Para aplicar uma transformação ao novo conjunto de um, execute o pipeline.  

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 