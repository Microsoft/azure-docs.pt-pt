---
title: (PRECADO) Monitor Azure DC/OS cluster - Dynatrace
description: Monitorize um cluster de serviço de contentores Azure DC/OS com Dynatrace. Desloque o Dynatrace OneAgent utilizando o painel DC/OS.
author: MartinGoodwell
ms.service: container-service
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: ab6bb116c93aad8501da21dc5688d7e39f4195fe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82166194"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>(PRECADO) Monitorize um cluster de serviço de contentores Azure DC/OS com Dynatrace SaaS/Managed

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste artigo, mostramos-lhe como implantar o [Dynatrace](https://www.dynatrace.com/) OneAgent para monitorizar todos os nós de agente no seu cluster de Serviço de Contentores Azure. Precisa de uma conta com a Dynatrace SaaS/Managed para esta configuração. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/Gerido
A Dynatrace é uma solução de monitorização nativa da nuvem para ambientes de contentores e aglomerados altamente dinâmicos. Permite-lhe otimizar melhor as implementações dos seus contentores e alocações de memória utilizando dados de utilização em tempo real. É capaz de identificar automaticamente as questões de aplicação e infraestrutura, fornecendo baseamento automatizado, correlação de problemas e deteção de causas de raiz.

A seguinte figura mostra o Dynatrace UI:

![Dynatrace UI](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Pré-requisitos 
[Desdobre](container-service-deployment.md) e [ligue](./../container-service-connect.md) a um cluster configurado pelo Serviço de Contentores Azure. Explore a [IU do Marathon](container-service-mesos-marathon-ui.md). Vá [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) criar uma conta da Dynatrace SaaS.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Configurar um destacamento dynatrace com maratona
Estes passos mostram-lhe como configurar e implementar aplicações Dynatrace para o seu cluster com a Maratona.

1. Aceda ao seu UI DC/OS via `http://localhost:80/` . Uma vez na UI DC/OS, navegue para o **separador Universo** e, em seguida, procure **dynatrace**.

    ![Dynatrace no Universo DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. Para completar a configuração, precisa de uma conta Dynatrace SaaS ou de uma conta de teste gratuita. Assim que iniciar sessão no painel Dynatrace, selecione **Implementar Dynatrace**.

    ![Dynatrace Configura a integração paaS](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Na página, selecione **Configurar a integração paaS**. 

    ![Ficha da Dynatrace API](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Introduza o seu token API na configuração Dynatrace OneAgent dentro do Universo DC/OS. 

    ![Configuração Dynatrace OneAgent no Universo DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Desa estada o número de nós que pretende executar. A definição de um número mais elevado também funciona, mas a DC/OS continuará a tentar encontrar novas instâncias até que esse número seja realmente atingido. Se preferir, também pode definir isto para um valor como 1000000. Neste caso, sempre que um novo nó é adicionado ao cluster, a Dynatrace implanta automaticamente um agente para esse novo nó, ao preço de DC/OS tentando constantemente implementar mais instâncias.

    ![Configuração de Dynatrace nos instâncias do Universo DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Passos seguintes

Depois de instalar o pacote, volte para o painel Dynatrace. Pode explorar as diferentes métricas de utilização para os recipientes dentro do seu cluster. 
