---
title: Registos de contentores privado do Docker no Azure - descrição geral
description: Introdução ao serviço de Registo de Contentores do Azure, que proporciona registos do Docker baseado na cloud, geridos e privados.
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 06/28/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: b8b4b5fc3ec15d921ff5580aff4d0202be1d38b9
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797902"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introdução aos registos privados de contentores Docker no Azure

O Azure Container Registry é um serviço de registo de Docker gerido e privado com base no código-fonte aberto Docker Registry 2.0. Criar e manter registos de contentor do Azure para armazenar e gerir as imagens de contentor privadas do Docker.

Utilizar registos de contentores do Azure com os seus atuais de desenvolvimento e pipelines de implantação ou utilizar tarefas de registo de contentor do Azure para criar imagens de contentor no Azure. Crie a pedido, ou totalmente automatizar compilações com acionadores como consolidações de código-fonte e atualizações da imagem de base.

Para obter mais informações sobre os conceitos do Docker e do Registro, consulte a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/) e [sobre os registos, imagens e repositórios](container-registry-concepts.md).

## <a name="use-cases"></a>Casos de utilização

Extrair imagens de um registo de contentores do Azure para vários destinos de implementações:

* **Sistemas de orquestração dimensionáveis** que gerem aplicações em contentores através de clusters de anfitriões, incluindo [Kubernetes](https://kubernetes.io/docs/), [CD/SO](https://docs.mesosphere.com/) e [Docker Swarm](https://docs.docker.com/swarm/).
* **Serviços do Azure** que suportam a criação e a execução de aplicações em escala, incluindo o [Azure Kubernetes Service (AKS)](../aks/index.yml), o [Serviço de Aplicações](../app-service/index.yml), o [Batch](../batch/index.yml), o [Service Fabric](/azure/service-fabric/) e outros.

Os programadores também podem enviar para um registo de contentores como parte de um fluxo de trabalho de desenvolvimento de contentores. Por exemplo, podem segmentar um registo de contentor a partir de uma ferramenta de integração e entrega contínua, como [Pipelines do Azure](/azure/devops/pipelines/get-started/what-is-azure-pipelines.md) ou [Jenkins](https://jenkins.io/).

Configurar tarefas de ACR para reconstruir automaticamente as imagens do aplicativo quando suas imagens bases são atualizadas ou automatizar compilações de imagem quando a sua equipa consolida código para um repositório de Git. Crie tarefas de vários passos para automatizar a criação, teste e aplicação de patches de várias imagens de contentor em paralelo na cloud.

O Azure disponibiliza ferramentas que inclui a Interface de linha de comandos do Azure, o portal do Azure e o suporte de API para gerir os registos de contentores do Azure. Opcionalmente, instale o [extensão Docker para Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) e o [conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) extensão para trabalhar com os registos de contentores do Azure. Extrair e enviar imagens para um Azure container registry ou executar tarefas de ACR, tudo no Visual Studio Code.

## <a name="key-features"></a>Principais funcionalidades

* **SKUs de registo** -criar um ou mais registos de contentores na sua subscrição do Azure. Os registos estão disponíveis em três SKUs: [Básico, Standard e Premium](container-registry-skus.md), cada um deles suporta a integração de webhook, autenticação de registo no Azure Active Directory e a funcionalidade de eliminação. Tire partido do armazenamento local e perto da rede das imagens do seu contentor ao criar um registo na mesma localização do Azure das suas implementações. Utilize a funcionalidade de [georreplicação](container-registry-geo-replication.md) de registos Premium para replicação avançada e cenários de distribuição de imagens de contentor. 

  [Controlar o acesso](container-registry-authentication.md) para um registo de contentor com uma identidade do Azure, um Azure Active Directory com cópia de segurança [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md), ou uma conta de administrador fornecida. Iniciar sessão no registo com a CLI do Azure ou o padrão `docker login` comando.

* **Imagens e artefatos suportados** -agrupados num repositório, cada imagem é um instantâneo só de leitura de um contentor compatível com o Docker. O registo de contentores do Azure pode incluir imagens do Windows e do Linux. O utilizador controla os nomes de todas as implementações de contentores. Utilize os [comandos do Docker](https://docs.docker.com/engine/reference/commandline/) padrão para enviar imagens para um repositório ou extrair imagens de um repositório. Para além das imagens de contentor do Docker, registo de contentor do Azure armazena [relacionados com formatos de conteúdo](container-registry-image-formats.md) como [executar Helm gráficos](container-registry-helm-repos.md) e imagens criadas para o [imagem da iniciativa de contentor aberto (OCI) Especificação de formato](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Tarefas de registo de contentor do Azure** -utilize [tarefas de registo de contentor do Azure](container-registry-tasks-overview.md) (tarefas de ACR) para simplificar a criação, teste, enviar e implantar as imagens no Azure. Por exemplo, utilizar tarefas do ACR para estender seu interna-ciclo de desenvolvimento para a cloud ao descarregar `docker build` operações para o Azure. Configure tarefas de compilação para automatizar o SO do contentor e o pipeline de aplicação de patches de arquitetura e compilar imagens automaticamente quando a sua equipa consolidar código para o controlo de origem.

  [Tarefas de vários passos](container-registry-tasks-overview.md#multi-step-tasks) fornecer a definição de tarefa com base no passo e execução para criação, teste e aplicar patches em imagens de contentor na cloud. Os passos das tarefas definem a compilação individual da imagem de contentor e as operações push. Também podem definir a execução de um ou mais contentores, com cada passo a utilizar o contentor como o seu ambiente de execução.

## <a name="next-steps"></a>Passos Seguintes

* [Create a container registry using the Azure portal (Criar um registo de contentores com o portal do Azure)](container-registry-get-started-portal.md)
* [Create a container registry using the Azure CLI (Criar um registo de contentores com a CLI do Azure)](container-registry-get-started-azure-cli.md)
* [Automatizar compilações de contentor e a manutenção com tarefas do ACR](container-registry-tasks-overview.md)
