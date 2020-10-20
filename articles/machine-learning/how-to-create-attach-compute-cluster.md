---
title: Criar clusters de computação
titleSuffix: Azure Machine Learning
description: Aprenda a criar clusters de computação no seu espaço de trabalho Azure Machine Learning. Utilize o cluster de cálculo como alvo de computação para treino ou inferência.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 56ab5ba93545ffdbfd36850c08eda78cc239f694
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207126"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>Criar um cluster de cálculo de aprendizagem automática Azure

Aprenda a criar e gerir um [cluster de cálculo](concept-compute-target.md#azure-machine-learning-compute-managed) no seu espaço de trabalho Azure Machine Learning.

Você pode usar o cluster de cálculo Azure Machine Learning para distribuir um processo de inferência de treino ou lote através de um conjunto de nós de cálculo cpu ou GPU na nuvem. Para obter mais informações sobre os tamanhos de VM que incluem GPUs, consulte [os tamanhos de máquinas virtuais otimizadas pela GPU.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) 

Neste artigo, saiba como:

* Criar um cluster computacional
* Reduza o custo do seu cluster de cálculo
* Criar uma [identidade gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para o cluster

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](how-to-manage-workspace.md)

* A [extensão Azure CLI para o serviço de aprendizagem automática](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)ou a extensão do [Código do Estúdio Visual Azure Machine Learning.](tutorial-setup-vscode-extension.md)

## <a name="what-is-a-compute-cluster"></a>O que é um cluster computacional?

O cluster compute Azure Machine Learning é uma infraestrutura de computação gerida que permite criar facilmente um único ou multi-nó compute. O cálculo é criado dentro da sua região do espaço de trabalho como um recurso que pode ser partilhado com outros utilizadores no seu espaço de trabalho. O cálculo aumenta automaticamente quando um trabalho é submetido e pode ser colocado numa Rede Virtual Azure. O cálculo executa num ambiente contentorizado e embala as dependências do seu modelo num [recipiente Docker.](https://www.docker.com/why-docker)

Os clusters computativos podem executar empregos de forma segura num [ambiente de rede virtual,](how-to-secure-training-vnet.md)sem exigir que as empresas abram portas SSH. O trabalho executa num ambiente contentorizado e embala as dependências do seu modelo num contentor Docker. 

## <a name="limitations"></a>Limitações

* **Não crie anexos múltiplos e simultâneos ao mesmo cálculo** do seu espaço de trabalho. Por exemplo, anexar um cluster de cálculo a um espaço de trabalho utilizando dois nomes diferentes. Cada novo acessório quebrará os acessórios existentes anteriores.

    Se pretender voltar a anexar um alvo de cálculo, por exemplo para alterar as definições de configuração do cluster, tem primeiro de remover o acessório existente.

* Alguns dos cenários listados neste documento são marcados como __pré-visualização__. A funcionalidade de pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* O Azure Machine Learning Compute tem limites predefinidos, como o número de núcleos que podem ser atribuídos. Para obter mais informações, consulte [Gerir e solicitar quotas para recursos Azure.](how-to-manage-quotas.md)

* O Azure permite-lhe colocar _fechaduras_ nos recursos, para que não possam ser apagados ou lidos apenas. __Não aplique bloqueios de recursos ao grupo de recursos que contém o seu espaço de trabalho__. A aplicação de um bloqueio ao grupo de recursos que contém o seu espaço de trabalho impedirá operações de escala para clusters de computação Azure ML. Para obter mais informações sobre os recursos de bloqueio, consulte [os recursos de bloqueio para evitar alterações inesperadas](../azure-resource-manager/management/lock-resources.md).

> [!TIP]
> Os clusters podem geralmente escalar até 100 nós, desde que tenha quota suficiente para o número de núcleos necessários. Por predefinição, os clusters são configurados com comunicação inter-nódoa ativada entre os nós do cluster para apoiar empregos de MPI, por exemplo. No entanto, pode escalar os seus clusters para 1000s de nós simplesmente [levantando um bilhete](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)de apoio, e solicitando para permitir a lista da sua subscrição, ou espaço de trabalho, ou um cluster específico para desativar a comunicação inter-nól. 


## <a name="create"></a>Criar

**Estimativa de tempo:** Aproximadamente 5 minutos.

O Azure Machine Learning Compute pode ser reutilizado através de corridas. O cálculo pode ser partilhado com outros utilizadores no espaço de trabalho e é mantido entre corridas, escalando automaticamente os nós para cima ou para baixo com base no número de runs submetidos, e o max_nodes definido no seu cluster. A definição min_nodes controla os nós mínimos disponíveis.

Os núcleos dedicados por região por quota familiar VM e quotas regionais totais, que se aplicam à criação de clusters de computação, são unificados e partilhados com a quota de caso de formação em aprendizagem automática Azure. 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

A computação escala automaticamente para zero nós quando não é usada.   VMs dedicados são criados para executar os seus trabalhos conforme necessário.
    
# <a name="python"></a>[Python](#tab/python)

Para criar um recurso persistente de Azure Machine Learning Compute em Python, especifique as propriedades **vm_size** e **max_nodes.** A Azure Machine Learning utiliza então falhas inteligentes para as outras propriedades. 
    
* **vm_size**: A família VM dos nós criados pela Azure Machine Learning Compute.
* **max_nodes**: O número máximo de nós para autoescalar até quando você tem um trabalho no Azure Machine Learning Compute.


[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

Também pode configurar várias propriedades avançadas quando criar o Azure Machine Learning Compute. As propriedades permitem criar um cluster persistente de tamanho fixo, ou dentro de uma Rede Virtual Azure existente na sua subscrição.  Consulte a [aula AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py&preserve-view=true) para mais detalhes.


# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
```

Para obter mais informações, consulte [o az ml computetarget criar amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute&preserve-view=true).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Para obter informações sobre a criação de um cluster de computação no estúdio, consulte [Crie metas de computação no estúdio Azure Machine Learning](how-to-create-attach-compute-studio.md#amlcompute).

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Reduza o custo do seu cluster de cálculo

Também pode optar por utilizar [VMs de baixa prioridade](concept-plan-manage-cost.md#low-pri-vm) para executar algumas ou todas as suas cargas de trabalho. Estes VMs não têm disponibilidade garantida e podem ser antecipados durante a utilização. Um trabalho preventivo é reiniciado, não retomado. 

Utilize qualquer uma destas formas de especificar um VM de baixa prioridade:
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Definir o `vm-priority` :
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[Studio](#tab/azure-studio)

No estúdio, escolha **Low Priority** quando criar um VM.

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Configurar identidade gerida

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* Configure a identidade gerida na sua configuração de provisionamento:  

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

* Adicionar identidade gerida a um cluster de computação existente 
    
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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

* Criar um novo cluster de computação gerido com identidade gerida

  * Identidade gerida atribuída pelo utilizador

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * Identidade gerida atribuída pelo sistema

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* Adicione uma identidade gerida a um cluster existente:

    * Identidade gerida atribuída pelo utilizador
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * Identidade gerida atribuída pelo sistema

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[Studio](#tab/azure-studio)

Ver [Configurar identidade gerida em estúdio](how-to-create-attach-compute-studio.md#managed-identity).

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>Utilização de identidade gerida

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="next-steps"></a>Passos seguintes

Use o seu cluster de cálculo para:

* [Submeter uma corrida de formação](how-to-set-up-training-targets.md) 
* [Executar inferência de lote](how-to-use-parallel-run-step.md).