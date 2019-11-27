---
title: Tutorial – tarefa de ACR de várias etapas
description: Neste tutorial, você aprenderá a configurar uma tarefa de registro de contêiner do Azure para disparar automaticamente um fluxo de trabalho de várias etapas para compilar, executar e enviar por push imagens de contêiner na nuvem quando você confirmar o código-fonte em um repositório git.
ms.topic: tutorial
ms.date: 05/09/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: f5342e51af870b1e5f8651ea2d28894233ed8e62
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456069"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Tutorial: executar um fluxo de trabalho de contêiner de várias etapas na nuvem quando você confirmar o código-fonte

Além de uma [tarefa rápida](container-registry-tutorial-quick-task.md), as tarefas ACR dão suporte a fluxos de trabalho de várias etapas com base em vários contêineres que podem disparar automaticamente quando você confirma o código-fonte para um repositório git. 

Neste tutorial, você aprenderá a usar arquivos de exemplo YAML para definir tarefas de várias etapas que criam, executam e enviam por push uma ou mais imagens de contêiner para um registro quando você confirma o código-fonte. Para criar uma tarefa que só automatiza uma única compilação de imagem na confirmação de código, consulte [tutorial: automatizar compilações de imagem de contêiner na nuvem quando você confirma o código-fonte](container-registry-tutorial-build-task.md). Para obter uma visão geral das tarefas de ACR, consulte [automatizar o sistema operacional e a aplicação de patch de estrutura com tarefas ACR](container-registry-tasks-overview.md),

Neste tutorial:

> [!div class="checklist"]
> * Definir uma tarefa de várias etapas usando um arquivo YAML
> * Criar uma tarefa
> * Opcionalmente, adicione credenciais à tarefa para habilitar o acesso a outro registro
> * Testar a tarefa
> * Ver estado da tarefa
> * Acionar a tarefa com uma consolidação do código

Este tutorial parte do princípio de que já concluiu os passos no [tutorial anterior](container-registry-tutorial-quick-task.md). Se ainda não o fez, conclua os passos na secção [Pré-requisitos](container-registry-tutorial-quick-task.md#prerequisites) do tutorial anterior antes de continuar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você quiser usar o CLI do Azure localmente, deverá ter CLI do Azure versão **2.0.62** ou posterior instalada e conectada com [AZ login][az-login]. Executar `az --version` para localizar a versão. Se você precisar instalar ou atualizar a CLI, consulte [instalar CLI do Azure][azure-cli].

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-a-multi-step-task"></a>Criar uma tarefa de várias etapas

Agora que você concluiu as etapas necessárias para habilitar as tarefas ACR para ler o status de confirmação e criar WebHooks em um repositório, crie uma tarefa de várias etapas que dispara a criação, execução e envio por push de uma imagem de contêiner.

### <a name="yaml-file"></a>Ficheiro Yaml

Você define as etapas para uma tarefa de várias etapas em um [arquivo YAML](container-registry-tasks-reference-yaml.md). O primeiro exemplo de tarefa de várias etapas para este tutorial é definido no arquivo `taskmulti.yaml`, que está na raiz do repositório GitHub clonado:

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

Essa tarefa de várias etapas faz o seguinte:

1. Executa uma etapa `build` para criar uma imagem do Dockerfile no diretório de trabalho. A imagem tem como destino o `Run.Registry`, o registro em que a tarefa é executada e é marcada com uma ID de execução de tarefas exclusivas do ACR. 
1. Executa uma etapa `cmd` para executar a imagem em um contêiner temporário. Este exemplo inicia um contêiner de execução longa em segundo plano e retorna a ID do contêiner e, em seguida, para o contêiner. Em um cenário do mundo real, você pode incluir etapas para testar o contêiner em execução para garantir que ele seja executado corretamente.
1. Em uma etapa `push`, envia a imagem que foi criada para o registro de execução.

### <a name="task-command"></a>Comando de tarefa

Em primeiro lugar, preencha estas variáveis de ambiente da shell com os valores adequados para o seu ambiente. Este passo não é estritamente necessário, mas facilita um pouco a execução dos comandos da CLI do Azure com várias linhas neste tutorial. Se você não preencher essas variáveis de ambiente, será necessário substituir manualmente cada valor sempre que ele aparecer nos comandos de exemplo.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Agora, crie a tarefa executando o seguinte comando [AZ ACR Task Create][az-acr-task-create] :

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Essa tarefa especifica que qualquer código de tempo seja confirmado no Branch *mestre* no repositório especificado por `--context`, as tarefas de ACR executarão a tarefa de várias etapas do código nessa ramificação. O arquivo YAML especificado por `--file` da raiz do repositório define as etapas. 

A saída de um comando de [criação de tarefa AZ ACR][az-acr-task-create] com êxito é semelhante ao seguinte:

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

## <a name="test-the-multi-step-workflow"></a>Testar o fluxo de trabalho de várias etapas

Para testar a tarefa de várias etapas, dispare-a manualmente executando o comando [AZ ACR Task execute][az-acr-task-run] :

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Por predefinição, o comando `az acr task run` transmite a saída de registo para a consola quando executar o comando. A saída mostra o andamento da execução de cada uma das etapas da tarefa. A saída abaixo é condensada para mostrar as etapas principais.

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

Primeiro, verifique se você está no diretório que contém o clone local do [repositório][sample-repo]:

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

Depois de enviar por push uma confirmação para o repositório, o webhook criado por tarefas ACR é acionado e inicia a tarefa no registro de contêiner do Azure. Apresente os registos da compilação atualmente em execução para verificar e monitorizar o progresso da mesma:

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

Para ver uma lista de tarefas executadas que as tarefas ACR foram concluídas para o registro, execute o comando [AZ ACR Task List-executes][az-acr-task-list-runs] :

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

## <a name="create-a-multi-registry-multi-step-task"></a>Criar uma tarefa de várias etapas de vários registros

Por padrão, as tarefas ACR têm permissões para enviar por Push ou efetuar pull de imagens do registro em que a tarefa é executada. Talvez você queira executar uma tarefa de várias etapas que tenha como alvo um ou mais registros, além do registro de execução. Por exemplo, talvez seja necessário criar imagens em um registro e armazenar imagens com diferentes marcas em um segundo registro que é acessado por um sistema de produção. Este exemplo mostra como criar uma tarefa desse tipo e fornecer credenciais para outro registro.

Se você ainda não tiver um segundo registro, crie um para este exemplo. Se precisar de um registo, veja o [tutorial anterior](container-registry-tutorial-quick-task.md) ou [Início Rápido: Criar um registo de contentor com a CLI do Azure](container-registry-get-started-azure-cli.md).

Para criar a tarefa, você precisa do nome do servidor de logon do registro, que está no formato *mycontainerregistrydate.azurecr.Io* (todas as letras minúsculas). Neste exemplo, você usa o segundo registro para armazenar imagens marcadas por data de compilação.

### <a name="yaml-file"></a>Ficheiro Yaml

O segundo exemplo de tarefa de várias etapas para este tutorial é definido no arquivo `taskmulti-multiregistry.yaml`, que está na raiz do repositório GitHub clonado:

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

Essa tarefa de várias etapas faz o seguinte:

1. Executa duas `build` etapas para criar imagens do Dockerfile no diretório de trabalho:
    * O primeiro se destina ao `Run.Registry`, o registro em que a tarefa é executada e é marcado com a ID de execução de tarefas do ACR. 
    * O segundo tem como alvo o registro identificado pelo valor de `regDate`, que você define ao criar a tarefa (ou fornecer por meio de um arquivo de `values.yaml` externo passado para `az acr task create`). Esta imagem é marcada com a data de execução.
1. Executa uma etapa `cmd` para executar um dos contêineres compilados. Este exemplo inicia um contêiner de execução longa em segundo plano e retorna a ID do contêiner e, em seguida, para o contêiner. Em um cenário do mundo real, você pode testar um contêiner em execução para garantir que ele seja executado corretamente.
1. Em uma etapa `push`, envia as imagens que foram criadas, a primeira para o registro de execução, a segunda para o registro identificado por `regDate`.

### <a name="task-command"></a>Comando de tarefa

Usando as variáveis de ambiente do Shell definidas anteriormente, crie a tarefa executando o comando [AZ ACR Task Create][az-acr-task-create] a seguir. Substitua o nome do registro por *mycontainerregistrydate*.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file taskmulti-multiregistry.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Adicionar credencial de tarefa

Para enviar imagens por push para o registro identificado pelo valor de `regDate`, use o comando [AZ ACR Task Credential Add][az-acr-task-credential-add] para adicionar credenciais de logon para esse registro à tarefa.

Para este exemplo, recomendamos que você crie uma [entidade de serviço](container-registry-auth-service-principal.md) com acesso ao registro no escopo da função *AcrPush* . Para criar a entidade de serviço, consulte este [CLI do Azure script](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh).

Passe a ID do aplicativo da entidade de serviço e a senha no seguinte comando `az acr task credential add`:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

A CLI retorna o nome do servidor de logon do registro que você adicionou.

### <a name="test-the-multi-step-workflow"></a>Testar o fluxo de trabalho de várias etapas

Como no exemplo anterior, para testar a tarefa de várias etapas, acione-a manualmente executando o comando [AZ ACR Task execute][az-acr-task-run] . Para disparar a tarefa com uma confirmação para o repositório git, consulte a seção [disparar uma compilação com uma confirmação](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Por predefinição, o comando `az acr task run` transmite a saída de registo para a consola quando executar o comando. Como antes, a saída mostra o andamento da execução de cada uma das etapas da tarefa. A saída é condensada para mostrar as etapas principais.

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

Neste tutorial, você aprendeu a criar tarefas de várias etapas com base em vários contêineres que são disparadas automaticamente quando você confirma o código-fonte para um repositório git. Para obter recursos avançados de tarefas de várias etapas, incluindo a execução de etapas paralelas e dependentes, consulte a [referência de YAML de tarefas do ACR](container-registry-tasks-reference-yaml.md). Avance para o tutorial seguinte para saber como criar tarefas que acionam compilações quando a imagem de base de uma imagem do contentor é atualizada.

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
