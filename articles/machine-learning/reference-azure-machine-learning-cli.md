---
title: Extensão da CLI
titleSuffix: Azure Machine Learning
description: Saiba mais sobre a extensão da CLI do Azure Machine Learning para a CLI do Azure. A CLI do Azure é um utilitário de linha de comandos para várias plataformas que lhe permite trabalhar com os recursos na cloud do Azure. A extensão Machine Learning permite que você trabalhe com Azure Machine Learning. A CLI do ML cria e gerencia recursos como seu espaço de trabalho, repositórios de armazenamento, conjuntos de valores, pipelines, modelos e implantações.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 11/05/2019
ms.custom: seodec18
ms.openlocfilehash: fe9cee44970da94c346caffc0d5fe76ffebf4a0b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845151"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>Usar a extensão da CLI para Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

A CLI do Azure Machine Learning é uma extensão para o [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), uma interface de linha de comandos para várias plataformas para a plataforma Azure. Essa extensão fornece comandos para trabalhar com Azure Machine Learning. Ele permite automatizar suas atividades de aprendizado de máquina. A lista a seguir fornece algumas ações de exemplo que você pode fazer com a extensão CLI:

+ Executar experimentações para criar modelos de machine learning

+ Registe-se os modelos de aprendizagem automática para utilização do cliente

+ Empacotar, implementar e controlar o ciclo de vida de seus modelos de machine learning

A CLI não é uma substituição para o SDK do Azure Machine Learning. É uma ferramenta complementar que é otimizada para lidar com tarefas altamente parametrizadas que se adaptam bem à automação.

## <a name="prerequisites"></a>Pré-requisitos

* Para utilizar a CLI, tem de ter uma subscrição do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje.

* O [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="full-reference-docs"></a>Documentos de referência completa

Localize os [documentos de referência completos para a extensão Azure-CLI-ml de CLI do Azure](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Instalar a extensão

Para instalar a extensão da CLI do Machine Learning, utilize o seguinte comando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Os arquivos de exemplo que você pode usar com os comandos abaixo podem ser encontrados [aqui](https://aka.ms/azml-deploy-cloud).

Quando lhe for pedido, selecione `y` para instalar a extensão.

Para verificar se a extensão foi instalada, utilize o seguinte comando para apresentar uma lista de subcomandos do ML específicas:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>Atualizar a extensão

Para atualizar a extensão da CLI do Machine Learning, use o seguinte comando:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>Remover a extensão

Para remover a extensão da CLI, utilize o seguinte comando:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Gestão de recursos

Os comandos seguintes demonstram como utilizar a CLI para gerir os recursos utilizados pelo Azure Machine Learning.

+ Se você ainda não tiver um, crie um grupo de recursos:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Criar um espaço de trabalho Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > Este comando cria um espaço de trabalho básico de edição. Para criar um espaço de trabalho corporativo, use a opção `--sku enterprise` com o comando `az ml workspace create`. Para obter mais informações sobre as edições do Azure Machine Learning, consulte [o que é Azure Machine Learning](overview-what-is-azure-ml.md#sku).

    Para obter mais informações, consulte [AZ ml Workspace Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Anexe uma configuração de espaço de trabalho a uma pasta para habilitar a conscientização contextual da CLI.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Este comando cria um subdiretório `.azureml` que contém os arquivos de ambiente runconfig e Conda de exemplo. Ele também contém um arquivo `config.json` que é usado para se comunicar com seu espaço de trabalho do Azure Machine Learning.

    Para obter mais informações, consulte [AZ ml Folder Attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Anexe um contêiner de blob do Azure como um repositório de armazenamento.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Para obter mais informações, consulte [AZ ml datastore Attach-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Carregar arquivos em um repositório de armazenamento.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Para obter mais informações, consulte [AZ ml datastore upload](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload).

+ Anexe um cluster AKS como um destino de computação.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Para obter mais informações, consulte [AZ ml computetarget Attach AKs](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Crie um novo destino AMLcompute.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Para obter mais informações, consulte [AZ ml computetarget Create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a id="experiments"></a>Executar experimentos

* Inicie uma execução da sua experimentação. Ao usar esse comando, especifique o nome do arquivo runconfig (o texto antes de \*. runconfig se você estiver olhando seu sistema de arquivos) em relação ao parâmetro-c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > O comando `az ml folder attach` cria um subdiretório `.azureml`, que contém dois arquivos de exemplo runconfig. 
    >
    > Se você tiver um script Python que cria um objeto de configuração de execução programaticamente, poderá usar [runconfig. Save ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) para salvá-lo como um arquivo runconfig.
    >
    > O esquema runconfig completo pode ser encontrado neste [arquivo JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). O esquema está autodocumentando por meio da chave de `description` de cada objeto. Além disso, há enums para valores possíveis e um trecho de modelo no final.

    Para obter mais informações, consulte [AZ ml Run Submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Exibir uma lista de experimentos:

    ```azurecli-interactive
    az ml experiment list
    ```

    Para obter mais informações, consulte [AZ ml experimento List](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="dataset-management"></a>Gerenciamento de conjunto de

Os comandos a seguir demonstram como trabalhar com conjuntos de valores no Azure Machine Learning:

+ Registrar um conjunto de uma:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Para obter informações sobre o formato do ficheiro JSON utilizado para definir o conjunto de dados, utilize `az ml dataset register --show-template`.

    Para obter mais informações, consulte [AZ ml DataSet Register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Arquivar um conjunto de um DataSet ativo ou preterido:

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    Para obter mais informações, consulte [AZ ml DataSet Archive](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Substitua um conjunto de uma:

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    Para obter mais informações, consulte [AZ ml DataSet preterite](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Listar todos os conjuntos de valores em um espaço de trabalho:

    ```azurecli-interactive
    az ml dataset list
    ```

    Para obter mais informações, consulte [AZ ml DataSet List](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Obter detalhes de um conjunto de informações:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Para obter mais informações, consulte [AZ ml DataSet show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Reativar um conjunto de um arquivo morto ou preterido:

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    Para obter mais informações, consulte [AZ ml DataSet reactivate](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Cancelar o registro de um conjunto de uma:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Para obter mais informações, consulte [AZ ml DataSet Unregister](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>Gerenciamento de ambiente

Os comandos a seguir demonstram como criar, registrar e listar Azure Machine Learning [ambientes](how-to-configure-environment.md) para seu espaço de trabalho:

+ Criar arquivos scaffolding para um ambiente:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Para obter mais informações, consulte [AZ ml Environment Scaffold](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Registrar um ambiente:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Para obter mais informações, consulte [AZ ml Environment Register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register).

+ Listar ambientes registrados:

    ```azurecli-interactive
    az ml environment list
    ```

    Para obter mais informações, consulte [lista de ambiente AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list).

+ Baixar um ambiente registrado:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Para obter mais informações, consulte [fazer download do ambiente AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download).

### <a name="environment-configuration-schema"></a>Esquema de configuração do ambiente

Se usou o comando `az ml environment scaffold`, gera um modelo `azureml_environment.json` ficheiro que pode ser modificado e usado para criar configurações ambientais personalizadas com o CLI. O objeto de nível superior é mapeado de forma flexível para a classe [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) no SDK do Python. 

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

A tabela a seguir detalha cada campo de nível superior no arquivo JSON, é tipo e uma descrição. Se um tipo de objeto estiver vinculado a uma classe do SDK do Python, haverá uma correspondência de 1:1 flexível entre cada campo JSON e o nome da variável pública na classe Python. Em alguns casos, o campo pode ser mapeado para um argumento de Construtor em vez de uma variável de classe. Por exemplo, os mapas de campo `environmentVariables` para a variável `environment_variables` na classe [`Environment`.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py)

| Campo JSON | Tipo | Descrição |
|---|---|---|
| `name` | `string` | Nome do ambiente. Não inicie o nome com **Microsoft** ou **AzureML**. |
| `version` | `string` | Versão do ambiente. |
| `environmentVariables` | `{string: string}` | Um mapa de hash de valores e nomes de variáveis de ambiente. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | Objeto que define o ambiente Python e o intérprete a ser usado no recurso de computação de destino. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | Define as configurações para personalizar a imagem do Docker criada para as especificações do ambiente. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | A seção define as configurações do Spark. Ele só é usado quando a estrutura é definida como PySpark. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | Configura as dependências da biblioteca do databricks. |
| `inferencingStackVersion` | `string` | Especifica a versão da pilha inferência adicionada à imagem. Para evitar a adição de uma pilha inferência, deixe esse campo `null`. Valor válido: "mais recente". |

## <a name="ml-pipeline-management"></a>Gerenciamento de pipeline ML

Os comandos a seguir demonstram como trabalhar com pipelines do Machine Learning:

+ Criar um pipeline de Machine Learning:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Para obter mais informações, consulte [AZ ml pipeline Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create).

    Para obter mais informações sobre o arquivo pipeline YAML, consulte [definir pipelines de Machine Learning em YAML](reference-pipeline-yaml.md).

+ Executar um pipeline:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Para obter mais informações, consulte [AZ ml Run Submit-pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Para obter mais informações sobre o arquivo pipeline YAML, consulte [definir pipelines de Machine Learning em YAML](reference-pipeline-yaml.md).

+ Agendar um pipeline:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Para obter mais informações, consulte [AZ ml pipeline Create-Schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Para obter mais informações sobre o arquivo YAML de agendamento de pipeline, consulte [definir pipelines de Machine Learning em YAML](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Registro de modelo, criação de perfil, implantação

Os comandos seguintes demonstram como registar um modelo preparado e, em seguida, implementá-la como um serviço de produção:

+ Registe um modelo com o Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Para obter mais informações, consulte [AZ ml Model Register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **Opcional** Faça o profile de seu modelo para obter valores de CPU e memória ideais para implantação.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Para obter mais informações, consulte [perfil de modelo AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ Implantar seu modelo no AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Para obter mais informações sobre o esquema do arquivo de configuração de inferência, consulte [esquema de configuração de inferência](#inferenceconfig).
    
    Para obter mais informações sobre o esquema do arquivo de configuração de implantação, consulte [implantação de esquema de configuração](#deploymentconfig).

    Para obter mais informações, consulte [implantar o modelo do AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Esquema de configuração de inferência

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Esquema de configuração de implantação

### <a name="local-deployment-configuration-schema"></a>Esquema de configuração de implantação local

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Esquema de configuração de implantação da instância de contêiner do Azure 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Esquema de configuração de implantação do serviço kubernetes do Azure

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Passos seguintes

* [Referência de comando para a extensão da CLI do Machine Learning](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Treinar e implantar modelos de aprendizado de máquina usando o Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
