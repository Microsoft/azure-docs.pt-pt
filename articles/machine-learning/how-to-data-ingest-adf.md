---
title: Ingestão de dados com o Azure Data Factory
titleSuffix: Azure Machine Learning
description: Conheça as opções disponíveis para a construção de um pipeline de ingestão de dados com a Azure Data Factory e os benefícios de cada um.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: b842934ea6bb458a59a53ea7068aa9ece98aacf1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98796151"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Ingestão de dados com o Azure Data Factory

Neste artigo, você aprende sobre as opções disponíveis para a construção de um pipeline de ingestão de dados com [a Azure Data Factory.](../data-factory/introduction.md) Este pipeline Azure Data Factory é utilizado para ingerir dados para utilização com [a Azure Machine Learning](overview-what-is-azure-ml.md). A Data Factory permite-lhe extrair, transformar e carregar facilmente dados (ETL). Uma vez que os dados foram transformados e carregados em armazenamento, pode ser usado para treinar os seus modelos de aprendizagem automática em Azure Machine Learning.

A transformação simples de dados pode ser tratada com atividades nativas da Data Factory e instrumentos como [o fluxo de dados.](../data-factory/control-flow-execute-data-flow-activity.md) Quando se trata de cenários mais complicados, os dados podem ser processados com algum código personalizado. Por exemplo, código Python ou R.

## <a name="compare-azure-data-factory-data-ingestion-pipelines"></a>Compare os oleodutos de ingestão de dados da Azure Data Factory 
Existem várias técnicas comuns de utilização da Data Factory para transformar dados durante a ingestão. Cada técnica tem vantagens e desvantagens que ajudam a determinar se é um bom ajuste para um caso de uso específico:

| Técnica | Vantagens | Desvantagens |
| ----- | ----- | ----- |
| Fábrica de Dados + Funções Azure | <li> Baixa latência, computação sem servidor<li>Funções imponentes<li>Funções reutilizáveis | Só é bom para o processamento de curto prazo |
| Data Factory + componente personalizado | <li>Computação paralela em larga escala<li>Adequado para algoritmos pesados | <li>Requer o código de embrulho num executável<li>Complexidade do tratamento das dependências e IO |
| Data Factory + Azure Databricks portátil |<li> Apache Spark<li>Ambiente python nativo |<li>Pode ser caro<li>Criar clusters inicialmente leva tempo e adiciona latência

## <a name="azure-data-factory-with-azure-functions"></a>Fábrica de Dados Azure com funções Azure

As Funções Azure permitem executar pequenas peças de código (funções) sem se preocupar com a infraestrutura de aplicações. Nesta opção, os dados são processados com código Python personalizado embrulhado numa Função Azure. 

A função é invocada com a [atividade Azure Data Factory Azure Function](../data-factory/control-flow-azure-function-activity.md). Esta abordagem é uma boa opção para transformações leves de dados. 

![O diagrama mostra um pipeline Azure Data Factory, com Azure Function e Run ML Pipeline, e um pipeline Azure Machine Learning, com o Train Model, e como interagem com dados brutos e dados preparados.](media/how-to-data-ingest-adf/adf-function.png)



* Vantagens:
    * Os dados são processados num cálculo sem servidor com uma latência relativamente baixa
    * O oleoduto da Data Factory pode invocar uma [Função Azure Durável](../azure-functions/durable/durable-functions-overview.md) que pode implementar um fluxo sofisticado de transformação de dados 
    * Os detalhes da transformação de dados são resumidos pela Função Azure que pode ser reutilizada e invocada de outros lugares
* Desvantagens:
    * As Funções Azure devem ser criadas antes da utilização com a ADF
    * As funções Azure só são boas para o processamento de dados de curto prazo

## <a name="azure-data-factory-with-custom-component-activity"></a>Fábrica de Dados Azure com atividade de componentes personalizados

Nesta opção, os dados são processados com código Python personalizado embrulhado num executável. É invocado com uma [atividade de Componente Personalizado Azure Data Factory.](../data-factory/transform-data-using-dotnet-custom-activity.md) Esta abordagem é mais adequada para dados grandes do que a técnica anterior.

![O diagrama mostra um pipeline Azure Data Factory, com um componente personalizado e um Pipeline Run M L, e um pipeline de Aprendizagem automática Azure, com o Train Model, e como interagem com dados brutos e dados preparados.](media/how-to-data-ingest-adf/adf-customcomponent.png)

* Vantagens:
    * Os dados são processados no [conjunto Azure Batch,](../batch/batch-technical-overview.md) que fornece computação paralela e de alto desempenho em larga escala
    * Pode ser usado para executar algoritmos pesados e processar quantidades significativas de dados
* Desvantagens:
    * A piscina Azure Batch deve ser criada antes de ser usada com a Data Factory
    * Sobre engenharia relacionada a envolver o código Python num executável. Complexidade das dependências de manuseamento e parâmetros de entrada/saída

## <a name="azure-data-factory-with-azure-databricks-python-notebook"></a>Fábrica de dados Azure com caderno Python Azure Databricks

[Azure Databricks](https://azure.microsoft.com/services/databricks/) é uma plataforma de análise baseada em Apache Spark na nuvem da Microsoft.

Nesta técnica, a transformação de dados é realizada por um [caderno Python,](../data-factory/transform-data-using-databricks-notebook.md)que funciona num cluster Azure Databricks. Esta é provavelmente a abordagem mais comum que aproveita todo o poder de um serviço Azure Databricks. É projetado para o processamento de dados distribuídos em escala.

![O diagrama mostra um oleoduto Azure Data Factory, com Azure Databricks Python e Run M L Pipeline, e um pipeline Azure Machine Learning, com o Train Model, e como interagem com dados brutos e dados preparados.](media/how-to-data-ingest-adf/adf-databricks.png)

* Vantagens:
    * Os dados são transformados no serviço Azure de processamento de dados mais poderoso, que é apoiado pelo ambiente Apache Spark
    * Apoio nativo da Python juntamente com quadros e bibliotecas de ciência de dados, incluindo TensorFlow, PyTorch e scikit-learn
    * Não é necessário envolver o código Python em funções ou módulos executáveis. O código funciona como está.
* Desvantagens:
    * A infraestrutura Azure Databricks deve ser criada antes de ser utilizada com a Data Factory
    * Pode ser caro dependendo da configuração do Azure Databricks
    * Girar os clusters computacional do modo "frio" leva algum tempo que traz alta latência à solução 
    

## <a name="consume-data-in-azure-machine-learning"></a>Consumir dados em Azure Machine Learning 

O gasoduto Data Factory guarda os dados preparados para o seu armazenamento na nuvem (como Azure Blob ou Azure Datalake). <br>
Consuma os seus dados preparados em Azure Machine Learning por, 

* Invocando um pipeline de aprendizagem automática Azure a partir do seu oleoduto data factory.<br>**OR**
* Criar uma [loja de dados Azure Machine Learning](how-to-access-data.md#create-and-register-datastores) e um conjunto de [dados de aprendizagem automática Azure](how-to-create-register-datasets.md) para utilização posterior.

### <a name="invoke-azure-machine-learning-pipeline-from-data-factory"></a>Invocar o pipeline de aprendizagem automática Azure da Fábrica de Dados

Este método é recomendado para [trabalhos de machine learning operations (MLOps).](concept-model-management-and-deployment.md#what-is-mlops) Se não quiser configurar um pipeline de Aprendizagem automática Azure, consulte [os dados da Leitura diretamente do armazenamento](#read-data-directly-from-storage).

Cada vez que o gasoduto da Fábrica de Dados funciona, 

1. Os dados são guardados para um local diferente no armazenamento. 
1. Para passar a localização para Azure Machine Learning, o gasoduto Data Factory chama um [pipeline de Aprendizagem automática Azure.](concept-ml-pipelines.md) Ao ligar para o gasoduto ML, a localização dos dados e o ID de execução são enviados como parâmetros. 
1. O gasoduto ML pode então criar uma loja de dados Escoditura machine learning azure com a localização dos dados. Saiba mais em [executar gasodutos de aprendizagem automática Azure na Data Factory](../data-factory/transform-data-machine-learning-service.md).

![O diagrama mostra um oleoduto Azure Data Factory e um oleoduto Azure Machine Learning e como interagem com dados brutos e dados preparados. O oleoduto Data Factory fornece dados à base de dados de dados preparados, que alimenta uma loja de dados, que alimenta conjuntos de dados no espaço de trabalho machine learning.](media/how-to-data-ingest-adf/aml-dataset.png)

> [!TIP]
> A [versão de suporte](./how-to-version-track-datasets.md)de conjuntos de dados , para que o gasoduto ML possa registar uma nova versão do conjunto de dados que aponta para os dados mais recentes do pipeline ADF.

Uma vez que os dados são acessíveis através de uma datastore ou conjunto de dados, pode usá-lo para treinar um modelo ML. O processo de formação pode fazer parte do mesmo oleoduto ML que é chamado da ADF. Ou pode ser um processo separado, como a experimentação num caderno jupyter.

Uma vez que os conjuntos de dados suportam a versão, e cada corrida a partir do pipeline cria uma nova versão, é fácil entender que versão dos dados foi usada para treinar um modelo.

### <a name="read-data-directly-from-storage"></a>Ler dados diretamente do armazenamento

Se não quiser criar um pipeline ML, pode aceder aos dados diretamente a partir da conta de armazenamento onde os seus dados preparados são guardados com uma loja de dados e dataset Azure Machine Learning. 

O seguinte código Python demonstra como criar uma loja de dados que se conecta ao armazenamento Azure DataLake Generation 2. [Saiba mais sobre datastores e onde encontrar permissões principais do serviço.](how-to-access-data.md#create-and-register-datastores)

```python
ws = Workspace.from_config()
adlsgen2_datastore_name = '<ADLS gen2 storage account alias>'  #set ADLS Gen2 storage account alias in AML

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<ADLS account subscription ID>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<ADLS account resource group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<ADLS account name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<tenant id of service principal>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<client id of service principal>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<secret of service principal>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(
    workspace=ws,
    datastore_name=adlsgen2_datastore_name,
    account_name=account_name, # ADLS Gen2 account name
    filesystem='<filesystem name>', # ADLS Gen2 filesystem
    tenant_id=tenant_id, # tenant id of service principal
    client_id=client_id, # client id of service principal
```

Em seguida, crie um conjunto de dados para fazer referência aos ficheiros que pretende utilizar na sua tarefa de aprendizagem automática. 

O seguinte código cria um SeparadorSet a partir de um ficheiro CSV, `prepared-data.csv` . Saiba mais sobre [tipos de conjunto de dados e formatos de ficheiros aceites.](how-to-create-register-datasets.md#dataset-types) 

```python
from azureml.core import Workspace, Datastore, Dataset
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig

# retrieve data via AML datastore
datastore = Datastore.get(ws, adlsgen2_datastore)
datastore_path = [(datastore, '/data/prepared-data.csv')]
        
prepared_dataset = Dataset.Tabular.from_delimited_files(path=datastore_path)
```

A partir daqui, use `prepared_dataset` para fazer referência aos seus dados preparados, como nos seus scripts de treino. Saiba como [treinar modelos com conjuntos de dados em Azure Machine Learning](./how-to-train-with-datasets.md).

## <a name="next-steps"></a>Passos seguintes

* [Executar um caderno Databricks na Azure Data Factory](../data-factory/transform-data-using-databricks-notebook.md)
* [Dados de acesso nos serviços de armazenamento Azure](./how-to-access-data.md#create-and-register-datastores)
* [Treinar modelos com conjuntos de dados em Azure Machine Learning](./how-to-train-with-datasets.md).
* [DevOps para um pipeline de ingestão de dados](./how-to-cicd-data-ingestion.md)