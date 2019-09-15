---
title: Registros de contêiner do Docker privado no Azure-visão geral
description: Introdução ao serviço de Registo de Contentores do Azure, que proporciona registos do Docker baseado na cloud, geridos e privados.
services: container-registry
author: stevelas
manager: gwallace
ms.service: container-registry
ms.topic: overview
ms.date: 06/28/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 2262948a8368f9448d876166b712ebaf670b5a56
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994365"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introdução aos registos privados de contentores Docker no Azure

O registro de contêiner do Azure é um serviço gerenciado e privado do registro do Docker com base no registro do Docker 2,0 de código aberto. Crie e mantenha registros de contêiner do Azure para armazenar e gerenciar suas imagens privadas de contêiner do Docker.

Use registros de contêiner do Azure com seus pipelines de desenvolvimento e implantação de contêiner existentes ou use tarefas de registro de contêiner do Azure para criar imagens de contêiner no Azure. Crie sob demanda ou Automatize totalmente compilações com gatilhos, como confirmações de código-fonte e atualizações de imagem de base.

Para obter mais informações sobre os conceitos do Docker e do registro, consulte a [visão geral](https://docs.docker.com/engine/docker-overview/) do Docker e [sobre registros, repositórios e imagens](container-registry-concepts.md).

## <a name="use-cases"></a>Casos de utilização

Extrair imagens de um registo de contentores do Azure para vários destinos de implementações:

* **Sistemas de orquestração dimensionáveis** que gerem aplicações em contentores através de clusters de anfitriões, incluindo [Kubernetes](https://kubernetes.io/docs/), [CD/SO](https://docs.mesosphere.com/) e [Docker Swarm](https://docs.docker.com/swarm/).
* **Serviços do Azure** que suportam a criação e a execução de aplicações em escala, incluindo o [Azure Kubernetes Service (AKS)](../aks/index.yml), o [Serviço de Aplicações](../app-service/index.yml), o [Batch](../batch/index.yml), o [Service Fabric](/azure/service-fabric/) e outros.

Os programadores também podem enviar para um registo de contentores como parte de um fluxo de trabalho de desenvolvimento de contentores. Por exemplo, direcione um registro de contêiner de uma ferramenta de integração e entrega contínua, como [Azure pipelines](/azure/devops/pipelines/ecosystems/containers/acr-template) ou [Jenkins](https://jenkins.io/).

Configure tarefas ACR para recriar automaticamente imagens de aplicativos quando suas imagens base forem atualizadas ou automatizar compilações de imagem quando sua equipe confirmar o código para um repositório git. Crie tarefas de várias etapas para automatizar a criação, o teste e a aplicação de patches de várias imagens de contêiner em paralelo na nuvem.

O Azure fornece ferramentas, incluindo a interface de linha de comando do Azure, portal do Azure e suporte de API para gerenciar seus registros de contêiner do Azure. Opcionalmente, instale a [extensão do Docker para Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) e a extensão de [conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) para trabalhar com seus registros de contêiner do Azure. Efetuar pull e enviar imagens por push para um registro de contêiner do Azure ou executar tarefas de ACR, tudo no Visual Studio Code.

## <a name="key-features"></a>Principais funcionalidades

* **SKUs do registro** -crie um ou mais registros de contêiner em sua assinatura do Azure. Os registros estão disponíveis em três SKUs: [Básico, Standard e Premium](container-registry-skus.md), cada um deles dá suporte à integração de webhook, à autenticação de registro com o Azure Active Directory e à funcionalidade de exclusão. Tire partido do armazenamento local e perto da rede das imagens do seu contentor ao criar um registo na mesma localização do Azure das suas implementações. Utilize a funcionalidade de [georreplicação](container-registry-geo-replication.md) de registos Premium para replicação avançada e cenários de distribuição de imagens de contentor. 

  Você [controla o acesso](container-registry-authentication.md) a um registro de contêiner usando uma identidade do Azure, uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md)com suporte Azure Active Directory ou uma conta de administrador fornecida. Faça logon no registro usando o CLI do Azure ou o comando padrão `docker login` .

* **Imagens e artefatos com suporte** – agrupados em um repositório, cada imagem é um instantâneo somente leitura de um contêiner compatível com o Docker. O registo de contentores do Azure pode incluir imagens do Windows e do Linux. O utilizador controla os nomes de todas as implementações de contentores. Utilize os [comandos do Docker](https://docs.docker.com/engine/reference/commandline/) padrão para enviar imagens para um repositório ou extrair imagens de um repositório. Além das imagens de contêiner do Docker, o registro de contêiner do Azure armazena [formatos de conteúdo relacionados](container-registry-image-formats.md) , como [gráficos Helm](container-registry-helm-repos.md) e imagens criadas para a [especificação de formato de imagem de OCI (Open container Initiative)](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Tarefas do registro de contêiner do Azure** – use [as tarefas do registro de contêiner do Azure](container-registry-tasks-overview.md) (tarefas ACR) para simplificar a criação, o teste, o envio e a implantação de imagens no Azure. Por exemplo, use tarefas ACR para estender seu loop interno de desenvolvimento para a nuvem descarregando `docker build` operações no Azure. Configure tarefas de compilação para automatizar o SO do contentor e o pipeline de aplicação de patches de arquitetura e compilar imagens automaticamente quando a sua equipa consolidar código para o controlo de origem.

  [As tarefas de várias etapas](container-registry-tasks-overview.md#multi-step-tasks) fornecem uma definição de tarefa baseada em etapas e execução para criar, testar e aplicar patches em imagens de contêiner na nuvem. Os passos das tarefas definem a compilação individual da imagem de contentor e as operações push. Também podem definir a execução de um ou mais contentores, com cada passo a utilizar o contentor como o seu ambiente de execução.

## <a name="next-steps"></a>Passos Seguintes

* [Create a container registry using the Azure portal (Criar um registo de contentores com o portal do Azure)](container-registry-get-started-portal.md)
* [Create a container registry using the Azure CLI (Criar um registo de contentores com a CLI do Azure)](container-registry-get-started-azure-cli.md)
* [Automatizar compilações de contêiner e manutenção com tarefas ACR](container-registry-tasks-overview.md)
