---
title: (PRECADO) Monitorize um cluster Azure Kubernetes com CoScale
description: Monitorize um cluster Kubernetes no Serviço de Contentores Azure usando CoScale
author: fryckbos
ms.service: container-service
ms.topic: conceptual
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: f195a5c05c6c95dac898b2d471747952a3446d52
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81681720"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>(PRECADO) Monitorize um cluster de kubernetes de serviço de contentores Azure com CoScale

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Neste artigo, mostramos-lhe como implantar o agente [CoScale](https://web.archive.org/web/20180317071550/https://www.coscale.com/) para monitorizar todos os nós e contentores no seu cluster Kubernetes no Serviço de Contentores Azure. Precisa de uma conta com CoScale para esta configuração. 


## <a name="about-coscale"></a>Sobre coScale 

CoScale é uma plataforma de monitorização que reúne métricas e eventos de todos os contentores em várias plataformas de orquestração. CoScale oferece monitorização completa para ambientes Kubernetes. Fornece visualizações e análises para todas as camadas da pilha: o SO, Kubernetes, Docker e aplicações que correm dentro dos seus contentores. O CoScale oferece vários dashboards de monitorização incorporados, e tem deteção de anomalias incorporadas para permitir que operadores e desenvolvedores encontrem rapidamente problemas de infraestruturas e aplicações.

![CoScale UI](./media/container-service-kubernetes-coscale/coscale.png)

Como mostrado neste artigo, você pode instalar agentes em um cluster Kubernetes para executar CoScale como uma solução SaaS. Se quiser manter os seus dados no local, o CoScale também está disponível para instalação no local.


## <a name="prerequisites"></a>Pré-requisitos

Primeiro tem de [criar uma conta CoScale.](https://web.archive.org/web/20170507123133/https://www.coscale.com/free-trial)

Este walkthrough assume que [criou um cluster Kubernetes usando o Serviço de Contentores Azure.](container-service-kubernetes-walkthrough.md)

Também assume que tem o `az` Azure CLI e `kubectl` ferramentas instaladas.

Pode testar se a ferramenta está `az` instalada em execução:

```azurecli
az --version
```

Se não tiver a `az` ferramenta instalada, existem instruções [aqui](/cli/azure/install-azure-cli).

Pode testar se a ferramenta está `kubectl` instalada em execução:

```bash
kubectl version
```

Se não tiver `kubectl` instalado, pode correr:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Instalação do agente CoScale com um DaemonSet
[Os DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) são utilizados pela Kubernetes para executar uma única instância de um recipiente em cada hospedeiro do cluster.
São perfeitos para executar agentes de monitorização, como o agente CoScale.

Depois de iniciar sessão no CoScale, aceda à página do [agente](https://developer.newrelic.com/) para instalar agentes CoScale no seu cluster utilizando um DaemonSet. O CoScale UI fornece passos de configuração guiados para criar um agente e começar a monitorizar o seu cluster completo de Kubernetes.

![Configuração do agente CoScale](./media/container-service-kubernetes-coscale/installation.png)

Para iniciar o agente no cluster, executar o comando fornecido:

![Inicie o agente CoScale](./media/container-service-kubernetes-coscale/agent_script.png)

Já está! Assim que os agentes estiverem a funcionar, deverá ver dados na consola dentro de minutos. Visite a página do [agente](https://developer.newrelic.com/) para ver um resumo do seu cluster, execute etapas de configuração adicionais e veja dashboards como a visão geral do **cluster Kubernetes**.

![Visão geral do cluster de Kubernetes](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

O agente CoScale é automaticamente implantado em novas máquinas no cluster. O agente atualiza-se automaticamente quando uma nova versão é lançada.


## <a name="next-steps"></a>Próximos passos

Consulte a documentação e [blog](https://web.archive.org/web/20170501021344/http://www.coscale.com:80/blog) [coScale](https://web.archive.org/web/20180415164304/http://docs.coscale.com:80/) para obter mais informações sobre soluções de monitorização coScale. 

