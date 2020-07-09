---
title: Analisar e monitorizar a deriva de dados em conjuntos de dados (pré-visualização)
titleSuffix: Azure Machine Learning
description: Crie monitores de conjuntos de dados Azure Machine Learning (pré-visualização), monitorize a deriva de dados em conjuntos de dados e crie alertas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: 15cfa56f718290af3ae5fb87aadab70016cc8594
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84430235"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Detetar deriva de dados (pré-visualização) em conjuntos de dados
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a criar monitores de conjuntos de dados Azure Machine Learning (pré-visualização), monitorizar a deriva de dados e alterações estatísticas nos conjuntos de dados e configurar alertas.

Com monitores de conjunto de dados Azure Machine Learning, pode:
* **Analise a deriva nos seus dados** para entender como muda ao longo do tempo.
* **Monitorize os dados do modelo** para as diferenças entre o treino e a servir conjuntos de dados.
* **Monitorize novos dados** para as diferenças entre qualquer conjunto de dados de base e de destino.
* **Perfil apresenta-se em dados** para acompanhar como as propriedades estatísticas mudam ao longo do tempo.
* **Adavie os alertas sobre a deriva de dados** para avisos precoces para potenciais problemas. 

Métricas e insights estão disponíveis através do recurso [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) associado ao espaço de trabalho Azure Machine Learning.

> [!Important]
> Por favor, note que monitorizar a deriva de dados com o SDK está disponível em todas as edições, enquanto monitorizar a deriva de dados através do estúdio na web é apenas a edição da Enterprise.

## <a name="prerequisites"></a>Pré-requisitos

Para criar e trabalhar com monitores de conjunto de dados, precisa de:
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)
* Um [espaço de trabalho de aprendizagem automática Azure.](how-to-manage-workspace.md)
* O [Azure Machine Learning SDK para Python instalado,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)que inclui o pacote de conjuntos de dados azureml.
* Dados estruturados (tabulares) com um timetamp especificado na trajetória do ficheiro, nome do ficheiro ou coluna nos dados.

## <a name="what-is-data-drift"></a>O que é a deriva de dados?

No contexto da aprendizagem automática, a deriva de dados é a mudança nos dados de entrada do modelo que leva à degradação do desempenho do modelo. É uma das principais razões pelas quais a precisão do modelo se degrada ao longo do tempo, pelo que monitorizar a deriva de dados ajuda a detetar problemas de desempenho do modelo.

As causas da deriva de dados incluem: 

- Alterações de processo a montante, como a substituição de um sensor que altera as unidades de medição de centímetros para centímetros. 
- Problemas de qualidade de dados, como um sensor quebrado sempre lendo 0.
- Deriva natural nos dados, como a mudança média da temperatura com as estações do ano.
- Alteração na relação entre características ou mudança de covaria. 

Com os monitores de dataset de aprendizagem de máquinas Azure, pode configurar alertas que ajudam na deteção de deriva de dados em conjuntos de dados ao longo do tempo. 

### <a name="dataset-monitors"></a>Monitores de conjunto de dados 

Pode criar um monitor de conjuntos de dados para detetar e alertar para a deriva de dados em novos dados num conjunto de dados, analisar dados históricos para deriva e perfilar novos dados ao longo do tempo. O algoritmo de deriva de dados fornece uma medida global de mudança de dados e indicação de quais as características são responsáveis por uma investigação mais aprofundada. Os monitores do Dataset produzem uma série de outras métricas, perfis de novos dados no `timeseries` conjunto de dados. O alerta personalizado pode ser configurado em todas as métricas geradas pelo monitor através do [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Os monitores do Dataset podem ser usados para capturar rapidamente problemas de dados e reduzir o tempo para depurar o problema identificando as causas prováveis.  

Conceptualmente, existem três cenários primários para a criação de monitores de conjuntos de dados em Azure Machine Learning.

Scenario | Descrição
---|---
Monitorizar os dados de serviço de um modelo para deriva dos dados de formação do modelo | Os resultados deste cenário podem ser interpretados como monitorização de um proxy para a precisão do modelo, dado que a precisão do modelo se degrada se os dados de serviço derivarem dos dados de treino.
Monitorização de um conjunto de dados de séries de tempo para deriva de um período de tempo anterior. | Este cenário é mais geral, e pode ser usado para monitorizar conjuntos de dados envolvidos a montante ou a jusante da construção de modelos.  O conjunto de dados-alvo deve ter uma coluna de timetamp, enquanto o conjunto de dados de base pode ser qualquer conjunto de dados tabular que tenha características em comum com o conjunto de dados-alvo.
Realizando análises em dados passados. | Este cenário pode ser usado para compreender dados históricos e informar decisões em configurações para monitores de conjuntos de dados.

## <a name="how-dataset-can-monitor-data"></a>Como o conjunto de dados pode monitorizar dados

Utilizando a Azure Machine Learning, a deriva de dados é monitorizada através de conjuntos de dados. Para monitorizar a deriva de dados, é especificado um conjunto de dados de base - geralmente o conjunto de dados de formação para um modelo - especificado. Um conjunto de dados-alvo - geralmente dados de entrada de modelo - é comparado ao longo do tempo com o conjunto de dados de base. Esta comparação significa que o conjunto de dados do seu alvo deve ter uma coluna de hora especificada.

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>Definir o `timeseries` traço no conjunto de dados-alvo

O conjunto de dados-alvo precisa de ter o `timeseries` traço definido nele especificando a coluna de hora marcada a partir de uma coluna nos dados ou de uma coluna virtual derivada do padrão de trajetória dos ficheiros. Isto pode ser feito através do estúdio Python SDK ou Azure Machine Learning. Deve especificar-se uma coluna que represente um semão de tempo "grão fino" para adicionar `timeseries` características ao conjunto de dados. Se os seus dados forem divididos na estrutura de pastas com informações de tempo, tais como '{yyyy/MM/dd}', pode criar uma coluna virtual através da definição do padrão do caminho e defini-lo como o timetamp "grão grosso" para melhorar a importância da funcionalidade da série de tempo. 

#### <a name="python-sdk"></a>SDK Python

O [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) método da classe define a coluna de carimbo de tempo para o conjunto de [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) dados. 

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

Para obter um exemplo completo da utilização `timeseries` do traço de conjuntos de dados, consulte o caderno de [exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) ou a [documentação SDK dos conjuntos de dados](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-).

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Se criar o seu conjunto de dados utilizando o estúdio Azure Machine Learning, certifique-se de que o caminho para os seus dados contém informações de timetamp, inclua todas as sub-dobradeiras com dados e defina o formato de partição. 

No exemplo seguinte, todos os dados do sub-dobrador *NoaaIsdFlorida/2019* são recolhidos, e o formato de partição especifica o ano, mês e dia da data. 

[![Formato de partição](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

Nas definições de **Schema,** especifique a coluna de marcação de tempo de uma coluna virtual ou real no conjunto de dados especificado:

![Carimbo de data/hora](./media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>Definições do monitor do conjunto de dados

Assim que criar o conjunto de dados com as definições de marcação de tempos especificadas, está pronto para configurar o seu monitor de conjuntos de dados.

As várias definições do monitor do conjunto de dados são divididas em três grupos: **Informações básicas, definições de monitor** e **definições de backfill**.

### <a name="basic-info"></a>Informação básica

Esta tabela contém definições básicas utilizadas para o monitor do conjunto de dados.

| Definição | Descrição | Sugestões | Mutável | 
| ------- | ----------- | ---- | ------- | 
| Name | Nome do monitor do conjunto de dados. | | Não |
| Conjunto de dados de base | Conjunto de dados tabulares que será usado como base para comparação do conjunto de dados-alvo ao longo do tempo. | O conjunto de dados de base deve ter características em comum com o conjunto de dados-alvo. Geralmente, a linha de base deve ser definida para o conjunto de dados de treino de um modelo ou uma fatia do conjunto de dados-alvo. | Não |
| Conjunto de dados-alvo | Conjunto de dados tabular com coluna de hora especificada que será analisada para deriva de dados. | O conjunto de dados-alvo deve ter funcionalidades em comum com o conjunto de dados de base, e deve ser um `timeseries` conjunto de dados, a que os novos dados são anexados. Os dados históricos no conjunto de dados-alvo podem ser analisados ou novos dados podem ser monitorizados. | Não | 
| Frequência | A frequência que será usada para agendar o trabalho do oleoduto e analisar dados históricos se executar um enchimento traseiro. As opções incluem diariamente, semanalmente ou mensalmente. | Ajuste esta definição para incluir um tamanho comparável de dados à linha de base. | Não | 
| Funcionalidades | Lista de funcionalidades que serão analisadas para deriva de dados ao longo do tempo. | Definir para a(s) características de saída de um modelo para medir a deriva do conceito. Não inclua características que naturalmente se desviem ao longo do tempo (mês, ano, índice, etc.). Pode preencher de costas e monitorizar a deriva de dados existente depois de ajustar a lista de funcionalidades. | Sim | 
| Destino de computação | Azure Machine Learning computação alvo para executar os trabalhos de monitor de conjunto de dados. | | Sim | 

### <a name="monitor-settings"></a>Definições de monitor

Estas definições destinam-se ao pipeline de monitor de conjunto de dados programado, que será criado. 

| Definição | Descrição | Sugestões | Mutável | 
| ------- | ----------- | ---- | ------- |
| Ativar | Ativar ou desativar o horário no pipeline do monitor do conjunto de dados | Desative o calendário para analisar dados históricos com a definição de enchimento. Pode ser ativado após a criação do monitor do conjunto de dados. | Sim | 
| Latência | O tempo, em horas, leva para os dados chegarem ao conjunto de dados. Por exemplo, se os dados demorarem três dias a chegar ao SQL DB, o conjunto de dados encapsula, definir a latência para 72. | Não pode ser alterado após a criação do monitor do conjunto de dados | Não | 
| Endereços de e-mail | Endereços de e-mail para alerta com base na violação do limiar percentual de deriva de dados. | Os e-mails são enviados através do Azure Monitor. | Sim | 
| Limiar | Limite percentual de deriva de dados para alerta de e-mail. | Outros alertas e eventos podem ser definidos em muitas outras métricas no recurso de Insights de Aplicação associado do espaço de trabalho. | Sim | 

### <a name="backfill-settings"></a>Definições de enchimento de retos

Estas definições são para executar um enchimento em dados passados para métricas de deriva de dados.

| Definição | Descrição | Sugestões |
| ------- | ----------- | ---- |
| Data de início | Data de início do trabalho de retos. | | 
| Data de fim | Data de fim do trabalho de retos. | A data de fim não pode ser mais de 31*unidades de frequência a partir da data de início. Num monitor de conjuntos de dados existente, as métricas podem ser preenchidas para analisar dados históricos ou substituir métricas por configurações atualizadas. |

## <a name="create-dataset-monitors"></a>Criar monitores de conjuntos de dados 

Crie monitores de conjunto de dados para detetar e alertar para a deriva de dados num novo conjunto de dados com o estúdio Azure Machine Learning ou o Python SDK. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning studio
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Para configurar alertas no seu monitor de conjuntos de dados, o espaço de trabalho que contém o conjunto de dados para o quais pretende criar um monitor deve ter capacidades de edição Enterprise. 

Após a confirmação da funcionalidade do espaço de trabalho, navegue para a página inicial do estúdio e selecione o separador Datasets à esquerda. Selecione monitores do Conjunto de Dados.

![Lista de monitores](./media/how-to-monitor-datasets/monitor-list.png)

Clique no botão **+Criar monitor** e continue através do assistente clicando em **Seguinte**.

![Feiticeiro](./media/how-to-monitor-datasets/wizard.png)

O monitor de conjunto de dados resultante aparecerá na lista. Selecione-o para ir à página de detalhes do monitor.

### <a name="from-python-sdk"></a>Do Python SDK

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

## <a name="understanding-data-drift-results"></a>Compreender os resultados da deriva de dados

O monitor de dados produz dois grupos de resultados: Visão geral da deriva e detalhes do recurso. A animação a seguir mostra os gráficos de monitores de deriva disponíveis com base na funcionalidade e métrica selecionadas. 

![Vídeo de demonstração](./media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>Visão geral da deriva

A secção **de visão geral drift** contém insights de alto nível sobre a magnitude da deriva de dados e quais as características que devem ser investigadas mais aprofundadamente. 

| Metric | Descrição | Sugestões | 
| ------ | ----------- | ---- | 
| Magnitude da deriva de dados | Dada como percentagem entre o conjunto de dados de base e o conjunto de dados-alvo ao longo do tempo. Variando de 0 a 100 onde 0 indica conjuntos de dados idênticos e 100 indica que a capacidade de deriva de dados de aprendizagem de máquinas Azure pode distinguir completamente os dois conjuntos de dados. | Espera-se ruído na percentagem exata medida devido às técnicas de aprendizagem automática utilizadas para gerar esta magnitude. | 
| Contribuição à deriva por recurso | A contribuição de cada recurso no conjunto de dados-alvo para a magnitude de deriva medida. |  Devido à mudança de covaria, a distribuição subjacente de uma funcionalidade não precisa necessariamente de mudar para ter uma importância relativamente elevada. | 

A imagem a seguir é um exemplo de gráficos vistos na **visão geral drift** resulta no estúdio Azure Machine Learning, resultante de um enchimento de [dados de superfície integrados no NOAA](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/). Os dados foram recolhidos `stationName contains 'FLORIDA'` para , com janeiro de 2019 a ser usado como o conjunto de dados de base e todos os dados de 2019 utilizados como alvo.
 
![Visão geral da deriva](./media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>Detalhes do recurso

A secção **de detalhes da Funcionalidade** contém insights de nível de funcionalidade sobre a alteração na distribuição da funcionalidade selecionada, bem como outras estatísticas, ao longo do tempo. 

O conjunto de dados-alvo também é perfilado ao longo do tempo. A distância estatística entre a distribuição de base de cada característica é comparada com a do conjunto de dados-alvo ao longo do tempo, que é conceptualmente semelhante à magnitude da deriva de dados, com a exceção de que esta distância estatística é para uma característica individual. Min, max e médio também estão disponíveis. 

No estúdio Azure Machine Learning, se clicar num ponto de dados no gráfico, a distribuição da funcionalidade que está a ser mostrada ajustar-se-á em conformidade. Por padrão, mostra a distribuição do conjunto de dados de base e a distribuição mais recente da mesma funcionalidade. 

Estas métricas também podem ser recuperadas no Python SDK através do `get_metrics()` método de um `DataDriftDetector` objeto. 

#### <a name="numeric-features"></a>Características numéricas 

As funções numéricas são perfiladas em cada execução do monitor de conjuntos de dados. Os seguintes são expostos no estúdio Azure Machine Learning. A densidade de probabilidade é mostrada para a distribuição.

| Metric | Descrição |  
| ------ | ----------- |  
| Distância wasserstein | Quantidade mínima de trabalho para transformar a distribuição de base na distribuição do alvo. |
| Valor médio | Valor médio da característica. |
| Valor mínimo | Valor mínimo da característica. |
| Valor máximo | Valor máximo da característica. |

![Detalhes da funcionalidade numéricos](./media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>Características categóricas 

As funções numéricas são perfiladas em cada execução do monitor de conjuntos de dados. Os seguintes são expostos no estúdio Azure Machine Learning. Um histograma é mostrado para a distribuição.

| Metric | Descrição |  
| ------ | ----------- |  
| Distância euclidiana | Distância geométrica entre a linha de base e as distribuições de alvos. |
| Valores únicos | Número de valores únicos (cardinalidade) da característica. |


![Detalhes da funcionalidade categóricos](./media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>Métricas, alertas e eventos

As métricas podem ser consultadas no recurso [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) associado ao seu espaço de trabalho de aprendizagem automática. O que dá acesso a todas as funcionalidades de Application Insights, incluindo configuração para regras de alerta personalizadas e grupos de ação para desencadear uma ação como, uma Função de E-mail/SMS/Push/Voice ou Azure. Consulte a documentação completa do Application Insights para obter mais detalhes. 

Para começar, navegue até ao portal Azure e selecione a página geral do seu espaço **de trabalho.**  O recurso Application Insights associado é da extrema-direita:

[![Descrição geral do portal do Azure](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Selecione Registos (Analytics) em Monitorização no painel esquerdo:

![Visão geral das informações sobre a aplicação](./media/how-to-monitor-datasets/ai-overview.png)

As métricas do monitor do conjunto de dados são armazenadas como `customMetrics` . Pode escrever e executar uma consulta depois de configurar um monitor de conjunto de dados para os visualizar:

[![Consulta de análise de registo](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Depois de identificar métricas para configurar regras de alerta, crie uma nova regra de alerta:

![Nova regra de alerta](./media/how-to-monitor-datasets/alert-rule.png)

Pode utilizar um grupo de ação existente ou criar um novo para definir as medidas a tomar quando as condições definidas estiverem reunidas:

![Novo grupo de ação](./media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>Resolução de problemas

Limitações e questões conhecidas:

* A gama de tempo de trabalhos de reenchimento está limitada a 31 intervalos da regulação de frequência do monitor. 
* Limitação de 200 funcionalidades, a menos que não seja especificada uma lista de recursos (todas as funcionalidades utilizadas).
* O tamanho do cálculo deve ser grande o suficiente para lidar com os dados. 
* Certifique-se de que o seu conjunto de dados tem dados dentro da data de início e de fim para uma determinada execução do monitor.
* Os monitores do Dataset só funcionarão em conjuntos de dados que contenham 50 linhas ou mais. 

Colunas ou características, no conjunto de dados são classificadas como categóricas ou numéricas com base nas condições do quadro seguinte. Se a funcionalidade não corresponder a estas condições - por exemplo, uma coluna de tipo string com >100 valores únicos - a funcionalidade é retirada do nosso algoritmo de deriva de dados, mas ainda está perfilada. 

| Tipo de recurso | Tipo de dados | Condição | Limitações | 
| ------------ | --------- | --------- | ----------- |
| Categórico | corda, bool, int, flutuar | O número de valores únicos na funcionalidade é inferior a 100 e menos de 5% do número de linhas. | O nulo é tratado como a sua própria categoria. | 
| Numérico | int, flutuar | Os valores na funcionalidade são de um tipo de dado numérico e não correspondem à condição de uma característica categórica. | A função caiu se >15% dos valores forem nulos. | 

## <a name="next-steps"></a>Próximos passos

* Dirija-se ao [estúdio Azure Machine Learning](https://ml.azure.com) ou ao caderno [Python](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) para configurar um monitor de conjunto de dados.
* Veja como configurar a deriva de dados em [modelos implantados no Serviço Azure Kubernetes](how-to-monitor-data-drift.md).
* Configurar monitores de deriva do conjunto de dados com [grelha de eventos.](how-to-use-event-grid.md) 
