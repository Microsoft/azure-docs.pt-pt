---
title: Configurar metas de cálculo de inferência de & de formação
titleSuffix: Azure Machine Learning
description: Adicione recursos computacional (metas de computação) ao seu espaço de trabalho para usar para formação e inferência de machine learning
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 10/02/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: cc4256ae0591e9fc82dcdce7c66514710fad3f57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711379"
---
# <a name="set-up-compute-targets-for-model-training-and-deployment"></a>Configurar metas de computação para formação e implantação de modelos

Saiba como anexar recursos de computação Azure ao seu espaço de trabalho Azure Machine Learning.  Em seguida, pode usar estes recursos como alvos de treinamento e inferência nas suas [tarefas](concept-compute-target.md) de aprendizagem automática.

Neste artigo, aprenda a configurar o seu espaço de trabalho para utilizar estes recursos computacional:

* O seu computador local
* Máquinas virtuais remotas
* Azure HDInsight
* Azure Batch
* Azure Databricks
* Azure Data Lake Analytics
* Instância de Contentor do Azure

Para utilizar alvos de cálculo geridos pela Azure Machine Learning, consulte:


* [Instância de computação do Azure Machine Learning](how-to-create-manage-compute-instance.md)
* [Aglomerado de computação Azure Machine Learning](how-to-create-attach-compute-cluster.md)
* [Aglomerado de serviçoS Azure Kubernetes](how-to-create-attach-kubernetes.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](how-to-manage-workspace.md)

* A [extensão Azure CLI para o serviço de aprendizagem automática](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)ou a extensão do [Código do Estúdio Visual Azure Machine Learning.](tutorial-setup-vscode-extension.md)

## <a name="limitations"></a>Limitações

* **Não crie anexos múltiplos e simultâneos ao mesmo cálculo** do seu espaço de trabalho. Por exemplo, anexar um cluster de serviço Azure Kubernetes a um espaço de trabalho usando dois nomes diferentes. Cada novo acessório quebrará os acessórios existentes anteriores.

    Se pretender voltar a colocar um alvo de cálculo, por exemplo, para alterar TLS ou outra configuração de configuração do cluster, deve primeiro remover o acessório existente.

## <a name="whats-a-compute-target"></a>O que é um alvo computacional?

Com a Azure Machine Learning, pode treinar o seu modelo numa variedade de recursos ou ambientes, coletivamente referidos como [__metas de computação.__](concept-azure-machine-learning-architecture.md#compute-targets) Um alvo de computação pode ser uma máquina local ou um recurso em nuvem, como um Azure Machine Learning Compute, Azure HDInsight ou uma máquina virtual remota.  Também utiliza alvos de cálculo para a implementação do modelo, conforme descrito em ["Onde e como implementar os seus modelos".](how-to-deploy-and-where.md)


## <a name="local-computer"></a><a id="local"></a>Computador local

Quando utiliza o computador local para **treinar,** não há necessidade de criar um alvo de computação.  Basta [submeter o treino](how-to-set-up-training-targets.md) da sua máquina local.

Quando utilizar o computador local para **inferência,** deve instalar o Docker. Para efetuar a implementação, utilize [LocalWebservice.deploy_configuration para](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py&preserve-view=true#deploy-configuration-port-none-) definir a porta que o serviço web utilizará. Em seguida, utilize o processo de implantação normal, conforme descrito nos [modelos Deploy com Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="remote-virtual-machines"></a><a id="vm"></a>Máquinas virtuais remotas

O Azure Machine Learning também suporta trazer o seu próprio recurso de computação e ligá-lo ao seu espaço de trabalho. Um desse tipo de recurso é um VM remoto arbitrário, desde que seja acessível a partir de Azure Machine Learning. O recurso pode ser um Azure VM, um servidor remoto na sua organização ou no local. Especificamente, dado o endereço IP e credenciais (nome de utilizador e palavra-passe, ou chave SSH), pode utilizar qualquer VM acessível para execuções remotas.

Você pode usar um ambiente conda construído pelo sistema, um ambiente Python já existente, ou um recipiente Docker. Para executar num contentor Docker, tem de ter um motor Docker a funcionar no VM. Esta funcionalidade é especialmente útil quando se pretende um ambiente dev/experimentação mais flexível, baseado na nuvem do que a sua máquina local.

Utilize a Máquina Virtual Azure Data Science (DSVM) como O VM Azure de eleição para este cenário. Este VM é um ambiente pré-configurado de ciência de dados e desenvolvimento de IA em Azure. O VM oferece uma escolha curada de ferramentas e quadros para o desenvolvimento de aprendizagem automática de ciclo de vida completo. Para obter mais informações sobre como utilizar o DSVM com Azure Machine Learning, consulte [configurar um ambiente de desenvolvimento.](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm)

1. **Criar:** Crie um DSVM antes de o utilizar para treinar o seu modelo. Para criar este recurso, consulte [a Ficha da Máquina Virtual de Ciência de Dados para Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > A Azure Machine Learning só suporta máquinas virtuais que executam **Ubuntu.** Quando criar um VM ou escolher um VM existente, tem de selecionar um VM que utilize Ubuntu.
    > 
    > O Azure Machine Learning também requer que a máquina virtual tenha um __endereço IP público.__

1. **Anexar:** Para anexar uma máquina virtual existente como alvo de computação, tem de fornecer o ID, o nome de utilizador e a palavra-passe para a máquina virtual. O ID de recursos do VM pode ser construído usando o ID de subscrição, nome de grupo de recursos e nome VM usando o seguinte formato de cadeia: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Ou pode anexar o DSVM ao seu espaço de trabalho [utilizando o estúdio Azure Machine Learning](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Não crie ligações múltiplas e simultâneas ao mesmo DSVM do seu espaço de trabalho. Cada novo acessório quebrará os acessórios existentes anteriores.

1. **Configuração**: Crie uma configuração de execução para o alvo de computação DSVM. Docker e conda são usados para criar e configurar o ambiente de treino no DSVM.

   ```python
   from azureml.core import ScriptRunConfig
   from azureml.core.environment import Environment
   from azureml.core.conda_dependencies import CondaDependencies
   
   # Create environment
   myenv = Environment(name="myenv")
   
   # Specify the conda dependencies
   myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
   
   # If no base image is explicitly specified the default CPU image "azureml.core.runconfig.DEFAULT_CPU_IMAGE" will be used
   # To use GPU in DSVM, you should specify the default GPU base Docker image or another GPU-enabled image:
   # myenv.docker.enabled = True
   # myenv.docker.base_image = azureml.core.runconfig.DEFAULT_GPU_IMAGE
   
   # Configure the run configuration with the Linux DSVM as the compute target and the environment defined above
   src = ScriptRunConfig(source_directory=".", script="train.py", compute_target=compute, environment=myenv) 
   ```

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight é uma plataforma popular para análise de big data. A plataforma fornece Apache Spark, que pode ser usado para treinar o seu modelo.

1. **Criar:** Crie o cluster HDInsight antes de o utilizar para treinar o seu modelo. Para criar uma faísca no cluster HDInsight, consulte [Criar um cluster de faíscas em HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    > [!WARNING]
    > A Azure Machine Learning requer que o cluster HDInsight tenha um __endereço IP público.__

    Ao criar o cluster, tem de especificar o nome de utilizador e a palavra-passe SSH. Tome nota destes valores, pois precisa que utilize o HDInsight como alvo de computação.
    
    Após a criação do cluster, conecte-se com o nome de hospedeiro \<clustername> -ssh.azurehdinsight.net, onde \<clustername> está o nome que forneceu para o cluster. 

1. **Anexar:** Para anexar um cluster HDInsight como alvo de computação, tem de fornecer o ID, o nome de utilizador e a palavra-passe do cluster HDInsight. O ID de recursos do cluster HDInsight pode ser construído usando o ID de subscrição, nome de grupo de recursos e nome de cluster HDInsight usando o seguinte formato de cadeia: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Ou pode anexar o cluster HDInsight ao seu espaço de trabalho utilizando o [estúdio Azure Machine Learning](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Não crie múltiplos anexos simultâneos ao mesmo HDInsight do seu espaço de trabalho. Cada novo acessório quebrará os acessórios existentes anteriores.

1. **Configuração**: Crie uma configuração de execução para o alvo do cálculo HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Agora que ligou o cálculo e configura o seu percurso, o próximo passo é [submeter a corrida de treino.](how-to-set-up-training-targets.md)

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

O Azure Batch é utilizado para executar aplicações de computação paralela e de alto desempenho (HPC) de grande escala na nuvem. O AzureBatchStep pode ser usado num Pipeline de Aprendizagem de Máquinas Azure para submeter empregos a um conjunto de máquinas Azure Batch.

Para anexar o Azure Batch como alvo de computação, deve utilizar o Azure Machine Learning SDK e fornecer as seguintes informações:

-    **Nome do cálculo Azure Batch**: Um nome amigável a ser usado para o cálculo dentro do espaço de trabalho
-    **Nome da conta Azure Batch**: O nome da conta Azure Batch
-    **Grupo de Recursos**: O grupo de recursos que contém a conta Azure Batch.

O seguinte código demonstra como anexar o Azure Batch como alvo de computação:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> Não crie ligações múltiplas e simultâneas ao mesmo Azure Batch do seu espaço de trabalho. Cada novo acessório quebrará os acessórios existentes anteriores.

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks é um ambiente baseado em Apache Spark na nuvem Azure. Pode ser usado como um alvo de computação com um pipeline Azure Machine Learning.

Crie um espaço de trabalho Azure Databricks antes de o utilizar. Para criar um recurso de espaço de trabalho, consulte o trabalho Run a Spark no documento [Azure Databricks.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

Para anexar a Azure Databricks como alvo de computação, forneça as seguintes informações:

* __Databricks computação nome__: O nome que pretende atribuir a este recurso de computação.
* __Databricks nome do espaço de trabalho__: O nome do espaço de trabalho Azure Databricks.
* __Databricks acesso token__: O token de acesso usado para autenticar a Azure Databricks. Para gerar um token de acesso, consulte o documento [de Autenticação.](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html)

O código que se segue demonstra como anexar a Azure Databricks como alvo de computação com o Azure Machine Learning SDK __(O espaço de trabalho Databricks precisa de estar presente na mesma subscrição que o seu espaço de trabalho AML):__

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Para um exemplo mais detalhado, consulte um [caderno de exemplo](https://aka.ms/pl-databricks) no GitHub.

> [!WARNING]
> Não crie anexos múltiplos e simultâneos com as mesmas Azure Databricks do seu espaço de trabalho. Cada novo acessório quebrará os acessórios existentes anteriores.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics é uma plataforma de análise de dados na nuvem Azure. Pode ser usado como um alvo de computação com um pipeline Azure Machine Learning.

Crie uma conta Azure Data Lake Analytics antes de a utilizar. Para criar este recurso, consulte o [Get start with Azure Data Lake Analytics.](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal)

Para anexar o Data Lake Analytics como um alvo de computação, deve utilizar o Azure Machine Learning SDK e fornecer as seguintes informações:

* __Nome do cálculo__: O nome que pretende atribuir a este recurso de cálculo.
* __Grupo de Recursos__: O grupo de recursos que contém a conta Data Lake Analytics.
* __Nome da conta__: O nome da conta Data Lake Analytics.

O seguinte código demonstra como anexar o Data Lake Analytics como um alvo de computação:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Para um exemplo mais detalhado, consulte um [caderno de exemplo](https://aka.ms/pl-adla) no GitHub.

> [!WARNING]
> Não crie múltiplos anexos simultâneos ao mesmo ADLA do seu espaço de trabalho. Cada novo acessório quebrará os acessórios existentes anteriores.

> [!TIP]
> Os oleodutos Azure Machine Learning só podem funcionar com dados armazenados na loja de dados predefinida da conta Data Lake Analytics. Se os dados com os qual precisa trabalhar estiverem numa loja não padrão, pode utilizar um [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py&preserve-view=true) para copiar os dados antes do treino.

## <a name="azure-container-instance"></a><a id="aci"></a>Instância de Contentor do Azure

As instâncias do recipiente Azure (ACI) são criadas dinamicamente quando se implementa um modelo. Não é possível criar ou anexar OCI ao seu espaço de trabalho de outra forma. Para obter mais informações, consulte [implementar um modelo para Azure Container Instances](how-to-deploy-azure-container-instance.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

O Serviço Azure Kubernetes (AKS) permite uma variedade de opções de configuração quando usado com Azure Machine Learning. Para mais informações, consulte [Como criar e anexar o Serviço Azure Kubernetes](how-to-create-attach-kubernetes.md).


## <a name="notebook-examples"></a>Exemplos de cadernos

Consulte estes cadernos para exemplos de formação com vários alvos de computação:
* [como usar-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutoriais/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes

* Utilize o recurso computacional para [configurar e submeter uma formação](how-to-set-up-training-targets.md).
* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md) usa um alvo de computação gerido para treinar um modelo.
* Aprenda a [sintonizar eficientemente os hiperparmetros](how-to-tune-hyperparameters.md) para construir modelos melhores.
* Uma vez treinado, aprenda [como e onde implementar modelos.](how-to-deploy-and-where.md)
* [Use a azure machine learning com redes virtuais Azure](how-to-enable-virtual-network.md)
