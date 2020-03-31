---
title: Extensão CLI
titleSuffix: Azure Machine Learning
description: Conheça a extensão CLI de aprendizagem automática Azure para o Azure CLI. O Azure CLI é um utilitário de linha de comando transversal que lhe permite trabalhar com recursos na nuvem Azure. A extensão machine learning permite-lhe trabalhar com o Azure Machine Learning. O ML CLI cria e gere recursos como o seu espaço de trabalho, datastores, conjuntos de dados, oleodutos, modelos e implementações.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 471b26ebc4bd4aecb814ec43c7eba56e3d764fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402485"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>Utilize a extensão CLI para aprendizagem automática azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

O Azure Machine Learning CLI é uma extensão do [Azure CLI,](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)uma interface de linha de comando transversal para a plataforma Azure. Esta extensão fornece comandos para trabalhar com o Azure Machine Learning. Permite automatizar as suas atividades de aprendizagem automática. A lista que se segue fornece algumas ações de exemplo que pode fazer com a extensão CLI:

+ Realizar experiências para criar modelos de aprendizagem automática

+ Registe modelos de aprendizagem automática para uso do cliente

+ Pacote, implementação e rastreio do ciclo de vida dos seus modelos de aprendizagem automática

O CLI não substitui o Azure Machine Learning SDK. É uma ferramenta complementar que é otimizada para lidar com tarefas altamente parametrizadas que se adequam bem à automação.

## <a name="prerequisites"></a>Pré-requisitos

* Para utilizar o CLI, deve ter uma assinatura Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* O [Azure CLI.](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

## <a name="full-reference-docs"></a>Docs de referência completos

Encontre os [docs de referência completos para a extensão azure-cli-ml do Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Instalar a extensão

Para instalar a extensão CLI de Aprendizagem automática, utilize o seguinte comando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Exemplo de ficheiros que pode utilizar com os comandos abaixo podem ser encontrados [aqui](https://aka.ms/azml-deploy-cloud).

Quando solicitado, `y` selecione para instalar a extensão.

Para verificar se a extensão foi instalada, utilize o seguinte comando para apresentar uma lista de subcomandos específicos do ML:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>Atualizar a extensão

Para atualizar a extensão CLI de Aprendizagem automática, utilize o seguinte comando:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>Remover a extensão

Para remover a extensão CLI, utilize o seguinte comando:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Gestão de recursos

Os seguintes comandos demonstram como usar o CLI para gerir os recursos utilizados pela Azure Machine Learning.

+ Se ainda não tem um, crie um grupo de recursos:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Criar um espaço de trabalho azure machine learning:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > Este comando cria um espaço de trabalho de edição básica. Para criar um espaço de `--sku enterprise` trabalho `az ml workspace create` empresarial, utilize o interruptor com o comando. Para mais informações sobre as edições de Machine Learning Azure, consulte [o que é o Azure Machine Learning.](overview-what-is-azure-ml.md#sku)

    Para mais informações, consulte [az ml workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Fixe uma configuração do espaço de trabalho a uma pasta para permitir a consciência contextual cli.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Este comando `.azureml` cria um subdiretório que contém ficheiros de ambiente runconfig e conda exemplo. Também contém `config.json` um ficheiro que é usado para comunicar com o seu espaço de trabalho Azure Machine Learning.

    Para mais informações, consulte a anexação da [pasta Az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Fixe um recipiente de blob Azure como uma loja de dados.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Para mais informações, consulte [az ml datastore attach-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Faça upload de ficheiros para uma loja de dados.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Para mais informações, consulte o upload da [az ml datastore](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload).

+ Fixe um cluster AKS como um Alvo computacional.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Para mais informações, consulte [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Crie um novo alvo AMLcompute.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Para mais informações, consulte [az ml computetarget criar amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a name="run-experiments"></a><a id="experiments"></a>Executar experiências

* Comece uma corrida da sua experiência. Ao utilizar este comando, especifique o nome \*do ficheiro runconfig (o texto antes de .runconfig se estiver a olhar para o seu sistema de ficheiros) contra o parâmetro -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > O `az ml folder attach` comando `.azureml` cria um subdiretório, que contém dois exemplos de ficheiros runconfig. 
    >
    > Se tiver um script Python que cria um objeto de configuração de execução programáticamente, pode utilizar [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) para guardá-lo como ficheiro runconfig.
    >
    > O esquema completo de runconfig pode ser encontrado neste [ficheiro JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). O esquema está a auto-documentar-se através da `description` chave de cada objeto. Além disso, existem enums para possíveis valores, e um corte de modelo no final.

    Para mais informações, consulte [az ml executar envie um sub-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Ver uma lista de experiências:

    ```azurecli-interactive
    az ml experiment list
    ```

    Para mais informações, consulte a lista de [experiências az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="dataset-management"></a>Gestão de conjuntos de dados

Os seguintes comandos demonstram como trabalhar com conjuntos de dados em Azure Machine Learning:

+ Registar um conjunto de dados:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Para obter informações sobre o formato do ficheiro JSON utilizado para definir o conjunto de dados, utilize. `az ml dataset register --show-template`

    Para mais informações, consulte o [registo de dataset az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Arquivar um conjunto de dados ativo ou depreciado:

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    Para mais informações, consulte o [arquivo az ml dataset](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Depreizar um conjunto de dados:

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    Para mais informações, consulte [az ml dataset deprecate](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Enumerar todos os conjuntos de dados num espaço de trabalho:

    ```azurecli-interactive
    az ml dataset list
    ```

    Para mais informações, consulte a lista de conjuntos de [dados az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Obtenha detalhes de um conjunto de dados:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Para mais informações, consulte [az ml dataset mostrar](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Reativar um conjunto de dados arquivado ou depreciado:

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    Para mais informações, consulte [a reativação](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)do conjunto de dados az ml .

+ Desregistar um conjunto de dados:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Para mais informações, consulte o [conjunto de dados az ml de sem registo](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>Gestão do ambiente

Os seguintes comandos demonstram como criar, registar e listar [ambientes](how-to-configure-environment.md) de Aprendizagem Automática Azure para o seu espaço de trabalho:

+ Crie ficheiros de andaimes para um ambiente:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Para mais informações, consulte um [andaime ambiente az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Registe um ambiente:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Para mais informações, consulte o [registo ambiental az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register).

+ Lista de ambientes registados:

    ```azurecli-interactive
    az ml environment list
    ```

    Para mais informações, consulte a [lista de ambiente az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list).

+ Descarregue um ambiente registado:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Para mais informações, consulte [o download do ambiente az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download)

### <a name="environment-configuration-schema"></a>Esquema de configuração do ambiente

Se usou `az ml environment scaffold` o comando, gera `azureml_environment.json` um ficheiro de modelo que pode ser modificado e usado para criar configurações ambientais personalizadas com o CLI. O objeto de nível [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) superior mapeia vagamente para a classe no Python SDK. 

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

A tabela seguinte detalha cada campo de alto nível no ficheiro JSON, é tipo e uma descrição. Se um tipo de objeto está ligado a uma classe do Python SDK, há uma correspondência solta de 1:1 entre cada campo JSON e o nome variável público na classe Python. Em alguns casos, o campo pode mapear um argumento de construtor em vez de uma variável de classe. Por exemplo, `environmentVariables` o campo `environment_variables` mapeia a variável na [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) classe.

| Campo JSON | Tipo | Descrição |
|---|---|---|
| `name` | `string` | Nome do ambiente. Não inicie o nome com **microsoft** ou **AzureML**. |
| `version` | `string` | Versão do ambiente. |
| `environmentVariables` | `{string: string}` | Um hash-map de nomes e valores variáveis ambientais. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | Objeto que define o ambiente python e intérprete para usar em recurso de computação alvo. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | Define as definições para personalizar a imagem do Docker construída de acordo com as especificações do ambiente. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | A secção configura as definições de Faísca. Só é utilizado quando a estrutura é definida para pySpark. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | Configures databricks dependências da biblioteca. |
| `inferencingStackVersion` | `string` | Especifica a versão da pilha de inferência adicionada à imagem. Para evitar adicionar uma pilha de `null`inferência, deixe este campo . Valor válido: "mais recente". |

## <a name="ml-pipeline-management"></a>Gestão do gasoduto ML

Os seguintes comandos demonstram como trabalhar com gasodutos de aprendizagem automática:

+ Criar um pipeline de aprendizagem automática:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Para mais informações, consulte a [criação de gasoduto az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create).

    Para obter mais informações sobre o ficheiro YAML do gasoduto, consulte [os gasodutos de aprendizagem automática Define em YAML](reference-pipeline-yaml.md).

+ Executar um oleoduto:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Para mais informações, consulte [az ml run submit-pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Para obter mais informações sobre o ficheiro YAML do gasoduto, consulte [os gasodutos de aprendizagem automática Define em YAML](reference-pipeline-yaml.md).

+ Agende um oleoduto:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Para mais informações, consulte o [programa de criação de gasodutos Az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Para obter mais informações sobre o ficheiro YAML do pipeline, consulte Os gasodutos de [aprendizagem automática Define em YAML](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Registo de modelos, perfis, implantação

Os seguintes comandos demonstram como registar um modelo treinado e, em seguida, implantá-lo como um serviço de produção:

+ Registe um modelo com Aprendizagem automática Azure:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Para mais informações, consulte o registo do [modelo Az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **OPCIONAL** Perfile o seu modelo para obter valores de CPU e memória ideais para implementação.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Para mais informações, consulte o perfil do [modelo az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ Implante o seu modelo para AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Para obter mais informações sobre o esquema de ficheiro de configuração de inferência, consulte esquema de [configuração de inferência](#inferenceconfig).
    
    Para obter mais informações sobre o esquema de ficheiro de configuração de implementação, consulte o esquema de [configuração de implementação](#deploymentconfig).

    Para mais informações, consulte a implementação do [modelo Az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Esquema de configuração de inferência

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Esquema de configuração de implementação

### <a name="local-deployment-configuration-schema"></a>Esquema de configuração de implementação local

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Esquema de configuração de configuração de configuração de instância de contentores Azure 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Esquema de configuração de configuração de configuração do serviço Azure Kubernetes

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Passos seguintes

* [Referência de comando para a extensão CLI de aprendizagem automática](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Treine e implemente modelos de aprendizagem automática utilizando pipelines Azure](/azure/devops/pipelines/targets/azure-machine-learning)
