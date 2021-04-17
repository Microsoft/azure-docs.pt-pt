---
title: Use o Apache Spark num pipeline de aprendizagem automática (pré-visualização)
titleSuffix: Azure Machine Learning
description: Ligue o seu espaço de trabalho Azure Synapse Analytics ao seu pipeline de aprendizagem de máquinas Azure para usar o Apache Spark para manipulação de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, synapse-azureml
ms.openlocfilehash: 2c69ec853cdeeed6f9e28fb9f2884053580ce08e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576385"
---
# <a name="how-to-use-apache-spark-powered-by-azure-synapse-analytics-in-your-machine-learning-pipeline-preview"></a>Como utilizar o Apache Spark (alimentado pela Azure Synapse Analytics) no seu pipeline de aprendizagem automática (pré-visualização)

Neste artigo, você aprenderá a usar piscinas Apache Spark alimentadas pela Azure Synapse Analytics como o alvo do cálculo para um passo de preparação de dados em um oleoduto de Aprendizagem automática Azure. Você vai aprender como um único oleoduto pode usar recursos computacional adequados para o passo específico, como preparação de dados ou treino. Verá como os dados são preparados para o passo da Faísca e como é passado para o próximo passo. 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Crie um espaço de [trabalho para aprendizagem automática Azure](how-to-manage-workspace.md) para manter todos os seus recursos de pipeline.

* [Configure o seu ambiente de desenvolvimento](how-to-configure-environment.md) para instalar o Azure Machine Learning SDK, ou use uma instância de cálculo [Azure Machine Learning](concept-compute-instance.md) com o SDK já instalado.

* Crie um espaço de trabalho Azure Synapse Analytics e piscina Apache Spark (ver [Quickstart: Criar uma piscina Apache Spark sem servidor utilizando o Synapse Studio).](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md) 

## <a name="link-your-azure-machine-learning-workspace-and-azure-synapse-analytics-workspace"></a>Ligue o seu espaço de trabalho de aprendizagem de máquinas Azure e o espaço de trabalho Azure Synapse Analytics 

Você cria e administra as suas piscinas Apache Spark num espaço de trabalho Azure Synapse Analytics. Para integrar uma piscina Apache Spark com um espaço de trabalho Azure Machine Learning, você deve [ligar-se ao espaço de trabalho Azure Synapse Analytics](how-to-link-synapse-ml-workspaces.md). 

Você pode anexar uma piscina Apache Spark via Azure Machine Learning studio UI usando a página **Linked Services.** Pode também fazê-lo através da página **Compute** com a opção **de computação Attach.**

Também pode anexar uma piscina Apache Spark via SDK (conforme elaborado abaixo) ou através de um modelo ARM (ver [este modelo Exemplo ARM).](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json) 

Pode utilizar a linha de comando para seguir o modelo ARM, adicionar o serviço ligado e anexar a piscina Apache Spark com o seguinte código:

```bash
az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
```

> [!Important]
> Para ligar ao espaço de trabalho Azure Synapse Analytics com sucesso, você deve ter o papel de Proprietário no recurso de espaço de trabalho Azure Synapse Analytics. Verifique o seu acesso no portal Azure.
> O serviço ligado receberá uma Identidade Atribuída ao Sistema (SAI) quando a criar. Deve atribuir a este serviço de ligação SAI o papel de "Administrador De Faíscas De Sinapse Apache" do Synapse Studio para que possa submeter o trabalho spark (ver Como gerir atribuições de [funções de Synapse RBAC no Synapse Studio).](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md) Deve também dar ao utilizador do espaço de trabalho Azure Machine Learning a função "Contribuinte" do portal Azure de sarna de recursos.

## <a name="create-or-retrieve-the-link-between-your-azure-synapse-analytics-workspace-and-your-azure-machine-learning-workspace"></a>Crie ou recupere a ligação entre o seu espaço de trabalho Azure Synapse Analytics e o seu espaço de trabalho Azure Machine Learning

Pode recuperar serviços ligados no seu espaço de trabalho com códigos como:

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

Em primeiro lugar, `Workspace.from_config()` acede ao seu espaço de trabalho Azure Machine Learning utilizando a configuração em `config.json` (ver [Tutorial: Começar com Azure Machine Learning no seu ambiente de desenvolvimento).](tutorial-1st-experiment-sdk-setup-local.md) Em seguida, o código imprime todos os serviços ligados disponíveis no Espaço de Trabalho. Finalmente, `LinkedService.get()` recupera um serviço ligado chamado `'synapselink1'` . 

## <a name="attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>Anexe a sua piscina de faíscas Apache como alvo de computação para a Azure Machine Learning

Para utilizar a sua piscina de faíscas Apache para alimentar um passo no seu oleoduto de aprendizagem automática, deve anexá-lo como um `ComputeTarget` passo de pipeline, como mostra o seguinte código.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(
        linked_service = linked_service,
        type="SynapseSpark",
        pool_name="spark01") # This name comes from your Synapse workspace

synapse_compute=ComputeTarget.attach(
        workspace=ws,
        name='link1-spark01',
        attach_configuration=attach_config)

synapse_compute.wait_for_completion()
```

O primeiro passo é configurar o `SynapseCompute` . O `linked_service` argumento é o objeto que criou ou `LinkedService` recuperou no passo anterior. O `type` argumento deve `SynapseSpark` ser. O `pool_name` argumento deve coincidir com o de uma piscina existente no seu espaço de trabalho `SynapseCompute.attach_configuration()` Azure Synapse Analytics. Para obter mais informações sobre a criação de uma piscina de faíscas Apache no espaço de trabalho Azure Synapse Analytics, consulte [Quickstart: Criar uma piscina Apache Spark sem servidor utilizando o Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md). O tipo de `attach_config` `ComputeTargetAttachConfiguration` é.

Uma vez criada a configuração, cria-se uma aprendizagem automática `ComputeTarget` passando no , e o nome pelo qual gostaria de se referir ao cálculo dentro do espaço de trabalho de machine `Workspace` `ComputeTargetAttachConfiguration` learning. A chamada `ComputeTarget.attach()` é assíncronea, por isso a amostra bloqueia até que a chamada termine.

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>Criar um `SynapseSparkStep` que use a piscina Apache Spark ligada

O trabalho de amostra de [prossódutão Spark na piscina de faíscas Apache](https://github.com/azure/machinelearningnotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb) define um simples oleoduto de aprendizagem automática. Em primeiro lugar, o caderno define um passo de preparação de dados alimentado pelo `synapse_compute` definido no passo anterior. Em seguida, o caderno define um passo de treino alimentado por um alvo de computação mais adequado para o treino. O caderno de amostras utiliza a base de dados de sobrevivência do Titanic para demonstrar a entrada e saída de dados; na verdade, não limpa os dados ou faz um modelo preditivo. Como não há formação real nesta amostra, o passo de treino usa um recurso computacional barato baseado em CPU.

Os dados fluem para um pipeline de aprendizagem automática através de `DatasetConsumptionConfig` objetos, que podem conter dados tabulares ou conjuntos de ficheiros. Os dados vêm frequentemente de ficheiros no armazenamento de bolhas na loja de dados de um espaço de trabalho. O código que se segue mostra algum código típico para criar entrada para um pipeline de aprendizagem automática:

```python
from azureml.core import Dataset

datastore = ws.get_default_datastore()
file_name = 'Titanic.csv'

titanic_tabular_dataset = Dataset.Tabular.from_delimited_files(path=[(datastore, file_name)])
step1_input1 = titanic_tabular_dataset.as_named_input("tabular_input")

# Example only: it wouldn't make sense to duplicate input data, especially one as tabular and the other as files
titanic_file_dataset = Dataset.File.from_files(path=[(datastore, file_name)])
step1_input2 = titanic_file_dataset.as_named_input("file_input").as_hdfs()
```

O código acima pressupõe que o ficheiro `Titanic.csv` está no armazenamento de bolhas. O código mostra como ler o ficheiro como `TabularDataset` um e como um `FileDataset` . Este código destina-se apenas a fins de demonstração, pois seria confuso duplicar as entradas ou interpretar uma única fonte de dados como um recurso contendo tabela e apenas como um ficheiro.

> [!Important]
> Para utilizar uma `FileDataset` entrada como entrada, a sua versão deve `azureml-core` ser pelo menos `1.20.0` . Como especificar isto usando a `Environment` classe é discutido abaixo.

Quando um passo estiver concluído, poderá optar por armazenar dados de saída utilizando código semelhante a:

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

Neste caso, os dados seriam armazenados `datastore` num ficheiro chamado e estariam `test` disponíveis dentro do espaço de trabalho de machine learning como um `Dataset` nome `registered_dataset` .

Além dos dados, um passo de gasoduto pode ter dependências python por passo. Os objetos individuais `SynapseSparkStep` também podem especificar a sua configuração exata de Azure Synapse Apache Spark. Isto é indicado no seguinte código, que especifica que a `azureml-core` versão do pacote deve ser pelo menos `1.20.0` . (Como mencionado anteriormente, este requisito `azureml-core` é necessário para usar um como `FileDataset` entrada.)

```python
from azureml.core.environment import Environment
from azureml.pipeline.steps import SynapseSparkStep

env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core>=1.20.0")

step_1 = SynapseSparkStep(name = 'synapse-spark',
                          file = 'dataprep.py',
                          source_directory="./code", 
                          inputs=[step1_input1, step1_input2],
                          outputs=[step1_output],
                          arguments = ["--tabular_input", step1_input1, 
                                       "--file_input", step1_input2,
                                       "--output_dir", step1_output],
                          compute_target = 'link1-spark01',
                          driver_memory = "7g",
                          driver_cores = 4,
                          executor_memory = "7g",
                          executor_cores = 2,
                          num_executors = 1,
                          environment = env)
```

O código acima especifica um único passo no gasoduto de aprendizagem da máquina Azure. Este passo `environment` especifica uma versão específica `azureml-core` e pode adicionar outras dependências de conda ou pip, se necessário.

O `SynapseSparkStep` will zip e upload do computador local o subdirecional `./code` . Esse diretório será recriado no servidor computacional e o passo irá executar o ficheiro a `dataprep.py` partir desse diretório. O `inputs` e desse passo são os , e `outputs` `step1_input1` `step1_input2` `step1_output` objetos previamente discutidos. A forma mais fácil de aceder a esses valores dentro do `dataprep.py` script é associá-los ao nome `arguments` .

O próximo conjunto de argumentos para o `SynapseSparkStep` construtor controla Apache Spark. O `compute_target` é o que `'link1-spark01'` anexamos como alvo de computação anteriormente. Os outros parâmetros especificam a memória e os núcleos que gostaríamos de usar.

O caderno de amostras utiliza o seguinte código `dataprep.py` para:

```python
import os
import sys
import azureml.core
from pyspark.sql import SparkSession
from azureml.core import Run, Dataset

print(azureml.core.VERSION)
print(os.environ)

import argparse
parser = argparse.ArgumentParser()
parser.add_argument("--tabular_input")
parser.add_argument("--file_input")
parser.add_argument("--output_dir")
args = parser.parse_args()

# use dataset sdk to read tabular dataset
run_context = Run.get_context()
dataset = Dataset.get_by_id(run_context.experiment.workspace,id=args.tabular_input)
sdf = dataset.to_spark_dataframe()
sdf.show()

# use hdfs path to read file dataset
spark= SparkSession.builder.getOrCreate()
sdf = spark.read.option("header", "true").csv(args.file_input)
sdf.show()

sdf.coalesce(1).write\
.option("header", "true")\
.mode("append")\
.csv(args.output_dir)
```

Este script de "preparação de dados" não faz nenhuma transformação real de dados, mas ilustra como recuperar dados, convertê-los num dataframe de faísca, e como fazer alguma manipulação básica do Apache Spark. Pode encontrar a saída no Azure Machine Learning Studio abrindo a corrida para crianças, escolhendo o separador **Saídas + registos** e abrindo o `logs/azureml/driver/stdout` ficheiro, como mostra a seguinte figura.

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="Screenshot do Estúdio mostrando o separador de stdout da corrida infantil":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>Use o `SynapseSparkStep` num oleoduto

O exemplo a seguir utiliza a saída da `SynapseSparkStep` criada na secção [anterior](#create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool). Outros passos no oleoduto podem ter os seus próprios ambientes únicos e funcionar em diferentes recursos de computação adequados à tarefa em questão. O caderno de amostras executa o "passo de treino" num pequeno aglomerado de CPU:

```python
from azureml.core.compute import AmlCompute

cpu_cluster_name = "cpucluster"

if cpu_cluster_name in ws.compute_targets:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
else:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', max_nodes=1)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    print('Allocating new CPU compute cluster')

cpu_cluster.wait_for_completion(show_output=True)

step2_input = step1_output.as_input("step2_input").as_download()

step_2 = PythonScriptStep(script_name="train.py",
                          arguments=[step2_input],
                          inputs=[step2_input],
                          compute_target=cpu_cluster_name,
                          source_directory="./code",
                          allow_reuse=False)
```

O código acima cria o novo recurso de computação, se necessário. Em seguida, o `step1_output` resultado é convertido para entrada para o passo de treino. A `as_download()` opção significa que os dados serão transferidos para o recurso compute, resultando num acesso mais rápido. Se os dados fossem tão grandes que não encaixassem no disco rígido computacional local, utilizaria a `as_mount()` opção de transmitir os dados através do sistema de ficheiros FUSE. O `compute_target` segundo passo é , não o recurso que `'cpucluster'` `'link1-spark01'` usou na etapa de preparação de dados. Este passo utiliza um programa simples `train.py` em vez do utilizado no passo `dataprep.py` anterior. Pode ver os detalhes do `train.py` caderno de amostras.

Uma vez definidos todos os seus passos, pode criar e executar o seu oleoduto. 

```python
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

O código acima cria um gasoduto que consiste no passo de preparação de dados nas piscinas Apache Spark alimentadas pela Azure Synapse Analytics ( `step_1` ) e o passo de treino ( `step_2` ). O Azure calcula o gráfico de execução examinando as dependências de dados entre os passos. Neste caso, há apenas uma dependência simples que `step2_input` necessariamente `step1_output` requer.

A chamada para `pipeline.submit` criar, se necessário, uma Experiência chamada `synapse-pipeline` e assíncronea começa uma Corrida dentro dela. Os passos individuais dentro do oleoduto são executados como Child Runs desta corrida principal e podem ser monitorizados e revistos na página experimentos do Estúdio.

## <a name="next-steps"></a>Passos seguintes

* [Publicar e rastrear gasodutos de aprendizagem de máquinas](how-to-deploy-pipelines.md) 
* [Monitorizar o Azure Machine Learning](monitor-azure-machine-learning.md)
* [Utilize ML automatizado num oleoduto de aprendizagem de máquinas Azure em Python](how-to-use-automlstep-in-pipelines.md)
