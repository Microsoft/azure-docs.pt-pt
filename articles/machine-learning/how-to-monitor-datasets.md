---
title: Detetar deriva de dados em conjuntos de dados (pré-visualização)
titleSuffix: Azure Machine Learning
description: Saiba como configurar a deteção de deriva de dados em Azure Learning. Criar monitores de conjuntos de dados (pré-visualização), monitorizar para deriva de dados e configurar alertas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 06/25/2020
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.openlocfilehash: 1622f8ce988c5592ac96cec798617ca6ac37aa8d
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617175"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Detetar deriva de dados (pré-visualização) em conjuntos de dados


> [!IMPORTANT]
> A deteção de deriva de dados para conjuntos de dados está atualmente em visualização pública.
> A versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Aprenda a monitorizar a deriva de dados e desemprete alertas quando a deriva é elevada.  

Com monitores de conjunto de dados Azure Machine Learning (pré-visualização), pode:
* **Analise a deriva nos seus dados** para entender como muda ao longo do tempo.
* **Monitorize os dados do modelo** para as diferenças entre o treino e a servir conjuntos de dados.  Comece por [recolher dados de modelos a partir de modelos implantados.](how-to-enable-data-collection.md)
* **Monitorize novos dados** para as diferenças entre qualquer conjunto de dados de base e de destino.
* **Perfil apresenta-se em dados** para acompanhar como as propriedades estatísticas mudam ao longo do tempo.
* **Adavie os alertas sobre a deriva de dados** para avisos precoces para potenciais problemas. 

Um [conjunto de dados de aprendizagem Azure Machine](how-to-create-register-datasets.md) é utilizado para criar o monitor. O conjunto de dados deve incluir uma coluna de horatamp.

Pode ver métricas de deriva de dados com o Python SDK ou no estúdio Azure Machine Learning.  Outras métricas e insights estão disponíveis através do recurso [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) associado ao espaço de trabalho Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

Para criar e trabalhar com monitores de conjunto de dados, precisa de:
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)
* Um [espaço de trabalho de aprendizagem automática Azure.](how-to-manage-workspace.md)
* O [Azure Machine Learning SDK para Python instalado,](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)que inclui o pacote de conjuntos de dados azureml.
* Dados estruturados (tabulares) com um timetamp especificado na trajetória do ficheiro, nome do ficheiro ou coluna nos dados.

## <a name="what-is-data-drift"></a>O que é a deriva de dados?

A deriva de dados é uma das principais razões pelas quais a precisão do modelo se degrada ao longo do tempo.  Para os modelos de aprendizagem automática, a deriva de dados é a mudança nos dados de entrada do modelo que leva à degradação do desempenho do modelo.  Monitorizar a deriva de dados ajuda a detetar estes problemas de desempenho do modelo.

As causas da deriva de dados incluem:

- Alterações de processo a montante, como a substituição de um sensor que altera as unidades de medição de centímetros para centímetros. 
- Problemas de qualidade de dados, como um sensor quebrado sempre lendo 0.
- Deriva natural nos dados, como a mudança média da temperatura com as estações do ano.
- Alteração na relação entre características ou mudança de covaria. 

A Azure Machine Learning simplifica a deteção de deriva com a computação de uma única métrica que abstrai a complexidade dos conjuntos de dados que estão a ser comparados.  Estes conjuntos de dados podem ter centenas de funcionalidades e dezenas de milhares de linhas. Uma vez detetada a deriva, perfura-se em que características estão a causar a deriva.  Em seguida, inspecione as métricas do nível da funcionalidade para depurar e isolar a causa da deriva.

Esta abordagem de cima para baixo facilita a monitorização dos dados em vez das técnicas tradicionais baseadas em regras. Técnicas baseadas em regras, tais como a gama de dados permitida ou valores únicos permitidos podem ser demorados e propensos a erros.

No Azure Machine Learning, utiliza monitores de conjunto de dados para detetar e alertar para a deriva de dados.
  
### <a name="dataset-monitors"></a>Monitores de conjunto de dados 

Com um monitor de conjunto de dados pode:

* Detetar e alertar para a deriva de dados em novos dados num conjunto de dados.
* Analise os dados históricos para a deriva.
* Perfilar novos dados ao longo do tempo.

O algoritmo de deriva de dados fornece uma medida global de mudança de dados e indicação de quais as características são responsáveis por uma investigação mais aprofundada. Os monitores do Dataset produzem uma série de outras métricas, perfis de novos dados no `timeseries` conjunto de dados. 

O alerta personalizado pode ser configurado em todas as métricas geradas pelo monitor através do [Azure Application Insights](../azure-monitor/app/app-insights-overview.md). Os monitores do Dataset podem ser usados para capturar rapidamente problemas de dados e reduzir o tempo para depurar o problema identificando as causas prováveis.  

Conceptualmente, existem três cenários primários para a criação de monitores de conjuntos de dados em Azure Machine Learning.

Cenário | Descrição
---|---
Monitorize os dados de serviço de um modelo para deriva dos dados de formação | Os resultados deste cenário podem ser interpretados como monitorização de um proxy para a precisão do modelo, uma vez que a precisão do modelo se degrada quando os dados de serviço derivam dos dados de treino.
Monitorize um conjunto de dados de séries de tempo para deriva de um período de tempo anterior. | Este cenário é mais geral, e pode ser usado para monitorizar conjuntos de dados envolvidos a montante ou a jusante da construção de modelos.  O conjunto de dados do alvo deve ter uma coluna de horatamp. O conjunto de dados de base pode ser qualquer conjunto de dados tabular que tenha funcionalidades em comum com o conjunto de dados-alvo.
Realizar análises em dados passados. | Este cenário pode ser usado para compreender dados históricos e informar decisões em configurações para monitores de conjuntos de dados.

Os monitores do Dataset dependem dos seguintes serviços Azure.

|Serviço do Azure  |Descrição  |
|---------|---------|
| *Conjunto de dados* | Drift usa conjuntos de dados de Machine Learning para recuperar dados de treino e comparar dados para a formação de modelos.  Gerar perfil de dados é usado para gerar algumas das métricas relatadas, tais como min, máx, valores distintos, valores distintos contam. |
| *Gasoduto e computação Azureml* | O trabalho de cálculo à deriva está alojado no gasoduto azureml.  O trabalho é desencadeado a pedido ou por horário para funcionar num cálculo configurado no tempo de criação do monitor de drift.
| *Insights de aplicação*| Drift emite métricas para Application Insights pertencentes ao espaço de trabalho de machine learning.
| *Armazenamento de bolhas Azure*| Drift emite métricas em formato json para armazenamento de bolhas Azure.

## <a name="how-dataset-monitors-data"></a>Como o conjunto de dados monitoriza os dados

Utilize conjuntos de dados de Machine Learning para monitorizar a deriva de dados. Especifique um conjunto de dados de base - geralmente o conjunto de dados de treino para um modelo. Um conjunto de dados-alvo - geralmente dados de entrada de modelo - é comparado ao longo do tempo com o conjunto de dados de base. Esta comparação significa que o conjunto de dados do seu alvo deve ter uma coluna de hora especificada.

## <a name="create-target-dataset"></a>Criar conjunto de dados-alvo

O conjunto de dados-alvo necessita do `timeseries` traço definido, especificando a coluna de hora a partir de uma coluna nos dados ou de uma coluna virtual derivada do padrão de trajetória dos ficheiros. Crie o conjunto de dados com um timetamp através do estúdio [Python SDK](#sdk-dataset) ou [Azure Machine Learning](#studio-dataset). Uma coluna que represente uma "timetamp" deve ser especificada para adicionar `timeseries` traço ao conjunto de dados. Se os seus dados forem divididos na estrutura de pastas com informações de tempo, tais como '{y/MM/dd}', crie uma coluna virtual através da definição do padrão do caminho e defina-o como "partição de tempo" para melhorar a importância da funcionalidade da série de tempo.

### <a name="python-sdk"></a><a name="sdk-dataset"></a>SDK Python

O [`Dataset`](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=truewith-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) método de classe define a coluna de carimbo de tempo para o conjunto de [`with_timestamp_columns()`](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=truewith-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)  dados.

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

Para obter um exemplo completo da utilização `timeseries` do traço de conjuntos de dados, consulte o caderno de [exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) ou a [documentação SDK dos conjuntos de dados](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=truewith-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-).

### <a name="azure-machine-learning-studio"></a><a name="studio-dataset"></a>Estúdio Azure Machine Learning

Se criar o seu conjunto de dados utilizando o estúdio Azure Machine Learning, certifique-se de que o caminho para os seus dados contém informações de timetamp, inclua todas as sub-dobradeiras com dados e defina o formato de partição.

No exemplo seguinte, todos os dados do sub-dobrador *NoaaIsdFlorida/2019* são recolhidos, e o formato de partição especifica o ano, mês e dia da data.

[![Formato de partição](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

Nas definições de **Schema,** especifique a coluna de marcação de tempo de uma coluna virtual ou real no conjunto de dados especificado:

:::image type="content" source="media/how-to-monitor-datasets/timestamp.png" alt-text="Definir a estada de tempo":::

Se os seus dados forem divididos por data, como é o caso aqui, também pode especificar o partition_timestamp.  Isto permite um processamento mais eficiente das datas.

:::image type="content" source="media/how-to-monitor-datasets/timeseries-partitiontimestamp.png" alt-text="Tempo de partição":::


## <a name="create-dataset-monitors"></a>Criar monitores de conjuntos de dados

Crie monitores de conjunto de dados para detetar e alertar para a deriva de dados num novo conjunto de dados.  Utilize o estúdio [Python SDK](#sdk-monitor) ou [Azure Machine Learning](#studio-monitor).

### <a name="python-sdk"></a><a name="sdk-monitor"></a>SDK Python

Consulte a [documentação de referência da Python SDK sobre a deriva de dados](/python/api/azureml-datadrift/azureml.datadrift) para obter todos os detalhes. 

O exemplo a seguir mostra como criar um monitor de conjunto de dados utilizando o Python SDK

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

Para obter um exemplo completo de configuração de um conjunto de `timeseries` dados e detetor de deriva de dados, consulte o nosso [caderno de exemplo](https://aka.ms/datadrift-notebook).

### <a name="azure-machine-learning-studio"></a><a name="studio-monitor"></a> Estúdio Azure Machine Learning

1. Navegue para a [página inicial do estúdio.](https://ml.azure.com)
1. Selecione o **separador Datasets** à esquerda. 
1. Selecione **monitores dataset**.
   ![Lista de monitores](./media/how-to-monitor-datasets/monitor-list.png)

1. Clique no botão **+Criar monitor** e continue através do assistente clicando em **Seguinte**.  

:::image type="content" source="media/how-to-monitor-datasets/wizard.png" alt-text="Criar um assistente de monitor":::

* **Selecione o conjunto de dados-alvo**.  O conjunto de dados-alvo é um conjunto de dados tabular com coluna de timetamp especificada que será analisada para deriva de dados. O conjunto de dados-alvo deve ter funcionalidades em comum com o conjunto de dados de base, e deve ser um `timeseries` conjunto de dados, a que os novos dados são anexados. Os dados históricos no conjunto de dados-alvo podem ser analisados ou novos dados podem ser monitorizados.

* **Selecione conjunto de dados de base.**  Selecione o conjunto de dados tabular para ser usado como base para comparação do conjunto de dados-alvo ao longo do tempo.  O conjunto de dados de base deve ter características em comum com o conjunto de dados-alvo.  Selecione um intervalo de tempo para utilizar uma fatia do conjunto de dados-alvo ou especifique um conjunto de dados separado para usar como linha de base.

* **Regulação do monitor**.  Estas definições destinam-se ao pipeline de monitor de conjunto de dados programado, que será criado. 

    | Definições | Descrição | Sugestões | Mutável | 
    | ------- | ----------- | ---- | ------- |
    | Nome | Nome do monitor do conjunto de dados. | | Não |
    | Funcionalidades | Lista de funcionalidades que serão analisadas para deriva de dados ao longo do tempo. | Definir para a(s) características de saída de um modelo para medir a deriva do conceito. Não inclua características que naturalmente se desviem ao longo do tempo (mês, ano, índice, etc.). Pode preencher de costas e monitorizar a deriva de dados existente depois de ajustar a lista de funcionalidades. | Sim | 
    | Destino de computação | Azure Machine Learning computação alvo para executar os trabalhos de monitor de conjunto de dados. | | Sim | 
    | Ativar | Ativar ou desativar o horário no pipeline do monitor do conjunto de dados | Desative o calendário para analisar dados históricos com a definição de enchimento. Pode ser ativado após a criação do monitor do conjunto de dados. | Sim | 
    | Frequência | A frequência que será usada para agendar o trabalho do oleoduto e analisar dados históricos se executar um enchimento traseiro. As opções incluem diariamente, semanalmente ou mensalmente. | Cada execução compara dados no conjunto de dados-alvo de acordo com a frequência: <li>Diariamente: Compare o dia completo mais recente no conjunto de dados-alvo com a linha de base <li>Semanal: Compare a semana completa mais recente (segunda-feira a domingo) em conjunto de dados-alvo com linha de base <li>Mensalmente: Compare o mês completo mais recente no conjunto de dados-alvo com a linha de base | Não | 
    | Latência | O tempo, em horas, leva para os dados chegarem ao conjunto de dados. Por exemplo, se os dados demorarem três dias a chegar ao SQL DB, o conjunto de dados encapsula, definir a latência para 72. | Não pode ser alterado após a criação do monitor do conjunto de dados | Não | 
    | Endereços de e-mail | Endereços de e-mail para alerta com base na violação do limiar percentual de deriva de dados. | Os e-mails são enviados através do Azure Monitor. | Sim | 
    | Limite | Limite percentual de deriva de dados para alerta de e-mail. | Outros alertas e eventos podem ser definidos em muitas outras métricas no recurso de Insights de Aplicação associado do espaço de trabalho. | Sim |

Após terminar o assistente, aparecerá na lista o monitor de conjuntos de dados resultante. Selecione-o para ir à página de detalhes do monitor.

## <a name="understand-data-drift-results"></a>Compreender os resultados da deriva de dados

Esta secção mostra-lhe os resultados da monitorização de um conjunto de **dados,** encontrado na página de  /  **monitores datasets do Datasets** no estúdio Azure.  Pode atualizar as definições, bem como analisar os dados existentes durante um período de tempo específico nesta página.  

Comece com os conhecimentos de alto nível sobre a magnitude da deriva de dados e um destaque das características a serem investigadas.

:::image type="content" source="media/how-to-monitor-datasets/drift-overview.png" alt-text="Visão geral da deriva":::


| Métrica | Descrição | 
| ------ | ----------- | 
| Magnitude da deriva de dados | Uma percentagem de deriva entre o conjunto de dados de base e o alvo ao longo do tempo. Variar entre 0 e 100, 0 indica conjuntos de dados idênticos e 100 indica que o modelo de deriva de dados Azure Machine Learning pode distinguir completamente os dois conjuntos de dados. Espera-se ruído na percentagem exata medida devido às técnicas de aprendizagem automática utilizadas para gerar esta magnitude. | 
| Principais características à deriva | Mostra as características do conjunto de dados que mais têm derivado e estão, portanto, a contribuir mais para a métrica de Magnitude da Deriva. Devido à mudança de covaria, a distribuição subjacente de uma funcionalidade não precisa necessariamente de mudar para ter uma importância relativamente elevada. |
| Limite | A magnitude da deriva de dados para além do limiar definido irá desencadear alertas. Isto pode ser configurado nas definições do monitor. | 

### <a name="drift-magnitude-trend"></a>Tendência de magnitude de deriva

Veja como o conjunto de dados difere do conjunto de dados-alvo no período de tempo especificado.  Quanto mais perto de 100%, mais os dois conjuntos de dados diferem.

:::image type="content" source="media/how-to-monitor-datasets/drift-magnitude.png" alt-text="Tendência de magnitude de deriva":::

### <a name="drift-magnitude-by-features"></a>Magnitude de deriva por características

Esta secção contém insights de nível de funcionalidade sobre a alteração na distribuição da funcionalidade selecionada, bem como outras estatísticas, ao longo do tempo.

O conjunto de dados-alvo também é perfilado ao longo do tempo. A distância estatística entre a distribuição de base de cada característica é comparada com a do conjunto de dados-alvo ao longo do tempo.  Conceptualmente, isto é semelhante à magnitude da deriva de dados.  No entanto, esta distância estatística é para uma característica individual e não para todas as características. Min, max e médio também estão disponíveis.

No estúdio Azure Machine Learning, clique numa barra no gráfico para ver os detalhes do nível de funcionalidade para essa data. Por padrão, vê a distribuição do conjunto de dados de base e a distribuição mais recente da mesma funcionalidade.

:::image type="content" source="media/how-to-monitor-datasets/drift-by-feature.gif" alt-text="Magnitude de deriva por características":::

Estas métricas também podem ser recuperadas no Python SDK através do `get_metrics()` método de um `DataDriftDetector` objeto.

### <a name="feature-details"></a>Detalhes do recurso

Por fim, desloque-se para ver os detalhes de cada recurso individual.  Utilize as descidas acima do gráfico para selecionar a função e, adicionalmente, selecione a métrica que pretende visualizar.

:::image type="content" source="media/how-to-monitor-datasets/numeric-feature.gif" alt-text="Gráfico e comparação de características numéricas":::

As métricas na tabela dependem do tipo de recurso.

* Características numéricas

    | Métrica | Descrição |  
    | ------ | ----------- |  
    | Distância wasserstein | Quantidade mínima de trabalho para transformar a distribuição de base na distribuição do alvo. |
    | Valor médio | Valor médio da característica. |
    | Valor mínimo | Valor mínimo da característica. |
    | Valor máximo | Valor máximo da característica. |

* Características categóricas
    
    | Métrica | Descrição |  
    | ------ | ----------- |  
    | Distância euclidiana     |  Computado para colunas categóricas. A distância euclideana é calculada em dois vetores, gerados a partir da distribuição empírica da mesma coluna categórica a partir de dois conjuntos de dados. 0 indica que não há diferença nas distribuições empíricas.  Quanto mais se desvia de 0, mais esta coluna se desvia. As tendências podem ser observadas a partir de um enredo de série sonora desta métrica e podem ser úteis para descobrir uma característica à deriva.  |
    | Valores únicos | Número de valores únicos (cardinalidade) da característica. |

Neste gráfico, selecione uma única data para comparar a distribuição de funcionalidades entre o alvo e esta data para a funcionalidade apresentada. Para funcionalidades numéricas, isto mostra duas distribuições de probabilidades.  Se a característica for numérica, é mostrado um gráfico de barras.

:::image type="content" source="media/how-to-monitor-datasets/select-date-to-compare.gif" alt-text="Selecione uma data para comparar com o alvo":::

## <a name="metrics-alerts-and-events"></a>Métricas, alertas e eventos

As métricas podem ser consultadas no recurso [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) associado ao seu espaço de trabalho de aprendizagem automática. Tem acesso a todas as funcionalidades de Application Insights, incluindo configuração para regras de alerta personalizadas e grupos de ação para desencadear uma ação como, uma Função de E-mail/SMS/Push/Voice ou Azure. Consulte a documentação completa do Application Insights para obter mais detalhes. 

Para começar, navegue até ao [portal Azure](https://portal.azure.com) e selecione a página geral do seu espaço **de trabalho.**  O recurso Application Insights associado é da extrema-direita:

[![Descrição geral do portal do Azure](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Selecione Registos (Analytics) em Monitorização no painel esquerdo:

![Visão geral das informações sobre a aplicação](./media/how-to-monitor-datasets/ai-overview.png)

As métricas do monitor do conjunto de dados são armazenadas como `customMetrics` . Pode escrever e executar uma consulta depois de configurar um monitor de conjunto de dados para os visualizar:

[![Consulta de análise de registo](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Depois de identificar métricas para configurar regras de alerta, crie uma nova regra de alerta:

![Nova regra de alerta](./media/how-to-monitor-datasets/alert-rule.png)

Pode utilizar um grupo de ação existente ou criar um novo para definir as medidas a tomar quando as condições definidas estiverem reunidas:

![Novo grupo de ação](./media/how-to-monitor-datasets/action-group.png)

## <a name="next-steps"></a>Passos seguintes

* Dirija-se ao [estúdio Azure Machine Learning](https://ml.azure.com) ou ao caderno [Python](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) para configurar um monitor de conjunto de dados.
* Veja como configurar a deriva de dados em [modelos implantados no Serviço Azure Kubernetes](./how-to-enable-data-collection.md).
* Configurar monitores de deriva do conjunto de dados com [grelha de eventos.](how-to-use-event-grid.md) 
* Veja estas [dicas comuns de resolução de problemas](resource-known-issues.md#data-drift) se tiver problemas.
