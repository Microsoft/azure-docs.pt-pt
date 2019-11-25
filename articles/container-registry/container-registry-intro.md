---
title: Managed container registries
description: Introdução ao serviço de Registo de Contentores do Azure, que proporciona registos do Docker baseado na cloud, geridos e privados.
author: stevelas
ms.topic: overview
ms.date: 06/28/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: fceaf312f35356acf18c0873a467ed43d11ddd83
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454938"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introdução aos registos privados de contentores Docker no Azure

Azure Container Registry is a managed, private Docker registry service based on the open-source Docker Registry 2.0. Create and maintain Azure container registries to store and manage your private Docker container images.

Use Azure container registries with your existing container development and deployment pipelines, or use Azure Container Registry Tasks to build container images in Azure. Build on demand, or fully automate builds with triggers such as source code commits and base image updates.

For more about Docker and registry concepts, see the [Docker overview](https://docs.docker.com/engine/docker-overview/) and [About registries, repositories, and images](container-registry-concepts.md).

## <a name="use-cases"></a>Casos de utilização

Extrair imagens de um registo de contentores do Azure para vários destinos de implementações:

* **Sistemas de orquestração dimensionáveis** que gerem aplicações em contentores através de clusters de anfitriões, incluindo [Kubernetes](https://kubernetes.io/docs/), [CD/SO](https://docs.mesosphere.com/) e [Docker Swarm](https://docs.docker.com/swarm/).
* **Serviços do Azure** que suportam a criação e a execução de aplicações em escala, incluindo o [Azure Kubernetes Service (AKS)](../aks/index.yml), o [Serviço de Aplicações](../app-service/index.yml), o [Batch](../batch/index.yml), o [Service Fabric](/azure/service-fabric/) e outros.

Os programadores também podem enviar para um registo de contentores como parte de um fluxo de trabalho de desenvolvimento de contentores. For example, target a container registry from a continuous integration and delivery tool such as [Azure Pipelines](/azure/devops/pipelines/ecosystems/containers/acr-template) or [Jenkins](https://jenkins.io/).

Configure ACR Tasks to automatically rebuild application images when their base images are updated, or automate image builds when your team commits code to a Git repository. Create multi-step tasks to automate building, testing, and patching multiple container images in parallel in the cloud.

Azure provides tooling including Azure Command-Line Interface, Azure portal, and API support to manage your Azure container registries. Optionally install the [Docker Extension for Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) and the [Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) extension to work with your Azure container registries. Pull and push images to an Azure container registry, or run ACR Tasks, all within Visual Studio Code.

## <a name="key-features"></a>Principais funcionalidades

* **Registry SKUs** - Create one or more container registries in your Azure subscription. Registries are available in three SKUs: [Basic, Standard, and Premium](container-registry-skus.md), each of which supports webhook integration, registry authentication with Azure Active Directory, and delete functionality. Tire partido do armazenamento local e perto da rede das imagens do seu contentor ao criar um registo na mesma localização do Azure das suas implementações. Utilize a funcionalidade de [georreplicação](container-registry-geo-replication.md) de registos Premium para replicação avançada e cenários de distribuição de imagens de contentor. 

  You [control access](container-registry-authentication.md) to a container registry using an Azure identity, an Azure Active Directory-backed [service principal](../active-directory/develop/app-objects-and-service-principals.md), or a provided admin account. Log in to the registry using the Azure CLI or the standard `docker login` command.

* **Supported images and artifacts** - Grouped in a repository, each image is a read-only snapshot of a Docker-compatible container. O registo de contentores do Azure pode incluir imagens do Windows e do Linux. O utilizador controla os nomes de todas as implementações de contentores. Utilize os [comandos do Docker](https://docs.docker.com/engine/reference/commandline/) padrão para enviar imagens para um repositório ou extrair imagens de um repositório. In addition to Docker container images, Azure Container Registry stores [related content formats](container-registry-image-formats.md) such as [Helm charts](container-registry-helm-repos.md) and images built to the [Open Container Initiative (OCI) Image Format Specification](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Azure Container Registry Tasks** - Use [Azure Container Registry Tasks](container-registry-tasks-overview.md) (ACR Tasks) to streamline building, testing, pushing, and deploying images in Azure. For example, use ACR Tasks to extend your development inner-loop to the cloud by offloading `docker build` operations to Azure. Configure tarefas de compilação para automatizar o SO do contentor e o pipeline de aplicação de patches de arquitetura e compilar imagens automaticamente quando a sua equipa consolidar código para o controlo de origem.

  [Multi-step tasks](container-registry-tasks-overview.md#multi-step-tasks) provide step-based task definition and execution for building, testing, and patching container images in the cloud. Os passos das tarefas definem a compilação individual da imagem de contentor e as operações push. Também podem definir a execução de um ou mais contentores, com cada passo a utilizar o contentor como o seu ambiente de execução.

## <a name="next-steps"></a>Passos seguintes

* [Create a container registry using the Azure portal (Criar um registo de contentores com o portal do Azure)](container-registry-get-started-portal.md)
* [Create a container registry using the Azure CLI (Criar um registo de contentores com a CLI do Azure)](container-registry-get-started-azure-cli.md)
* [Automate container builds and maintenance with ACR Tasks](container-registry-tasks-overview.md)
