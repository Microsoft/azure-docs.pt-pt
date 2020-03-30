---
title: Configurar o escalão PremiumV2
description: Aprenda a melhorar o desempenho da sua aplicação web, móvel e API no Azure App Service, escalando para o novo nível de preços PremiumV2.
keywords: serviço de aplicações, serviço de aplicações do azure, dimensionar, dimensionável, plano do serviço de aplicações, custo do serviço de aplicações
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 07/25/2018
ms.custom: seodec18
ms.openlocfilehash: f7f9f6d5617ad0f9be69c47ce514d395534fd892
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672215"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Configure premiumV2 nível para serviço de aplicações Azure

O novo nível de preços **PremiumV2** dá-lhe processadores mais rápidos, armazenamento SSD e duplica a relação memória-núcleo dos níveis de preços existentes. Com a vantagem de desempenho, você poderia economizar dinheiro executando as suas aplicações em menos casos. Neste artigo, aprende-se a criar uma aplicação no escalão **PremiumV2** ou a aumentar uma aplicação para o nível **PremiumV2.**

## <a name="prerequisites"></a>Pré-requisitos

Para escalar uma aplicação para **PremiumV2,** é necessário ter uma app Azure App Service que seja de um nível de preços inferior ao **PremiumV2**, e a aplicação deve estar a funcionar numa implementação do App Service que suporta o PremiumV2.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>Disponibilidade PremiumV2

O nível **PremiumV2** está disponível para o Serviço de Aplicações tanto no _Windows_ como no _Linux._

**PremiumV2** está disponível na maioria das regiões do Azure. Para ver se está disponível na sua região, execute o seguinte comando Azure CLI na [Concha da Nuvem Azure:](../cloud-shell/overview.md)

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Criar uma aplicação no escalão PremiumV2

O nível de preços de uma aplicação do App Service está definido no plano do [App Service](overview-hosting-plans.md) em que funciona. Pode criar um plano de Serviço de Aplicações por si só ou como parte da criação de apps.

Ao configurar o plano de serviço de aplicações no <a href="https://portal.azure.com" target="_blank">portal Azure,</a>selecione **o nível**de preços . 

Selecione **Produção,** depois selecione **P1V2,** **P2V2**, ou **P3V2,** em seguida, clique **em Aplicar**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Se não vir **p1V2,** **P2V2**e **P3V2** como opções, ou se as opções estiverem acinzentadas, então o **PremiumV2** provavelmente não está disponível na implementação subjacente do Serviço de Aplicações que contém o plano de Serviço de Aplicações. Consulte a Scale up de um grupo de [recursos não apoiados e uma combinação de região](#unsupported) para mais detalhes.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Aumentar uma app existente para o nível PremiumV2

Antes de escalonar uma aplicação existente para o nível **PremiumV2,** certifique-se de que o **PremiumV2** está disponível. Para obter informações, consulte [a disponibilidade premiumV2](#availability). Se não estiver disponível, consulte scale up de um grupo de [recursos não apoiado e combinação de região](#unsupported).

Dependendo do ambiente de hospedagem, a escalada pode exigir passos extra. 

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>abra a sua página de aplicações do App Service.

Na navegação à esquerda da sua página de aplicações do App Service, selecione Scale up (plano de serviço de **aplicações)**.

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Selecione **Produção,** depois selecione **P1V2,** **P2V2**, ou **P3V2,** em seguida, clique **em Aplicar**.

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Se a sua operação terminar com sucesso, a página geral da sua aplicação mostra que está agora num nível **PremiumV2.**

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Se tiver um erro

Alguns planos de Serviço de Aplicações não podem escalar até ao nível PremiumV2 se a implementação subjacente ao Serviço de Aplicações não suportar o PremiumV2.  Consulte a Scale up de um grupo de [recursos não apoiados e uma combinação de região](#unsupported) para mais detalhes.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Escala de um grupo de recursos não apoiados e combinação de região

Se a sua aplicação for implementada numa implementação do Serviço de Aplicações onde o **PremiumV2** não esteja disponível, ou se a sua aplicação for executado numa região que atualmente não suporta o **PremiumV2,** terá de reutilizar a sua aplicação para tirar partido do **PremiumV2**.  Tem duas opções:

- Crie um **novo** grupo de recursos e, em seguida, crie um **novo** plano de aplicação e app service no **novo** grupo de recursos, escolhendo a sua região azure desejada durante o processo de criação.  Deve **must** selecionar o plano **PremiumV2** no momento em que o novo plano de serviço de aplicações é criado.  Isto garante a combinação de grupo de recursos, plano de serviço de aplicações e região do Azure resultará na criação do plano de Serviço de Aplicações numa implementação do Serviço de Aplicações que suporta o **PremiumV2**.  Em seguida, recoloque o seu código de aplicação no plano de aplicação e serviço de aplicações recém-criado. Se desejar, poderá posteriormente reduzir o plano de Serviço de Aplicações do **PremiumV2** para economizar custos, e ainda assim poderá voltar a escalar com sucesso no futuro utilizando o **PremiumV2**.
- Se a sua aplicação já estiver em funcionamento num nível **Premium** existente, então pode clonar a sua aplicação com todas as definições de aplicações, cordas de ligação e configuração de implementação num novo plano de serviço de aplicações que utiliza **premiumV2**.

    ![](media/app-service-configure-premium-tier/clone-app.png)

    Na página da **aplicação Clone,** pode criar um plano de Serviço de Aplicações utilizando **premiumV2** na região que deseja, e especificar as definições e configurações da aplicação que pretende clonar.

## <a name="automate-with-scripts"></a>Automatizar com scripts

Pode automatizar a criação de aplicações no nível **PremiumV2** com scripts, utilizando o [Azure CLI](/cli/azure/install-azure-cli) ou [o Azure PowerShell.](/powershell/azure/overview)

### <a name="azure-cli"></a>CLI do Azure

O seguinte comando cria um plano de Serviço de Aplicações em _P1V2_. Podes executá-lo na Cloud Shell. As opções para `--sku` são P1V2, _P2V2_e _P3V2_.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O seguinte comando cria um plano de Serviço de Aplicações em _P1V2_. As opções `-WorkerSize` para são _Pequenas,_ _Médias_e _Grandes._

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>Mais recursos

[Aumentar verticalmente uma aplicação no Azure](manage-scale-up.md)  
[Scale instance count manually or automatically](../monitoring-and-diagnostics/insights-how-to-scale.md) (Dimensionar a contagem de instâncias manual ou automaticamente)
