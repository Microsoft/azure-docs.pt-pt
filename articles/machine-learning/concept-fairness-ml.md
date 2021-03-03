---
title: Mitigar a injustiça nos modelos de aprendizagem automática (pré-visualização)
titleSuffix: Azure Machine Learning
description: Saiba mais sobre a equidade nos modelos de machine learning e como o pacote Fairlearn Python pode ajudá-lo a construir modelos mais justos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 01/26/2021
ms.custom: responsible-ml
ms.openlocfilehash: 276c91ad7fb5b09dbe18d989741f0f54b8b0eb09
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659686"
---
# <a name="mitigate-unfairness-in-machine-learning-models-preview"></a>Mitigar a injustiça nos modelos de aprendizagem automática (pré-visualização)

Saiba mais sobre a equidade na aprendizagem automática e como o pacote Detona de código aberto [fairlearn](https://fairlearn.github.io/) pode ajudá-lo a mitigar problemas de injustiça em modelos de aprendizagem automática. Se não está a fazer um esforço para compreender as questões de equidade e avaliar a equidade na construção de modelos de aprendizagem automática, pode construir modelos que produzam resultados injustos.

O seguinte resumo do guia de [utilizador](https://fairlearn.github.io/main/user_guide/index.html) para o pacote fairlearn open-source, descreve como usá-lo para avaliar a equidade dos sistemas de IA que está a construir.  O pacote fairlearn open-source também pode oferecer opções para ajudar a mitigar, ou ajudar a reduzir, quaisquer problemas de equidade que você observa.  Consulte os cadernos [de como fazer](how-to-machine-learning-fairness-aml.md) e [amostrar](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) para permitir a avaliação da equidade dos sistemas de IA durante a formação em Azure Machine Learning.


## <a name="what-is-fairness-in-machine-learning-models"></a>O que é a equidade nos modelos de aprendizagem automática?

>[!NOTE]
> A justiça é um desafio sociotérmico. Muitos aspetos da equidade, como a justiça e o devido processo, não são capturados em métricas de justiça quantitativa. Além disso, muitas métricas de justiça quantitativa não podem ser satisfeitas simultaneamente. O objetivo com o pacote fairlearn open-source é permitir que os humanos avaliem diferentes estratégias de impacto e mitigação. Em última análise, cabe aos utilizadores humanos construir em inteligência artificial e modelos de machine learning fazer trocas adequadas ao seu cenário.

A inteligência artificial e os sistemas de aprendizagem automática podem apresentar comportamentos injustos. Uma maneira de definir comportamentos injustos é pelo seu dano, ou impacto nas pessoas. Há muitos tipos de danos que os sistemas de IA podem dar origem. Veja o [keynote NeurIPS 2017 de Kate Crawford](https://www.youtube.com/watch?v=fMym_BKWQzk) para saber mais.

Dois tipos comuns de danos causados pela IA são:

- Prejuízo da atribuição: Um sistema de IA alarga ou retém oportunidades, recursos ou informações para determinados grupos. Exemplos incluem contratação, admissões escolares, e empréstimos onde um modelo pode ser muito melhor na escolha de bons candidatos entre um grupo específico de pessoas do que entre outros grupos.

- Danos à qualidade de serviço: Um sistema de IA não funciona tão bem para um grupo de pessoas como para outro. A seu favor, um sistema de reconhecimento de voz pode não funcionar tão bem para as mulheres como para os homens.

Para reduzir comportamentos injustos nos sistemas de IA, é preciso avaliar e mitigar estes danos.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Avaliação e mitigação da equidade com Fairlearn

Fairlearn é um pacote python de código aberto que permite aos desenvolvedores de sistemas de aprendizagem automática avaliar a equidade dos seus sistemas e mitigar a injustiça.

O pacote fairlearn open-source tem dois componentes:

- Painel de avaliação: Um widget de caderno Jupyter para avaliar como as previsões de um modelo afetam diferentes grupos. Também permite comparar vários modelos utilizando métricas de equidade e desempenho.
- Algoritmos de mitigação: Um conjunto de algoritmos para mitigar a injustiça na classificação binária e regressão.

Juntos, estes componentes permitem que cientistas de dados e líderes empresariais naveguem em quaisquer trocas entre justiça e desempenho, e selecionem a estratégia de mitigação que melhor se adequa às suas necessidades.

## <a name="assess-fairness-in-machine-learning-models"></a>Avaliar a equidade nos modelos de aprendizagem automática

No pacote fairlearn open-source, a equidade é conceptualizada embora uma abordagem conhecida como **justiça de grupo**, que pergunta: Que grupos de indivíduos estão em risco de sofrer danos? Os grupos relevantes, também conhecidos como subpopulações, são definidos através de **características sensíveis** ou atributos sensíveis. As características sensíveis são transmitidas a um estimador no pacote fairlearn de código aberto como um vetor ou uma matriz chamada  `sensitive_features` . O termo sugere que o designer de sistemas deve ser sensível a estas características ao avaliar a equidade do grupo. 

Algo a ter em conta é se estas funcionalidades contêm implicações de privacidade devido a dados privados. Mas a palavra "sensível" não implica que estas características não devam ser usadas para fazer previsões.

>[!NOTE]
> Uma avaliação da equidade não é um exercício puramente técnico.  O pacote fairlearn open-source pode ajudá-lo a avaliar a equidade de um modelo, mas não irá realizar a avaliação para si.  O pacote fairlearn de código aberto ajuda a identificar métricas quantitativas para avaliar a equidade, mas os desenvolvedores também devem realizar uma análise qualitativa para avaliar a equidade dos seus próprios modelos.  As características sensíveis acima referidas são um exemplo deste tipo de análise qualitativa.     

Durante a fase de avaliação, a equidade é quantificada através de métricas de disparidade. **As métricas de disparidade** podem avaliar e comparar o comportamento do modelo em diferentes grupos, quer como rácios, quer como diferenças. O pacote fairlearn open-source suporta duas classes de métricas de disparidade:


- Disparidade no desempenho do modelo: Estes conjuntos de métricas calculam a disparidade (diferença) nos valores da métrica de desempenho selecionada em diferentes subgrupos. Alguns exemplos incluem:

  - disparidade na taxa de precisão
  - disparidade na taxa de erro
  - disparidade na precisão
  - disparidade na recordação
  - disparidade no MAE
  - muitos outros

- Disparidade na taxa de seleção: Esta métrica contém a diferença na taxa de seleção entre diferentes subgrupos. Um exemplo disso é a disparidade na taxa de aprovação dos empréstimos. Taxa de seleção: a fração de pontos de dados em cada classe classificada como 1 (na classificação binária) ou distribuição dos valores de previsão (em regressão).

## <a name="mitigate-unfairness-in-machine-learning-models"></a>Mitigar a injustiça nos modelos de aprendizagem automática

### <a name="parity-constraints"></a>Restrições de paridade

O pacote fairlearn open-source inclui uma variedade de algoritmos de mitigação de injustiças. Estes algoritmos suportam um conjunto de restrições no comportamento do preditor **chamados restrições** ou critérios de paridade. Os constrangimentos de paridade requerem que alguns aspetos do comportamento do preditor sejam comparáveis entre os grupos que as características sensíveis definem (por exemplo, raças diferentes). Os algoritmos de mitigação no pacote de código aberto fairlearn usam tais restrições de paridade para mitigar as questões de equidade observadas.

>[!NOTE]
> Atenuar a injustiça num modelo significa reduzir a injustiça, mas esta mitigação técnica não pode eliminar completamente esta injustiça.  Os algoritmos de mitigação da injustiça no pacote fairlearn open-source podem fornecer estratégias de mitigação sugeridas para ajudar a reduzir a injustiça num modelo de aprendizagem automática, mas não são soluções para eliminar completamente a injustiça.  Pode haver outros constrangimentos ou critérios de paridade que devem ser considerados para o modelo de aprendizagem automática de cada desenvolvedor. Os desenvolvedores que usam a Azure Machine Learning devem determinar por si mesmos se a mitigação elimina suficientemente qualquer injustiça na utilização e implantação de modelos de aprendizagem automática.  

O pacote fairlearn open-source suporta os seguintes tipos de restrições de paridade: 

|Restrição de paridade  | Objetivo  |Tarefa de aprendizagem automática  |
|---------|---------|---------|
|Paridade demográfica     |  Mitigar os danos de atribuição | Classificação binária, Regressão |
|Probabilidades iguais  | Alocação de diagnóstico e danos de qualidade de serviço | Classificação binária        |
|Igualdade de oportunidades | Alocação de diagnóstico e danos de qualidade de serviço | Classificação binária        |
|Perda de grupo limitada     |  Mitigar danos de qualidade de serviço | Regressão |



### <a name="mitigation-algorithms"></a>Algoritmos de mitigação

O pacote fairlearn open-source fornece algoritmos de mitigação pós-processamento e redução de injustiças:

- Redução: Estes algoritmos tomam um estimador padrão de aprendizagem de máquinas de caixa preta (por exemplo, um modelo LightGBM) e geram um conjunto de modelos retreinados usando uma sequência de conjuntos de dados de treino re-ponderados. Por exemplo, os candidatos de um determinado género podem ser ponderados ou ponderados para reformular modelos e reduzir as disparidades entre diferentes grupos de género. Os utilizadores podem então escolher um modelo que proporcione a melhor compensação entre precisão (ou outra métrica de desempenho) e disparidade, que geralmente teria de se basear em regras de negócio e cálculos de custos.  
- Pós-processamento: Estes algoritmos tomam um classificador existente e a característica sensível como entrada. Em seguida, derivam uma transformação da previsão do classificador para impor as restrições de equidade especificadas. A maior vantagem da otimização dos limiares é a sua simplicidade e flexibilidade, uma vez que não necessita de reconverter o modelo. 

| Algoritmo | Descrição | Tarefa de aprendizagem automática | Características sensíveis | Restrições de paridade apoiadas | Tipo de algoritmo |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | Abordagem da caixa preta para uma classificação justa descrita na [Abordagem de Reduções A para classificação justa](https://arxiv.org/abs/1803.02453) | Classificação binária | Categórico | [Paridade demográfica,](#parity-constraints) [odds iguais](#parity-constraints) | Redução |
| `GridSearch` | Abordagem da caixa preta descrita na [abordagem de reduções a uma classificação justa](https://arxiv.org/abs/1803.02453)| Classificação binária | Binário | [Paridade demográfica,](#parity-constraints) [odds iguais](#parity-constraints) | Redução |
| `GridSearch` | Abordagem black-box que implementa uma variante de pesquisa de grelha de Regressão Justa com o algoritmo para perda de grupo delimitada descrita em [Regressão Justa: Definições Quantitativas e Algoritmos baseados na Redução](https://arxiv.org/abs/1905.12843) | Regressão | Binário | [Perda de grupo limitada](#parity-constraints) | Redução |
| `ThresholdOptimizer` | Algoritmo pós-processamento baseado no artigo [Igualdade de Oportunidades em Aprendizagem Supervisionada.](https://arxiv.org/abs/1610.02413) Esta técnica toma como entrada um classificador existente e a característica sensível, e deriva uma transformação monotone da previsão do classificador para impor as restrições de paridade especificadas. | Classificação binária | Categórico | [Paridade demográfica,](#parity-constraints) [odds iguais](#parity-constraints) | Pós-processamento |

## <a name="next-steps"></a>Passos seguintes

- Saiba como utilizar os diferentes componentes verificando o [GitHub](https://github.com/fairlearn/fairlearn/)do Fairlearn, o guia do [utilizador,](https://fairlearn.github.io/main/user_guide/index.html) [exemplos](https://fairlearn.github.io/main/auto_examples/index.html)e [os cadernos de amostras.](https://github.com/fairlearn/fairlearn/tree/master/notebooks)
- Saiba como permitir [a](how-to-machine-learning-fairness-aml.md) avaliação da equidade dos modelos de aprendizagem automática em Azure Machine Learning.
- Consulte os cadernos de amostras para obter [cenários](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) adicionais de avaliação da equidade no Azure Machine Learning. 
