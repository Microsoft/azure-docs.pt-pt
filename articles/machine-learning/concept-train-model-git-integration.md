---
title: Integração do git para Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba como o Azure Machine Learning se integra a um repositório git local. Ao enviar uma execução de treinamento de um diretório local que é um repositório git, as informações sobre o repositório, a ramificação e a confirmação atual são controladas como parte da execução.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 10/11/2019
ms.openlocfilehash: b83eb1556ed3f4a41409faf70f6ba9d8cd28322d
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732183"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integração do git para Azure Machine Learning

O [git](https://git-scm.com/) é um sistema de controle de versão popular que permite compartilhar e colaborar com seus projetos. Ao enviar um trabalho de treinamento para Azure Machine Learning, se os arquivos de treinamento estiverem armazenados em um repositório git local, as informações sobre o repositório serão rastreadas como parte do processo de treinamento.

Como Azure Machine Learning rastreia informações de um repositório git local, ele não está vinculado a nenhum repositório central específico. Seu repositório pode ser clonado do GitHub, GitLab, bitbucket, Azure DevOps ou qualquer outro serviço compatível com o git.

## <a name="how-does-git-integration-work"></a>Como funciona a integração do git?

Quando você envia uma execução de treinamento do SDK do Python ou da CLI do Machine Learning, os arquivos necessários para treinar o modelo são carregados no espaço de trabalho. Se o comando `git` estiver disponível em seu ambiente de desenvolvimento, o processo de carregamento o usará para verificar se os arquivos estão armazenados em um repositório git. Nesse caso, as informações do repositório git também são carregadas como parte da execução do treinamento. Essas informações são armazenadas nas seguintes propriedades para a execução de treinamento:

| Propriedade | Comando git usado para obter o valor | Descrição |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | O URI do qual o repositório foi clonado. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | O URI do qual o repositório foi clonado. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | A ramificação ativa quando a execução foi enviada. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | A ramificação ativa quando a execução foi enviada. |
| `azureml.git.commit` | `git rev-parse HEAD` | O hash de confirmação do código que foi enviado para a execução. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | O hash de confirmação do código que foi enviado para a execução. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, se a ramificação/confirmação estiver suja; caso contrário, `false`. |

Essas informações são enviadas para execuções que usam um estimador, pipeline de Machine Learning ou execução de script.

Se os arquivos de treinamento não estiverem localizados em um repositório git em seu ambiente de desenvolvimento, ou se o comando `git` não estiver disponível, nenhuma informação relacionada ao git será controlada.

> [!TIP]
> Para verificar se o comando git está disponível no ambiente de desenvolvimento, abra uma sessão do Shell, um prompt de comando, o PowerShell ou outra interface de linha de comando e digite o seguinte comando:
>
> ```
> git --version
> ```
>
> Se estiver instalado e, no caminho, você receberá uma resposta semelhante a `git version 2.4.1`. Para obter mais informações sobre como instalar o Git em seu ambiente de desenvolvimento, consulte o [site do git](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Exibir as informações registradas

As informações do git são armazenadas nas propriedades de uma execução de treinamento. Você pode exibir essas informações usando o portal do Azure, o SDK do Python e a CLI. 

### <a name="azure-portal"></a>Portal do Azure

1. No [portal do Azure](https://portal.azure.com), selecione seu espaço de trabalho.
1. Selecione __experimentos__e, em seguida, selecione um de seus experimentos.
1. Selecione uma das execuções na coluna __número da execução__ .
1. Selecione __logs__e, em seguida, expanda os __logs__ e as entradas do __azureml__ . Selecione o link que começa com __###\_Azure__.

    ![A entrada # # #_azure no portal](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

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

O comando `az ml run` CLI pode ser usado para recuperar as propriedades de uma execução. Por exemplo, o comando a seguir retorna as propriedades da última execução no experimento chamado `train-on-amlcompute`:

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Para obter mais informações, consulte a documentação de referência de [execução do AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) .

## <a name="next-steps"></a>Passos seguintes

* [Configurar e usar destinos de computação para treinamento de modelo](how-to-set-up-training-targets.md)