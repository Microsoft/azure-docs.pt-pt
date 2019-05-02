---
title: (PRETERIDO) Monitorizar um cluster de Kubernetes do Azure com o CoScale
description: Monitorizar um cluster de Kubernetes no Azure Container Service com o CoScale
services: container-service
author: fryckbos
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 895346447e33926dcaa5ca09302f35c9d6636ed9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60713082"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>(PRETERIDO) Monitorizar um cluster de Kubernetes do Azure Container Service com o CoScale

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Neste artigo, mostramos como implementar o [o CoScale](https://web.archive.org/web/20180317071550/ https://www.coscale.com/) agente para monitorizar todos os nós e contentores no cluster do Kubernetes no Azure Container Service. Precisa de uma conta com o CoScale para esta configuração. 


## <a name="about-coscale"></a>Sobre o CoScale 

O coScale é uma plataforma de monitorização que recolhe métricas e eventos de todos os contentores em várias plataformas de orquestração. O coScale oferece monitorização de pilha completa para ambientes do Kubernetes. Ele fornece análises e para todas as camadas da pilha: o sistema operacional, Kubernetes, Docker e aplicações em execução nos contentores. O coScale oferece vários dashboards de monitorização incorporadas, não tendo a deteção de anomalias incorporada para permitir que os operadores e os programadores localizar problemas de infraestrutura e aplicação rápida.

![CoScale da interface do Usuário](./media/container-service-kubernetes-coscale/coscale.png)

Como é mostrado neste artigo, pode instalar agentes num cluster do Kubernetes para executar o CoScale como uma solução de SaaS. Se pretender manter os seus dados no local, o CoScale também está disponível para instalação no local.


## <a name="prerequisites"></a>Pré-requisitos

Precisa primeiro [criar uma conta do CoScale](https://web.archive.org/web/20170507123133/ https://www.coscale.com/free-trial).

Este passo a passo pressupõe que tenha [criado um cluster de Kubernetes com o Azure Container Service](container-service-kubernetes-walkthrough.md).

Também parte do princípio que tem o `az` CLI do Azure e `kubectl` ferramentas instaladas.

Pode testar se tiver o `az` ferramenta instalada executando:

```azurecli
az --version
```

Se não tiver o `az` ferramenta instalada, existem instruções [aqui](/cli/azure/install-azure-cli).

Pode testar se tiver o `kubectl` ferramenta instalada executando:

```bash
kubectl version
```

Se não tiver `kubectl` instalado, pode executar:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Instalar o agente do CoScale com um DaemonSet
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) são usados pelo Kubernetes para executar uma única instância de um contentor em cada anfitrião no cluster.
Eles são perfeitos para a execução de agentes de monitorização, como o agente do CoScale.

Depois de iniciar sessão para o CoScale, vá para o [página do agente](https://app.coscale.com/) para instalar agentes do CoScale em seu cluster com um DaemonSet. A interface do Usuário do CoScale fornece passos de configuração assistida para criar um agente e iniciar a monitorização do seu cluster do Kubernetes completa.

![Configuração do agente o coScale](./media/container-service-kubernetes-coscale/installation.png)

Para iniciar o agente no cluster, execute o comando fornecido:

![Iniciar o agente do CoScale](./media/container-service-kubernetes-coscale/agent_script.png)

Já está! Assim que os agentes estão em execução, deverá ver os dados na consola dentro de alguns minutos. Visite o [página do agente](https://app.coscale.com/) para ver um resumo do cluster, execute os passos de configuração adicionais e ver os dashboards, tais como o **descrição geral do cluster de Kubernetes**.

![Descrição geral do cluster de Kubernetes](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

O agente do CoScale automaticamente é implementado em novas máquinas no cluster. As atualizações de agente automaticamente quando for lançada uma nova versão.


## <a name="next-steps"></a>Passos Seguintes

Consulte a [o CoScale documentação](https://web.archive.org/web/20180415164304/ http://docs.coscale.com:80/) e [blog](https://web.archive.org/web/20170501021344/ http://www.coscale.com:80/blog) para obter mais informações sobre o CoScale soluções de monitorização. 

