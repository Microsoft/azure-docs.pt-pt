---
title: Amostras de tarefas ACR
description: Experimente as tarefas de registo de contentores do Azure (Tarefas ACR) para construir, executar e remendar imagens de contentores
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "74456093"
---
# <a name="acr-tasks-samples"></a>Amostras de tarefas ACR

Este artigo liga-se a ficheiros de exemplo `task.yaml` e ficheiros de Docker associados para [vários cenários de tarefas de registo de contentores Azure](container-registry-tasks-overview.md) (Tarefas ACR). 

Para outros exemplos, consulte as [amostras Azure][task-examples] repo.

## <a name="scenarios"></a>Cenários

* **Construir imagem**  -  [YAML,](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml) [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Recipiente de execução**  -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **Construir e empurrar imagem**  -  [YAML,](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml) [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Construir e executar imagem**  -  [YAML,](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml) [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Construir e empurrar várias imagens**  -   [YAML,](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml) [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Construir e testar imagens em paralelo**  -   [YAML,](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml) [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Construir e empurrar imagens para vários registos**  -  [YAML,](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml) [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as Tarefas ACR:

* [Tarefas em várias etapas](container-registry-tasks-multi-step.md) - Fluxos de trabalho baseados em tarefas ACR para construir, testar e remendar imagens de contentores na nuvem.
* [Referência de tarefa](container-registry-tasks-reference-yaml.md) - Tipos de etapas de tarefa, suas propriedades e utilização.
* [Cmd repo](https://github.com/AzureCR/cmd) - Uma coleção de contentores como comandos para tarefas ACR.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
