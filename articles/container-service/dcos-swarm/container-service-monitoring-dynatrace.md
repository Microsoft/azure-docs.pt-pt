---
title: (PRETERIDO) Monitorizar o cluster do DC/OS do Azure - Dynatrace
description: Monitorize um cluster de DC/OS do Azure Container Service com o Dynatrace. Implemente o Dynatrace OneAgent através do dashboard de DC/OS.
services: container-service
author: MartinGoodwell
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 8f34a00d9256c288a2842e905c06d5336522eece
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62119862"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>(PRETERIDO) Monitorizar um cluster DC/OS do Azure Container Service com o Dynatrace SaaS/gerida

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste artigo, mostramos como implementar o [Dynatrace](https://www.dynatrace.com/) OneAgent para monitorizar todos os nós de agente no seu cluster do Azure Container Service. Precisa de uma conta com o Dynatrace SaaS/gerida para esta configuração. 

## <a name="dynatrace-saasmanaged"></a>A Dynatrace SaaS/geridos
A Dynatrace é uma solução de monitorização de nativas da cloud para o contentor altamente dinâmico e ambientes de cluster. Permite-lhe otimizar ainda mais as suas implementações de contentores e as alocações de memória, utilizando dados de utilização em tempo real. Ele é capaz de indicar automaticamente problemas de aplicação e infraestrutura, fornecendo automatizada de linha de base, a correlação de problema e a deteção de causa raiz.

A figura seguinte mostra a interface do Usuário de Dynatrace:

![Dynatrace UI](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Pré-requisitos 
[Implementar](container-service-deployment.md) e [ligar](./../container-service-connect.md) para um cluster configurado pelo Azure Container Service. Explore a [IU do Marathon](container-service-mesos-marathon-ui.md). Aceda a [ https://www.dynatrace.com/trial/ ](https://www.dynatrace.com/trial/) para configurar uma conta de Dynatrace SaaS.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Configurar uma implementação de Dynatrace com o Marathon
Estes passos mostram como configurar e implementar aplicações de Dynatrace no seu cluster com o Marathon.

1. Aceder à IU do DC/OS através de [ http://localhost:80/ ](http://localhost:80/). Uma vez na IU do DC/OS, navegue para o **universo** separador e procure **Dynatrace**.

    ![Dynatrace no DC/OS Universe](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. Para concluir a configuração, tem uma conta de SaaS a Dynatrace ou uma conta de avaliação gratuita. Depois de iniciar sessão para o dashboard de Dynatrace, selecione **implementar a Dynatrace**.

    ![A Dynatrace conjunto a integração de PaaS](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Na página, selecione **configurar a integração de PaaS**. 

    ![Token de API de Dynatrace](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Introduza o seu token de API para a configuração de Dynatrace OneAgent no DC/OS Universe. 

    ![Configuração de Dynatrace OneAgent no DC/OS Universe](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Defina as instâncias para o número de nós que pretende executar. Configurar um número maior também funciona, mas o DC/OS vai continuar a tentar encontrar novas instâncias até esse número, na verdade, é alcançado. Se preferir, pode também definir esta opção para um valor como 1000000. Neste caso, sempre que um novo nó é adicionado ao cluster, o Dynatrace implementa automaticamente um agente nesse novo nó, ao preço de DC/OS, constantemente tentando implantar instâncias adicionais.

    ![Configuração de Dynatrace no DC/OS Universe-instâncias](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Passos Seguintes

Depois de instalar o pacote, navegue de volta para o dashboard de Dynatrace. Pode explorar as métricas de utilização diferentes para os contentores no cluster. 