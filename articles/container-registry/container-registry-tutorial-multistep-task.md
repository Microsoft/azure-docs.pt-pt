---
title: Tutorial - tarefas de vários passos contentor - Azure Container Registry tarefas
description: Neste tutorial, saiba como configurar uma tarefa de registo de contentor do Azure para automaticamente acionar um fluxo de trabalho de vários passo para criar, executar e colocar imagens de contentor na cloud ao consolidar o código-fonte para um repositório de Git.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 09b8e5d31bc6a4ec24633889920e2768bb7ce538
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546574"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Tutorial: Executar um fluxo de trabalho do contentor de vários passos na cloud ao consolidar o código-fonte

Com um [tarefas rápidas](container-registry-tutorial-quick-task.md), tarefas de ACR suporta vários passos, baseadas em container vários fluxos de trabalho podem acionar automaticamente ao consolidar código-fonte para um repositório de Git. 

Neste tutorial, saiba como utilizar os ficheiros YAML de exemplo para definir as tarefas de vários passos que criam, executar e enviar uma ou mais imagens de contentor para um registo ao consolidar o código-fonte. Para criar uma tarefa que automatiza a apenas uma única imagem se baseiam na consolidação de código, consulte [Tutorial: Automatizar compilações de imagem de contentor na cloud ao consolidar o código-fonte](container-registry-tutorial-build-task.md). Para uma descrição geral das tarefas de ACR, consulte [automatizar o sistema operacional e a aplicação de patches de estrutura com tarefas do ACR](container-registry-tasks-overview.md),

Neste tutorial:

> [!div class="checklist"]
> * Definir uma tarefa de várias etapa usando um ficheiro YAML
> * Criar uma tarefa
> * Opcionalmente, adicione as credenciais para a tarefa para ativar o acesso a outro registo
> * Testar a tarefa
> * Ver estado da tarefa
> * Acionar a tarefa com uma consolidação do código

Este tutorial parte do princípio de que já concluiu os passos no [tutorial anterior](container-registry-tutorial-quick-task.md). Se ainda não o fez, conclua os passos na secção [Pré-requisitos](container-registry-tutorial-quick-task.md#prerequisites) do tutorial anterior antes de continuar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se gostaria de utilizar a CLI do Azure localmente, tem de ter versão da CLI do Azure **2.0.62** ou posterior instalada e com sessão iniciada [início de sessão az][az-login]. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI do Azure][azure-cli].

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-a-multi-step-task"></a>Criar uma tarefa de vários passo

Agora que concluiu os passos necessários para ativar tarefas de ACR para ler o status de consolidação e criar webhooks num repositório, crie uma tarefa de várias etapa que aciona a criação, execução e envio de uma imagem de contentor.

### <a name="yaml-file"></a>Ficheiro YAML

Define os passos para uma tarefa de várias etapa numa [ficheiro YAML](container-registry-tasks-reference-yaml.md). A primeira tarefa de vários passos de exemplo para este tutorial é definida no ficheiro `taskmulti.yaml`, que se encontra na raiz do repositório do GitHub que clonou:

```yml
version: v1.0.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

Esta tarefa de várias etapa faz o seguinte:

1. Execuções de um `build` passo para criar uma imagem a partir do Dockerfile no diretório de trabalho. Os destinos de imagem a `Run.Registry`, o registo em que a tarefa é executada e é marcada com um único tarefas de ACR executar ID. 
1. Execuções de um `cmd` passo para executar a imagem num contentor temporário. Neste exemplo inicia um contentor de longa execução em segundo plano e retorna a ID de contentor, em seguida, para o contentor. Num cenário do mundo real, pode incluir passos para testar o contentor em execução para garantir que ele é executado corretamente.
1. Num `push` passo, envia a imagem que foi criada para o registo de execução.

### <a name="task-command"></a>Comando de tarefa

Em primeiro lugar, preencha estas variáveis de ambiente da shell com os valores adequados para o seu ambiente. Este passo não é estritamente necessário, mas facilita um pouco a execução dos comandos da CLI do Azure com várias linhas neste tutorial. Se não preencher estas variáveis de ambiente, tem de substituir manualmente cada valor onde quer que aparece nos comandos de exemplo.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Agora, crie a tarefa executando o seguinte [az acr tarefa criar] [ az-acr-task-create] comando:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Esta tarefa Especifica que qualquer código de tempo está comprometido a *mestre* ramo no repositório especificado pelo `--context`, tarefas de ACR será executada a tarefa de várias etapa do código nessa sucursal. O ficheiro YAML especificado pelo `--file` do repositório raiz define os passos. 

O resultado de um comando [az acr task create][az-acr-task-create] com êxito é semelhante ao seguinte:

```console
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2019-05-03T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "westus",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
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
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
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

## <a name="test-the-multi-step-workflow"></a>Testar o fluxo de trabalho de vários passo

Para testar a tarefa de várias etapa, dispará-lo manualmente ao executar o [execução da tarefa az acr] [ az-acr-task-run] comando:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Por predefinição, o comando `az acr task run` transmite a saída de registo para a consola quando executar o comando. A saída mostra o progresso da execução de cada um dos passos de tarefas. O resultado abaixo é resumido para mostrar as etapas principais.

```console
Queued a run with ID: cf19
Waiting for an agent...
2019/05/03 03:03:31 Downloading source code...
2019/05/03 03:03:33 Finished downloading source code
2019/05/03 03:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2019/05/03 03:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 03:03:34 Successfully set up Docker network: acb_default_network
2019/05/03 03:03:34 Setting up Docker configuration...
2019/05/03 03:03:34 Successfully set up Docker configuration
2019/05/03 03:03:34 Logging in to registry: myregistry.azurecr.io
2019/05/03 03:03:35 Successfully logged into myregistry.azurecr.io
2019/05/03 03:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:35 Scanning for dependencies...
2019/05/03 03:03:36 Successfully scanned dependencies
2019/05/03 03:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB

[...]

Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:43 Successfully executed container: acb_step_0
2019/05/03 03:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2019/05/03 03:03:44 Successfully executed container: acb_step_1
2019/05/03 03:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1

[...]

2019/05/03 03:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2019/05/03 03:03:46 Populating digests for step ID: acb_step_0...
2019/05/03 03:03:47 Successfully populated digests for step ID: acb_step_0
2019/05/03 03:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2019/05/03 03:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2019/05/03 03:03:47 The following dependencies were found:
2019/05/03 03:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cf19 was successful after 18s
```

## <a name="trigger-a-build-with-a-commit"></a>Acionar uma compilação com uma consolidação

Agora que testou a tarefa através de uma execução manual, acione-a automaticamente com uma alteração do código de origem.

Em primeiro lugar, confirme que está no diretório que contém o clone local do [repositório][sample-repo]:

```azurecli-interactive
cd acr-build-helloworld-node
```

Em seguida, execute os comandos seguintes para criar, consolidar e emitir um novo ficheiro para a bifurcação do repositório no GitHub:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

Poderá ser-lhe pedido para fornecer as credenciais do GitHub, quando executar o comando `git push`. Forneça o seu nome de utilizador do GitHub e introduza o token de acesso pessoal (PAT) que criou anteriormente para a palavra-passe.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Uma vez que já enviou uma consolidação para o seu repositório, o webhook criado pelas tarefas de ACR é acionado e inicia a tarefa no Azure Container Registry. Apresente os registos da compilação atualmente em execução para verificar e monitorizar o progresso da mesma:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

O resultado é semelhante ao seguinte, apresentando a compilação atualmente em execução (ou a última executada):

```console
$ az acr task logs --registry $ACR_NAME
Showing logs of the last created run.
Run ID: cf1d

[...]

Run ID: cf1d was successful after 37s
```

## <a name="list-builds"></a>Listar as compilações

Para ver uma lista das execuções de tarefas que o ACR Tasks concluiu para o seu registo, execute o comando [az acr task list-runs][az-acr-task-list-runs]:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

O resultado do comando deve ter um aspeto semelhante ao que se segue. As execuções do ACR Tasks são apresentadas e “Consolidação de Git” aparece na coluna ACIONAR da tarefa mais recente:

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
cf1d      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:37
cf1c      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:39
cf1b      example1   linux       Succeeded  Manual     2019-05-03T03:10:30Z  00:00:31
cf1a      example1   linux       Succeeded  Commit     2019-05-03T03:09:32Z  00:00:31
cf19      example1   linux       Succeeded  Manual     2019-05-03T03:03:30Z  00:00:21
```

## <a name="create-a-multi-registry-multi-step-task"></a>Criar uma tarefa de várias etapa de registro multi

Tarefas de ACR por padrão tem permissões para emitir ou solicitar imagens do registo em que a tarefa é executada. Pode querer executar uma tarefa de vários passo que se destina a um ou mais registos, além do registo de execução. Por exemplo, poderá ter de criar imagens no registo de um e armazenar imagens com etiquetas diferentes de um segundo registo que é acedido por um sistema de produção. Este exemplo mostra como criar uma tarefa desse tipo e forneça as credenciais para o outro registo.

Se ainda não tiver um segundo registo, crie uma para este exemplo. Se precisar de um registo, consulte a [tutorial anterior](container-registry-tutorial-quick-task.md), ou [início rápido: Criar um registo de contentor com a CLI do Azure](container-registry-get-started-azure-cli.md).

Para criar a tarefa, precisa do nome do servidor de início de sessão de registo, que é o formato *mycontainerregistrydate.azurecr.io* (em minúsculas). Neste exemplo, vai utilizar o segundo registo para armazenar imagens etiquetadas por data de compilação.

### <a name="yaml-file"></a>Ficheiro YAML

A segunda tarefa de vários passos de exemplo para este tutorial é definida no ficheiro `taskmulti-multiregistry.yaml`, que se encontra na raiz do repositório do GitHub que clonou:

```yml
version: v1.0.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

Esta tarefa de várias etapa faz o seguinte:

1. Executa dois `build` passos para criar imagens a partir do Dockerfile no diretório de trabalho:
    * A primeira tem como destino o `Run.Registry`, o registo em que a tarefa é executada e é marcada com as tarefas de ACR executar ID. 
    * O segundo destina-se o registro identificado pelo valor de `regDate`, que definiu quando criou a tarefa (ou fornecer por meio de uma externa `values.yaml` arquivo passado para `az acr task create`). Esta imagem é marcada com a data de execução.
1. Execuções de um `cmd` passo para executar um dos contentores incorporados. Neste exemplo inicia um contentor de longa execução em segundo plano e retorna a ID de contentor, em seguida, para o contentor. Num cenário do mundo real, pode testar um contentor em execução para garantir que ele é executado corretamente.
1. Num `push` passo, envia as imagens que tenham sido criados, o primeiro para o registo de execução, o segundo para o registo identificado pelo `regDate`.

### <a name="task-command"></a>Comando de tarefa

Com as variáveis de ambiente de shell definidas anteriormente, criar a tarefa executando o seguinte [az acr tarefa criar] [ az-acr-task-create] comando. Substitua o nome do seu registo para *mycontainerregistrydate*.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file taskmulti-image.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Adicionar credenciais de tarefa

Para imagens de push para o registro identificado pelo valor de `regDate`, utilize o [adicionar credenciais de tarefa az acr] [ az-acr-task-credential-add] comando para adicionar credenciais de início de sessão para esse registo para a tarefa.

Neste exemplo, recomendamos que crie uma [principal de serviço](container-registry-auth-service-principal.md) com o acesso ao registo de um âmbito para o *AcrPush* função. Para criar o principal de serviço, veja este [script da CLI do Azure](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh).

Transmita o ID da aplicação principal de serviço e a palavra-passe na seguinte `az acr task credential add` comando:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

A CLI devolve o nome do servidor de início de sessão do registo que adicionou.

### <a name="test-the-multi-step-workflow"></a>Testar o fluxo de trabalho de vários passo

Como no exemplo anterior, para testar a tarefa de várias etapa, dispará-lo manualmente ao executar o [execução da tarefa az acr] [ az-acr-task-run] comando. Para acionar a tarefa com uma consolidação para o repositório de Git, consulte a secção [acionar uma compilação com uma consolidação](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Por predefinição, o comando `az acr task run` transmite a saída de registo para a consola quando executar o comando. Como antes, a saída mostra o progresso da execução de cada um dos passos de tarefas. A saída é resumida para mostrar as etapas principais

Saída:

```console
Queued a run with ID: cf1g
Waiting for an agent...
2019/05/03 04:33:39 Downloading source code...
2019/05/03 04:33:41 Finished downloading source code
2019/05/03 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2019/05/03 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 04:33:43 Successfully set up Docker network: acb_default_network
2019/05/03 04:33:43 Setting up Docker configuration...
2019/05/03 04:33:44 Successfully set up Docker configuration
2019/05/03 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:47 Scanning for dependencies...
2019/05/03 04:33:47 Successfully scanned dependencies
2019/05/03 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:33:55 Successfully executed container: acb_step_0
2019/05/03 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:55 Scanning for dependencies...
2019/05/03 04:33:56 Successfully scanned dependencies
2019/05/03 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:33:57 Successfully executed container: acb_step_1
2019/05/03 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2019/05/03 04:33:58 Successfully executed container: acb_step_2
2019/05/03 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:58 Launching container with name: acb_step_3
test
2019/05/03 04:34:09 Successfully executed container: acb_step_3
2019/05/03 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]

[...]

2019/05/03 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]

[...]

2019/05/03 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2019/05/03 04:34:19 Populating digests for step ID: acb_step_0...
2019/05/03 04:34:21 Successfully populated digests for step ID: acb_step_0
2019/05/03 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2019/05/03 04:34:21 Populating digests for step ID: acb_step_1...
2019/05/03 04:34:23 Successfully populated digests for step ID: acb_step_1
2019/05/03 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2019/05/03 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2019/05/03 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2019/05/03 04:34:23 The following dependencies were found:
2019/05/03 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.io
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a criar com vários passos, baseada na container de várias tarefas que acionam automaticamente ao consolidar o código-fonte para um repositório de Git. Para funcionalidades avançadas de tarefas de várias etapas, incluindo a execução paralela e dependentes passo, consulte a [referência de ACR tarefas YAML](container-registry-tasks-reference-yaml.md). Avance para o tutorial seguinte para saber como criar tarefas que acionam compilações quando a imagem de base de uma imagem do contentor é atualizada.

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
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add    
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
