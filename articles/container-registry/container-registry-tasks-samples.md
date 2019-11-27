---
title: Amostras de tarefa ACR
description: Exemplos de tarefas do registro de contêiner do Azure (tarefas ACR) para compilar, executar e corrigir imagens de contêiner
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456093"
---
# <a name="acr-tasks-samples"></a>Exemplos de tarefas ACR

Este artigo fornece links para exemplos de `task.yaml` arquivos e Dockerfiles associados para vários cenários de [tarefas de registro de contêiner do Azure](container-registry-tasks-overview.md) (tarefas ACR). 

Para obter exemplos adicionais, consulte o repositório de [exemplos do Azure][task-examples] .

## <a name="scenarios"></a>Cenários

* **Criar imagem** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Executar contêiner** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **Criar e enviar por push a imagem** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Criar e executar a imagem** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Criar e enviar por push várias imagens** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Criar e testar imagens em paralelo** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Crie e envie imagens por push para vários registros** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as tarefas do ACR:

* [Tarefas de várias etapas](container-registry-tasks-multi-step.md) – fluxos de trabalho baseados em tarefas de ACR para compilar, testar e aplicar patches em imagens de contêiner na nuvem.
* [Referência de tarefa](container-registry-tasks-reference-yaml.md) -tipos de etapa de tarefa, suas propriedades e uso.
* [Repositório cmd](https://github.com/AzureCR/cmd) – uma coleção de contêineres como comandos para tarefas ACR.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks
