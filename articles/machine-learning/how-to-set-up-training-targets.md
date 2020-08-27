---
title: Utilize alvos de computação para a formação de modelos
titleSuffix: Azure Machine Learning
description: Configure os ambientes de formação (metas de computação) para a formação de modelos de aprendizagem automática. Pode facilmente alternar entre ambientes de treino. Comece a treinar localmente. Se precisar de escalar, mude para um alvo de computação baseado na nuvem.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: e83faee7d72026dafc50b21d0a0773e663e5a03a
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933121"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Configurar e utilizar metas de computação para a formação de modelos 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Com a Azure Machine Learning, pode treinar o seu modelo numa variedade de recursos ou ambientes, coletivamente referidos como [__metas de computação.__](concept-azure-machine-learning-architecture.md#compute-targets) Um alvo de computação pode ser uma máquina local ou um recurso em nuvem, como um Azure Machine Learning Compute, Azure HDInsight ou uma máquina virtual remota.  Também pode criar metas de computação para a implementação do modelo, conforme descrito em ["Onde e como implementar os seus modelos".](how-to-deploy-and-where.md)

Você pode criar e gerir um alvo de computação usando o Azure Machine Learning SDK, Azure Machine Learning studio, Azure CLI, ou Azure Machine Learning VS Code. Se tiver metas de computação que foram criadas através de outro serviço (por exemplo, um cluster HDInsight), pode usá-las ligando-as ao seu espaço de trabalho Azure Machine Learning.
 
Neste artigo, aprende-se a usar vários alvos de computação para a formação de modelos.  Os passos para todos os alvos de computação seguem o mesmo fluxo de trabalho:
1. __Crie__ um alvo de computação se ainda não tiver um.
2. __Prenda__ o alvo do cálculo ao seu espaço de trabalho.
3. __Configure__ o alvo do cálculo de modo a que contenha o ambiente Python e as dependências de pacotes necessárias pelo seu script.


>[!NOTE]
> O código deste artigo foi testado com a versão 1.0.74 do Azure Machine Learning SDK.

## <a name="compute-targets-for-training"></a>Metas de cálculo para a formação

A Azure Machine Learning tem um suporte variado em diferentes alvos de computação. Um ciclo de vida típico de desenvolvimento de modelos começa com dev/experimentação em uma pequena quantidade de dados. Nesta fase, recomendamos a utilização de um ambiente local. Por exemplo, o seu computador local ou um VM baseado na nuvem. À medida que aumenta a sua formação em conjuntos de dados maiores, ou realiza treino distribuído, recomendamos a utilização do Azure Machine Learning Compute para criar um cluster único ou multi-nós que se autoescala cada vez que submete uma corrida. Também pode anexar o seu próprio recurso compute, embora o suporte para vários cenários possa variar conforme descrito abaixo:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Os clusters Azure Machine Learning Compute podem ser criados como um recurso persistente ou criados dinamicamente quando solicita uma execução. A criação baseada em execução remove o alvo do cálculo após o treino estar completo, por isso não é possível reutilizar os alvos de computação criados desta forma.

## <a name="whats-a-run-configuration"></a>O que é uma configuração de execução?

Ao treinar, é comum começar no computador local e, mais tarde, executar esse script de treino num alvo de computação diferente. Com o Azure Machine Learning, pode executar o seu script em vários alvos de computação sem ter de alterar o seu script.

Tudo o que precisa fazer é definir o ambiente para cada alvo de cálculo dentro de uma **configuração de execução**.  Em seguida, quando quiser executar a sua experiência de treino num alvo de computação diferente, especifique a configuração de execução para esse cálculo. Para obter detalhes sobre a especificação de um ambiente e encaderna-lo para executar a configuração, consulte [Criar e gerir ambientes para treino e implantação](how-to-use-environments.md).

Saiba mais sobre [a submissão de experiências](#submit) no final deste artigo.

## <a name="whats-an-estimator"></a>O que é um estimador?

Para facilitar a formação de modelos utilizando quadros populares, o Azure Machine Learning Python SDK fornece uma abstração de nível superior alternativa, a classe de estimador.  Esta classe permite-lhe construir facilmente configurações de execução. Pode criar e utilizar um [Estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico para submeter scripts de formação que utilizem qualquer estrutura de aprendizagem que escolher (como o scikit-learn). Recomendamos a utilização de um estimador para o treino, uma vez que constrói automaticamente objetos incorporados como um ambiente ou objetos runConfiguration para si. Se desejar ter mais controlo sobre a forma como estes objetos são criados e especificar quais os pacotes a instalar para a sua experiência, siga [estes passos](#amlcompute) para submeter as suas experiências de treino utilizando um objeto RunConfiguration num Azure Machine Learning Compute.

A Azure Machine Learning fornece estimativas específicas para [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)e [Ray RLlib](how-to-use-reinforcement-learning.md).

Para obter mais informações, consulte [os Modelos ML do Comboio com estimadores.](how-to-train-ml-models.md)

## <a name="whats-an-ml-pipeline"></a>O que é um Oleoduto ML?

Com os gasodutos ML, pode otimizar o seu fluxo de trabalho com simplicidade, velocidade, portabilidade e reutilização. Ao construir oleodutos com Azure Machine Learning, pode focar-se na sua experiência, machine learning, em vez de em infraestruturas e automação.

Os gasodutos ML são construídos a partir de **vários passos,** que são unidades computacionais distintas no oleoduto. Cada passo pode correr independentemente e usar recursos computacional isolados. Esta abordagem permite que vários cientistas de dados trabalhem no mesmo oleoduto ao mesmo tempo sem sobrecarregar recursos compute, e também facilita a utilização de diferentes tipos/tamanhos computacional para cada passo.

> [!TIP]
> Os gasodutos ML podem utilizar configuração ou estimadores de execução quando se treinam modelos.

Enquanto os gasodutos ML podem formar modelos, também podem preparar dados antes de treinar e implementar modelos após o treino. Um dos casos de utilização primária para os gasodutos é a pontuação do lote. Para obter mais informações, consulte [Pipelines: Otimize os fluxos de trabalho de aprendizagem automática.](concept-ml-pipelines.md)

## <a name="set-up-in-python"></a>Instale-se em Python

Utilize as secções abaixo para configurar estes alvos de cálculo:

* [Computador local](#local)
* [Aglomerado de computação Azure Machine Learning](#amlcompute)
* [Instância de computação do Azure Machine Learning](#instance)
* [Máquinas virtuais remotas](#vm)
* [Azure HDInsight](#hdinsight)


### <a name="local-computer"></a><a id="local"></a>Computador local

1. **Criar e anexar:** Não há necessidade de criar ou anexar um alvo de computação para usar o computador local como ambiente de treino.  

1. **Configure**: Quando utiliza o computador local como alvo de computação, o código de formação é executado no seu [ambiente de desenvolvimento](how-to-configure-environment.md).  Se esse ambiente já tiver as embalagens Python de que necessita, utilize o ambiente gerido pelo utilizador.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Agora que ligou o cálculo e configura o seu percurso, o próximo passo é [submeter a corrida de treino.](#submit)

### <a name="azure-machine-learning-compute-cluster"></a><a id="amlcompute"></a>Aglomerado de computação Azure Machine Learning

O cluster compute Azure Machine Learning é uma infraestrutura de computação gerida que permite criar facilmente um único ou multi-nó compute. O cálculo é criado dentro da sua região do espaço de trabalho como um recurso que pode ser partilhado com outros utilizadores no seu espaço de trabalho. O cálculo aumenta automaticamente quando um trabalho é submetido e pode ser colocado numa Rede Virtual Azure. O cálculo executa num ambiente contentorizado e embala as dependências do seu modelo num [recipiente Docker.](https://www.docker.com/why-docker)

Você pode usar Azure Machine Learning Compute para distribuir o processo de treino através de um conjunto de nós de cálculo CPU ou GPU na nuvem. Para obter mais informações sobre os tamanhos de VM que incluem GPUs, consulte [os tamanhos de máquinas virtuais otimizadas pela GPU.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) 

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

    Ou pode criar e anexar um recurso persistente Azure Machine Learning Compute no [estúdio Azure Machine Learning](#portal-create).

   
1. **Configuração**: Crie uma configuração de execução para o alvo de computação persistente.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Agora que ligou o cálculo e configura o seu percurso, o próximo passo é [submeter a corrida de treino.](#submit)

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

 Os clusters de computação Azure Machine Learning também suportam [identidades geridas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para autenticar o acesso aos recursos do Azure sem incluir credenciais no seu código. Existem dois tipos de identidades geridas:

* Uma **identidade gerida atribuída pelo sistema** é ativada diretamente no cluster de cálculo de Aprendizagem de Máquinas Azure. O ciclo de vida de uma identidade atribuída ao sistema está diretamente ligado ao cluster compute. Se o cluster de cálculo for eliminado, o Azure limpa automaticamente as credenciais e a identidade em Azure AD.
* Uma **identidade gerida atribuída ao utilizador** é um recurso autónomo da Azure fornecido através do serviço de Identidade Gerida Azure. Pode atribuir uma identidade gerida atribuída ao utilizador a vários recursos, e persiste durante o tempo que quiser.

Utilize qualquer uma destas formas de especificar uma identidade gerida para um cluster de cálculo:
    
* No estúdio, quando computa a criação de clusters ou ao editar detalhes do cluster compute, **altere Atribuir uma identidade gerida** e especificar uma identidade atribuída ao sistema ou identidade atribuída ao utilizador.
    
* Com o Python SDK, desa estale o `identity_type` atributo na sua configuração de provisionamento.  
    
    ```python
    # configure cluster with a system-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="SystemAssigned",
                                                            )

    # configure cluster with a user-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="UserAssigned",
                                                            identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])

    cpu_cluster_name = "cpu-cluster"
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    ```

* Com o Python SDK, desa estale os `identity_type` atributos e `identity_id` (se criar uma identidade gerida atribuída pelo utilizador) na sua configuração de provisionamento.  
    
    ```python
    # add a system-assigned managed identity
    cpu_cluster.add_identity(identity_type="SystemAssigned")

    # add a user-assigned managed identity
    cpu_cluster.add_identity(identity_type="UserAssigned", 
                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    ```
    
* Utilizando o CLI, desapedaça o atributo durante a `assign-identity` criação do cluster:
    
    ```azurecli
    # create a cluster with a user-assigned managed identity
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'

    # create a cluster with a system-managed identity
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'

* Using the CLI, execute the following commands to assign a managed identity on an existing cluster:
    
    ```azurecli
    # add a user-assigned managed identity
    az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'

    # add a system-assigned managed identity
    az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'

> [!NOTE]
> Azure Machine Learning compute clusters support only **one system-assigned identity** or **multiple user-assigned identities**, not both concurrently.
> 
> Additionally, you can assign only one managed identity from the studio.

#### Managed identity usage

AML defines the **default managed identity** as the system-assigned managed identity or the first user-assigned managed identity.

During a run there are two applications of an identity:
1. The system uses an identity to setup the user's storage mounts, container registry, and datastores.
    * In this case, the system will use the default managed identity.

1. The user applies an identity to access resources from within the code for a submitted run
    
    * In this case, the user must provide the *client_id* corresponding to the managed identity they want to use to retrieve a credential. 
    * Alternatively, AML exposes the user-assigned identity's client ID through the *DEFAULT_IDENTITY_CLIENT_ID* environment variable.
    
    For example, to retrieve a token for a datastore with the default managed identity:
    
    ```python
    client_id = os.environ.get('DEFAULT_IDENTITY_CLIENT_ID')
    credential = ManagedIdentityCredential(client_id=client_id)
    token = credential.get_token('https://storage.azure.com/')



### <a id="instance"></a>Azure Machine Learning compute instance

[Azure Machine Learning compute instance](concept-compute-instance.md) is a managed-compute infrastructure that allows you to easily create a single VM. The compute is created within your workspace region, but unlike a compute cluster, an instance cannot be shared with other users in your workspace. Also the instance does not automatically scale down.  You must stop the resource to prevent ongoing charges.

A compute instance can run multiple jobs in parallel and has a job queue. 

Compute instances can run jobs securely in a [virtual network environment](how-to-enable-virtual-network.md#compute-instance), without requiring enterprises to open up SSH ports. The job executes in a containerized environment and packages your model dependencies in a Docker container. 

1. **Create and attach**: 
    
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

1. **Configuração**: Criar uma configuração de execução.
    
    ```python
    
    from azureml.core import ScriptRunConfig
    from azureml.core.runconfig import DEFAULT_CPU_IMAGE
    
    src = ScriptRunConfig(source_directory='', script='train.py')
    
    # Set compute target to the one created in previous step
    src.run_config.target = instance
    
    # Set environment
    src.run_config.environment = myenv
     
    run = experiment.submit(config=src)
    ```

Agora que ligou o cálculo e configura o seu percurso, o próximo passo é [submeter a corrida de treino.](#submit)


### <a name="remote-virtual-machines"></a><a id="vm"></a>Máquinas virtuais remotas

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

   Ou pode anexar o DSVM ao seu espaço de trabalho [utilizando o estúdio Azure Machine Learning](#portal-reuse).

1. **Configuração**: Crie uma configuração de execução para o alvo de computação DSVM. Docker e conda são usados para criar e configurar o ambiente de treino no DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Agora que ligou o cálculo e configura o seu percurso, o próximo passo é [submeter a corrida de treino.](#submit)

### <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

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

   Ou pode anexar o cluster HDInsight ao seu espaço de trabalho utilizando o [estúdio Azure Machine Learning](#portal-reuse).

1. **Configuração**: Crie uma configuração de execução para o alvo do cálculo HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Agora que ligou o cálculo e configura o seu percurso, o próximo passo é [submeter a corrida de treino.](#submit)


### <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

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
> Os oleodutos Azure Machine Learning só podem funcionar com dados armazenados na loja de dados predefinida da conta Data Lake Analytics. Se os dados com os qual precisa trabalhar estiverem numa loja não padrão, pode utilizar um [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) para copiar os dados antes do treino.

## <a name="set-up-in-azure-machine-learning-studio"></a>Instale-se no estúdio Azure Machine Learning

Pode aceder aos alvos de computação que estão associados ao seu espaço de trabalho no estúdio Azure Machine Learning.  Pode usar o estúdio para:

* [Ver metas de cálculo](#portal-view) anexadas ao seu espaço de trabalho
* [Crie um alvo computacional](#portal-create) no seu espaço de trabalho
* [Anexar um alvo computacional](#portal-reuse) que foi criado fora do espaço de trabalho


Depois de um alvo ser criado e ligado ao seu espaço de trabalho, irá usá-lo na sua configuração de execução com um `ComputeTarget` objeto: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a name="view-compute-targets"></a><a id="portal-view"></a>Ver metas de cálculo


Para ver os alvos de cálculo para o seu espaço de trabalho, utilize os seguintes passos:

1. Navegue para [o estúdio Azure Machine Learning.](https://ml.azure.com)
 
1. Em __Aplicações__, __selecione Compute__.

    [![Ver separador computacional](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a name="create-a-compute-target"></a><a id="portal-create"></a>Criar um alvo de computação

Siga os passos anteriores para ver a lista de alvos de computação. Em seguida, use estes passos para criar um alvo de computação: 

1. Selecione o sinal de mais (+) para adicionar um alvo de cálculo.

    ![Adicione um alvo de cálculo](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Insira um nome para o alvo do cálculo. 

1. Selecione **Machine Learning Compute** como o tipo de computação a utilizar para __o Treino__. 

    >[!NOTE]
    >Azure Machine Learning Compute é o único recurso gerido que pode criar no estúdio Azure Machine Learning.  Todos os outros recursos computacional podem ser anexados após a sua criação.

1. Preencha o formulário. Fornecer valores para as propriedades necessárias, especialmente **VM Family,** e os **nós máximos** para usar para girar o cálculo.  

1. Selecione __Criar__.


1. Ver o estado da operação de criação selecionando o alvo do cálculo na lista:

    ![Selecione um alvo de cálculo para visualizar o estado de operação de criação](./media/how-to-set-up-training-targets/View_list.png)

1. Em seguida, veja os detalhes para o alvo do cálculo: 

    ![Ver os detalhes do alvo do computador](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a name="attach-compute-targets"></a><a id="portal-reuse"></a>Anexar metas de cálculo

Para utilizar alvos de cálculo criados fora do espaço de trabalho Azure Machine Learning, deve anexá-los. A fixação de um alvo de computação torna-o disponível para o seu espaço de trabalho.

Siga os passos descritos anteriormente para ver a lista de alvos de cálculo. Em seguida, utilize os seguintes passos para anexar um alvo de cálculo: 

1. Selecione o sinal de mais (+) para adicionar um alvo de cálculo. 
1. Insira um nome para o alvo do cálculo. 
1. Selecione o tipo de cálculo a anexar para __a Formação:__

    > [!IMPORTANT]
    > Nem todos os tipos de cálculo podem ser ligados a partir do estúdio Azure Machine Learning. Os tipos de cálculo que podem ser atualmente ligados para a formação incluem:
    >
    > * Um VM remoto
    > * Azure Databricks (para utilização em gasodutos de aprendizagem automática)
    > * Azure Data Lake Analytics (para utilização em gasodutos de aprendizagem automática)
    > * Azure HDInsight

1. Preencha o formulário e forneça valores para as propriedades necessárias.

    > [!NOTE]
    > A Microsoft recomenda que utilize as chaves SSH, que são mais seguras do que as palavras-passe. As palavras-passe são vulneráveis a ataques de força bruta. As chaves SSH dependem de assinaturas criptográficas. Para obter informações sobre como criar chaves SSH para utilização com Máquinas Virtuais Azure, consulte os seguintes documentos:
    >
    > * [Criar e utilizar chaves SSH no Linux ou no macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Criar e utilizar chaves SSH no Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. __Selecione Anexar__. 
1. Consulte o estado da operação da anexação selecionando o alvo do cálculo da lista.

## <a name="set-up-with-cli"></a>Configurar com CLI

Pode aceder aos alvos de cálculo que estão associados ao seu espaço de trabalho utilizando a [extensão CLI](reference-azure-machine-learning-cli.md) para Azure Machine Learning.  Pode utilizar o CLI para:

* Criar um alvo de computação gerido
* Atualizar um alvo de computação gerido
* Anexar um alvo de computação não gerido

Para mais informações, consulte [a gestão de Recursos.](reference-azure-machine-learning-cli.md#resource-management)

## <a name="set-up-with-vs-code"></a>Configurar com código VS

Pode aceder, criar e gerir os alvos de computação que estão associados ao seu espaço de trabalho utilizando a [extensão](how-to-manage-resources-vscode.md#compute-clusters) do Código VS para Azure Machine Learning.

## <a name="submit-training-run-using-azure-machine-learning-sdk"></a><a id="submit"></a>Submeter a formação usando Azure Machine Learning SDK

Depois de criar uma configuração de execução, use-a para executar a sua experiência.  O código padrão para submeter uma corrida de treino é o mesmo para todos os tipos de metas de computação:

1. Criar uma experiência para correr
1. Submeta a corrida.
1. Espere que a corrida termine.

> [!IMPORTANT]
> Quando submete a formação, uma imagem do diretório que contém os seus scripts de treino é criada e enviada para o alvo do cálculo. Também é armazenado como parte da experiência no seu espaço de trabalho. Se alterar ficheiros e submeter a execução novamente, apenas os ficheiros alterados serão carregados.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Para mais informações, consulte [Snapshots](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Criar uma experimentação

Primeiro, crie uma experiência no seu espaço de trabalho.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Submeter a experimentação

Submeta a experiência com um `ScriptRunConfig` objeto.  Este objeto inclui:

* **source_directory**: O diretório de origem que contém o seu roteiro de treino
* **Script**: Identifique o roteiro de treino
* **run_config**: A configuração de execução, que por sua vez define onde o treino ocorrerá.

Por exemplo, para utilizar a configuração [do alvo local:](#local)

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Altere a mesma experiência para executar num alvo de computação diferente utilizando uma configuração de execução diferente, como o [alvo amlcompute](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> Este exemplo não permite apenas utilizar um nó do alvo do cálculo para o treino. Para utilizar mais do que um nó, desa um conjunto `node_count` da configuração de execução para o número de nós pretendido. Por exemplo, o seguinte código define o número de nós utilizados para a formação para quatro:
>
> ```python
> src.run_config.node_count = 4
> ```

Ou pode:

* Submeta a experiência com um `Estimator` objeto como mostrado nos [modelos de ML do comboio com estimadores](how-to-train-ml-models.md).
* Submeta uma execução HyperDrive para [sintonização hiperparítnica](how-to-tune-hyperparameters.md).
* Submeter uma experiência através da extensão do [Código VS.](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)

Para obter mais informações, consulte a documentação [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) e [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Crie configuração de execução e submeta execução usando O CLI de Aprendizagem de Máquinas Azure

Pode utilizar a [extensão CLI](reference-azure-machine-learning-cli.md) do [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e machine learning CLI para criar configurações de execução e submeter corridas em diferentes alvos de computação. Os exemplos a seguir assumem que tem um espaço de trabalho de aprendizagem de máquinas Azure existente e que iniciou sessão no Azure usando `az login` o comando CLI. 

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

### <a name="create-run-configuration"></a>Criar configuração de execução

A forma mais simples de criar configuração de execução é navegar na pasta que contém os scripts Python de aprendizagem de máquinas e usar o comando CLI

```azurecli
az ml folder attach
```

Este comando cria uma sub-segmentação `.azureml` que contém ficheiros de configuração de execução de modelos para diferentes alvos de computação. Pode copiar e editar estes ficheiros para personalizar a sua configuração, por exemplo, para adicionar pacotes Python ou alterar as definições do Docker.  

### <a name="structure-of-run-configuration-file"></a>Estrutura do ficheiro de configuração de execução

O ficheiro de configuração de execução é formatado YAML, com secções seguintes
 * O guião a ser executado e os seus argumentos
 * Computação nome-alvo, ou "local" ou nome de um cálculo sob o espaço de trabalho.
 * Parâmetros para a execução: quadro, comunicador para execuções distribuídas, duração máxima e número de nós de computação.
 * Secção ambiental. Consulte [Criar e gerir ambientes de formação e implantação](how-to-use-environments.md) para mais detalhes sobre os campos nesta secção.
   * Para especificar as embalagens Python para instalar para a execução, crie [um ficheiro ambiente conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)e desconfifique o campo __CondaDependenciesFile.__
 * Execute detalhes do histórico para especificar a pasta do ficheiro de registo e para ativar ou desativar a recolha de saída e executar instantâneos de histórico.
 * Detalhes de configuração específicos da estrutura selecionada.
 * Dados de referência e dados de loja de dados.
 * Detalhes de configuração específicos para Machine Learning Compute para criar um novo cluster.

Veja o [ficheiro JSON de](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) exemplo para um esquema de configuração completa.

### <a name="create-an-experiment"></a>Criar uma experimentação

Primeiro, crie uma experiência para as suas corridas

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Execução de script

Para submeter uma execução de script, execute um comando

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>Corrida hyperDrive

Pode utilizar o HyperDrive com O Azure CLI para executar ensaios de afinação de parâmetros. Em primeiro lugar, crie um ficheiro de configuração HyperDrive no seguinte formato. Consulte [hiperparmetros sintonizadores para o seu](how-to-tune-hyperparameters.md) artigo de modelo para obter detalhes sobre parâmetros de afinação do hiperparaímetro.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Adicione este ficheiro ao lado dos ficheiros de configuração de execução. Em seguida, envie uma corrida HyperDrive utilizando:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Note a secção *de argumentos* no espaço runconfig e *parâmetros* em HyperDrive config. Eles contêm os argumentos da linha de comando para serem passados para o guião de treino. O valor em runconfig permanece o mesmo para cada iteração, enquanto a gama em HyperDrive config é iterada. Não especifique o mesmo argumento em ambos os ficheiros.

Para mais detalhes sobre estes ```az ml``` comandos CLI, consulte [a documentação de referência](reference-azure-machine-learning-cli.md).

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Rastreio e integração de Git

Quando se inicia uma corrida de treinamento onde o diretório de origem é um repositório local de Git, a informação sobre o repositório é armazenada na história da execução. Para obter mais informações, consulte [a integração do Git para a Azure Machine Learning.](concept-train-model-git-integration.md)

## <a name="notebook-examples"></a>Exemplos de cadernos

Consulte estes cadernos para exemplos de formação com vários alvos de computação:
* [como usar-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutoriais/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md) usa um alvo de computação gerido para treinar um modelo.
* Aprenda a [sintonizar eficientemente os hiperparmetros](how-to-tune-hyperparameters.md) para construir modelos melhores.
* Uma vez treinado, aprenda [como e onde implementar modelos.](how-to-deploy-and-where.md)
* Consulte a referência [SDK da classe RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py)
* [Use a azure machine learning com redes virtuais Azure](how-to-enable-virtual-network.md)
