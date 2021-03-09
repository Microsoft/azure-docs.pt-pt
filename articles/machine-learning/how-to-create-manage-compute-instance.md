---
title: Criar e gerir uma instância computacional
titleSuffix: Azure Machine Learning
description: Aprenda a criar e gerir um caso de cálculo de Aprendizagem automática Azure. Use como ambiente de desenvolvimento, ou como alvo de cálculo para fins de dev/teste.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 5aaaf2000fb8310a5208447f8eaf8546feda90fb
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509324"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>Criar e gerir um exemplo de cálculo de aprendizagem automática Azure

Aprenda a criar e gerir uma [instância de computação](concept-compute-instance.md) no seu espaço de trabalho Azure Machine Learning.

Use uma instância computacional como o seu ambiente de desenvolvimento totalmente configurado e gerido na nuvem. Para o desenvolvimento e testes, também pode usar o exemplo como [alvo de computação de formação](concept-compute-target.md#train) ou para um [alvo de inferência](concept-compute-target.md#deploy).   Uma instância computacional pode executar vários trabalhos em paralelo e tem uma fila de emprego. Como ambiente de desenvolvimento, uma instância de computação não pode ser partilhada com outros utilizadores no seu espaço de trabalho.

Neste artigo, vai aprender a:

* Criar uma instância de computação 
* Gerir (iniciar, parar, reiniciar, excluir) uma instância computacional
* Aceda à janela do terminal 
* Instalar pacotes R ou Python
* Criar novos ambientes ou núcleos jupyter

As instâncias computacional podem executar empregos de forma segura num [ambiente de rede virtual,](how-to-secure-training-vnet.md)sem exigir que as empresas abram portas SSH. O trabalho executa num ambiente contentorizado e embala as dependências do seu modelo num contentor Docker. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](how-to-manage-workspace.md)

* A [extensão Azure CLI para o serviço de aprendizagem automática](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK,](/python/api/overview/azure/ml/intro)ou a extensão do [Código do Estúdio Visual Azure Machine Learning.](tutorial-setup-vscode-extension.md)

## <a name="create"></a>Criar

**Estimativa de tempo:** Aproximadamente 5 minutos.

Criar uma instância computacional é um processo único para o seu espaço de trabalho. Pode reutilizar o cálculo como uma estação de trabalho de desenvolvimento ou como um alvo de computação para a formação. Pode ter várias instâncias de computação anexadas ao seu espaço de trabalho.

Os núcleos dedicados por região por quota familiar VM e quotas regionais totais, que se aplicam à criação de instâncias computacional, são unificados e partilhados com a quota de cluster de formação de aprendizagem de máquinas de azure. Parar a instância de computação não liberta quota para garantir que poderá reiniciar a instância do cálculo. Note que não é possível alterar o tamanho virtual da máquina de instância computacional uma vez que é criado.

O exemplo a seguir demonstra como criar uma instância computacional:

# <a name="python"></a>[Python](#tab/python)

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

Para obter mais informações sobre as classes, métodos e parâmetros utilizados neste exemplo, consulte os seguintes documentos de referência:

* [Aula de Computação](/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computeinstance(class)#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

Para obter mais informações, consulte o [az ml computetarget criar referência de computação.](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext_azure_cli_ml_az_ml_computetarget_create_computeinstance)

# <a name="studio"></a>[Studio](#tab/azure-studio)

No seu espaço de trabalho no estúdio Azure Machine Learning, crie uma nova instância de computação a partir da secção **Compute** ou na secção **Notebooks** quando estiver pronto para executar um dos seus cadernos.

Para obter informações sobre a criação de uma instância computacional no estúdio, consulte [Crie metas de computação no estúdio Azure Machine Learning](how-to-create-attach-compute-studio.md#compute-instance).

---

Também pode criar uma instância de computação com um [modelo de Gestor de Recursos Azure.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance) 

### <a name="create-on-behalf-of-preview"></a>Criar em nome de (pré-visualização)

Como administrador, pode criar uma instância computacional em nome de um cientista de dados e atribuir-lhes a instância com:
* [Modelo de Gestor de Recursos Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).  Para obter mais informações sobre como encontrar o TenantID e o ObjectID necessários neste modelo, consulte [encontrar iDs de objeto de identidade para configuração de autenticação](../healthcare-apis/find-identity-object-ids.md).  Pode também encontrar estes valores no portal Azure Ative Directory.
* API REST

O cientista de dados que cria o caso de computação para necessidades são permissões de [controlo de acesso baseado em funções Azure (Azure RBAC):](../role-based-access-control/overview.md) 
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action*

O cientista de dados pode começar, parar e reiniciar a instância de computação. Podem usar a instância computacional para:
* Jupyter
* JupyterLab
* RStudio
* Cadernos integrados

## <a name="manage"></a>Gerir

Iniciar, parar, reiniciar e apagar uma instância de computação. Uma instância de cálculo não diminui automaticamente, por isso certifique-se de parar o recurso para evitar cargas em curso.

# <a name="python"></a>[Python](#tab/python)

Nos exemplos abaixo, o nome da instância computacional é **exemplo**

* Obter estado

    ```python
    # get_status() gets the latest status of the ComputeInstance target
    instance.get_status()
    ```

* Parar

    ```python
    # stop() is used to stop the ComputeInstance
    # Stopping ComputeInstance will stop the billing meter and persist the state on the disk.
    # Available Quota will not be changed with this operation.
    instance.stop(wait_for_completion=True, show_output=True)
    ```

* Iniciar

    ```python
    # start() is used to start the ComputeInstance if it is in stopped state
    instance.start(wait_for_completion=True, show_output=True)
    ```

* Reiniciar

    ```python
    # restart() is used to restart the ComputeInstance
    instance.restart(wait_for_completion=True, show_output=True)
    ```

* Eliminar

    ```python
    # delete() is used to delete the ComputeInstance target. Useful if you want to re-use the compute name 
    instance.delete(wait_for_completion=True, show_output=True)
    ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Nos exemplos abaixo, o nome da instância computacional é **exemplo**

* Parar

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n instance -v
    ```

    Para obter mais informações, consulte [a az ml computetarget stop computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop).

* Iniciar 

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    Para obter mais informações, consulte [a az ml computetarget start computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start).

* Reiniciar 

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    Para obter mais informações, consulte [a az ml computetarget restart computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart).

* Eliminar

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    Para obter mais informações, consulte [az ml computetarget eliminar a computação](/cli/azure/ext/azure-cli-ml/ml/computetarget#ext-azure-cli-ml-az-ml-computetarget-delete).

# <a name="studio"></a>[Studio](#tab/azure-studio)

No seu espaço de trabalho no estúdio Azure Machine Learning, selecione **Compute** e **selecione Compute Instance** no topo.

![Gerir uma instância computacional](./media/concept-compute-instance/manage-compute-instance.png)

Pode realizar as seguintes ações:

* Criar uma nova instância computacional 
* Refresque o separador de instâncias computacional.
* Comece, pare e reinicie uma instância de computação.  Pagas pelo exemplo sempre que está a funcionar. Pare a instância de cálculo quando não estiver a usá-lo para reduzir o custo. Parar um caso de computação o impede. Então, comece de novo quando precisar.
* Apagar uma instância computacional.
* Filtre a lista de casos de computação para mostrar apenas aqueles que criou.

Para cada instância de cálculo no seu espaço de trabalho que criou (ou que foi criada para si), pode:

* Acesso Jupyter, JupyterLab, RStudio na instância compute compute
* SSH em caso de computação. O acesso ao SSH é desativado por padrão, mas pode ser ativado no tempo de criação de instâncias de computação. O acesso ao SSH é através de um mecanismo chave público/privado. O separador irá dar-lhe detalhes para a ligação SSH, como endereço IP, nome de utilizador e número de porta.
* Obtenha detalhes sobre uma instância computacional específica, como endereço IP e região.

---

[O Azure RBAC](../role-based-access-control/overview.md) permite-lhe controlar quais os utilizadores no espaço de trabalho que podem criar, eliminar, iniciar, parar, reiniciar uma instância de computação. Todos os utilizadores no espaço de trabalho colaborador e papel de proprietário podem criar, eliminar, iniciar, parar e reiniciar casos de cálculo em todo o espaço de trabalho. No entanto, apenas o criador de uma instância computacional específica, ou o utilizador designado se foi criado em seu nome, é autorizado a aceder a Jupyter, JupyterLab e RStudio nessa instância de computação. Uma instância computacional é dedicada a um único utilizador que tenha acesso à raiz, e pode terminalizar através do Jupyter/JupyterLab/RStudio. A instância computacional terá um único utilizador a iniciar sessão e todas as ações utilizarão a identidade desse utilizador para o Azure RBAC e a atribuição de execuções de experiências. O acesso ao SSH é controlado através de um mecanismo chave público/privado.

Estas ações podem ser controladas pela Azure RBAC:
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*

## <a name="next-steps"></a>Passos seguintes

* [Aceda ao terminal de instância computacional](how-to-access-terminal.md)
* [Criar e gerir ficheiros](how-to-manage-files.md)
* [Submeter uma corrida de formação](how-to-set-up-training-targets.md)
