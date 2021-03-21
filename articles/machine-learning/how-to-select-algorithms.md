---
title: Como selecionar um algoritmo de aprendizagem automática
titleSuffix: Azure Machine Learning
description: Como selecionar algoritmos de aprendizagem automática Azure para aprendizagem supervisionada e sem supervisão em experiências de agrupamento, classificação ou regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 05/07/2020
ms.openlocfilehash: 27b18fdc2dda40f8361483e6ecce28d0ccbd0310
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93308238"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Como selecionar algoritmos para Azure Machine Learning

Uma pergunta comum é" Que algoritmo de aprendizagem automática devo usar?" O algoritmo que seleciona depende principalmente de dois aspetos diferentes do seu cenário de ciência de dados:

 - **O que quer fazer com os seus dados?** Especificamente, qual é a questão do negócio que quer responder aprendendo com os seus dados anteriores?

 - **Quais são os requisitos do seu cenário de ciência de dados?** Especificamente, qual é a precisão, tempo de treino, linearidade, número de parâmetros e número de funcionalidades que a sua solução suporta?

 ![Considerações para escolher algoritmos: O que quer saber? Quais são os requisitos do cenário?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>Cenários de negócio e a folha de batota do algoritmo de aprendizagem automática

A Folha de Batota do [Algoritmo de Aprendizagem de Máquinas Azure](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri) ajuda-o com a primeira consideração: **O que pretende fazer com os seus dados?** Na Folha de Batota do Algoritmo de Aprendizagem automática, procure a tarefa que deseja fazer e, em seguida, encontre um algoritmo [de designer de machine learning Azure](./concept-designer.md?WT.mc_id=docs-article-lazzeri) para a solução de análise preditiva. 

O designer de Machine Learning fornece um portfólio abrangente de algoritmos, tais como Floresta de [Decisão Multiclasse,](./algorithm-module-reference/multiclass-decision-forest.md?WT.mc_id=docs-article-lazzeri) [Sistemas de Recomendação,](./algorithm-module-reference/evaluate-recommender.md?WT.mc_id=docs-article-lazzeri) [Regressão de Rede Neural,](./algorithm-module-reference/neural-network-regression.md?WT.mc_id=docs-article-lazzeri) [Rede Neural Multiclass,](./algorithm-module-reference/multiclass-neural-network.md?WT.mc_id=docs-article-lazzeri)e [Clustering K-Means.](./algorithm-module-reference/k-means-clustering.md?WT.mc_id=docs-article-lazzeri) Cada algoritmo é projetado para resolver um tipo diferente de problema de aprendizagem automática. Consulte o [algoritmo e o módulo do designer machine learning](./algorithm-module-reference/module-reference.md?WT.mc_id=docs-article-lazzeri) para uma lista completa juntamente com documentação sobre como cada algoritmo funciona e como sintonizar parâmetros para otimizar o algoritmo.

> [!NOTE]
> Para descarregar a folha de batota do algoritmo de aprendizagem automática, vá ao [Azure Machine learning algorithm cheat sheet](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri).
> 
> 

Juntamente com a orientação na Folha de Batota do Algoritmo de Aprendizagem de Máquinas Azure, tenha em mente outros requisitos ao escolher um algoritmo de aprendizagem automática para a sua solução. Seguem-se fatores adicionais a ter em conta, tais como a precisão, o tempo de treino, a linearidade, o número de parâmetros e o número de características.

## <a name="comparison-of-machine-learning-algorithms"></a>Comparação de algoritmos de aprendizagem automática

Alguns algoritmos de aprendizagem fazem suposições particulares sobre a estrutura dos dados ou os resultados desejados. Se encontrar um que se adapte às suas necessidades, pode dar-lhe resultados mais úteis, previsões mais precisas ou tempos de treino mais rápidos.

A tabela a seguir resume algumas das características mais importantes dos algoritmos das famílias de classificação, regressão e agrupamento:

| **Algoritmo** | **Precisão** | **Tempo de preparação** | **Linearidade** | **Parâmetros** | **Notas** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Família classificação** | | | | | |
| [Regressão logística de duas classes](./algorithm-module-reference/two-class-logistic-regression.md?WT.mc_id=docs-article-lazzeri) |Bom  |Rápido |Yes |4 | |
| [Floresta de decisão de duas classes](./algorithm-module-reference/two-class-decision-forest.md?WT.mc_id=docs-article-lazzeri) |Excelente |Moderado |No |5 |Mostra tempos de pontuação mais lentos. Sugiro não trabalhar com uma multiclasse One-vs-All, devido aos tempos de pontuação mais lentos causados pelo bloqueio de pisos nas previsões de árvores acumuladas |
| [Árvore de decisão reforçada de duas classes](./algorithm-module-reference/two-class-boosted-decision-tree.md?WT.mc_id=docs-article-lazzeri) |Excelente |Moderado |No |6 |Grande pegada de memória |
| [Rede neural de duas classes](./algorithm-module-reference/two-class-neural-network.md?WT.mc_id=docs-article-lazzeri) |Bom |Moderado |No |8 | |
| [Perceptron médio de duas classes](./algorithm-module-reference/two-class-averaged-perceptron.md?WT.mc_id=docs-article-lazzeri) |Bom |Moderado |Yes |4 | |
| [Máquina vetorial de apoio de duas classes](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri) |Bom |Rápido |Yes |5 |Bom para grandes conjuntos de características |
| [Regressão logística multiclasse](./algorithm-module-reference/multiclass-logistic-regression.md?WT.mc_id=docs-article-lazzeri) |Bom |Rápido |Yes |4 | |
| [Floresta de decisão multiclasse](./algorithm-module-reference/multiclass-decision-forest.md?WT.mc_id=docs-article-lazzeri) |Excelente |Moderado |No |5 |Mostra tempos de pontuação mais lentos |
| [Árvore de decisão reforçada em multiclasse](./algorithm-module-reference/multiclass-boosted-decision-tree.md?WT.mc_id=docs-article-lazzeri) |Excelente |Moderado |No |6 | Tende a melhorar a precisão com algum pequeno risco de menor cobertura |
| [Rede neural multiclasse](./algorithm-module-reference/multiclass-neural-network.md?WT.mc_id=docs-article-lazzeri) |Bom |Moderado |No |8 | |
| [Uma-vs-todas multiclasses](./algorithm-module-reference/one-vs-all-multiclass.md?WT.mc_id=docs-article-lazzeri) | - | - | - | - |Ver propriedades do método de duas classes selecionado |
| **Família de regressão** | | | | | |
| [Regressão linear](./algorithm-module-reference/linear-regression.md?WT.mc_id=docs-article-lazzeri) |Bom |Rápido |Yes |4 | |
| [Regressão florestal de decisão](./algorithm-module-reference/decision-forest-regression.md?WT.mc_id=docs-article-lazzeri)|Excelente |Moderado |No |5 | |
| [Regressão da árvore de decisão reforçada](./algorithm-module-reference/boosted-decision-tree-regression.md?WT.mc_id=docs-article-lazzeri) |Excelente |Moderado |No |6 |Grande pegada de memória |
| [Regressão da rede neural](./algorithm-module-reference/neural-network-regression.md?WT.mc_id=docs-article-lazzeri) |Bom |Moderado |No |8 | |
| **Família de agrupamento** | | | | | |
| [K-significa agrupamento](./algorithm-module-reference/k-means-clustering.md?WT.mc_id=docs-article-lazzeri) |Excelente |Moderado |Yes |8 |Um algoritmo de agrupamento |

## <a name="requirements-for-a-data-science-scenario"></a>Requisitos para um cenário de ciência de dados

Uma vez que saiba o que pretende fazer com os seus dados, precisa determinar requisitos adicionais para a sua solução. 

Fazer escolhas e, eventualmente, compensações para os seguintes requisitos:

- Precisão
- Tempo de preparação
- Linearidade
- Número de parâmetros
- Número de funcionalidades

## <a name="accuracy"></a>Precisão

A precisão na aprendizagem automática mede a eficácia de um modelo como a proporção de resultados verdadeiros para os casos totais. No designer de Machine Learning, o [módulo De Avaliar](./algorithm-module-reference/evaluate-model.md?WT.mc_id=docs-article-lazzeri) calcula um conjunto de métricas de avaliação padrão da indústria. Pode utilizar este módulo para medir a precisão de um modelo treinado.

Obter a resposta mais precisa possível nem sempre é necessário. Às vezes, uma aproximação é adequada, dependendo do que pretende usá-la. Se for esse o caso, poderá ser capaz de reduzir drasticamente o seu tempo de processamento mantendo-se com métodos mais aproximados. Os métodos aproximados também tendem naturalmente a evitar a sobremontagem.

Existem três formas de utilizar o módulo De Avaliar o Modelo:

- Gere pontuações sobre os seus dados de treino para avaliar o modelo
- Gere pontuações no modelo, mas compare essas pontuações com pontuações num conjunto de testes reservado
- Compare pontuações para dois modelos diferentes mas relacionados, usando o mesmo conjunto de dados

Para obter uma lista completa de métricas e abordagens, pode utilizar para avaliar a precisão dos modelos de aprendizagem automática, consulte [o módulo De Avaliar o Modelo.](./algorithm-module-reference/evaluate-model.md?WT.mc_id=docs-article-lazzeri)

## <a name="training-time"></a>Tempo de preparação

Na aprendizagem supervisionada, o treino significa usar dados históricos para construir um modelo de aprendizagem automática que minimize os erros. O número de minutos ou horas necessárias para treinar um modelo varia muito entre algoritmos. O tempo de treino está muitas vezes intimamente ligado à precisão; um normalmente acompanha o outro. 

Além disso, alguns algoritmos são mais sensíveis ao número de pontos de dados do que outros. Pode escolher um algoritmo específico porque tem uma limitação de tempo, especialmente quando o conjunto de dados é grande.

No designer de Machine Learning, criar e utilizar um modelo de aprendizagem automática é tipicamente um processo em três etapas:

1.  Configurar um modelo, escolhendo um tipo específico de algoritmo, e, em seguida, definindo os seus parâmetros ou hiperparímetros. 

2.  Forneça um conjunto de dados que esteja rotulado e tenha dados compatíveis com o algoritmo. Ligue tanto os dados como o modelo ao [módulo Train Model](./algorithm-module-reference/train-model.md?WT.mc_id=docs-article-lazzeri).

3.  Após o treino estar concluído, utilize o modelo treinado com um dos [módulos de pontuação](./algorithm-module-reference/score-model.md?WT.mc_id=docs-article-lazzeri) para fazer previsões sobre novos dados.

## <a name="linearity"></a>Linearidade

A linearidade nas estatísticas e na aprendizagem automática significa que existe uma relação linear entre uma variável e uma constante no seu conjunto de dados. Por exemplo, os algoritmos de classificação linear assumem que as classes podem ser separadas por uma linha reta (ou o seu analógico superior).

Muitos algoritmos de aprendizagem automática fazem uso da linearidade. No designer de Machine Learning Azure, incluem: 

- [Regressão logística multiclasse](./algorithm-module-reference/multiclass-logistic-regression.md?WT.mc_id=docs-article-lazzeri)
- [Regressão logística de duas classes](./algorithm-module-reference/two-class-logistic-regression.md?WT.mc_id=docs-article-lazzeri)
- [Apoiar máquinas de vetor](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri)  

Os algoritmos lineares de regressão assumem que as tendências de dados seguem uma linha reta. Esta suposição não é má para alguns problemas, mas para outros reduz a precisão. Apesar dos seus inconvenientes, os algoritmos lineares são populares como uma primeira estratégia. Tendem a ser algoritmoticamente simples e rápidos de treinar.

![Limite de classe nonlinear](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***Limite de classe nonlinear** _: _Relying num algoritmo de classificação linear resultaria em baixa precisão.*

![Dados com tendência não linear](./media/how-to-select-algorithms/nonlinear-trend.png)

***Dados com uma tendência não linear** _: _Using um método linear de regressão geraria erros muito maiores do que o necessário.*

## <a name="number-of-parameters"></a>Número de parâmetros

Os parâmetros são os botões que um cientista de dados consegue rodar ao configurar um algoritmo. São números que afetam o comportamento do algoritmo, como a tolerância ao erro ou o número de iterações, ou opções entre variantes de como o algoritmo se comporta. O tempo de treino e a precisão do algoritmo podem por vezes ser sensíveis a obter as definições certas. Tipicamente, algoritmos com um grande número de parâmetros requerem mais tentativa e erro para encontrar uma boa combinação.

Em alternativa, existe o [módulo Tune Model Hyperparameters](./algorithm-module-reference/tune-model-hyperparameters.md?WT.mc_id=docs-article-lazzeri) no designer machine learning: O objetivo deste módulo é determinar os hiperparmetros ideais para um modelo de aprendizagem automática. O módulo constrói e testa vários modelos utilizando diferentes combinações de configurações. Compara métricas em todos os modelos para obter as combinações de configurações. 

Embora esta seja uma ótima maneira de se certificar de que estendeu o espaço dos parâmetros, o tempo necessário para treinar um modelo aumenta exponencialmente com o número de parâmetros. O lado positivo é que ter muitos parâmetros normalmente indica que um algoritmo tem maior flexibilidade. Muitas vezes pode obter uma precisão muito boa, desde que encontre a combinação certa de definições de parâmetros.

## <a name="number-of-features"></a>Número de funcionalidades

Na aprendizagem automática, uma característica é uma variável quantificável do fenómeno que está a tentar analisar. Para certos tipos de dados, o número de funcionalidades pode ser muito grande em comparação com o número de pontos de dados. É frequente o caso da genética ou dos dados texuais. 

Um grande número de funcionalidades pode afundar alguns algoritmos de aprendizagem, tornando o tempo de treino invisivelmente longo. [As máquinas vetoriais de suporte](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri) são particularmente adequadas a cenários com um elevado número de características. Por esta razão, têm sido utilizados em muitas aplicações, desde a recuperação de informações até à classificação de texto e imagem. As máquinas vetoriais de suporte podem ser usadas tanto para tarefas de classificação como para regressão.

A seleção de recursos refere-se ao processo de aplicação de testes estatísticos às entradas, dada uma saída especificada. O objetivo é determinar quais as colunas mais preditivas da saída. O [módulo de seleção de recursos baseado](./algorithm-module-reference/filter-based-feature-selection.md?WT.mc_id=docs-article-lazzeri) em filtro no designer machine learning fornece vários algoritmos de seleção de recursos para escolher. O módulo inclui métodos de correlação como a correlação de Pearson e os valores chi-quadrados.

Também pode utilizar o [módulo Permutaation Feature Importance](./algorithm-module-reference/permutation-feature-importance.md?WT.mc_id=docs-article-lazzeri) para calcular um conjunto de pontuações de importância de característica para o seu conjunto de dados. Em seguida, pode aproveitar estas pontuações para ajudá-lo a determinar as melhores funcionalidades a utilizar num modelo.

## <a name="next-steps"></a>Passos seguintes

 - [Saiba mais sobre o designer de Aprendizagem automática Azure](./concept-designer.md?WT.mc_id=docs-article-lazzeri)
 - Para descrições de todos os algoritmos de aprendizagem automática disponíveis no designer de Machine Learning Azure, consulte [algoritmo de designer de Machine Learning e referência de módulo](./algorithm-module-reference/module-reference.md?WT.mc_id=docs-article-lazzeri)
 - Para explorar a relação entre aprendizagem profunda, aprendizagem automática e IA, consulte [Deep Learning vs. Machine Learning](./concept-deep-learning-vs-machine-learning.md?WT.mc_id=docs-article-lazzeri)