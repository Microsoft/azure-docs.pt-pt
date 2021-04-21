---
title: Ver registos de execução de tarefas - Tarefas
description: Como visualizar e gerir registos de execução gerados por Tarefas ACR.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: ce5f33853be2aa48bcfd1916c7f8b94b9702f38c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781060"
---
# <a name="view-and-manage-task-run-logs"></a>Ver e gerir registos de execução de tarefas

Cada tarefa executada em [tarefas de registo de contentores Azure](container-registry-tasks-overview.md) gera saída de registo que pode inspecionar para determinar se os passos de tarefa foram executados com sucesso. 

Este artigo explica como visualizar e gerir registos de execução de tarefas.

## <a name="view-streamed-logs"></a>Ver registos streamed

Quando ativa uma tarefa manualmente, a saída de registo é transmitida diretamente para a consola. Por exemplo, quando ativar uma tarefa manualmente utilizando o [az acr build](/cli/azure/acr#az_acr_build), [az acr run](/cli/azure/acr#az_acr_run), ou o comando [de execução de tarefas az acr,](/cli/azure/acr/task#az_acr_task_run) vê a saída de registo transmitida para a consola. 

O comando [de execução a az acr](/cli/azure/acr#az_acr_run) da amostra seguinte aciona manualmente uma tarefa que executa um contentor retirado do mesmo registo:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Diário de streaming:

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>Ver registos armazenados 

As lojas de registo de contentores Azure executam registos para todas as tarefas. Pode visualizar registos de execução armazenados no portal Azure. Ou, use o comando [de registos de tarefas az acr](/cli/azure/acr/task#az_acr_task_logs) para visualizar um registo selecionado. Por predefinição, os registos são mantidos por 30 dias.

Se uma tarefa for automaticamente ativada, por exemplo através de uma atualização de código fonte, aceder aos registos armazenados é a *única* forma de visualizar os registos de execução. Os detonadores automáticos de tarefas incluem compromissos de código fonte ou solicitações de puxar, atualizações de imagem base e gatilhos de temporizador.

Para visualizar registos de execução no portal:

1. Navegue para o seu registo de contentores.
1. Em **Serviços**, selecione **Tasks**  >  **Runs**.
1. Selecione um **Run Id** para visualizar o estado de execução e executar registos. O registo contém a mesma informação que um registo transmitido, se um for gerado.

![Ver portal de login executado por tarefas](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Para visualizar um registo utilizando o CLI Azure, executar [registos de tarefas az acr](/cli/azure/acr/task#az_acr_task_logs) e especificar um ID de execução, um nome de tarefa ou uma imagem específica criar por uma tarefa de construção. Se um nome de tarefa for especificado, o comando mostra o registo para a última execução criada.

O exemplo a seguir sai o log para a execução com iD *cf4*:

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Armazenamento alternativo de registos

É possível que pretenda armazenar registos de execução de tarefas num sistema de ficheiros local ou utilizar uma solução alternativa de arquivamento, como o Azure Storage.

Por exemplo, crie um diretório de *tasklogs* local e redirecione a saída dos registos de [tarefas az acr](/cli/azure/acr/task#az_acr_task_logs) para um ficheiro local:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

Também pode guardar ficheiros de registo local para o Azure Storage. Por exemplo, utilize o [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md), o [portal Azure,](../storage/blobs/storage-quickstart-blobs-portal.md)ou outros métodos para enviar ficheiros para uma conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre as [Tarefas de Registo de Contentores do Azure](container-registry-tasks-overview.md)


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-login]: /cli/azure/reference-index#az_login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
