---
title: Gerencie armadilhas de modeloml comuns com aprendizagem automática de máquinas.
titleSuffix: Azure Machine Learning
description: Identifique e gerencie armadilhas comuns de modelos ML com as soluções automatizadas de machine learning da Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 03/27/2020
ms.openlocfilehash: e0bc1aa48dfb40ea146fa79fdfd57da841ca1404
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385535"
---
# <a name="manage-ml-pitfalls-with-automated-machine-learning"></a>Gerir armadilhas ml com machine learning automatizado

Dados exagerados e desequilibrados são armadilhas comuns quando se constroem modelos de aprendizagem automática. Por padrão, a aprendizagem automática de máquinas da Azure Machine Learning fornece gráficos e métricas para ajudá-lo a identificar estes riscos, e implementa as melhores práticas para ajudá-los a mitigar. 

## <a name="identify-over-fitting"></a>Identificar excesso de encaixe

O excesso de adaptação na aprendizagem automática ocorre quando um modelo se encaixa demasiado bem nos dados de treino, e como resultado não pode prever com precisão em dados de teste invisíveis. Por outras palavras, o modelo simplesmente memorizou padrões específicos e ruído nos dados de treino, mas não é suficientemente flexível para fazer previsões sobre dados reais.

Considere os seguintes modelos treinados e as respetivas precisões de comboio e teste.

| Modelo | Precisão do comboio | Precisão do teste |
|-------|----------------|---------------|
| A | 99,9% | 95% |
| B | 87% | 87% |
| C | 99,9% | 45% |

Considerando o modelo **A,** existe um equívoco comum de que se a precisão do teste em dados invisíveis for inferior à precisão do treino, o modelo está sobre-montado. No entanto, a precisão do teste deve ser sempre menor do que a precisão do treino, e a distinção para o excesso de ajuste vs. adequadamente ajustado resume-se a *quão* menos precisa. 

Ao comparar os modelos **A** e **B,** o modelo **A** é um modelo melhor porque tem uma maior precisão de teste, e embora a precisão do teste seja ligeiramente mais baixa em 95%, não é uma diferença significativa que sugira que o excesso de adaptação está presente. Não escolheria o modelo **B** simplesmente porque as precisões do comboio e do teste estão mais próximas.

O modelo **C** representa um caso claro de excesso de adaptação; a precisão do treino é muito alta, mas a precisão do teste não é tão alta. Esta distinção é subjetiva, mas vem do conhecimento do seu problema e dos seus dados, e que magnitudes de erro são aceitáveis.

## <a name="prevent-over-fitting"></a>Evitar excesso de adaptação

Nos casos mais flagrantes, um modelo sobre-equipado assumirá que as combinações de valor da funcionalidade observadas durante o treino resultarão sempre na mesma saída para o alvo.

A melhor maneira de evitar o excesso de adaptação é seguir as melhores práticas ml, incluindo:

* Usando mais dados de formação e eliminando o enviesamento estatístico
* Prevenção de fugas de alvo
* Usando menos funcionalidades
* **Regularização e otimização de hiperparâmetros**
* **Limitações de complexidade do modelo**
* **Validação cruzada**

No contexto do ML automatizado, os três primeiros itens acima são **as melhores práticas que implementa.** Os três últimos itens arrojados são **as melhores práticas implementadas ml automatizadas** por padrão para proteger contra o excesso de encaixe. Em definições que não sejam ML automatizadas, todas as seis melhores práticas valem a pena seguir para evitar modelos demasiado adequados.

### <a name="best-practices-you-implement"></a>As melhores práticas que implementa

Usar **mais dados** é a forma mais simples e possível de prevenir o excesso de adaptação, e como um bónus adicionado normalmente aumenta a precisão. Quando se utiliza mais dados, torna-se mais difícil para o modelo memorizar padrões exatos, e é forçado a alcançar soluções mais flexíveis para acomodar mais condições. Também é importante reconhecer **o enviesamento estatístico,** para garantir que os seus dados de formação não incluem padrões isolados que não existirão nos dados de previsão ao vivo. Este cenário pode ser difícil de resolver, porque pode não haver excesso de adaptação entre o seu comboio e os conjuntos de teste, mas pode haver um presente demasiado adequado quando comparado com os dados de teste ao vivo.

A fuga de alvos é um problema semelhante, onde pode não ver o excesso de adaptação entre os conjuntos de comboios/testes, mas aparece na hora da previsão. A fuga de destino ocorre quando o seu modelo "faz batota" durante o treino, tendo acesso a dados que normalmente não deveria ter no momento da previsão. Por exemplo, se o seu problema é prever na segunda-feira qual será o preço da mercadoria na sexta-feira, mas uma das suas características incluiu acidentalmente dados a partir das quintas-feiras, que seriam dados que o modelo não terá na hora da previsão, uma vez que não pode ver o futuro. A fuga de alvo é um erro fácil de perder, mas é frequentemente caracterizada por uma precisão anormalmente elevada para o seu problema. Se está a tentar prever o preço das ações e treinou um modelo com uma precisão de 95%, é provável que haja fugas de destino algures nas suas características.

A remoção de funcionalidades também pode ajudar na sobremontagem, impedindo que o modelo tenha demasiados campos para memorizar padrões específicos, fazendo com que seja mais flexível. Pode ser difícil medir quantitativamente, mas se conseguir remover as funcionalidades e reter a mesma precisão, provavelmente tornou o modelo mais flexível e reduziu o risco de excesso de adaptação.

### <a name="best-practices-automated-ml-implements"></a>Implementações automáticas de ML de boas práticas

Regularização é o processo de minimizar uma função de custo para penalizar modelos complexos e sobre-instalados. Existem diferentes tipos de funções de regularização, mas em geral todos penalizam o tamanho, variação e complexidade do modelo. Ml automatizado usa L1 (Lasso), L2 (Ridge) e ElasticNet (L1 e L2 simultaneamente) em diferentes combinações com diferentes configurações de hiperparâmetros de modelo que controlam o excesso de encaixe. Em termos simples, ml automatizado variará quanto um modelo é regulado e escolherá o melhor resultado.

O ML automatizado também implementa limitações explícitas de complexidade do modelo para evitar o excesso de adaptação. Na maioria dos casos, esta implementação é especificamente para algoritmos de árvores de decisão ou floresta, onde a profundidade máxima individual das árvores é limitada, e o número total de árvores usadas em técnicas florestais ou de conjunto são limitados.

A validação cruzada (CV) é o processo de tomar muitos subconjuntos dos seus dados de treino completos e treinar um modelo em cada subconjunto. A ideia é que um modelo possa ter "sorte" e ter uma grande precisão com um subconjunto, mas ao usar muitos subconjuntos o modelo não conseguirá sempre esta alta precisão. Ao fazer CV, fornece um conjunto de dados de validação, especifique as suas dobras cv (número de subconjuntos) e ml automatizado treinará o seu modelo e afinará hiperparâmetros para minimizar o erro no seu conjunto de validação. Uma dobra de CV pode ser demasiado ajustada, mas ao usar muitos deles reduz a probabilidade de o seu modelo final estar demasiado apto. A contrapartida é que o CV resulta em tempos de treino mais longos e, portanto, maior custo, porque em vez de treinar um modelo uma vez, treina-o uma vez para *cada* subconjunto de CV. 

> [!NOTE]
> A validação cruzada não é ativada por defeito; deve ser configurado em definições automáticas de ML. No entanto, após a validação cruzada ser configurada e um conjunto de dados de validação ter sido fornecido, o processo é automatizado para si. Consulte 

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>Identificar modelos com dados desequilibrados

Os dados desequilibrados são geralmente encontrados em dados para cenários de classificação de aprendizagem automática, e refere-se a dados que contêm uma relação desproporcional de observações em cada classe. Este desequilíbrio pode levar a um efeito positivo falsamente percebido da precisão de um modelo, porque os dados de entrada têm tendência para uma classe, o que resulta no modelo treinado para imitar esse enviesamento. 

Como os algoritmos de classificação são geralmente avaliados pela precisão, verificar a pontuação de precisão de um modelo é uma boa maneira de identificar se foi impactado por dados desequilibrados. Tinha uma precisão muito alta ou uma precisão muito baixa para certas classes?

Além disso, as execuções automáticas de ML geram automaticamente os seguintes gráficos, o que pode ajudá-lo a entender a correção das classificações do seu modelo, e identificar modelos potencialmente impactados por dados desequilibrados.

Gráfico| Descrição
---|---
[Matriz de confusão](how-to-understand-automated-ml.md#confusion-matrix)| Avalia as etiquetas corretamente classificadas com os rótulos reais dos dados. 
[Recolha de precisão](how-to-understand-automated-ml.md#precision-recall-chart)| Avalia a relação dos rótulos corretos com o rácio de instâncias de etiquetas encontradas dos dados 
[Curvas ROC](how-to-understand-automated-ml.md#roc)| Avalia a relação dos rótulos corretos com o rácio de etiquetas falsas positivas.

## <a name="handle-imbalanced-data"></a>Lidar com dados desequilibrados 

Como parte do seu objetivo de simplificar o fluxo de trabalho de aprendizagem automática, o ML automatizado construiu capacidades para ajudar a lidar com dados desequilibrados como, tais como, 

- Uma coluna de **peso**: ml automatizado suporta uma coluna ponderada como entrada, fazendo com que as linhas nos dados sejam ponderadas para cima ou para baixo, o que pode tornar uma classe mais ou menos "importante".

- Os algoritmos utilizados por ML automatizado podem lidar adequadamente com o desequilíbrio de até 20:1, o que significa que a classe mais comum pode ter 20 vezes mais linhas nos dados do que a classe menos comum.

As seguintes técnicas são opções adicionais para lidar com dados desequilibrados fora do ML automatizado. 

- Reprovando até mesmo o desequilíbrio de classes, quer através da amostragem das classes mais pequenas, quer da amostragem das classes maiores. Estes métodos requerem conhecimentos especializados para processar e analisar.

- Use uma métrica de desempenho que lida melhor com dados desequilibrados. Por exemplo, a pontuação de F1 é uma média ponderada de precisão e recordação. A precisão mede a exacticidade de um classificador, a baixa precisão indica um elevado número de falsos positivos--, enquanto a recordação mede a completude de um classificador- a baixa recordação indica um elevado número de falsos negativos. 

## <a name="next-steps"></a>Passos seguintes

Veja exemplos e aprenda a construir modelos usando machine learning automatizado:

+ Siga o [Tutorial: Treine automaticamente um modelo de regressão com Azure Machine Learning](tutorial-auto-train-models.md)

+ Configure as definições para a experiência de treino automático:
  + No estúdio Azure Machine Learning, [utilize estes passos.](how-to-use-automated-ml-for-ml-models.md)
  + Com o Python SDK, [use estes passos.](how-to-configure-auto-train.md)


