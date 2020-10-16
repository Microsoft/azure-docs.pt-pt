---
title: O que é automatizado ML / AutoML
titleSuffix: Azure Machine Learning
description: Saiba como o Azure Machine Learning pode escolher automaticamente um algoritmo para si e gerar um modelo para economizar tempo usando os parâmetros e critérios que fornece para selecionar o melhor algoritmo para o seu modelo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 04/22/2020
ms.openlocfilehash: 4908f66dbc699a449b7b94febac8133bacc9f669
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760974"
---
# <a name="what-is-automated-machine-learning-automl"></a>O que é o machine learning automatizado (AutoML)?

Machine learning automatizado, também referido como ML automatizado ou AutoML, é o processo de automatização das tarefas morosas e iterativas do desenvolvimento de modelos de aprendizagem automática. Permite que cientistas de dados, analistas e desenvolvedores construam modelos ML com alta escala, eficiência e produtividade, ao mesmo tempo que sustentam a qualidade do modelo. O ML automatizado baseia-se numa descoberta da nossa divisão de [Pesquisa microsoft.](https://www.microsoft.com/research/project/automl/)

O desenvolvimento de modelos de aprendizagem automática tradicional é intensivo em recursos, requerendo conhecimento e tempo significativos de domínio para produzir e comparar dezenas de modelos. Com machine learning automatizado, você vai acelerar o tempo que leva para obter modelos ML prontos para produção com grande facilidade e eficiência.

## <a name="when-to-use-automl-classify-regression--forecast"></a>Quando utilizar o AutoML: classificar, regressão, & previsão

Aplique ML automatizado quando quiser que o Azure Machine Learning treine e afina um modelo para si utilizando a métrica-alvo que especifica. O ML automatizado democratiza o processo de desenvolvimento de modelos de aprendizagem automática e capacita os seus utilizadores, independentemente da sua experiência em ciência de dados, a identificar um pipeline de aprendizagem automática de ponta a ponta para qualquer problema.

Cientistas de dados, analistas e desenvolvedores de indústrias podem usar ML automatizado para:
+ Implementar soluções ML sem conhecimentos de programação alargados
+ Economize tempo e recursos
+ Alavancar as melhores práticas da ciência dos dados
+ Proporcionar uma resolução ágil de problemas

### <a name="classification"></a>Classificação

A classificação é uma tarefa comum de aprendizagem automática. A classificação é um tipo de aprendizagem supervisionada em que os modelos aprendem usando dados de formação, e aplicam essas aprendizagens a novos dados. A Azure Machine Learning oferece ações especificamente para estas tarefas, tais como os adúldeos de texto de rede neural profunda para a classificação. Saiba mais sobre [as opções de exibição.](how-to-configure-auto-features.md#featurization) 

O principal objetivo dos modelos de classificação é prever em que categorias novos dados serão baseados em aprendizagens a partir dos seus dados de formação. Exemplos de classificação comuns incluem deteção de fraude, reconhecimento de caligrafia e deteção de objetos. Saiba mais e veja um exemplo na [Criar um modelo de classificação com ML automatizado.](tutorial-first-experiment-automated-ml.md)

Veja exemplos de classificação e aprendizagem automática de máquinas nestes cadernos Python: [Deteção de Fraudes,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) [Previsão de Marketing](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)e [Classificação de Dados do Newsgroup](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b)

### <a name="regression"></a>Regressão

À semelhança da classificação, as tarefas de regressão são também uma tarefa comum de aprendizagem supervisionada. A Azure Machine Learning oferece [ações especificamente para estas tarefas.](how-to-configure-auto-features.md#featurization)

Diferentes da classificação onde os valores de saída previstos são categóricos, os modelos de regressão preveem valores de saída numéricos baseados em preditores independentes. Na regressão, o objetivo é ajudar a estabelecer a relação entre essas variáveis preditores independentes, estimando como uma variável impacta as outras. Por exemplo, o preço do automóvel com base em características como, quilometragem de gás, classificação de segurança, etc. Saiba mais e veja um exemplo de [regressão com machine learning automatizado.](tutorial-auto-train-models.md)

Veja exemplos de regressão e aprendizagem automática de máquinas para as previsões nestes cadernos Python: [Previsão do Desempenho do CPU,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb) 

### <a name="time-series-forecasting"></a>Previsão da série-tempo

As previsões de construção são parte integrante de qualquer negócio, seja receita, inventário, vendas ou procura de clientes. Você pode usar ML automatizado para combinar técnicas e abordagens e obter uma previsão recomendada de séries de tempo de alta qualidade. Saiba mais com este como fazer: [aprendizagem automática de máquinas para previsão de séries temporq.](how-to-auto-train-forecast.md) 

Uma experiência automatizada da série de tempo é tratada como um problema de regressão multivariada. Os valores de séries de tempo anteriores são "apostados" para se tornarem dimensões adicionais para o regressor juntamente com outros preditores. Esta abordagem, ao contrário dos métodos clássicos das séries de tempo, tem a vantagem de incorporar naturalmente múltiplas variáveis contextuais e a sua relação entre si durante o treino. ML automatizado aprende um modelo único, mas muitas vezes ramificado internamente para todos os itens nos horizontes de dataset e previsão. Mais dados estão, assim, disponíveis para estimar os parâmetros do modelo e a generalização para séries invisíveis torna-se possível.

A configuração avançada de previsão inclui:
* deteção e caracterização de férias
* time-series e alunos DNN (Auto-ARIMA, Profeta, ForecastTCN)
* muitos modelos de suporte através do agrupamento
* validação cruzada de origem rolante
* atrasos configuráveis
* funcionalidades agregadas de janelas rolantes


Veja exemplos de regressão e aprendizagem automática de máquinas para previsões nestes cadernos Python: [Previsão de Vendas,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb) [Previsão da Procura](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)e Previsão de [Produção de Bebidas.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

## <a name="how-automl-works"></a>Como funciona a AutoML

Durante o treino, o Azure Machine Learning cria uma série de oleodutos em paralelo que experimentam diferentes algoritmos e parâmetros para si. O serviço itera através de algoritmos ML emparelhados com seleções de recursos, onde cada iteração produz um modelo com uma pontuação de treino. Quanto maior for a pontuação, melhor o modelo é considerado para "encaixar" os seus dados.  Para assim que atingir os critérios de saída definidos na experiência. 

Utilizando **a Azure Machine Learning,** pode conceber e executar as suas experiências automatizadas de treinamento de ML com estes passos:

1. **Identificar o problema do ML** a resolver: classificação, previsão ou regressão

1. **Escolha se deseja utilizar o Python SDK ou a experiência web do estúdio**: Saiba mais sobre a paridade entre o Python [SDK e](#parity)a experiência web do estúdio.

   * Para experiência limitada ou sem código, experimente a experiência web do estúdio Azure Machine Learning em [https://ml.azure.com](https://ml.azure.com/)  
   * Para desenvolvedores de Python, confira o [Azure Machine Learning Python SDK](how-to-configure-auto-train.md) 
    
1. **Especificar a origem e o formato dos dados de formação rotulados:** Matrizes numpiadas ou dataframe pandas

1. **Configure o alvo de computação para a formação de modelos,** tais como o seu [computador local, Azure Machine Learning Computes, VMs remotos ou Azure Databricks](how-to-set-up-training-targets.md).  Saiba mais sobre formação automatizada [num recurso remoto.](how-to-auto-train-remote.md)

1. **Configure os parâmetros automatizados de aprendizagem automática** que determinam quantas iterações sobre diferentes modelos, configurações de hiperparímetros, pré-processamento/caracterização avançada, e quais as métricas a analisar na determinação do melhor modelo.  
1. **Submeta o treino.**

1. **Rever os resultados** 

O diagrama que se segue ilustra este processo. 
![Aprendizagem automática de máquinas](./media/concept-automated-ml/automl-concept-diagram2.png)


Também pode verificar as informações de execução [registadas, que contêm métricas recolhidas](how-to-understand-automated-ml.md) durante a execução. O ensaio de treino produz um objeto serializado Python `.pkl` (ficheiro) que contém o modelo e o pré-processamento de dados.

Enquanto a construção de modelos é automatizada, também pode [aprender a importância ou as características relevantes](how-to-configure-auto-train.md#explain) para os modelos gerados.

Aprenda a utilizar um [alvo de computação remota.](how-to-auto-train-remote.md)



> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]


## <a name="feature-engineering"></a>Desenvolvimento de funcionalidades

A engenharia de recursos é o processo de utilização do conhecimento de domínio dos dados para criar funcionalidades que ajudam os algoritmos ML a aprender melhor. No Azure Machine Learning, aplicam-se técnicas de escala e normalização para facilitar a engenharia de recursos. Coletivamente, estas técnicas e engenharia de recursos são referidas como caracterização.

Para experiências automatizadas de machine learning, a caracterização é aplicada automaticamente, mas também pode ser personalizada com base nos seus dados. Saiba mais sobre o que está incluído na [caracterização.](how-to-configure-auto-features.md#featurization)  

> [!NOTE]
> As etapas automatizadas de aprendizagem automática (normalização de recurso, manuseamento de dados em falta, conversão de texto em numérico, etc.) tornam-se parte do modelo subjacente. Ao utilizar o modelo para previsões, aplicam-se automaticamente os mesmos passos de apresentação aplicados durante o treino.

### <a name="automatic-featurization-standard"></a>Caracterização automática (padrão)

Em todas as experiências automatizadas de aprendizagem automática de máquinas, os seus dados são automaticamente dimensionados ou normalizados para ajudar os algoritmos a funcionarem bem. Durante o treino de modelo, uma das seguintes técnicas de escala ou normalização será aplicada a cada modelo. Saiba como o AutoML ajuda a [prevenir dados sobreajustes e desequilibrados](concept-manage-ml-pitfalls.md) nos seus modelos.

|Normalização &nbsp; & de escala &nbsp;| Descrição |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Padronizar as características removendo a média e a escala para a variação da unidade  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transforma as características escalando cada recurso pelo mínimo e máximo da coluna  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Escalar cada característica pelo seu valor absoluto máximo |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Este Scaler apresenta-se pela sua gama quantile |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Redução linear da dimensionalidade utilizando a decomposição do valor singular dos dados para projetá-lo para um espaço dimensional inferior |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Este transformador realiza a redução da dimensionalidade linear através da decomposição de valor singular truncado (SVD). Ao contrário do APC, este estimador não centra os dados antes de calcular a decomposição de valor singular, o que significa que pode trabalhar com matrizes scipy.sparse eficientemente |
| [Malizador SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Cada amostra (isto é, cada linha da matriz de dados) com pelo menos um componente não zero é redimensionada independentemente de outras amostras de modo a que a sua norma (l1 ou l2) seja igual a uma |

### <a name="customize-featurization"></a>Personalizar a exibição

Estão também disponíveis técnicas adicionais de engenharia de recursos, tais como codificação e transformações. 

Ative esta definição com:

+ Azure Machine Learning studio: Ative **a participação automática** na secção **de configuração adicional Ver** com estes [passos](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

+ Python SDK: Especificar `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` no seu objeto [AutoMLConfig.](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) Saiba mais sobre [a capacitação da participação.](how-to-configure-auto-features.md) 

## <a name="ensemble-models"></a><a name="ensemble"></a> Modelos de conjunto

A aprendizagem automática suporta modelos conjuntos, que são ativados por padrão. A aprendizagem do conjunto melhora os resultados da aprendizagem automática e o desempenho preditivo, combinando vários modelos em oposição à utilização de modelos individuais. As iterações do conjunto aparecem como as iterações finais da sua corrida. A aprendizagem automática de máquinas utiliza métodos de votação e empilhamento para combinar modelos:

* **Votação**: prevê com base na média ponderada das probabilidades de classe previstas (para tarefas de classificação) ou nos objetivos de regressão previstos (para tarefas de regressão).
* **Empilhamento**: empilhamento combina modelos heterogéneos e treina um meta-modelo baseado na saída dos modelos individuais. Os meta-modelos predefinidos atuais são a LogísticaRegression para tarefas de classificação e a ElasticNet para tarefas de regressão/previsão.

O [algoritmo de seleção do conjunto Caruana](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) com inicialização de conjunto ordenado é usado para decidir quais modelos usar dentro do conjunto. A um nível elevado, este algoritmo inicializa o conjunto com até cinco modelos com as melhores pontuações individuais, e verifica que estes modelos estão a 5% do limiar da melhor pontuação para evitar um conjunto inicial pobre. Em seguida, para cada iteração de conjunto, um novo modelo é adicionado ao conjunto existente e a pontuação resultante é calculada. Se um novo modelo melhorou a pontuação do conjunto existente, o conjunto é atualizado para incluir o novo modelo.

Consulte o [modo de](how-to-configure-auto-train.md#ensemble) alterar as definições do conjunto predefinido na aprendizagem automática de máquinas.

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>Orientação sobre alvos de computação ML geridos remotamente

A interface web para ML automatizado usa sempre um [alvo de computação](concept-compute-target.md)remota .  Mas quando utilizar o Python SDK, escolherá um compute local ou um alvo de computação remota para o treino automatizado de ML.

* **Cálculo local**: A formação ocorre no seu portátil local ou no cálculo VM. 
* **Computação remota**: O treino ocorre em clusters de cálculo machine learning.  

### <a name="choose-compute-target"></a>Escolha o alvo do cálculo
Considere estes fatores ao escolher o seu alvo de computação:

 * **Escolha um cálculo local**: Se o seu cenário for sobre explorações iniciais ou demonstrações usando pequenos dados e comboios curtos (isto é, segundos ou alguns minutos por criança), treinar no seu computador local pode ser uma escolha melhor.  Não há tempo de configuração, os recursos de infraestrutura (o seu PC ou VM) estão disponíveis diretamente.
 * **Escolheu um cluster de computação ML remoto**: Se estiver a treinar com conjuntos de dados maiores, como na formação de produção, criando modelos que necessitem de comboios mais longos, o cálculo remoto proporcionará um desempenho de tempo muito melhor de ponta a ponta, porque `AutoML` irá paralelizar os comboios através dos nós do cluster. Num cálculo remoto, o tempo de arranque da infraestrutura interna irá adicionar cerca de 1,5 minutos por criança, além de minutos adicionais para a infraestrutura de cluster se os VMs ainda não estiverem a funcionar.

### <a name="pros-and-cons"></a>Prós e contras
Considere estes prós e contras ao optar por utilizar local vs. remoto.

|  | Prós (Vantagens)  |Contras (Handicaps)  |
|---------|---------|---------|---------|
|**Alvo de computação local** |  <li> Sem tempo de arranque do ambiente   | <li>  Subconjunto de funcionalidades<li>  Não se pode paralelizar corridas <li> Pior para grandes dados. <li>Sem streaming de dados durante o treino <li>  Sem caracterização baseada em DNN <li> Python SDK apenas |
|**Clusters de computação ML remotos**|  <li> Conjunto completo de funcionalidades <li> Paralelamente corridas de crianças <li>   Grande suporte a dados<li>  A caracterização baseada em DNN <li>  Escalabilidade dinâmica do cluster computacional a pedido <li> Experiência sem código (Web UI) também disponível  |  <li> Tempo de arranque para os nóns de cluster <li> Tempo de arranque para cada corrida de crianças    |

### <a name="feature-availability"></a>Disponibilidade de funcionalidades 

 Mais funcionalidades estão disponíveis quando utiliza o cálculo remoto, como mostra a tabela abaixo. 

| Funcionalidade                                                    | Remoto | Local | 
|------------------------------------------------------------|--------|-------|
| Streaming de dados (Grande suporte a dados, até 100 GB)          | ✓      |       | 
| A participação e formação de texto baseados em DNN-BERT             | ✓      |       |
| Suporte de GPU fora da caixa (treino e inferência)        | ✓      |       |
| Classificação de imagem e suporte à rotulagem                  | ✓      |       |
| Modelos Auto-ARIMA, Profeta e ForecastTCN para previsão | ✓      |       | 
| Múltiplas corridas/iterações em paralelo                       | ✓      |       |
| Criar modelos com interpretação na experiência web do estúdio AutoML UI      | ✓      |       |
| Personalização de engenharia de recursos na experiência web do estúdio UI| ✓      |       |
| Azure ML afinação hiperparítnica                             | ✓      |       |
| Suporte ao fluxo de trabalho do gasoduto Azure ML                         | ✓      |       |
| Continue uma corrida                                             | ✓      |       |
| Previsão                                                | ✓      | ✓     |
| Criar e executar experiências em cadernos                    | ✓      | ✓     |
| Registar e visualizar as informações e métricas da experiência em UI | ✓      | ✓     |
| Guarda-dados                                            | ✓      | ✓     |

## <a name="many-models"></a>Muitos modelos 

O [Acelerador de Soluções de Muitos Modelos](https://aka.ms/many-models) (pré-visualização) baseia-se no Azure Machine Learning e permite-lhe utilizar ML automatizado para treinar, operar e gerir centenas ou mesmo milhares de modelos de machine learning.

Por exemplo, a construção de um modelo __para cada instância ou indivíduo__ nos seguintes cenários pode levar a melhores resultados:

* Previsão de vendas para cada loja individual
* Manutenção preditiva para centenas de poços de petróleo
* Adaptação de uma experiência para utilizadores individuais.

## <a name="automl-in-azure-machine-learning"></a>AutoML em Aprendizagem automática Azure

A Azure Machine Learning oferece duas experiências para trabalhar com ML automatizado:

* Para clientes experientes em código, [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) 

* Para clientes de experiência de código limitada/sem código, estúdio Azure Machine Learning em [https://ml.azure.com](https://ml.azure.com/)  

<a name="parity"></a>

### <a name="experiment-settings"></a>Definições de experiência 

As seguintes definições permitem-lhe configurar a sua experiência automatizada de ML. 

| |O Python SDK|A experiência web do estúdio|
----|:----:|:----:
|**Dividir dados em conjuntos de comboio/validação**| ✓|✓
|**Suporta tarefas ML: classificação, regressão e previsão**| ✓| ✓
|**Otimiza com base na métrica primária**| ✓| ✓
|**Suporta o cálculo AML como alvo de computação** | ✓|✓
|**Configure horizonte de previsão, meta fica & janela rolante**|✓|✓
|**Definir critérios de saída** |✓|✓ 
|**Definir iterações simultâneas**| ✓|✓
|**Colunas de queda**| ✓|✓
|**Algoritmos de bloco**|✓|✓
|**Validação cruzada** |✓|✓
|**Apoia a formação em clusters Azure Databricks**| ✓|
|**Ver nomes de recursos projetados**|✓|
|**Resumo da exibição**| ✓|
|**Exibição para férias**|✓|
|**Registar níveis de verbosidade de ficheiros**| ✓|

### <a name="model-settings"></a>Configurações de modelos

Estas definições podem ser aplicadas ao melhor modelo como resultado da sua experiência automatizada de ML.

| |O Python SDK|A experiência web do estúdio|
|----|:----:|:----:|
|**Melhor registo de modelo, implantação, explicabilidade**| ✓|✓|
|**Ativar os modelos de conjunto de pilhas & de votação**| ✓|✓|
|**Mostrar o melhor modelo baseado em métrica não primária**|✓||
|**Ativar/desativar a compatibilidade do modelo ONNX**|✓||
|**Testar o modelo** | ✓| |

### <a name="run-control-settings"></a>Executar definições de controlo

Estas definições permitem-lhe rever e controlar as suas corridas de experiências e as suas corridas de crianças. 

| |O Python SDK|A experiência web do estúdio|
|----|:----:|:----:|
|**Tabela de resumo de execução**| ✓|✓|
|**Cancelar corre & corridas de crianças**| ✓|✓|
|**Obter guarda-costas**| ✓|✓|
|**Pausa & currículo corre**| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

Com a Azure Machine Learning, pode utilizar ml automatizado para construir um modelo Python e convertê-lo para o formato ONNX. Uma vez que os modelos estão no formato ONNX, podem ser executados em várias plataformas e dispositivos. Saiba mais sobre [acelerar os modelos ML com ONNX](concept-onnx.md).

Veja como converter para o formato ONNX [neste exemplo de caderno Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). Saiba quais [os algoritmos suportados no ONNX.](how-to-configure-auto-train.md#select-your-experiment-type)

O tempo de execução ONNX também suporta C#, para que possa utilizar o modelo construído automaticamente nas suas aplicações C# sem qualquer necessidade de recoding ou qualquer uma das latências de rede que os pontos finais REST introduzem. Saiba mais sobre [a inferencção dos modelos ONNX com o tempo de execução ONNX C# API](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md). 

## <a name="next-steps"></a>Passos seguintes

Existem vários recursos para te pôr a funcionar com o AutoML. 

### <a name="tutorials-how-tos"></a>Tutoriais/ como-tos
Os tutoriais são exemplos introdutórios de cenários AutoML.
+ **Para uma primeira experiência**de código, siga o [Tutorial: Treine automaticamente um modelo de regressão com Azure Machine Learning Python SDK](tutorial-auto-train-models.md).

 + **Para uma experiência de baixo ou nenhum código,** consulte o [Tutorial: Crie modelos automatizados de classificação ML com estúdio Azure Machine Learning](tutorial-first-experiment-automated-ml.md).

Como fornecer artigos fornecem detalhes adicionais sobre a funcionalidade que o AutoML oferece. Por exemplo, 

+ Configure as configurações para experiências de formação automática
    + No estúdio Azure Machine Learning, [utilize estes passos.](how-to-use-automated-ml-for-ml-models.md) 
    + Com o Python SDK, [use estes passos.](how-to-configure-auto-train.md)

+  Saiba como treinar automaticamente utilizando dados da série de tempo, [com estes passos](how-to-auto-train-forecast.md).

### <a name="jupyter-notebook-samples"></a>Amostras de caderno jupyter 

Reveja exemplos de código detalhados e use casos no [repositório de cadernos GitHub para amostras automatizadas de aprendizagem automática](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)de máquinas .

### <a name="python-sdk-reference"></a>Referência Python SDK

Aprofundar a sua experiência em padrões de design SDK e especificações de classe com a [documentação de referência da classe AutoML.](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py&preserve-view=true) 

> [!Note]
> As capacidades automatizadas de aprendizagem automática também estão disponíveis noutras soluções da Microsoft, tais [como, ML.NET,](https://docs.microsoft.com/dotnet/machine-learning/automl-overview) [HDInsight,](../hdinsight/spark/apache-spark-run-machine-learning-automl.md) [Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated) e [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)

