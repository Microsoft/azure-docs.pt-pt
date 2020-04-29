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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79283462"
---
# <a name="understand-automated-machine-learning-results"></a>Compreender os resultados de machine learning automatizado
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a ver e a compreender os gráficos e métricas de cada uma das suas corridas automatizadas de aprendizagem automática. 

Saiba mais sobre:
+ [Métricas, gráficos e curvas para modelos de classificação](#classification)
+ [Métricas, gráficos e gráficos para modelos de regressão](#regression)
+ [Interpretação do modelo e importância da característica](#explain-model)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Crie uma experiência para a sua corrida automatizada de machine learning, seja com o SDK, quer no estúdio Azure Machine Learning.

    * Use o SDK para construir um modelo de [classificação](how-to-auto-train-remote.md) ou modelo de [regressão](tutorial-auto-train-models.md)
    * Utilize o [estúdio Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md) para criar um modelo de classificação ou regressão, carregando os dados apropriados.

## <a name="view-the-run"></a>Ver a corrida

Depois de executar uma experiência automatizada de aprendizagem automática de máquinas, um histórico das corridas pode ser encontrado no seu espaço de trabalho de aprendizagem automática. 

1. Vá para a área de trabalho.

1. No painel esquerdo do espaço de trabalho, selecione **Experimentos**.

   ![Screenshot do menu de experiências](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Na lista de experiências, selecione a que pretende explorar.

   [![Lista de experiências](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Na tabela inferior, selecione o **Run**.

   Execução de experiências) [ ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png)

1. Nos Modelos, selecione o **nome Algoritmo** para o modelo que pretende explorar mais.

   [![Modelo de experimentação](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Também vê estes mesmos resultados durante `RunDetails`uma corrida quando utiliza o [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification-results"></a><a name="classification"></a>Resultados da classificação

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

Métrica|Descrição|Cálculo|Parâmetros extra
--|--|--|--
AUC_Macro| AUC é a área sob a Curva Característica De Funcionamento do Recetor. Macro é a média aritmética da AUC para cada classe.  | [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | média="macro"|
AUC_Micro| AUC é a área sob a Curva Característica De Funcionamento do Recetor. A micro é calculada globalmente combinando os verdadeiros positivos e falsos positivos de cada classe.| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | média="micro"|
AUC_Weighted  | AUC é a área sob a Curva Característica De Funcionamento do Recetor. Ponderado é a média aritmética da pontuação para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|média="ponderado"
accuracy|A precisão é a percentagem de rótulos previstos que correspondem exatamente aos rótulos verdadeiros. |[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Nenhuma|
average_precision_score_macro|A precisão média resume uma curva de recolha de precisão como a média ponderada de precisões alcançadas em cada limiar, com o aumento da recuperação do limiar anterior utilizado como peso. Macro é a média aritmética da pontuação média de precisão de cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|média="macro"|
average_precision_score_micro|A precisão média resume uma curva de recolha de precisão como a média ponderada de precisões alcançadas em cada limiar, com o aumento da recuperação do limiar anterior utilizado como peso. A micro é calculada globalmente combinando os verdadeiros positivos e falsos positivos em cada corte.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|média="micro"|
average_precision_score_weighted|A precisão média resume uma curva de recolha de precisão como a média ponderada de precisões alcançadas em cada limiar, com o aumento da recuperação do limiar anterior utilizado como peso. Ponderado é a média aritmética da pontuação média de precisão para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|média="ponderado"|
balanced_accuracy|Precisão equilibrada é a média aritmética de recordação para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média="macro"|
f1_score_macro|A pontuação de F1 é a média harmónica de precisão e recordação. Macro é a média aritmética da pontuação de F1 para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|média="macro"|
f1_score_micro|A pontuação de F1 é a média harmónica de precisão e recordação. A micro é calculada globalmente contando os verdadeiros positivos, falsos negativos e falsos positivos.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|média="micro"|
f1_score_weighted|A pontuação de F1 é a média harmónica de precisão e recordação. Média ponderada por frequência de classe de pontuação de F1 para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|média="ponderado"|
log_loss|Esta é a função de perda utilizada na regressão logística (multinomial) e extensões dela, tais como redes neurais, definidas como a probabilidade negativa de log-probabilidade dos verdadeiros rótulos dadas as previsões de um classificador probabilístico. Para uma única amostra com {0,1} rótulo verdadeiro yt in e probabilidade estimada yp que yt = 1, a perda de tronco é -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp)).|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Nenhuma|
norm_macro_recall|Macro Recall normalizado é Macro Recall normalizado para que o desempenho aleatório tenha uma pontuação de 0 e o desempenho perfeito tenha uma pontuação de 1. Isto é conseguido por norm_macro_recall := (recall_score_macro - R)/1 - R), onde R é o valor esperado de recall_score_macro para previsões aleatórias (isto é, R=0,5 para classificação binária e R=(1/C) para problemas de classificação da classe C).|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "macro" |
precision_score_macro|A precisão é a percentagem de elementos positivamente previstos que estão corretamente rotulados. Macro é a média aritmética de precisão para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média="macro"|
precision_score_micro|A precisão é a percentagem de elementos positivamente previstos que estão corretamente rotulados. A micro é calculada globalmente contando os verdadeiros positivos e falsos positivos.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média="micro"|
precision_score_weighted|A precisão é a percentagem de elementos positivamente previstos que estão corretamente rotulados. Ponderado é a média aritmética de precisão para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média="ponderado"|
recall_score_macro|A recordação é a percentagem de elementos corretamente rotulados de uma determinada classe. Macro é a média aritmética de recordar cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média="macro"|
recall_score_micro|A recordação é a percentagem de elementos corretamente rotulados de uma determinada classe. Micro é calculado globalmente contando os verdadeiros positivos, falsos negativos e falsos positivos|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média="micro"|
recall_score_weighted|A recordação é a percentagem de elementos corretamente rotulados de uma determinada classe. Ponderado é a média aritmética de recordação para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média="ponderado"|
weighted_accuracy|Precisão ponderada é precisão em que o peso dado a cada exemplo é igual à proporção de instâncias verdadeiras na verdadeira classe desse exemplo.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight é um vetor igual à proporção dessa classe para cada elemento no alvo|

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>Matriz de confusão

#### <a name="what-is-a-confusion-matrix"></a>O que é uma matriz de confusão?
Uma matriz de confusão é usada para descrever o desempenho de um modelo de classificação. Cada linha apresenta as instâncias da classe verdadeira, ou real no seu conjunto de dados, e cada coluna representa os casos da classe que foi prevista pelo modelo. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>O que o ML automatizado faz com a matriz de confusão?
Para problemas de classificação, o Azure Machine Learning fornece automaticamente uma matriz de confusão para cada modelo que é construído. Para cada matriz de confusão, ml automatizado mostrará a frequência de cada etiqueta prevista (coluna) em comparação com a verdadeira etiqueta (linha). Quanto mais escura a cor, maior a contagem nessa parte particular da matriz. 

#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
Estamos a comparar o valor real do conjunto de dados com os valores previstos que o modelo deu. Por isso, os modelos de machine learning têm maior precisão se o modelo tiver a maioria dos seus valores ao longo da diagonal, o que significa que o modelo previu o valor correto. Se um modelo tiver desequilíbrio de classe, a matriz de confusão ajudará a detetar um modelo tendencioso.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Exemplo 1: Um modelo de classificação com fraca precisão
![Um modelo de classificação com má precisão](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>Exemplo 2: Um modelo de classificação com alta precisão 
![Um modelo de classificação com alta precisão](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Exemplo 3: Um modelo de classificação com alta precisão e elevado enviesamento nas previsões do modelo
![Um modelo de classificação com alta precisão e elevado enviesamento nas previsões do modelo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Gráfico de recordação de precisão
#### <a name="what-is-a-precision-recall-chart"></a>O que é um gráfico de recordação de precisão?
A curva de recordação de precisão mostra a relação entre precisão e recordação de um modelo. O termo precisão representa essa capacidade de um modelo rotular corretamente todas as instâncias. A recordação representa a capacidade de um classificador encontrar todos os casos de um determinado rótulo.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>O que o ML automatizado faz com o gráfico de recolha de precisão?

Com este gráfico, pode comparar as curvas de recuperação de precisão para cada modelo para determinar qual modelo tem uma relação aceitável entre precisão e recuperação para o seu problema de negócio particular. Este gráfico mostra a Recuperação de Precisão Média Macro, Micro Average Precision-Recall, e a recolha de precisão associada a todas as classes para um modelo. 

A macromédia calculará a métrica independentemente de cada classe e, em seguida, tomará a média, tratando todas as classes de forma igual. No entanto, a micromédia irá agregar as contribuições de todas as classes para calcular a média. A micromédia é preferível se houver desequilíbrio de classe presente no conjunto de dados.

#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
Dependendo do objetivo do problema de negócio, a curva ideal de recolha de precisão pode diferir. Alguns exemplos são dados abaixo

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Exemplo 1: Um modelo de classificação com baixa precisão e baixa recuperação
![Um modelo de classificação com baixa precisão e baixa recuperação](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Exemplo 2: Um modelo de classificação com ~100% de precisão e ~100% de recordação 
![Um modelo de classificação de alta precisão e recordação](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>Gráfico roc

#### <a name="what-is-a-roc-chart"></a>O que é um gráfico roc?
A característica de funcionamento do recetor (ou ROC) é um enredo das etiquetas corretamente classificadas vs. as etiquetas classificadas incorretamente para um determinado modelo. A curva ROC pode ser menos informativa quando se treinam modelos em conjuntos de dados com elevado enviesamento, uma vez que não mostrará as etiquetas falsas positivas.

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
Os gráficos de elevação são usados para avaliar o desempenho de um modelo de classificação. Mostra o quanto pode esperar fazer com o modelo gerado em comparação com sem um modelo em termos de precisão.
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>O que o ML automatizado faz com a tabela de elevação?
Pode comparar o elevador do modelo construído automaticamente com o Azure Machine Learning com a linha de base de forma a ver o ganho de valor desse modelo em particular.
#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>Exemplo 1: Um modelo de classificação que faz pior do que um modelo de seleção aleatório
![Um modelo de classificação que faz pior do que um modelo de seleção aleatório](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Exemplo 2: Um modelo de classificação que tem um melhor desempenho do que um modelo de seleção aleatório
![Um modelo de classificação que tem um melhor desempenho](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>Gráfico de ganhos
#### <a name="what-is-a-gains-chart"></a>O que é um gráfico de ganhos?

Um gráfico de ganhos avalia o desempenho de um modelo de classificação por cada parte dos dados. Mostra para cada percentil do conjunto de dados, o quão melhor você pode esperar para executar em comparação com um modelo de seleção aleatório.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>O que faz o ML automatizado com a tabela de ganhos?
Utilize o gráfico de ganhos acumulados para ajudá-lo a escolher o corte de classificação usando uma percentagem que corresponde a um ganho desejado do modelo. Esta informação fornece outra forma de analisar os resultados no gráfico de elevação que o acompanha.

#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Exemplo 1: Um modelo de classificação com ganho mínimo
![um modelo de classificação com ganho mínimo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Exemplo 2: Um modelo de classificação com ganhos significativos
![Um modelo de classificação com ganhos significativos](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Gráfico de calibração

#### <a name="what-is-a-calibration-chart"></a>O que é um gráfico de calibração?
Um enredo de calibração é usado para mostrar a confiança de um modelo preditivo. Fá-lo mostrando a relação entre a probabilidade prevista e a probabilidade real, onde a "probabilidade" representa a probabilidade de uma determinada instância pertencer a algum rótulo.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>O que o ML automatizado faz com o gráfico de calibração?
Para todos os problemas de classificação, pode rever a linha de calibração para micromédia, macromédia e cada classe num determinado modelo preditivo.

A macromédia calculará a métrica independentemente de cada classe e, em seguida, tomará a média, tratando todas as classes de forma igual. No entanto, a micromédia irá agregar as contribuições de todas as classes para calcular a média. 
#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
 Um modelo bem calibrado alinha-se com a linha y=x, onde é razoavelmente confiante nas suas previsões. Um modelo demasiado confiante alinha-se com a linha y=0, onde a probabilidade prevista está presente, mas não há probabilidade real. 


##### <a name="example-1-a-well-calibrated-model"></a>Exemplo 1: Um modelo bem calibrado
![ modelo mais bem calibrado](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>Exemplo 2: Um modelo demasiado confiante
![Um modelo demasiado confiante](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression-results"></a><a name="regression"></a>Resultados da regressão

As métricas e gráficos que se seguem estão disponíveis para todos os modelos de regressão que você constrói usando as capacidades automatizadas de aprendizagem automática de máquinas de aprendizagem automática de Azure Machine Learning

+ [Métricas](#reg-metrics)
+ [Previsto vs. Verdadeiro](#pvt)
+ [Histograma dos residuais](#histo)


### <a name="regression-metrics"></a><a name="reg-metrics"></a>Métricas de regressão

As seguintes métricas são guardadas em cada iteração de execução para uma tarefa de regressão ou previsão.

|Métrica|Descrição|Cálculo|Parâmetros extra
--|--|--|--|
explained_variance|Variação explicada é a proporção a que um modelo matemático explica a variação de um determinado conjunto de dados. É a diminuição percentual da variação dos dados originais à variação dos erros. Quando a média dos erros é 0, é igual a uma variação explicada.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Nenhuma|
r2_score|R2 é o coeficiente de determinação ou a redução percentual de erros quadrados em comparação com um modelo de base que produz a média. |[Cálculo](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Nenhuma|
spearman_correlation|A correlação com o homem-lança é uma medida não paramétrica da monotonicidade da relação entre dois conjuntos de dados. Ao contrário da correlação pearson, a correlação de Spearman não assume que ambos os conjuntos de dados são normalmente distribuídos. Tal como outros coeficientes de correlação, este varia entre -1 e +1 com 0 implicando nenhuma correlação. As correlações de -1 ou +1 implicam uma relação monotónica exata. Correlações positivas implicam que à medida que x aumenta, y também. Correlações negativas implicam que à medida que x aumenta, y diminui.|[Cálculo](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Nenhuma|
mean_absolute_error|Erro absoluto médio é o valor esperado do valor absoluto da diferença entre o alvo e a previsão|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Nenhuma|
normalized_mean_absolute_error|Erro absoluto médio normalizado é erro absoluto dividido pela gama de dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Divida por alcance dos dados|
median_absolute_error|O erro absoluto mediano é a mediana de todas as diferenças absolutas entre o alvo e a previsão. Esta perda é robusta para os mais avançados.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Nenhuma|
normalized_median_absolute_error|Erro absoluto mediano normalizado é erro absoluto mediano dividido pela gama de dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Divida por alcance dos dados|
root_mean_squared_error|Erro quadrado médio raiz é a raiz quadrada da diferença quadrada esperada entre o alvo e a previsão|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Nenhuma|
normalized_root_mean_squared_error|Erro quadrado da raiz normalizado é erro quadrado médio raiz dividido pela gama dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Divida por alcance dos dados|
root_mean_squared_log_error|Erro de log quadrado médio de raiz é a raiz quadrada do erro logaritármico quadrado esperado|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Nenhuma|
normalized_root_mean_squared_log_error|Erro de registo quadrado da raiz normalizado é erro de registo quadrado médio raiz dividido pela gama dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Divida por alcance dos dados|

### <a name="predicted-vs-true-chart"></a><a name="pvt"></a>Gráfico previsto vs. gráfico verdadeiro
#### <a name="what-is-a-predicted-vs-true-chart"></a>O que é um gráfico previsto vs. verdadeiro?
Previsível vs. True mostra a relação entre um valor previsto e o seu valor real correlacionado para um problema de regressão. Este gráfico pode ser usado para medir o desempenho de um modelo como quanto mais perto da linha y=x os valores previstos são, melhor a precisão de um modelo preditivo.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>O que o ML automatizado faz com o gráfico Previsto vs. True?
Após cada corrida, você pode ver um gráfico previsível vs. verdadeiro gráfico para cada modelo de regressão. Para proteger a privacidade dos dados, os valores são ligados entre si e o tamanho de cada caixote é mostrado como um gráfico de barras na parte inferior da área do gráfico. Pode comparar o modelo preditivo, com a área de sombra mais clara mostrando margens de erro, com o valor ideal de onde o modelo deve estar.

#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>Exemplo 1: Um modelo de classificação com baixa precisão
![Um modelo de regressão com baixa precisão nas previsões](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>Exemplo 2: Um modelo de regressão com alta precisão 
[![Um modelo de regressão com alta precisão nas suas previsões](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histogram-of-residuals-chart"></a><a name="histo"></a>Histograma da tabela de residuais
#### <a name="what-is-a-residuals-chart"></a>O que é um gráfico de residuais?
Um residual representa um y observado – o previsto y. Para mostrar uma margem de erro com baixo enviesamento, o histograma dos residuais deve ser moldado como uma curva do sino, centrada em torno de 0. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>O que o ML automatizado faz com o gráfico de residuais?
O ML automatizado fornece automaticamente um gráfico residual para mostrar a distribuição de erros nas previsões.
#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
Um bom modelo normalmente terá uma curva do sino ou erros em torno de zero.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Exemplo 1: Um modelo de regressão com preconceito nos seus erros
![Modelo de regressão da SA com preconceito nos seus erros](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Exemplo 2: Um modelo de regressão com distribuição mais uniforme de erros
![Um modelo de regressão com distribuição mais uniforme de erros](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="model-interpretability-and-feature-importance"></a><a name="explain-model"></a>Interpretação do modelo e importância da característica
Ml automatizado fornece um painel de interpretabilidade de aprendizagem automática para as suas corridas.
Para obter mais informações sobre a possibilidade de funcionalidades de interpretação, consulte o [como](how-to-machine-learning-interpretability-automl.md) permitir a interpretação em experiências automatizadas de ML.

## <a name="next-steps"></a>Passos seguintes

+ Saiba mais sobre [ml automatizado](concept-automated-ml.md) em Azure Machine Learning.
+ Experimente os cadernos de amostras de modelo de [aprendizagem automática](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) de máquinas.
