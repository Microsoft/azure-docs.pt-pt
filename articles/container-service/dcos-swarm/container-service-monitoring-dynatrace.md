---
title: PRETERIDO Monitorar o cluster de DC/so do Azure-dynaTrace
description: Monitore um cluster DC/OS do serviço de contêiner do Azure com dynaTrace. Implante o dynaTrace OneAgent usando o painel do DC/so.
author: MartinGoodwell
ms.service: container-service
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: a82481c5cb3d12b11179b41999f73e67583ec43b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277752"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>PRETERIDO Monitorar um cluster DC/OS do serviço de contêiner do Azure com o dynaTrace SaaS/gerenciado

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste artigo, mostraremos como implantar o [dynaTrace](https://www.dynatrace.com/) OneAgent para monitorar todos os nós de agente em seu cluster do serviço de contêiner do Azure. Você precisa de uma conta com dynaTrace SaaS/gerenciado para essa configuração. 

## <a name="dynatrace-saasmanaged"></a>SaaS/gerenciado dynaTrace
O dynaTrace é uma solução de monitoramento nativa de nuvem para ambientes de cluster e de contêiner altamente dinâmicos. Ele permite otimizar melhor suas implantações de contêineres e alocações de memória usando dados de uso em tempo real. Ele é capaz de identificar automaticamente os problemas de aplicativos e de infraestrutura ao fornecer a linha de base automatizada, a correlação de problemas e a detecção de causa raiz.

A figura a seguir mostra a interface do usuário do amDynatrace:

![Interface do usuário do amDynatrace](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Pré-requisitos 
[Implante](container-service-deployment.md) e [Conecte-se](./../container-service-connect.md) a um cluster configurado pelo serviço de contêiner do Azure. Explore a [IU do Marathon](container-service-mesos-marathon-ui.md). Vá para [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) para configurar uma conta SaaS dynaTrace.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Configurar uma implantação do dynaTrace com o Marathon
Estas etapas mostram como configurar e implantar aplicativos dynaTrace em seu cluster com o Marathon.

1. Acesse sua interface do usuário do DC/OS via [http://localhost:80/](http://localhost:80/). Uma vez na interface do usuário do DC/so, navegue até a guia **universo** e procure **dynaTrace**.

    ![DynaTrace no universo DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. Para concluir a configuração, você precisa de uma conta dynaTrace SaaS ou uma conta de avaliação gratuita. Depois de fazer logon no painel do dynaTrace, selecione **implantar dynaTrace**.

    ![DynaTrace configurar integração de PaaS](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Na página, selecione **Configurar integração de PaaS**. 

    ![Token de API dynaTrace](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Insira seu token de API na configuração do dynaTrace OneAgent no universo DC/OS. 

    ![Configuração do dynaTrace OneAgent no universo DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Defina as instâncias para o número de nós que você pretende executar. A definição de um número mais alto também funciona, mas o DC/OS continuará tentando encontrar novas instâncias até que esse número seja realmente atingido. Se preferir, você também pode definir isso com um valor como 1 milhão. Nesse caso, sempre que um novo nó é adicionado ao cluster, o dynaTrace implanta automaticamente um agente para esse novo nó, no preço do DC/so, constantemente tentando implantar instâncias adicionais.

    ![Configuração do dynaTrace no universo do DC/so – instâncias](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Passos seguintes

Depois de instalar o pacote, navegue de volta para o painel do dynaTrace. Você pode explorar as diferentes métricas de uso para os contêineres no cluster. 
