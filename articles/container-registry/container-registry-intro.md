---
title: Registos de contentores geridos
description: Introdução ao serviço de Registo de Contentores do Azure, que proporciona registos do Docker baseado na cloud, geridos e privados.
author: stevelas
ms.topic: overview
ms.date: 02/10/2020
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: b5c81d8b2e6d7eac2dcd9070bf1f448340ea1a18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96341270"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introdução aos registos privados de contentores Docker no Azure

O Registo de Contentores Azure é um serviço de registo privado e gerido do Docker baseado no registo de estivador de código aberto 2.0. Crie e mantenha os registos de contentores Azure para armazenar e gerir as imagens privadas do seu contentor Docker e artefactos relacionados.

Utilize registos de contentores Azure com os seus oleodutos de desenvolvimento e implantação de contentores existentes, ou utilize tarefas de registo de contentores Azure para construir imagens de contentores em Azure. Baseie-se a pedido ou automatize totalmente as construções com gatilhos, tais como código fonte compromete e atualizações de imagem base.

Para mais informações sobre o Docker e os conceitos de registo, consulte a [visão geral](https://docs.docker.com/engine/docker-overview/) do Docker e [sobre registos, repositórios e imagens.](container-registry-concepts.md)

## <a name="use-cases"></a>Casos de utilização

Extrair imagens de um registo de contentores do Azure para vários destinos de implementações:

* **Sistemas de orquestração dimensionáveis** que gerem aplicações em contentores através de clusters de anfitriões, incluindo [Kubernetes](https://kubernetes.io/docs/), [CD/SO](https://docs.mesosphere.com/) e [Docker Swarm](https://docs.docker.com/get-started/swarm-deploy/).
* **Serviços do Azure** que suportam a criação e a execução de aplicações em escala, incluindo o [Azure Kubernetes Service (AKS)](../aks/index.yml), o [Serviço de Aplicações](../app-service/index.yml), o [Batch](../batch/index.yml), o [Service Fabric](../service-fabric/index.yml) e outros.

Os programadores também podem enviar para um registo de contentores como parte de um fluxo de trabalho de desenvolvimento de contentores. Por exemplo, direcione um registo de contentores a partir de uma ferramenta de integração e entrega contínua, como [a Azure Pipelines](/azure/devops/pipelines/ecosystems/containers/acr-template) ou [a Jenkins](https://jenkins.io/).

Configure as tarefas ACR para reconstruir automaticamente as imagens de aplicação quando as suas imagens base forem atualizadas, ou automatizar a imagem quando a sua equipa compromete o código a um repositório git. Crie tarefas em várias etapas para automatizar a construção, teste e remendar várias imagens de contentores em paralelo na nuvem.

O Azure fornece ferramentas incluindo interface de Command-Line Azure, portal Azure e suporte API para gerir os seus registos de contentores Azure. Instale opcionalmente a [extensão do Docker para o Código do Estúdio Visual](https://code.visualstudio.com/docs/azure/docker) e a extensão da Conta [Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) para trabalhar com os registos dos seus contentores Azure. Puxe e empurre as imagens para um registo de contentores Azure, ou execute tarefas ACR, tudo dentro do Código do Estúdio Visual.

## <a name="key-features"></a>Principais funcionalidades

* **Níveis de serviço de registo** - Crie um ou mais registos de contentores na sua subscrição do Azure. Os registos estão disponíveis em três níveis: [Básico, Standard e Premium,](container-registry-skus.md)cada um dos quais suporta a integração do webhook, a autenticação de registo com o Azure Ative Directory e eliminar a funcionalidade. Tire partido do armazenamento local e perto da rede das imagens do seu contentor ao criar um registo na mesma localização do Azure das suas implementações. Utilize a funcionalidade de [georreplicação](container-registry-geo-replication.md) de registos Premium para replicação avançada e cenários de distribuição de imagens de contentor. 

* **Segurança e acesso** - Faça login num registo utilizando o CLI Azure ou o comando padrão. `docker login` O Registo de Contentores Azure transfere imagens de contentores em HTTPS e suporta o TLS para garantir ligações ao cliente. 

  > [!IMPORTANT]
  > A partir de 13 de janeiro de 2020, o Registo de Contentores Azure exigirá todas as ligações seguras dos servidores e aplicações para utilizar o TLS 1.2. Ativar o TLS 1.2 utilizando qualquer cliente estivador recente (versão 18.03.0 ou posterior). O suporte para tLS 1.0 e 1.1 será retirado. 

  Você [controla o acesso](container-registry-authentication.md) a um registo de contentores usando uma identidade Azure, um principal de [serviço](../active-directory/develop/app-objects-and-service-principals.md)apoiado pelo Azure Ative Directory , ou uma conta de administração fornecida. Utilize o controlo de acesso baseado em funções Azure (Azure RBAC) para atribuir aos utilizadores ou sistemas permissões de grãos finos a um registo.

  As funcionalidades de segurança do nível de serviço Premium incluem [a confiança de conteúdo](container-registry-content-trust.md) para a assinatura de etiquetas de imagem, e [firewalls e redes virtuais (pré-visualização)](container-registry-vnet.md) para restringir o acesso ao registo. O Azure Security Center integra opcionalmente o Registo de Contentores Azure para [digitalizar imagens](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) sempre que uma imagem é empurrada para um registo.

* **Imagens e artefactos suportados** - Agrupados num repositório, cada imagem é uma imagem apenas de leitura de um recipiente compatível com Docker. O registo de contentores do Azure pode incluir imagens do Windows e do Linux. O utilizador controla os nomes de todas as implementações de contentores. Utilize os [comandos do Docker](https://docs.docker.com/engine/reference/commandline/) padrão para enviar imagens para um repositório ou extrair imagens de um repositório. Para além das imagens do contentor Docker, o Registo de Contentores Azure armazena [formatos de conteúdo relacionados,](container-registry-image-formats.md) tais como [gráficos Helm](container-registry-helm-repos.md) e imagens construídas para a Especificação do [Formato de Imagem da Iniciativa de Contentor Aberto (OCI).](https://github.com/opencontainers/image-spec/blob/master/spec.md)

* **Construções automatizadas de imagens** - Use [Azure Container Registry Tasks](container-registry-tasks-overview.md) (ACR Tasks) para simplificar a construção, teste, empurrar e implantar imagens em Azure. Por exemplo, utilize tarefas ACR para estender o seu ciclo interno de desenvolvimento à nuvem, descarregando `docker build` operações para Azure. Configure tarefas de compilação para automatizar o SO do contentor e o pipeline de aplicação de patches de arquitetura e compilar imagens automaticamente quando a sua equipa consolidar código para o controlo de origem.

  [As tarefas em várias etapas](container-registry-tasks-overview.md#multi-step-tasks) fornecem definição de tarefa baseada em passos e execução para a construção, teste e remendação de imagens de contentores na nuvem. Os passos das tarefas definem a compilação individual da imagem de contentor e as operações push. Também podem definir a execução de um ou mais contentores, com cada passo a utilizar o contentor como o seu ambiente de execução.

## <a name="next-steps"></a>Passos seguintes

* [Create a container registry using the Azure portal (Criar um registo de contentores com o portal do Azure)](container-registry-get-started-portal.md)
* [Create a container registry using the Azure CLI (Criar um registo de contentores com a CLI do Azure)](container-registry-get-started-azure-cli.md)
* [Construções e manutenção de contentores de automatização com tarefas ACR](container-registry-tasks-overview.md)