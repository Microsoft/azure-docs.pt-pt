---
title: Depurar o seu modelo
titleSuffix: Azure Machine Learning Studio
description: Como depurar erros produzidos por módulos preparar modelo e Pontuar modelo no Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 5730a85a640759e61ad0e4aa2ae112a022900b27
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822109"
---
# <a name="debug-your-model-in-azure-machine-learning-studio"></a>Depurar o seu modelo no Azure Machine Learning Studio

Ao executar um modelo, poderão ocorrer os seguintes erros:

* o [Train Model] [ train-model] módulo gera um erro 
* o [modelo de pontuação] [ score-model] módulo produz resultados incorretos 

Este artigo explica as causas em potencial para estes erros.


## <a name="train-model-module-produces-an-error"></a>Módulo do modelo de formação produz um erro

![image1](./media/debug-models/train_model-1.png)

O [Train Model] [ train-model] módulo espera duas entradas:

1. O tipo de modelo de aprendizagem automática da coleção de modelos fornecidos pelo Azure Machine Learning Studio.
2. Os dados de treinamento com uma coluna de etiqueta especificada que especifica a variável para prever (as outras colunas devem para ser recursos).

Este módulo pode produzir um erro nos seguintes casos:

1. A coluna de etiqueta foi incorretamente especificada. Isto pode acontecer se a mais de uma coluna está selecionada como a etiqueta ou um índice de coluna incorretos é selecionado. Por exemplo, o segundo caso seria aplicada caso um índice de coluna de 30 é utilizado com um conjunto de dados de entrada que tem apenas 25 colunas.

2. O conjunto de dados não contém quaisquer colunas de funcionalidades. Por exemplo, se o conjunto de dados de entrada tem apenas uma coluna, o que é marcada como a coluna de etiqueta, não haveria nenhum recurso para a criação do modelo. Neste caso, o [Train Model] [ train-model] módulo gera um erro.

3. Do dataset de entrada (funcionalidades ou etiqueta) contém infinito como um valor.

## <a name="score-model-module-produces-incorrect-results"></a>Módulo do modelo de pontuação produz resultados incorretos

![image2](./media/debug-models/train_test-2.png)

Numa experimentação treinamento/teste típica para learning supervisionado, o [Split Data] [ split] módulo divide o conjunto de dados original em duas partes: parte de um é utilizada para preparar o modelo e uma parte é utilizada para pontuação como também realiza o modelo preparado. O modelo preparado, em seguida, é utilizado para classificar os dados de teste, após o qual os resultados são avaliados para determinar a precisão do modelo.

O [modelo de pontuação] [ score-model] módulo requer duas entradas:

1. Uma saída do modelo treinado a [Train Model] [ train-model] módulo.
2. Um conjunto de dados classificação diferente do conjunto de dados usado para treinar o modelo.

É possível que, embora a experimentação for bem-sucedida, o [modelo de pontuação] [ score-model] módulo produz resultados incorretos. Vários cenários podem fazer com que este problema acontecer:

1. Se a etiqueta especificada é categórica, e é preparado um modelo de regressão nos dados, uma saída incorreta poderia ser produzida pelos [modelo de pontuação] [ score-model] módulo. Isso é porque regressão requer uma variável de resposta contínuas. Neste caso, seria mais adequado utilizar um modelo de classificação. 

2. Da mesma forma, se é preparado um modelo de classificação num conjunto de dados ter números de pontos de vírgula flutuante na coluna de etiqueta, poderá produzir resultados indesejáveis. Isto acontece porque necessita de classificação de uma variável de resposta discreto que apenas permite valores nesse intervalo através de um conjunto finito e pequeno, de classes.

3. Se o conjunto de dados de classificação não contém todos os recursos usados para treinar o modelo, o [modelo de pontuação] [ score-model] produz um erro.

4. Se uma linha no conjunto de dados de classificação contiver um valor em falta ou um valor de infinito de qualquer um dos seus recursos, o [modelo de pontuação] [ score-model] não produz qualquer saída correspondente a essa linha.

5. O [modelo de pontuação] [ score-model] pode produzir saídas idênticas para todas as linhas no conjunto de dados de classificação. Isto pode ocorrer, por exemplo, quando tentar classificação com florestas de decisão, se o número mínimo de exemplos por nó de folha é escolhido para ser mais do que o número de exemplos de treinamento disponíveis.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

