---
title: Integração do git para Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba como o Azure Machine Learning se integra a um repositório git local.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 10/11/2019
ms.openlocfilehash: db96663ef3d901546e1b32362a9eb9c9ae09dd21
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377515"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integração do git para Azure Machine Learning

O [git](https://git-scm.com/) é um sistema de controle de versão popular que permite compartilhar e colaborar com seus projetos. Ao enviar um trabalho de treinamento para Azure Machine Learning, se os arquivos de treinamento estiverem armazenados em um repositório git local, as informações sobre o repositório serão rastreadas como parte do processo de treinamento.

Como Azure Machine Learning rastreia informações de um repositório git local, ele não está vinculado a nenhum repositório central específico. Seu repositório pode ser clonado do GitHub, GitLab, bitbucket, Azure DevOps ou qualquer outro serviço compatível com o git.

## <a name="how-does-git-integration-work"></a>Como funciona a integração do git?

Quando você envia uma execução de treinamento do SDK do Python ou da CLI do Machine Learning, os arquivos necessários para treinar o modelo são carregados no espaço de trabalho. Se o comando `git` estiver disponível em seu ambiente de desenvolvimento, o processo de carregamento o usará para verificar se os arquivos estão armazenados em um repositório git. Nesse caso, as informações do repositório git também são carregadas como parte da execução do treinamento. Essas informações são armazenadas nas seguintes propriedades para a execução de treinamento:

| Propriedade | Descrição |
| ----- | ----- |
| `azureml.git.repository_uri` | O URI do qual o repositório foi clonado. |
| `mlflow.source.git.repoURL` | O URI do qual o repositório foi clonado. |
| `azureml.git.branch` | A ramificação ativa quando a execução foi enviada. |
| `mlflow.source.git.branch` | A ramificação ativa quando a execução foi enviada. |
| `azureml.git.commit` | O hash de confirmação do código que foi enviado para a execução. |
| `mlflow.source.git.commit` | O hash de confirmação do código que foi enviado para a execução. |
| `azureml.git.dirty` | `True`, se a confirmação for suja; caso contrário, `false`. |

Essas informações são enviadas para execuções que usam um estimador, pipeline de Machine Learning ou execução de script.

Se os arquivos de treinamento não estiverem localizados em um repositório git no ambiente de desenvolvimento ou se o comando `git` não estiver disponível, nenhuma informação relacionada ao git será controlada.

## <a name="view-the-logged-information"></a>Exibir as informações registradas

As informações do git são armazenadas nas propriedades de uma execução de treinamento. Você pode exibir essas informações usando o portal do Azure, o SDK do Python e a CLI. 

### <a name="azure-portal"></a>Portal do Azure

1. No [portal do Azure](https://portal.azure.com), selecione seu espaço de trabalho.
1. Selecione __experimentos__e, em seguida, selecione um de seus experimentos.
1. Selecione uma das execuções na coluna __número da execução__ .
1. Selecione __logs__e, em seguida, expanda os __logs__ e as entradas do __azureml__ . Selecione o link que começa com __### @ no__t-2azure__.

As informações registradas em log contêm texto semelhante ao JSON a seguir:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

Depois de enviar uma execução de treinamento, um objeto [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) é retornado. O atributo `properties` desse objeto contém as informações do git registradas. Por exemplo, o código a seguir recupera o hash de confirmação:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

O comando da CLI `az ml run` pode ser usado para recuperar as propriedades de uma execução. Por exemplo, o comando a seguir retorna as propriedades da última execução no experimento chamado `train-on-amlcompute`:

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Para obter mais informações, consulte a documentação de referência de [execução do AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) .

## <a name="next-steps"></a>Passos seguintes

* Para obter uma explicação de como treinar com Azure Machine Learning no Visual Studio Code, consulte [tutorial: treinar modelos com Azure Machine Learning](tutorial-train-models-with-aml.md).
* Para obter uma explicação de como editar, executar e depurar código localmente, consulte o [tutorial do Python Hello-World](https://code.visualstudio.com/docs/Python/Python-tutorial).
