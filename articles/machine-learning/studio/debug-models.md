---
title: Depurar seu modelo
titleSuffix: ML Studio (classic) Azure
description: Como depurar erros produzidos pelos módulos modelo de treinamento e modelo de pontuação no Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 0ab7f041da63731706742547a53df47462bc584a
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73619445"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>Depurar seu modelo no Azure Machine Learning Studio (clássico)

Ao executar um modelo, você pode encontrar os seguintes erros:

* o módulo [modelo de treinamento][train-model] produz um erro 
* o módulo [modelo de Pontuação][score-model] produz resultados incorretos 

Este artigo explica as possíveis causas desses erros.


## <a name="train-model-module-produces-an-error"></a>O módulo modelo de treinamento produz um erro

![image1](./media/debug-models/train_model-1.png)

O módulo [modelo de treinamento][train-model] espera duas entradas:

1. O tipo de modelo de aprendizado de máquina da coleção de modelos fornecida pelo Azure Machine Learning Studio (clássico).
2. Os dados de treinamento com uma coluna de rótulo especificada que especifica a variável a prever (as outras colunas são consideradas recursos).

Esse módulo pode produzir um erro nos seguintes casos:

1. A coluna de rótulo está especificada incorretamente. Isso pode acontecer se mais de uma coluna for selecionada como o rótulo ou se um índice de coluna incorreto estiver selecionado. Por exemplo, o segundo caso se aplicaria se um índice de coluna 30 fosse usado com um conjunto de dados de entrada que tenha apenas 25 colunas.

2. O conjunto de recursos não contém nenhuma coluna de recurso. Por exemplo, se o conjunto de dados de entrada tiver apenas uma coluna, que é marcada como a coluna de rótulo, não haveria nenhum recurso com o qual criar o modelo. Nesse caso, o módulo [modelo de treinamento][train-model] produz um erro.

3. O conjunto de dados de entrada (recursos ou rótulo) contém infinito como um valor.

## <a name="score-model-module-produces-incorrect-results"></a>O módulo modelo de Pontuação produz resultados incorretos

![image2](./media/debug-models/train_test-2.png)

Em um experimento de treinamento/teste típico para aprendizado supervisionado, o módulo [dividir dados][split] divide o conjunto de dado original em duas partes: uma parte é usada para treinar o modelo e uma parte é usada para pontuar o desempenho do modelo treinado. O modelo treinado é usado para pontuar os dados de teste, após os quais os resultados são avaliados para determinar a precisão do modelo.

O módulo [modelo de Pontuação][score-model] requer duas entradas:

1. Uma saída de modelo treinado do módulo [treinar modelo][train-model] .
2. Um conjunto de uma pontuação que é diferente do conjunto de um usado para treinar o modelo.

É possível que, embora o experimento tenha sucesso, o módulo [modelo de Pontuação][score-model] produza resultados incorretos. Vários cenários podem causar a ocorrência desse problema:

1. Se o rótulo especificado for categórico e um modelo de regressão for treinado nos dados, uma saída incorreta será produzida pelo módulo [modelo de Pontuação][score-model] . Isso ocorre porque a regressão requer uma variável de resposta contínua. Nesse caso, seria mais adequado usar um modelo de classificação. 

2. Da mesma forma, se um modelo de classificação for treinado em um conjunto de um DataSet com números de ponto flutuantes na coluna de rótulo, ele poderá produzir resultados indesejáveis. Isso ocorre porque a classificação requer uma variável de resposta discreta que permite apenas valores que variam em um conjunto finito e pequeno de classes.

3. Se o conjunto de pontos de pontuação não contiver todos os recursos usados para treinar o modelo, o [modelo de Pontuação][score-model] produzirá um erro.

4. Se uma linha no conjunto de resultados de Pontuação contiver um valor ausente ou um valor infinito para qualquer um de seus recursos, o [modelo de Pontuação][score-model] não produzirá nenhuma saída correspondente a essa linha.

5. O [modelo de Pontuação][score-model] pode produzir saídas idênticas para todas as linhas no conjunto de registros de pontuação. Isso pode ocorrer, por exemplo, ao tentar a classificação usando florestas de decisão se o número mínimo de amostras por nó folha for escolhido como sendo mais do que o número de exemplos de treinamento disponíveis.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

