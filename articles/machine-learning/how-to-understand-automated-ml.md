---
title: Compreender resultados automatizados do ML
titleSuffix: Azure Machine Learning
description: Aprenda a visualizar e compreender gráficos e métricas para cada uma das suas corridas automatizadas de aprendizagem automática.
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: c5f12da3606361b504d4581916d9645fa3cd24f0
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129674"
---
# <a name="understand-automated-machine-learning-results"></a>Compreender resultados automatizados de aprendizagem automática
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a ver e a compreender os gráficos e métricas de cada uma das suas corridas automatizadas de aprendizagem automática. 

Saiba mais sobre:
+ [Métricas, gráficos e curvas para modelos de classificação](#classification)
+ [Métricas, gráficos e gráficos para modelos de regressão](#regression)
+ [Interpretação do modelo e importância da característica](#explain-model)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Crie uma experiência para a sua corrida automatizada de machine learning, seja com o SDK, quer no estúdio Azure Machine Learning.

    * Use o SDK para construir um modelo de [classificação](how-to-auto-train-remote.md) ou modelo de [regressão](tutorial-auto-train-models.md)
    * Utilize o [estúdio Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md) para criar um modelo de classificação ou regressão, carregando os dados apropriados.

## <a name="view-the-run"></a>Ver a corrida

Depois de executar uma experiência automatizada de aprendizagem automática de máquinas, um histórico das corridas pode ser encontrado no seu espaço de trabalho de aprendizagem automática. 

1. Vá para a área de trabalho.

1. No painel esquerdo do espaço de trabalho, selecione **Experimentos**.

   ![Captura de ecrã do menu de experimentação](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Na lista de experiências, selecione a que pretende explorar.

   [Lista de experiências ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Na tabela inferior, selecione o **Run**.

   [![experimento executar)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png)

1. Nos Modelos, selecione o **nome Algoritmo** para o modelo que pretende explorar mais.

   [modelo ![Experiment](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Também vê estes mesmos resultados durante uma corrida quando utiliza o [widget `RunDetails`Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification"></a>Resultados da classificação

As métricas e gráficos que se seguem estão disponíveis para todos os modelos de classificação que você constrói usando as capacidades automatizadas de aprendizagem automática de máquinas de aprendizagem automática de Azure Machine Learning

+ [Métricas](#classification-metrics)
+ [Matriz de confusão](#confusion-matrix)
+ [Gráfico de recolha de precisão](#precision-recall-chart)
+ [Características de funcionamento do recetor (ou ROC)](#roc)
+ [Curva de elevador](#lift-curve)
+ [Ganha curva](#gains-curve)
+ [Enredo de calibração](#calibration-plot)

### <a name="classification-metrics"></a>Métricas de classificação

As seguintes métricas são guardadas em cada iteração de execução para uma tarefa de classificação.

Métrica|Descrição|Cálculo|Parâmetros adicionais
--|--|--|--
AUC_Macro| AUC é a área sob a curva de característica do Recetor operacional. Macro é a média aritmética de AUC para cada classe.  | [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | média = "macro"|
AUC_Micro| AUC é a área sob a curva de característica do Recetor operacional. A micro é calculada globalmente combinando os verdadeiros positivos e falsos positivos de cada classe.| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | média = "micro"|
AUC_Weighted  | AUC é a área sob a curva de característica do Recetor operacional. Ponderado é a média aritmética da pontuação para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|média = "ponderado"
accuracy|A precisão é a porcentagem de etiquetas previstas que correspondam exatamente com as etiquetas verdadeiras. |[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Nenhum|
average_precision_score_macro|Precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisions alcançados em cada limite, com o aumento de Lembre-se de que o limiar anterior utilizado como o peso. Macro é a média aritmética da pontuação média de precisão de cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|média = "macro"|
average_precision_score_micro|Precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisions alcançados em cada limite, com o aumento de Lembre-se de que o limiar anterior utilizado como o peso. A micro é calculada globalmente combinando os verdadeiros positivos e falsos positivos em cada corte.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|média = "micro"|
average_precision_score_weighted|Precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisions alcançados em cada limite, com o aumento de Lembre-se de que o limiar anterior utilizado como o peso. Ponderado é a média aritmética da pontuação média de precisão para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|média = "ponderado"|
balanced_accuracy|Precisão equilibrada é a média aritmética de remoção para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "macro"|
f1_score_macro|F1 pontuação é a média harmônica de precisão e lembre-se. Macro é a média aritmética da pontuação de F1 para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|média = "macro"|
f1_score_micro|F1 pontuação é a média harmônica de precisão e lembre-se. A micro é calculada globalmente contando os verdadeiros positivos, falsos negativos e falsos positivos.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|média = "micro"|
f1_score_weighted|F1 pontuação é a média harmônica de precisão e lembre-se. Média ponderada pela frequência de classe de pontuação F1 para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|média = "ponderado"|
log_loss|Esta é a função de perda utilizada na regressão logística (multinomial) e extensões dela, tais como redes neurais, definidas como a probabilidade negativa de log-probabilidade dos verdadeiros rótulos dadas as previsões de um classificador probabilístico. Para uma única amostra com rótulo verdadeiro em {0,1} e probabilidade estimada yp que yt =&#124;1, a perda de tronco é -log P(yt yp) = -(yt log(yp) + (1 - yt) log(1 - yp)).|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Nenhum|
norm_macro_recall|Normalizado Macro Lembre-se de é lembrar de Macro normalizados de modo a que o desempenho aleatório tem uma pontuação igual a 0 e desempenho perfeito tem uma pontuação de 1. Isto é conseguido por norm_macro_recall := (recall_score_macro - R)/1 - R), onde R é o valor esperado de recall_score_macro para previsões aleatórias (isto é, R=0,5 para classificação binária e R=(1/C) para problemas de classificação da classe C).|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "macro" |
precision_score_macro|A precisão é a percentagem de elementos positivamente previstos que estão corretamente rotulados. Macro é a média aritmética de precisão para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média = "macro"|
precision_score_micro|A precisão é a percentagem de elementos positivamente previstos que estão corretamente rotulados. A micro é calculada globalmente contando os verdadeiros positivos e falsos positivos.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média = "micro"|
precision_score_weighted|A precisão é a percentagem de elementos positivamente previstos que estão corretamente rotulados. Ponderado é a média aritmética de precisão para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média = "ponderado"|
recall_score_macro|A recordação é a percentagem de elementos corretamente rotulados de uma determinada classe. Macro é a média aritmética de recordar cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "macro"|
recall_score_micro|A recordação é a percentagem de elementos corretamente rotulados de uma determinada classe. Micro é calculado globalmente contando os verdadeiros positivos, falsos negativos e falsos positivos|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "micro"|
recall_score_weighted|A recordação é a percentagem de elementos corretamente rotulados de uma determinada classe. Ponderado é a média aritmética de recordação para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "ponderado"|
weighted_accuracy|Precisão ponderada é precisão em que o peso dado a cada exemplo é igual à proporção de instâncias verdadeiras na verdadeira classe desse exemplo.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight é um vetor igual a proporção dessa classe para cada elemento no destino|

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>Matriz de confusão

#### <a name="what-is-a-confusion-matrix"></a>O que é uma matriz de confusão?
Uma matriz de confusão é usada para descrever o desempenho de um modelo de classificação. Cada linha apresenta as instâncias da classe verdadeira, ou real no seu conjunto de dados, e cada coluna representa os casos da classe que foi prevista pelo modelo. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>O que o ML automatizado faz com a matriz de confusão?
Para problemas de classificação, o Azure Machine Learning fornece automaticamente uma matriz de confusão para cada modelo que foi criado. Para cada matriz de confusão, ml automatizado mostrará a frequência de cada etiqueta prevista (coluna) em comparação com a verdadeira etiqueta (linha). Quanto mais escura a cor, maior a contagem nessa parte particular da matriz. 

#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
Estamos a comparar o valor real do conjunto de dados com os valores previstos que o modelo deu. Por isso, os modelos de machine learning têm maior precisão se o modelo tiver a maioria dos seus valores ao longo da diagonal, o que significa que o modelo previu o valor correto. Se um modelo tiver desequilíbrio de classe, a matriz de confusão ajudará a detetar um modelo tendencioso.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Exemplo 1: Um modelo de classificação com fraca precisão
![Um modelo de classificação com má precisão](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>Exemplo 2: Um modelo de classificação com alta precisão 
![Um modelo de classificação com alta precisão](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Exemplo 3: Um modelo de classificação com alta precisão e elevado enviesamento nas previsões do modelo
![Um modelo de classificação com alta precisão e elevado enviesamento nas previsões do modelo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Gráfico de recolhimento de precisão
#### <a name="what-is-a-precision-recall-chart"></a>O que é um gráfico de recordação de precisão?
A curva de recordação de precisão mostra a relação entre precisão e recordação de um modelo. O termo precisão representa essa capacidade de um modelo rotular corretamente todas as instâncias. Lembre-se representa a capacidade para um classificador localizar todas as instâncias de uma etiqueta específica.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>O que o ML automatizado faz com o gráfico de recolha de precisão?

Com este gráfico, pode comparar as curvas de recolhimento de precisão para cada modelo determinar qual modelo tem uma relação aceitável entre precisão e lembre-se para o seu problema comercial em particular. Este gráfico mostra o Recolhimento de precisão de média Macro, o Recolhimento de precisão de média Micro e o recolhimento de precisão associado com todas as classes para um modelo. 

A macromédia calculará a métrica independentemente de cada classe e, em seguida, tomará a média, tratando todas as classes de forma igual. No entanto, a micromédia irá agregar as contribuições de todas as classes para calcular a média. A micromédia é preferível se houver desequilíbrio de classe presente no conjunto de dados.

#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
Dependendo do objetivo do problema de negócio, a curva ideal de recolha de precisão pode diferir. Alguns exemplos são dados abaixo

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Exemplo 1: Um modelo de classificação com baixa precisão e baixa recuperação
![Um modelo de classificação com baixa precisão e baixa recuperação](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Exemplo 2: Um modelo de classificação com ~100% de precisão e ~100% de recordação 
![Um modelo de classificação de alta precisão e recorde](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>Gráfico roc

#### <a name="what-is-a-roc-chart"></a>O que é um gráfico roc?
Recetor operacional característica (ou ROC) é um desenho das etiquetas classificadas corretamente vs. as etiquetas classificadas incorretamente para um determinado modelo. A curva cor MULTICLASSE pode ser menos informativa quando os modelos de formação em conjuntos de dados com tendência alto, como ele não mostrará as etiquetas de positivas FALSO.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>O que o ML automatizado faz com o gráfico ROC?
O ML automatizado gera a Recuperação de Precisão Média Macro, a Micro Média precisão-recall e a recolha de precisão associada a todas as classes para um modelo. 

A macromédia calculará a métrica independentemente de cada classe e, em seguida, tomará a média, tratando todas as classes de forma igual. No entanto, a micromédia irá agregar as contribuições de todas as classes para calcular a média. A micromédia é preferível se houver desequilíbrio de classe presente no conjunto de dados.

#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
Idealmente, o modelo terá uma taxa positiva mais próxima de 100% e perto de 0% de taxa positiva falsa. 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Exemplo 1: Um modelo de classificação com rótulos verdadeiros baixos e etiquetas falsas elevadas
![Modelo de classificação com rótulos verdadeiros baixos e etiquetas falsas elevadas](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Exemplo 2: Um modelo de classificação com rótulos verdadeiros elevados e rótulos falsos baixos
![um modelo de classificação com rótulos verdadeiros elevados e rótulos falsos baixos](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Gráfico de elevação
#### <a name="what-is-a-lift-chart"></a>O que é um gráfico de elevador?
Gráficos de comparação de precisão são utilizados para avaliar o desempenho de um modelo de classificação. Mostra o quanto pode esperar fazer com o modelo gerado em comparação com sem um modelo em termos de precisão.
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>O que o ML automatizado faz com a tabela de elevação?
Pode comparar a comparação de precisão do modelo criado automaticamente com o Azure Machine Learning para a linha de base para visualizar o ganho de valor desse modelo específico.
#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>Exemplo 1: Um modelo de classificação que faz pior do que um modelo de seleção aleatório
![Um modelo de classificação que faz pior do que um modelo de seleção aleatório](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Exemplo 2: Um modelo de classificação que tem um melhor desempenho do que um modelo de seleção aleatório
![Um modelo de classificação que tem melhor desempenho](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>Gráfico de ganhos
#### <a name="what-is-a-gains-chart"></a>O que é um gráfico de ganhos?

Um gráfico de ganhos avalia o desempenho de um modelo de classificação por cada parte dos dados. Ela mostra para cada percentil do conjunto de dados, quanto melhor pode esperar efetuar comparado com um modelo de seleção aleatória.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>O que faz o ML automatizado com a tabela de ganhos?
Utilize o gráfico de quadro de ganhos cumulativos para ajudar a escolher o limite classificação usando uma percentagem que corresponde a um ganho de pretendida do modelo. Estas informações proporciona outra forma de ver os resultados num gráfico de comparação de precisão que acompanha este artigo.

#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Exemplo 1: Um modelo de classificação com ganho mínimo
![um modelo de classificação com ganho mínimo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Exemplo 2: Um modelo de classificação com ganhos significativos
![Modelo de classificação A com ganho saem](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Gráfico de calibração

#### <a name="what-is-a-calibration-chart"></a>O que é um gráfico de calibração?
Um desenho de calibragem é utilizado para apresentar a confiança de um modelo preditivo. Fá-lo mostrando a relação entre a probabilidade prevista e a probabilidade real, onde a "probabilidade" representa a probabilidade de uma determinada instância pertencer a algum rótulo.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>O que o ML automatizado faz com o gráfico de calibração?
Para todos os problemas de classificação, pode rever a linha de calibração para cada classe num determinado modelo preditivo, média de macro e micro-média.

A macromédia calculará a métrica independentemente de cada classe e, em seguida, tomará a média, tratando todas as classes de forma igual. No entanto, a micromédia irá agregar as contribuições de todas as classes para calcular a média. 
#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
 Um modelo bem calibrado alinha-se com a linha y=x, onde é razoavelmente confiante nas suas previsões. Um modelo de confident excessiva se alinha com a y = 0 linha, em que a probabilidade prevista está presente, mas não existe nenhuma probabilidade real. 


##### <a name="example-1-a-well-calibrated-model"></a>Exemplo 1: Um modelo bem calibrado
![ modelo mais bem calibrado](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>Exemplo 2: Um modelo demasiado confiante
![Um modelo demasiado confiante](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Resultados da regressão

As métricas e gráficos que se seguem estão disponíveis para todos os modelos de regressão que você constrói usando as capacidades automatizadas de aprendizagem automática de máquinas de aprendizagem automática de Azure Machine Learning

+ [Métricas](#reg-metrics)
+ [Previsto vs. Verdadeiro](#pvt)
+ [Histograma dos residuais](#histo)


### <a name="reg-metrics"></a>Métricas de regressão

As seguintes métricas são guardadas em cada iteração de execução para uma tarefa de regressão ou previsão.

|Métrica|Descrição|Cálculo|Parâmetros adicionais
--|--|--|--|
explained_variance|Variância explicada é a proporção ao qual um modelo matemático contas para a variação de um determinado conjunto de dados. É que a percentagem de diminuir a variação dos dados originais para a variância dos erros. Quando a média dos erros for 0, é igual a variância explicada.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Nenhum|
r2_score|R2 é o coeficiente de determinação ou a redução de percentagem de erros ao quadrado, em comparação comparada um modelo de linha de base que produz a média. |[Cálculo](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Nenhum|
spearman_correlation|Correlação de Spearman é uma medida nonparametric de monotonicidade da relação entre dois conjuntos de dados. Ao contrário de correlação Pearson, a correlação de Spearman não partem do princípio de que ambos os conjuntos de dados normalmente são distribuídos. Como outros coeficientes de correlação, isso varia entre -1 e + 1 com 0 indicando sem correlação. Correlações de -1 ou + 1 implicam uma relação de monotónica exata. Correlações positivas implicam que como x aumenta, assim como o y. Correlações negativas implicam que como x aumenta, y diminui.|[Cálculo](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Nenhum|
mean_absolute_error|Significa erro absoluto é o valor esperado de valor absoluto de diferença entre o destino e a predição|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Nenhum|
normalized_mean_absolute_error|Erro de absoluto mean normalizado é mean erro absoluto dividida pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Divida por intervalo dos dados|
median_absolute_error|Erro de absoluto mediano é o valor mediano de todas as diferenças absolutas entre o destino e a predição. Esta perda é robusta para valores atípicos.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Nenhum|
normalized_median_absolute_error|Erro de absoluto mediano normalizado é mediano erro absoluto dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Divida por intervalo dos dados|
root_mean_squared_error|Raiz significar um erro ao quadrado é a raiz quadrada da diferença ao quadrado esperada entre o destino e a predição|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Nenhum|
normalized_root_mean_squared_error|Raiz normalizado significar um erro ao quadrado é raiz mean erros dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Divida por intervalo dos dados|
root_mean_squared_log_error|Raiz significa de erro do registo ao quadrado é a raiz quadrada do erro logarítmica ao quadrado esperado|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Nenhum|
normalized_root_mean_squared_log_error|Erro de registo quadrado da raiz normalizado é erro de registo quadrado médio raiz dividido pela gama dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Divida por intervalo dos dados|

### <a name="pvt"></a>Gráfico previsto vs. gráfico verdadeiro
#### <a name="what-is-a-predicted-vs-true-chart"></a>O que é um gráfico previsto vs. verdadeiro?
Previsível vs. True mostra a relação entre um valor previsto e o seu valor real correlacionado para um problema de regressão. Este gráfico pode ser utilizado para medir o desempenho de um modelo como o mais perto de y = x linha os valores previstos são, melhor será a precisão de um modelo preditivo.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>O que o ML automatizado faz com o gráfico Previsto vs. True?
Após cada execução, pode ver um previstos versus verdadeiro gráfico para cada modelo de regressão. Para proteger a privacidade dos dados, os valores são posicionados em conjunto e o tamanho de cada contentor é mostrado como um gráfico de barras na parte inferior da área do gráfico. Pode comparar o modelo de previsão, com a área de tom mais clara, que mostra de margens de erro, com o valor ideal de onde o modelo deve ser.

#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>Exemplo 1: Um modelo de classificação com baixa precisão
![Um modelo de regressão com baixa precisão nas previsões](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>Exemplo 2: Um modelo de regressão com alta precisão 
[![Um modelo de regressão com alta precisão nas suas previsões](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Histograma da tabela de residuais
#### <a name="what-is-a-residuals-chart"></a>O que é um gráfico de residuais?
Um residuais representa um y observado – o y previsto. Para mostrar uma margem de erro com tendência baixa, o histograma de residuals deve ser formatado como uma curva de sino centrada em torno de 0. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>O que o ML automatizado faz com o gráfico de residuais?
O ML automatizado fornece automaticamente um gráfico residual para mostrar a distribuição de erros nas previsões.
#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
Um bom modelo normalmente terá uma curva do sino ou erros em torno de zero.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Exemplo 1: Um modelo de regressão com preconceito nos seus erros
![Modelo de regressão da SA com preconceito nos seus erros](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Exemplo 2: Um modelo de regressão com distribuição mais uniforme de erros
![Um modelo de regressão com distribuição mais uniforme de erros](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>Interpretação do modelo e importância da característica
Ml automatizado fornece um painel de interpretabilidade de aprendizagem automática para as suas corridas.
Para obter mais informações sobre a possibilidade de funcionalidades de interpretação, consulte o [como](how-to-machine-learning-interpretability-automl.md) permitir a interpretação em experiências automatizadas de ML.

## <a name="next-steps"></a>Passos seguintes

+ Saiba mais sobre [ml automatizado](concept-automated-ml.md) em Azure Machine Learning.
+ Experimente os cadernos de amostras de modelo de [aprendizagem automática](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) de máquinas.
