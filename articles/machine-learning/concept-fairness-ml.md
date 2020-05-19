---
title: Avaliar e mitigar a equidade nos modelos de aprendizagem automática
titleSuffix: Azure Machine Learning
description: Aprenda sobre a equidade nos modelos de machine learning e como o pacote Fairlearn Python pode ajudá-lo a construir modelos mais justos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/02/2020
ms.openlocfilehash: c21ec0329a7b5716a00262b7422296df3afe208b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598606"
---
# <a name="fairness-in-machine-learning-models"></a>Equidade nos modelos de aprendizagem automática

Saiba mais sobre justiça na aprendizagem automática e como o pacote Fairlearn de open-source Python pode ajudá-lo a construir modelos mais justos.

## <a name="what-is-fairness-in-machine-learning-systems"></a>O que é a justiça nos sistemas de aprendizagem automática?

Os sistemas de inteligência artificial e aprendizagem automática podem apresentar comportamentos injustos. Uma maneira de definir comportamentos injustos é pelo seu mal, ou impacto nas pessoas. Há muitos tipos de danos que os sistemas de IA podem dar origem. Dois tipos comuns de danos causados pela IA são:

- Danos na atribuição: Um sistema de IA alarga ou retém oportunidades, recursos ou informações. Exemplos incluem contratação, admissão na escola e empréstimos onde um modelo poderia ser muito melhor na escolha de bons candidatos entre um grupo específico de pessoas do que entre outros grupos.

- Danos à qualidade do serviço: Um sistema de IA não funciona tão bem para um grupo de pessoas como para outro. A seu favor, um sistema de reconhecimento de voz pode não funcionar tão bem para as mulheres como para os homens.

Para reduzir comportamentos injustos nos sistemas de IA, tem de avaliar e mitigar estes danos.

>[!NOTE]
> A justiça é um desafio sociotécnico. Muitos aspetos da equidade, como a justiça e o devido processo, não são capturados em métricas quantitativas de equidade. Além disso, muitas métricas de equidade quantitativa não podem ser satisfeitas simultaneamente. O objetivo é permitir que os humanos avaliem diferentes estratégias de mitigação e, em seguida, fazer trocas adequadas ao seu cenário.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Avaliação e mitigação da equidade com fairlearn

Fairlearn é um pacote python de código aberto que permite aos desenvolvedores de sistemas de aprendizagem automática avaliar a equidade dos seus sistemas e mitigar as questões de equidade observadas.

Fairlearn tem dois componentes:

- Painel de avaliação: Um widget de caderno Jupyter para avaliar como as previsões de um modelo afetam diferentes grupos. Também permite comparar vários modelos utilizando métricas de equidade e desempenho.
- Algoritmos de Mitigação: Um conjunto de algoritmos para mitigar a injustiça na classificação binária e na regressão.

Juntos, estes componentes permitem aos cientistas de dados e líderes empresariais navegar em quaisquer trocas entre a equidade e o desempenho, e selecionar a estratégia de mitigação que melhor se adequa às suas necessidades.

## <a name="fairness-assessment"></a>Avaliação da equidade

Em Fairlearn, a equidade é conceptualizada embora uma abordagem conhecida como justiça de **grupo**, que pergunta: Que grupos de indivíduos estão em risco de sofrer danos?

Os grupos relevantes, também conhecidos como subpopulações, são definidos através de **características sensíveis** ou atributos sensíveis. Características sensíveis são passadas para um estimador fairlearn como um vetor ou uma matriz chamada `sensitive_features` . O termo sugere que o designer do sistema deve ser sensível a estas características ao avaliar a equidade do grupo. Algo a ter em conta é se estas funcionalidades contêm implicações de privacidade devido a informações pessoalmente identificáveis. Mas a palavra "sensível" não implica que estas características não devam ser usadas para fazer previsões.

Durante a fase de avaliação, a equidade é quantificada através de métricas de disparidade. **As métricas** de disparidade podem avaliar e comparar o comportamento do modelo em diferentes grupos, quer como rácios, quer como diferenças. Fairlearn apoia duas classes de métricas de disparidade:


- Disparidade no desempenho do modelo: Estes conjuntos de métricas calculam a disparidade (diferença) nos valores da métrica de desempenho selecionada em diferentes subgrupos. Alguns exemplos incluem:

  - disparidade na taxa de precisão
  - disparidade na taxa de erro
  - disparidade na precisão
  - disparidade na recuperação
  - disparidade no MAE
  - muitos outros

- Disparidade na taxa de seleção: Esta métrica contém a diferença na taxa de seleção entre os diferentes subgrupos. Um exemplo disso é a disparidade na taxa de aprovação de empréstimos. Taxa de seleção: a fração de pontos de dados em cada classe classificada como 1 (em classificação binária) ou a distribuição dos valores de previsão (em regressão).

## <a name="unfairness-mitigation"></a>Mitigação da injustiça

### <a name="parity-constraints"></a>Restrições de paridade

Fairlearn inclui uma variedade de algoritmos de mitigação da injustiça. Estes algoritmos suportam um conjunto de restrições no comportamento do preditor chamado **simpalidade seletiva ou critérios.** As restrições de paridade requerem que alguns aspetos do comportamento do preditor sejam comparáveis entre os grupos que as características sensíveis definem (por exemplo, raças diferentes). Os algoritmos de mitigação da Fairlearn usam tais restrições de paridade para mitigar as questões de equidade observadas.

A Fairlearn suporta os seguintes tipos de restrições de paridade:

|Restrição de paridade  | Objetivo  |Tarefa de aprendizagem automática  |
|---------|---------|---------|
|Paridade demográfica     |  Atenuar os danos na atribuição | Classificação binária, Regressão |
|Probabilidades igualizadas  | Atribuição de diagnóstico e danos de qualidade de serviço | Classificação binária        |
|Perda de grupo limitada     |  Atenuar os danos de qualidade de serviço | Regressão |

### <a name="mitigation-algorithms"></a>Algoritmos de mitigação

Fairlearn fornece algoritmos de mitigação pós-processamento e redução da injustiça:

- Redução: Estes algoritmos pegam num estimador de ml de caixa preta padrão (por exemplo, um modelo LightGBM) e geram um conjunto de modelos retreinados utilizando uma sequência de conjuntos de dados de treino reponderados. Por exemplo, os candidatos a um determinado género podem ser ponderados ou ponderados para reconverter modelos e reduzir as disparidades entre diferentes grupos de género. Os utilizadores podem então escolher um modelo que proporcione a melhor compensação entre precisão (ou outra métrica de desempenho) e disparidade, que geralmente teria de se basear em regras de negócio e cálculos de custos.  
- Pós-processamento: Estes algoritmos tomam um classificador existente e a funcionalidade sensível como entrada. Depois, derivam de uma transformação da previsão do classificador para impor os restrições de equidade especificados. A maior vantagem da otimização do limiar é a sua simplicidade e flexibilidade, uma vez que não precisa de retreinar o modelo. 

| Algoritmo | Descrição | Tarefa de aprendizagem automática | Características sensíveis | Restrições de paridade suportadas | Tipo de algoritmo |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | Abordagem black-box para uma classificação justa descrita em [A Reduções Abordagem à Classificação Justa](https://arxiv.org/abs/1803.02453) | Classificação binária | Categórico | [Paridade demográfica,](#parity-constraints) [odds iguais](#parity-constraints) | Redução |
| `GridSearch` | Abordagem black-box descrita em Uma Abordagem de [Reduções à Classificação Justa](https://arxiv.org/abs/1803.02453)| Classificação binária | Binário | [Paridade demográfica,](#parity-constraints) [odds iguais](#parity-constraints) | Redução |
| `GridSearch` | Abordagem black-box que implementa uma variante de pesquisa de grelha de regressão justa com o algoritmo de perda de grupo limitada descrito em [Regressão Justa: Definições Quantitativas e Algoritmos baseados em Redução](https://arxiv.org/abs/1905.12843) | Regressão | Binário | [Perda de grupo limitada](#parity-constraints) | Redução |
| `ThresholdOptimizer` | Algoritmo pós-processamento baseado no papel [Igualdade de Oportunidade sintetizada em Aprendizagem Supervisionada.](https://arxiv.org/abs/1610.02413) Esta técnica toma como entrada um classificador existente e a característica sensível, e deriva de uma transformação monótona da previsão do classificador para impor as restrições de paridade especificadas. | Classificação binária | Categórico | [Paridade demográfica,](#parity-constraints) [odds iguais](#parity-constraints) | Pós-processamento |

## <a name="next-steps"></a>Passos seguintes

- Para aprender a usar os diferentes componentes, consulte os cadernos de [rectório saqueado do Fairlearn GitHub](https://github.com/fairlearn/fairlearn/) e [amostras.](https://github.com/fairlearn/fairlearn/tree/master/notebooks)
- Aprenda a preservar a privacidade dos dados utilizando a [privacidade diferencial e o pacote WhisperNoise](concept-differential-privacy.md).