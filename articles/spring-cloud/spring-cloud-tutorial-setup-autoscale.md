---
title: Tutorial - Configurar autoescala para aplicações de microserviços
description: Este artigo descreve como configurar as definições de Autoscale para as suas aplicações utilizando o portal Microsoft Azure ou o Azure CLI.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/22/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 6023d1ebd27ebaccacfce85ce0f49b7ef87e17ad
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742674"
---
# <a name="tutorial-set-up-autoscale-for-microservice-applications"></a>Tutorial: Configurar autoescala para aplicações de microserviços

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

A Autoscale é uma característica incorporada do Azure Spring Cloud que ajuda as aplicações de microserviços a desempenharem o seu melhor quando a procura muda. Isto inclui modificar o número de CPUs virtuais, memória e instâncias de aplicações. Este artigo descreve como configurar as definições de Autoscale para as suas aplicações utilizando o portal Microsoft Azure ou o Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir estes procedimentos, é necessário:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Uma instância de serviço Azure Spring Cloud implantada. Siga o [quickstart na implementação de uma aplicação através do CLI Azure](./spring-cloud-quickstart.md) para começar.
* Pelo menos uma aplicação já criada na sua instância de serviço.

## <a name="navigate-to-the-autoscale-page-in-the-azure-portal"></a>Navegue para a página autoescala no portal Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Vá à página geral da nuvem **de** primavera do Azure.
3. Selecione o grupo de recursos que contém o seu serviço.
4. Selecione o separador **Apps** em **Definições** no menu no painel de navegação esquerdo.
5. Selecione a aplicação para a qual pretende configurar a Autoscale. Neste exemplo, selecione a **demo denominada aplicação.** Em seguida, deverá consultar a **página** geral da aplicação.
6. Vá ao separador **Escala em** **Definições** no menu do painel de navegação à esquerda.
7. Selecione a implementação que pretende configurar Autoscale. Deve ver as opções de Autoscale mostradas na secção seguinte.


![Menu de escala automática](./media/spring-cloud-autoscale/autoscale-menu.png)

## <a name="set-up-autoscale-settings-for-your-application-in-the-azure-portal"></a>Confifique as definições de Autoescala para a sua aplicação no portal Azure

Existem duas opções para a gestão da procura de autoescala:

* Escala manual: Mantém uma contagem de instância fixa. No nível Standard, pode escalar até um máximo de 500 instâncias. Este valor altera o número de instâncias de execução separadas da aplicação de micro-serviço.
* Autoescala personalizada: Escalas em qualquer horário, com base em quaisquer métricas.

No portal Azure, escolha como pretende escalar.  A seguinte figura mostra as definições de escala **automática personalizada** e de modo.

![Autoescala personalizada](./media/spring-cloud-autoscale/custom-autoscale.png)

## <a name="set-up-autoscale-settings-for-your-application-in-azure-cli"></a>Configurar definições de Autoescala para a sua aplicação no Azure CLI
Também pode definir modos de autoescalação utilizando o Azure CLI.  Os seguintes comandos criam uma definição de autoescala e uma regra de autoescala.

* Criar definição de autoescala
  ```
  az monitor autoscale create -g demo-rg --resource /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourcegroups/demo-rg/providers/Microsoft.AppPlatform/Spring/autoscale/apps/demo/deployments/default --name demo-setting --min-count 1 --max-count 5 --count 1
  ```
* Criar regra de escala automática
  ```
  az monitor autoscale rule create -g demo-rg --autoscale-name demo-setting --scale out 1 --cooldown 1 --condition "tomcat.global.request.total.count > 100 avg 1m where AppName == demo and Deployment == default"
  ```

## <a name="upgrade-to-the-standard-tier"></a>Atualizar para o escalão Standard

Se estiver no nível Básico e limitado por um ou mais destes limites, pode fazer upgrade para o nível Standard. Para isso, aceda ao menu de nível **de preços** selecionando primeiro *a* coluna standard e clicando no botão **De upgrade.**

## <a name="next-steps"></a>Passos seguintes

* [Visão geral da autoescala no Microsoft Azure](../azure-monitor/platform/autoscale-overview.md)
* [Azure CLI Monitorizando autoescala](/cli/azure/monitor/autoscale?preserve-view=true&view=azure-cli-latest)
