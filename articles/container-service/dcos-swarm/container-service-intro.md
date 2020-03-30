---
title: (DEPRECIADO) Alojamento de contentores Docker na nuvem azure
description: O Azure Container Service proporciona uma forma de simplificar a criação, configuração e gestão de um cluster de máquinas virtuais que estão pré-configuradas para executar aplicações em contentores.
author: rgardler
ms.service: container-service
ms.topic: overview
ms.date: 03/01/2017
ms.author: rogardle
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f13e3b8c861d963c2e9e0b827ba00ee6fa70d31e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76277823"
---
# <a name="deprecated-introduction-to-docker-container-hosting-solutions-with-azure-container-service"></a>(DEPRECIADO) Introdução a soluções de hospedagem de contentores Docker com serviço de contentores Azure 

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

O Azure Container Service simplifica a criação, configuração e gestão de um cluster de máquinas virtuais que estão pré-configuradas para executar aplicações em contentores. Utiliza uma configuração otimizada de populares ferramentas open-source de agendamento e orquestração. Isto permite-lhe utilizar as suas competências ou basear-se num corpo extenso e crescente de conhecimentos da comunidade, para implementar e gerir aplicações baseadas em contentores do Microsoft Azure.

![O Azure Container Service fornece um meio para gerir aplicações em contentores, em vários anfitriões no Azure.](./media/acs-intro/acs-cluster-new.png)

O Azure Container Service melhora o formato de contentor Docker para assegurar que os contentores das aplicações são totalmente portáteis. Também suporta a sua escolha de Marathon e DC/OS, Docker Swarm ou Kubernetes, de modo a que possa ampliar estas aplicações para milhares ou mesmo dezenas de milhares de contentores.

Ao utilizar o Azure Container Service, pode tirar partido das funcionalidades de nível empresarial do Azure, mantendo a portabilidade da aplicação - incluindo a portabilidade nas camadas de orquestração.

## <a name="using-azure-container-service"></a>Utilizar o Azure Container Service
O nosso objetivo com o Serviço de Contentores Azure é proporcionar um ambiente de hospedagem de contentores utilizando ferramentas e tecnologias de código aberto que são populares entre os utilizadores de hoje. Para este fim, vamos expor os pontos finais de API padrão para o orquestrador escolhido (DC/OS, Docker Swarm ou Kubernetes). Ao utilizar estes pontos finais, pode tirar partido de qualquer software que consiga comunicar com esses pontos finais. Por exemplo, no caso do ponto final de Docker Swarm, poderá optar por utilizar a interface Docker de linha de comandos (CLI). Para DC/OS, poderá optar pela CLI DCOS. Para Kubernetes, pode optar por `kubectl`.

## <a name="creating-a-docker-cluster-by-using-azure-container-service"></a>Criar um cluster de Docker com o Azure Container Service
Para começar a utilizar o Azure Container Service, implemente um cluster do Azure Container Service através do portal (pesquise **Azure Container Service** no Marketplace), com um modelo do Azure Resource Manager ([Docker Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm), [DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos) ou [Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)) ou com a [CLI do Azure](container-service-create-acs-cluster-cli.md). Os modelos de início rápido fornecidos podem ser modificados para incluir configuração do Azure, adicional ou avançada. Para mais informações, consulte [Deploy an Azure Container Service cluster (Implementar um cluster do Azure Container Service)](container-service-deployment.md).

## <a name="deploying-an-application"></a>Implementar uma aplicação
O Azure Container Service permite escolher o Docker Swarm, o DC/OS ou o Kubernetes para orquestração. O modo como implementa a aplicação depende da sua escolha do orquestrador.

### <a name="using-dcos"></a>Utilizar DC/OS
O DC/OS é um sistema operativo distribuído, baseado no kernel Apache Mesos de sistemas distribuídos. O Apache Mesos tem a sua sede na Apache Software Foundation e tem alguns dos [maiores nomes de TI](https://mesos.apache.org/documentation/latest/powered-by-mesos/) como utilizadores e contribuintes.

![O Azure Container Service configurado para DC/OS, a mostrar agentes e mestres.](media/acs-intro/dcos.png)

O DC/OS e o Apache Mesos incluem um conjunto impressionante de funcionalidades:

* Escalabilidade comprovada
* Primárias e secundárias replicadas por falhas usando O ZooKeeper Apache
* Suporte para contentores formatados para Docker
* Isolamento nativo entre tarefas com contentores Linux
* Agendamento de vários recursos (memória, CPU, disco e portas)
* APIs Java, Python e C++ para o desenvolvimento de novas aplicações paralelas
* Uma IU da Web para ver o estado do cluster

Por predefinição, o DC/OS que está em execução no Azure Container Service inclui a plataforma de orquestração Marathon para cargas de trabalho de agendamento. No entanto, a implementação do DC/OS de ACS inclui o Mesosphere Universe dos serviços que podem ser adicionados ao seu serviço. Os serviços do Universe incluem o Spark, o Hadoop, o Cassandra e muito mais.

![Universo DC/OS no Azure Container Service](media/dcos/universe.png)

#### <a name="using-marathon"></a>Utilizar o Marathon
O Marathon é uma unidade ampla de cluster e controlo de sistema em grupos ou, no caso do Azure Container Service, contentores formatados para Docker. O Marathon fornece uma IU da Web a partir da qual pode implementar as aplicações. Pode aceder a esta IU através de um URL semelhante a `http://DNS_PREFIX.REGION.cloudapp.azure.com`
em que DNS\_PREFIX e REGION são definidos no momento da implementação. Também pode fornecer o seu próprio nome DNS. Para obter mais informações sobre como executar um contentor utilizando a IU da Web do Marathon, veja [DC/OS container management through the Marathon web UI (Gestão de contentores DC/OS através da IU da Web do Marathon)](container-service-mesos-marathon-ui.md).

![Lista de Aplicações Marathon](media/dcos/marathon-applications-list.png)

Também pode utilizar as APIs REST para comunicar com o Marathon. Existem várias bibliotecas de cliente disponíveis para cada ferramenta. Eles cobrem uma variedade de idiomas -- e você pode usar o protocolo HTTP em qualquer idioma. Além disso, muitas ferramentas DevOps populares fornecem suporte para o Marathon. Isto proporciona flexibilidade máxima para a sua equipa de operações quando estiver a trabalhar com um cluster do Azure Container Service. Para obter mais informações sobre como executar um contentor com a API REST do Marathon, veja [DC/OS container management through the Marathon REST API (Gestão de contentores DC/OS através da API REST do Marathon)](container-service-mesos-marathon-rest.md).

### <a name="using-docker-swarm"></a>Utilizar o Docker Swarm
O Docker Swarm fornece clustering nativo para o Docker. Uma vez que o Docker Swarm serve a API padrão do Docker, qualquer ferramenta que já comunique com um daemon Docker pode utilizar o Swarm para dimensionar de forma transparente para vários anfitriões no Azure Container Service.

![Azure Container Service configurado para utilizar o Swarm.](media/acs-intro/acs-swarm2.png)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

As ferramentas suportadas para a gestão de contentores num cluster do Swarm incluem, mas não se limitam às seguintes:

* Dokku
* CLI do Docker e Docker Compose
* Krane
* Jenkins

### <a name="using-kubernetes"></a>Utilizar Kubernetes
O Kubernetes é uma ferramenta popular de orquestração do contentor de grau de produção de código aberto. O Kubernetes automatiza a implementação, o dimensionamento e a gestão de aplicações no contentor. Uma vez que é uma solução de código aberto e é controlada pela comunidade de código aberto, é executada de forma totalmente integrada no Azure Container Service e pode ser utilizada para implementar contentores em escala no serviço.

![Azure Container Service configurado para utilizar o Kubernetes.](media/acs-intro/kubernetes.png)

Tem um conjunto avançado de funcionalidades, incluindo:
* Dimensionamento horizontal
* Deteção do serviço e balanceamento de carga
* Gestão de segredos e configuração
* Implementações e reversões automáticas com base em API
* Autorrecuperação

## <a name="videos"></a>Vídeos
Introdução ao Azure Container Service (101):  

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Container-Service-101/player]
>
>

Criar Aplicações com o Azure Container Service (Compilação 2016)

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/B822/player]
>
>

## <a name="next-steps"></a>Passos seguintes

Implementar um cluster do serviço de contentores com o [portal](container-service-deployment.md) ou a [ CLI do Azure](container-service-create-acs-cluster-cli.md).
