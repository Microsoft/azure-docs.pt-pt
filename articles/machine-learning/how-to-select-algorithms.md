---
title: Como selecionar um algoritmo de aprendizado de máquina
titleSuffix: Azure Machine Learning
description: Como selecionar algoritmos de Azure Machine Learning para aprendizado supervisionado e não supervisionado em experimentos de clustering, classificação ou regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 01/21/2020
ms.openlocfilehash: 65f43bf87e704c85d83220f4ffbc50581aafb549
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76315470"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Como selecionar algoritmos para Azure Machine Learning

Uma pergunta comum é "qual algoritmo de aprendizado de máquina devo usar?" O algoritmo selecionado depende principalmente de dois aspectos diferentes do cenário de ciência de dados:

 - **O que você deseja fazer com seus dados?** Especificamente, qual é a pergunta comercial que você deseja responder aprendendo com seus dados passados?

 - **Quais são os requisitos de seu cenário de ciência de dados?** Especificamente, qual é a precisão, o tempo de treinamento, a linearidade, o número de parâmetros e o número de recursos aos quais sua solução dá suporte?

 ![Considerações para escolher algoritmos: o que você deseja saber? Quais são os requisitos do cenário?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>Roteiros de negócios e a folha de consulta do algoritmo de Machine Learning

A [folha](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri) de consulta do algoritmo de Azure Machine Learning ajuda na primeira consideração: **o que você deseja fazer com seus dados**? Na folha de consulta do algoritmo de Machine Learning, procure a tarefa que você deseja fazer e localize um algoritmo de [Designer de Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri) para a solução de análise preditiva. 

O designer de Machine Learning fornece um portfólio abrangente de algoritmos, como [floresta de decisão multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri), [sistemas de recomendação](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri), [regressão de rede neural](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri), [rede neural multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri)e [clustering de K-](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri)means. Cada algoritmo é projetado para resolver um tipo diferente de problema de aprendizado de máquina. Consulte o [algoritmo do designer de Machine Learning e a referência de módulo](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri) para obter uma lista completa, juntamente com a documentação sobre como cada algoritmo funciona e como ajustar parâmetros para otimizar o algoritmo.

> [!NOTE]
> Para baixar a folha de consulta do algoritmo de aprendizado de máquina, vá para a folha de consulta [do algoritmo do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri).
> 
> 

Juntamente com as diretrizes na folha de consulta do algoritmo Azure Machine Learning, tenha em mente outros requisitos ao escolher um algoritmo de aprendizado de máquina para sua solução. A seguir, são fatores adicionais a serem considerados, como precisão, tempo de treinamento, linearidade, número de parâmetros e número de recursos.

## <a name="additional-requirements-for-a-data-science-scenario"></a>Requisitos adicionais para um cenário de ciência de dados

Depois de saber o que você deseja fazer com seus dados, você precisa determinar os requisitos adicionais para sua solução. 

Faça escolhas e, possivelmente, compensações para os seguintes requisitos:

- Correta
- Tempo de preparação
- Linearidade
- Número de parâmetros
- Número de recursos

## <a name="accuracy"></a>Correta

A precisão no aprendizado de máquina mede a eficácia de um modelo como a proporção de resultados verdadeiros em casos totais. No Machine Learning designer, o [módulo avaliar modelo](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri) computa um conjunto de métricas de avaliação padrão do setor. Você pode usar este módulo para medir a precisão de um modelo treinado.

Obter a resposta mais precisa possível nem sempre é necessário. Às vezes, uma aproximação é adequada, dependendo de como você deseja usá-la. Se esse for o caso, você poderá reduzir drasticamente o tempo de processamento acompanhando os métodos mais aproximados. Os métodos aproximados também tendem naturalmente a evitar o superajuste.

Há três maneiras de usar o módulo avaliar modelo:

- Gerar pontuações sobre seus dados de treinamento para avaliar o modelo
- Gerar pontuações no modelo, mas comparar essas pontuações com pontuações em um conjunto de teste reservado
- Comparar pontuações para dois modelos diferentes, mas relacionados, usando o mesmo conjunto de dados

Para obter uma lista completa de métricas e abordagens que você pode usar para avaliar a precisão dos modelos de aprendizado de máquina, consulte [avaliar módulo modelo](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri).

## <a name="training-time"></a>Tempo de preparação

No aprendizado supervisionado, o treinamento significa usar dados históricos para criar um modelo de aprendizado de máquina que minimiza os erros. O número de minutos ou horas necessárias para treinar um modelo varia muito entre algoritmos. O tempo de treinamento geralmente está fortemente vinculado à precisão; um normalmente acompanha o outro. 

Além disso, alguns algoritmos são mais sensíveis ao número de pontos de dados do que outros. Você pode escolher um algoritmo específico porque tem uma limitação de tempo, especialmente quando o conjunto de dados é grande.

No Machine Learning designer, criar e usar um modelo de aprendizado de máquina normalmente é um processo de três etapas:

1.  Configure um modelo, escolhendo um tipo específico de algoritmo e, em seguida, definindo seus parâmetros ou hiperparâmetros. 

2.  Forneça um conjunto de dados que seja rotulado e que seja compatível com o algoritmo. Conecte os dados e o modelo para [treinar o módulo modelo](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri).

3.  Após a conclusão do treinamento, use o modelo treinado com um dos [módulos de Pontuação](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri) para fazer previsões sobre novos dados.

## <a name="linearity"></a>Linearidade

A linearidade em estatísticas e no aprendizado de máquina significa que há uma relação linear entre uma variável e uma constante em seu conjunto de informações. Por exemplo, os algoritmos de classificação linear pressupõem que as classes podem ser separadas por uma linha reta (ou sua analogia de alta dimensional).

Muitos algoritmos de aprendizado de máquina fazem uso da linearidade. No Azure Machine Learning designer, eles incluem: 

- [Regressão logística multiclasse](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Regressão logística de duas classes](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [Computadores de vetor de suporte](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

Algoritmos de regressão linear pressupõem que as tendências de dados seguem uma linha reta. Essa suposição não é muito boa para alguns problemas, mas para outros, ela reduz a precisão. Apesar de suas desvantagens, os algoritmos lineares são populares como uma primeira estratégia. Eles tendem a ser forma algorítmica simples e rápidos de treinar.

![Limite de classe não linear](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***Limite de classe não linear***: *depender de um algoritmo de classificação linear resultaria em baixa precisão.*

![Dados com uma tendência não linear](./media/how-to-select-algorithms/nonlinear-trend.png)

***Dados com uma tendência não linear***: *usar um método de regressão linear geraria erros muito maiores do que o necessário.*

## <a name="number-of-parameters"></a>Número de parâmetros

Os parâmetros são os botões que um cientista de dados obtém para se transformar ao configurar um algoritmo. Eles são números que afetam o comportamento do algoritmo, como tolerância a erros ou número de iterações, ou opções entre variantes de como o algoritmo se comporta. Às vezes, o tempo de treinamento e a precisão do algoritmo podem ser confidenciais para obter apenas as configurações corretas. Normalmente, os algoritmos com um grande número de parâmetros exigem a mais avaliação e o erro para encontrar uma boa combinação.

Como alternativa, há o [módulo ajustar hiperparâmetros de modelo](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri) no designer de Machine Learning: o objetivo desse módulo é determinar os hiperparâmetros ideais para um modelo de aprendizado de máquina. O módulo cria e testa vários modelos usando diferentes combinações de configurações. Ele compara métricas em todos os modelos para obter as combinações de configurações. 

Embora essa seja uma ótima maneira de certificar-se de que você colocou o espaço de parâmetro, o tempo necessário para treinar um modelo aumenta exponencialmente com o número de parâmetros. A vantagem é que ter muitos parâmetros normalmente indica que um algoritmo tem maior flexibilidade. Geralmente, isso pode atingir uma precisão muito boa, desde que você encontre a combinação certa de configurações de parâmetro.

## <a name="number-of-features"></a>Número de recursos

No Machine Learning, um recurso é uma variável quantificável do fenômeno que você está tentando analisar. Para determinados tipos de dados, o número de recursos pode ser muito grande em comparação ao número de pontos de dados. Geralmente, esse é o caso com dados genéticos ou textuais. 

Um grande número de recursos pode atrasár alguns algoritmos de aprendizado, tornando o tempo de treinamento unfeasibly longo. As [máquinas de vetor de suporte](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri) são particularmente adequadas para cenários com um grande número de recursos. Por esse motivo, eles foram usados em muitos aplicativos da recuperação de informações para a classificação de texto e imagem. As máquinas de vetor de suporte podem ser usadas para tarefas de classificação e regressão.

A seleção de recursos refere-se ao processo de aplicação de testes estatísticos a entradas, dado uma saída especificada. O objetivo é determinar quais colunas são mais previsíveis na saída. O [módulo seleção de recursos baseada em filtro](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri) no Machine Learning designer fornece vários algoritmos de seleção de recursos para escolher. O módulo inclui métodos de correlação, como os valores de correlação Pearson e qui-quadrado.

Você também pode usar o [módulo de importância do recurso de permuta](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri) para calcular um conjunto de pontuações de importância do recurso para seu conjunto de seus conjuntos de resultados. Você pode aproveitar essas pontuações para ajudá-lo a determinar os melhores recursos a serem usados em um modelo.


## <a name="next-steps"></a>Passos seguintes

 - [Saiba mais sobre o designer de Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-designer?WT.mc_id=docs-article-lazzeri)
 - Para obter descrições de todos os algoritmos de aprendizado de máquina disponíveis no designer de Azure Machine Learning, consulte [algoritmo de designer de Machine Learning e referência de módulo](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri)
 - Para explorar a relação entre aprendizado profundo, aprendizado de máquina e ia, consulte [aprendizado profundo versus Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri)
