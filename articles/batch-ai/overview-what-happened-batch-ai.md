---
title: O que está a acontecer ao Azure Batch AI? | Microsoft Docs
description: Saiba mais sobre o que está a acontecer ao Azure Batch AI e a opção de computação de serviço do Azure Machine Learning.
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 2/14/2019
ms.author: garye
ms.openlocfilehash: fb1114e94c227ce6787532c6059186399d0f57f0
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961365"
---
# <a name="whats-happening-to-azure-batch-ai"></a>O que está a acontecer ao Azure Batch AI?

**O serviço Azure Batch AI está desativando em Março.** A preparação de à escala e classificação de recursos do Batch AI estão agora disponíveis na [serviço Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md), que foi disponibilizado no dia 4 de Dezembro de 2018.

Juntamente com muitas outras capacidades de machine learning, o serviço Azure Machine Learning inclui um destino de computação gerida baseado na nuvem para treinamento, implementação e modelos de aprendizagem automática de classificação. Este destino de computação é chamado [computação do Azure Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Começar a migrar e usá-lo hoje](#migrate). Pode interagir com o serviço Azure Machine Learning através da respetiva [SDKs de Python](../machine-learning/service/quickstart-create-workspace-with-python.md), interface de linha de comandos e o [portal do Azure](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Linha cronológica de suporte

| Date | Detalhes de suporte do serviço de IA do batch |
| ---- |-----------------|
| Dezembro&nbsp;14&#x2c;&nbsp;2018| Pode usar as suas subscrições do Azure Batch AI existentes, como antes. No entanto, não existem **novas subscrições** são possíveis e não existem novos investimentos que estão a ser efetuados.|
| Março&nbsp;31&#x2c;&nbsp;2019 | Após esta data, as assinaturas existentes do Batch AI deixará de funcionar. |

## <a name="how-does-azure-machine-learning-service-compare"></a>Como o que compara o serviço Azure Machine Learning?
É um serviço em nuvem que utiliza para preparar, implementar, automatizar e gerir modelos de aprendizagem automática, tudo na escala amplo que a cloud oferece. Obter uma compreensão geral do [serviço do Azure Machine Learning nesta visão geral](../machine-learning/service/overview-what-is-azure-ml.md).
 

Um ciclo de vida de desenvolvimento do modelo comum envolve a preparação de dados, treinamento e experimentação e uma fase de implementação. Este ciclo de ponto a ponto pode ser organizado, utilizando pipelines de Machine Learning.

![Diagrama de fluxo](./media/overview-what-happened-batch-ai/lifecycle.png)


[Saiba mais sobre como funciona o serviço e seus conceitos principais](../machine-learning/service/concept-azure-machine-learning-architecture.md). Muitos dos conceitos do fluxo de trabalho de treinamento de modelo são semelhantes aos conceitos existentes do Batch AI. 

Especificamente, este é um mapeamento de que forma deverá encará-los:
 
|Serviço batch AI|  Serviço Azure Machine Learning|
|:--:|:---:|
|Área de trabalho|Área de trabalho|
|Cluster|   Computação do tipo `AmlCompute`|
|Servidores de ficheiros|DataStores|
|Experimentações|Experimentações|
|Tarefas|É executada (permite execuções aninhadas)|
 
Esta é outra vista da mesma tabela que irá ajudá-lo a visualizar ainda mais as coisas:
 
**Hierarquia de IA do batch**
![diagrama de fluxo](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
**Hierarquia de serviço do Azure Machine Learning**
![diagrama de fluxo](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

### <a name="platform-capabilities"></a>Capacidades da plataforma
O Azure Machine Learning, serviço traz um ótimo conjunto de novas funcionalidades, incluindo um treinamento de ponta a ponta -> a pilha de implementação que pode utilizar para o desenvolvimento de IA sem ter de gerir todos os recursos do Azure. Esta tabela compara o suporte de funcionalidades para formação entre os dois serviços.

|Funcionalidade|Serviço de BatchAI|Serviço Azure Machine Learning|
|-------|:-------:|:-------:|
|Opção de tamanho VM |CPU/GPU    |CPU/GPU. Também suporta FPGA para inferência|
|Cluster de pronto para IA (Drivers, Docker, etc.)|  Sim |Sim|
|Preparação do nó| Sim|    Não|
|Escolha de família de SO   |Parcial    |Não|
|Dedicado e LowPriority VMs  |Sim    |Sim|
|Dimensionamento Automático   |Sim    |Sim (por predefinição)|
|Tempo para o dimensionamento automático de espera  |Não |Sim|
|SSH    |Sim|   Sim|
|Montagem de nível de cluster |Sim (existem FileShares, Blobs, NFS, personalizado)   |Sim (montar ou Baixe o arquivo de dados)|
|Formação distribuída|  Sim |Sim|
|Modo de execução de tarefa|    VM ou de contentor|    Contentor|
|Imagem de contentor personalizado|    Sim |Sim|
|Qualquer Toolkit    |Sim    |Sim (Script de Python de execução)|
|JobPreparation|    Sim |Ainda não|
|Montagem de nível de tarefa |Sim (existem FileShares, Blobs, NFS, personalizado)   |Sim (existem FileShares, Blobs)|
|Monitorização de tarefas     |via GetJob|    através do histórico de execuções de mensagens em fila (informações mais detalhadas, tempo de execução personalizado para enviar por push mais métricas)|
|Recuperar ficheiros/modelos e os registos da tarefa |   por meio ListFiles e APIs de armazenamento  |por meio do serviço de artefacto|
 |Suporte para Tensorboard   |Não|    Sim|
|Quotas de nível de famílias de VM |Sim    |Sim (com a capacidade do anterior transportada)|
 
Para além da tabela anterior, existem funcionalidades no serviço do Azure Machine Learning que tradicionalmente não eram suportadas no BatchAI.

|Funcionalidade|Serviço de BatchAI|Serviço Azure Machine Learning|
|-------|:-------:|:-------:|
|Preparação do ambiente    |Não |Sim (Conda preparar e carregar para o ACR)|
|A otimização de hiper-parâmetros  |Não|    Sim|
|Gestão de modelos   |Não |Sim|
|Implementação/operacionalização| Não  |Através do AKS e do ACI|
|Preparação de dados   |Não |Sim|
|Destinos de computação    |VMs do Azure  |Local, BatchAI (como AmlCompute), o DataBricks, o HDInsight|
|Aprendizagem automática |Não|    Sim|
|Pipelines  |Não |Sim|
|Classificação de lote  |Sim    |Sim|
|Suporte do portal/CLI|    Sim |Sim|


### <a name="programming-interfaces"></a>Interfaces de programação

Esta tabela apresenta as várias interfaces de programação disponíveis para cada serviço.
    
|Funcionalidade|Serviço de BatchAI|Serviço Azure Machine Learning|
|-------|:-------:|:-------:|
|SDK    |Java, C#, Python, Nodejs   |Python (com base de configuração de execução e estimator com base para arquiteturas comuns)|
|CLI    |Sim    |Ainda não|
|Portal do Azure   |Sim    |Sim (exceto a submissão da tarefa)|
|API REST   |Sim    |Sim, mas distribuída em microsserviços|




## <a name="why-migrate"></a>Por que migrar?

Atualização do serviço BatchAI de pré-visualização para o serviço GA'ed do Azure Machine Learning dá-lhe uma experiência melhor os conceitos que são mais fáceis de usar como avaliadores e arquivos de dados. Esta ação garante também GA SLAs de nível de serviço do Azure e o suporte ao cliente.

O serviço do Azure Machine Learning também traz novas funcionalidades, como ML automatizada, a otimização de hiper-parâmetros e Pipelines de ML, que são úteis para cargas de trabalho de IA mais em grande escala. A capacidade de operacionalizar um modelo preparado sem mudar para um serviço separado ajuda a concluir o loop de ciência de dados de preparação de dados (com o SDK de preparação de dados) até à monitorização do modelo e de operacionalização.

<a name="migrate"></a>

## <a name="how-do-i-migrate"></a>Como posso migrar?

Antes de seguir os passos neste guia de migração que ajudam a comandos de mapa entre os dois serviços, recomendamos que passe algum tempo se familiarizar com o serviço Azure Machine Learning através da respetiva [documentação](../machine-learning/service/overview-what-is-azure-ml.md) incluindo o [tutorial em Python](../machine-learning/service/tutorial-train-models-with-aml.md).

Para evitar interrupções às suas aplicações e beneficiar das funcionalidades mais recentes, siga os passos seguintes antes de 31 de Março de 2019:

1. Criar uma área de trabalho do serviço do Azure Machine Learning e começar a utilizar:
    + [Guia de introdução do Python com base em](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Portal com base início rápido do Azure](../machine-learning/service/quickstart-get-started.md)

1. Configurar uma [computação do Azure Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute) para preparação de modelos.

1. Atualize os scripts para usar a computação do Azure Machine Learning.


### <a name="sdk-migration"></a>Migração de SDK

SDK do atual suporte no serviço é por meio de vários SDKs de Python do Azure Machine Learning. O SDK principal é aproximadamente atualizado a cada duas semanas e pode ser instalado a partir PyPi com o seguinte comando:

```python
pip install --upgrade azureml-sdk[notebooks]
```

Configurar o ambiente e instalar o SDK usá-los [instruções de início rápido](../machine-learning/service/quickstart-create-workspace-with-python.md#install-the-sdk)

Depois de abrir um bloco de notas do jupyter com o kernel apontando para o ambiente de conda relevantes, eis como o mapeamento os comandos nos dois serviços:


#### <a name="create-a-workspace"></a>Criar uma área de trabalho
O conceito de inicializar uma área de trabalho usando um configuration.json no BatchAI mapeia da mesma forma para utilizar um ficheiro de configuração no Azure ML.

Para **Batch AI**, conseguiu desta forma:

```python
sys.path.append('../../..')
import utilities as utils

cfg = utils.config.Configuration('../../configuration.json')
client = utils.config.create_batchai_client(cfg)

utils.config.create_resource_group(cfg)
_ = client.workspaces.create(cfg.resource_group, cfg.workspace, cfg.location).result()
```

**O serviço do Azure Machine Learning**, tente:

```python
from azureml.core.workspace import Workspace

ws = Workspace.from_config()
print('Workspace name: ' + ws.name, 
      'Azure region: ' + ws.location, 
      'Subscription id: ' + ws.subscription_id, 
      'Resource group: ' + ws.resource_group, sep = '\n')
```

Além disso, também pode criar uma área de trabalho diretamente ao especificar os parâmetros de configuração, como

```python
from azureml.core import Workspace
# Create the workspace using the specified parameters
ws = Workspace.create(name = workspace_name,
                      subscription_id = subscription_id,
                      resource_group = resource_group, 
                      location = workspace_region,
                      create_resource_group = True,
                      exist_ok = True)
ws.get_details()

# write the details of the workspace to a configuration file to the notebook library
ws.write_config()
```

Saiba mais sobre a classe de área de trabalho de AML no [documentação de referência do SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py).


#### <a name="create-a-compute-cluster"></a>Criar um cluster de computação
O Azure Machine Learning suporta vários destinos de computação, alguns dos quais são geridos pelo serviço e outras pessoas que podem ser anexados a sua área de trabalho (por exemplo. Um cluster do HDInsight ou VM remota. Saiba mais sobre várias [destinos de computação](../machine-learning/service/how-to-set-up-training-targets.md). O conceito de criação de um BatchAI cluster é mapeado para a criação de um cluster de AmlCompute no Azure ML de computação. A criação de Amlcompute demora numa configuração de computação semelhante a como passar parâmetros no BatchAI. Uma coisa a observar é que o dimensionamento automático está ativado por predefinição no seu cluster AmlCompute enquanto ele é desativado por predefinição no BatchAI.

Para **Batch AI**, conseguiu desta forma:

```python
nodes_count = 2
cluster_name = 'nc6'

parameters = models.ClusterCreateParameters(
    vm_size='STANDARD_NC6',
    scale_settings=models.ScaleSettings(
        manual=models.ManualScaleSettings(target_node_count=nodes_count)
    ),
    user_account_settings=models.UserAccountSettings(
        admin_user_name=cfg.admin,
        admin_user_password=cfg.admin_password or None,
        admin_user_ssh_public_key=cfg.admin_ssh_key or None,
    )
)
_ = client.clusters.create(cfg.resource_group, cfg.workspace, cluster_name, parameters).result()
```

Para **serviço Azure Machine Learning**, tente:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
gpu_cluster_name = "nc6"

# Verify that cluster does not exist already
try:
    gpu_cluster = ComputeTarget(workspace=ws, name=gpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           vm_priority='lowpriority',
                                                           min_nodes=1,
                                                           max_nodes=2,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    gpu_cluster = ComputeTarget.create(ws, gpu_cluster_name, compute_config)

gpu_cluster.wait_for_completion(show_output=True)
```

Saiba mais sobre a classe AMLCompute a [documentação de referência do SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py). Tenha em atenção que, na configuração acima, apenas vm_size e max_nodes são obrigatórios, e o resto das propriedades como VNets destinam-se apenas a configuração do cluster avançadas.


#### <a name="monitoring-status-of-your-cluster"></a>Monitorização do Estado do cluster
Isso é mais simples no Azure ML como verá abaixo.

Para **Batch AI**, conseguiu desta forma:

```python
cluster = client.clusters.get(cfg.resource_group, cfg.workspace, cluster_name)
utils.cluster.print_cluster_status(cluster)
```

Para **serviço Azure Machine Learning**, tente:

```python
gpu_cluster.get_status().serialize()
```

#### <a name="getting-reference-to-a-storage-account"></a>Obter a referência a uma conta de armazenamento
O conceito de um armazenamento de dados, como o blob, obtém simplificada no Azure ML usando o objeto de arquivo de dados. Por predefinição, a área de trabalho do Azure ML cria uma conta de armazenamento, mas pode anexar seu próprio armazenamento também como parte da criação da área de trabalho. 

Para **Batch AI**, conseguiu desta forma:

```python
azure_blob_container_name = 'batchaisample'
blob_service = BlockBlobService(cfg.storage_account_name, cfg.storage_account_key)
blob_service.create_container(azure_blob_container_name, fail_on_exist=False)
```

Para **serviço Azure Machine Learning**, tente:

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)
```

Saiba mais sobre o registo de contas de armazenamento adicional ou obter uma referência a outro arquivo de dados registado no [documentação de serviço do Azure ML](../machine-learning/service/how-to-access-data.md#access).


#### <a name="downloading-and-uploading-data"></a>Baixando e carregando dados 
Com um dos serviços, pode carregar os dados para a conta de armazenamento facilmente usando a referência do arquivo de dados acima. Para BatchAI, podemos também implementar o script de treinamento como parte da partilha de ficheiros, embora verá como pode especificá-la como parte da sua configuração de tarefa no caso do Azure ML.

Para **Batch AI**, conseguiu desta forma:

```python
mnist_dataset_directory = 'mnist_dataset'
utils.dataset.download_and_upload_mnist_dataset_to_blob(
    blob_service, azure_blob_container_name, mnist_dataset_directory)

script_directory = 'tensorflow_samples'
script_to_deploy = 'mnist_replica.py'

blob_service.create_blob_from_path(azure_blob_container_name,
                                   script_directory + '/' + script_to_deploy, 
                                   script_to_deploy)
```


Para **serviço Azure Machine Learning**, tente:

```python
import os
import urllib
os.makedirs('./data', exist_ok=True)
download_url = 'https://s3.amazonaws.com/img-datasets/mnist.npz'
urllib.request.urlretrieve(download_url, filename='data/mnist.npz')

ds.upload(src_dir='data', target_path='mnist_dataset', overwrite=True, show_progress=True)

path_on_datastore = ' mnist_dataset/mnist.npz' ds_data = ds.path(path_on_datastore) print(ds_data)
```

#### <a name="create-an-experiment"></a>Criar uma experimentação
Conforme mencionado acima do Azure ML tem um conceito de uma experiência semelhante à BatchAI. Cada experimentação, em seguida, pode ter execuções individuais, semelhantes à forma precisamos tarefas no BatchAI. O Azure ML também permite que tem a hierarquia em cada principal a executar, para execuções de subordinados individuais.

Para **Batch AI**, conseguiu desta forma:

```python
experiment_name = 'tensorflow_experiment'
experiment = client.experiments.create(cfg.resource_group, cfg.workspace, experiment_name).result()
```

Para **serviço Azure Machine Learning**, tente:

```python
from azureml.core import Experiment

experiment_name = 'tensorflow_experiment'
experiment = Experiment(ws, name=experiment_name)
```


#### <a name="submit-a-job"></a>Submeter uma tarefa
Depois de criar uma experimentação, tem algumas formas diferentes de submissão de uma execução. Neste exemplo, está a tentar criar um modelo de aprendizagem profunda com o TensorFlow e usará um avaliador que esteja Azure ML para fazer isso. Uma [Estimator](../machine-learning/service/how-to-train-ml-models.md) é simplesmente uma função de wrapper na configuração de execução subjacente, que torna mais fácil submeter execuções e atualmente é suportada para Pytorch e TensorFlow apenas. Através do conceito de arquivos de dados, também verá como é fácil torna-se para especificar os caminhos de montagem 

Para **Batch AI**, conseguiu desta forma:

```python
azure_file_share = 'afs'
azure_blob = 'bfs'
args_fmt = '--job_name={0} --num_gpus=1 --train_steps 10000 --checkpoint_dir=$AZ_BATCHAI_OUTPUT_MODEL --log_dir=$AZ_BATCHAI_OUTPUT_TENSORBOARD --data_dir=$AZ_BATCHAI_INPUT_DATASET --ps_hosts=$AZ_BATCHAI_PS_HOSTS --worker_hosts=$AZ_BATCHAI_WORKER_HOSTS --task_index=$AZ_BATCHAI_TASK_INDEX'

parameters = models.JobCreateParameters(
     cluster=models.ResourceId(id=cluster.id),
     node_count=2,
     input_directories=[
        models.InputDirectory(
            id='SCRIPT',
            path='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}/{1}'.format(azure_blob, script_directory)),
        models.InputDirectory(
            id='DATASET',
            path='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}/{1}'.format(azure_blob, mnist_dataset_directory))],
     std_out_err_path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
     output_directories=[
        models.OutputDirectory(
            id='MODEL',
            path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
            path_suffix='Models'),
        models.OutputDirectory(
            id='TENSORBOARD',
            path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
            path_suffix='Logs')
     ],
     mount_volumes=models.MountVolumes(
            azure_file_shares=[
                models.AzureFileShareReference(
                    account_name=cfg.storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=cfg.storage_account_key),
                    azure_file_url='https://{0}.file.core.windows.net/{1}'.format(
                        cfg.storage_account_name, azure_file_share_name),
                    relative_mount_path=azure_file_share)
            ],
            azure_blob_file_systems=[
                models.AzureBlobFileSystemReference(
                    account_name=cfg.storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=cfg.storage_account_key),
                    container_name=azure_blob_container_name,
                    relative_mount_path=azure_blob)
            ]
        ),
     container_settings=models.ContainerSettings(
         image_source_registry=models.ImageSourceRegistry(image='tensorflow/tensorflow:1.8.0-gpu')),
     tensor_flow_settings=models.TensorFlowSettings(
         parameter_server_count=1,
         worker_count=nodes_count,
         python_script_file_path='$AZ_BATCHAI_INPUT_SCRIPT/'+ script_to_deploy,
         master_command_line_args=args_fmt.format('worker'),
         worker_command_line_args=args_fmt.format('worker'),
         parameter_server_command_line_args=args_fmt.format('ps'),
     )
)
```

Submeter a tarefa propriamente dito no BatchAI é por meio da função de criar.

```python
job_name = datetime.utcnow().strftime('tf_%m_%d_%Y_%H%M%S')
job = client.jobs.create(cfg.resource_group, cfg.workspace, experiment_name, job_name, parameters).result()
print('Created Job {0} in Experiment {1}'.format(job.name, experiment.name))
```

As informações completas para este fragmento de código de treinamento (incluindo o ficheiro de mnist_replica.py que tinha carregado para a partilha de ficheiros acima) podem ser encontradas na [repositório do github de bloco de notas de exemplo de BatchAI](https://github.com/Azure/BatchAI/tree/2238607d5a028a0c5e037168aefca7d7bb165d5c/recipes/TensorFlow/TensorFlow-GPU-Distributed).

Para **serviço Azure Machine Learning**, tente:

```python
from azureml.train.dnn import TensorFlow

script_params={
    '--num_gpus': 1,
    '--train_steps': 500,
    '--input_data': ds_data.as_mount()

}

estimator = TensorFlow(source_directory=project_folder,
                       compute_target=gpu_cluster,
                       script_params=script_params,
                       entry_script='tf_mnist_replica.py',
                       node_count=2,
                       worker_count=2,
                       parameter_server_count=1,   
                       distributed_backend='ps',
                       use_gpu=True)
```

As informações completas para este fragmento de código de treinamento (incluindo o arquivo de tf_mnist_replica.py) podem ser encontradas na [repositório do github de bloco de notas do Azure ML exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/distributed-tensorflow-with-parameter-server). O arquivo de dados em si também pode ser montado em nós individuais ou os dados de treinamento podem ser baixados no próprio nó. Obter mais detalhes sobre a referenciar o arquivo de dados no seu estimator está no [documentação de serviço do Azure ML](../machine-learning/service/how-to-access-data.md#access). 

Submeter uma execução no Azure ML é por meio da função de envio.

```python
run = experiment.submit(estimator)
print(run)
```

Existe outra maneira de especificar parâmetros para a execução, com uma configuração de execução – especialmente útil para a definição de um ambiente de treinamento personalizado. Pode encontrar mais detalhes desta [bloco de notas do exemplo AmlCompute](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-amlcompute/train-on-amlcompute.ipynb). 

#### <a name="monitor-your-run"></a>Monitorizar a execução
Depois de submeter uma execução, pode optar por esperar pelo concluir ou monitorizá-lo no Azure ML com widgets Jupyter claro que pode invocar diretamente a partir do seu código. Também pode extrair o contexto de qualquer execução anterior pelo loop as experimentações vários numa área de trabalho e a pessoa é executado dentro de cada experimentação.

Para **Batch AI**, conseguiu desta forma:

```python
utils.job.wait_for_job_completion(client, cfg.resource_group, cfg.workspace, 
                                  experiment_name, job_name, cluster_name, 'stdouterr', 'stdout-wk-0.txt')

files = client.jobs.list_output_files(cfg.resource_group, cfg.workspace, experiment_name, job_name,
                                      models.JobsListOutputFilesOptions(outputdirectoryid='stdouterr')) 
for f in list(files):
    print(f.name, f.download_url or 'directory')
```


Para **serviço Azure Machine Learning**, tente:

```python
run.wait_for_completion(show_output=True)

from azureml.widgets import RunDetails
RunDetails(run).show()
```

Este é um instantâneo de como o widget carregaria em seu bloco de notas para observar o registos em tempo real: ![Diagrama de monitorização de widget](./media/overview-what-happened-batch-ai/monitor.png)



#### <a name="editing-a-cluster"></a>Edição de um cluster
A eliminar um cluster é muito simples. Além disso, Azure ML também permite-lhe atualizar um cluster a partir do bloco de notas, caso queira dimensioná-lo para um maior número de nós ou aumentar o tempo de espera de inatividade antes de reduzir verticalmente o cluster. Não permitimos-lhe alterar o tamanho da VM do cluster em si, uma vez que requer uma nova implementação com eficiência no back-end.

Para **Batch AI**, conseguiu desta forma:
```python
_ = client.clusters.delete(cfg.resource_group, cfg.workspace, cluster_name)
```

Para **serviço Azure Machine Learning**, tente:

```python
gpu_cluster.delete()

gpu_cluster.update(min_nodes=2, max_nodes=4, idle_seconds_before_scaledown=600)
```

## <a name="support"></a>Suporte

BatchAI está programado para extinguir em 31 de Março e já está a bloquear as subscrições novas impedir registo contra o serviço, a menos que seja na lista de permissões ao gerar uma exceção através do suporte.  Contacte-na [pré-visualização do Azure Batch AI treinamento](mailto:AzureBatchAITrainingPreview@service.microsoft.com) com alguma questão ou se tiver comentários, ao migrar para o serviço Azure Machine Learning.

O serviço do Azure Machine Learning é um serviço disponível em geral. Isso significa que ele vem com um SLA de compromisso e vários planos de suporte à sua escolha.

Não devem variar de preços para a utilização de infraestrutura do Azure através do serviço de BatchAI ou através do serviço Azure Machine Learning, à medida que só paga o preço de computação e armazenamento em ambos os casos. Para obter mais informações, consulte a [Calculadora de preços](https://azure.microsoft.com/pricing/details/machine-learning-service/).

Ver a disponibilidade regional entre os dois serviços sobre o [portal do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all).


## <a name="next-steps"></a>Passos Seguintes

+ Leitura a [descrição geral do serviço Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Configurar um destino de computação para a preparação de modelos](../machine-learning/service/how-to-set-up-training-targets.md) com o serviço Azure Machine Learning.

+ Reveja os [mapa do Azure](https://azure.microsoft.com/updates/) para saber de outras atualizações de serviço do Azure.
