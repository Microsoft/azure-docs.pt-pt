---
title: Configurar a camada PremiumV2-serviço de Azure App | Microsoft Docs
description: Saiba como melhorar o desempenho para seu aplicativo Web, móvel e de API em Azure App serviço, dimensionando para o novo tipo de preço PremiumV2.
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
ms.openlocfilehash: d500c3841a9ecec1e4ceae8b13824f0240a5121d
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639856"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Configurar a camada PremiumV2 para o serviço Azure App

O novo tipo de preço **PremiumV2** fornece processadores mais rápidos, armazenamento SSD e dobra a proporção de memória para núcleo dos tipos de preço existentes. Com a vantagem de desempenho, você pode economizar dinheiro executando seus aplicativos em menos instâncias. Neste artigo, você aprende a criar um aplicativo na camada **PremiumV2** ou a escalar verticalmente um aplicativo para a camada **PremiumV2** .

## <a name="prerequisites"></a>Pré-requisitos

Para escalar verticalmente um aplicativo para o **PremiumV2**, você precisa ter um aplicativo de serviço Azure app que é executado em um tipo de preço inferior a **PremiumV2**e o aplicativo deve estar em execução em uma implantação do serviço de aplicativo que dá suporte a PremiumV2.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>Disponibilidade do PremiumV2

A camada **PremiumV2** está disponível para o serviço de aplicativo no _Windows_ , bem como no _Linux_.

O **PremiumV2** está disponível na maioria das regiões do Azure. Para ver se ele está disponível em sua região, execute o seguinte comando CLI do Azure no [Azure cloud Shell](../cloud-shell/overview.md):

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Criar um aplicativo na camada PremiumV2

O tipo de preço de um aplicativo do serviço de aplicativo é definido no [plano do serviço de aplicativo](overview-hosting-plans.md) em que ele é executado. Você pode criar um plano do serviço de aplicativo sozinho ou como parte da criação do aplicativo.

Ao configurar o plano do serviço de aplicativo no <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, selecione **tipo de preço**. 

Selecione **produção**e, em seguida, selecione **P1V2**, **P2V2**ou **P3V2**, em seguida, clique em **aplicar**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Se você não vir **P1V2**, **P2V2**e **P3V2** como opções ou se as opções estiverem esmaecidas, **PremiumV2** provavelmente não estará disponível na implantação do serviço de aplicativo subjacente que contém o plano do serviço de aplicativo. Consulte [escalar verticalmente de uma combinação de grupo de recursos sem suporte e região](#unsupported) para obter mais detalhes.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Escalar verticalmente um aplicativo existente para a camada PremiumV2

Antes de dimensionar um aplicativo existente para a camada **PremiumV2** , verifique se **PremiumV2** está disponível. Para obter informações, consulte [disponibilidade do PremiumV2](#availability). Se não estiver disponível, consulte [escalar verticalmente de uma combinação de grupo de recursos sem suporte e região](#unsupported).

Dependendo do seu ambiente de hospedagem, a expansão pode exigir etapas adicionais. 

Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, abra a página do aplicativo do serviço de aplicativo.

No painel de navegação à esquerda da página do aplicativo do serviço de aplicativo, selecione **escalar verticalmente (plano do serviço de aplicativo)** .

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Selecione **produção**e, em seguida, selecione **P1V2**, **P2V2**ou **P3V2**, em seguida, clique em **aplicar**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Se a operação for concluída com êxito, a página Visão geral do aplicativo mostrará que agora está em uma camada **PremiumV2** .

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Se você receber um erro

Alguns planos do serviço de aplicativo não podem ser escalados verticalmente para a camada PremiumV2 se a implantação do serviço de aplicativo subjacente não oferecer suporte a PremiumV2.  Consulte [escalar verticalmente de uma combinação de grupo de recursos sem suporte e região](#unsupported) para obter mais detalhes.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Escalar verticalmente de um grupo de recursos sem suporte e uma combinação de regiões

Se seu aplicativo for executado em uma implantação do serviço de aplicativo em que **PremiumV2** não está disponível ou se seu aplicativo for executado em uma região que atualmente não dá suporte a **PremiumV2**, você precisará reimplantar seu aplicativo para tirar proveito do **PremiumV2**.  Tem duas opções:

- Crie um **novo** grupo de recursos e, em seguida, crie um **novo** aplicativo e o plano do serviço de aplicativo no **novo** grupo de recursos, escolhendo a região do Azure desejada durante o processo de criação.  Você **deve** selecionar o plano **PremiumV2** no momento em que o novo plano do serviço de aplicativo é criado.  Isso garante que a combinação do grupo de recursos, do plano do serviço de aplicativo e da região do Azure fará com que o plano do serviço de aplicativo seja criado em uma implantação do serviço de aplicativo que dá suporte a **PremiumV2**.  Em seguida, reimplante o código do aplicativo no aplicativo recém-criado e no plano do serviço de aplicativo. Se desejar, é possível dimensionar subsequentemente o plano do serviço de aplicativo de **PremiumV2** para economizar custos, e você ainda poderá dimensionar com êxito novamente no futuro usando o **PremiumV2**.
- Se seu aplicativo já for executado em uma camada **Premium** existente, você poderá clonar seu aplicativo com todas as configurações do aplicativo, cadeias de conexão e a configuração de implantação em um novo plano do serviço de aplicativo que usa **PremiumV2**.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    Na página **clonar aplicativo** , você pode criar um plano do serviço de aplicativo usando **PremiumV2** na região desejada e especificar as configurações do aplicativo e a configuração que deseja clonar.

## <a name="automate-with-scripts"></a>Automatizar com scripts

Você pode automatizar a criação de aplicativos na camada **PremiumV2** com scripts, usando o [CLI do Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>CLI do Azure

O comando a seguir cria um plano do serviço de aplicativo no _P1V2_. Você pode executá-lo no Cloud Shell. As opções para `--sku` são P1V2, _P2V2_e _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O comando a seguir cria um plano do serviço de aplicativo no _P1V2_. As opções para `-WorkerSize` são _pequeno_, _médio_e _grande_.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Mais recursos

[Escalar verticalmente um aplicativo no Azure](manage-scale-up.md)  
[Scale instance count manually or automatically](../monitoring-and-diagnostics/insights-how-to-scale.md) (Dimensionar a contagem de instâncias manual ou automaticamente)
