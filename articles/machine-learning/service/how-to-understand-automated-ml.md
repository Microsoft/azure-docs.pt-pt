---
title: Entender os resultados de ML automatizados
titleSuffix: Azure Machine Learning service
description: Saiba como exibir e entender os gráficos e as métricas para cada uma das execuções automatizadas do Machine Learning.
services: machine-learning
author: nilesha
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: d626a7768e7070360a6f0249486bfa83407fe998
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70192052"
---
# <a name="understand-automated-machine-learning-results"></a>Entender os resultados automatizados do Machine Learning

Neste artigo, você aprenderá a exibir e a entender os gráficos e as métricas de cada uma das execuções automatizadas do Machine Learning. 

Saiba mais sobre:
+ [Métricas, gráficos e curvas para modelos de classificação](#classification)
+ [Métricas, gráficos e grafos para modelos de regressão](#regression)
+ [Importância da interpretação do modelo e do recurso](#explain-model)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do serviço de Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* Crie um experimento de Machine Learning automatizado, seja com o SDK ou na portal do Azure.

    * Usar o SDK para criar um modelo de [classificação](how-to-auto-train-remote.md) ou um [modelo](tutorial-auto-train-models.md) de regressão
    * Use o [portal do Azure](how-to-create-portal-experiments.md) para criar um modelo de classificação ou regressão carregando os dados apropriados.

## <a name="view-the-run"></a>Exibir a execução

Depois de executar um experimento de aprendizado de máquina automatizado, um histórico das execuções pode ser encontrado em seu espaço de trabalho do serviço de Machine Learning. 

1. Vá para a área de trabalho.

1. No painel esquerdo do espaço de trabalho, selecione **experimentos**.

   ![Captura de ecrã do menu de experimentação](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. Na lista de experimentos, selecione aquele que você deseja explorar.

   [![Lista de experimentos](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. Na tabela inferior, selecione o **número de execução**.

   Execução do experimento) [ ![](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png)

1. Na tabela iterações, selecione o **número** de iteração para o modelo que você deseja explorar ainda mais.

   [![Modelo de experimento](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

Você também vê esses mesmos resultados durante uma execução quando usa o `RunDetails` [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).

## <a name="classification"></a>Resultados da classificação

Três as métricas e os gráficos a seguir estão disponíveis para cada modelo de classificação que você cria usando os recursos de aprendizado de máquina automatizados do Azure Machine Learning

+ [Métricas](#classification-metrics)
+ [Matriz de confusão](#confusion-matrix)
+ [Gráfico de Recolhimento de precisão](#precision-recall-chart)
+ [Recetor operacional características (ou ROC)](#roc)
+ [Curva de comparação de precisão](#lift-curve)
+ [Curva de ganhos](#gains-curve)
+ [Desenho de calibragem](#calibration-plot)

### <a name="classification-metrics"></a>Métricas de classificação

As métricas a seguir são salvas em cada iteração de execução para uma tarefa de classificação.

|Métrica|Descrição|Cálculo|Parâmetros adicionais
--|--|--|--|
AUC_Macro| AUC é a área sob a curva de característica do Recetor operacional. Macro é a média aritmética de AUC para cada classe.  | [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | média = "macro"|
AUC_Micro| AUC é a área sob a curva de característica do Recetor operacional. O micro é calculado globalmente combinando os verdadeiros positivos e falsos positivos de cada classe.| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | média = "micro"|
AUC_Weighted  | AUC é a área sob a curva de característica do Recetor operacional. Ponderado é a média aritmética da Pontuação de cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|média = "ponderado"
accuracy|A precisão é a porcentagem de etiquetas previstas que correspondam exatamente com as etiquetas verdadeiras. |[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Nenhuma|
average_precision_score_macro|Precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisions alcançados em cada limite, com o aumento de Lembre-se de que o limiar anterior utilizado como o peso. Macro é a média aritmética da Pontuação de precisão média de cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|média = "macro"|
average_precision_score_micro|Precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisions alcançados em cada limite, com o aumento de Lembre-se de que o limiar anterior utilizado como o peso. O micro é calculado globalmente combinando os verdadeiros positivos e falsos positivos em cada corte.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|média = "micro"|
average_precision_score_weighted|Precisão média resume uma curva de recolhimento de precisão como a média ponderada de precisions alcançados em cada limite, com o aumento de Lembre-se de que o limiar anterior utilizado como o peso. Ponderado é a média aritmética da Pontuação de precisão média para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|média = "ponderado"|
balanced_accuracy|Precisão equilibrada é a média aritmética de remoção para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "macro"|
f1_score_macro|F1 pontuação é a média harmônica de precisão e lembre-se. Macro é a média aritmética da Pontuação F1 para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|média = "macro"|
f1_score_micro|F1 pontuação é a média harmônica de precisão e lembre-se. O micro é calculado globalmente contando o total de positivos verdadeiros, falsos negativos e falsos positivos.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|média = "micro"|
f1_score_weighted|F1 pontuação é a média harmônica de precisão e lembre-se. Média ponderada pela frequência de classe de pontuação F1 para cada classe|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|média = "ponderado"|
log_loss|Esta é a função de perda utilizada regressão logística de (multinomial) e extensões do mesmo como redes neurais, definidas como a probabilidade de registo de negativa das etiquetas verdadeiras tendo em conta as previsões de um classificador probabilístico. Para um único exemplo com verdadeiro rótulo YT {0,1} e YP de probabilidade estimada que YT = 1, a perda de log é-log P (YT&#124;YP) =-(YT log (YP) + (1-YT) log (1-YP)).|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Nenhuma|
norm_macro_recall|Normalizado Macro Lembre-se de é lembrar de Macro normalizados de modo a que o desempenho aleatório tem uma pontuação igual a 0 e desempenho perfeito tem uma pontuação de 1. Isso é obtido por norm_macro_recall: = (recall_score_macro-R)/(1-R), em que R é o valor esperado de recall_score_macro para previsões aleatórias (ou seja, R = 0,5 para classificação binária e R = (1/C) para problemas de classificação de classe C).|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|Average = "macro" |
precision_score_macro|Precisão é a percentagem de elementos identificados como uma determinada classe, que, na verdade, estão nessa classe. Macro é a média aritmética de precisão para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média = "macro"|
precision_score_micro|Precisão é a percentagem de elementos identificados como uma determinada classe, que, na verdade, estão nessa classe. O micro é computado globalmente, contando os verdadeiros positivos totais e os falsos positivos.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média = "micro"|
precision_score_weighted|Precisão é a percentagem de elementos identificados como uma determinada classe, que, na verdade, estão nessa classe. Ponderado é a média aritmética de precisão para cada classe, ponderada pelo número de instâncias verdadeiras em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|média = "ponderado"|
recall_score_macro|Lembre-se é a percentagem de elementos, na verdade, numa determinada classe, que estão identificadas corretamente. Macro é a média aritmética de recall para cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "macro"|
recall_score_micro|Lembre-se é a percentagem de elementos, na verdade, numa determinada classe, que estão identificadas corretamente. O micro é calculado globalmente contando o total de positivos verdadeiros, falsos negativos e falsos positivos|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "micro"|
recall_score_weighted|Lembre-se é a percentagem de elementos, na verdade, numa determinada classe, que estão identificadas corretamente. Ponderado é a média aritmética de recall para cada classe, ponderada pelo número de instâncias true em cada classe.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|média = "ponderado"|
weighted_accuracy|A precisão ponderada é a precisão em que o peso dado a cada exemplo é igual à proporção de instâncias verdadeiras na classe verdadeira do exemplo.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight é um vetor igual a proporção dessa classe para cada elemento no destino|

### <a name="confusion-matrix"></a>Matriz de confusão

Uma matriz de confusão é usada para descrever o desempenho de um modelo de classificação. Cada linha apresenta as instâncias da classe verdadeira, e cada coluna representa as instâncias da classe prevista. A matriz de confusão mostra as etiquetas classificadas corretamente e as etiquetas classificadas incorretamente de um modelo específico.

Para problemas de classificação, o Azure Machine Learning fornece automaticamente uma matriz de confusão para cada modelo que foi criado. Para cada matriz de confusão, o ML automatizado mostrará a frequência de cada rótulo previsto e de cada interseção de rótulo verdadeiro. Quanto mais escura a cor, maior a contagem nessa parte específica da matriz. O ideal é que as cores mais escuras sejam ao longo da diagonal da matriz. 

Exemplo 1: Um modelo de classificação com uma ![precisão ruim de um modelo de classificação com precisão insuficiente](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

Exemplo 2: Um modelo de classificação com alta precisão (ideal ![) um modelo de classificação com alta precisão](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)


### <a name="precision-recall-chart"></a>Gráfico de recolhimento de precisão

Com este gráfico, pode comparar as curvas de recolhimento de precisão para cada modelo determinar qual modelo tem uma relação aceitável entre precisão e lembre-se para o seu problema comercial em particular. Este gráfico mostra o Recolhimento de precisão de média Macro, o Recolhimento de precisão de média Micro e o recolhimento de precisão associado com todas as classes para um modelo.

O termo que precisão representa essa habilidade para um classificador que identifique todas as instâncias corretamente. Lembre-se representa a capacidade para um classificador localizar todas as instâncias de uma etiqueta específica. A curva de recolhimento de precisão mostra a relação entre esses dois conceitos. O ideal é que o modelo teria a precisão de 100% e a precisão de 100%.

Exemplo 1: Um modelo de classificação com baixa precisão e baixa ![recuperação de um modelo de classificação com baixa precisão e baixa recall](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

Exemplo 2: Um modelo de classificação com aproximadamente 100% de precisão e ~ 100% recall ![(ideal) um modelo de classificação alta precisão e RECALL](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

### <a name="roc"></a>COR MULTICLASSE

Recetor operacional característica (ou ROC) é um desenho das etiquetas classificadas corretamente vs. as etiquetas classificadas incorretamente para um determinado modelo. A curva cor MULTICLASSE pode ser menos informativa quando os modelos de formação em conjuntos de dados com tendência alto, como ele não mostrará as etiquetas de positivas FALSO.

Exemplo 1: Um modelo de classificação com baixos rótulos verdadeiros e alto modelo ![de classificação de rótulos falsos com baixos rótulos verdadeiros e grandes rótulos falsos](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

Exemplo 2: Um modelo de classificação com grandes rótulos verdadeiros e baixos rótulos ![falsos um modelo de classificação com rótulos verdadeiros de alta e baixa de rótulos falsos](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)

### <a name="lift-curve"></a>Curva de comparação de precisão

Pode comparar a comparação de precisão do modelo criado automaticamente com o Azure Machine Learning para a linha de base para visualizar o ganho de valor desse modelo específico.

Gráficos de comparação de precisão são utilizados para avaliar o desempenho de um modelo de classificação. Mostra quanto melhor pode esperar fazer com um modelo em comparação comparado sem um modelo. 

Exemplo 1: O modelo tem ![um desempenho pior do que um modelo de seleção aleatório um modelo de classificação que faz pior do que um modelo de seleção aleatório](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

Exemplo 2: O modelo tem um desempenho melhor do que ![um modelo de seleção aleatório um modelo de classificação que é executado melhor](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

### <a name="gains-curve"></a>Curva de ganhos

Um gráfico de ganhos avalia o desempenho de um modelo de classificação por cada parte dos dados. Ela mostra para cada percentil do conjunto de dados, quanto melhor pode esperar efetuar comparado com um modelo de seleção aleatória.

Utilize o gráfico de quadro de ganhos cumulativos para ajudar a escolher o limite classificação usando uma percentagem que corresponde a um ganho de pretendida do modelo. Estas informações proporciona outra forma de ver os resultados num gráfico de comparação de precisão que acompanha este artigo.

Exemplo 1: Um modelo de classificação com um ![mínimo de obter um modelo de classificação com o mínimo de lucro](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

Exemplo 2: Um modelo de classificação com obter ![um modelo de classificação significativo com um lucro significativo](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

### <a name="calibration-plot"></a>Desenho de calibragem

Para todos os problemas de classificação, pode rever a linha de calibração para cada classe num determinado modelo preditivo, média de macro e micro-média. 

Um desenho de calibragem é utilizado para apresentar a confiança de um modelo preditivo. Ele faz isso ao mostrar a relação entre a probabilidade prevista e a probabilidade real, em que "probabilidade" representa a probabilidade de que uma instância específica pertence alguns da etiqueta. Um modelo calibrated também se alinha com a y = x linha, onde é razoavelmente confiante em sua previsões. Um modelo de confident excessiva se alinha com a y = 0 linha, em que a probabilidade prevista está presente, mas não existe nenhuma probabilidade real.

Exemplo 1: Um modelo mais ![ bem calibrado mais bem-sulibrado](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

Exemplo 2: Um modelo mais seguro ![de um modelo com excesso de confiança](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression"></a>Resultados da regressão

Três as métricas e os gráficos a seguir estão disponíveis para cada modelo de regressão que você cria usando os recursos de aprendizado de máquina automatizados do Azure Machine Learning

+ [Métricas](#reg-metrics)
+ [Vs previstas. VERDADEIRO](#pvt)
+ [Histograma da residuals](#histo)


### <a name="reg-metrics"></a>Métricas de regressão

As métricas a seguir são salvas em cada iteração de execução para uma tarefa de regressão ou de previsão.

|Métrica|Descrição|Cálculo|Parâmetros adicionais
--|--|--|--|
explained_variance|Variância explicada é a proporção ao qual um modelo matemático contas para a variação de um determinado conjunto de dados. É que a percentagem de diminuir a variação dos dados originais para a variância dos erros. Quando a média dos erros for 0, é igual a variância explicada.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Nenhuma|
r2_score|R2 é o coeficiente de determinação ou a redução de percentagem de erros ao quadrado, em comparação comparada um modelo de linha de base que produz a média. |[Cálculo](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Nenhuma|
spearman_correlation|Correlação de Spearman é uma medida nonparametric de monotonicidade da relação entre dois conjuntos de dados. Ao contrário de correlação Pearson, a correlação de Spearman não partem do princípio de que ambos os conjuntos de dados normalmente são distribuídos. Como outros coeficientes de correlação, isso varia entre -1 e + 1 com 0 indicando sem correlação. Correlações de -1 ou + 1 implicam uma relação de monotónica exata. Correlações positivas implicam que como x aumenta, assim como o y. Correlações negativas implicam que como x aumenta, y diminui.|[Cálculo](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Nenhuma|
mean_absolute_error|Significa erro absoluto é o valor esperado de valor absoluto de diferença entre o destino e a predição|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Nenhuma|
normalized_mean_absolute_error|Erro de absoluto mean normalizado é mean erro absoluto dividida pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Divida por intervalo dos dados|
median_absolute_error|Erro de absoluto mediano é o valor mediano de todas as diferenças absolutas entre o destino e a predição. Esta perda é robusta para valores atípicos.|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Nenhuma|
normalized_median_absolute_error|Erro de absoluto mediano normalizado é mediano erro absoluto dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Divida por intervalo dos dados|
root_mean_squared_error|Raiz significar um erro ao quadrado é a raiz quadrada da diferença ao quadrado esperada entre o destino e a predição|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Nenhuma|
normalized_root_mean_squared_error|Raiz normalizado significar um erro ao quadrado é raiz mean erros dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Divida por intervalo dos dados|
root_mean_squared_log_error|Raiz significa de erro do registo ao quadrado é a raiz quadrada do erro logarítmica ao quadrado esperado|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Nenhuma|
normalized_root_mean_squared_log_error|O erro de log quadrado de raiz normalizada é o erro de log de quadrado médio raiz dividido pelo intervalo dos dados|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Divida por intervalo dos dados|

### <a name="pvt"></a>Previsto versus True

Vs previstas. TRUE mostra a relação entre um valor previsto e o respetivo valor de verdadeiro correlating para um problema de regressão. Este gráfico pode ser utilizado para medir o desempenho de um modelo como o mais perto de y = x linha os valores previstos são, melhor será a precisão de um modelo preditivo.

Após cada execução, pode ver um previstos versus verdadeiro gráfico para cada modelo de regressão. Para proteger a privacidade dos dados, os valores são posicionados em conjunto e o tamanho de cada contentor é mostrado como um gráfico de barras na parte inferior da área do gráfico. Pode comparar o modelo de previsão, com a área de tom mais clara, que mostra de margens de erro, com o valor ideal de onde o modelo deve ser.

Exemplo 1: Um modelo de regressão com baixa precisão em previsões ![um modelo de regressão com baixa precisão nas previsões](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

Exemplo 2: Um modelo de regressão com alta precisão em suas previsões [ ![um modelo de regressão com alta precisão em suas previsões](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histo"></a>Histograma de resíduos

Um residuais representa um y observado – o y previsto. Para mostrar uma margem de erro com tendência baixa, o histograma de residuals deve ser formatado como uma curva de sino centrada em torno de 0. 

Exemplo 1: Um modelo de regressão com tendência em seu ![modelo de regressão de SA de erros com tendência em seus erros](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

Exemplo 2: Um modelo de regressão com uma distribuição mais uniforme ![de erros um modelo de regressão com mais distribuição de erros uniforme](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="explain-model"></a>Importância da interpretação do modelo e do recurso

A importância dos recursos permite que você veja quanto valor cada recurso estava na construção de um modelo. Esse cálculo é desativado por padrão, pois pode aumentar significativamente o tempo de execução.   Você pode habilitar a explicação do modelo para todos os modelos ou explicar apenas o modelo de melhor ajuste.

Pode rever a classificação de importância de funcionalidade para o modelo geral, bem como por classe num modelo preditivo. Pode ver por funcionalidade como a importância se compara em relação a cada classe e geral.

![Capacidade de explicar de funcionalidade](./media/how-to-understand-automated-ml/feature-importance.gif)

Para obter mais informações sobre como habilitar recursos de interpretação, consulte [Configurar experimentos de ml automatizados em Python](how-to-configure-auto-train.md#explain-the-model-interpretability).  Para obter um exemplo que explica o melhor modelo, consulte a [melhor explicação do modelo](how-to-auto-train-remote.md#explain).

## <a name="next-steps"></a>Passos Seguintes

+ Saiba mais sobre o [ml automatizado](concept-automated-ml.md) no Azure Machine Learning.
+ Experimente o bloco de anotações de exemplo [explicação do modelo de Machine Learning automatizado](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .
