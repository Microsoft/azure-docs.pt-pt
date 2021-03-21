---
title: Evite a sobremontagem & dados desequilibrados com o AutoML
titleSuffix: Azure Machine Learning
description: Identificar e gerir armadilhas comuns de modelos ML com as soluções automatizadas de aprendizagem automática de máquinas da Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/09/2020
ms.openlocfilehash: ba85bda1e322d3efd467527b48bd4cd90eb7ce8c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96922623"
---
# <a name="prevent-overfitting-and-imbalanced-data-with-automated-machine-learning"></a>Prevenir a sobremontagem e os dados desequilibrados com a aprendizagem automática de máquinas

Dados sobreajustes e desequilibrados são armadilhas comuns quando se constroem modelos de aprendizagem automática. Por padrão, a aprendizagem automática de máquinas da Azure Machine Learning fornece gráficos e métricas para ajudá-lo a identificar estes riscos, e implementa as melhores práticas para ajudar a mitigar. 

## <a name="identify-over-fitting"></a>Identificar excesso de adaptação

A adaptação excessiva na aprendizagem automática ocorre quando um modelo se encaixa demasiado nos dados de treino e, como resultado, não pode prever com precisão dados de teste invisíveis. Por outras palavras, o modelo simplesmente memorizou padrões específicos e ruído nos dados de treino, mas não é suficientemente flexível para fazer previsões sobre dados reais.

Considere os seguintes modelos treinados e as respetivas precisões de comboio e teste.

| Modelação | Precisão do comboio | Precisão do teste |
|-------|----------------|---------------|
| A | 99,9% | 95% |
| B | 87% | 87% |
| C | 99,9% | 45% |

Considerando o modelo **A,** existe um equívoco comum de que se a precisão do teste em dados invisíveis for inferior à precisão de treino, o modelo está sobre-equipado. No entanto, a precisão do teste deve ser sempre inferior à precisão do treino, e a distinção por excesso de ajuste vs. adequadamente se resume ao *quão* menos preciso. 

Ao comparar os modelos **A** e **B**, o modelo **A** é um modelo melhor porque tem uma maior precisão de teste, e embora a precisão do teste seja ligeiramente inferior a 95%, não é uma diferença significativa que sugira que o excesso de adaptação está presente. Não escolheria o modelo **B** simplesmente porque as precisões do comboio e dos testes estão mais próximas.

O modelo **C** representa um caso claro de sobreajuste; a precisão do treino é muito alta, mas a precisão do teste não está nem perto tão alta. Esta distinção é subjetiva, mas vem do conhecimento do seu problema e dados, e que magnitudes de erro são aceitáveis.

## <a name="prevent-over-fitting"></a>Evitar a sobreajuste

Nos casos mais flagrantes, um modelo sobre-equipado assumirá que as combinações de valor de recurso observadas durante o treino resultarão sempre na mesma saída para o alvo.

A melhor forma de evitar o excesso de adaptação é seguir as melhores práticas do ML, incluindo:

* Usando mais dados de formação e eliminando o enviesamento estatístico
* Prevenção de fugas de alvo
* Usando menos funcionalidades
* **Regularização e otimização do hiperparímetro**
* **Limitações de complexidade do modelo**
* **Validação cruzada**

No contexto do ML automatizado, os três primeiros itens acima são **as melhores práticas que implementa.** Os três últimos itens arrojados são **as aplicações automatizadas de ML** de boas práticas por defeito para proteger contra o excesso de adaptação. Em configurações que não sejam ml automatizadas, todas as seis melhores práticas valem a pena seguir para evitar modelos de adaptação excessiva.

### <a name="best-practices-you-implement"></a>As melhores práticas que implementa

A utilização de **mais dados** é a forma mais simples e melhor possível de prevenir o excesso de adaptação, e como um bónus adicional normalmente aumenta a precisão. Quando utiliza mais dados, torna-se mais difícil para o modelo memorizar padrões exatos, e é forçado a chegar a soluções mais flexíveis para acomodar mais condições. Também é importante reconhecer **o enviesamento estatístico,** para garantir que os seus dados de treino não incluem padrões isolados que não existirão em dados de previsão ao vivo. Este cenário pode ser difícil de resolver, porque pode não haver um excesso de adaptação entre o seu comboio e os conjuntos de testes, mas pode haver um presente excessivo quando comparado com os dados de testes ao vivo.

**A fuga de** alvo é uma questão semelhante, em que pode não ver uma sobreajustamento entre conjuntos de comboio/teste, mas aparece na hora da previsão. A fuga de alvo ocorre quando o seu modelo "engana" durante o treino, tendo acesso a dados que normalmente não deveria ter na hora da previsão. Por exemplo, se o seu problema for prever na segunda-feira qual será o preço das matérias-primas na sexta-feira, mas uma das suas funcionalidades acidentalmente incluiu dados a partir de quinta-feira, esses serão dados que o modelo não terá na hora da previsão, uma vez que não pode ver o futuro. A fuga de alvos é um erro fácil de não perder, mas é muitas vezes caracterizada por uma precisão anormalmente alta para o seu problema. Se estiver a tentar prever o preço das ações e tiver treinado um modelo com uma precisão de 95%, é provável que haja fugas de alvo algures nas suas funcionalidades.

**A remoção de funcionalidades** também pode ajudar na adaptação excessiva, evitando que o modelo tenha demasiados campos para usar para memorizar padrões específicos, fazendo com que seja mais flexível. Pode ser difícil de medir quantitativamente, mas se conseguir remover funcionalidades e manter a mesma precisão, provavelmente tornou o modelo mais flexível e reduziu o risco de excesso de adaptação.

### <a name="best-practices-automated-ml-implements"></a>Aplicações automatizadas de ML de boas práticas

**A regularização** é o processo de minimizar uma função de custo para penalizar modelos complexos e sobre-equipados. Existem diferentes tipos de funções de regularização, mas em geral todos eles penalizam o tamanho do coeficiente de modelo, a variação e a complexidade. O ML automatizado utiliza L1 (Lasso), L2 (Ridge) e ElasticNet (L1 e L2 simultaneamente) em diferentes combinações com diferentes configurações de hiperparímetro de modelo que controlam o excesso de adaptação. Em termos simples, o ML automatizado variará quanto um modelo é regulado e escolherá o melhor resultado.

O ML automatizado também implementa **limitações explícitas** de complexidade do modelo para evitar a sobreajuste. Na maioria dos casos esta implementação é especificamente para algoritmos de decisão árvore ou floresta, onde a profundidade máxima individual da árvore é limitada, e o número total de árvores usadas em técnicas de floresta ou conjunto são limitadas.

**A validação cruzada (CV)** é o processo de tomada de muitos subconjuntos dos seus dados completos de treino e formação de um modelo em cada subconjunto. A ideia é que um modelo possa ter "sorte" e ter uma grande precisão com um subconjunto, mas ao utilizar muitos subconjuntos o modelo não vai conseguir sempre esta elevada precisão. Ao fazer CV, fornece um conjunto de dados de retenção de validação, especifica as suas dobras CV (número de subconjuntos) e o ML automatizado treinará o seu modelo e afinará hiperparmetros para minimizar o erro no seu conjunto de validação. Uma dobra cv pode estar em excesso, mas ao usar muitos deles reduz a probabilidade de o seu modelo final estar demasiado ajustado. A contrapartida é que o CV resulta em tempos de treino mais longos e, portanto, num custo maior, porque em vez de treinar um modelo uma vez, treina-se uma vez para cada subconjunto de *CV.* 

> [!NOTE]
> A validação cruzada não é ativada por defeito; deve ser configurado em definições automáticas de ML. No entanto, após a validação cruzada ser configurada e ter sido fornecido um conjunto de dados de validação, o processo é automatizado para si. Saiba mais sobre [a configuração de validação cruzada em Auto ML](how-to-configure-cross-validation-data-splits.md)

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>Identificar modelos com dados desequilibrados

Os dados desequilibrados são geralmente encontrados em dados para cenários de classificação de machine learning, e referem-se a dados que contêm uma relação desproporcional de observações em cada classe. Este desequilíbrio pode levar a um efeito positivo falsamente percebido da precisão de um modelo, porque os dados de entrada têm tendência para uma classe, o que resulta no modelo treinado para imitar esse enviesamento. 

Além disso, as correções automáticas de ML geram automaticamente os seguintes gráficos, o que pode ajudá-lo a compreender a correção das classificações do seu modelo, e identificar modelos potencialmente impactados por dados desequilibrados.

Gráfico| Description
---|---
[Matriz de confusão](how-to-understand-automated-ml.md#confusion-matrix)| Avalia as etiquetas corretamente classificadas com as etiquetas reais dos dados. 
[Recuperação de precisão](how-to-understand-automated-ml.md#precision-recall-curve)| Avalia a relação entre as etiquetas corretas com a relação entre as instâncias de etiqueta encontradas dos dados 
[Curvas ROC](how-to-understand-automated-ml.md#roc-curve)| Avalia a relação entre as etiquetas corretas e a relação entre rótulos falsos positivos.

## <a name="handle-imbalanced-data"></a>Lidar com dados desequilibrados 

Como parte do seu objetivo de simplificar o fluxo de trabalho de machine learning, **a ML automatizada construiu capacidades** para ajudar a lidar com dados desequilibrados tais como, 

- Uma coluna de **peso**: ML automatizado suporta uma coluna de pesos como entrada, fazendo com que as linhas nos dados sejam ponderadas para cima ou para baixo, o que pode ser usado para tornar uma classe mais ou menos "importante".

- Os algoritmos utilizados pelo ML automatizado detetam desequilíbrio quando o número de amostras na classe minoritária é igual ou inferior a 20% do número de amostras na classe maioritária, onde a classe minoritária se refere à que tem menos amostras e a classe maioritária refere-se à que tem mais amostras. Posteriormente, a AutoML realizará uma experiência com dados sub-amostrados para verificar se a utilização de pesos de classe resolveria este problema e melhoraria o desempenho. Se verificar um melhor desempenho através desta experiência, então este remédio é aplicado.

- Use uma métrica de desempenho que lida melhor com dados desequilibrados. Por exemplo, o AUC_weighted é uma métrica primária que calcula a contribuição de cada classe com base no número relativo de amostras que representam essa classe, portanto é mais robusto contra o desequilíbrio.

As seguintes técnicas são opções adicionais para lidar com dados desequilibrados **fora do ML automatizado**. 

- Resampling até mesmo o desequilíbrio de classe, quer através da amostragem das classes mais pequenas, quer diminuindo a amostragem das classes maiores. Estes métodos requerem conhecimentos especializados para processar e analisar.

- Rever métricas de desempenho para dados desequilibrados. Por exemplo, a pontuação F1 é a média harmónica de precisão e recordação. A precisão mede a exatidão de um classificador, onde uma maior precisão indica menos falsos positivos, enquanto a recuperação mede a completude de um classificador, onde uma recolha mais elevada indica menos falsos negativos.

## <a name="next-steps"></a>Passos seguintes

Veja exemplos e aprenda a construir modelos utilizando machine learning automatizado:

+ Siga o [Tutorial: Treine automaticamente um modelo de regressão com Azure Machine Learning](tutorial-auto-train-models.md)

+ Configure as definições para a experiência de formação automática:
  + No estúdio Azure Machine Learning, [utilize estes passos.](how-to-use-automated-ml-for-ml-models.md)
  + Com o Python SDK, [use estes passos.](how-to-configure-auto-train.md)


