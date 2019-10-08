---
title: O que é o ML/automl automatizado
titleSuffix: Azure Machine Learning
description: Saiba como Azure Machine Learning pode escolher automaticamente um algoritmo para você e gerar um modelo a partir dele para poupar tempo usando os parâmetros e critérios que você fornece para selecionar o melhor algoritmo para seu modelo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 06/20/2019
ms.custom: seodec18
ms.openlocfilehash: e9bc0cf3ab0d168a55ab04913bdc71eed5c5fced
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001227"
---
# <a name="what-is-automated-machine-learning"></a>O que é automatizado aprendizagem automática?

O Machine Learning automatizado, também conhecido como ML automatizado, é o processo de automatização das tarefas iterativas demoradas do desenvolvimento de modelos de aprendizado de máquina. Ele permite que cientistas de dados, analistas e desenvolvedores criem modelos de ML com alta escala, eficiência e produtividade, tudo ao mesmo tempo em que sustenta a qualidade do modelo. O ML automatizado se baseia em uma inovação da [divisão do Microsoft Research](https://arxiv.org/abs/1705.05355).

O desenvolvimento de modelos de aprendizado de máquina tradicional tem uso intensivo de recursos, exigindo conhecimento e tempo de domínio significativos para produzir e comparar dezenas de modelos. Aplique ML automatizado quando desejar que Azure Machine Learning treine e ajuste um modelo para você usando a métrica de destino que você especificar. Em seguida, o serviço faz a iteração por meio de algoritmos de ML emparelhados com seleções de recursos, em que cada iteração produz um modelo com uma pontuação de treinamento. Quanto maior a pontuação, melhor o modelo é considerado para "ajustar" seus dados.

Com o aprendizado de máquina automatizado, você acelerará o tempo necessário para obter modelos de ML prontos para produção com grande facilidade e eficiência.

## <a name="when-to-use-automated-ml"></a>Quando usar ML automatizado

Democratiza automatizado o processo de desenvolvimento do modelo do Machine Learning e capacita seus usuários, não importa sua experiência em ciência de dados, para identificar um pipeline de aprendizado de máquina de ponta a ponta para qualquer problema.

Cientistas de dados, analistas e desenvolvedores em setores podem usar ML automatizado para:

+ Implementar soluções de Machine Learning sem amplo conhecimento de programação
+ Economize tempo e recursos
+ Aproveitar as práticas recomendadas de ciência de dados
+ Fornecer solução de problemas ágil

Os casos comuns de uso de ML automatizados incluem:
+ [Detecção de fraudes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)
+ [Previsão de marketing](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing/auto-ml-classification-bank-marketing.ipynb)
+ [Previsão de demanda](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
+ [Previsão de vendas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)
+ [Previsão de desempenho](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance/auto-ml-regression-hardware-performance.ipynb)
+ [Previsão de durabilidade de material](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-concrete-strength/auto-ml-regression-concrete-strength.ipynb)

## <a name="how-automated-ml-works"></a>Como o ML automatizado funciona

Usando **Azure Machine Learning**, você pode projetar e executar seus experimentos de treinamento de ml automatizados com estas etapas:

1. **Identificar o problema de ml** a ser resolvido: classificação, previsão ou regressão

1. **Especifique a origem e o formato dos dados de treinamento rotulados**: Matrizes numpy ou pandas dataframe

1. **Configure o destino de computação para treinamento de modelo**, como seu [computador local, Azure Machine Learning computações, VMS remotas ou Azure Databricks](how-to-set-up-training-targets.md).  Saiba mais sobre o treinamento automatizado [em um recurso remoto](how-to-auto-train-remote.md).

1. **Configure os parâmetros automatizados de aprendizado de máquina** que determinam quantas iterações em diferentes modelos, configurações de hiperparâmetro, pré-processamento avançado/personalização e quais métricas examinar ao determinar o melhor modelo.  Você pode definir as configurações de teste de treinamento automático no [portal do Azure](how-to-create-portal-experiments.md), [a página de aterrissagem do espaço de trabalho (versão prévia)](https://ml.azure.com)ou [com o SDK](how-to-configure-auto-train.md). 

1. **Envie a execução de treinamento.**

  ![Machine Learning automatizado](./media/how-to-automated-ml/automl-concept-diagram2.png)

Durante o treinamento, Azure Machine Learning cria um número de pipelines paralelos que tentam algoritmos e parâmetros diferentes. Ele será interrompido quando atingir os critérios de saída definidos no experimento.

Você também pode inspecionar as informações de execução registradas, que [contêm as métricas](how-to-understand-automated-ml.md) coletadas durante a execução. A execução de treinamento produz um objeto serializado do Python (arquivo `.pkl`) que contém o modelo e o pré-processamento de dados.

Embora a criação de modelos seja automatizada, você também pode [aprender como os recursos importantes ou relevantes são](how-to-configure-auto-train.md#explain) para os modelos gerados.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Pré-processamento

Em todos os experimentos de aprendizado de máquina automatizados, seus dados são pré-processados usando os métodos padrão e, opcionalmente, por meio de pré-processamento avançado.

> [!NOTE]
> As etapas de pré-processamento automatizado do Machine Learning (normalização de recursos, manipulação de dados ausentes, conversão de texto em numeric, etc.) tornam-se parte do modelo subjacente. Ao usar o modelo para previsões, as mesmas etapas de pré-processamento aplicadas durante o treinamento são aplicadas aos dados de entrada automaticamente.

### <a name="automatic-preprocessing-standard"></a>Pré-processamento automático (padrão)

Em todos os experimentos de aprendizado de máquina automatizados, seus dados são dimensionados e normalizados automaticamente para ajudar os algoritmos a executarem bem.  Durante o treinamento do modelo, uma das técnicas de dimensionamento ou normalização a seguir será aplicada a cada modelo.

|Dimensionamento de @ no__t-0 @ no__t-1 @ no__t-2normalization| Descrição |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Padronizar recursos removendo a média e dimensionamento para a variação de unidade  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transforma recursos dimensionando cada recurso pelo mínimo e máximo da coluna  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Dimensionar cada recurso por seu valor absoluto máximo |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Esses recursos de scaler por seu intervalo de Quantil |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Redução de dimensionalidade linear usando a decomposição de valor singular dos dados para projetar a ti em um espaço dimensional inferior |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Esse transformador executa a redução de dimensionalidade linear por meio da decomposição de valor singular truncado (SVD). Ao contrário do PCA, esse estimador não centraliza os dados antes de calcular a decomposição do valor singular. Isso significa que ele pode trabalhar com matrizes SciPy. esparsas com eficiência |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Cada exemplo (ou seja, cada linha da matriz de dados) com pelo menos um componente diferente de zero é redimensionado independentemente de outros exemplos para que seu normal (L1 ou L2) seja igual a um |

### <a name="advanced-preprocessing-optional-featurization"></a>Pré-processamento avançado: personalização opcional

O pré-processamento avançado adicional e os personalização também estão disponíveis, como valores ausentes de imputação, codificação e transformações. [Saiba mais sobre o que o personalização está incluído](how-to-create-portal-experiments.md#preprocess). Habilite essa configuração com:

+ Portal do Azure: Marcando a caixa de seleção **pré-processar** nas **Configurações avançadas** [com estas etapas](how-to-create-portal-experiments.md).

+ SDK do Python: Especificando `"preprocess": True` para a [classe `AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).


## <a name="time-series-forecasting"></a>Previsão da série temporal
A criação de previsões é parte integrante de qualquer empresa, seja ela de receita, inventário, vendas ou demanda do cliente. Você pode usar o ML automatizado para combinar técnicas e abordagens e obter uma previsão de série temporal de alta qualidade e recomendada.

Um experimento de série temporal automatizado é tratado como um problema de regressão multivariado. Os valores de série temporal anteriores são "dinamizados" para se tornarem dimensões adicionais para o regressor junto com outros previsões. Essa abordagem, ao contrário dos métodos de série temporal clássica, tem uma vantagem de incorporar naturalmente várias variáveis contextuais e sua relação entre si durante o treinamento. O ML automatizado aprende um modelo único, mas geralmente ramificado internamente para todos os itens no conjunto de e horizontes de previsão. Por isso, mais dados estão disponíveis para estimar os parâmetros de modelo e a generalização para uma série não vista se torna possível.

Saiba mais e veja um exemplo de [Machine Learning automatizado para previsão de série temporal](how-to-auto-train-forecast.md). Ou então, consulte o [notebook de demanda de energia](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) para obter exemplos de código detalhados de configuração de previsão avançada, incluindo:

* detecção de feriados e personalização
* validação cruzada de origem sem interrupção
* retardo configurável
* recursos agregados de janela sem interrupção

## <a name="ensemble"></a>Modelos de Ensemble

O aprendizado de máquina automatizado dá suporte a modelos Ensemble, que são habilitados por padrão. O Ensemble Learning melhora os resultados do Machine Learning e o desempenho de previsão combinando vários modelos em oposição ao uso de modelos únicos. As iterações Ensemble são exibidas como as iterações finais da sua execução. O Machine Learning automatizado usa os métodos Ensemble de votação e empilhamento para combinar modelos:

* **Votação**: prevê com base na média ponderada das probabilidades de classe prevista (para tarefas de classificação) ou destinos de regressão previstos (para tarefas de regressão).
* **Empilhamento**: a empilhamento combina modelos heterogêneos e treina um meta-modelo com base na saída dos modelos individuais. Os metamodelos padrão atuais são LogisticRegression para tarefas de classificação e ElasticNet para tarefas de regressão/previsão.

O [algoritmo de seleção Caruana Ensemble](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) com inicialização Ensemble classificada é usado para decidir quais modelos usar dentro do Ensemble. Em um alto nível, esse algoritmo Inicializa o Ensemble com até 5 modelos com as melhores pontuações individuais e verifica se esses modelos estão dentro do limite de 5% da melhor pontuação para evitar uma Ensemble inicial inadequada. Em seguida, para cada iteração Ensemble, um novo modelo é adicionado ao Ensemble existente e a pontuação resultante é calculada. Se um novo modelo tiver melhorado a pontuação Ensemble existente, o Ensemble será atualizado para incluir o novo modelo.

Consulte a [instruções](how-to-configure-auto-train.md#ensemble) para alterar as configurações de Ensemble padrão no Machine Learning automatizado.

## <a name="imbalance"></a>Dados desequilibrados

Dados desequilibrados geralmente são encontrados em dados para cenários de classificação de aprendizado de máquina e referem-se a dados que contêm uma proporção desproporcional de observações em cada classe. Esse desequilíbrio pode levar a um efeito positivo falso e perceptível da precisão de um modelo, pois os dados de entrada têm a tendência de uma classe, o que resulta no modelo treinado para imitar essa tendência. 

Como parte de seu objetivo de simplificar o fluxo de trabalho do Machine Learning, o ML automatizado tem recursos internos para ajudar a lidar com dados desequilibrados, como 

- Uma **coluna de peso**: o ml automatizado dá suporte a uma coluna ponderada como entrada, fazendo com que as linhas nos dados sejam ponderadas ou reduzidas, o que pode tornar uma classe mais ou menos "importante". Consulte este [exemplo de bloco de anotações](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/sample-weight/auto-ml-sample-weight.ipynb) 

- Os algoritmos usados pelo ML automatizado podem lidar corretamente com o desequilíbrio de até 20:1, o que significa que a classe mais comum pode ter 20 vezes mais linhas nos dados do que a classe menos comum.

### <a name="identify-models-with-imbalanced-data"></a>Identificar modelos com dados desbalanceado

Como os algoritmos de classificação geralmente são avaliados por exatidão, a verificação da Pontuação de precisão de um modelo é uma boa maneira de identificar se ele foi afetado por dados desequilibrados. Tem realmente alta precisão ou precisão muito baixa para determinadas classes?

Além disso, as execuções automáticas de ML geram automaticamente os gráficos a seguir, o que pode ajudá-lo a entender a exatidão das classificações do modelo e identificar os modelos potencialmente afetados por dados desequilibrados.

Gráfico| Descrição
---|---
[Matriz de confusão](how-to-understand-automated-ml.md#confusion-matrix)| Avalia os rótulos classificados corretamente em relação aos rótulos reais dos dados. 
[Recall de precisão](how-to-understand-automated-ml.md#precision-recall-chart)| Avalia a proporção de rótulos corretos em relação à taxa de instâncias de rótulo encontradas dos dados 
[Curvas ROC](how-to-understand-automated-ml.md#roc)| Avalia a proporção de rótulos corretos em relação à taxa de rótulos falsos positivos.

### <a name="handle-imbalanced-data"></a>Lidar com dados desbalanceado 

As técnicas a seguir são opções adicionais para lidar com dados desequilibrados fora do ML automatizado. 

- A reamostragem até mesmo do desequilíbrio de classe, seja por amostragem das classes menores ou da amostragem das classes maiores. Esses métodos exigem experiência para processar e analisar.

- Use uma métrica de desempenho que lida melhor com dados desequilibrados. Por exemplo, a pontuação F1 é uma média ponderada de precisão e RECALL. A precisão mede a exatidão de um classificador – a precisão baixa indica um alto número de falsos positivos--,, enquanto a RECALL mede a integridade de um classificador, a RECALL baixa indica um grande número de falsos negativos. 

## <a name="use-with-onnx-in-c-apps"></a>Usar com ONNX em C# aplicativos

Com Azure Machine Learning, você pode usar o ML automatizado para criar um modelo Python e fazer com que ele seja convertido no formato ONNX. O tempo de execução C#do ONNX dá suporte para que você possa usar o modelo C# criado automaticamente em seus aplicativos sem a necessidade de recodificar ou qualquer uma das latências de rede que os pontos de extremidade REST apresentam. Experimente um exemplo desse fluxo [neste Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>ML automatizado na Microsoft

O ML automatizado também está disponível em outras soluções da Microsoft, como:

|Integrações|Descrição|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Seleção automática de modelos e treinamento em aplicativos .NET usando o Visual Studio e Visual Studio Code com o ML automatizado ML.NET (versão prévia).|
|[HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|Escale horizontalmente seus trabalhos de treinamento de ML automatizados no Spark em clusters HDInsight em paralelo.|
|[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Invocar modelos de aprendizado de máquina diretamente no Power BI (versão prévia).|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|Crie novos modelos de Machine Learning em seus dados em clusters SQL Server 2019 Big Data.|

## <a name="next-steps"></a>Passos seguintes

Veja exemplos e aprenda a criar modelos usando o aprendizado de máquina automatizado:

+ Siga o [Tutorial: Treinar automaticamente um modelo de regressão com o Azure Automated Machine Learning @ no__t-0

+ Defina as configurações para o teste de treinamento automático:
  + Na interface portal do Azure ou na página de aterrissagem do espaço de trabalho (versão prévia), [Use estas etapas](how-to-create-portal-experiments.md).
  + Com o SDK do Python, [Use estas etapas](how-to-configure-auto-train.md).

+ Saiba como treinar automaticamente usando dados de série temporal, [Use estas etapas](how-to-auto-train-forecast.md).

+ Experimente [Jupyter Notebook amostras para o aprendizado de máquina automatizado](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
