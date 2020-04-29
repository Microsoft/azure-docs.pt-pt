---
title: Depurar o seu modelo
titleSuffix: ML Studio (classic) - Azure
description: Como depurar erros produzidos por módulos Train Model e Score Model no Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 910e788830ec55b610a9234a8c8ac75dda1ea189
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79218092"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>Desmarque o seu modelo no Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Ao executar um modelo, pode encontrar os seguintes erros:

* o módulo [Modelo de Comboio][train-model] produz um erro 
* o módulo [Score Model][score-model] produz resultados incorretos 

Este artigo explica potenciais causas para estes erros.


## <a name="train-model-module-produces-an-error"></a>O Módulo modelo do comboio produz um erro

![image1](./media/debug-models/train_model-1.png)

O Módulo [modelo do comboio][train-model] espera duas inputs:

1. O tipo de modelo de machine learning da coleção de modelos fornecidos pelo Azure Machine Learning Studio (clássico).
2. Os dados de formação com uma coluna de etiqueta especificada que especifica a variável a prever (as outras colunas são assumidas como Características).

Este módulo pode produzir um erro nos seguintes casos:

1. A coluna etiquetada é especificada incorretamente. Isto pode acontecer se for selecionada mais de uma coluna à medida que o Rótulo ou um índice de coluna incorreto for selecionado. Por exemplo, o segundo caso aplicar-se-ia se um índice de coluna sacar 30 é utilizado com um conjunto de dados de entrada que tem apenas 25 colunas.

2. O conjunto de dados não contém quaisquer colunas de Recurso. Por exemplo, se o conjunto de dados de entrada tiver apenas uma coluna, que está marcada como a coluna Label, não existiria nenhuma característica para construir o modelo. Neste caso, o módulo [Modelo de Comboio][train-model] produz um erro.

3. O conjunto de dados de entrada (Funcionalidades ou Etiqueta) contém o Infinito como um valor.

## <a name="score-model-module-produces-incorrect-results"></a>O Módulo de Modelo de Pontuação produz resultados incorretos

![image2](./media/debug-models/train_test-2.png)

Numa experiência típica de treino/teste para aprendizagem supervisionada, o módulo [Split Data][split] divide o conjunto de dados original em duas partes: uma parte é usada para treinar o modelo e uma parte é usada para obter o desempenho do modelo treinado. O modelo treinado é então utilizado para marcar os dados do teste, após o qual os resultados são avaliados para determinar a precisão do modelo.

O módulo ['Modelo de Pontuação'][score-model] requer duas entradas:

1. Uma saída de modelo treinada a partir do módulo [Modelo de Comboio.][train-model]
2. Um conjunto de dados de pontuação diferente do conjunto de dados utilizado para treinar o modelo.

É possível que, mesmo que a experiência tenha sucesso, o módulo [Score Model][score-model] produz resultados incorretos. Vários cenários podem fazer com que esta questão aconteça:

1. Se o rótulo especificado for categórico e for treinado um modelo de regressão nos dados, uma saída incorreta seria produzida pelo módulo ['Modelo de Pontuação'.][score-model] Isto porque a regressão requer uma variável de resposta contínua. Neste caso, seria mais adequado utilizar um modelo de classificação. 

2. Da mesma forma, se um modelo de classificação for treinado num conjunto de dados com números de pontos flutuantes na coluna Label, pode produzir resultados indesejáveis. Isto porque a classificação requer uma variável de resposta discreta que só permite valores que variam sobre um conjunto finito, e pequeno, conjunto de classes.

3. Se o conjunto de dados de pontuação não contiver todas as funcionalidades utilizadas para treinar o modelo, o [Modelo de Pontuação][score-model] produz um erro.

4. Se uma linha no conjunto de dados de pontuação contiver um valor em falta ou um valor infinito para qualquer uma das suas funcionalidades, o [Modelo de Pontuação][score-model] não produz qualquer saída correspondente a essa linha.

5. O [Modelo de Pontuação][score-model] pode produzir saídas idênticas para todas as linhas no conjunto de dados de pontuação. Isto pode ocorrer, por exemplo, quando se tenta classificar utilizando as Florestas de Decisão se o número mínimo de amostras por nó de folha for escolhido para ser superior ao número de exemplos de formação disponíveis.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

