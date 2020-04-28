---
title: (DEPRECIADO) Monitor Azure DC/OS cluster - Datadog
description: Monitorize um cluster do Serviço de Contentores Azure com datadog. Utilize a UI web DC/OS para implantar os agentes Datadog no seu cluster.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: fcb005e39f89298b35bf0f3a0ad1e19601ae4d13
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82166155"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>(DEPRECIADO) Monitorize um cluster de serviço de contentores Azure DC/OS com Datadog

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste artigo vamos implantar agentes da Datadog em todos os nós de agente no seu cluster do Serviço de Contentores Azure. Você precisará de uma conta com datadog para esta configuração. 

## <a name="prerequisites"></a>Pré-requisitos
[Implemente](container-service-deployment.md) e [ligue](../container-service-connect.md) um cluster configurado pelo Azure Container Service. Explore a [IU do Marathon](container-service-mesos-marathon-ui.md). Vá [https://datadoghq.com](https://datadoghq.com) para criar uma conta datadog. 

## <a name="datadog"></a>Datadog
Datadog é um serviço de monitorização que recolhe dados de monitorização dos seus contentores dentro do seu cluster de Serviço de Contentores Azure. Datadog tem um Dashboard de Integração de Docker onde você pode ver métricas específicas dentro dos seus recipientes. As métricas recolhidas dos seus contentores são organizadas pela CPU, Memory, Network e I/O. Datadog divide métricas em recipientes e imagens. Um exemplo do que a UI parece para o uso de CPU está abaixo.

![Datadog UI](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Configure uma implantação de Datadog com maratona
Estes passos mostrar-lhe-ão como configurar e implementar aplicações datadog para o seu cluster com maratona. 

Aceda ao seu DC/OS UI via `http://localhost:80/`. Uma vez na UI DC/OS navegar para o "Universo" que está na parte inferior à esquerda e depois procurar por "Datadog" e clicar em "Instalar".

![Pacote Datadog dentro do Universo DC/OS](./media/container-service-monitoring/datadog1.png)

Agora para completar a configuração, necessitará de uma conta Datadog ou de uma conta de teste gratuita. Uma vez que você está registrado no site datadog olhar para a esquerda e ir para Integrações - > então [APIs](https://app.datadoghq.com/account/settings#api). 

![Chave Datadog API](./media/container-service-monitoring/datadog2.png)

Em seguida, introduza a sua chave API na configuração datadog dentro do Universo DC/OS. 

![Configuração do Datadog no Universo DC/OS](./media/container-service-monitoring/datadog3.png) 

Nas instâncias de configuração acima estão definidas para 10000000, pelo que sempre que um novo nó é adicionado ao cluster Datadog irá automaticamente implantar um agente para esse nó. Esta é uma solução provisória. Depois de ter instalado o pacote, deve navegar de volta para o site do Datadog e encontrar "[Dashboards](https://app.datadoghq.com/dash/list)". A partir daí, você verá Dashboards Personalizados e Integração. O [painel do Docker](https://app.datadoghq.com/screen/integration/docker) terá todas as métricas do recipiente necessáriapara monitorizar o seu cluster. 

