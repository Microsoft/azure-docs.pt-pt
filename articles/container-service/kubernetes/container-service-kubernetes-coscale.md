---
title: PRETERIDO Monitorar um cluster kubernetes do Azure com coescala
description: Monitorar um cluster kubernetes no serviço de contêiner do Azure usando a coescala
author: fryckbos
ms.service: container-service
ms.topic: conceptual
ms.date: 05/22/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: f1d0ca1ffc2e7a3d645ac5acbaafdf45f85550be
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271103"
---
# <a name="deprecated-monitor-an-azure-container-service-kubernetes-cluster-with-coscale"></a>PRETERIDO Monitorar um cluster kubernetes do serviço de contêiner do Azure com coescala

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Neste artigo, mostraremos como implantar o agente de [coescala](https://web.archive.org/web/20180317071550/https://www.coscale.com/) para monitorar todos os nós e contêineres em seu cluster kubernetes no serviço de contêiner do Azure. Você precisa de uma conta com coescala para essa configuração. 


## <a name="about-coscale"></a>Sobre coescala 

A coescala é uma plataforma de monitoramento que reúne métricas e eventos de todos os contêineres em várias plataformas de orquestração. O coscale oferece monitoramento de pilha completa para ambientes kubernetes. Ele fornece visualizações e análises para todas as camadas na pilha: o OS, o kubernetes, o Docker e os aplicativos em execução dentro de seus contêineres. A coescala oferece vários painéis de monitoramento internos e tem detecção interna de anomalias para permitir que operadores e desenvolvedores encontrem problemas de infraestrutura e de aplicativos rapidamente.

![Interface do usuário de coescala](./media/container-service-kubernetes-coscale/coscale.png)

Conforme mostrado neste artigo, você pode instalar agentes em um cluster kubernetes para executar o coescala como uma solução de SaaS. Se você quiser manter seus dados no local, o coscale também estará disponível para instalação local.


## <a name="prerequisites"></a>Pré-requisitos

Primeiro, você precisa [criar uma conta de coescala](https://web.archive.org/web/20170507123133/https://www.coscale.com/free-trial).

Este tutorial pressupõe que você [criou um cluster kubernetes usando o serviço de contêiner do Azure](container-service-kubernetes-walkthrough.md).

Ele também pressupõe que você tenha as ferramentas `az` CLI do Azure e `kubectl` instaladas.

Você pode testar se tem a ferramenta de `az` instalada executando:

```azurecli
az --version
```

Se você não tiver a ferramenta de `az` instalada, há instruções [aqui](/cli/azure/install-azure-cli).

Você pode testar se tem a ferramenta de `kubectl` instalada executando:

```bash
kubectl version
```

Se você não tiver `kubectl` instalado, poderá executar:

```azurecli
az acs kubernetes install-cli
```

## <a name="installing-the-coscale-agent-with-a-daemonset"></a>Instalando o agente de coescala com um Daemonset
[DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) são usados pelo kubernetes para executar uma única instância de um contêiner em cada host no cluster.
Eles são perfeitos para a execução de agentes de monitoramento, como o agente de coescala.

Depois de fazer logon no coscale, vá para a [página do agente](https://app.coscale.com/) para instalar os agentes de coescala no cluster usando um daemonset. A interface do usuário de coescala fornece etapas de configuração guiadas para criar um agente e iniciar o monitoramento de seu cluster kubernetes completo.

![Configuração do agente de coescala](./media/container-service-kubernetes-coscale/installation.png)

Para iniciar o agente no cluster, execute o comando fornecido:

![Iniciar o agente de coescala](./media/container-service-kubernetes-coscale/agent_script.png)

Já está! Depois que os agentes estiverem em execução, você deverá ver os dados no console em alguns minutos. Visite a [página do agente](https://app.coscale.com/) para ver um resumo do cluster, executar etapas de configuração adicionais e ver painéis como a **visão geral do cluster kubernetes**.

![Visão geral do cluster kubernetes](./media/container-service-kubernetes-coscale/dashboard_clusteroverview.png)

O agente de coescala é implantado automaticamente em novos computadores no cluster. O agente é atualizado automaticamente quando uma nova versão é liberada.


## <a name="next-steps"></a>Passos seguintes

Consulte a [documentação de coescala](https://web.archive.org/web/20180415164304/http://docs.coscale.com:80/) e o [blog](https://web.archive.org/web/20170501021344/http://www.coscale.com:80/blog) para obter mais informações sobre soluções de monitoramento de coescala. 

