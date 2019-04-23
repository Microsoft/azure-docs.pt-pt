---
title: Registos de contentores privado do Docker no Azure - descrição geral
description: Introdução ao serviço de Registo de Contentores do Azure, que proporciona registos do Docker baseado na cloud, geridos e privados.
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 04/03/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: ce870bfb8d29f7a808962e4d273388ab31186f10
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997412"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introdução aos registos privados de contentores Docker no Azure

O Registo de Contentores do Azure é um serviço de [registo do Docker](https://docs.docker.com/registry/) gerido e baseado no Docker Registry 2.0. Crie e mantenha registos de contentores do Azure para armazenar e gerir as suas imagens privadas do [contentor do Docker](https://www.docker.com/what-docker).

Utilizar registos de contentores no Azure com os seus atuais de desenvolvimento e pipelines de implantação ou utilizar [ACR tarefas](#azure-container-registry-tasks) para criar imagens de contentor no Azure. Compile a pedido ou automatize totalmente as compilações com acionadores de consolidação do código de origem e de compilação de atualização da imagem de base.

Para obter informações sobre o Docker e os contentores, veja [Docker overview](https://docs.docker.com/engine/docker-overview/) (Descrição geral do Docker).

## <a name="use-cases"></a>Casos de utilização

Extrair imagens de um registo de contentores do Azure para vários destinos de implementações:

* **Sistemas de orquestração dimensionáveis** que gerem aplicações em contentores através de clusters de anfitriões, incluindo [Kubernetes](https://kubernetes.io/docs/), [CD/SO](https://docs.mesosphere.com/) e [Docker Swarm](https://docs.docker.com/swarm/).
* **Serviços do Azure** que suportam a criação e a execução de aplicações em escala, incluindo o [Azure Kubernetes Service (AKS)](../aks/index.yml), o [Serviço de Aplicações](../app-service/index.yml), o [Batch](../batch/index.yml), o [Service Fabric](/azure/service-fabric/) e outros.

Os programadores também podem enviar para um registo de contentores como parte de um fluxo de trabalho de desenvolvimento de contentores. Por exemplo, podem segmentar um registo de contentor a partir de uma ferramenta de integração e implementação contínua, como o [Serviços de DevOps do Azure](https://docs.microsoft.com/azure/devops/) ou o [Jenkins](https://jenkins.io/).

Configurar tarefas de ACR para reconstruir automaticamente as imagens do aplicativo quando suas imagens bases são atualizadas ou automatizar compilações de imagem quando a sua equipa consolida código para um repositório de Git. Crie tarefas de vários passos para automatizar a criação, teste e aplicação de patches de várias imagens de contentor em paralelo na cloud.

O Azure disponibiliza ferramentas que inclui a Interface de linha de comandos do Azure, o portal do Azure e o suporte de API para gerir os registos de contentores do Azure. Opcionalmente, instale o [extensão Docker para Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) e o [conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) extensão para trabalhar com os registos de contentores do Azure. Extrair e enviar imagens para um Azure container registry ou executar tarefas de ACR, tudo no Visual Studio Code.

## <a name="key-concepts"></a>Conceitos-chave

* **Registo** - crie um ou mais registos de contentores na sua subscrição do Azure. Os registos estão disponíveis em três SKUs: [Básico, Standard e Premium](container-registry-skus.md), cada um deles suporta a integração de webhook, autenticação de registo no Azure Active Directory e a funcionalidade de eliminação. Tire partido do armazenamento local e perto da rede das imagens do seu contentor ao criar um registo na mesma localização do Azure das suas implementações. Utilize a funcionalidade de [georreplicação](container-registry-geo-replication.md) de registos Premium para replicação avançada e cenários de distribuição de imagens de contentor. Os nomes de registo completamente qualificados têm o formato `myregistry.azurecr.io`.

  [Controlar o acesso](container-registry-authentication.md) para um registo de contentor com uma identidade do Azure, um Azure Active Directory com cópia de segurança [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md), ou uma conta de administrador fornecida. Iniciar sessão no registo com a CLI do Azure ou o padrão `docker login` comando.

* **Repositório** -um registo contém um ou mais repositórios, que são grupos de virtual de imagens de contentor com o mesmo nome mas etiquetas diferentes ou resumos. O Registo de Contentores do Azure suporta espaços de nomes de repositórios com múltiplos níveis. Com espaços de nomes de vários níveis, pode agrupar coleções de imagens relacionadas com uma aplicação específica ou uma coleção de aplicações para equipas de programação ou operacionais específicas. Por exemplo:

  * `myregistry.azurecr.io/aspnetcore:1.0.1` representa uma imagem transversal a toda a empresa
  * `myregistry.azurecr.io/warrantydept/dotnet-build` representa uma imagem utilizada para criar aplicações .NET, partilhadas em todo o departamento de garantias
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` representa uma imagem Web, agrupada na aplicação de submissões do cliente, que é propriedade do departamento de garantias

* **Imagem** -armazenados num repositório, cada imagem é um instantâneo só de leitura de um contentor compatível com o Docker. O registo de contentores do Azure pode incluir imagens do Windows e do Linux. O utilizador controla os nomes de todas as implementações de contentores. Utilize os [comandos do Docker](https://docs.docker.com/engine/reference/commandline/) padrão para enviar imagens para um repositório ou extrair imagens de um repositório. Para além das imagens de contentor do Docker, registo de contentor do Azure armazena [relacionados com formatos de conteúdo](container-registry-image-formats.md) como [executar Helm gráficos](container-registry-helm-repos.md) e imagens criadas para o [imagem da iniciativa de contentor aberto (OCI) Especificação de formato](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Contentor** - um contentor define uma aplicação de software e as respetivas dependências, envoltas num sistema de ficheiros completo, que inclui código, tempo de execução, ferramentas do sistema e bibliotecas. Execute os contentores do Docker com base nas imagens do Windows ou do Linux que extrai de um registo de contentores. Os contentores executados num computador individual partilham o kernel do sistema operativo. Os contentores do Docker são totalmente portáteis para as principais distribuições de Linux e para macOS e Windows.

## <a name="azure-container-registry-tasks"></a>Tarefas do Azure Container Registry

As [Tarefas do Azure Container Registry](container-registry-tasks-overview.md) (Tarefas do ACR) são um conjunto de funcionalidades no Azure Container Registry que fornecem compilações de imagens de contentores do Docker simplificadas e eficientes no Azure. Utilize as Tarefas do ACR para expandir o seu ciclo interno de programação para a cloud ao descarregar operações `docker build` para o Azure. Configure tarefas de compilação para automatizar o SO do contentor e o pipeline de aplicação de patches de arquitetura e compilar imagens automaticamente quando a sua equipa consolidar código para o controlo de origem.

[Tarefas de vários passos](container-registry-tasks-overview.md#multi-step-tasks) fornecer a definição de tarefa com base no passo e execução para criação, teste e aplicar patches em imagens de contentor na cloud. Os passos das tarefas definem a compilação individual da imagem de contentor e as operações push. Também podem definir a execução de um ou mais contentores, com cada passo a utilizar o contentor como o seu ambiente de execução.

## <a name="next-steps"></a>Passos Seguintes

* [Create a container registry using the Azure portal (Criar um registo de contentores com o portal do Azure)](container-registry-get-started-portal.md)
* [Create a container registry using the Azure CLI (Criar um registo de contentores com a CLI do Azure)](container-registry-get-started-azure-cli.md)
* [Automatizar a aplicação de patches ao SO e à arquitetura com as Tarefas do ACR](container-registry-tasks-overview.md)
