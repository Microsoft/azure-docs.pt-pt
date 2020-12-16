---
title: Tutorial - Construa imagem no código comprometendo-se
description: Neste tutorial, aprende-se a configurar uma Tarefa de Registo de Contentores Azure para ativar automaticamente as construções de imagem de contentores na nuvem quando se compromete código fonte a um repositório de Git.
ms.topic: tutorial
ms.date: 11/24/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 7f2e6d7f304977d3e6d92a778dba5bf026343707
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562910"
---
# <a name="tutorial-automate-container-image-builds-in-the-cloud-when-you-commit-source-code"></a>Tutorial: Automatizar compilações de imagens de contentor na cloud ao consolidar o código fonte

Além de uma [tarefa rápida,](container-registry-tutorial-quick-task.md)a ACR Tasks suporta a imagem automatizada do contentor Docker na nuvem quando compromete o código fonte a um repositório git. Os contextos de Git apoiados para tarefas ACR incluem gitHub público ou privado ou Azure Repos.

> [!NOTE]
> Atualmente, a ACR Tasks não suporta o commit ou pull request triggers nos repos da GitHub Enterprise.

Neste tutorial, a sua tarefa ACR constrói e empurra uma única imagem de recipiente especificada num Dockerfile quando compromete o código fonte a um repo Git. Para criar uma [tarefa em várias etapas](container-registry-tasks-multi-step.md) que utilize um ficheiro YAML para definir passos para construir, empurrar e testar opcionalmente vários recipientes no código cometer, consulte [Tutorial: Executar um fluxo de trabalho de contentor em várias etapas na nuvem quando cometer código fonte](container-registry-tutorial-multistep-task.md). Para uma visão geral das tarefas ACR, consulte [automatizar o SISTEMA e a correção de enquadramento com tarefas ACR](container-registry-tasks-overview.md)

Neste tutorial:

> [!div class="checklist"]
> * Criar uma tarefa
> * Testar a tarefa
> * Ver estado da tarefa
> * Acionar a tarefa com uma consolidação do código

Este tutorial parte do princípio de que já concluiu os passos no [tutorial anterior](container-registry-tutorial-quick-task.md). Se ainda não o fez, conclua os passos na secção [Pré-requisitos](container-registry-tutorial-quick-task.md#prerequisites) do tutorial anterior antes de continuar.

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]
[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="create-the-build-task"></a>Criar a tarefa de compilação

Agora que concluiu os passos necessários para ativar o ACR Tasks para ler o estado de consolidação e criar webhooks num repositório, pode criar uma tarefa de compilação que acione uma compilação da imagem do contentor nas consolidações para o repositório.

Em primeiro lugar, preencha estas variáveis de ambiente da shell com os valores adequados para o seu ambiente. Este passo não é estritamente necessário, mas facilita um pouco a execução dos comandos da CLI do Azure com várias linhas neste tutorial. Se não preencher estas variáveis ambientais, deve substituir manualmente cada valor onde quer que apareça nos comandos de exemplo.

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Agora, crie a tarefa executando a seguinte [tarefa az acr criar][az-acr-task-create] comando:

```azurecli
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile \
    --git-access-token $GIT_PAT
```


Esta tarefa especifica que qualquer código de tempo é comprometido com o ramo *principal* no repositório especificado por `--context` , ACR Tasks irá construir a imagem do contentor a partir do código nesse ramo. O Dockerfile especificado pela `--file` raiz do repositório é usado para construir a imagem. O argumento `--image` especifica um valor com parâmetros de `{{.Run.ID}}` para a parte da versão da etiqueta da imagem, o que garante que a imagem compilada está correlacionada com uma compilação específica e é etiquetada de forma exclusiva.

O resultado de um comando [az acr task create][az-acr-task-create] com êxito é semelhante ao seguinte:

```output
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2010-11-19T22:42:32.972298+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskhelloworld",
  "location": "westcentralus",
  "name": "taskhelloworld",
  "platform": {
    "architecture": "amd64",
    "os": "Linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myregistry",
  "status": "Enabled",
  "step": {
    "arguments": [],
    "baseImageDependencies": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node",
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Run.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "type": "Docker"
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "main",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
          "sourceControlAuthProperties": null,
          "sourceControlType": "GitHub"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-build-task"></a>Testar a tarefa de compilação

Tem agora uma tarefa que define a sua compilação. Para testar o pipeline da compilação, acione manualmente uma compilação ao executar o comando[az acr build-task run][az-acr-task-run]:

```azurecli
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Por predefinição, o comando `az acr task run` transmite a saída de registo para a consola quando executar o comando. A saída é condensada para mostrar os passos-chave.

```output
2020/11/19 22:51:00 Using acb_vol_9ee1f28c-4fd4-43c8-a651-f0ed027bbf0e as the home volume
2020/11/19 22:51:00 Setting up Docker configuration...
2020/11/19 22:51:02 Successfully set up Docker configuration
2020/11/19 22:51:02 Logging in to registry: myregistry.azurecr.io
2020/11/19 22:51:03 Successfully logged in
2020/11/19 22:51:03 Executing step: build
2020/11/19 22:51:03 Obtaining source code and scanning for dependencies...
2020/11/19 22:51:05 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  23.04kB
Step 1/5 : FROM node:15-alpine
[...]
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 7382eea2a56a
Removing intermediate container 7382eea2a56a
 ---> e33cd684027b
Successfully built e33cd684027b
Successfully tagged myregistry.azurecr.io/helloworld:da2
2020/11/19 22:51:11 Executing step: push
2020/11/19 22:51:11 Pushing image: myregistry.azurecr.io/helloworld:da2, attempt 1
The push refers to repository [myregistry.azurecr.io/helloworld]
4a853682c993: Preparing
[...]
4a853682c993: Pushed
[...]
da2: digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419 size: 1366
2020/11/19 22:51:21 Successfully pushed image: myregistry.azurecr.io/helloworld:da2
2020/11/19 22:51:21 Step id: build marked as successful (elapsed time in seconds: 7.198937)
2020/11/19 22:51:21 Populating digests for step id: build...
2020/11/19 22:51:22 Successfully populated digests for step id: build
2020/11/19 22:51:22 Step id: push marked as successful (elapsed time in seconds: 10.180456)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloworld
    tag: da2
    digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 68cdf2a37cdae0873b8e2f1c4d80ca60541029bf


Run ID: ca6 was successful after 27s
```

## <a name="trigger-a-build-with-a-commit"></a>Acionar uma compilação com uma consolidação

Agora que testou a tarefa através de uma execução manual, acione-a automaticamente com uma alteração do código de origem.

Em primeiro lugar, confirme que está no diretório que contém o clone local do [repositório][sample-repo]:

```console
cd acr-build-helloworld-node
```

Em seguida, execute os comandos seguintes para criar, consolidar e emitir um novo ficheiro para a bifurcação do repositório no GitHub:

```console
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin main
```

Poderá ser-lhe pedido para fornecer as credenciais do GitHub, quando executar o comando `git push`. Forneça o seu nome de utilizador do GitHub e introduza o token de acesso pessoal (PAT) que criou anteriormente para a palavra-passe.

```azurecli
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Assim que tiver enviado a consolidação para o repositório, o webhook criado pelo ACR Tasks é acionado e é iniciada uma compilação no Azure Container Registry. Apresente os registos da compilação atualmente em execução para verificar e monitorizar o progresso da mesma:

```azurecli
az acr task logs --registry $ACR_NAME
```

O resultado é semelhante ao seguinte, apresentando a compilação atualmente em execução (ou a última executada):

```output
Showing logs of the last created run.
Run ID: ca7

[...]

Run ID: ca7 was successful after 38s
```

## <a name="list-builds"></a>Listar as compilações

Para ver uma lista das execuções de tarefas que o ACR Tasks concluiu para o seu registo, execute o comando [az acr task list-runs][az-acr-task-list-runs]:

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

O resultado do comando deve ter um aspeto semelhante ao que se segue. As execuções do ACR Tasks são apresentadas e “Consolidação de Git” aparece na coluna ACIONAR da tarefa mais recente:

```output
RUN ID    TASK            PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  --------------  ----------  ---------  ---------  --------------------  ----------
ca7       taskhelloworld  linux       Succeeded  Commit     2020-11-19T22:54:34Z  00:00:29
ca6       taskhelloworld  linux       Succeeded  Manual     2020-11-19T22:51:47Z  00:00:24
ca5                       linux       Succeeded  Manual     2020-11-19T22:23:42Z  00:00:23
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar uma tarefa para acionar automaticamente compilações da imagem do contentor no Azure quando consolida o código de origem para um repositório de Git. Avance para o tutorial seguinte para saber como criar tarefas que acionam compilações quando a imagem de base de uma imagem do contentor é atualizada.

> [!div class="nextstepaction"]
> [Automatizar compilações ao atualizar a imagem de base](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-login]: /cli/azure/reference-index#az-login



