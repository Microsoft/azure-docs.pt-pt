---
title: 'Aplicar transformação: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo aplicar transformação no serviço de Azure Machine Learning para modificar um conjunto de dados de entrada com base em uma transformação computada anteriormente.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 8b4ec9e71d9ce932a7b206467db91760f4a4fc47
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128972"
---
# <a name="apply-transformation-module"></a>Aplicar o módulo de transformação

Este artigo descreve um módulo da interface visual (visualização) para Azure Machine Learning serviço.

Use este módulo para modificar um conjunto de dados de entrada com base em uma transformação computada anteriormente.  
  
Por exemplo, se você usou pontuações z para normalizar seus dados de treinamento usando o módulo **normalizar dados** , convém usar o valor de pontuação z que foi calculado para treinamento durante a fase de Pontuação também. No Azure Machine Learning, você pode salvar o método de normalização como uma transformação e, em seguida, usar **aplicar transformação** para aplicar a pontuação z aos dados de entrada antes da pontuação.
  
Azure Machine Learning fornece suporte para criar e, em seguida, aplicar vários tipos diferentes de transformações personalizadas. Por exemplo, talvez você queira salvar e reutilizar transformações em:  
  
- Remover ou substituir valores ausentes, usando **limpar dados ausentes**
- Normalizar dados, usando **dados normalizados**
  

## <a name="how-to-use-apply-transformation"></a>Como usar aplicar transformação  
  
1. Adicione o módulo **aplicar transformação** ao seu experimento. Você pode encontrar esse módulo em **Machine Learning**, na categoria **Pontuação** . 
  
2. Localize uma transformação existente para usar como uma entrada.  As transformações salvas anteriormente podem ser encontradas no grupo transformações no painel de navegação esquerdo.  
  
   
  
3. Conecte o conjunto de um que você deseja transformar. O DataSet deve ter exatamente o mesmo esquema (número de colunas, nomes de coluna, tipos de dados) que o conjunto para o qual a transformação foi criada primeiro.  
  
4. Nenhum outro parâmetro precisa ser definido, pois toda a personalização é feita ao definir a transformação.  
  
5. Para aplicar uma transformação ao novo conjunto de um, execute o experimento.  

## <a name="next-steps"></a>Passos Seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 