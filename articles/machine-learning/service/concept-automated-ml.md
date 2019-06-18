---
title: Seleção de algoritmo ML automatizado e Otimização
titleSuffix: Azure Machine Learning service
description: Saiba como serviço Azure Machine Learning pode automaticamente escolher um algoritmo para e gerar um modelo a partir do mesmo a economizar tempo, usando os parâmetros e os critérios fornecidos para selecionar o melhor algoritmo para o seu modelo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: 1dcdbbf0a2a71fa38b6eacd6a8d179cdad979937
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059286"
---
# <a name="what-is-automated-machine-learning"></a>O que é automatizado aprendizagem automática?

Automatizada aprendizagem automática, também conhecido como ML automatizado, é o processo de automatizar as tarefas demoradas e iterativas de desenvolvimento de modelos de aprendizagem automática. Ele permite que os cientistas de dados, os analistas e desenvolvedores criar modelos de ML com escala elevada, eficiência e produtividade enquanto estiverem a qualidade dos modelos.

Máquina tradicional, desenvolvimento de modelos de aprendizagem é com muitos recursos, que requerem o conhecimento do domínio significativo e a hora para produzir e comparar dezenas de modelos. Aplicam-se ML automatizado quando pretender que o Azure Machine Learning para treinar e otimizar um modelo para si com a métrica de destino que especificar. O serviço, em seguida, itera através de algoritmos de ML emparelhados com seleções de funcionalidade, em que cada iteração produz um modelo com uma pontuação de treinamento. Maior a pontuação, melhor o modelo é considerado "encaixe" seus dados.

Com a aprendizagem automática, que irá acelerar o tempo que demora a obter modelos de ML prontos para produção com grande facilidade e eficiência.

## <a name="when-to-use-automated-ml"></a>Quando utilizar o ML automatizada

ML automatizada democratiza a processo de desenvolvimento do modelo de aprendizagem automática e permite aos seus utilizadores, independentemente de sua experiência de ciência de dados, para identificar um pipeline de aprendizado de máquina de ponto-a-ponto para qualquer problema.

Os cientistas de dados, os analistas e os desenvolvedores mais variadas indústrias, podem utilizar ML automatizado para:

+ Implementar soluções de aprendizagem automática sem o conhecimento de programação extensa
+ Poupe tempo e recursos
+ Tirar partido das práticas recomendadas de ciência de dados
+ Fornecer agile solução de problemas

## <a name="how-automated-ml-works"></a>Funciona como automatizada de ML

Usando **serviço Azure Machine Learning**, criar e executar as suas experiências de treinamento de ML automatizadas com estes passos:

1. **Identificar o problema de ML** a ser solucionado: classificação, previsão ou regressão

1. **Especificar a origem e o formato dos dados de treinamento etiquetadas**: Matrizes de Numpy ou Pandas dataframe

1. **Configurar o destino de computação para a preparação de modelos**, tais como sua [computador local, Azure Machine Learning computa, VMs remotas ou do Azure Databricks](how-to-set-up-training-targets.md).  Saiba mais sobre treinamento automatizado [num recurso remoto](how-to-auto-train-remote.md).

1. **Configurar a parâmetros de aprendizagem automatizada** que determinam o número de iterações através de modelos diferentes, as definições de hiper-parâmetros, advanced pré-processamento/featurization e que métricas examinar ao determinar o melhor modelo.  Pode configurar as definições para experimentação de preparação automático [no portal do Azure](how-to-create-portal-experiments.md) ou [com o SDK](how-to-configure-auto-train.md).

1. **Submeta o treinamento ser executado.**

  ![Aprendizagem automática](./media/how-to-automated-ml/automl-concept-diagram2.png)

Durante o treinamento, o serviço Azure Machine Learning cria um número de nos pipelines paralelos que tenta parâmetros e algoritmos diferentes. Ele irá parar assim que chegar a critérios de saída definidos na experimentação.

Também pode inspecionar as informações de execução com sessão iniciada, que contém a métricas recolhidas durante a execução. A execução de treinamento produz um objeto de Python serializados (`.pkl` ficheiro) que contém o modelo e, em seguida, o pré-processamento de dados.

Enquanto a criação de modelo é automatizada, também pode [Saiba como importantes ou relevantes recursos são](how-to-configure-auto-train.md#explain) para os modelos gerados.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="time-series-forecasting"></a>Previsão de séries de tempo
Criação de previsões é parte integrante de qualquer empresa, quer seja a pedido de receita, inventário, vendas ou cliente. Automatizado ml utiliza uma série de combinado técnicas e abordagens para recomendar uma série de tempo de alta qualidade de previsão. Série de tempo de experimentações no ml automatizada são tratadas como um problema de multivariada. Últimos séries de tempo valores são "articulados" para se tornar dimensões adicionais para o regressor foi, juntamente com outros indicadores. 

Esta abordagem, ao contrário dos métodos de série de tempo clássicas, tem uma vantagem de incorporação naturalmente de várias variáveis contextuais e a respetiva relação entre si durante o treinamento. Em aplicativos de previsão do mundo real, vários fatores podem influenciar uma previsão. Por exemplo, quando a previsão de vendas, interações de tendências históricas, taxa de câmbio e preço todos os unidade em conjunto, o resultado de vendas. Um benefício adicional é que todas as recentes inovações feitas em modelos de regressão aplicam-se imediatamente a previsão.

Até que ponto para o futuro a previsão deverão expandir-se (horizonte previsão) faz parte da especificação de previsão básica. Definir o parâmetro necessário de `max_horizon` na experimentação define o número de períodos de unidade (com base no intervalo de tempo dos seus dados de preparação, semanais, mensais por exemplo, o forecaster deve prever horizontalmente. 

ML automatizada aprende um modelo único, mas, muitas vezes, internamente, ser também autor para todos os itens no horizontes de conjunto de dados e de predição. Mais dados, portanto, estão disponíveis para estimar os parâmetros de modelo e generalização a série que não foram visto torna-se possível. 

Recursos extraídos dos dados de treinamento têm um papel fundamental. ML automatizada efetua os passos de pré-processamento padrão e gera as funcionalidades de séries de tempo adicionais (por exemplo, ano, mês, dia da semana, etc.) para capturar os efeitos sazonais e maximizar a precisão de previsão. 

Se é apropriado para o seu cenário, pode direcionar ML automatizada para criar lags (`target_lags`) ou janela sem interrupção de agregação de dados (`target_rolling_window_size`) do seu destino (`y_value`) últimos valores. 

## <a name="preprocessing"></a>O pré-processamento

Em cada automatizada experimentação do machine learning, os seus dados são processados usando os métodos padrão e, opcionalmente, por meio de avançadas processamento prévio.

### <a name="automatic-preprocessing-standard"></a>Automático pré-processamento (padrão)

Em cada automatizada experimentação do machine learning, os seus dados é automaticamente dimensionados ou normalizados para ajudar a algoritmos de bom desempenho.  Durante a preparação de modelos, uma das seguintes técnicas de dimensionamento ou a normalização será aplicada a cada modelo.

|Dimensionar&nbsp;&&nbsp;normalização| Descrição |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Padronizar funcionalidades ao remover a média e dimensionar a variância de unidade  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transforma funcionalidades através do dimensionamento cada funcionalidade por essa coluna mínimo e máximo  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Aumente a cada funcionalidade, o valor absoluto máximo |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Esta funcionalidade de Scaler pelo respetivo intervalo quantile |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Redução de dimensionalidade linear com Singular decomposição de valor dos dados para o project-lo para um espaço de dimensional inferior |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Este transformador executa uma redução de dimensionalidade linear por meio de decomposição de valor único truncado (programa). Ao contrário do PCA, este vistoriador center não os dados antes de computação a decomposição de valor único. Isso significa que ele pode trabalhar com matrizes scipy.sparse com eficiência |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Cada exemplo (ou seja, cada linha da matriz de dados) pelo menos um componente diferente de zero é novamente dimensionado independentemente dos outros exemplos, para que seu norma (l1 ou erros de l2) é igual a um |

### <a name="advanced-preprocessing-optional-featurization"></a>Advanced pré-processamento: featurization opcional

O pré-processamento avançadas adicionais e featurization também estão disponíveis, como em falta imputation de valores, codificação de mensagens em fila e transformações. [Saiba mais sobre o que featurization está incluído](how-to-create-portal-experiments.md#preprocess). Ative esta definição com:

+ Portal do Azure: Selecionar o **Preprocess** caixa de seleção o **definições avançadas** [com estes passos](how-to-create-portal-experiments.md).

+ Python SDK: Especificar `"preprocess": True` para o [ `AutoMLConfig` classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

## <a name="ensemble-models"></a>Modelos de Ensemble

Pode preparar os modelos de ensemble com aprendizagem automática com o [algoritmo de seleção de ensemble Caruana com a inicialização de Ensemble classificada](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). Ensemble learning melhora os resultados do machine learning e o desempenho de previsão ao combinam muitos modelos em vez de utilizar os modelos de únicos. A iteração ensemble é apresentado como a última iteração de sua execução.

## <a name="training-metric-output"></a>Saída de métrica de treinamento

Existem várias formas de visualizar as métricas de precisão de treinamento para cada iteração de execução.

* Utilize o widget do Jupyter.
* Utilize o `get_metrics()` função em qualquer `Run` objeto.
* Ver as métricas no portal do Azure na sua experimentação.

### <a name="classification-metrics"></a>Métricas de classificação

As métricas seguintes são salvas em cada iteração de execução de uma tarefa de classificação.

|Métrica|Descrição|Cálculo|Parâmetros adicionais
--|--|--|--|
AUC_Macro| AUC é a área sob a curva de característica do Recetor operacional. Macro é a média aritmética de AUC para cada classe.  | [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | média = "macro"|
AUC_Micro| AUC é a área sob a curva de característica do Recetor operacional. Micro é computada globalmente ao combinar os positivos verdadeiros e falsos positivos de cada classe| [Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | média = "micro"|
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

### <a name="regression-and-forecasting-metrics"></a>Regressão e métricas de previsão

As métricas seguintes são salvas em cada iteração de execução para uma regressão ou a tarefa de previsão.

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
normalized_root_mean_squared_log_error|Erro de registo ao quadrado mean normalizado raiz é dividido pelo intervalo dos dados de erro do registo ao quadrado médio de raiz|[Cálculo](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Divida por intervalo dos dados|


## <a name="use-with-onnx-in-c-apps"></a>Utilize com ONNX no C# aplicações

Com o Azure Machine Learning, pode utilizar o ML automatizada para criar um modelo de Python e fazer com que ele convertido em formato ONNX. O tempo de execução ONNX suporta C#, por isso, pode usar o modelo criado automaticamente no seu C# aplicações sem qualquer necessidade de qualquer recodificação ou qualquer um das latências de rede que apresentam os pontos finais REST. Experimente um exemplo deste fluxo [neste bloco de notas do Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>ML automatizada da Microsoft

ML automatizada também está disponível em outras soluções da Microsoft, tais como:

|Integrações|Descrição|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Seleção automática de modelos e formação em aplicações .NET com o Visual Studio e Visual Studio Code com ML.NET automatizadas ML (pré-visualização).|
|[HDIsnight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|Aumentar horizontalmente as suas tarefas de formação de ML automáticas, no Spark em clusters do HDInsight em paralelo.|
|[PowerBI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Invoca os modelos de aprendizagem automática diretamente no Power BI (pré-visualização).|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|Crie novos modelos de aprendizagem sobre os seus dados em clusters de grandes volumes de dados do SQL Server 2019.|

## <a name="next-steps"></a>Passos Seguintes

Ver exemplos e saiba como criar modelos com o Machine Learning automatizada:

+ Siga o [Tutorial: Preparar automaticamente um modelo de classificação com o Azure automatizada Machine Learning](tutorial-auto-train-models.md)

+ Configure as definições para experimentação de preparação automático:
  + Na interface do portal do Azure, [Utilize estes passos](how-to-create-portal-experiments.md).
  + Com o SDK de Python [Utilize estes passos](how-to-configure-auto-train.md).

+ Saiba como automaticamente train com dados de séries de tempo [Utilize estes passos](how-to-auto-train-forecast.md).

+ Experimentar o [exemplos de bloco de notas do Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
