---
title: Compreender os resultados automatizados do ML
titleSuffix: Azure Machine Learning
description: Aprenda a ver e a compreender gráficos e métricas para cada uma das suas máquinas automáticas de aprendizagem.
services: machine-learning
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/05/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: f0a41aa062cf4804587b97ce224f80c0bc4bf2b3
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650572"
---
# <a name="understand-automated-machine-learning-results"></a>Compreender os resultados de machine learning automatizado
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende a ver e entender os gráficos e métricas para cada uma das suas máquinas automáticas de aprendizagem. 

Saiba mais sobre:
+ [Métricas e gráficos para modelos de classificação](#classification)
+ [Métricas e gráficos para modelos de regressão](#regression)
+ [Interpretação do modelo e importância de característica](#explain-model)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Crie uma experiência para a sua aprendizagem automática de máquinas, seja com o SDK, quer no estúdio Azure Machine Learning.

    * Utilize o SDK para construir um modelo de [classificação](how-to-auto-train-remote.md) ou [modelo de regressão](tutorial-auto-train-models.md)
    * Utilize [o estúdio Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md) para criar um modelo de classificação ou regressão carregando os dados apropriados.

## <a name="view-the-run"></a>Ver a corrida

Depois de executar uma experiência automatizada de aprendizagem automática, uma história das corridas pode ser encontrada no seu espaço de trabalho de aprendizagem automática. 

1. Vá para a área de trabalho.

1. No painel esquerdo do espaço de trabalho, selecione **Experimentos**.

   ![Screenshot do menu de experiências](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Na lista de experiências, selecione a que deseja explorar.

   [![Lista de experiências](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Na tabela inferior, selecione o **Run**.

   [ ![ Corrida de experiências](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. Nos Modelos, selecione o **nome Algoritmo** para o modelo que pretende explorar mais.

   [![Modelo de experiência](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Você também vê estes mesmos resultados durante uma corrida quando você usa o `RunDetails` [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true).

## <a name="classification-results"></a><a name="classification"></a> Resultados da classificação

As seguintes métricas e gráficos estão disponíveis para todos os modelos de classificação que constrói utilizando as capacidades automatizadas de aprendizagem automática de máquinas de aprendizagem automática do Azure Machine Learning

+ [Métricas](#classification-metrics)
+ [A matriz de confusão](#confusion-matrix)
+ [Gráfico de recuperação de precisão](#precision-recall-chart)
+ [Características de funcionamento do recetor (ou ROC)](#roc)
+ [Curva de elevação](#lift-curve)
+ [Curva de ganhos](#gains-curve)
+ [Enredo de calibração](#calibration-plot)

### <a name="classification-metrics"></a>Métricas de classificação

As seguintes métricas são guardadas em cada iteração de execução para uma tarefa de classificação.

Métrica|Descrição|Cálculo|Parâmetros extra
--|--|--|--
AUC_macro| AUC é a área sob a curva característica de funcionamento do recetor. Macro é a média aritmética da AUC para cada classe.  | [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | média="macro"|
AUC_micro| AUC é a área sob a curva característica de funcionamento do recetor. A Micro é calculada globalmente combinando os verdadeiros positivos e falsos positivos de cada classe.| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | média="micro"|
AUC_weighted  | AUC é a área sob a curva característica de funcionamento do recetor. Ponderada é a média aritmética da pontuação para cada classe, ponderada pelo número de casos verdadeiros em cada classe.| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|média="ponderado"
accuracy|Precisão é a percentagem de rótulos previstos que correspondem exatamente às verdadeiras etiquetas. |[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Nenhum|
average_precision_score_macro|A precisão média resume uma curva de precisão-recordação como a média ponderada de precisões alcançadas em cada limiar, com o aumento da recuperação do limiar anterior utilizado como peso. Macro é a média aritmética da pontuação média de precisão de cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|média="macro"|
average_precision_score_micro|A precisão média resume uma curva de precisão-recordação como a média ponderada de precisões alcançadas em cada limiar, com o aumento da recuperação do limiar anterior utilizado como peso. A Micro é calculada globalmente combinando os verdadeiros positivos e falsos positivos em cada corte.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|média="micro"|
average_precision_score_weighted|A precisão média resume uma curva de precisão-recordação como a média ponderada de precisões alcançadas em cada limiar, com o aumento da recuperação do limiar anterior utilizado como peso. Ponderada é a média aritmética da pontuação média de precisão para cada classe, ponderada pelo número de casos verdadeiros em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|média="ponderado"|
balanced_accuracy|Precisão equilibrada é a média aritmética de recordação para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média="macro"|
f1_score_macro|A pontuação de F1 é a média harmónica de precisão e recordação. Macro é a média aritmética da pontuação de F1 para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|média="macro"|
f1_score_micro|A pontuação de F1 é a média harmónica de precisão e recordação. A Micro é calculada globalmente contando os verdadeiros positivos, falsos negativos e falsos positivos.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|média="micro"|
f1_score_weighted|A pontuação de F1 é a média harmónica de precisão e recordação. Média ponderada por frequência de classe da pontuação F1 para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|média="ponderado"|
log_loss|Esta é a função de perda utilizada na regressão logística (multinomial) e extensões da mesmo, tais como redes neurais, definidas como a probabilidade negativa de log-probabilidade dos verdadeiros rótulos dadas as previsões de um classificador probabilístico. Para uma única amostra com yt de etiqueta verdadeira {0,1} e probabilidade estimada yp que yt = 1, a perda de log é -log P (yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp)).|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Nenhum|
norm_macro_recall|Macro Recall normalizado é Macro Recall normalizado de modo que o desempenho aleatório tem uma pontuação de 0 e desempenho perfeito tem uma pontuação de 1. Isto é conseguido por norm_macro_recall := (recall_score_macro - R)/(1 - R), onde R é o valor esperado de recall_score_macro para previsões aleatórias (i.e., R=0,5 para classificação binária e R=(1/C) para problemas de classificação classe C).|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "macro" |
precision_score_macro|Precisão é a percentagem de elementos positivamente previstos que estão corretamente rotulados. Macro é a média aritmética de precisão para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média="macro"|
precision_score_micro|Precisão é a percentagem de elementos positivamente previstos que estão corretamente rotulados. A Micro é calculada globalmente contando os verdadeiros positivos e falsos positivos.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média="micro"|
precision_score_weighted|Precisão é a percentagem de elementos positivamente previstos que estão corretamente rotulados. Ponderada é a média aritmética de precisão para cada classe, ponderada pelo número de casos verdadeiros em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média="ponderado"|
recall_score_macro|Lembre-se é a percentagem de elementos corretamente rotulados de uma determinada classe. Macro é a média aritmética de recordação para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média="macro"|
recall_score_micro|Lembre-se é a percentagem de elementos corretamente rotulados de uma determinada classe. Micro é calculado globalmente contando os verdadeiros positivos totais, falsos negativos e falsos positivos|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média="micro"|
recall_score_weighted|Lembre-se é a percentagem de elementos corretamente rotulados de uma determinada classe. Ponderada é a média aritmética de recordação para cada classe, ponderada pelo número de casos verdadeiros em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média="ponderado"|
weighted_accuracy|Precisão ponderada é a precisão em que o peso dado a cada exemplo é igual à proporção de casos verdadeiros na verdadeira classe desse exemplo.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight é um vetor igual à proporção dessa classe para cada elemento no alvo|

### <a name="binary-vs-multiclass-metrics"></a>Métricas binárias vs. multiclasse

O AutoML não diferencia entre métricas binárias e multiclasses. As mesmas métricas de validação são reportadas se um conjunto de dados tem duas classes ou mais de duas classes. No entanto, algumas métricas destinam-se à classificação multiclasse. Quando aplicadas a um conjunto binário de dados, estas métricas não tratarão nenhuma classe como a `true` classe, como seria de esperar. As métricas claramente destinadas a multiclasse são sufixadas com `micro` `macro` , ou `weighted` . Exemplos `average_precision_score` `f1_score` incluem, , , `precision_score` e `recall_score` `AUC` .

Um exemplo concreto torna esta distinção mais clara: Em vez de calcular a recordação como `tp / (tp + fn)` , as médias médias multiclasse `micro` `macro` (, ou ) `weighted` médias em ambas as classes de um conjunto de dados de classificação binária. Isto equivale a calcular a recordação para a `true` classe e a classe `false` separadamente e, em seguida, tomar a média dos dois.

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>A matriz de confusão

#### <a name="what-is-a-confusion-matrix"></a>O que é uma matriz de confusão?
Uma matriz de confusão é usada para descrever o desempenho de um modelo de classificação. Cada linha exibe os casos da classe verdadeira ou real no seu conjunto de dados, e cada coluna representa os casos da classe que foi prevista pelo modelo. 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>O que faz a ML automatizada com a matriz de confusão?
Para problemas de classificação, o Azure Machine Learning fornece automaticamente uma matriz de confusão para cada modelo que é construído. Para cada matriz de confusão, o ML automatizado mostrará a frequência de cada etiqueta prevista (coluna) em comparação com a verdadeira etiqueta (linha). Quanto mais escura a cor, maior a contagem nessa parte particular da matriz. 

#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
Estamos a comparar o valor real do conjunto de dados com os valores previstos que o modelo deu. Por isso, os modelos de machine learning têm maior precisão se o modelo tiver a maioria dos seus valores ao longo da diagonal, o que significa que o modelo previu o valor correto. Se um modelo tiver desequilíbrio de classe, a matriz de confusão ajudará a detetar um modelo tendencioso.

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Exemplo 1: Um modelo de classificação com pouca precisão
![Um modelo de classificação com pouca precisão](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>Exemplo 2: Um modelo de classificação com elevada precisão 
![Um modelo de classificação com alta precisão](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Exemplo 3: Um modelo de classificação com elevada precisão e elevado enviesamento nas previsões dos modelos
![Um modelo de classificação com alta precisão e alta tendência nas previsões de modelos](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>Gráfico de recuperação de precisão
#### <a name="what-is-a-precision-recall-chart"></a>O que é um gráfico de precisão?
A curva de precisão-recordação mostra a relação entre precisão e recordação de um modelo. O termo precisão representa essa capacidade de um modelo rotular corretamente todas as instâncias. A recordação representa a capacidade de um classificador encontrar todas as instâncias de um rótulo específico.

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>O que faz a ML automatizada com o gráfico de recolha de precisão?

Com este gráfico, pode comparar as curvas de precisão para cada modelo para determinar qual o modelo que tem uma relação aceitável entre precisão e recordação para o seu problema de negócio em particular. Este gráfico mostra macro average Precision-Recall, Micro Average Precision-Recall, e a recordação de precisão associada a todas as classes para um modelo. 

A média macro-média calculará a métrica independentemente de cada classe e, em seguida, tomará a média, tratando todas as classes de forma igual. No entanto, a micro-média irá agregar as contribuições de todas as classes para calcular a média. A micro-média é preferível se houver desequilíbrio de classe presente no conjunto de dados.

#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
Dependendo do objetivo do problema do negócio, a curva ideal de recuperação de precisão pode diferir. Alguns exemplos são dados abaixo

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Exemplo 1: Um modelo de classificação com baixa precisão e baixa recuperação
![Um modelo de classificação com baixa precisão e baixa recuperação](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Exemplo 2: Um modelo de classificação com ~100% de precisão e ~100% de recordação 
![Um modelo de classificação de alta precisão e recordação](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>Gráfico ROC

#### <a name="what-is-a-roc-chart"></a>O que é um gráfico ROC?
A característica de funcionamento do recetor (ou ROC) é um enredo das etiquetas corretamente classificadas vs. as etiquetas incorretamente classificadas para um determinado modelo. A curva ROC pode ser menos informativa quando se treinam modelos em conjuntos de dados com desequilíbrio de classe elevada, uma vez que a classe maioritária pode abafar a contribuição das classes minoritárias.

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>O que faz a ML automatizada com a tabela ROC?
Pode visualizar a área sob a tabela ROC como a proporção de amostras corretamente classificadas. Um utilizador avançado da tabela ROC pode olhar para além da área sob a curva e obter uma intuição para as verdadeiras taxas positivas e falsas positivas em função do limiar de classificação ou limite de decisão.

#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
Uma curva ROC que se aproxima do canto superior esquerdo com uma taxa positiva 100% verdadeira e uma taxa positiva falsa de 0% será o melhor modelo. Um modelo aleatório exibiria como uma linha plana de baixo para a esquerda para o canto superior direito. Pior do que o aleatório mergulharia abaixo da linha y=x.

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Exemplo 1: Um modelo de classificação com rótulos de baixa verdade e rótulos falsos elevados
![Modelo de classificação com rótulos de baixa verdade e rótulos falsos elevados](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Exemplo 2: Um modelo de classificação com rótulos de alta verdade e rótulos falsos baixos
![um modelo de classificação com rótulos de alta verdade e rótulos falsos baixos](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>Gráfico de elevação
#### <a name="what-is-a-lift-chart"></a>O que é um gráfico de elevador?
Os gráficos de elevação são usados para avaliar o desempenho dos modelos de classificação. Um gráfico de elevação mostra quantas vezes melhor um modelo executa em comparação com um modelo aleatório. Isto dá-lhe um desempenho relativo que tem em conta o facto de que a classificação fica mais difícil à medida que aumenta o número de turmas. Um modelo aleatório irá prever incorretamente uma fração mais alta de amostras de um conjunto de dados com dez classes em comparação com um conjunto de dados com duas classes.

#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>O que faz a ML automatizada com o gráfico de elevação?
Pode comparar o elevador do modelo construído automaticamente com o Azure Machine Learning com a linha de base para ver o ganho de valor desse modelo em particular.
#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>Exemplo 1: Um modelo de classificação que faz pior do que um modelo de seleção aleatório
![Um modelo de classificação que faz pior do que um modelo de seleção aleatório](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Exemplo 2: Um modelo de classificação que funciona melhor do que um modelo de seleção aleatório
![Um modelo de classificação que tem um melhor desempenho](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="cumulative-gains-chart"></a>Gráfico de ganhos acumulados
#### <a name="what-is-a-cumulative-gains-chart"></a>O que é um gráfico de ganhos acumulados?

Um gráfico de ganhos cumulativos avalia o desempenho de um modelo de classificação por cada parte dos dados. Para cada percentil do conjunto de dados, o gráfico mostra quantas mais amostras foram classificadas com precisão.

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>O que a ML automatizada faz com o gráfico de ganhos?
Utilize o gráfico de ganhos acumulados para ajudá-lo a escolher o corte de classificação usando uma percentagem que corresponde a um ganho desejado do modelo. Esta informação fornece outra forma de analisar os resultados no gráfico de elevação que o acompanha.

#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Exemplo 1: Um modelo de classificação com ganho mínimo
![um modelo de classificação com ganho mínimo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Exemplo 2: Um modelo de classificação com ganho significativo
![Um modelo de classificação com ganho significativo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>Gráfico de calibração

#### <a name="what-is-a-calibration-chart"></a>O que é um gráfico de calibração?
Um enredo de calibração é usado para mostrar a confiança de um modelo preditivo. Fá-lo mostrando a relação entre a probabilidade prevista e a probabilidade real, onde a "probabilidade" representa a probabilidade de que um determinado caso pertença a algum rótulo.
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>O que faz a ML automatizada com o gráfico de calibração?
Para todos os problemas de classificação, pode rever a linha de calibração para micro-média, macro-média e cada classe num determinado modelo preditivo.

A média macro-média calculará a métrica independentemente de cada classe e, em seguida, tomará a média, tratando todas as classes de forma igual. No entanto, a micro-média irá agregar as contribuições de todas as classes para calcular a média. 
#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
Um modelo bem calibrado alinha-se com a linha y=x, onde prevê corretamente a probabilidade de as amostras pertencerem a cada classe. Um modelo sobre-confiante irá prever probabilidades excessivamente próximas de zero e um, raramente sendo incerto sobre a classe de cada amostra.


##### <a name="example-1-a-well-calibrated-model"></a>Exemplo 1: Um modelo bem calibrado
![ modelo mais bem calibrado](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>Exemplo 2: Um modelo muito confiante
![Um modelo muito confiante](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression-results"></a><a name="regression"></a> Resultados da regressão

As seguintes métricas e gráficos estão disponíveis para cada modelo de regressão que constrói utilizando as capacidades automatizadas de aprendizagem automática de máquinas de aprendizagem de máquinas

+ [Métricas](#reg-metrics)
+ [Previsto vs. Verdadeiro](#pvt)
+ [Histograma de residuais](#histo)


### <a name="regression-metrics"></a><a name="reg-metrics"></a> Métricas de regressão

As seguintes métricas são guardadas em cada iteração de execução para uma tarefa de regressão ou previsão.

|Métrica|Descrição|Cálculo|Parâmetros extra
--|--|--|--|
explained_variance|A variação explicada é a proporção a que um modelo matemático explica a variação de um determinado conjunto de dados. É a diminuição percentual da variação dos dados originais para a variação dos erros. Quando a média dos erros é 0, é igual a uma variação explicada.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Nenhum|
r2_score|R2 é o coeficiente de determinação ou a redução por cento dos erros quadrados em comparação com um modelo de base que produz a média. |[Cálculo](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Nenhum|
spearman_correlation|A correlação de Spearman é uma medida nãoparamétrica da monótona relação entre dois conjuntos de dados. Ao contrário da correlação pearson, a correlação de Spearman não assume que ambos os conjuntos de dados são normalmente distribuídos. Como outros coeficientes de correlação, este varia entre -1 e +1 com 0 implicando nenhuma correlação. As correlações de -1 ou +1 implicam uma relação monótona exata. Correlações positivas implicam que à medida que x aumenta, y também. As correlações negativas implicam que à medida que x aumenta, y diminui.|[Cálculo](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Nenhum|
mean_absolute_error|Erro absoluto médio é o valor esperado do valor absoluto da diferença entre o alvo e a previsão|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Nenhum|
normalized_mean_absolute_error|Erro absoluto médio normalizado é erro absoluto dividido pela gama de dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Divida por intervalo de dados|
median_absolute_error|O erro absoluto mediano é a mediana de todas as diferenças absolutas entre o alvo e a previsão. Esta perda é robusta para os forasteiros.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Nenhum|
normalized_median_absolute_error|Erro absoluto mediano normalizado é erro absoluto mediano dividido pela gama de dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Divida por intervalo de dados|
root_mean_squared_error|Erro quadrado de raiz é a raiz quadrada da diferença quadrada esperada entre o alvo e a previsão|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Nenhum|
normalized_root_mean_squared_error|Erro quadrado de raiz normalizado é erro quadrado de raiz dividido pela gama de dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Divida por intervalo de dados|
root_mean_squared_log_error|Erro de log quadrado de raiz é a raiz quadrada do erro logarítmico quadrado esperado|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Nenhum|
normalized_root_mean_squared_log_error|Erro de registo quadrado de raiz normalizado é erro de registo quadrado de raiz dividido pela gama de dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Divida por intervalo de dados|

### <a name="predicted-vs-true-chart"></a><a name="pvt"></a> Gráfico previsto vs. Verdadeiro gráfico
#### <a name="what-is-a-predicted-vs-true-chart"></a>O que é um gráfico previsto vs. Verdadeiro?
Previsto vs. Verdadeiro mostra a relação entre um valor previsto e o seu valor real correlacionado para um problema de regressão. Este gráfico pode ser usado para medir o desempenho de um modelo à medida que quanto mais próximo da linha y=x os valores previstos são, melhor a precisão de um modelo preditivo.

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>O que faz a ML automatizada com o gráfico previsto vs. Verdadeiro?
Após cada corrida, pode ver um gráfico previsto vs. verdadeiro para cada modelo de regressão. Para proteger a privacidade dos dados, os valores são ligados em conjunto e o tamanho de cada caixa é mostrado como um gráfico de barra na parte inferior da área do gráfico. Pode comparar o modelo preditivo, com a área de sombra mais clara a mostrar margens de erro, com o valor ideal de onde o modelo deve estar.

#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>Exemplo 1: Um modelo de classificação com baixa precisão
![Um modelo de regressão com baixa precisão nas previsões](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>Exemplo 2: Um modelo de regressão com elevada precisão 
[![Um modelo de regressão com elevada precisão nas suas previsões](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histogram-of-residuals-chart"></a><a name="histo"></a> Histograma de gráfico de residuais
#### <a name="what-is-a-residuals-chart"></a>O que é um gráfico residual?
Um residual é a diferença entre a previsão e o valor real ( `y_pred - y_true` ). Para mostrar uma margem de erro com baixo viés, o histograma dos residuais deve ser moldado como uma curva sineira, centrada em torno de 0. 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>O que faz a ML automatizada com o gráfico de residuais?
O ML automatizado fornece automaticamente um gráfico residual para mostrar a distribuição de erros nas previsões.
#### <a name="what-does-a-good-model-look-like"></a>Como é um bom modelo?
Um bom modelo normalmente terá residuais intimamente centrados em torno de zero.

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Exemplo 1: Um modelo de regressão com parcialidade nos seus erros
![Modelo de regressão sa com distorção nos seus erros](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>Exemplo 2: Um modelo de regressão com distribuição mais uniforme de erros
![Um modelo de regressão com distribuição mais uniforme de erros](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="model-interpretability-and-feature-importance"></a><a name="explain-model"></a> Interpretação do modelo e importância de característica
O ML automatizado fornece um painel de interpretação de aprendizagem automática para as suas execuções.
Para obter mais informações sobre a ativação de funcionalidades de interpretação, consulte o como permitir [a](how-to-machine-learning-interpretability-automl.md) interpretação em experiências automatizadas de ML.

> [!NOTE]
> O modelo ForecastTCN não é atualmente suportado pelo Cliente explicação. Este modelo não devolverá um dashboard de explicação se for devolvido como o melhor modelo, e não suporta explicações a pedido.

## <a name="next-steps"></a>Próximos passos

+ Saiba mais sobre [ml automatizado](concept-automated-ml.md) em Azure Machine Learning.
+ Experimente os [cadernos de amostras de modelo de aprendizagem automática de](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) máquinas.
