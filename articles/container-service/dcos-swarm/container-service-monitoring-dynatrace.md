---
title: (DEPRECIADO) Monitor Azure DC/OS cluster - Dynatrace
description: Monitorize um cluster de serviço de contentores Azure DC/OS com Dynatrace. Implemente o Dynatrace OneAgent utilizando o painel DE DC/OS.
author: MartinGoodwell
ms.service: container-service
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: a82481c5cb3d12b11179b41999f73e67583ec43b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277752"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>(DEPRECIADO) Monitorize um cluster de serviço de contentores Azure DC/OS com Dynatrace SaaS/Managed

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste artigo, mostramos-lhe como implementar o [Dynatrace](https://www.dynatrace.com/) OneAgent para monitorizar todos os nós de agente no seu cluster de Serviço de Contentores Azure. Precisa de uma conta com Dynatrace SaaS/Managed para esta configuração. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/Gerido
Dynatrace é uma solução de monitorização nativa da nuvem para ambientes de contentores e cluster altamente dinâmicos. Permite-lhe otimizar melhor as implementações dos seus contentores e alocação de memória utilizando dados de utilização em tempo real. É capaz de identificar automaticamente as questões de aplicação e infraestrutura, fornecendo baseamento automatizado, correlação de problemas e deteção de causas-raiz.

A figura que se segue mostra o Dynatrace UI:

![Dinatrace UI](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Pré-requisitos 
[Desloque](container-service-deployment.md) e [ligue](./../container-service-connect.md) a um cluster configurado pelo Serviço de Contentores Azure. Explore a [IU do Marathon](container-service-mesos-marathon-ui.md). Vá [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) para criar uma conta Dynatrace SaaS.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Configure uma implantação dynatrace com maratona
Estes passos mostram-lhe como configurar e implementar aplicações Dynatrace para o seu cluster com maratona.

1. Aceda ao seu DC/OS UI via [http://localhost:80/](http://localhost:80/). Uma vez na UI DC/OS, navegue para o separador **Universo** e, em seguida, procure por **Dynatrace**.

    ![Dynatrace no Universo DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. Para completar a configuração, necessita de uma conta Dynatrace SaaS ou de uma conta de teste gratuita. Assim que iniciar sessão no painel dynatrace, selecione **Deploy Dynatrace**.

    ![Dynatrace Configurar a integração do PaaS](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Na página, **selecione Configurar a integração paaS**. 

    ![Ficha da Dynatrace API](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Introduza o seu token API na configuração Dynatrace OneAgent dentro do Universo DC/OS. 

    ![Configuração dynatrace OneAgent no Universo DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Detete as instâncias para o número de nós que pretende executar. A definição de um número mais elevado também funciona, mas a DC/OS continuará a tentar encontrar novas instâncias até que esse número seja realmente atingido. Se preferir, também pode definir isto como 1000000. Neste caso, sempre que um novo nó é adicionado ao cluster, a Dynatrace implanta automaticamente um agente para esse novo nó, ao preço de DC/OS constantemente tentando implementar outras instâncias.

    ![Configuração dynatrace nos casos do Universo DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Passos seguintes

Depois de instalar a embalagem, navegue de volta para o painel dynatrace. Pode explorar as diferentes métricas de utilização dos recipientes dentro do seu cluster. 
