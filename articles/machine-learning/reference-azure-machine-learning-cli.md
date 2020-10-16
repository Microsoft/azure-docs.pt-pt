---
title: Instale & utilizar O CLI de Aprendizagem de Máquinas Azure
description: Aprenda a instalar e utilizar a extensão CLI de Aprendizagem de Máquinas Azure para criar e gerir recursos como o seu espaço de trabalho, datastores, conjuntos de dados, oleodutos, modelos e implementações.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 06/22/2020
ms.custom: seodec18
ms.openlocfilehash: 50ac8ede9f8272d6956d1ac29e57a9c26d2c0bd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708276"
---
# <a name="install--use-the-cli-extension-for-azure-machine-learning"></a>Instale & utilize a extensão CLI para aprendizagem automática Azure


O Azure Machine Learning CLI é uma extensão do [Azure CLI,](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true)uma interface de linha de comando transversal para a plataforma Azure. Esta extensão fornece comandos para trabalhar com a Azure Machine Learning. Permite automatizar as suas atividades de aprendizagem automática. A lista a seguir fornece algumas ações de exemplo que pode fazer com a extensão do CLI:

+ Executar experiências para criar modelos de aprendizagem automática

+ Registar modelos de aprendizagem automática para uso do cliente

+ Pacote, implemente e acompanhe o ciclo de vida dos seus modelos de aprendizagem automática

O CLI não substitui o Azure Machine Learning SDK. É uma ferramenta complementar que é otimizada para lidar com tarefas altamente parametrizadas que se adequam bem à automação.

## <a name="prerequisites"></a>Pré-requisitos

* Para utilizar o CLI, tem de ter uma assinatura Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Para utilizar os comandos CLI neste documento a partir do seu **ambiente local,** necessita do [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

    Se utilizar o [Azure Cloud Shell,](https://azure.microsoft.com//features/cloud-shell/)o CLI é acedido através do navegador e vive na nuvem.

## <a name="full-reference-docs"></a>Documentos de referência completos

Encontre os [documentos de referência completos para a extensão azure-cli-ml de Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest&preserve-view=true).

## <a name="connect-the-cli-to-your-azure-subscription"></a>Ligue o CLI à sua subscrição Azure

> [!IMPORTANT]
> Se estiver a utilizar a Azure Cloud Shell, pode saltar esta secção. A concha da nuvem autentica-o automaticamente utilizando a conta que inicia sessão na sua subscrição Azure.

Existem várias formas de autenticar a sua subscrição Azure a partir do CLI. O mais básico é autenticar interativamente usando um navegador. Para autenticar interativamente, abra uma linha de comando ou terminal e utilize o seguinte comando:

```azurecli-interactive
az login
```

Se a CLI conseguir abrir o seu browser predefinido, executa essa ação e carrega uma página de início de sessão. Caso contrário, é necessário abrir um browser e seguir as instruções na linha de comando. As instruções envolvem navegar [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e introduzir um código de autorização.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Para outros métodos de autenticação, consulte [iniciar sing com Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true).

## <a name="install-the-extension"></a>Instalar a extensão

Para instalar a extensão CLI machine learning, utilize o seguinte comando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Os ficheiros de exemplo que pode utilizar com os comandos abaixo podem ser [consultados aqui](https://aka.ms/azml-deploy-cloud).

Quando solicitado, selecione `y` para instalar a extensão.

Para verificar se a extensão foi instalada, utilize o seguinte comando para apresentar uma lista de subcommandes específicos da ML:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>Atualizar a extensão

Para atualizar a extensão do CLI de aprendizagem automática, utilize o seguinte comando:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>Remover a extensão

Para remover a extensão do CLI, utilize o seguinte comando:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Gestão de recursos

Os seguintes comandos demonstram como usar o CLI para gerir os recursos utilizados pela Azure Machine Learning.

+ Se ainda não tiver um, crie um grupo de recursos:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Criar um espaço de trabalho para aprendizagem automática Azure:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    Para mais informações, consulte [o espaço de trabalho az ml create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-create).

+ Fixe uma configuração do espaço de trabalho a uma pasta para permitir a consciência contextual do CLI.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Este comando cria uma `.azureml` subdiretória que contém ficheiros de ambiente runconfig e conda. Também contém um `config.json` ficheiro que é usado para comunicar com o seu espaço de trabalho Azure Machine Learning.

    Para obter mais informações, consulte [o az ml pasta anexa .](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-folder-attach)

+ Fixe um recipiente de bolhas Azure como uma Datastore.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Para obter mais informações, consulte [a az ml datastore attach-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Faça o upload de ficheiros para uma datastore.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Para obter mais informações, consulte [o upload da loja de dados AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-datastore-upload).

+ Anexe um cluster AKS como um alvo de computação.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Para mais informações, consulte [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

### <a name="compute-clusters"></a>Clusters computacional

+ Criar um novo cluster de computação gerido.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```



+ Criar um novo cluster de computação gerido com identidade gerida

  + Identidade gerida atribuída pelo utilizador

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  + Identidade gerida atribuída pelo sistema

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
+ Adicione uma identidade gerida a um cluster existente:

    + Identidade gerida atribuída pelo utilizador
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    + Identidade gerida atribuída pelo sistema

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

Para obter mais informações, consulte [o az ml computetarget criar amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

<a id="computeinstance"></a>

### <a name="compute-instance"></a>Instância de computação
Gerir casos de computação.  Em todos os exemplos abaixo, o nome da instância computacional é **cpu**

+ Criar uma nova computação.

    ```azurecli-interactive
    az ml computetarget create computeinstance  -n cpu -s "STANDARD_D3_V2" -v
    ```

    Para obter mais informações, consulte [o az ml computetarget criar computação](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-create-computeinstance).

+ Pare com uma computação.

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n cpu -v
    ```

    Para obter mais informações, consulte [a az ml computetarget stop computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop).

+ Inicie uma intromissão de computação.

    ```azurecli-interactive
    az ml computetarget start computeinstance -n cpu -v
    ```

    Para obter mais informações, consulte [a az ml computetarget start computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start).

+ Reinicie uma insímia computestance.

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n cpu -v
    ```

    Para obter mais informações, consulte [a az ml computetarget restart computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart).

+ Elimine uma insístance computestance.

    ```azurecli-interactive
    az ml computetarget delete -n cpu -v
    ```

    Para obter mais informações, consulte [az ml computetarget eliminar a computação](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-delete).


## <a name="run-experiments"></a><a id="experiments"></a>Executar experiências

* Comece uma série da sua experiência. Ao utilizar este comando, especifique o nome do ficheiro runconfig (o texto antes \* de .runconfig se estiver a olhar para o seu sistema de ficheiros) contra o parâmetro -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > O `az ml folder attach` comando cria uma `.azureml` subdiretório, que contém dois ficheiros runconfig de exemplo. 
    >
    > Se tiver um script Python que crie um objeto de configuração de execução programáticamente, pode utilizar [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py&preserve-view=true#&preserve-view=truesave-path-none--name-none--separate-environment-yaml-false-) para o guardar como ficheiro runconfig.
    >
    > O esquema de configuração total pode ser encontrado neste [ficheiro JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). O esquema está a auto-documentar através da `description` chave de cada objeto. Além disso, existem enums para possíveis valores, e um corte de modelo no final.

    Para obter mais informações, consulte [o roteiro de submissão az ml run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-run-submit-script).

* Ver uma lista de experiências:

    ```azurecli-interactive
    az ml experiment list
    ```

    Para mais informações, consulte [a lista de experiências az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-experiment-list).

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

## <a name="dataset-management"></a>Gestão de dataset

Os seguintes comandos demonstram como trabalhar com conjuntos de dados em Azure Machine Learning:

+ Registar um conjunto de dados:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Para obter informações sobre o formato do ficheiro JSON utilizado para definir o conjunto de dados, utilize `az ml dataset register --show-template` .

    Para obter mais informações, consulte [o registo de conjuntos de dados az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-dataset-register).

+ Listar todos os conjuntos de dados num espaço de trabalho:

    ```azurecli-interactive
    az ml dataset list
    ```

    Para obter mais informações, consulte [a lista de conjuntos de dados az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-dataset-list).

+ Obtenha detalhes de um conjunto de dados:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Para obter mais informações, consulte [o conjunto de dados az ml .](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-dataset-show)

+ Não registar um conjunto de dados:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Para obter mais informações, consulte [o conjunto de dados az ml não registador](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>Gestão do ambiente

Os seguintes comandos demonstram como criar, registar e listar [ambientes](how-to-configure-environment.md) de aprendizagem automática Azure para o seu espaço de trabalho:

+ Criar ficheiros de andaimes para um ambiente:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Para mais informações, consulte [o andaime ambiente az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-environment-scaffold)

+ Registar um ambiente:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Para mais informações, consulte [o registo ambiental az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-environment-register)

+ Listar ambientes registados:

    ```azurecli-interactive
    az ml environment list
    ```

    Para mais informações, consulte [a lista de ambientes az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-environment-list).

+ Faça o download de um ambiente registado:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Para mais informações, consulte [o download do ambiente az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-environment-download)

### <a name="environment-configuration-schema"></a>Esquema de configuração do ambiente

Se usou o `az ml environment scaffold` comando, gera um ficheiro de modelo `azureml_environment.json` que pode ser modificado e usado para criar configurações de ambiente personalizadas com o CLI. O objeto de nível superior mapeia vagamente para a [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true) classe no Python SDK. 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

A tabela seguinte detalha cada campo de alto nível no ficheiro JSON, é tipo, e uma descrição. Se um tipo de objeto estiver ligado a uma classe do Python SDK, há uma correspondência solta de 1:1 entre cada campo JSON e o nome variável público na classe Python. Em alguns casos, o campo pode mapear para um argumento de construtor em vez de uma variável de classe. Por exemplo, o `environmentVariables` campo mapeia para a `environment_variables` variável na [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true) classe.

| Campo JSON | Tipo | Descrição |
|---|---|---|
| `name` | `string` | O nome do ambiente. Não comece o nome com **Microsoft** ou **AzureML**. |
| `version` | `string` | Versão do ambiente. |
| `environmentVariables` | `{string: string}` | Um mapa de haxixe de nomes e valores variáveis ambientais. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py&preserve-view=true)chapéu define o ambiente python e intérprete para usar no recurso de cálculo alvo. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py&preserve-view=true) | Define definições para personalizar a imagem do Docker construída de acordo com as especificações do ambiente. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py&preserve-view=true) | A secção configura as definições de faísca. Só é utilizado quando a estrutura é definida para PySpark. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py&preserve-view=true) | Configura as dependências da biblioteca Databricks. |
| `inferencingStackVersion` | `string` | Especifica a versão da pilha de inferenculação adicionada à imagem. Para evitar a adição de uma pilha de inferenculação, deixe este campo `null` . Valor válido: "mais recente". |

## <a name="ml-pipeline-management"></a>Gestão de gasodutos ML

Os seguintes comandos demonstram como trabalhar com os oleodutos de aprendizagem automática:

+ Criar um oleoduto de aprendizagem automática:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Para obter mais informações, consulte [a az ml pipeline create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-pipeline-create).

    Para obter mais informações sobre o ficheiro YAML do gasoduto, consulte [os gasodutos de aprendizagem automática definidos em YAML](reference-pipeline-yaml.md).

+ Executar um oleoduto:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Para obter mais informações, consulte [o az ml run submit-pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Para obter mais informações sobre o ficheiro YAML do gasoduto, consulte [os gasodutos de aprendizagem automática definidos em YAML](reference-pipeline-yaml.md).

+ Agendar um oleoduto:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Para obter mais informações, consulte [o crono de criação do gasoduto AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Para obter mais informações sobre o ficheiro YAML do programa de [gasodutos, consulte os oleodutos de aprendizagem automática de definição em YAML](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Registo de modelos, perfis, implantação

Os seguintes comandos demonstram como registar um modelo treinado e, em seguida, implantá-lo como serviço de produção:

+ Registe um modelo com Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Para mais informações, consulte [o registo do modelo AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-register).

+ **OPCIONAL** Perfile o seu modelo para obter os valores de CPU e memória ideais para a implantação.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Para mais informações, consulte [o perfil do modelo az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-profile).

+ Implemente o seu modelo para AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Para obter mais informações sobre o esquema de ficheiro de configuração de [inferência, consulte o esquema de configuração de inferência](#inferenceconfig).
    
    Para obter mais informações sobre o esquema de ficheiros de configuração de configuração, consulte [o esquema de configuração de implementação](#deploymentconfig).

    Para obter mais informações, consulte [o modelo az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-deploy)

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Esquema de configuração de inferência

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Esquema de configuração de implementação

### <a name="local-deployment-configuration-schema"></a>Esquema de configuração de implementação local

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Esquema de configuração de configuração de configuração de configuração de instância de dados de azure 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Esquema de configuração de implementação do Serviço Azure Kubernetes

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Passos seguintes

* [Referência de comando para a extensão CLI de aprendizagem automática](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest&preserve-view=true).

* [Treine e implemente modelos de aprendizagem automática usando gasodutos Azure](/azure/devops/pipelines/targets/azure-machine-learning)
