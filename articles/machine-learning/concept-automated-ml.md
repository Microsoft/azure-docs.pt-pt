---
title: O que é automatizado ML / AutoML
titleSuffix: Azure Machine Learning
description: Saiba como o Azure Machine Learning pode escolher automaticamente um algoritmo para si e gerar um modelo para economizar tempo utilizando os parâmetros e critérios que fornece para selecionar o melhor algoritmo para o seu modelo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 04/22/2020
ms.openlocfilehash: f592a7f5a4af38988bcf433f0adc89d9be7579cb
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082014"
---
# <a name="what-is-automated-machine-learning-automl"></a>O que é aprendizagem automática de máquinas (AutoML)?

O machine learning automatizado, também referido como ML automatizado ou AutoML, é o processo de automatização das tarefas iterativas e de desenvolvimento do modelo de aprendizagem automática. Permite que cientistas de dados, analistas e desenvolvedores construam modelos ML com alta escala, eficiência e produtividade, mantendo a qualidade do modelo. O ML automatizado baseia-se numa descoberta da nossa [divisão de Pesquisa microsoft.](https://arxiv.org/abs/1705.05355)

O desenvolvimento do modelo tradicional de aprendizagem automática é intensivo em recursos, requerendo conhecimentos e tempo significativos de domínio para produzir e comparar dezenas de modelos. Com machine learning automatizado, você vai acelerar o tempo que leva para obter modelos ML prontos para a produção com grande facilidade e eficiência.

## <a name="when-to-use-automl-classify-regression--forecast"></a>Quando utilizar o AutoML: classificar, regressão, previsão &

Aplique ML automatizado quando quiser que o Azure Machine Learning treine e sintetetete um modelo para si utilizando a métrica-alvo que especifica. O ML automatizado democratiza o processo de desenvolvimento do modelo de aprendizagem automática e capacita os seus utilizadores, independentemente da sua experiência em ciência de dados, a identificarem um pipeline de aprendizagem automática de ponta a ponta para qualquer problema.

Cientistas de dados, analistas e desenvolvedores em todas as indústrias podem usar ML automatizado para:
+ Implementar soluções ML sem um vasto conhecimento de programação
+ Poupe tempo e recursos
+ Alavancar as melhores práticas da ciência dos dados
+ Proporcionar uma resolução ágil de problemas

### <a name="classification"></a>Classificação

A classificação é uma tarefa comum de aprendizagem automática. A classificação é um tipo de aprendizagem supervisionada em que os modelos aprendem usando dados de formação, e aplicam essas aprendizagens a novos dados. O Azure Machine Learning oferece recursos especificamente para estas tarefas, tais como recursos de texto de rede neural profunda para classificação. Saiba mais sobre [opções de recursos.](how-to-use-automated-ml-for-ml-models.md#featurization) 

O principal objetivo dos modelos de classificação é prever em que categorias novos dados se basearão nas aprendizagens a partir dos seus dados de formação. Exemplos comuns de classificação incluem deteção de fraudes, reconhecimento de caligrafia e deteção de objetos.  Saiba mais e veja um exemplo de [classificação com machine learning automatizado.](tutorial-train-models-with-aml.md)

Veja exemplos de classificação e aprendizagem automática de máquinas nestes cadernos Python: [Deteção de Fraudes,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) [Previsão de Marketing](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)e Classificação de Dados do [Newsgroup](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)

### <a name="regression"></a>Regressão
Semelhante à classificação, as tarefas de regressão são também uma tarefa comum de aprendizagem supervisionada. O Azure Machine Learning oferece [recursos especificamente para estas tarefas.](how-to-use-automated-ml-for-ml-models.md#featurization)

Diferente da classificação onde os valores de saída previstos são categóricos, os modelos de regressão preveem valores de saída numéricos baseados em preditores independentes. Em regressão, o objetivo é ajudar a estabelecer a relação entre essas variáveis de previsão independente, estimando como uma variável impacta as outras. Por exemplo, o preço do automóvel com base em características como, quilometragem de gás, classificação de segurança, etc. Saiba mais e veja um exemplo de [regressão com aprendizagem automática de máquinas.](tutorial-auto-train-models.md)

Veja exemplos de regressão e aprendizagem automática de máquinas para previsões nestes cadernos Python: [CPU Performance Prediction,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb) 

### <a name="time-series-forecasting"></a>Previsão de séries temporais

As previsões de construção são parte integrante de qualquer negócio, seja receita, inventário, vendas ou procura de clientes. Pode utilizar ml automatizado para combinar técnicas e abordagens e obter uma previsão recomendada e de alta qualidade da série de tempo. Saiba mais com este how-to: machine learning automatizado para previsão de [séries temporais.](how-to-auto-train-forecast.md) 

Uma experiência automatizada em séries temporais é tratada como um problema de regressão multivariada. Os valores das séries de tempo passadas são "apostados" para se tornarem dimensões adicionais para o regresso, juntamente com outros preditores. Esta abordagem, ao contrário dos métodos clássicos da série de tempo, tem uma vantagem de incorporar naturalmente múltiplas variáveis contextuais e sua relação entre si durante o treino. O ML automatizado aprende um modelo único, mas muitas vezes ramificado internamente para todos os itens nos horizontes de dataset e previsão. Mais dados estão assim disponíveis para estimar os parâmetros dos modelos e a generalização para séries invisíveis torna-se possível.

A configuração avançada de previsão inclui:
* deteção e caracterização de férias
* séries temporais e alunos do DNN (Auto-ARIMA, Profeta, ForecastTCN)
* muitos modelos suportam através do agrupamento
* validação cruzada de origem rolante
* lags configuráveis
* características agregados de janela sonantes


Veja exemplos de regressão e aprendizagem automática de máquinas para previsões nestes cadernos Python: Previsão de [Vendas,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb) [Previsão da Procura](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)e Previsão de Produção de [Bebidas.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

## <a name="how-automl-works"></a>Como funciona a AutoML

Durante o treino, o Azure Machine Learning cria uma série de oleodutos em paralelo que experimentam diferentes algoritmos e parâmetros para si. O serviço iterates através de algoritmos ML emparelhados com seleções de recursos, onde cada iteração produz um modelo com uma pontuação de treino. Quanto maior for a pontuação, melhor o modelo é considerado para "encaixar" os seus dados.  Vai parar assim que atingir os critérios de saída definidos na experiência. 

Utilizando o **Azure Machine Learning,** pode projetar e executar as suas experiências automatizadas de treino ML com estes passos:

1. **Identificar o problema ml** a resolver: classificação, previsão ou regressão

1. **Escolha se deseja utilizar o Python SDK ou a experiência web do estúdio**: Saiba mais sobre a paridade entre o Python [SDK e](#parity)a experiência web do estúdio.

   * Para uma experiência limitada ou sem código, experimente a experiência web do estúdio Azure Machine Learning em[https://ml.azure.com](https://ml.azure.com/)  
   * Para os desenvolvedores da Python, confira o [Azure Machine Learning Python SDK](how-to-configure-auto-train.md) 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]  
    
1. **Especificar a fonte e o formato dos dados de formação rotulados:** Matrizes numpy ou quadro de dados pandas

1. **Configure o alvo da computação para a formação**de modelos, como o seu [computador local, as Computações de Aprendizagem automática Azure, os VMs remotos ou os Databricks Azure.](how-to-set-up-training-targets.md)  Aprenda sobre formação automatizada [num recurso remoto.](how-to-auto-train-remote.md)

1. **Configure os parâmetros automatizados** de aprendizagem automática de máquinas que determinam quantas iterações em diferentes modelos, configurações de hiperparâmetros, pré-processamento/caracterização avançada, e que métricas olhar para a determinação do melhor modelo.  
1. **Submeta o treino.**

1. **Reveja os resultados** 

O diagrama que se segue ilustra este processo. 
![Aprendizagem automática de máquinas](./media/concept-automated-ml/automl-concept-diagram2.png)


Também pode inspecionar as informações de execução [registadas, que contêm métricas recolhidas](how-to-understand-automated-ml.md) durante a execução. O ensaio de treino produz um`.pkl` objeto serializado Python (ficheiro) que contém o modelo e o pré-processamento de dados.

Enquanto a construção de modelos é automatizada, também pode [aprender o quão importantes ou relevantes são as funcionalidades](how-to-configure-auto-train.md#explain) para os modelos gerados.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Pré-processamento

Em todas as experiências automatizadas de aprendizagem automática de máquinas, os seus dados são pré-processados utilizando os métodos predefinidos e opcionalmente através de um pré-processamento avançado.

> [!NOTE]
> Os passos automatizados de pré-processamento de aprendizagem automática (normalização de recursos, manuseamento de dados em falta, conversão de texto para numérico, etc.) tornam-se parte do modelo subjacente. Ao utilizar o modelo para previsões, as mesmas etapas de pré-processamento aplicadas durante o treino são aplicadas automaticamente aos seus dados de entrada.

### <a name="automatic-preprocessing-standard"></a>Pré-processamento automático (padrão)

Em todas as experiências automatizadas de aprendizagem automática de máquinas, os seus dados são automaticamente dimensionados ou normalizados para ajudar os algoritmos a ter um bom desempenho.  Durante o treino de modelos, uma das seguintes técnicas de escala ou normalização será aplicada a cada modelo.

|Normalização&nbsp;&&nbsp;de escala| Descrição |
| ------------- | ------------- |
| [Invólucro standardScale](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Padronize as funcionalidades removendo a média e a escala para a variação da unidade  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transforma as características escalando cada característica pelo mínimo e máximo da coluna  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Escala cada característica pelo seu valor absoluto máximo |
| [RobustSescalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Este Scaler apresenta-se pela sua gama quântica |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Redução da dimensionalidade linear usando singular valor decomposição dos dados para projetá-lo para um espaço dimensional inferior |
| [Invólucro TruncadoSVD](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Este transformador realiza a redução linear da dimensionalidade através de decomposição de valor singular truncado (SVD). Ao contrário do PCA, este estimador não centra os dados antes de calcular a decomposição de valor singular, o que significa que pode funcionar com matrizes scipy.sparse eficientemente |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Cada amostra (isto é, cada linha da matriz de dados) com pelo menos um componente não zero é redimensionada independentemente de outras amostras de modo que a sua norma (l1 ou l2) seja igual a uma |

### <a name="advanced-preprocessing--featurization"></a>Característica avançada de & de pré-processamento

Estão também disponíveis pré-processamento e funcionalidades avançados adicionais, tais como guarda-dados, codificação e transformações. [Saiba mais sobre o que está incluído.](how-to-use-automated-ml-for-ml-models.md#featurization) Ativar esta definição com:

+ Estúdio Azure Machine Learning: Ativar **a funcionalidade automática** na secção de **configuração adicional do View** com estes [passos](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment).

+ Python SDK: `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` Especificar para a [ `AutoMLConfig` aula](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

<a name="parity"></a>

## <a name="the-studio-vs-sdk"></a>O estúdio vs SDK

Conheça a paridade e as diferenças entre as capacidades automáticas de ML de alto nível disponíveis através do Python SDK e do estúdio em Azure Machine Learning. 

### <a name="experiment-settings"></a>Configurações de experiências 

As seguintes definições permitem configurar a sua experiência ml automatizada. 

| |O Python SDK|A experiência web do estúdio|
----|:----:|:----:
Dados divididos em conjuntos de comboio/validação| ✓|✓
Suporta tarefas ML: classificação, regressão e previsão| ✓| ✓
Otimiza com base na métrica primária| ✓| ✓
Suporta a computação AML como alvo de cálculo | ✓|✓
Configure horizonte de previsão, o alvo fica & janela rolante|✓|✓
Definir critérios de saída |✓|✓ 
Definir iterações simultâneas| ✓|✓
Colunas de gotete| ✓|✓
Algoritmos de bloco|✓|✓
Validação cruzada |✓|✓
Apoia a formação em clusters de Databricks Azure| ✓|
Ver nomes de funcionalidades projetados|✓|
Resumo da caracterização| ✓|
Caracterização para férias|✓|
Níveis de verbosidade de ficheiros de log| ✓|

### <a name="model-settings"></a>Definições do modelo

Estas definições podem ser aplicadas ao melhor modelo como resultado da sua experiência automatizada em ML.

| |O Python SDK|A experiência web do estúdio|
|----|:----:|:----:|
|Melhor registo de modelo, implantação, explicabilidade| ✓|✓|
|Ativar o conjunto de votação & empilhar modelos de conjunto| ✓|✓|
|Mostrar melhor modelo com base em métricanão primária|✓||
|Ativar/desativar a compatibilidade do modelo ONNX|✓||
|Testar o modelo | ✓| |

### <a name="run-control-settings"></a>Executar definições de controlo

Estas definições permitem-lhe rever e controlar as suas experiências e a sua criança corre. 

| |O Python SDK|A experiência web do estúdio|
|----|:----:|:----:|
|Executar mesa de resumo| ✓|✓|
|Cancele corridas & corridas de crianças| ✓|✓|
|Pegue guarda-costas| ✓|✓|
|Pausa & retomar funciona| ✓| |

## <a name="ensemble-models"></a><a name="ensemble"></a>Modelos conjunto

O machine learning automatizado suporta modelos conjuntos, que são ativados por padrão. A aprendizagem do conjunto melhora os resultados do machine learning e o desempenho preditivo, combinando vários modelos em oposição à utilização de modelos individuais. As iterações do conjunto aparecem como as iterações finais da sua corrida. O machine learning automatizado utiliza métodos de conjunto de votação e empilhamento para combinar modelos:

* **Votação**: prevê com base na média ponderada das probabilidades de classe previstas (para tarefas de classificação) ou nos objetivos de regressão previstos (para tarefas de regressão).
* **Empilhamento**: empilhamento combina modelos heterogéneos e treina um meta-modelo com base na saída dos modelos individuais. Os atuais meta-modelos padrão são LogisticRegression para tarefas de classificação e ElasticNet para tarefas de regressão/previsão.

O algoritmo de [seleção do conjunto Caruana](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) com inicialização de conjunto siolado é usado para decidir quais os modelos a usar dentro do conjunto. A um nível elevado, este algoritmo inicializa o conjunto com até cinco modelos com as melhores pontuações individuais, e verifica que estes modelos estão dentro do limiar de 5% da melhor pontuação para evitar um conjunto inicial pobre. Em seguida, para cada iteração do conjunto, um novo modelo é adicionado ao conjunto existente e a pontuação resultante é calculada. Se um novo modelo melhorou a pontuação do conjunto existente, o conjunto é atualizado para incluir o novo modelo.

Consulte o ["como fazer"](how-to-configure-auto-train.md#ensemble) para alterar as definições padrão do conjunto na aprendizagem automática de máquinas.

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

Com o Azure Machine Learning, pode utilizar ml automatizado para construir um modelo Python e convertê-lo para o formato ONNX. Uma vez que os modelos estão no formato ONNX, podem ser executados em várias plataformas e dispositivos. Saiba mais sobre a aceleração dos modelos ML com o [ONNX](concept-onnx.md).

Veja como converter-se em formato ONNX neste exemplo de [portátil Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). Saiba quais [os algoritmos suportados no ONNX](how-to-configure-auto-train.md#select-your-experiment-type).

O tempo de funcionação ONNX também suporta C#, para que possa utilizar o modelo construído automaticamente nas suas aplicações C# sem necessidade de recodificação ou qualquer das latenciências da rede que os pontos finais rest introduzem. Saiba mais sobre inferência de [modelos ONNX com o tempo de execução ONNX C# API](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md). 



## <a name="next-steps"></a>Passos seguintes

Veja exemplos e aprenda a construir modelos usando machine learning automatizado:

+ Siga o [Tutorial: Treine automaticamente um modelo de regressão com Azure Machine Learning](tutorial-auto-train-models.md)

+ Configure as definições para a experiência de treino automático:
  + No estúdio Azure Machine Learning, [utilize estes passos.](how-to-use-automated-ml-for-ml-models.md)
  + Com o Python SDK, [use estes passos.](how-to-configure-auto-train.md)

+ Aprenda a treinar automaticamente utilizando dados da série time, [use estes passos](how-to-auto-train-forecast.md).

+ Experimente [amostras de Caderno Jupyter para aprendizagem automática de máquinas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

* Ml automatizado também está disponível em outras soluções da Microsoft tais como, [ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview), [HDInsight,](../hdinsight/spark/apache-spark-run-machine-learning-automl.md) [Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated) e [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
