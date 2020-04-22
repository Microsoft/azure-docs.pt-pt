---
title: (DEPRECIADO) Monitorize um cluster Azure Kubernetes com CoScale
description: Monitorize um cluster Kubernetes no Serviço de Contentores Azure utilizando coScale
author: fryckbos
ms.service: container-service
ms.topic: conceptual
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: f195a5c05c6c95dac898b2d471747952a3446d52
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681720"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>(DEPRECIADO) Monitorize um cluster de Kubernetes do Serviço de Contentores Azure com CoScale

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Neste artigo, mostramos-lhe como implantar o agente [CoScale](https://web.archive.org/web/20180317071550/https://www.coscale.com/) para monitorizar todos os nós e contentores do seu cluster Kubernetes no Serviço de Contentores Azure. Precisa de uma conta com CoScale para esta configuração. 


## <a name="about-coscale"></a>Sobre coscale 

CoScale é uma plataforma de monitorização que reúne métricas e eventos de todos os contentores em várias plataformas de orquestração. CoScale oferece monitorização completa para ambientes Kubernetes. Fornece visualizações e análises para todas as camadas da pilha: o S, Kubernetes, Docker e aplicações que correm dentro dos seus recipientes. O CoScale oferece vários dashboards de monitorização incorporados, e tem uma deteção de anomalias incorporada para permitir que operadores e desenvolvedores encontrem rapidamente problemas de infraestrutura e aplicação.

![CoScale UI](./media/container-service-kubernetes-coscale/coscale.png)

Como mostrado neste artigo, pode instalar agentes num cluster Kubernetes para executar o CoScale como uma solução SaaS. Se pretender manter os seus dados no local, o CoScale também está disponível para instalação no local.


## <a name="prerequisites"></a>Pré-requisitos

Primeiro é necessário [criar uma conta CoScale.](https://web.archive.org/web/20170507123133/https://www.coscale.com/free-trial)

Este walkthrough pressupõe que [criou um cluster Kubernetes usando](container-service-kubernetes-walkthrough.md)o Serviço de Contentores Azure .

Também assume que tem `az` o AZURE `kubectl` CLI e ferramentas instaladas.

Pode testar se tiver `az` a ferramenta instalada executando:

```azurecli
az --version
```

Se não tiver a `az` ferramenta instalada, existem instruções [aqui.](/cli/azure/install-azure-cli)

Pode testar se tiver `kubectl` a ferramenta instalada executando:

```bash
kubectl version
```

Se não tiver `kubectl` instalado, pode correr:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Instalação do agente CoScale com um DaemonSet
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) são usados por Kubernetes para executar uma única instância de um recipiente em cada hospedeiro no cluster.
São perfeitos para executar agentes de monitorização, como o agente CoScale.

Depois de iniciar sessão no CoScale, aceda à página do [agente](https://developer.newrelic.com/) para instalar agentes CoScale no seu cluster utilizando um DaemonSet. O CoScale UI fornece passos de configuração guiados para criar um agente e começar a monitorizar o seu cluster completo de Kubernetes.

![Configuração do agente CoScale](./media/container-service-kubernetes-coscale/installation.png)

Para ligar o agente no cluster, executar o comando fornecido:

![Inicie o agente CoScale](./media/container-service-kubernetes-coscale/agent_script.png)

Já está! Uma vez que os agentes estejam a funcionar, deve ver os dados na consola em poucos minutos. Visite a página do [agente](https://developer.newrelic.com/) para ver um resumo do seu cluster, execute passos de configuração adicionais e veja dashboards como a visão geral do **cluster Kubernetes**.

![Visão geral do cluster kubernetes](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

O agente CoScale é automaticamente implantado em novas máquinas no cluster. O agente atualiza-se automaticamente quando é lançada uma nova versão.


## <a name="next-steps"></a>Passos seguintes

Consulte a documentação [e](https://web.archive.org/web/20170501021344/http://www.coscale.com:80/blog) blog [coScale](https://web.archive.org/web/20180415164304/http://docs.coscale.com:80/) para obter mais informações sobre as soluções de monitorização do CoScale. 

