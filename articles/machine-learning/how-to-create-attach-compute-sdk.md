---
title: Criar formação & implementar computas (Python)
titleSuffix: Azure Machine Learning
description: Utilize o Azure Machine Learning Python SDK para criar recursos de formação e implementação de computação (metas de computação) para a aprendizagem automática
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: c25ee5d9c626ba95d28f2247e6771d9fa1ada0f7
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662541"
---
# <a name="create-compute-targets-for-model-training-and-deployment-with-python-sdk"></a>Criar metas de computação para formação de modelos e implantação com Python SDK

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, use o Azure Machine Learning Python SDK para criar e gerir alvos de computação. Também pode criar e gerir alvos de computação com:
* [Estúdio Azure Machine Learning,](how-to-create-attach-compute-studio.md) 
* A [extensão do CLI](reference-azure-machine-learning-cli.md#resource-management) para a aprendizagem automática Azure
* A [extensão do Código VS](how-to-manage-resources-vscode.md#compute-clusters) para Azure Machine Learning.


## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree)
* [O Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
* [Um espaço de trabalho de aprendizagem de máquinas Azure](how-to-manage-workspace.md)

## <a name="limitations"></a>Limitações

Alguns dos cenários listados neste documento são marcados como __pré-visualização__. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="whats-a-compute-target"></a>O que é um alvo computacional?

Com a Azure Machine Learning, pode treinar o seu modelo numa variedade de recursos ou ambientes, coletivamente referidos como [__metas de computação.__](concept-azure-machine-learning-architecture.md#compute-targets) Um alvo de computação pode ser uma máquina local ou um recurso em nuvem, como um Azure Machine Learning Compute, Azure HDInsight ou uma máquina virtual remota.  Também pode criar metas de computação para a implementação do modelo, conforme descrito em ["Onde e como implementar os seus modelos".](how-to-deploy-and-where.md)

## <a name="compute-targets-for-training"></a>Metas de cálculo para a formação

A Azure Machine Learning tem um suporte variado em diferentes alvos de computação. Um ciclo de vida típico de desenvolvimento de modelos começa com dev/experimentação em uma pequena quantidade de dados. Nesta fase, recomendamos a utilização de um ambiente local. Por exemplo, o seu computador local ou um VM baseado na nuvem. À medida que aumenta a sua formação em conjuntos de dados maiores, ou realiza treino distribuído, recomendamos a utilização do Azure Machine Learning Compute para criar um cluster único ou multi-nós que se autoescala cada vez que submete uma corrida. Também pode anexar o seu próprio recurso compute, embora o suporte para vários cenários possa variar conforme descrito abaixo:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

> [!NOTE]
> Os clusters Azure Machine Learning Compute podem ser criados como um recurso persistente ou criados dinamicamente quando solicita uma execução. A criação baseada em execução remove o alvo do cálculo após o treino estar completo, por isso não é possível reutilizar os alvos de computação criados desta forma.

Utilize as secções abaixo para configurar estes alvos de cálculo:

* [Computador local](#local)
* [Aglomerado de computação Azure Machine Learning](#amlcompute)
* [Instância de computação do Azure Machine Learning](#instance)
* [Máquinas virtuais remotas](#vm)
* [Azure HDInsight](#hdinsight)

## <a name="compute-targets-for-inference"></a>Metas de cálculo para inferência

Ao realizar inferência, a Azure Machine Learning cria um recipiente Docker que acolhe o modelo e os recursos associados necessários para a sua utilização. Este recipiente é então utilizado num dos seguintes cenários de implantação:

* Como um __serviço web__ que é usado para inferência em tempo real. As implementações de serviço web utilizam um dos seguintes alvos de computação:

    * [Computador local](#local)
    * [Instância de computação do Azure Machine Learning](#instance)
    * [Azure Container Instances](#aci)
    * [Azure Kubernetes Service](how-to-create-attach-kubernetes.md)
    * Funções Azure (pré-visualização). A implementação para as funções Azure depende apenas da Aprendizagem automática Azure para construir o recipiente Docker. A partir daí, é implantado utilizando funções Azure. Para obter mais informações, consulte [Implementar um modelo de aprendizagem automática para funções Azure (pré-visualização)](how-to-deploy-functions.md).

* Como um ponto final __de inferência de lote__ que é usado periodicamente para processar lotes de dados. As inferências do lote utilizam [o cluster de cálculo Azure Machine Learning](#amlcompute).

* Para um __dispositivo IoT__ (pré-visualização). A implantação num dispositivo IoT depende apenas da Azure Machine Learning para construir o recipiente Docker. A partir daí, é implantado utilizando a Azure IoT Edge. Para obter mais informações, consulte [implementar como um módulo IoT Edge (pré-visualização)](/azure/iot-edge/tutorial-deploy-machine-learning).

## <a name="local-computer"></a><a id="local"></a>Computador local

Quando utiliza o computador local para **treinar,** não há necessidade de criar um alvo de computação.  Basta [submeter o treino](how-to-set-up-training-targets.md) da sua máquina local.

Quando utilizar o computador local para **inferência,** deve instalar o Docker. Para efetuar a implementação, utilize [LocalWebservice.deploy_configuration para](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) definir a porta que o serviço web utilizará. Em seguida, utilize o processo de implantação normal, conforme descrito nos [modelos Deploy com Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="azure-machine-learning-compute-cluster"></a><a id="amlcompute"></a>Aglomerado de computação Azure Machine Learning

O cluster compute Azure Machine Learning é uma infraestrutura de computação gerida que permite criar facilmente um único ou multi-nó compute. O cálculo é criado dentro da sua região do espaço de trabalho como um recurso que pode ser partilhado com outros utilizadores no seu espaço de trabalho. O cálculo aumenta automaticamente quando um trabalho é submetido e pode ser colocado numa Rede Virtual Azure. O cálculo executa num ambiente contentorizado e embala as dependências do seu modelo num [recipiente Docker.](https://www.docker.com/why-docker)

Você pode usar Azure Machine Learning Compute para distribuir um processo de treino ou inferência de lote através de um conjunto de nós de cálculo cpu ou GPU na nuvem. Para obter mais informações sobre os tamanhos de VM que incluem GPUs, consulte [os tamanhos de máquinas virtuais otimizadas pela GPU.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) 

O Azure Machine Learning Compute tem limites predefinidos, como o número de núcleos que podem ser atribuídos. Para obter mais informações, consulte [Gerir e solicitar quotas para recursos Azure.](how-to-manage-quotas.md)

> [!TIP]
> Os clusters podem geralmente escalar até 100 nós, desde que tenha quota suficiente para o número de núcleos necessários. Por predefinição, os clusters são configurados com comunicação inter-nódoa ativada entre os nós do cluster para apoiar empregos de MPI, por exemplo. No entanto, pode escalar os seus clusters para 1000s de nós simplesmente [levantando um bilhete](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)de apoio, e solicitando para permitir a lista da sua subscrição, ou espaço de trabalho, ou um cluster específico para desativar a comunicação inter-nól. 

O Azure Machine Learning Compute pode ser reutilizado através de corridas. O cálculo pode ser partilhado com outros utilizadores no espaço de trabalho e é mantido entre corridas, escalando automaticamente os nós para cima ou para baixo com base no número de runs submetidos, e o max_nodes definido no seu cluster. A definição min_nodes controla os nós mínimos disponíveis.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

1. **Criar e anexar:** Para criar um recurso Azure Machine Learning Compute em Python, especifique as propriedades **vm_size** e **max_nodes.** A Azure Machine Learning utiliza então falhas inteligentes para as outras propriedades. A computação escala automaticamente para zero nós quando não é usada.   VMs dedicados são criados para executar os seus trabalhos conforme necessário.
    
    * **vm_size**: A família VM dos nós criados pela Azure Machine Learning Compute.
    * **max_nodes**: O número máximo de nós para autoescalar até quando você tem um trabalho no Azure Machine Learning Compute.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Também pode configurar várias propriedades avançadas quando criar o Azure Machine Learning Compute. As propriedades permitem criar um cluster persistente de tamanho fixo, ou dentro de uma Rede Virtual Azure existente na sua subscrição.  Consulte a [aula AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) para mais detalhes.

    Ou pode criar e anexar um recurso persistente Azure Machine Learning Compute no [estúdio Azure Machine Learning](how-to-create-attach-compute-studio.md#portal-create).

Agora que ligou o cálculo, o próximo passo é [submeter a corrida](how-to-set-up-training-targets.md) de treino ou executar [inferência de lote.](how-to-use-parallel-run-step.md)

 ### <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Reduza o custo do seu cluster de cálculo

Também pode optar por utilizar [VMs de baixa prioridade](concept-plan-manage-cost.md#low-pri-vm) para executar algumas ou todas as suas cargas de trabalho. Estes VMs não têm disponibilidade garantida e podem ser antecipados durante a utilização. Um trabalho preventivo é reiniciado, não retomado. 

Utilize qualquer uma destas formas de especificar um VM de baixa prioridade:
    
* No estúdio, escolha **Low Priority** quando criar um VM.
    
* Com o Python SDK, desa estale o `vm_priority` atributo na sua configuração de provisionamento.  
    
    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                vm_priority='lowpriority',
                                                                max_nodes=4)
    ```
    
* Utilizando o CLI, desemprete o `vm-priority` seguinte:
    
    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 ### <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Configurar identidade gerida

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Configure a identidade gerida na sua configuração de provisionamento:  
    
* Identidade gerida atribuída pelo sistema:
    ```python
    # configure cluster with a system-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="SystemAssigned",
                                                            )
    ```

* Identidade gerida atribuída pelo utilizador: 

    ```python
    # configure cluster with a user-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="UserAssigned",
                                                            identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])

    cpu_cluster_name = "cpu-cluster"
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    ```

Adicione a identidade gerida a um cluster de computação existente:  
    
* Identidade gerida atribuída pelo sistema:

    ```python
    # add a system-assigned managed identity
    cpu_cluster.add_identity(identity_type="SystemAssigned")
    ````

* Identidade gerida atribuída pelo utilizador:

    ```python
    # add a user-assigned managed identity
    cpu_cluster.add_identity(identity_type="UserAssigned", 
                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    ```

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

#### <a name="managed-identity-usage"></a>Utilização de identidade gerida

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]


## <a name="azure-machine-learning-compute-instance"></a><a id="instance"></a>Instância de computação do Azure Machine Learning

[A azure Machine Learning compute instance](concept-compute-instance.md) é uma infraestrutura de computação gerida que permite criar facilmente um único VM. O cálculo é criado dentro da sua região do espaço de trabalho, mas ao contrário de um cluster de cálculo, um caso não pode ser partilhado com outros utilizadores no seu espaço de trabalho. Também o caso não diminui automaticamente.  Tem de parar o recurso para evitar cargas em curso.

Uma instância computacional pode executar vários trabalhos em paralelo e tem uma fila de emprego. 

As instâncias computacional podem executar empregos de forma segura num [ambiente de rede virtual,](how-to-enable-virtual-network.md#compute-instance)sem exigir que as empresas abram portas SSH. O trabalho executa num ambiente contentorizado e embala as dependências do seu modelo num contentor Docker. 

1. **Criar e anexar:** 
    
    ```python
    import datetime
    import time
    
    from azureml.core.compute import ComputeTarget, ComputeInstance
    from azureml.core.compute_target import ComputeTargetException
    
    # Choose a name for your instance
    # Compute instance name should be unique across the azure region
    compute_name = "ci{}".format(ws._workspace_id)[:10]
    
    # Verify that instance does not exist already
    try:
        instance = ComputeInstance(workspace=ws, name=compute_name)
        print('Found existing instance, use it.')
    except ComputeTargetException:
        compute_config = ComputeInstance.provisioning_configuration(
            vm_size='STANDARD_D3_V2',
            ssh_public_access=False,
            # vnet_resourcegroup_name='<my-resource-group>',
            # vnet_name='<my-vnet-name>',
            # subnet_name='default',
            # admin_user_ssh_public_key='<my-sshkey>'
        )
        instance = ComputeInstance.create(ws, compute_name, compute_config)
        instance.wait_for_completion(show_output=True)
    ```

Agora que ligou o cálculo e configura o seu percurso, o próximo passo é [submeter o treino](how-to-set-up-training-targets.md) ou implementar um modelo de [inferência.](how-to-deploy-local-container-notebook-vm.md)

## <a name="azure-container-instance"></a><a id="aci"></a>Instância de Contentor do Azure

As instâncias do recipiente Azure (ACI) são criadas dinamicamente quando se implementa um modelo. Não é possível criar ou anexar OCI ao seu espaço de trabalho de outra forma. Para obter mais informações, consulte [implementar um modelo para Azure Container Instances](how-to-deploy-azure-container-instance.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

O Serviço Azure Kubernetes (AKS) permite uma variedade de opções de configuração quando usado com Azure Machine Learning. Para mais informações, consulte [Como criar e anexar o Serviço Azure Kubernetes](how-to-create-attach-kubernetes.md).

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

1. **Configuração**: Crie uma configuração de execução para o alvo de computação DSVM. Docker e conda são usados para criar e configurar o ambiente de treino no DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Agora que ligou o cálculo e configura o seu percurso, o próximo passo é [submeter a corrida de treino.](how-to-set-up-training-targets.md)

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

> [!TIP]
> Os oleodutos Azure Machine Learning só podem funcionar com dados armazenados na loja de dados predefinida da conta Data Lake Analytics. Se os dados com os qual precisa trabalhar estiverem numa loja não padrão, pode utilizar um [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py&preserve-view=true) para copiar os dados antes do treino.

## <a name="notebook-examples"></a>Exemplos de cadernos

Consulte estes cadernos para exemplos de formação com vários alvos de computação:
* [como usar-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutoriais/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximos passos

* Utilize o recurso computacional para [submeter uma formação](how-to-set-up-training-targets.md).
* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md) usa um alvo de computação gerido para treinar um modelo.
* Aprenda a [sintonizar eficientemente os hiperparmetros](how-to-tune-hyperparameters.md) para construir modelos melhores.
* Uma vez treinado, aprenda [como e onde implementar modelos.](how-to-deploy-and-where.md)
* [Use a azure machine learning com redes virtuais Azure](how-to-enable-virtual-network.md)
