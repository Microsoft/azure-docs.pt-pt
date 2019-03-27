---
title: Configurar o escalão PremiumV2 - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como melhor desempenho para a web, móveis e de aplicação API no App Service do Azure através do dimensionamento para o novo escalão de preço PremiumV2.
keywords: serviço de aplicações, serviço de aplicações do azure, dimensionar, dimensionável, plano do serviço de aplicações, custo do serviço de aplicações
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c85644e3cab39f9e0864af91722ee54aab6d59f3
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486874"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Configurar o escalão PremiumV2 para serviço de aplicações do Azure

A nova **PremiumV2** escalão de preço fornece processadores mais rápidos, armazenamento SSD e o rácio de duplicatas de memória/núcleo dos escalões de preços existentes. Com a vantagem de desempenho, que pode poupar dinheiro ao executar as suas aplicações em menos instâncias. Neste artigo, irá aprender a criar uma aplicação no **PremiumV2** escalão ou aumentar verticalmente uma aplicação para **PremiumV2** escalão.

## <a name="prerequisites"></a>Pré-requisitos

Uma aplicação para aumentar verticalmente **PremiumV2**, tem de ter uma aplicação de serviço de aplicações do Azure que é executado num escalão de preço inferior **PremiumV2**, e a aplicação tem de executar uma implementação de serviço de aplicações que suporte a PremiumV2.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>Disponibilidade de PremiumV2

O **PremiumV2** escalão está disponível para o serviço de aplicações em ambos _Windows_ , bem como _Linux_.

**PremiumV2** está disponível em regiões mais do Azure. Para ver se ele está disponível na sua região, execute o seguinte comando da CLI do Azure no [Azure Cloud Shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Criar uma aplicação num escalão PremiumV2

O escalão de preço de uma aplicação de serviço de aplicações está definido no [plano do App Service](overview-hosting-plans.md) que é executado. Pode criar um plano do serviço de aplicações por si só ou como parte da criação de aplicações.

Quando configurar o plano do serviço de aplicações na <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, selecione **escalão de preço**. 

Selecione **produção**, em seguida, selecione **P1V2**, **P2V2**, ou **P3V2**, em seguida, clique em **aplicar**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Se não vir **P1V2**, **P2V2**, e **P3V2** como opções, ou se as opções são cinzento fora, em seguida, **PremiumV2** provavelmente não está disponível no a implementação de serviço de aplicações subjacente que contém o plano do serviço de aplicações. Ver [aumentar verticalmente a partir de uma combinação de grupo e a região de recurso não suportado](#unsupported) para obter mais detalhes.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Aumentar verticalmente uma aplicação existente para o escalão PremiumV2

Antes de dimensionar uma aplicação existente para **PremiumV2** camada, certifique-se de que **PremiumV2** está disponível. Para obter informações, consulte [PremiumV2 disponibilidade](#availability). Se não estiver disponível, consulte [aumentar verticalmente a partir de uma combinação de grupo e a região de recurso não suportado](#unsupported).

Dependendo do ambiente de alojamento, a aumentar verticalmente pode requerer passos adicionais. 

Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, abra a página da aplicação de serviço de aplicações.

No painel de navegação esquerdo da página da aplicação de serviço de aplicações, selecione **aumentar verticalmente (plano do serviço de aplicações)**.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Selecione **produção**, em seguida, selecione **P1V2**, **P2V2**, ou **P3V2**, em seguida, clique em **aplicar**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Se a operação for concluída com êxito, a página de descrição geral da sua aplicação mostra que está agora num **PremiumV2** escalão.

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Se obtiver um erro

Alguns planos de serviço de aplicações não é possível aumentar verticalmente para o escalão PremiumV2 se a implementação subjacente do serviço de aplicações não suporta PremiumV2.  Ver [aumentar verticalmente a partir de uma combinação de grupo e a região de recurso não suportado](#unsupported) para obter mais detalhes.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Aumentar verticalmente a partir de uma combinação de grupo e a região de recurso não suportado

Se o aplicativo é executado numa implantação de serviço de aplicações em que **PremiumV2** não estiver disponível, ou se a sua aplicação for executada numa região que, atualmente não suporta **PremiumV2**, terá de voltar a implementar a aplicação para tirar aproveitar **PremiumV2**.  Tem duas opções:

- Criar uma **novos** recursos de grupo e, em seguida, criar um **novo** plano de aplicação e serviço de aplicações no **novo** grupo de recursos, escolher a região do Azure pretendida durante o processo de criação.  **Tem** selecione a **PremiumV2** plano no momento, o novo plano do serviço de aplicação é criado.  Isto garante que a combinação do grupo de recursos, o plano do serviço de aplicações, e a região do Azure irá resultar no plano do serviço de aplicações a ser criado numa implantação de serviço de aplicações suporta **PremiumV2**.  Em seguida, volte a implementar o código da aplicação para a aplicação criada recentemente e o plano do serviço de aplicações. Se assim o desejar, em seguida, pode dimensionar o plano do serviço de aplicações para baixo desde **PremiumV2** para guardar os custos e ainda será possível com êxito verticalmente back novamente no futuro usando **PremiumV2**.
- Se a sua aplicação já é executado num existente **Premium** escalão, em seguida, pode clonar a aplicação com todas as definições da aplicação, as cadeias de ligação e configuração de implementação para um novo plano do serviço de aplicações que utiliza **PremiumV2**.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    Na **clonar aplicação** página, pode criar um plano do serviço de aplicações utilizando **PremiumV2** na região que pretende e especificar as definições de aplicação e a configuração que pretende clonar.

## <a name="automate-with-scripts"></a>Automatizar com scripts

Pode automatizar a criação de aplicações no **PremiumV2** escalão com scripts, utilizando o [CLI do Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>CLI do Azure

O comando seguinte cria um plano do serviço de aplicações no _P1V2_. Pode executá-lo no Cloud Shell. As opções para `--sku` são P1V2, _P2V2_, e _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O comando seguinte cria um plano do serviço de aplicações no _P1V2_. As opções para `-WorkerSize` estão _pequenas_, _médio_, e _grande_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Mais recursos

[Aumentar verticalmente uma aplicação no Azure](web-sites-scale.md)  
[Scale instance count manually or automatically](../monitoring-and-diagnostics/insights-how-to-scale.md) (Dimensionar a contagem de instâncias manual ou automaticamente)
