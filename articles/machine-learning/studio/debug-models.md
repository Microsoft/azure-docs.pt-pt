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
ms.openlocfilehash: 6c672e57c0c1c0b62b79afbacec3f842770e3d61
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169233"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>Desmarque o seu modelo no Azure Machine Learning Studio (clássico)

Ao executar um modelo, pode encontrar os seguintes erros:

* o módulo [Modelo de Comboio][train-model] produz um erro 
* o módulo [Score Model][score-model] produz resultados incorretos 

Este artigo explica potenciais causas para estes erros.


## <a name="train-model-module-produces-an-error"></a>Módulo do modelo de formação produz um erro

![image1](./media/debug-models/train_model-1.png)

O Módulo [modelo do comboio][train-model] espera duas inputs:

1. O tipo de modelo de machine learning da coleção de modelos fornecidos pelo Azure Machine Learning Studio (clássico).
2. Os dados de formação com uma coluna de etiqueta especificada que especifica a variável a prever (as outras colunas são assumidas como Características).

Este módulo pode produzir um erro nos seguintes casos:

1. A coluna de etiqueta foi incorretamente especificada. Isto pode acontecer se a mais de uma coluna está selecionada como a etiqueta ou um índice de coluna incorretos é selecionado. Por exemplo, o segundo caso aplicar-se-ia se um índice de coluna sacar 30 é utilizado com um conjunto de dados de entrada que tem apenas 25 colunas.

2. O conjunto de dados não contém quaisquer colunas de funcionalidades. Por exemplo, se o conjunto de dados de entrada tem apenas uma coluna, o que é marcada como a coluna de etiqueta, não haveria nenhum recurso para a criação do modelo. Neste caso, o módulo [Modelo de Comboio][train-model] produz um erro.

3. Do dataset de entrada (funcionalidades ou etiqueta) contém infinito como um valor.

## <a name="score-model-module-produces-incorrect-results"></a>Módulo do modelo de pontuação produz resultados incorretos

![image2](./media/debug-models/train_test-2.png)

Numa experiência típica de treino/teste para aprendizagem supervisionada, o módulo [Split Data][split] divide o conjunto de dados original em duas partes: uma parte é usada para treinar o modelo e uma parte é usada para obter o desempenho do modelo treinado. O modelo preparado, em seguida, é utilizado para classificar os dados de teste, após o qual os resultados são avaliados para determinar a precisão do modelo.

O módulo ['Modelo de Pontuação'][score-model] requer duas entradas:

1. Uma saída de modelo treinada a partir do módulo [Modelo de Comboio.][train-model]
2. Um conjunto de dados classificação diferente do conjunto de dados usado para treinar o modelo.

É possível que, mesmo que a experiência tenha sucesso, o módulo [Score Model][score-model] produz resultados incorretos. Vários cenários podem fazer com que esta questão aconteça:

1. Se o rótulo especificado for categórico e for treinado um modelo de regressão nos dados, uma saída incorreta seria produzida pelo módulo ['Modelo de Pontuação'.][score-model] Isso é porque regressão requer uma variável de resposta contínuas. Neste caso, seria mais adequado utilizar um modelo de classificação. 

2. Da mesma forma, se é preparado um modelo de classificação num conjunto de dados ter números de pontos de vírgula flutuante na coluna de etiqueta, poderá produzir resultados indesejáveis. Isto porque a classificação requer uma variável de resposta discreta que só permite valores que variam sobre um conjunto finito, e pequeno, conjunto de classes.

3. Se o conjunto de dados de pontuação não contiver todas as funcionalidades utilizadas para treinar o modelo, o [Modelo de Pontuação][score-model] produz um erro.

4. Se uma linha no conjunto de dados de pontuação contiver um valor em falta ou um valor infinito para qualquer uma das suas funcionalidades, o [Modelo de Pontuação][score-model] não produz qualquer saída correspondente a essa linha.

5. O [Modelo de Pontuação][score-model] pode produzir saídas idênticas para todas as linhas no conjunto de dados de pontuação. Isto pode ocorrer, por exemplo, quando tentar classificação com florestas de decisão, se o número mínimo de exemplos por nó de folha é escolhido para ser mais do que o número de exemplos de treinamento disponíveis.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

