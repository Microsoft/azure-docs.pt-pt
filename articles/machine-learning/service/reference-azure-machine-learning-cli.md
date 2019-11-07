---
title: Extensão da CLI do Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba mais sobre a extensão Azure Machine Learning CLI para o CLI do Azure. O CLI do Azure é um utilitário de linha de comando de plataforma cruzada que permite trabalhar com recursos na nuvem do Azure. A extensão Machine Learning permite que você trabalhe com Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 11/05/2019
ms.custom: seodec18
ms.openlocfilehash: 20ff7d2cb6c185f574494871a986e1bce63536ba
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73619960"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>Usar a extensão da CLI para Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

A CLI do Azure Machine Learning é uma extensão do [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), uma interface de linha de comando de plataforma cruzada para a plataforma do Azure. Essa extensão fornece comandos para trabalhar com Azure Machine Learning. Ele permite automatizar suas atividades de aprendizado de máquina. A lista a seguir fornece algumas ações de exemplo que você pode fazer com a extensão CLI:

+ Executar experimentos para criar modelos de aprendizado de máquina

+ Registrar modelos de aprendizado de máquina para uso do cliente

+ Empacotar, implantar e acompanhar o ciclo de vida de seus modelos de aprendizado de máquina

A CLI não é uma substituição para o SDK do Azure Machine Learning. É uma ferramenta complementar que é otimizada para lidar com tarefas altamente parametrizadas que se adaptam bem à automação.

## <a name="prerequisites"></a>Pré-requisitos

* Para usar a CLI, você deve ter uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje.

* O [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="full-reference-docs"></a>Documentos de referência completa

Localize os [documentos de referência completos para a extensão Azure-CLI-ml de CLI do Azure](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Instalar a extensão

Para instalar a extensão da CLI do Machine Learning, use o seguinte comando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Os arquivos de exemplo que você pode usar com os comandos abaixo podem ser encontrados [aqui](https://aka.ms/azml-deploy-cloud).

Quando solicitado, selecione `y` para instalar a extensão.

Para verificar se a extensão foi instalada, use o seguinte comando para exibir uma lista de subcomandos específicos do ML:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>Atualizar a extensão

Para atualizar a extensão da CLI do Machine Learning, use o seguinte comando:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>Remover a extensão

Para remover a extensão da CLI, use o seguinte comando:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Gestão de recursos

Os comandos a seguir demonstram como usar a CLI para gerenciar os recursos usados pelo Azure Machine Learning.

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

* Inicie uma execução do seu experimento. Ao usar esse comando, especifique o nome do arquivo runconfig (o texto antes de \*. runconfig se você estiver olhando seu sistema de arquivos) em relação ao parâmetro-c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > O comando `az ml folder attach` cria um subdiretório `.azureml`, que contém dois arquivos de exemplo runconfig. 
    >
    > Se você tiver um script Python que cria um objeto de configuração de execução programaticamente, poderá usar [runconfig. Save ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) para salvá-lo como um arquivo runconfig.
    >
    > O esquema runconfig completo pode ser encontrado neste [arquivo JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json).

    Para obter mais informações, consulte [AZ ml Run Submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Exibir uma lista de experimentos:

    ```azurecli-interactive
    az ml experiment list
    ```

    Para obter mais informações, consulte [AZ ml experimento List](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

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

Os comandos a seguir demonstram como registrar um modelo treinado e, em seguida, implantá-lo como um serviço de produção:

+ Registrar um modelo com Azure Machine Learning:

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

[!INCLUDE [inferenceconfig](../../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Esquema de configuração de implantação

### <a name="local-deployment-configuration-schema"></a>Esquema de configuração de implantação local

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Esquema de configuração de implantação da instância de contêiner do Azure 

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Esquema de configuração de implantação do serviço kubernetes do Azure

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Passos seguintes

* [Referência de comando para a extensão da CLI do Machine Learning](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Treinar e implantar modelos de aprendizado de máquina usando o Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
