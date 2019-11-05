---
title: Entender os resultados de ML automatizados
titleSuffix: Azure Machine Learning
description: Saiba como exibir e entender os gráficos e as métricas para cada uma das execuções automatizadas do Machine Learning.
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 93695e0bbcb81a570519a6f74cfdeab4ef85f076
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489396"
---
# <a name="understand-automated-machine-learning-results"></a>Entender os resultados automatizados do Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a exibir e a entender os gráficos e as métricas de cada uma das execuções automatizadas do Machine Learning. 

Saiba mais sobre:
+ [Métricas, gráficos e curvas para modelos de classificação](#classification)
+ [Métricas, gráficos e grafos para modelos de regressão](#regression)
+ [Importância da interpretação do modelo e do recurso](#explain-model)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje.

* Crie um experimento para a execução automatizada do Machine Learning, seja com o SDK ou no Azure Machine Learning Studio.

    * Usar o SDK para criar um modelo de [classificação](how-to-auto-train-remote.md) ou um [modelo de regressão](tutorial-auto-train-models.md)
    * Use o [Azure Machine Learning Studio](how-to-create-portal-experiments.md) para criar um modelo de classificação ou regressão carregando os dados apropriados.

## <a name="view-the-run"></a>Exibir a execução

Depois de executar um experimento de aprendizado de máquina automatizado, um histórico das execuções pode ser encontrado no seu espaço de trabalho do Machine Learning. 

1. Vá para a área de trabalho.

1. No painel esquerdo do espaço de trabalho, selecione **experimentos**.

   ![Captura de tela do menu experimento](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Na lista de experimentos, selecione aquele que você deseja explorar.

   [lista de experimentos do ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Na tabela inferior, selecione o **número de execução**.

   [![execução do experimento](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png))

1. Na tabela iterações, selecione o **número de iteração** para o modelo que você deseja explorar ainda mais.

   [modelo de experimento ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Você também vê esses mesmos resultados durante uma execução quando usa o widget `RunDetails`[Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification"></a>Resultados da classificação

Três as métricas e os gráficos a seguir estão disponíveis para cada modelo de classificação que você cria usando os recursos de aprendizado de máquina automatizados do Azure Machine Learning

+ [Métricas](#classification-metrics)
+ [Matriz de confusão](#confusion-matrix)
+ [Gráfico de recall de precisão](#precision-recall-chart)
+ [Características operacionais do receptor (ou ROC)](#roc)
+ [Curva de comparação de precisão](#lift-curve)
+ [Ganha curva](#gains-curve)
+ [Plotagem de calibragem](#calibration-plot)

### <a name="classification-metrics"></a>Métricas de classificação

As métricas a seguir são salvas em cada iteração de execução para uma tarefa de classificação.

|Métrica|Descrição|Cálculo|Parâmetros extras
--|--|--|--|
AUC_Macro| AUC é a área sob a curva de características operacionais do receptor. Macro é a média aritmética do AUC para cada classe.  | [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Average = "macro"|
AUC_Micro| AUC é a área sob a curva de características operacionais do receptor. O micro é calculado globalmente combinando os verdadeiros positivos e falsos positivos de cada classe.| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | Average = "micro"|
AUC_Weighted  | AUC é a área sob a curva de características operacionais do receptor. Ponderado é a média aritmética da Pontuação de cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|média = "ponderado"
accuracy|A precisão é a porcentagem de rótulos previstos que correspondem exatamente aos rótulos verdadeiros. |[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Nenhum|
average_precision_score_macro|A precisão média resume uma curva de recall de precisão como a média ponderada de precisão obtidas em cada limite, com o aumento em RECALL do limite anterior usado como o peso. Macro é a média aritmética da Pontuação de precisão média de cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Average = "macro"|
average_precision_score_micro|A precisão média resume uma curva de recall de precisão como a média ponderada de precisão obtidas em cada limite, com o aumento em RECALL do limite anterior usado como o peso. O micro é calculado globalmente combinando os verdadeiros positivos e falsos positivos em cada corte.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|Average = "micro"|
average_precision_score_weighted|A precisão média resume uma curva de recall de precisão como a média ponderada de precisão obtidas em cada limite, com o aumento em RECALL do limite anterior usado como o peso. Ponderado é a média aritmética da Pontuação de precisão média para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|média = "ponderado"|
balanced_accuracy|A precisão equilibrada é a média aritmética de recall para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "macro"|
f1_score_macro|A pontuação F1 é a média harmônica de precisão e RECALL. Macro é a média aritmética da Pontuação F1 para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Average = "macro"|
f1_score_micro|A pontuação F1 é a média harmônica de precisão e RECALL. O micro é calculado globalmente contando o total de positivos verdadeiros, falsos negativos e falsos positivos.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|Average = "micro"|
f1_score_weighted|A pontuação F1 é a média harmônica de precisão e RECALL. Média ponderada por frequência de classe de Pontuação F1 para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|média = "ponderado"|
log_loss|Essa é a função de perda usada em regressão logística (multinomial) e extensões de ti, como redes neurais, definidas como a probabilidade negativa de log dos rótulos verdadeiros, dadas as previsões de um classificador probabilística. Para um único exemplo com verdadeiro rótulo YT em {0,1} e a probabilidade estimada YP que YT = 1, a perda de log é-log&#124;P (YT YP) =-(YT log (YP) + (1-YT) log (1-YP)).|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Nenhum|
norm_macro_recall|A recall de macro normalizada é recall de macro normalizado para que o desempenho aleatório tenha uma pontuação de 0 e o desempenho perfeito tenha uma pontuação de 1. Isso é obtido por norm_macro_recall: = (recall_score_macro-R)/(1-R), em que R é o valor esperado de recall_score_macro para previsões aleatórias (ou seja, R = 0,5 para classificação binária e R = (1/C) para problemas de classificação de classe C).|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "macro" |
precision_score_macro|Precisão é a porcentagem de elementos rotulados como uma determinada classe que realmente estão nessa classe. Macro é a média aritmética de precisão para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Average = "macro"|
precision_score_micro|Precisão é a porcentagem de elementos rotulados como uma determinada classe que realmente estão nessa classe. O micro é computado globalmente, contando os verdadeiros positivos totais e os falsos positivos.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|Average = "micro"|
precision_score_weighted|Precisão é a porcentagem de elementos rotulados como uma determinada classe que realmente estão nessa classe. Ponderado é a média aritmética de precisão para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média = "ponderado"|
recall_score_macro|Recall é a porcentagem de elementos na verdade em uma determinada classe que é rotulada corretamente. Macro é a média aritmética de recall para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "macro"|
recall_score_micro|Recall é a porcentagem de elementos na verdade em uma determinada classe que é rotulada corretamente. O micro é calculado globalmente contando o total de positivos verdadeiros, falsos negativos e falsos positivos|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "micro"|
recall_score_weighted|Recall é a porcentagem de elementos na verdade em uma determinada classe que é rotulada corretamente. Ponderado é a média aritmética de recall para cada classe, ponderada pelo número de instâncias true em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "ponderado"|
weighted_accuracy|A precisão ponderada é a precisão em que o peso dado a cada exemplo é igual à proporção de instâncias verdadeiras na classe verdadeira do exemplo.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight é um vetor igual à proporção dessa classe para cada elemento no destino|

### <a name="confusion-matrix"></a>Matriz de confusão

Uma matriz de confusão é usada para descrever o desempenho de um modelo de classificação. Cada linha exibe as instâncias da classe true e cada coluna representa as instâncias da classe prevista. A matriz de confusão mostra os rótulos classificados corretamente e os rótulos classificados incorretamente para um determinado modelo.

Para problemas de classificação, Azure Machine Learning automaticamente fornece uma matriz de confusão para cada modelo criado. Para cada matriz de confusão, o ML automatizado mostrará a frequência de cada rótulo previsto e de cada interseção de rótulo verdadeiro. Quanto mais escura a cor, maior a contagem nessa parte específica da matriz. O ideal é que as cores mais escuras sejam ao longo da diagonal da matriz. 

Exemplo 1: um modelo de classificação com precisão insuficiente ![um modelo de classificação com precisão insuficiente](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

Exemplo 2: um modelo de classificação com alta precisão (ideal) ![um modelo de classificação com alta precisão](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)


### <a name="precision-recall-chart"></a>Gráfico de recall de precisão

Com esse gráfico, você pode comparar as curvas de recall de precisão de cada modelo para determinar qual modelo tem uma relação aceitável entre precisão e recall para seu problema comercial específico. Este gráfico mostra a macro de precisão média-recall, micro média de recall de precisão e a recall de precisão associada a todas as classes de um modelo.

A precisão do termo representa essa capacidade para um classificador rotular todas as instâncias corretamente. Recall representa a capacidade de um classificador localizar todas as instâncias de um rótulo específico. A curva de recall de precisão mostra a relação entre esses dois conceitos. O ideal é que o modelo tenha 100% de precisão e 100% de precisão.

Exemplo 1: um modelo de classificação com baixa precisão e baixa recall ![um modelo de classificação com baixa precisão e baixa recall](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

Exemplo 2: um modelo de classificação com aproximadamente 100% de precisão e ~ 100% recall (ideal) ![um modelo de classificação alta precisão e RECALL](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

### <a name="roc"></a>ROC

A característica operacional do receptor (ou ROC) é uma plotagem dos rótulos corretamente classificados versus os rótulos classificados incorretamente para um modelo específico. A curva ROC pode ser menos informativa ao treinar modelos em conjuntos de valores com alta tendência, pois ele não mostrará os rótulos falsos positivos.

Exemplo 1: um modelo de classificação com rótulos verdadeiros baixos e alto rótulos falsos ![modelo de classificação com rótulos verdadeiros de baixo e grandes rótulos de falsos](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

Exemplo 2: um modelo de classificação com alto rótulos verdadeiros e rótulos falso baixo ![um modelo de classificação com rótulos verdadeiros de alta e baixa de rótulos falsos](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)

### <a name="lift-curve"></a>Curva de comparação de precisão

Você pode comparar a elevação do modelo criado automaticamente com Azure Machine Learning para a linha de base para exibir a conquista do valor desse modelo específico.

Os gráficos de comparação de precisão são usados para avaliar o desempenho de um modelo de classificação. Ele mostra o quanto melhor você pode esperar fazer com um modelo comparado a sem um modelo. 

Exemplo 1: o modelo tem um desempenho pior do que um modelo de seleção aleatório ![um modelo de classificação que faz pior do que um modelo de seleção aleatório](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

Exemplo 2: o modelo tem um desempenho melhor do que um modelo de seleção aleatório ![um modelo de classificação que executa melhor](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

### <a name="gains-curve"></a>Ganha curva

Um gráfico de ganhos avalia o desempenho de um modelo de classificação por cada parte dos dados. Ele mostra para cada percentil do conjunto de dados, quanto melhor você pode esperar para executar em comparação com um modelo de seleção aleatório.

Use o gráfico ganhos cumulativos para ajudá-lo a escolher o corte de classificação usando uma porcentagem que corresponde a um ganho desejado do modelo. Essas informações fornecem outra maneira de examinar os resultados no gráfico de comparação de precisão.

Exemplo 1: um modelo de classificação com um mínimo de lucro ![um modelo de classificação com o mínimo de lucro](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

Exemplo 2: um modelo de classificação com um lucro significativo ![um modelo de classificação com um lucro significativo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

### <a name="calibration-plot"></a>Plotagem de calibragem

Para todos os problemas de classificação, você pode examinar a linha de calibragem para micro-Average, média de macro e cada classe em um determinado modelo de previsão. 

Um gráfico de calibragem é usado para exibir a confiança de um modelo de previsão. Ele faz isso mostrando a relação entre a probabilidade prevista e a probabilidade real, em que "probabilidade" representa a probabilidade de uma determinada instância pertencer a algum rótulo. Um modelo bem calibrado se alinha com a linha y = x, em que é razoavelmente confiante em suas previsões. Um modelo com excesso de confiança se alinha com a linha y = 0, em que a probabilidade prevista está presente, mas não há nenhuma probabilidade real.

Exemplo 1: um modelo mais bem calibrado ![ um modelo mais bem calibrado](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

Exemplo 2: um modelo mais confiável ![um modelo de confiança excessiva](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Resultados da regressão

Três as métricas e os gráficos a seguir estão disponíveis para cada modelo de regressão que você cria usando os recursos de aprendizado de máquina automatizados do Azure Machine Learning

+ [Métricas](#reg-metrics)
+ [Previsto versus verdadeiro](#pvt)
+ [Histograma de resíduos](#histo)


### <a name="reg-metrics"></a>Métricas de regressão

As métricas a seguir são salvas em cada iteração de execução para uma tarefa de regressão ou de previsão.

|Métrica|Descrição|Cálculo|Parâmetros extras
--|--|--|--|
explained_variance|A variação explicada é a proporção pela qual um modelo matemático conta para a variação de um determinado conjunto de dados. É a diminuição percentual da variação dos dados originais na variação dos erros. Quando a média dos erros é 0, ela é igual à variância explicada.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Nenhum|
r2_score|R2 é o coeficiente de determinação ou a redução de porcentagem em erros quadrados em comparação com um modelo de linha de base que gera a média. |[Cálculo](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Nenhum|
spearman_correlation|A correlação de Spearman é uma medida nonparametric do monotônico da relação entre dois conjuntos de valores. Ao contrário da correlação de Pearson, a correlação de Spearman não pressupõe que os dois conjuntos de linhas normalmente são distribuídos. Assim como outros coeficientes de correlação, esse um varia entre-1 e + 1, com 0 significando que não há correlação. Correlações de-1 ou + 1 implicam uma relação de monotônico exata. As correlações positivas significam que, à medida que x aumenta, o s. As correlações negativas significam que, à medida que x aumenta, y diminui.|[Cálculo](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Nenhum|
mean_absolute_error|Erro de média absoluta é o valor esperado do valor absoluto da diferença entre o destino e a previsão|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Nenhum|
normalized_mean_absolute_error|Erro absoluto médio normalizado é o erro absoluto médio dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Dividir por intervalo de dados|
median_absolute_error|O erro médio absoluto é a mediana de todas as diferenças absolutas entre o destino e a previsão. Essa perda é robusta para exceções.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Nenhum|
normalized_median_absolute_error|Erro absoluto de mediana normalizada é o erro absoluto médio dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Dividir por intervalo de dados|
root_mean_squared_error|Erro de quadrado da média de raiz é a raiz quadrada da diferença quadrada esperada entre o destino e a previsão|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Nenhum|
normalized_root_mean_squared_error|Erro de quadrado da média de raiz normalizada é um erro de raiz quadrada média dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Dividir por intervalo de dados|
root_mean_squared_log_error|Erro de log no quadrado da média raiz é a raiz quadrada do erro logarítmica de quadrado esperado|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Nenhum|
normalized_root_mean_squared_log_error|O erro de log quadrado de raiz normalizada é o erro de log de quadrado médio raiz dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Dividir por intervalo de dados|

### <a name="pvt"></a>Previsto versus verdadeiro

Previsto versus verdadeiro mostra a relação entre um valor previsto e seu valor verdadeiro correlacionado para um problema de regressão. Esse grafo pode ser usado para medir o desempenho de um modelo à medida que mais próximo da linha y = x os valores previstos são, melhor a precisão de um modelo de previsão.

Após cada execução, você pode ver um grafo previsto versus verdadeiro para cada modelo de regressão. Para proteger a privacidade de dados, os valores são compartimentalizados juntos e o tamanho de cada compartimento é mostrado como um gráfico de barras na parte inferior da área do gráfico. Você pode comparar o modelo de previsão, com a área de sombreamento mais clara mostrando margens de erro, em relação ao valor ideal de onde o modelo deve ser.

Exemplo 1: um modelo de regressão com baixa precisão em previsões ![um modelo de regressão com baixa precisão nas previsões](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

Exemplo 2: um modelo de regressão com alta precisão em suas previsões [![um modelo de regressão com alta precisão em suas previsões](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Histograma de resíduos

Um resíduo representa um y observado – o y previsto. Para mostrar uma margem de erro com baixo ajuste, o histograma de resíduos deve ser moldado como uma curva de sino, centralizada em cerca de 0. 

Exemplo 1: um modelo de regressão com tendência em seus erros ![modelo de regressão de SA com tendência em seus erros](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

Exemplo 2: um modelo de regressão com uma distribuição mais uniforme de erros ![um modelo de regressão com mais distribuição uniforme de erros](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>Importância da interpretação do modelo e do recurso

A importância dos recursos permite que você veja quanto valor cada recurso estava na construção de um modelo. Esse cálculo é desativado por padrão, pois pode aumentar significativamente o tempo de execução.   Você pode habilitar a explicação do modelo para todos os modelos ou explicar apenas o modelo de melhor ajuste.

Você pode examinar a pontuação de importância do recurso para o modelo geral, bem como por classe em um modelo de previsão. Você pode ver por recurso como a importância é comparada em cada classe e em geral.

![Habilidade de explicação do recurso](./media/how-to-understand-automated-ml/feature-importance.gif)

Para obter mais informações sobre como habilitar recursos de interpretação, consulte [como](how-to-machine-learning-interpretability-automl.md) habilitar a interpretabilidade em experimentos de ml automatizados.

## <a name="next-steps"></a>Passos seguintes

+ Saiba mais sobre o [ml automatizado](concept-automated-ml.md) no Azure Machine Learning.
+ Experimente o bloco de anotações de exemplo [explicação do modelo de Machine Learning automatizado](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .
