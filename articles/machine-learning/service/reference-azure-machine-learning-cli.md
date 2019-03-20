---
title: Extensão da CLI de aprendizagem automática
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre a extensão da CLI do Azure Machine Learning para a CLI do Azure. A CLI do Azure é um utilitário de linha de comandos para várias plataformas que lhe permite trabalhar com os recursos na cloud do Azure. A extensão de Machine Learning permite-lhe trabalhar com o serviço do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 4b7cd15b1746bac6f61ef46108ade6cf1fb3b7ff
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894653"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Utilizar a extensão da CLI para o serviço Azure Machine Learning

A CLI do Azure Machine Learning é uma extensão para o [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), uma interface de linha de comandos para várias plataformas para a plataforma Azure. Esta extensão fornece comandos para trabalhar com o serviço Azure Machine Learning na linha de comando. Permite-lhe criar scripts que automatizam o fluxos de trabalho da aprendizagem automática. Por exemplo, pode criar scripts que executem as seguintes ações:

+ Executar experimentações para criar modelos de machine learning

+ Registe-se os modelos de aprendizagem automática para utilização do cliente

+ Empacotar, implementar e controlar o ciclo de vida de seus modelos de machine learning

A CLI não é uma substituição para o SDK do Azure Machine Learning. É uma ferramenta complementar otimizado para lidar com tarefas altamente parametrizadas, tais como:

* Criação de recursos de computação

* submissão de experimentação parametrizado

* Registo do modelo

* Criação de imagens

* Implementação do serviço

## <a name="prerequisites"></a>Pré-requisitos


* Para utilizar a CLI, tem de ter uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* O [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Instalar a extensão

Para instalar a extensão da CLI do Machine Learning, utilize o seguinte comando:

```azurecli-interactive
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-1.0.10-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

Quando lhe for pedido, selecione `y` para instalar a extensão.

Para verificar se a extensão foi instalada, utilize o seguinte comando para apresentar uma lista de subcomandos do ML específicas:

```azurecli-interactive
az ml -h
```

> [!TIP]
> Para atualizar a extensão tem __Remova__ e, em seguida __instalar__ -lo. Esta ação instala a versão mais recente.

## <a name="remove-the-extension"></a>Remover a extensão

Para remover a extensão da CLI, utilize o seguinte comando:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Gestão de recursos

Os comandos seguintes demonstram como utilizar a CLI para gerir os recursos utilizados pelo Azure Machine Learning.


+ Crie uma área de trabalho do serviço do Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -n myworkspace -g myresourcegroup
    ```

+ Defina uma área de trabalho predefinido:

    ```azurecli-interactive
    az configure --defaults aml_workspace=myworkspace group=myresourcegroup
    ```
    
+ Anexar um cluster do AKS

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

## <a name="experiments"></a>Experimentações

Os comandos seguintes demonstram como utilizar a CLI para trabalhar com experiências:

* Anexe um projeto (configuração de execução) antes de submeter uma experimentação:

    ```azurecli-interactive
    az ml project attach --experiment-name myhistory
    ```

* Inicie uma execução da sua experimentação. Quando utilizar este comando, especifique o nome do ficheiro runconfig que contém a configuração de execução. O destino de computação utiliza a configuração de execução para criar o ambiente de treinamento para o modelo. Neste exemplo, a configuração de execução é carregada a partir do `./aml_config/myrunconfig.runconfig` ficheiro.

    ```azurecli-interactive
    az ml run submit -c myrunconfig train.py
    ```

    Para obter mais informações sobre o ficheiro de runconfig, consulte a [RunConfig](#runconfig) secção.

* Ver uma lista de experimentações submetidas:

    ```azurecli-interactive
    az ml history list
    ```

## <a name="model-registration-image-creation--deployment"></a>Registo de modelo, criação de imagens e implantação

Os comandos seguintes demonstram como registar um modelo preparado e, em seguida, implementá-la como um serviço de produção:

+ Registe um modelo com o Azure Machine Learning:

  ```azurecli-interactive
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ Crie uma imagem que contém o modelo e as dependências da aprendizagem automática: 

  ```azurecli-interactive
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ Implemente uma imagem para um destino de computação:

  ```azurecli-interactive
  az ml service create aci -n myaciservice --image-id myimage:1
  ```

## <a id="runconfig"></a> Ficheiro de Runconfig

Uma configuração de execução é utilizada para configurar o ambiente de treinamento usado para preparar o seu modelo. Esta configuração pode ser criada utilizando o SDK na memória ou pode ser carregado de um ficheiro de runconfig.

O ficheiro de runconfig é um documento de texto que descreve a configuração para o ambiente de treinamento. Por exemplo, ele lista o nome do script de formação e o ficheiro que contém as dependências de conda necessárias para preparar o modelo.

A CLI do Azure Machine Learning cria predefinidos `.runconfig` ficheiros com o nome `docker.runconfig` e `local.runconfig` quando anexa um projeto com o `az ml project attach` comando. 

Se tiver o código que cria uma configuração de execução com o [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) classe, pode usar o `save()` método mantê-los para um `.runconfig` ficheiro.

Segue-se um exemplo do conteúdo de um `.runconfig` ficheiro:

```text
# The script to run.
script: train.py
# The arguments to the script file.
arguments: []
# The name of the compute target to use for this run.
target: local
# Framework to execute inside. Allowed values are "Python" ,  "PySpark", "CNTK",  "TensorFlow", and "PyTorch".
framework: PySpark
# Communicator for the given framework. Allowed values are "None" ,  "ParameterServer", "OpenMpi", and "IntelMpi".
communicator: None
# Automatically prepare the run environment as part of the run itself.
autoPrepareEnvironment: true
# Maximum allowed duration for the run.
maxRunDurationSeconds:
# Number of nodes to use for running job.
nodeCount: 1
# Environment details.
environment:
# Environment variables set for the run.
  environmentVariables:
    EXAMPLE_ENV_VAR: EXAMPLE_VALUE
# Python details
  python:
# user_managed_dependencies=True indicates that the environmentwill be user managed. False indicates that AzureML willmanage the user environment.
    userManagedDependencies: false
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
  docker:
# Set True to perform this run inside a Docker container.
    enabled: true
# Base image used for Docker-based runs.
    baseImage: mcr.microsoft.com/azureml/base:0.2.1
# Set False if necessary to work around shared volume bugs.
    sharedVolumes: true
# Run with NVidia Docker extension to support GPUs.
    gpuSupport: false
# Extra arguments to the Docker run command.
    arguments: []
# Image registry that contains the base image.
    baseImageRegistry:
# DNS name or IP address of azure container registry(ACR)
      address:
# The username for ACR
      username:
# The password for ACR
      password:
# Spark details
  spark:
# List of spark repositories.
    repositories:
    - https://mmlspark.azureedge.net/maven
    packages:
    - group: com.microsoft.ml.spark
      artifact: mmlspark_2.11
      version: '0.12'
    precachePackages: true
# Databricks details
  databricks:
# List of maven libraries.
    mavenLibraries: []
# List of PyPi libraries
    pypiLibraries: []
# List of RCran libraries
    rcranLibraries: []
# List of JAR libraries
    jarLibraries: []
# List of Egg libraries
    eggLibraries: []
# History details.
history:
# Enable history tracking -- this allows status, logs, metrics, and outputs
# to be collected for a run.
  outputCollection: true
# whether to take snapshots for history.
  snapshotProject: true
# Spark configuration details.
spark:
  configuration:
    spark.app.name: Azure ML Experiment
    spark.yarn.maxAppAttempts: 1
# HDI details.
hdi:
# Yarn deploy mode. Options are cluster and client.
  yarnDeployMode: cluster
# Tensorflow details.
tensorflow:
# The number of worker tasks.
  workerCount: 1
# The number of parameter server tasks.
  parameterServerCount: 1
# Mpi details.
mpi:
# When using MPI, number of processes per node.
  processCountPerNode: 1
# data reference configuration details
dataReferences: {}
# Project share datastore reference.
sourceDirectoryDataStore:
# AmlCompute details.
amlcompute:
# VM size of the Cluster to be created.Allowed values are Azure vm sizes. The list of vm sizes is available in 'https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs
  vmSize:
# VM priority of the Cluster to be created.Allowed values are "dedicated" , "lowpriority".
  vmPriority:
# A bool that indicates if the cluster has to be retained after job completion.
  retainCluster: false
# Name of the cluster to be created. If not specified, runId will be used as cluster name.
  name:
# Maximum number of nodes in the AmlCompute cluster to be created. Minimum number of nodes will always be set to 0.
  clusterMaxNodeCount: 1
```