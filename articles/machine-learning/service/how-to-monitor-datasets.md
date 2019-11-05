---
title: Analise e monitore a descompasso de dados em DataSets (visualização)
titleSuffix: Azure Machine Learning
description: Crie Azure Machine Learning monitores de conjuntos de dados (versão prévia), monitore a descompasso de data em conjuntos de dados e alertas de instalação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: 88da346b3367ffd20d1a28d1d8cc45364e4f862f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515294"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Detectar descompasso de dados (versão prévia) em conjuntos
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a criar Azure Machine Learning monitores de conjunto de dados (versão prévia), monitorar as alterações estatísticas e descompasso e os alertas de configuração.

Com os monitores Azure Machine Learning DataSet, você pode:
* **Analise a descompasso em seus dados** para entender como ele muda ao longo do tempo.
* **Monitore os dados do modelo** para obter diferenças entre o treinamento e o fornecimento de conjuntos.
* **Monitore novos dados** para obter diferenças entre qualquer linha de base e um DataSet de destino.
* **Recursos de perfil em dados** para controlar como as propriedades estatísticas são alteradas com o passar do tempo.
* **Configure alertas sobre descompasso de dados** para avisos antecipados a problemas potenciais. 

As métricas e as informações estão disponíveis por meio do Aplicativo Azure recurso de [informações](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) associadas ao espaço de trabalho do Azure Machine Learning Service.

## <a name="prerequisites"></a>Pré-requisitos

Para criar e trabalhar com monitores de conjunto de trabalho, você precisa de:
* Uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje.
* Um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md).
* O [SDK do Azure Machine Learning para Python instalado](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), que inclui o pacote de conjuntos de linhas do azureml.
* Dados estruturados (tabulares) com um carimbo de data/hora especificado no caminho do arquivo, nome do arquivo ou coluna nos dados.

## <a name="what-is-data-drift"></a>O que é descompasso de dados?

No contexto do Machine Learning, a descompasso de dados é a alteração nos dados de entrada do modelo que leva à degradação do desempenho do modelo. É um dos principais motivos pelos quais a precisão do modelo diminui ao longo do tempo, o que monitora a descompasso de dados ajuda a detectar problemas de desempenho do modelo.

As causas de descompasso de dados incluem: 

- Alterações no processo de upstream, como um sensor sendo substituído que altera as unidades de medida de polegadas para centímetros. 
- Problemas de qualidade de dados, como um sensor quebrado sempre lendo 0.
- Descompasso natural nos dados, como a mudança de temperatura média com as estações.
- Alteração em relação entre recursos ou deslocamento de covariable. 

Com os monitores de conjunto de dados Azure Machine Learning, você pode configurar alertas que auxiliam na detecção de descompasso de dado em DataSets ao longo do tempo. 

### <a name="dataset-monitors"></a>Monitores de DataSet 

Você pode criar um monitor de conjunto de dados para detectar e alertar sobre a descompasso do dado em novos dados em um DataSet, analisar dados históricos para descompasso e criar o perfil de novos dados ao longo do tempo. O algoritmo de descompasso de dados fornece uma medida geral de alteração nos dados e a indicação de quais recursos são responsáveis por investigações adicionais. Os monitores de conjunto de dados produzem uma série de outras métricas por meio da criação de perfil de novos dados no DataSet da série temporal. Os alertas personalizados podem ser configurados em todas as métricas geradas pelo monitor por meio do [aplicativo Azure insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Os monitores de conjunto de dados podem ser usados para detectar rapidamente problemas de dado e reduzir o tempo para depurar o problema identificando as causas prováveis.  

Conceitualmente, há três cenários principais para configurar monitores de conjuntos de conjunto de Azure Machine Learning.

Cenário | Descrição
---|---
Monitorando os dados de serviço de um modelo para descompasso dos dados de treinamento do modelo | Os resultados desse cenário podem ser interpretados como monitoramento de um proxy para a precisão do modelo, Considerando que a precisão do modelo diminui se o fornecimento de dados descompassos dos dados de treinamento.
Monitoramento de um conjunto de uma série temporal para descompasso de um período de tempo anterior. | Esse cenário é mais geral e pode ser usado para monitorar conjuntos de linhas envolvidos no upstream ou no downstream da construção do modelo.  O conjunto de recursos de destino deve ter uma coluna timestamp, enquanto o conjunto de linha de base pode ser qualquer conjunto de tabelas de tabela que tenha recursos em comum com o DataSet de destino.
Executando a análise nos dados anteriores. | Isso pode ser usado para entender dados históricos e informar decisões em configurações para monitores de conjuntos de dados.

## <a name="how-dataset-monitors-work-in-azure-machine-learning"></a>Como o DataSet monitora o trabalho no Azure Machine Learning

Usando o Azure Machine Learning, a descompasso de dados é monitorada por meio de DataSets. Para monitorar a descompasso de dados, é um DataSet de linha de base – geralmente o conjunto de dado de treinamento para um modelo-é especificado. Um conjunto de dados de destino-geralmente, o modelo de dado de entrada, é comparado ao longo do tempo para o conjunto de dados de Isso significa que o DataSet de destino deve ter uma coluna timestamp especificada.

### <a name="setting-the-timeseries-trait-in-the-target-dataset"></a>Definindo a característica de `timeseries` no conjunto de entrada de destino

O conjunto de dados de destino precisa ter a característica `timeseries` configurada especificando a coluna timestamp a partir de uma coluna nos dados ou uma coluna virtual derivada do padrão de caminho dos arquivos. Isso pode ser feito por meio do SDK do Python ou do Azure Machine Learning Studio. Uma coluna que representa um carimbo de data/hora "refinado" deve ser especificada para adicionar `timeseries` característica ao DataSet. Se os dados forem particionados na estrutura de pastas com informações de tempo, como ' {YYYY/MM/DD} ', você poderá criar uma coluna virtual por meio da configuração de padrão de caminho e defini-la como o carimbo de data/hora "granular" para melhorar a importância da funcionalidade de série temporal. 

#### <a name="python-sdk"></a>Python SDK

O método de [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) classe ' [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) define a coluna de carimbo de data/hora para o conjunto de um. 

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

Para obter um exemplo completo de como usar a característica `timeseries` de conjuntos de valores, consulte o [bloco de anotações de exemplo](http://aka.ms/azureml-tsd-notebook) ou a documentação do SDK dos conjuntos de [valores](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-).

#### <a name="azure-machine-learning-studio"></a>Azure Machine Learning studio
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku-inline.md)]

Se você criar seu conjunto de dados usando o Azure Machine Learning Studio, certifique-se de que o caminho para os data contém informações de carimbo de data/hora, inclua todas as subpastas com dados e defina o formato da partição. 

No exemplo a seguir, todos os dados na subpasta *NoaaIsdFlorida/2019* são tirados e o formato de partição especifica o ano, o mês e o dia do carimbo de data/hora. 

[formato de partição de ![](media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

Nas configurações de **esquema** , especifique a coluna timestamp de uma coluna virtual ou real no conjunto de espaço especificado:

![Carimbo de data/hora](media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>Configurações do monitor de DataSet

Depois de criar seu conjunto de um com as configurações de carimbo de data/hora especificadas, você estará pronto para configurar o monitor de conjunto de seus.

As várias configurações do monitor de conjunto de informações são divididas em três grupos: **informações básicas, configurações de monitor** e **configurações de aterramento**.

### <a name="basic-info"></a>Informações básicas

Esta tabela contém as configurações básicas usadas para o monitor de conjunto de conteúdo.

| Definição | Descrição | Sugestões | Mutável | 
| ------- | ----------- | ---- | ------- | 
| Nome | Nome do monitor de DataSet. | | Não |
| Conjunto de linha de base | Conjunto de tabelas que será usado como a linha de base para comparação do conjunto de origem de destino ao longo do tempo. | O conjunto de linha de base deve ter recursos em comum com o DataSet de destino. Em geral, a linha de base deve ser definida como um conjunto de linhas de treinamento do modelo ou uma fatia do conjunto de origem de destino. | Não |
| DataSet de destino | Conjunto de dados tabular com coluna timestamp especificada que será analisada para descompasso | O conjunto de dados de destino deve ter recursos em comum com o conjunto de dados de linha de base, e deve ser um conjunto de dados da série temporal para o qual são anexados novos. Os dados de histórico no DataSet de destino podem ser analisados ou novos dados podem ser monitorados. | Não | 
| Frequência | Essa é a frequência que será usada para agendar o trabalho de pipeline e analisar os dados históricos se estiver executando um aterramento. As opções incluem diário, semanal ou mensal. | Ajuste essa configuração para incluir um tamanho comparável de dados para a linha de base. | Não | 
| Funcionalidades | Lista de recursos que serão analisados para descompasso de dados ao longo do tempo | Definido como um ou mais recursos de saída do modelo para medir a descompasso de conceito. Não inclua recursos que naturalmente se descompassom ao longo do tempo (mês, ano, índice, etc.). Você pode aterrar e monitorar o descompasso de dados existente depois de ajustar a lista de recursos. | Sim | 
| Destino de computação | Azure Machine Learning o destino de computação para executar os trabalhos do monitor de conjunto de trabalho. | | Sim | 

### <a name="monitor-settings"></a>Configurações do monitor

Essas configurações são para o pipeline monitor do conjunto de DataSet agendado que será criado. 

| Definição | Descrição | Sugestões | Mutável | 
| ------- | ----------- | ---- | ------- |
| Ativar | Habilitar ou desabilitar a agenda no pipeline do monitor de conjunto de um | Desabilite-o para analisar dados históricos com a configuração de aterramento. Ele pode ser habilitado após a criação do monitor de conjunto de um. | Sim | 
| Latência | Tempo, em horas, leva para que os dados cheguem no DataSet. Por exemplo, se demorar três dias para que os dados cheguem no BD SQL que meu DataSet encapsula, defina a latência como 72. | Não pode ser alterado após a criação do monitor de conjunto de um | Não | 
| Endereços de email | Endereços de email para alertas com base na violação do limite de porcentagem de descompasso de dados. | Os emails são enviados por meio de Azure Monitor. | Sim | 
| Os | Limite de porcentagem de descompasso de dados para alerta de email. | Alertas e eventos adicionais podem ser definidos em muitas outras métricas no recurso de Application Insights associado do espaço de trabalho. | Sim | 

### <a name="backfill-settings"></a>Configurações de aterramento

Essas configurações são para executar um aterramento nos dados anteriores para métricas de descompasso de dados.

| Definição | Descrição | Sugestões |
| ------- | ----------- | ---- |
| Data de início | Data de início do trabalho de aterramento. | | 
| Data de término | Data de término do trabalho de aterramento. | Isso não pode ter mais de 31 * unidades de frequência de tempo a partir da data de início. Em um monitor de conjunto de dados existente, as métricas podem ser repreenchidas para analisar dados históricos ou substituir métricas por configurações atualizadas. |

## <a name="create-dataset-monitors"></a>Criar monitores de conjuntos de conjunto 

Crie monitores de conjunto de dados para detectar e alertar a descompasso de dado em um novo DataSet com o Azure Machine Learning Studio ou o SDK do Python. 

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning studio
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku-inline.md)]

Para configurar alertas em seu monitor de conjunto de negócios, o espaço de trabalho que contém o conjunto de um para o qual você deseja criar um monitor deve ter recursos de Enterprise Edition. 

Depois que a funcionalidade do espaço de trabalho for confirmada, navegue até a página inicial do estúdio e selecione a guia conjuntos de valores à esquerda. Selecione monitores de DataSet.

![Lista de monitores](media/how-to-monitor-datasets/monitor-list.png)

Clique no botão **+ criar monitor** e prossiga com o assistente clicando em **Avançar**.

![Para](media/how-to-monitor-datasets/wizard.png)

O monitor do conjunto de resultados resultante será exibido na lista. Selecione-o para ir para a página de detalhes do monitor.

### <a name="from-python-sdk"></a>Do SDK do Python

Consulte a [documentação de referência do SDK do Python sobre descompasso de dados](http://aka.ms/datadriftapi) para obter detalhes completos. 

Veja a seguir um exemplo de criação de um monitor de conjunto de um DataSet usando o SDK do Python

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
# setup data drift detector
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

Para obter um exemplo completo de como configurar um conjunto de dados de `timeseries` e um detector de descompasso, consulte nosso [bloco de anotações de exemplo](https://aka.ms/datadrift-notebook).

## <a name="understanding-data-drift-results"></a>Noções básicas sobre resultados de descompasso de dados

O monitor de dados produz dois grupos de resultados: visão geral da descompasso e detalhes do recurso. A animação a seguir mostra os gráficos de monitor de descompasso disponíveis com base no recurso e métrica selecionados. 

![Vídeo de demonstração](media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>Visão geral do descompasso

A seção **visão geral da descompasso** contém informações de nível superior sobre a magnitude da descompasso de dados e quais recursos devem ser investigados mais detalhadamente. 

| Métrica | Descrição | Sugestões | 
| ------ | ----------- | ---- | 
| Magnitude da descompasso de dados | Dado como uma porcentagem entre a linha de base e o conjunto de dados de destino ao longo do tempo. Variando de 0 a 100, em que 0 indica conjuntos de dados idênticos e 100 indica que a funcionalidade de descompasso de Azure Machine Learning pode contar totalmente com os dois conjuntos. | O ruído na porcentagem exata medida é esperado devido a técnicas de aprendizado de máquina que estão sendo usadas para gerar essa magnitude. | 
| Contribuição de descompasso por recurso | A contribuição de cada recurso no conjunto de recursos de destino para a magnitude de descompasso medida. |  Devido ao deslocamento covariado, a distribuição subjacente de um recurso não necessariamente precisa ser alterada para ter uma importância de recurso relativamente alta. | 

A imagem a seguir é um exemplo de gráficos vistos na **visão geral de descompasso** resulta no Azure Machine Learning Studio, resultando de um aterramento de [dados de superfície integrada NOAA](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/). Os dados foram amostrados para `stationName contains 'FLORIDA'`, com janeiro de 2019 sendo usados como o conjunto de dados de linha de base e todos os data 2019 usados como destino.
 
![Visão geral do descompasso](media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>Detalhes do recurso

A seção **detalhes do recurso** contém informações em nível de recurso sobre a alteração na distribuição do recurso selecionado, bem como outras estatísticas, ao longo do tempo. 

O conjunto de ponto de origem de destino também é criado com o passar do tempo. A distância estatística entre a distribuição de linha de base de cada recurso é comparada com o conjunto de dados de destino ao longo do tempo, o que é conceitualmente semelhante à magnitude da descompasso do dado, com a exceção de que se trata de um recurso individual. Mín., máx. e média também estão disponíveis. 

No Azure Machine Learning Studio, se você clicar em um ponto de dados no grafo, a distribuição do recurso que está sendo mostrado será ajustada de acordo. Por padrão, ele mostra a distribuição do conjunto de linhas de base e a distribuição da execução mais recente do mesmo recurso. 

Essas métricas também podem ser recuperadas no SDK do Python por meio do método `get_metrics()` em um objeto `DataDriftDetector`. 

#### <a name="numeric-features"></a>Recursos numéricos 

Os recursos numéricos são profiledos em cada execução do monitor de conjunto de conjuntos. Os itens a seguir são expostos no Azure Machine Learning Studio. A densidade de probabilidade é mostrada para a distribuição.

| Métrica | Descrição |  
| ------ | ----------- |  
| Distância Wasserstein | Quantidade mínima de trabalho para transformar a distribuição de linha de base na distribuição de destino. |
| Valor médio | Valor médio do recurso. |
| Valor mínimo | Valor mínimo do recurso. |
| Valor máximo | Valor máximo do recurso. |

![Detalhes do recurso numéricos](media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>Recursos categóricos 

Os recursos numéricos são profiledos em cada execução do monitor de conjunto de conjuntos. Os itens a seguir são expostos no Azure Machine Learning Studio. Um histograma é mostrado para a distribuição.

| Métrica | Descrição |  
| ------ | ----------- |  
| Distância euclidiana | Distância geométrica entre distribuições de linha de base e destino. |
| Valores exclusivos | Número de valores exclusivos (cardinalidade) do recurso. |


![Detalhes do recurso categóricos](media/how-to-monitor-datasets/feature-details2.png)

## <a name="troubleshooting"></a>Resolução de problemas

Limitações e problemas conhecidos:

* O intervalo de tempo de trabalhos de aterramento é limitado a 31 intervalos de configuração de frequência do monitor. 
* Limitação de 200 recursos, a menos que uma lista de recursos não seja especificada (todos os recursos usados).
* O tamanho da computação deve ser grande o suficiente para lidar com os dados. 

Colunas ou recursos, no conjunto de linhas, são classificados como categóricos ou numéricos com base nas condições na tabela abaixo. Se o recurso não atender a essas condições-por exemplo, uma coluna do tipo cadeia de caracteres com > 100 valores exclusivos-o recurso será descartado de nosso algoritmo de descompasso de dados, mas ainda terá o perfil criado. 

| Tipo de recurso | Data type | Condição | Limitações | 
| ------------ | --------- | --------- | ----------- |
| Categórico | Cadeia de caracteres, bool, int, float | O número de valores exclusivos no recurso é menor que 100 e menor que 5% do número de linhas. | NULL é tratado como sua própria categoria. | 
| Numéricos | int, float | De um tipo de dados numérico e não atende às condições de um recurso categórico. | Recurso Descartado se > 15% dos valores forem nulos. | 

## <a name="next-steps"></a>Passos seguintes

* Vá para o [Azure Machine Learning Studio](https://ml.azure.com) ou o [Notebook Python](https://aka.ms/datadrift-notebook) para configurar um monitor de conjunto de um DataSet.
* Consulte como configurar a descompasso de dados em [modelos implantados no serviço kubernetes do Azure](how-to-monitor-data-drift.md).
