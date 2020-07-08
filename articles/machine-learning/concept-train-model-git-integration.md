---
title: Integração de Git para Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba como a Azure Machine Learning se integra com um repositório local de Git. Ao submeter uma corrida de formação a partir de um diretório local que é um repositório git, as informações sobre repo, branch e compromisso atual são rastreadas como parte da corrida.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 7cc2e346a35cd1cdf1278b527dc451a903d60f89
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78402834"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integração de Git para Azure Machine Learning

[Git](https://git-scm.com/) é um popular sistema de controlo de versão que permite partilhar e colaborar nos seus projetos. 

A Azure Machine Learning suporta totalmente os repositórios de Git para o trabalho de rastreio - pode clonar repositórios diretamente no seu sistema de ficheiros de espaço de trabalho partilhado, usar Git na sua estação de trabalho local ou usar Git a partir de um oleoduto CI/CD.

Ao submeter um trabalho ao Azure Machine Learning, se os ficheiros de origem forem armazenados num repositório local, então a informação sobre o repo é rastreada como parte do processo de treino.

Uma vez que a Azure Machine Learning rastreia informações de um repo local, não está ligada a nenhum repositório central específico. O seu repositório pode ser clonado do GitHub, GitLab, Bitbucket, Azure DevOps ou de qualquer outro serviço compatível com git.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Clonar repositórios Git no sistema de ficheiros da área de trabalho
O Azure Machine Learning fornece um sistema de ficheiros partilhado para todos os utilizadores no espaço de trabalho.
Para clonar um repositório git nesta partilha de ficheiros, recomendamos que crie uma Instância de Computação & abrir um terminal.
Uma vez aberto o terminal, você tem acesso a um cliente Git completo e pode clonar e trabalhar com Git através da experiência Git CLI.

Recomendamos que clone o repositório no diretório dos seus utilizadores para que outros não façam colisões diretamente no seu ramo de trabalho.

Você pode clonar qualquer repositório git que você pode autenticar (GitHub, Azure Repos, BitBucket, etc.)

Para um guia sobre como usar o Git CLI, leia [aqui.](https://guides.github.com/introduction/git-handbook/)

## <a name="track-code-that-comes-from-git-repositories"></a>Código de rastreio que vem dos repositórios de Git

Quando submete uma corrida de treino a partir do Python SDK ou do Machine Learning CLI, os ficheiros necessários para treinar o modelo são enviados para o seu espaço de trabalho. Se o `git` comando estiver disponível no seu ambiente de desenvolvimento, o processo de upload utiliza-o para verificar se os ficheiros estão armazenados num repositório de git. Em caso afirmativo, então a informação do seu repositório de git também é carregada como parte da formação. Esta informação é armazenada nas seguintes propriedades para a execução de formação:

| Propriedade | Comando Git usado para obter o valor | Descrição |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | O URI de onde o seu repositório foi clonado. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | O URI de onde o seu repositório foi clonado. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | O ramo ativo quando a corrida foi submetida. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | O ramo ativo quando a corrida foi submetida. |
| `azureml.git.commit` | `git rev-parse HEAD` | O haxixe do código que foi submetido para a corrida. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | O haxixe do código que foi submetido para a corrida. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, se o ramo/compromisso estiver sujo; caso contrário, `false` . . |

Esta informação é enviada para corridas que utilizem um estimador, um pipeline de aprendizagem automática ou script run.

Se os seus ficheiros de treino não estiverem localizados num repositório de git no seu ambiente de desenvolvimento, ou se o `git` comando não estiver disponível, então nenhuma informação relacionada com git é rastreada.

> [!TIP]
> Para verificar se o comando git está disponível no seu ambiente de desenvolvimento, abra uma sessão de shell, pedido de comando, PowerShell ou outra interface de linha de comando e digite o seguinte comando:
>
> ```
> git --version
> ```
>
> Se instalado, e no caminho, recebe uma resposta semelhante a `git version 2.4.1` . Para obter mais informações sobre a instalação do git no seu ambiente de desenvolvimento, consulte o [site da Git.](https://git-scm.com/)

## <a name="view-the-logged-information"></a>Ver as informações registadas

A informação do git é armazenada nas propriedades para uma corrida de treino. Pode ver esta informação utilizando o portal Azure, Python SDK e CLI. 

### <a name="azure-portal"></a>Portal do Azure

1. A partir do [portal Azure,](https://portal.azure.com)selecione o seu espaço de trabalho.
1. Selecione __Experimentos__e, em seguida, selecione uma das suas experiências.
1. Selecione uma das correções da coluna __RUN NUMBER.__
1. Selecione __Logs__e, em seguida, expanda os __registos__ e as entradas __azureml.__ Selecione o link que começa com __ ### \_ azul__.

    ![A entrada ###_azure no portal](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

As informações registadas contêm texto semelhante ao seguinte JSON:

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

Depois de submeter uma corrida de treino, um objeto [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) é devolvido. O `properties` atributo deste objeto contém a informação do git registado. Por exemplo, o seguinte código recupera o haxixe do compromisso:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

O `az ml run` comando CLI pode ser usado para recuperar as propriedades de uma corrida. Por exemplo, o seguinte comando devolve as propriedades para a última execução da experiência `train-on-amlcompute` denominada:

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Para mais informações, consulte a documentação de referência [az ml run.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest)

## <a name="next-steps"></a>Próximos passos

* [Configurar e utilizar metas de computação para a formação de modelos](how-to-set-up-training-targets.md)
