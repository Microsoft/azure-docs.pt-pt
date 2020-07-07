---
title: (PRECADO) Monitor Azure DC/OS cluster - Datadog
description: Monitorize um cluster de serviço de contentores Azure com datadog. Utilize o UI web DC/OS para implantar os agentes Datadog no seu cluster.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: fcb005e39f89298b35bf0f3a0ad1e19601ae4d13
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82166155"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>(PRECADO) Monitorize um cluster de serviço de contentores Azure DC/OS com Datadog

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste artigo, iremos enviar agentes datadog para todos os nós de agente no seu cluster de Serviço de Contentores Azure. Vai precisar de uma conta com o Datadog para esta configuração. 

## <a name="prerequisites"></a>Pré-requisitos
[Implemente](container-service-deployment.md) e [ligue](../container-service-connect.md) um cluster configurado pelo Azure Container Service. Explore a [IU do Marathon](container-service-mesos-marathon-ui.md). Vá [https://datadoghq.com](https://datadoghq.com) para criar uma conta Datadog. 

## <a name="datadog"></a>Datadog
O Datadog é um serviço de monitorização que recolhe dados de monitorização dos seus contentores dentro do seu cluster de Serviço de Contentores Azure. O Datadog tem um Painel de Integração docker onde pode ver métricas específicas dentro dos seus contentores. As métricas recolhidas dos seus contentores são organizadas pela CPU, Memória, Rede e E/S. Datadog divide métricas em recipientes e imagens. Um exemplo de como a UI se parece para o uso do CPU está abaixo.

![Datadog UI](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Configure uma implantação de Datadog com maratona
Estes passos irão mostrar-lhe como configurar e implementar aplicações Datadog para o seu cluster com a Marathon. 

Aceda ao seu UI DC/OS via `http://localhost:80/` . Uma vez na UI DC/OS navegue para o "Universo" que está no canto inferior esquerdo e, em seguida, procure por "Datadog" e clique em "Instalar".

![Pacote Datadog dentro do Universo DC/OS](./media/container-service-monitoring/datadog1.png)

Agora para completar a configuração, precisará de uma conta Datadog ou de uma conta de teste gratuita. Assim que iniciar sessão no site do Datadog, procure para a esquerda e vá para Integrações - > então [APIs](https://app.datadoghq.com/account/settings#api). 

![Chave API datadog](./media/container-service-monitoring/datadog2.png)

Em seguida, introduza a sua tecla API na configuração Datadog dentro do Universo DC/OS. 

![Configuração do Datadog no Universo DC/OS](./media/container-service-monitoring/datadog3.png) 

Nas instâncias de configuração acima são definidas para 100000000, pelo que sempre que um novo nó é adicionado ao cluster Datadog irá automaticamente implantar um agente nesse nó. Esta é uma solução provisória. Uma vez instalado o pacote, deverá navegar de volta para o site do Datadog e encontrar "[Dashboards](https://app.datadoghq.com/dash/list)". A partir daí verás dashboards de personalização e integração. O [painel docker](https://app.datadoghq.com/screen/integration/docker) terá todas as métricas de recipiente que precisa para monitorizar o seu cluster. 

