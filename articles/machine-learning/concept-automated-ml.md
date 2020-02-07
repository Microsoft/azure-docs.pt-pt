---
title: O que é o ML/AutoML automatizado
titleSuffix: Azure Machine Learning
description: Saiba como Azure Machine Learning pode escolher automaticamente um algoritmo para você e gerar um modelo a partir dele para poupar tempo usando os parâmetros e critérios que você fornece para selecionar o melhor algoritmo para seu modelo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 11/04/2019
ms.openlocfilehash: 012300c95fd1edd135b97f52ed3702ce3e7ef0bd
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048962"
---
# <a name="what-is-automated-machine-learning"></a>O que é automatizado aprendizagem automática?

O Machine Learning automatizado, também conhecido como ML automatizado, é o processo de automatização das tarefas iterativas demoradas do desenvolvimento de modelos de aprendizado de máquina. Ele permite que cientistas de dados, analistas e desenvolvedores criem modelos de ML com alta escala, eficiência e produtividade, tudo ao mesmo tempo em que sustenta a qualidade do modelo. O ML automatizado baseia-se numa descoberta da nossa [divisão de Pesquisa microsoft.](https://arxiv.org/abs/1705.05355)

O desenvolvimento de modelos de aprendizado de máquina tradicional tem uso intensivo de recursos, exigindo conhecimento e tempo de domínio significativos para produzir e comparar dezenas de modelos. Aplique ML automatizado quando desejar que Azure Machine Learning treine e ajuste um modelo para você usando a métrica de destino que você especificar. Em seguida, o serviço faz a iteração por meio de algoritmos de ML emparelhados com seleções de recursos, em que cada iteração produz um modelo com uma pontuação de treinamento. Quanto maior a pontuação, melhor o modelo é considerado para "ajustar" seus dados.

Com o aprendizado de máquina automatizado, você acelerará o tempo necessário para obter modelos de ML prontos para produção com grande facilidade e eficiência.

## <a name="when-to-use-automated-ml"></a>Quando usar ML automatizado

Democratiza automatizado o processo de desenvolvimento do modelo do Machine Learning e capacita seus usuários, não importa sua experiência em ciência de dados, para identificar um pipeline de aprendizado de máquina de ponta a ponta para qualquer problema.

Cientistas de dados, analistas e desenvolvedores em setores podem usar o ML automatizado para:

+ Implementar soluções de Machine Learning sem amplo conhecimento de programação
+ Economize tempo e recursos
+ Aproveitar as práticas recomendadas de ciência de dados
+ Fornecer solução de problemas ágil

A tabela a seguir lista casos comuns de uso de ML automatizados. 

Classificação| Previsão de série temporal | Regressão
---|---|---
[Deteção de Fraudes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[Previsão de Vendas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)|[Previsão do Desempenho do CPU](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)
|[Previsão de Marketing](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[Previsão da Procura](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
|[Classificação de Dados do Newsgroup](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)|[Previsão de Produção de Bebidas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)|

## <a name="how-automated-ml-works"></a>Como o ML automatizado funciona

Utilizando o **Azure Machine Learning,** pode projetar e executar as suas experiências automatizadas de treino ML com estes passos:

1. **Identificar o problema ml** a resolver: classificação, previsão ou regressão

1. **Especificar a fonte e o formato dos dados de formação rotulados:** Matrizes numpy ou quadro de dados pandas

1. **Configure o alvo da computação para a formação**de modelos, como o seu [computador local, as Computações de Aprendizagem automática Azure, os VMs remotos ou os Databricks Azure.](how-to-set-up-training-targets.md)  Aprenda sobre formação automatizada [num recurso remoto.](how-to-auto-train-remote.md)

1. **Configure os parâmetros automatizados** de aprendizagem automática de máquinas que determinam quantas iterações em diferentes modelos, configurações de hiperparâmetros, pré-processamento/caracterização avançada, e que métricas olhar para a determinação do melhor modelo.  Pode configurar as definições para experiências de treino automático no [estúdio Azure Machine Learning,](https://ml.azure.com)ou [com o SDK](how-to-configure-auto-train.md). 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **Submeta o treino.**

  ![Machine Learning automatizado](./media/concept-automated-ml/automl-concept-diagram2.png)

Durante o treinamento, Azure Machine Learning cria um número de pipelines paralelos que tentam algoritmos e parâmetros diferentes. Ele será interrompido quando atingir os critérios de saída definidos no experimento.

Também pode inspecionar as informações de execução [registadas, que contêm métricas recolhidas](how-to-understand-automated-ml.md) durante a execução. O ensaio de treino produz um objeto serializado Python (ficheiro`.pkl`) que contém o modelo e o pré-processamento de dados.

Enquanto a construção de modelos é automatizada, também pode [aprender o quão importantes ou relevantes são as funcionalidades](how-to-configure-auto-train.md#explain) para os modelos gerados.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Pré-processamento

Em todos os experimentos de aprendizado de máquina automatizados, seus dados são pré-processados usando os métodos padrão e, opcionalmente, por meio de pré-processamento avançado.

> [!NOTE]
> As etapas de pré-processamento automatizado do Machine Learning (normalização de recursos, manipulação de dados ausentes, conversão de texto em numeric, etc.) tornam-se parte do modelo subjacente. Ao usar o modelo para previsões, as mesmas etapas de pré-processamento aplicadas durante o treinamento são aplicadas aos dados de entrada automaticamente.

### <a name="automatic-preprocessing-standard"></a>Pré-processamento automático (padrão)

Em todos os experimentos de aprendizado de máquina automatizados, seus dados são dimensionados e normalizados automaticamente para ajudar os algoritmos a executarem bem.  Durante o treinamento do modelo, uma das técnicas de dimensionamento ou normalização a seguir será aplicada a cada modelo.

|Escalada&nbsp;&&nbsp;normalização| Descrição |
| ------------- | ------------- |
| [Invólucro standardScale](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Padronizar recursos removendo a média e dimensionamento para a variação de unidade  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transforma recursos dimensionando cada recurso pelo mínimo e máximo da coluna  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Dimensionar cada recurso por seu valor absoluto máximo |
| [RobustSescalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Esses recursos de scaler por seu intervalo de Quantil |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Redução de dimensionalidade linear usando a decomposição de valor singular dos dados para projetar a ti em um espaço dimensional inferior |
| [Invólucro TruncadoSVD](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Esse transformador executa a redução de dimensionalidade linear por meio da decomposição de valor singular truncado (SVD). Ao contrário do PCA, esse estimador não centraliza os dados antes de calcular a decomposição do valor singular, o que significa que ele pode trabalhar com matrizes SciPy. esparsas com eficiência |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Cada exemplo (ou seja, cada linha da matriz de dados) com pelo menos um componente diferente de zero é redimensionado independentemente de outros exemplos para que sua norma (L1 ou L2) seja igual a uma |

### <a name="advanced-preprocessing-optional-featurization"></a>Pré-processamento avançado: personalização opcional

O pré-processamento avançado adicional e os personalização também estão disponíveis, como data guardrails, codificação e transformações. [Saiba mais sobre o que está incluído.](how-to-create-portal-experiments.md#featurization) Habilite essa configuração com:

+ Estúdio Azure Machine Learning: Ativar **a funcionalidade automática** na secção de **configuração adicional do View** com estes [passos](how-to-create-portal-experiments.md#create-and-run-experiment).

+ Python SDK: Especificar `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` para a [aula de`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

## <a name="prevent-over-fitting"></a>Evitar sobreajuste

O ajuste excessivo no aprendizado de máquina ocorre quando um modelo se ajusta também aos dados de treinamento e, como resultado, não pode prever com precisão os dados de teste não vistos. Em outras palavras, o modelo tem apenas padrões específicos memorizados e ruídos nos dados de treinamento, mas não é flexível o suficiente para fazer previsões sobre dados reais. Na maioria dos casos flagrantes, um modelo de sobreadaptação assumirá que as combinações de valor de recurso vistas durante o treinamento resultarão sempre na mesma saída para o destino. 

A melhor maneira de evitar o ajuste excessivo é seguir as práticas recomendadas do ML, incluindo:

* Usando mais dados de treinamento e eliminando a tendência estatística
* Evitando o vazamento de destino
* Usando menos recursos
* **Regularização e otimização de hiperparâmetros**
* **Limitações de complexidade do modelo**
* **Validação cruzada**

No contexto do ML automatizado, os três primeiros itens acima são **as melhores práticas que implementa.** Os três últimos itens arrojados são **as melhores práticas implementadas ml automatizadas** por padrão para proteger contra o excesso de encaixe. Em configurações diferentes de ML automatizado, vale a pena seguir todas as seis práticas recomendadas para evitar modelos de sobreajuste.

### <a name="best-practices-you-implement"></a>Práticas recomendadas que você implementa

Usar **mais dados** é a forma mais simples e possível de prevenir o excesso de adaptação, e como um bónus adicionado normalmente aumenta a precisão. Quando você usa mais dados, fica mais difícil para o modelo memorizar padrões exatos e é forçado a alcançar soluções que são mais flexíveis para acomodar mais condições. Também é importante reconhecer **o enviesamento estatístico,** para garantir que os seus dados de formação não incluem padrões isolados que não existirão nos dados de previsão ao vivo. Esse cenário pode ser difícil de resolver, pois pode não haver sobreajuste entre seus conjuntos de treinamento e teste, mas pode haver sobreajustes presentes em comparação com os dados de teste ao vivo.

O vazamento de destino é um problema semelhante, no qual você pode não ver o ajuste excessivo entre os conjuntos de treinamento/teste, mas, em vez disso, ele aparece em tempo de previsão. O vazamento de destino ocorre quando seu modelo "trapacear" durante o treinamento, tendo acesso aos dados que normalmente não deveria ter no tempo de previsão. Por exemplo, se o problema for prever na segunda-feira, qual será o preço de mercadoria na sexta-feira, mas um de seus recursos incluía dados acidentalmente das quintas, que seriam dados que o modelo não terá no tempo de previsão, pois não poderá vê-lo no futuro. O vazamento de destino é um erro fácil de ser ignorado, mas geralmente é caracterizado por uma precisão muito alta para o seu problema. Se está a tentar prever o preço das ações e treinou um modelo com uma precisão de 95%, é provável que haja fugas de destino algures nas suas características.

A remoção de recursos também pode ajudar com o sobreajuste, impedindo que o modelo tenha muitos campos a serem usados para memorizar padrões específicos, tornando-o mais flexível. Pode ser difícil medir de forma quantitativa, mas se você puder remover recursos e manter a mesma precisão, você provavelmente tornou o modelo mais flexível e reduziu o risco de sobreajuste.

### <a name="best-practices-automated-ml-implements"></a>O ML automatizado de práticas recomendadas implementa

A regularização é o processo de minimizar uma função de custo para penalizar modelos complexos e sobreajustados. Há diferentes tipos de funções de regularização, mas, em geral, todos eles penalizam o tamanho do coeficiente do modelo, a variância e a complexidade. O ML automatizado usa L1 (laço), L2 (saliência) e ElasticNet (L1 e L2 simultaneamente) em combinações diferentes com configurações de hiperparâmetro de modelo diferentes que controlam o sobreajuste. Em termos simples, o ML automatizado vai variar o quanto um modelo é regulamentado e escolher o melhor resultado.

O ML automatizado também implementa limitações de complexidade de modelo explícitas para evitar o ajuste excessivo. Na maioria dos casos, essa implementação é especificamente para algoritmos de árvore de decisão ou de floresta, em que a profundidade máxima da árvore individual é limitada e o número total de árvores usadas nas técnicas de floresta ou Ensemble são limitados.

A CV (validação cruzada) é o processo de pegar muitos subconjuntos de seus dados de treinamento completos e treinar um modelo em cada subconjunto. A ideia é que um modelo poderia ter "sorte" e ter grande precisão com um subconjunto, mas usando muitos subconjuntos o modelo não atingirá essa alta precisão a cada vez. Ao fazer a CV, você fornece um conjunto de um DataSet de validação, especifica as dobras de CV (número de subconjuntos) e o ML automatizado treinará seu modelo e ajustará os hiperparâmetros para minimizar o erro em seu conjunto de validação. Uma dobra de CV pode ser mais adequada, mas usar muitas delas reduz a probabilidade de que o modelo final seja mais adequado. A contrapartida é que o CV resulta em tempos de treino mais longos e, portanto, maior custo, porque em vez de treinar um modelo uma vez, treina-o uma vez para *cada* subconjunto de CV.

> [!NOTE]
> A validação cruzada não está habilitada por padrão; Ele deve ser configurado em configurações de ML automatizadas. No entanto, depois que a CV for configurada e um conjunto de dados de validação tiver sido fornecido, o processo será automatizado para você.

### <a name="identifying-over-fitting"></a>Identificação de sobreajuste

Considere os seguintes modelos treinados e suas imprecisões de treinamento e teste correspondentes.

| Modelo | Precisão do treinamento | Precisão do teste |
|-------|----------------|---------------|
| A | 99,9% | 95% |
| B | 87% | 87% |
| C | 99,9% | 45% |

Considerando o modelo **A,** existe um equívoco comum de que se a precisão do teste em dados invisíveis for inferior à precisão do treino, o modelo está sobre-montado. No entanto, a precisão do teste deve ser sempre menor do que a precisão do treino, e a distinção para o excesso de ajuste vs. adequadamente ajustado resume-se a *quão* menos precisa. 

Ao comparar os modelos **A** e **B,** o modelo **A** é um modelo melhor porque tem uma maior precisão de teste, e embora a precisão do teste seja ligeiramente mais baixa em 95%, não é uma diferença significativa que sugira que o excesso de adaptação está presente. Não escolheria o modelo **B** simplesmente porque as precisões do comboio e do teste estão mais próximas.

O modelo **C** representa um caso claro de excesso de adaptação; a precisão do treino é muito alta, mas a precisão do teste não é tão alta. Essa distinção é subjetiva, mas vem do conhecimento de seu problema e dados e de quais magnitudes de erro são aceitáveis. 

## <a name="time-series-forecasting"></a>Previsão de séries temporais

A criação de previsões é parte integrante de qualquer empresa, seja ela de receita, inventário, vendas ou demanda do cliente. Você pode usar o ML automatizado para combinar técnicas e abordagens e obter uma previsão de série temporal de alta qualidade e recomendada.

Um experimento de série temporal automatizado é tratado como um problema de regressão multivariado. Os valores de série temporal anteriores são "dinamizados" para se tornarem dimensões adicionais para o regressor junto com outros previsões. Essa abordagem, ao contrário dos métodos de série temporal clássica, tem uma vantagem de incorporar naturalmente várias variáveis contextuais e sua relação entre si durante o treinamento. O ML automatizado aprende um modelo único, mas geralmente ramificado internamente para todos os itens no conjunto de e horizontes de previsão. Por isso, mais dados estão disponíveis para estimar os parâmetros de modelo e a generalização para uma série não vista se torna possível.

Saiba mais e veja um exemplo de [aprendizagem automática de máquinas para previsão de séries temporais.](how-to-auto-train-forecast.md) Ou, consulte o caderno de procura de [energia](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) para exemplos de código detalhados de configuração de previsão avançada, incluindo:

* detecção de feriados e personalização
* série temporal e aprendizes DNN (auto-ARIMA, Prophet, ForecastTCN)
* muitos modelos dão suporte ao agrupamento
* validação cruzada de origem sem interrupção
* retardo configurável
* recursos agregados de janela sem interrupção

## <a name="ensemble"></a>Modelos conjunto

O aprendizado de máquina automatizado dá suporte a modelos Ensemble, que são habilitados por padrão. O Ensemble Learning melhora os resultados do Machine Learning e o desempenho de previsão combinando vários modelos em oposição ao uso de modelos únicos. As iterações Ensemble são exibidas como as iterações finais da sua execução. O Machine Learning automatizado usa os métodos Ensemble de votação e empilhamento para combinar modelos:

* **Votação**: prevê com base na média ponderada das probabilidades de classe previstas (para tarefas de classificação) ou nos objetivos de regressão previstos (para tarefas de regressão).
* **Empilhamento**: empilhamento combina modelos heterogéneos e treina um meta-modelo com base na saída dos modelos individuais. Os metamodelos padrão atuais são LogisticRegression para tarefas de classificação e ElasticNet para tarefas de regressão/previsão.

O algoritmo de [seleção do conjunto Caruana](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) com inicialização de conjunto siolado é usado para decidir quais os modelos a usar dentro do conjunto. A um nível elevado, este algoritmo inicializa o conjunto com até cinco modelos com as melhores pontuações individuais, e verifica que estes modelos estão dentro do limiar de 5% da melhor pontuação para evitar um conjunto inicial pobre. Em seguida, para cada iteração Ensemble, um novo modelo é adicionado ao Ensemble existente e a pontuação resultante é calculada. Se um novo modelo tiver melhorado a pontuação Ensemble existente, o Ensemble será atualizado para incluir o novo modelo.

Consulte o ["como fazer"](how-to-configure-auto-train.md#ensemble) para alterar as definições padrão do conjunto na aprendizagem automática de máquinas.

## <a name="imbalance"></a>Dados desequilibrados

Dados desequilibrados geralmente são encontrados em dados para cenários de classificação de aprendizado de máquina e referem-se a dados que contêm uma proporção desproporcional de observações em cada classe. Esse desequilíbrio pode levar a um efeito positivo falso e perceptível da precisão de um modelo, pois os dados de entrada têm a tendência de uma classe, o que resulta no modelo treinado para imitar essa tendência. 

Como parte de seu objetivo de simplificar o fluxo de trabalho do Machine Learning, o ML automatizado tem recursos internos para ajudar a lidar com dados desequilibrados, como 

- Uma coluna de **peso**: ml automatizado suporta uma coluna ponderada como entrada, fazendo com que as linhas nos dados sejam ponderadas para cima ou para baixo, o que pode tornar uma classe mais ou menos "importante".

- Os algoritmos usados pelo ML automatizado podem lidar corretamente com o desequilíbrio de até 20:1, o que significa que a classe mais comum pode ter 20 vezes mais linhas nos dados do que a classe menos comum.

### <a name="identify-models-with-imbalanced-data"></a>Identificar modelos com dados desbalanceado

Como os algoritmos de classificação geralmente são avaliados por exatidão, a verificação da Pontuação de precisão de um modelo é uma boa maneira de identificar se ele foi afetado por dados desequilibrados. Tem realmente alta precisão ou precisão muito baixa para determinadas classes?

Além disso, as execuções automáticas de ML geram automaticamente os gráficos a seguir, o que pode ajudá-lo a entender a exatidão das classificações do modelo e identificar os modelos potencialmente afetados por dados desequilibrados.

Gráfico| Descrição
---|---
[Matriz de confusão](how-to-understand-automated-ml.md#confusion-matrix)| Avalia os rótulos classificados corretamente em relação aos rótulos reais dos dados. 
[Recolha de precisão](how-to-understand-automated-ml.md#precision-recall-chart)| Avalia a proporção de rótulos corretos em relação à taxa de instâncias de rótulo encontradas dos dados 
[Curvas ROC](how-to-understand-automated-ml.md#roc)| Avalia a proporção de rótulos corretos em relação à taxa de rótulos falsos positivos.

### <a name="handle-imbalanced-data"></a>Lidar com dados desbalanceado 

As técnicas a seguir são opções adicionais para lidar com dados desequilibrados fora do ML automatizado. 

- A reamostragem até mesmo do desequilíbrio de classe, seja por amostragem das classes menores ou da amostragem das classes maiores. Esses métodos exigem experiência para processar e analisar.

- Use uma métrica de desempenho que lida melhor com dados desequilibrados. Por exemplo, a pontuação F1 é uma média ponderada de precisão e RECALL. A precisão mede a exatidão de um classificador – a precisão baixa indica um alto número de falsos positivos--,, enquanto a RECALL mede a integridade de um classificador, a RECALL baixa indica um grande número de falsos negativos. 

## <a name="use-with-onnx-in-c-apps"></a>Usar com ONNX em C# aplicativos

Com Azure Machine Learning, você pode usar o ML automatizado para criar um modelo Python e fazer com que ele seja convertido no formato ONNX. O tempo de execução C#do ONNX dá suporte para que você possa usar o modelo C# criado automaticamente em seus aplicativos sem a necessidade de recodificar ou qualquer uma das latências de rede que os pontos de extremidade REST apresentam. Experimente um exemplo deste fluxo [neste caderno jupyter.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)

## <a name="automated-ml-across-microsoft"></a>ML automatizado na Microsoft

O ML automatizado também está disponível em outras soluções da Microsoft, como:

|Integrações|Descrição|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Seleção automática de modelos e treinamento em aplicativos .NET usando o Visual Studio e Visual Studio Code com o ML automatizado ML.NET (versão prévia).|
|[HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|Escale horizontalmente seus trabalhos de treinamento de ML automatizados no Spark em clusters HDInsight em paralelo.|
|[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Invocar modelos de aprendizado de máquina diretamente no Power BI (versão prévia).|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|Crie novos modelos de Machine Learning em seus dados em clusters SQL Server 2019 Big Data.|

## <a name="next-steps"></a>Passos seguintes

Veja exemplos e aprenda a criar modelos usando o aprendizado de máquina automatizado:

+ Siga o [Tutorial: Treine automaticamente um modelo de regressão com o Azure Automated Machine Learning](tutorial-auto-train-models.md)

+ Defina as configurações para o teste de treinamento automático:
  + No estúdio Azure Machine Learning, [utilize estes passos.](how-to-create-portal-experiments.md)
  + Com o Python SDK, [use estes passos.](how-to-configure-auto-train.md)

+ Aprenda a treinar automaticamente utilizando dados da série time, [use estes passos](how-to-auto-train-forecast.md).

+ Experimente [amostras de Caderno Jupyter para aprendizagem automática de máquinas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
