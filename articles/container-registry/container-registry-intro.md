---
title: Registros de contêiner gerenciados
description: Introdução ao serviço de Registo de Contentores do Azure, que proporciona registos do Docker baseado na cloud, geridos e privados.
author: stevelas
ms.topic: overview
ms.date: 12/03/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 863b93497505443b79f41f580150a4dbf790a6f2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445718"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introdução aos registos privados de contentores Docker no Azure

O registro de contêiner do Azure é um serviço gerenciado e privado do registro do Docker com base no registro do Docker 2,0 de código aberto. Crie e mantenha registros de contêiner do Azure para armazenar e gerenciar suas imagens privadas de contêiner do Docker e artefatos relacionados.

Use registros de contêiner do Azure com seus pipelines de desenvolvimento e implantação de contêiner existentes ou use tarefas de registro de contêiner do Azure para criar imagens de contêiner no Azure. Crie sob demanda ou Automatize totalmente compilações com gatilhos, como confirmações de código-fonte e atualizações de imagem de base.

Para obter mais informações sobre os conceitos do Docker e do registro, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/) e [sobre registros, repositórios e imagens](container-registry-concepts.md).

## <a name="use-cases"></a>Casos de utilização

Extrair imagens de um registo de contentores do Azure para vários destinos de implementações:

* **Sistemas de orquestração dimensionáveis** que gerem aplicações em contentores através de clusters de anfitriões, incluindo [Kubernetes](https://kubernetes.io/docs/), [CD/SO](https://docs.mesosphere.com/) e [Docker Swarm](https://docs.docker.com/swarm/).
* **Serviços do Azure** que suportam a criação e a execução de aplicações em escala, incluindo o [Azure Kubernetes Service (AKS)](../aks/index.yml), o [Serviço de Aplicações](../app-service/index.yml), o [Batch](../batch/index.yml), o [Service Fabric](/azure/service-fabric/) e outros.

Os programadores também podem enviar para um registo de contentores como parte de um fluxo de trabalho de desenvolvimento de contentores. Por exemplo, direcione um registro de contêiner de uma ferramenta de integração e entrega contínua, como [Azure pipelines](/azure/devops/pipelines/ecosystems/containers/acr-template) ou [Jenkins](https://jenkins.io/).

Configure tarefas ACR para recriar automaticamente imagens de aplicativos quando suas imagens base forem atualizadas ou automatizar compilações de imagem quando sua equipe confirmar o código para um repositório git. Crie tarefas de várias etapas para automatizar a criação, o teste e a aplicação de patches de várias imagens de contêiner em paralelo na nuvem.

O Azure fornece ferramentas, incluindo a interface de linha de comando do Azure, portal do Azure e suporte de API para gerenciar seus registros de contêiner do Azure. Opcionalmente, instale a [extensão do Docker para Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) e a extensão de [conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) para trabalhar com seus registros de contêiner do Azure. Efetuar pull e enviar imagens por push para um registro de contêiner do Azure ou executar tarefas de ACR, tudo no Visual Studio Code.

## <a name="key-features"></a>Principais funcionalidades

* **SKUs do registro** -crie um ou mais registros de contêiner em sua assinatura do Azure. Os registros estão disponíveis em três SKUs: [básico, Standard e Premium](container-registry-skus.md), cada um deles dá suporte à integração de webhook, à autenticação de registro com Azure Active Directory e à funcionalidade de exclusão. Tire partido do armazenamento local e perto da rede das imagens do seu contentor ao criar um registo na mesma localização do Azure das suas implementações. Utilize a funcionalidade de [georreplicação](container-registry-geo-replication.md) de registos Premium para replicação avançada e cenários de distribuição de imagens de contentor. 

* **Segurança e acesso** -você faz logon em um registro usando o CLI do Azure ou o comando `docker login` padrão. O registro de contêiner do Azure transfere imagens de contêiner por HTTPS e dá suporte a TLS para proteger conexões de cliente. 

  > [!IMPORTANT]
  > A partir de 13 de janeiro de 2020, o registro de contêiner do Azure exigirá todas as conexões seguras de servidores e aplicativos para usar o TLS 1,2. O suporte para TLS 1,0 e 1,1 será desativado.

  Você [controla o acesso](container-registry-authentication.md) a um registro de contêiner usando uma identidade do Azure, uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md)com suporte Azure Active Directory ou uma conta de administrador fornecida. Use o RBAC (controle de acesso baseado em função) para atribuir permissões refinadas aos usuários ou sistemas a um registro.

  Os recursos de segurança do SKU Premium incluem [confiança de conteúdo](container-registry-content-trust.md) para assinatura de marca de imagem, [firewalls e redes virtuais (versão prévia)](container-registry-vnet.md) para restringir o acesso ao registro. A central de segurança do Azure integra-se opcionalmente ao registro de contêiner do Azure para [digitalizar imagens](../security-center/azure-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) sempre que uma imagem é enviada por push para um registro.

* **Imagens e artefatos com suporte** – agrupados em um repositório, cada imagem é um instantâneo somente leitura de um contêiner compatível com o Docker. O registo de contentores do Azure pode incluir imagens do Windows e do Linux. O utilizador controla os nomes de todas as implementações de contentores. Utilize os [comandos do Docker](https://docs.docker.com/engine/reference/commandline/) padrão para enviar imagens para um repositório ou extrair imagens de um repositório. Além das imagens de contêiner do Docker, o registro de contêiner do Azure armazena [formatos de conteúdo relacionados](container-registry-image-formats.md) , como [gráficos Helm](container-registry-helm-repos.md) e imagens criadas para a [especificação de formato de imagem de OCI (Open container Initiative)](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Compilações de imagem automatizadas** – use [as tarefas do registro de contêiner do Azure](container-registry-tasks-overview.md) (tarefas ACR) para simplificar a criação, o teste, o envio e a implantação de imagens no Azure. Por exemplo, use tarefas ACR para estender seu loop interno de desenvolvimento para a nuvem descarregando `docker build` operações para o Azure. Configure tarefas de compilação para automatizar o SO do contentor e o pipeline de aplicação de patches de arquitetura e compilar imagens automaticamente quando a sua equipa consolidar código para o controlo de origem.

  [As tarefas de várias etapas](container-registry-tasks-overview.md#multi-step-tasks) fornecem uma definição de tarefa baseada em etapas e execução para criar, testar e aplicar patches em imagens de contêiner na nuvem. Os passos das tarefas definem a compilação individual da imagem de contentor e as operações push. Também podem definir a execução de um ou mais contentores, com cada passo a utilizar o contentor como o seu ambiente de execução.

## <a name="next-steps"></a>Passos seguintes

* [Create a container registry using the Azure portal (Criar um registo de contentores com o portal do Azure)](container-registry-get-started-portal.md)
* [Create a container registry using the Azure CLI (Criar um registo de contentores com a CLI do Azure)](container-registry-get-started-azure-cli.md)
* [Automatizar compilações de contêiner e manutenção com tarefas ACR](container-registry-tasks-overview.md)