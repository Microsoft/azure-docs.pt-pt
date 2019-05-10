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
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 136141f5b598fd080edf3254fd01200f2742c763
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235824"
---
# <a name="what-is-automated-machine-learning"></a>O que é automatizado aprendizagem automática?

Aprendizagem automática, também conhecida como AutoML, permite que os cientistas de dados, os analistas e desenvolvedores criar modelos de ML com escala elevada, eficiência e produtividade enquanto estiverem a qualidade dos modelos. 

ML automatizada cria um conjunto de modelos de ML automaticamente, de forma inteligente selecionar modelos para formação e recomenda a uma melhor para. Máquina tradicional, desenvolvimento de modelos de aprendizagem é com muitos recursos que requerem o conhecimento do domínio significativo e a hora para produzir e comparar dezenas de modelos. Com o ML automatizado, que irá acelerar o tempo que demora a obter modelos de ML prontos para produção com grande facilidade e eficiência.

Em segundo plano, seus dados de preparação é feitos com um recurso de destino definidos e inteligentemente iteração após combinações de algoritmos de ML e seleções de funcionalidade. Em seguida, com base em classificações de treinamento, o melhor modelo ajustado é identificado e recomendado para. 

Ainda terá controlo sobre a sua experimentação e transparência no que está a acontecer. Pode definir restrições e experimente objetivos com base no tempo, precisão ou número de iterações, por exemplo. Pode ver a cada modelo que foi gerado para a experimentação, o fluxo de treinamento para cada iteração e os recursos mais influentes de um modelo específico.

## <a name="how-automated-ml-works"></a>Funciona como automatizada de ML

Usando **serviço Azure Machine Learning**, criar e executar as suas experiências de treinamento de ML automatizadas com estes passos:

1. **Identificar o problema de ML** a ser solucionado: classificação, previsão ou regressão
   
1. **Especificar a origem e o formato dos dados de treinamento etiquetadas**: Matrizes de Numpy ou Pandas dataframe

1. **Configurar o destino de computação para a preparação de modelos**, tais como sua [computador local, Azure Machine Learning computa, VMs remotas ou do Azure Databricks](how-to-set-up-training-targets.md).  Saiba mais sobre treinamento automatizado [num recurso remoto](how-to-auto-train-remote.md).

1. **Configurar a parâmetros de aprendizagem automatizada** que determinam o número de iterações através de modelos diferentes, as definições de hiper-parâmetros, advanced pré-processamento/featurization e que métricas examinar ao determinar o melhor modelo.  Pode configurar as definições para experimentação de preparação automático [no portal do Azure](how-to-create-portal-experiments.md) ou [com o SDK](how-to-configure-auto-train.md).

1. **Submeta o treinamento ser executado.** 


[![Aprendizagem automática](./media/how-to-automated-ml/automated-machine-learning.png)](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

Durante o treinamento, o serviço Azure Machine Learning cria um número de pipelines que tente parâmetros e algoritmos diferentes. Ele irá parar assim que chegar a critérios de saída definidos na experimentação. 

Também pode inspecionar as informações de execução com sessão iniciada, que contém a métricas recolhidas durante a execução. A execução de treinamento produz um objeto de Python serializados (`.pkl` ficheiro) que contém o modelo e, em seguida, o pré-processamento de dados.


Enquanto a criação de modelo é automatizada, também pode [Saiba como importantes ou relevantes recursos são](how-to-configure-auto-train.md#explain) para os modelos gerados. 

> [!VIDEO https://www.youtube.com/embed/l8c-4iDPE0M]

<a name="preprocess"></a>

## <a name="preprocessing"></a>O pré-processamento

Em cada automatizada experimentação do machine learning, os seus dados são processados usando os métodos padrão e, opcionalmente, por meio de avançadas processamento prévio.

### <a name="automatic-preprocessing-standard"></a>Automático pré-processamento (padrão)
Em cada automatizada experimentação do machine learning, os seus dados é automaticamente dimensionados ou normalizados para ajudar a algoritmos de bom desempenho.  Durante a preparação de modelos, uma das seguintes técnicas de dimensionamento ou a normalização será aplicada a cada modelo.

|Dimensionar&nbsp;&&nbsp;normalização| Descrição |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Padronizar funcionalidades ao remover a média e dimensionar a variância de unidade  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transforma funcionalidades através do dimensionamento cada funcionalidade por essa coluna mínimo e máximo  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |    Aumente a cada funcionalidade, o valor absoluto máximo |  
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |   Esta funcionalidade de Scaler pelo respetivo intervalo quantile |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) | Redução de dimensionalidade linear com Singular decomposição de valor dos dados para o project-lo para um espaço de dimensional inferior | 
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |    Este transformador executa uma redução de dimensionalidade linear por meio de decomposição de valor único truncado (programa). Ao contrário do PCA, este vistoriador center não os dados antes de computação a decomposição de valor único. Isso significa que ele pode trabalhar com matrizes scipy.sparse com eficiência | 
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Cada exemplo (ou seja, cada linha da matriz de dados) pelo menos um componente diferente de zero é redimensionado independentemente dos outros exemplos, para que seu norma (l1 ou erros de l2) é igual a um | 

### <a name="advanced-preprocessing-optional-featurization"></a>Advanced pré-processamento: featurization opcional

O pré-processamento avançadas adicionais e featurization também estão disponíveis, como em falta imputation de valores, codificação de mensagens em fila e transformações. [Saiba mais sobre o que featurization está incluído](how-to-create-portal-experiments.md#preprocess). Ative esta definição com:
+ Portal do Azure: Selecionar o **Preprocess** caixa de seleção o **definições avançadas** [com estes passos](how-to-create-portal-experiments.md). 
+ Python SDK: Especificar `"preprocess": True` para o [ `AutoMLConfig` classe](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

## <a name="ensemble-models"></a>Modelos de Ensemble

Pode preparar os modelos de ensemble com aprendizagem automática com o [algoritmo de seleção de ensemble Caruana com a inicialização de Ensemble classificada](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf). Ensemble learning melhora os resultados do machine learning e o desempenho de previsão ao combinam muitos modelos em vez de utilizar os modelos de únicos. A iteração ensemble é apresentado como a última iteração de sua execução.

## <a name="use-with-onnx-in-c-apps"></a>Utilize com ONNX no C# aplicações

Com o Azure Machine Learning, pode utilizar o ML automatizada para criar um modelo de Python e fazer com que ele convertido em formato ONNX. O tempo de execução ONNX suporta C#, por isso, pode usar o modelo criado automaticamente no seu C# aplicações sem qualquer necessidade de qualquer recodificação ou qualquer um das latências de rede que apresentam os pontos finais REST. Experimente um exemplo deste fluxo [neste bloco de notas do Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>ML automatizada da Microsoft

ML automatizada também está disponível em outras soluções da Microsoft, tais como:
+ Em aplicativos .NET usando o Visual Studio e Visual Studio Code com [ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)
+ [No HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md), onde pode aumentar horizontalmente as tarefas de formação de ML automatizadas, no Spark em clusters do HDInsight em paralelo. 
+ [No Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)

## <a name="next-steps"></a>Passos Seguintes

Ver exemplos e saiba como criar modelos com o Machine Learning automatizada:

+ Siga o [Tutorial: Preparar automaticamente um modelo de classificação com o Azure automatizada Machine Learning](tutorial-auto-train-models.md)

+ Configure as definições para experimentação de preparação automático: 
   + Na interface do portal do Azure, [Utilize estes passos](how-to-create-portal-experiments.md).
   + Com o SDK de Python [Utilize estes passos](how-to-configure-auto-train.md).
  
 + Saiba como automaticamente train com dados de séries de tempo [Utilize estes passos](how-to-auto-train-forecast.md).

+ Experimentar o [exemplos de bloco de notas do Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
