---
title: Métricas de precisão de treinamento em ML automatizado
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre as métricas de precisão de aprendizado de máquina automatizadas para cada uma de suas execuções.
author: j-martens
ms.author: jmartens
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/16/2019
ms.openlocfilehash: dc147fd0252b2b5ec4ce334d6c1c464d9cde8ef5
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297900"
---
# <a name="evaluate-training-accuracy-in-automated-ml-with-metrics"></a>Avaliar a precisão de treinamento em ML automatizado com métricas

Neste artigo, você aprenderá sobre as diferentes métricas disponíveis para modelos de ml automatizados no Azure Machine Learning. 

Há várias maneiras de exibir as métricas de precisão de treinamento para cada iteração de execução.
* Usar [um widget Jupyter](how-to-track-experiments.md#view-run-details)
* Usar [a `get_metrics()` função](how-to-track-experiments.md#query-run-metrics) em qualquer `Run` objeto
* Exibir [as métricas de experimento no portal do Azure](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal)

## <a name="prerequisites"></a>Pré-requisitos
 
* Uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do serviço de Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.
 
* Crie um experimento de Machine Learning automatizado, seja com o SDK ou na portal do Azure.
 
    * Usar o SDK para criar um modelo de [classificação](how-to-auto-train-remote.md) ou um [modelo](tutorial-auto-train-models.md) de regressão
    * Use o [portal do Azure](how-to-create-portal-experiments.md) para criar um modelo de classificação ou regressão carregando os dados apropriados.

## <a name="classification-metrics"></a>Métricas de classificação

As métricas a seguir são salvas em cada iteração de execução para uma tarefa de classificação.

|Métrica|Descrição|Cálculo|Parâmetros adicionais
--|--|--|--|
AUC_Macro| AUC é a área sob a curva de característica do Recetor operacional. Macro é a média aritmética de AUC para cada classe.  | [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | média = "macro"|
AUC_Micro| AUC é a área sob a curva de característica do Recetor operacional. O micro é calculado globalmente combinando os verdadeiros positivos e falsos positivos de cada classe| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | média = "micro"|
AUC_Weighted  | AUC é a área sob a curva de característica do Recetor operacional. Ponderada é a média aritmética de classificação para cada classe, ponderada pelo número de instâncias verdadeiros em cada classe| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|média = "ponderado"
accuracy|A precisão é a porcentagem de etiquetas previstas que correspondam exatamente com as etiquetas verdadeiras. |[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Nenhuma|
average_precision_score_macro|Precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisions alcançados em cada limite, com o aumento de Lembre-se de que o limiar anterior utilizado como o peso. Macro é a média aritmética da pontuação média de precisão de cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|média = "macro"|
average_precision_score_micro|Precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisions alcançados em cada limite, com o aumento de Lembre-se de que o limiar anterior utilizado como o peso. Micro é computada globalmente com o combinam os positivos verdadeiros e falsos positivos em cada limite|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|média = "micro"|
average_precision_score_weighted|Precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisions alcançados em cada limite, com o aumento de Lembre-se de que o limiar anterior utilizado como o peso. Ponderada é a média aritmética da pontuação média de precisão para cada classe, ponderada pelo número de instâncias verdadeiros em cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|média = "ponderado"|
balanced_accuracy|Precisão equilibrada é a média aritmética de remoção para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "macro"|
f1_score_macro|F1 pontuação é a média harmônica de precisão e lembre-se. Macro é a média aritmética de pontuação F1 para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|média = "macro"|
f1_score_micro|F1 pontuação é a média harmônica de precisão e lembre-se. Micro é computada globalmente por contagem de total positivos verdadeiros, falsos negativos e falsos positivos|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|média = "micro"|
f1_score_weighted|F1 pontuação é a média harmônica de precisão e lembre-se. Média ponderada pela frequência de classe de pontuação F1 para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|média = "ponderado"|
log_loss|Esta é a função de perda utilizada regressão logística de (multinomial) e extensões do mesmo como redes neurais, definidas como a probabilidade de registo de negativa das etiquetas verdadeiras tendo em conta as previsões de um classificador probabilístico. Para obter um exemplo único com true Etiquetar yt na {0,1} e estimado probabilidade yp esse yt = 1, a perda de log é – P de registo (yt&#124;yp) =-(yt log(yp) + (1 - yt) registo (1 - yp))|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Nenhuma|
norm_macro_recall|Normalizado Macro Lembre-se de é lembrar de Macro normalizados de modo a que o desempenho aleatório tem uma pontuação igual a 0 e desempenho perfeito tem uma pontuação de 1. Isto é conseguido ao norm_macro_recall: = (recall_score_macro - R) /(1-R), em que o R é o valor esperado de recall_score_macro para predições aleatórias (ou seja, R = 0,5 para classificação binária) e R=(1/C) para problemas de classificação de classe C|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "macro" e, em seguida, (recall_score_macro - R) /(1-R), em que o R é o valor esperado de recall_score_macro para predições aleatórias (ou seja, R = 0,5 para classificação binária) e R=(1/C) para problemas de classificação de classe C|
precision_score_macro|Precisão é a percentagem de elementos identificados como uma determinada classe, que, na verdade, estão nessa classe. Macro é a média aritmética de precisão para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média = "macro"|
precision_score_micro|Precisão é a percentagem de elementos identificados como uma determinada classe, que, na verdade, estão nessa classe. Micro é computada globalmente por contagem de total positivos verdadeiros e falsos positivos|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média = "micro"|
precision_score_weighted|Precisão é a percentagem de elementos identificados como uma determinada classe, que, na verdade, estão nessa classe. Ponderada é a média aritmética de precisão para cada classe, ponderado por número de instâncias verdadeiros em cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média = "ponderado"|
recall_score_macro|Lembre-se é a percentagem de elementos, na verdade, numa determinada classe, que estão identificadas corretamente. Macro é a média aritmética de remoção para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "macro"|
recall_score_micro|Lembre-se é a percentagem de elementos, na verdade, numa determinada classe, que estão identificadas corretamente. Micro é computada globalmente por contagem de total positivos verdadeiros, falsos negativos|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "micro"|
recall_score_weighted|Lembre-se é a percentagem de elementos, na verdade, numa determinada classe, que estão identificadas corretamente. Ponderada é a média aritmética de remoção para cada classe, ponderado por número de instâncias verdadeiros em cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "ponderado"|
weighted_accuracy|Precisão ponderada é precisão em que o peso para cada exemplo é igual a proporção de instâncias verdadeiras na classe de verdadeiro esse exemplo|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight é um vetor igual a proporção dessa classe para cada elemento no destino|

## <a name="regression-and-forecasting-metrics"></a>Regressão e métricas de previsão

As métricas a seguir são salvas em cada iteração de execução para uma tarefa de regressão ou de previsão.

|Métrica|Descrição|Cálculo|Parâmetros adicionais
--|--|--|--|
explained_variance|Variância explicada é a proporção ao qual um modelo matemático contas para a variação de um determinado conjunto de dados. É que a percentagem de diminuir a variação dos dados originais para a variância dos erros. Quando a média dos erros for 0, é igual a variância explicada.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Nenhuma|
r2_score|R2 é o coeficiente de determinação ou a redução de percentagem de erros ao quadrado, em comparação comparada um modelo de linha de base que produz a média. Quando a média dos erros for 0, é igual a variância explicada.|[Cálculo](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Nenhuma|
spearman_correlation|Correlação de Spearman é uma medida nonparametric de monotonicidade da relação entre dois conjuntos de dados. Ao contrário de correlação Pearson, a correlação de Spearman não partem do princípio de que ambos os conjuntos de dados normalmente são distribuídos. Como outros coeficientes de correlação, isso varia entre -1 e + 1 com 0 indicando sem correlação. Correlações de -1 ou + 1 implicam uma relação de monotónica exata. Correlações positivas implicam que como x aumenta, assim como o y. Correlações negativas implicam que como x aumenta, y diminui.|[Cálculo](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Nenhuma|
mean_absolute_error|Significa erro absoluto é o valor esperado de valor absoluto de diferença entre o destino e a predição|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Nenhuma|
normalized_mean_absolute_error|Erro de absoluto mean normalizado é mean erro absoluto dividida pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Divida por intervalo dos dados|
median_absolute_error|Erro de absoluto mediano é o valor mediano de todas as diferenças absolutas entre o destino e a predição. Esta perda é robusta para valores atípicos.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Nenhuma|
normalized_median_absolute_error|Erro de absoluto mediano normalizado é mediano erro absoluto dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Divida por intervalo dos dados|
root_mean_squared_error|Raiz significar um erro ao quadrado é a raiz quadrada da diferença ao quadrado esperada entre o destino e a predição|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Nenhuma|
normalized_root_mean_squared_error|Raiz normalizado significar um erro ao quadrado é raiz mean erros dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Divida por intervalo dos dados|
root_mean_squared_log_error|Raiz significa de erro do registo ao quadrado é a raiz quadrada do erro logarítmica ao quadrado esperado|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Nenhuma|
normalized_root_mean_squared_log_error|O erro de log quadrado de raiz normalizada é o erro de log de quadrado médio raiz dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Divida por intervalo dos dados|

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [ml automatizado](concept-automated-ml.md) no Azure Machine Learning.