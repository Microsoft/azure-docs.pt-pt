---
title: (PRETERIDO) Monitorizar o cluster do DC/OS do Azure - o Datadog
description: Monitorize um cluster do Azure Container Service com o Datadog. Utilize a IU da web de DC/OS para implementar os agentes de Datadog ao seu cluster.
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a094369d467b3b1f3d5fe93f870dccc9eae7519c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547216"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>(PRETERIDO) Monitorizar um cluster DC/OS do Azure Container Service com o Datadog

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste artigo vamos implementar agentes Datadog a todos os nós de agente no seu cluster do Azure Container Service. Precisa de uma conta com o Datadog para esta configuração. 

## <a name="prerequisites"></a>Pré-requisitos
[Implemente](container-service-deployment.md) e [ligue](../container-service-connect.md) um cluster configurado pelo Azure Container Service. Explore a [IU do Marathon](container-service-mesos-marathon-ui.md). Aceda a [ https://datadoghq.com ](https://datadoghq.com) para configurar uma conta de Datadog. 

## <a name="datadog"></a>Datadog
O Datadog é um serviço de monitoramento que recolhe dados de monitorização de seus contentores no seu cluster do Azure Container Service. O Datadog tem um Dashboard de integração de Docker onde pode ver métricas específicas dentro de seus contentores. As métricas reunidas a partir de seus contentores são organizadas por CPU, memória, rede e e/s. O Datadog divide as métricas em contentores e imagens. Um exemplo da aparência da interface do Usuário para a utilização da CPU é inferior.

![Interface de Usuário o Datadog](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Configurar uma implementação de Datadog com o Marathon
Estes passos mostram como configurar e implementar aplicações de Datadog no seu cluster com o Marathon. 

Aceder à IU do DC/OS através de [ http://localhost:80/ ](http://localhost:80/). Uma vez na IU do DC/OS, navegue para "Universe" que se encontra no canto inferior esquerdo e, em seguida, procure "Datadog" e clique em "Instalar".

![Pacote de Datadog dentro do DC/OS Universe](./media/container-service-monitoring/datadog1.png)

Agora para concluir a configuração terá uma conta de Datadog ou uma conta de avaliação gratuita. Depois de entrar para a aparência de Web site Datadog à esquerda e vá para integrações ->, em seguida, [APIs](https://app.datadoghq.com/account/settings#api). 

![Chave de API o Datadog](./media/container-service-monitoring/datadog2.png)

Em seguida, introduza a chave de API para a configuração de Datadog no DC/OS Universe. 

![Configuração de Datadog no DC/OS Universe](./media/container-service-monitoring/datadog3.png) 

Na configuração acima instâncias estão definidas para 10000000 isso sempre que for adicionado um novo nó ao cluster Datadog irá automaticamente implementar um agente para esse nó. Esta é uma solução provisória. Depois de instalar o pacote deve navegar de volta para o site de Datadog e encontrar "[Dashboards](https://app.datadoghq.com/dash/list)." A partir daí, verá personalizadas e Dashboards de integração. O [Docker dashboard](https://app.datadoghq.com/screen/integration/docker) terão todas as métricas de contentor que precisa para monitorizar o seu cluster. 

