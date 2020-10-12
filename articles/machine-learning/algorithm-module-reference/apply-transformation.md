---
title: 'Aplicar transformação: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de Transformação De Aplicar em Azure Machine Learning para modificar um conjunto de dados de entrada baseado numa transformação previamente calculada.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/05/2020
ms.openlocfilehash: 7573abbbee479bfb0d1710beba3b95d084a5e657
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898873"
---
# <a name="apply-transformation-module"></a>Aplicar módulo de transformação

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para modificar um conjunto de dados de entrada com base numa transformação previamente calculada.

Por exemplo, se usasse as pontuações z para normalizar os seus dados de treino utilizando o módulo **de Dados Normalizados,** também quereria utilizar o valor de z-score que foi calculado para treinar durante a fase de pontuação. No Azure Machine Learning, pode guardar o método de normalização como uma transformação e, em seguida, usar **a Apply Transformation** para aplicar a pontuação z nos dados de entrada antes de marcar.

## <a name="how-to-save-transformations"></a>Como salvar transformações

O designer permite-lhe guardar as transformações de dados como **conjuntos de dados** para que possa usá-los em outros oleodutos.

1. Selecione um módulo de transformação de dados que tenha sido executado com sucesso.

1. Selecione o **separador Saídas + registos.**

1. Encontre a saída de transformação e selecione o **conjunto de dados do Registo** para o guardar como um módulo na categoria Datasets na paleta de **módulos.**

## <a name="how-to-use-apply-transformation"></a>Como usar a Transformação De Aplicar  
  
1. Adicione o módulo **de Transformação De Aplicar** ao seu oleoduto. Pode encontrar este módulo na secção **de avaliação & de marcação** do modelo da paleta de módulos. 
  
1. Encontre a transformação guardada que pretende utilizar nos **Conjuntos de Dados** na paleta do módulo.

1. Ligue a saída da transformação guardada à porta de entrada esquerda do módulo **De Transformação Aplicada.**

    O conjunto de dados deve ter exatamente o mesmo esquema (número de colunas, nomes de colunas, tipos de dados) que o conjunto de dados para o qual a transformação foi concebida pela primeira vez.  
  
1. Ligue a saída do conjunto de dados do módulo pretendido à porta de entrada certa do módulo **De Transformação De Aplicar.**
  
1. Para aplicar uma transformação no novo conjunto de dados, executar o oleoduto.  

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 