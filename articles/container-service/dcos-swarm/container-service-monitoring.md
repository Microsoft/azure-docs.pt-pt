---
title: PRETERIDO Monitorar o cluster de DC/so do Azure-Datadog
description: Monitore um cluster do serviço de contêiner do Azure com o Datadog. Use a interface do usuário da Web do DC/so para implantar os agentes do Datadog no cluster.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 530092dfabacb0b07f4002a82078dd3535cd7e8f
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275259"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>PRETERIDO Monitorar um cluster DC/OS do serviço de contêiner do Azure com Datadog

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste artigo, implantaremos agentes do Datadog em todos os nós de agente em seu cluster do serviço de contêiner do Azure. Você precisará de uma conta com Datadog para essa configuração. 

## <a name="prerequisites"></a>Pré-requisitos
[Implemente](container-service-deployment.md) e [ligue](../container-service-connect.md) um cluster configurado pelo Azure Container Service. Explore a [IU do Marathon](container-service-mesos-marathon-ui.md). Vá para [https://datadoghq.com](https://datadoghq.com) para configurar uma conta do Datadog. 

## <a name="datadog"></a>Datadog
Datadog é um serviço de monitoramento que coleta dados de monitoramento de seus contêineres no cluster do serviço de contêiner do Azure. O Datadog tem um painel de integração do Docker onde você pode ver métricas específicas em seus contêineres. As métricas coletadas de seus contêineres são organizadas por CPU, memória, rede e e/s. O Datadog divide as métricas em contêineres e imagens. Abaixo está um exemplo da aparência da interface do usuário para uso da CPU.

![Interface do usuário do amDatadog](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Configurar uma implantação do Datadog com o Marathon
Essas etapas mostrarão como configurar e implantar aplicativos Datadog em seu cluster com o Marathon. 

Acesse sua interface do usuário do DC/OS via [http://localhost:80/](http://localhost:80/). Uma vez na interface do usuário do DC/OS, navegue até o "universo" que está na parte inferior esquerda e, em seguida, pesquise por "Datadog" e clique em "instalar".

![Pacote Datadog no universo DC/OS](./media/container-service-monitoring/datadog1.png)

Agora, para concluir a configuração, você precisará de uma conta do Datadog ou de uma conta de avaliação gratuita. Depois de fazer logon no site do Datadog, olhe para a esquerda e vá para integrações->, em seguida, [APIs](https://app.datadoghq.com/account/settings#api). 

![Chave de API Datadog](./media/container-service-monitoring/datadog2.png)

Em seguida, insira sua chave de API na configuração Datadog no universo DC/OS. 

![Configuração do Datadog no universo DC/OS](./media/container-service-monitoring/datadog3.png) 

Nas instâncias de configuração acima, são definidas como 10 milhões, portanto, sempre que um novo nó for adicionado ao cluster, o Datadog implantará automaticamente um agente nesse nó. Essa é uma solução provisória. Depois de instalar o pacote, você deve navegar de volta para o site Datadog e encontrar "[dashboards](https://app.datadoghq.com/dash/list)". A partir daí, você verá painéis personalizados e de integração. O [painel do Docker](https://app.datadoghq.com/screen/integration/docker) terá todas as métricas de contêiner necessárias para monitorar o cluster. 

