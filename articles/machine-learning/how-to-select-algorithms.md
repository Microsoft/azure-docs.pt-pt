---
title: Como selecionar um algoritmo de aprendizagem automática
titleSuffix: Azure Machine Learning
description: Como selecionar algoritmos de Aprendizagem automática Azure para aprendizagem supervisionada e sem supervisão em experiências de agrupamento, classificação ou regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 05/07/2020
ms.openlocfilehash: ad384896301e809940f6e99df2f5562cfdb6a6fe
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927586"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Como selecionar algoritmos para Azure Machine Learning

Uma pergunta comum é: "Que algoritmo de aprendizagem automática devo usar?" O algoritmo que selecionar depende principalmente de dois aspetos diferentes do seu cenário de ciência de dados:

 - **O que quer fazer com os seus dados?** Especificamente, qual é a questão do negócio que quer responder aprendendo com os seus dados anteriores?

 - **Quais são os requisitos do seu cenário de ciência de dados?** Especificamente, qual é a precisão, tempo de treino, linearidade, número de parâmetros e número de funcionalidades que a sua solução suporta?

 ![Considerações para escolher algoritmos: O que quer saber? Quais são os requisitos do cenário?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>Cenários de negócios e a folha de batota do algoritmo de aprendizagem automática

A Folha de Batota do Algoritmo de [Aprendizagem automática Azure](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri) ajuda-o com a primeira consideração: **O que quer fazer com os seus dados**? Na Folha de Batota do Algoritmo de Aprendizagem automática, procure uma tarefa que queira fazer e, em seguida, encontre um algoritmo de designer de [machine learning Azure](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri) para a solução de análise preditiva. 

O designer de Machine Learning fornece um portfólio abrangente de algoritmos, tais como Floresta de [Decisão Multiclasse,](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri) [Sistemas de Recomendação,](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri) [Regressão da Rede Neural,](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri) [Rede Neural Multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri)e [Clustering K-Means.](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri) Cada algoritmo é projetado para resolver um tipo diferente de problema de aprendizagem automática. Consulte o algoritmo de design de [Machine Learning e](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) a referência do módulo para uma lista completa juntamente com documentação sobre como cada algoritmo funciona e como sintonizar parâmetros para otimizar o algoritmo.

> [!NOTE]
> Para descarregar a folha de batota do algoritmo de aprendizagem automática, vá à folha de batota do algoritmo de [aprendizagem da Máquina Azure](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri).
> 
> 

Juntamente com a orientação na Folha de Batota de Algoritmo de Aprendizagem automática Azure, lembre-se de outros requisitos ao escolher um algoritmo de aprendizagem automática para a sua solução. Seguem-se fatores adicionais a ter em conta, como a precisão, o tempo de treino, a linearidade, o número de parâmetros e o número de características.

## <a name="comparison-of-machine-learning-algorithms"></a>Comparação de algoritmos de aprendizagem automática

Alguns algoritmos de aprendizagem fazem pressupostos particulares sobre a estrutura dos dados ou os resultados desejados. Se conseguir encontrar um que se adapte às suas necessidades, pode dar-lhe resultados mais úteis, previsões mais precisas ou tempos de treino mais rápidos.

A tabela que se segue resume algumas das características mais importantes dos algoritmos da classificação, regressão e agrupamento de famílias:

| **Algoritmo** | **Precisão** | **Tempo de preparação** | **Linearidade** | **Parâmetros** | **Notas** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Família de classificação** | | | | | |
| [Regressão logística de duas classes](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri) |Bom  |Rápido |Sim |4 | |
| [Floresta de decisão de duas classes](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-decision-forest?WT.mc_id=docs-article-lazzeri) |Excelente |Moderado |Não |5 |Mostra tempos de pontuação mais lentos. Sugiro que não trabalhe com a One-vs-All Multiclass, devido aos tempos de pontuação mais lentos causados pelo bloqueio de pisonas previsões de árvores acumuladas |
| [Árvore de decisão impulsionada por duas classes](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-boosted-decision-tree?WT.mc_id=docs-article-lazzeri) |Excelente |Moderado |Não |6 |Grande pegada de memória |
| [Rede neural de duas classes](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-neural-network?WT.mc_id=docs-article-lazzeri) |Bom |Moderado |Não |8 | |
| [Perceptron médio de duas classes](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-averaged-perceptron?WT.mc_id=docs-article-lazzeri) |Bom |Moderado |Sim |4 | |
| [Máquina vetorial de suporte de duas classes](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) |Bom |Rápido |Sim |5 |Bom para grandes conjuntos de recursos |
| [Regressão logística multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri) |Bom |Rápido |Sim |4 | |
| [Floresta de decisão multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri) |Excelente |Moderado |Não |5 |Mostra tempos de pontuação mais lentos |
| [Árvore de decisão impulsionada por multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-boosted-decision-tree?WT.mc_id=docs-article-lazzeri) |Excelente |Moderado |Não |6 | Tende a melhorar a precisão com algum pequeno risco de menor cobertura |
| [Rede neural multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri) |Bom |Moderado |Não |8 | |
| [Multiclasse One-vs-all](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/one-vs-all-multiclass?WT.mc_id=docs-article-lazzeri) | - | - | - | - |Ver propriedades do método de duas classes selecionadas |
| **Família de regressão** | | | | | |
| [Regressão linear](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/linear-regression?WT.mc_id=docs-article-lazzeri) |Bom |Rápido |Sim |4 | |
| [Regressão da floresta de decisão](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/decision-forest-regression?WT.mc_id=docs-article-lazzeri)|Excelente |Moderado |Não |5 | |
| [Regresão de árvore de decisão reforçada](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/boosted-decision-tree-regression?WT.mc_id=docs-article-lazzeri) |Excelente |Moderado |Não |6 |Grande pegada de memória |
| [Regressão da rede neural](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri) |Bom |Moderado |Não |8 | |
| **Família de agrupamento** | | | | | |
| [Agrupamento de k significa](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri) |Excelente |Moderado |Sim |8 |Um algoritmo de agrupamento |

## <a name="requirements-for-a-data-science-scenario"></a>Requisitos para um cenário de ciência de dados

Assim que souber o que pretende fazer com os seus dados, tem de determinar requisitos adicionais para a sua solução. 

Faça escolhas e, possivelmente, trocas para os seguintes requisitos:

- Precisão
- Tempo de preparação
- Linearidade
- Número de parâmetros
- Número de características

## <a name="accuracy"></a>Precisão

A precisão na aprendizagem automática mede a eficácia de um modelo como proporção de resultados reais para casos totais. No designer de Machine Learning, o [módulo De Avaliação](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri) computa um conjunto de métricas de avaliação padrão da indústria. Pode utilizar este módulo para medir a precisão de um modelo treinado.

Obter a resposta mais precisa possível nem sempre é necessário. Às vezes, uma aproximação é adequada, dependendo do que se quer usar. Se for esse o caso, poderá reduzir drasticamente o seu tempo de processamento mantendo-se com métodos mais aproximados. Os métodos aproximados também tendem naturalmente a evitar o excesso de encaixe.

Existem três formas de utilizar o módulo Modelo de Avaliação:

- Gere pontuações sobre os seus dados de treino de forma a avaliar o modelo
- Gere pontuações no modelo, mas compare essas pontuações com pontuações num conjunto de testes reservados
- Compare pontuações para dois modelos diferentes mas relacionados, usando o mesmo conjunto de dados

Para obter uma lista completa de métricas e abordagens que pode utilizar para avaliar a precisão dos modelos de aprendizagem automática, consulte avaliar o [módulo Model](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri).

## <a name="training-time"></a>Tempo de preparação

Na aprendizagem supervisionada, a formação significa usar dados históricos para construir um modelo de aprendizagem automática que minimize os erros. O número de minutos ou horas necessários para treinar um modelo varia muito entre algoritmos. O tempo de treino está muitas vezes intimamente ligado à precisão; um normalmente acompanha o outro. 

Além disso, alguns algoritmos são mais sensíveis ao número de pontos de dados do que outros. Pode escolher um algoritmo específico porque tem uma limitação de tempo, especialmente quando o conjunto de dados é grande.

No designer de Machine Learning, criar e usar um modelo de machine learning é tipicamente um processo em três etapas:

1.  Configure um modelo, escolhendo um tipo específico de algoritmo, e, em seguida, definindo os seus parâmetros ou hiperparâmetros. 

2.  Forneça um conjunto de dados que esteja rotulado e tenha dados compatíveis com o algoritmo. Ligue os dados e o modelo ao [módulo Modelo de Comboio](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri).

3.  Após o treino estar concluído, utilize o modelo treinado com um dos módulos de [pontuação](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri) para fazer previsões sobre novos dados.

## <a name="linearity"></a>Linearidade

Linearidade nas estatísticas e machine learning significa que existe uma relação linear entre uma variável e uma constante no seu conjunto de dados. Por exemplo, os algoritmos de classificação linear assumem que as classes podem ser separadas por uma linha reta (ou o seu analógico superior).

Muitos algoritmos de aprendizagem automática fazem uso da linearidade. No designer de Machine Learning Azure, incluem: 

- [Regressão logística multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Regressão logística de duas classes](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Máquinas vetoriais de suporte](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

Os algoritmos lineares de regressão assumem que as tendências de dados seguem uma linha reta. Esta suposição não é má para alguns problemas, mas para outros reduz a precisão. Apesar das suas desvantagens, os algoritmos lineares são populares como uma primeira estratégia. Tendem a ser algorítmicos simples e rápidos para treinar.

![Limite de classe não linear](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***Limite***de classe não linear : Confiar num algoritmo de *classificação linear resultaria em baixa precisão.*

![Dados com tendência não linear](./media/how-to-select-algorithms/nonlinear-trend.png)

***Dados com uma tendência não linear:*** Utilizar um método de *regressão linear geraria erros muito maiores do que o necessário.*

## <a name="number-of-parameters"></a>Número de parâmetros

Os parâmetros são os botões que um cientista de dados consegue rodar ao configurar um algoritmo. São números que afetam o comportamento do algoritmo, como a tolerância ao erro ou o número de iterações, ou opções entre variantes de como o algoritmo se comporta. O tempo de treino e a precisão do algoritmo podem, por vezes, ser sensíveis a obter apenas as configurações certas. Tipicamente, algoritmos com um grande número de parâmetros requerem o maior teste e erro para encontrar uma boa combinação.

Em alternativa, existe o [módulo tune Model Hyperparameters](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri) no designer de Machine Learning: O objetivo deste módulo é determinar os hiperparâmetros ideais para um modelo de machine learning. O módulo constrói e testa vários modelos utilizando diferentes combinações de configurações. Compara métricas em todos os modelos para obter as combinações de configurações. 

Embora esta seja uma ótima maneira de se certificar de que você estendeu o espaço do parâmetro, o tempo necessário para treinar um modelo aumenta exponencialmente com o número de parâmetros. O lado positivo é que ter muitos parâmetros tipicamente indica que um algoritmo tem maior flexibilidade. Muitas vezes pode obter uma precisão muito boa, desde que possa encontrar a combinação certa de definições de parâmetros.

## <a name="number-of-features"></a>Número de características

Na aprendizagem automática, uma característica é uma variável quantificável do fenómeno que está a tentar analisar. Para certos tipos de dados, o número de funcionalidades pode ser muito grande em comparação com o número de pontos de dados. É frequentemente o caso da genética ou dos dados texuais. 

Um grande número de funcionalidades pode afundar alguns algoritmos de aprendizagem, tornando o tempo de treino invisivelmente longo. [As máquinas vetoriais](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) de suporte são particularmente adequadas a cenários com um elevado número de características. Por esta razão, têm sido utilizados em muitas aplicações, desde a recuperação de informação até à classificação de texto e imagem. As máquinas vetoriais de suporte podem ser usadas tanto para tarefas de classificação como para a regressão.

A seleção de funcionalidades refere-se ao processo de aplicação de testes estatísticos a entradas, dada uma saída especificada. O objetivo é determinar quais as colunas mais preditivas da saída. O módulo de seleção de [funcionalidades baseado](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri) em filtros no designer de machine learning fornece vários algoritmos de seleção de funcionalidades para escolher. O módulo inclui métodos de correlação como a correlação pearson e os valores cin-quadrados.

Também pode utilizar o [módulo De Importância](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri) da Característica Permutação para calcular um conjunto de pontuações de importância de funcionalidades para o seu conjunto de dados. Em seguida, pode aproveitar estas pontuações para ajudá-lo a determinar as melhores funcionalidades para usar num modelo.

## <a name="next-steps"></a>Passos seguintes

 - [Saiba mais sobre o designer de machine learning Azure](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri)
 - Para descrições de todos os algoritmos de aprendizagem automática disponíveis no designer de machine learning azure, consulte algoritmo de design de [Machine Learning e referência](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) de módulo
 - Para explorar a relação entre a aprendizagem profunda, a aprendizagem automática e a IA, consulte [Deep Learning vs. Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri)
