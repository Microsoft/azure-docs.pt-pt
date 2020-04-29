---
title: Integração git para Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba como o Azure Machine Learning se integra com um repositório git local. Ao submeter uma formação a partir de um diretório local que é um repositório Git, as informações sobre repo, filial e compromisso atual são rastreadas como parte da corrida.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 7cc2e346a35cd1cdf1278b527dc451a903d60f89
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78402834"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integração git para Azure Machine Learning

[Git](https://git-scm.com/) é um sistema de controlo de versão popular que lhe permite partilhar e colaborar nos seus projetos. 

O Azure Machine Learning suporta totalmente os repositórios git para o trabalho de rastreio - pode clonar repositórios diretamente no seu sistema de ficheiros de espaço de trabalho partilhado, usar Git na sua estação de trabalho local, ou usar Git a partir de um pipeline CI/CD.

Ao submeter um trabalho ao Azure Machine Learning, se os ficheiros de origem forem armazenados num repositório local, então as informações sobre o repo são rastreadas como parte do processo de formação.

Uma vez que o Azure Machine Learning rastreia informações de um repo local, não está ligado a nenhum repositório central específico. O seu repositório pode ser clonado a partir de GitHub, GitLab, Bitbucket, Azure DevOps ou qualquer outro serviço compatível com git.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Clone Git repositórios no seu sistema de ficheiros espaço de trabalho
O Azure Machine Learning fornece um sistema de ficheiros partilhado para todos os utilizadores no espaço de trabalho.
Para clonar um repositório Git nesta partilha de ficheiros, recomendamos que crie um Compute Instance & abrir um terminal.
Uma vez aberto o terminal, você tem acesso a um cliente Git completo e pode clonar e trabalhar com git através da experiência Git CLI.

Recomendamos que clone o repositório no diretório dos seus utilizadores para que outros não façam colisões diretamente no seu ramo de trabalho.

Você pode clonar qualquer repositório Git que você pode autenticar (GitHub, Azure Repos, BitBucket, etc.)

Para obter um guia sobre como utilizar o Git CLI, leia [aqui](https://guides.github.com/introduction/git-handbook/).

## <a name="track-code-that-comes-from-git-repositories"></a>Código de pista que vem dos repositórios de Git

Quando submete uma execução de treino a partir do Python SDK ou do Machine Learning CLI, os ficheiros necessários para treinar o modelo são enviados para o seu espaço de trabalho. Se `git` o comando estiver disponível no seu ambiente de desenvolvimento, o processo de upload usa-o para verificar se os ficheiros são armazenados num repositório git. Em caso afirmativo, as informações do seu repositório git também são enviadas como parte do treino. Esta informação é armazenada nas seguintes propriedades para a execução de formação:

| Propriedade | Comando Git usado para obter o valor | Descrição |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | O URI de onde o seu repositório foi clonado. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | O URI de onde o seu repositório foi clonado. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | O ramo ativo quando a corrida foi submetida. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | O ramo ativo quando a corrida foi submetida. |
| `azureml.git.commit` | `git rev-parse HEAD` | O hash de compromisso do código que foi submetido para a execução. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | O hash de compromisso do código que foi submetido para a execução. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, se o ramo/compromisso estiver sujo; caso contrário, `false`. |

Esta informação é enviada para executagens que usam um estimador, um pipeline de aprendizagem automática ou uma execução de script.

Se os seus ficheiros de treino não estiverem localizados num `git` repositório git sobre o seu ambiente de desenvolvimento, ou se o comando não estiver disponível, então nenhuma informação relacionada com o git é rastreada.

> [!TIP]
> Para verificar se o comando git está disponível no seu ambiente de desenvolvimento, abra uma sessão de concha, pedido de comando, PowerShell ou outra interface de linha de comando e escreva o seguinte comando:
>
> ```
> git --version
> ```
>
> Se instalado, e no caminho, receberá uma `git version 2.4.1`resposta semelhante a . Para obter mais informações sobre a instalação do git no seu ambiente de desenvolvimento, consulte o [site git](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Ver a informação registada

A informação git é armazenada nas propriedades para um treino. Pode ver esta informação utilizando o portal Azure, Python SDK e CLI. 

### <a name="azure-portal"></a>Portal do Azure

1. A partir do [portal Azure,](https://portal.azure.com)selecione o seu espaço de trabalho.
1. Selecione __Experimentos__e, em seguida, selecione uma das suas experiências.
1. Selecione uma das correções da coluna __NÚMERO DE EXECUÇÃO.__
1. Selecione __Registos__e, em seguida, expanda os __registos__ e as entradas __em azul.__ Selecione o __ ### \___ link que começa com azul .

    ![A entrada ###_azure no portal](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

As informações registadas contêm textosemelhante ao seguinte JSON:

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

### <a name="python-sdk"></a>SDK Python

Depois de submeter um treino, um objeto [run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) é devolvido. O `properties` atributo deste objeto contém a informação de git registado. Por exemplo, o seguinte código recupera o hash de compromisso:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

O `az ml run` comando CLI pode ser usado para recuperar as propriedades de uma corrida. Por exemplo, o seguinte comando devolve as propriedades `train-on-amlcompute`para a última execução na experiência denominada:

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Para mais informações, consulte a documentação de referência [az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest)

## <a name="next-steps"></a>Passos seguintes

* [Configurar e utilizar alvos de cálculo para formação de modelos](how-to-set-up-training-targets.md)
