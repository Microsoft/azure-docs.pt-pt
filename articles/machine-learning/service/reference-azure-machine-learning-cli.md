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
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 2992ec9f43aac9e0d80c5e42873d26ac3a9c3fd1
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916985"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Utilizar a extensão da CLI para o serviço Azure Machine Learning

A CLI do Azure Machine Learning é uma extensão para o [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), uma interface de linha de comandos para várias plataformas para a plataforma Azure. Esta extensão fornece comandos para trabalhar com o serviço Azure Machine Learning na linha de comando. Ele permite que automatize fluxos de trabalho de aprendizagem da sua máquina. Por exemplo, pode efetuar as seguintes ações:

+ Executar experimentações para criar modelos de machine learning

+ Registe-se os modelos de aprendizagem automática para utilização do cliente

+ Empacotar, implementar e controlar o ciclo de vida de seus modelos de machine learning

A CLI não é uma substituição para o SDK do Azure Machine Learning. É uma ferramenta complementar otimizado para lidar com tarefas altamente parametrizadas que se adequar-se bem a automatização.

## <a name="prerequisites"></a>Pré-requisitos

* Para utilizar a CLI, tem de ter uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* O [CLI do Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Instalar a extensão

Para instalar a extensão da CLI do Machine Learning, utilize o seguinte comando:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Ficheiros de exemplo que pode utilizar com os comandos abaixo podem ser encontrados [aqui](http://aka.ms/azml-deploy-cloud).

Quando lhe for pedido, selecione `y` para instalar a extensão.

Para verificar se a extensão foi instalada, utilize o seguinte comando para apresentar uma lista de subcomandos do ML específicas:

```azurecli-interactive
az ml -h
```

## <a name="remove-the-extension"></a>Remover a extensão

Para remover a extensão da CLI, utilize o seguinte comando:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Gestão de recursos

Os comandos seguintes demonstram como utilizar a CLI para gerir os recursos utilizados pelo Azure Machine Learning.

+ Se ainda não tiver uma, crie um grupo de recursos:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Crie uma área de trabalho do serviço do Azure Machine Learning:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

+ Anexe uma configuração de área de trabalho para uma pasta para ativar a deteção contextual da CLI.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

+ Anexe um contentor de Blobs do Azure como um arquivo de dados.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

+ Anexe um cluster do AKS como um destino de computação.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

+ Criar um novo destino de AMLcompute

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```
    
## <a id="experiments"></a>Executar experimentações

+ Anexe uma configuração de área de trabalho para uma pasta para ativar a deteção contextual da CLI.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

* Inicie uma execução da sua experimentação. Quando utilizar este comando, especifique o nome do ficheiro runconfig (o texto antes do \*.runconfig se estiver à procura no seu sistema de ficheiros) contra o parâmetro - c.

    ```azurecli-interactive
    az ml run submit-script -c local -e testexperiment train.py
    ```

* Ver uma lista de experimentações:

    ```azurecli-interactive
    az ml experiment list
    ```

## <a name="model-registration-profiling-deployment"></a>Registo do modelo, criação de perfis, implementação

Os comandos seguintes demonstram como registar um modelo preparado e, em seguida, implementá-la como um serviço de produção:

+ Registe um modelo com o Azure Machine Learning:

  ```azurecli-interactive
  az ml model register -n mymodel -p sklearn_regression_model.pkl
  ```

+ Implementar o seu modelo no AKS

  ```azurecli-interactive
  az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
  ```
