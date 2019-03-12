---
title: Registos de contentores privado do Docker no Azure - descrição geral
description: Introdução ao serviço de Registo de Contentores do Azure, que proporciona registos do Docker baseado na cloud, geridos e privados.
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 09/25/2018
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: befac6f1429d5099f68f0c2ba0a90bb1217f8b6f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530267"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introdução aos registos privados de contentores Docker no Azure

O Registo de Contentores do Azure é um serviço de [registo do Docker](https://docs.docker.com/registry/) gerido e baseado no Docker Registry 2.0. Crie e mantenha registos de contentores do Azure para armazenar e gerir as suas imagens privadas do [contentor do Docker](https://www.docker.com/what-docker).

Utilize os registos de contentores no Azure com os seus pipelines atuais de programação e implementação de contentores. Utilize o Azure Container Registry Build (ACR Build) para compilar imagens de contentor no Azure. Compile a pedido ou automatize totalmente as compilações com acionadores de consolidação do código de origem e de compilação de atualização da imagem de base.

Para obter informações sobre o Docker e os contentores, veja [Docker overview](https://docs.docker.com/engine/docker-overview/) (Descrição geral do Docker).

## <a name="use-cases"></a>Casos de utilização

Extrair imagens de um registo de contentores do Azure para vários destinos de implementações:

* **Sistemas de orquestração dimensionáveis** que gerem aplicações em contentores através de clusters de anfitriões, incluindo [Kubernetes](https://kubernetes.io/docs/), [CD/SO](https://docs.mesosphere.com/) e [Docker Swarm](https://docs.docker.com/swarm/).
* **Serviços do Azure** que suportam a criação e a execução de aplicações em escala, incluindo o [Azure Kubernetes Service (AKS)](../aks/index.yml), o [Serviço de Aplicações](../app-service/index.yml), o [Batch](../batch/index.yml), o [Service Fabric](/azure/service-fabric/) e outros.

Os programadores também podem enviar para um registo de contentores como parte de um fluxo de trabalho de desenvolvimento de contentores. Por exemplo, podem segmentar um registo de contentor a partir de uma ferramenta de integração e implementação contínua, como o [Serviços de DevOps do Azure](https://docs.microsoft.com/azure/devops/) ou o [Jenkins](https://jenkins.io/).

Configure tarefas de ACR para reconstruir automaticamente as imagens do aplicativo quando suas imagens bases são atualizadas. Utilize as Tarefas do ACR para automatizar as compilações de imagens quando a sua equipa consolidar código para um repositório Git.

## <a name="key-concepts"></a>Conceitos-chave

* **Registo** - crie um ou mais registos de contentores na sua subscrição do Azure. Os registos estão disponíveis em três SKUs: [Básico, Standard e Premium](container-registry-skus.md), cada dos quais suporta a integração de webhook, autenticação de registo no Azure Active Directory e a funcionalidade de eliminação. Tire partido do armazenamento local e perto da rede das imagens do seu contentor ao criar um registo na mesma localização do Azure das suas implementações. Utilize a funcionalidade de [georreplicação](container-registry-geo-replication.md) de registos Premium para replicação avançada e cenários de distribuição de imagens de contentor. Os nomes de registo completamente qualificados têm o formato `myregistry.azurecr.io`.

  [Controlar o acesso](container-registry-authentication.md) para um registo de contentor com uma identidade do Azure, um Azure Active Directory com cópia de segurança [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md), ou uma conta de administrador fornecida. Iniciar sessão no registo utilizando a Interface de linha de comandos do Azure ou o padrão `docker login` comando.

* **Repositório** -um registo contém um ou mais repositórios, que armazenam grupos de imagens de contentor. O Registo de Contentores do Azure suporta espaços de nomes de repositórios com múltiplos níveis. Com espaços de nomes de vários níveis, pode agrupar coleções de imagens relacionadas com uma aplicação específica ou uma coleção de aplicações para equipas de programação ou operacionais específicas. Por exemplo:

  * `myregistry.azurecr.io/aspnetcore:1.0.1` representa uma imagem transversal a toda a empresa
  * `myregistry.azurecr.io/warrantydept/dotnet-build` representa uma imagem utilizada para criar aplicações .NET, partilhadas em todo o departamento de garantias
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` representa uma imagem Web, agrupada na aplicação de submissões do cliente, que é propriedade do departamento de garantias

* **Imagem** -armazenados num repositório, cada imagem é um instantâneo só de leitura de um contentor compatível com o Docker. O registo de contentores do Azure pode incluir imagens do Windows e do Linux. O utilizador controla os nomes de todas as implementações de contentores. Utilize os [comandos do Docker](https://docs.docker.com/engine/reference/commandline/) padrão para enviar imagens para um repositório ou extrair imagens de um repositório. Para além das imagens de contentor, o Azure Container Registry armazena [relacionados com formatos de conteúdo](container-registry-image-formats.md) como [executar Helm gráficos](container-registry-helm-repos.md), utilizado para implementar aplicações no Kubernetes.

* **Contentor** - um contentor define uma aplicação de software e as respetivas dependências, envoltas num sistema de ficheiros completo, que inclui código, tempo de execução, ferramentas do sistema e bibliotecas. Execute os contentores do Docker com base nas imagens do Windows ou do Linux que extrai de um registo de contentores. Os contentores executados num computador individual partilham o kernel do sistema operativo. Os contentores do Docker são totalmente portáteis para as principais distribuições de Linux e para macOS e Windows.

## <a name="azure-container-registry-tasks"></a>Tarefas do Azure Container Registry

As [Tarefas do Azure Container Registry](container-registry-tasks-overview.md) (Tarefas do ACR) são um conjunto de funcionalidades no Azure Container Registry que fornecem compilações de imagens de contentores do Docker simplificadas e eficientes no Azure. Utilize as Tarefas do ACR para expandir o seu ciclo interno de programação para a cloud ao descarregar operações `docker build` para o Azure. Configure tarefas de compilação para automatizar o SO do contentor e o pipeline de aplicação de patches de arquitetura e compilar imagens automaticamente quando a sua equipa consolidar código para o controlo de origem.

[Tarefas de vários passos](container-registry-tasks-overview.md#multi-step-tasks-preview), uma funcionalidade de pré-visualização das Tarefas do ACR, fornece a definição das tarefas com base em passos e a execução para a compilação, os testes e a aplicação de patches em imagens de contentor na cloud. Os passos das tarefas definem a compilação individual da imagem de contentor e as operações push. Também podem definir a execução de um ou mais contentores, com cada passo a utilizar o contentor como o seu ambiente de execução.

## <a name="next-steps"></a>Passos Seguintes

* [Create a container registry using the Azure portal (Criar um registo de contentores com o portal do Azure)](container-registry-get-started-portal.md)
* [Create a container registry using the Azure CLI (Criar um registo de contentores com a CLI do Azure)](container-registry-get-started-azure-cli.md)
* [Automatizar a aplicação de patches ao SO e à arquitetura com as Tarefas do ACR](container-registry-tasks-overview.md)
