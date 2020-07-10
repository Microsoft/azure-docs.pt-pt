---
title: Configurar o escalão PremiumV2
description: Saiba como melhorar o desempenho da sua aplicação web, móvel e API no Azure App Service, escalando para o novo nível de preços PremiumV2.
keywords: serviço de aplicações, serviço de aplicações do azure, dimensionar, dimensionável, plano do serviço de aplicações, custo do serviço de aplicações
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 07/25/2018
ms.custom: seodec18
ms.openlocfilehash: 4fa64562f8d26297d2b2c8a13cd8b6a513c2c630
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170025"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Configure nível PremiumV2 para O Serviço de Aplicações Azure

O novo nível de preços **PremiumV2** dá-lhe processadores mais rápidos, armazenamento SSD e duplica a relação memória-core dos níveis de preços existentes. Com a vantagem de desempenho, poderia economizar dinheiro executando as suas aplicações em menos instâncias. Neste artigo, aprende-se a criar uma app no nível **PremiumV2** ou a escalar uma aplicação para o nível **PremiumV2.**

## <a name="prerequisites"></a>Pré-requisitos

Para aumentar uma aplicação para **o PremiumV2,** é necessário ter uma aplicação do Azure App Service que funciona num nível de preços inferior ao **PremiumV2**, e a aplicação deve estar a ser executada numa implementação do App Service que suporte o PremiumV2.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>Disponibilidade PremiumV2

O nível **PremiumV2** está disponível para o Serviço de Aplicações tanto no _Windows_ como no _Linux._

**PremiumV2** está disponível na maioria das regiões de Azure. Para ver se está disponível na sua região, corra o seguinte comando Azure CLI na [Azure Cloud Shell:](../cloud-shell/overview.md)

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>Criar uma app no nível PremiumV2

O nível de preços de uma aplicação de Serviço de Aplicações está definido no plano de Serviço de [Aplicações](overview-hosting-plans.md) que executa. Pode criar um plano de Serviço de Aplicações por si só ou como parte da criação de aplicações.

Ao configurar o plano de Serviço de Aplicações no <a href="https://portal.azure.com" target="_blank">portal Azure,</a>selecione **o nível de preços**. 

Selecione **Produção**, em seguida, selecione **P1V2,** **P2V2**ou **P3V2,** em seguida, clique em **Aplicar**.

![Screenshot mostrando os níveis de preços recomendados para a sua aplicação.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Se não vir **o P1V2,** **P2V2**e **P3V2** como opções, ou se as opções estiverem cinzentas, então o **PremiumV2** provavelmente não está disponível na implementação subjacente do Serviço de Aplicações que contém o plano de Serviço de Aplicações. Consulte [a Scale up a partir de um grupo de recursos não suportado e combinação de região](#unsupported) para obter mais detalhes.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>Dimensione uma app existente para o nível PremiumV2

Antes de escalonar uma aplicação existente para o nível **PremiumV2,** certifique-se de que **o PremiumV2** está disponível. Para obter informações, consulte [a disponibilidade do PremiumV2.](#availability) Se não estiver disponível, consulte [Scale up de um grupo de recursos não suportado e combinação de região.](#unsupported)

Dependendo do ambiente de hospedagem, a escalada pode exigir passos extra. 

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>abra a sua página de aplicações do Serviço de Aplicações.

Na navegação à esquerda da sua página de aplicações do Serviço de Aplicações, selecione **Scale up (Plano de Serviço de Aplicações)**.

![Screenshot mostrando como aumentar o seu plano de serviço de aplicações.](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Selecione **Produção**, em seguida, selecione **P1V2,** **P2V2**ou **P3V2,** em seguida, clique em **Aplicar**.

![Screenshot mostrando os níveis de preços recomendados para a sua aplicação.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Se a sua operação terminar com sucesso, a página geral da sua aplicação mostra que está agora num nível **PremiumV2.**

![Screenshot mostrando o nível de preços PremiumV2 na página geral da sua aplicação.](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Se tiver um erro

Alguns planos do Serviço de Aplicações não podem escalar até ao nível PremiumV2 se a implementação subjacente do Serviço de Aplicações não suportar o PremiumV2.  Consulte [a Scale up a partir de um grupo de recursos não suportado e combinação de região](#unsupported) para obter mais detalhes.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Dimensione a partir de um grupo de recursos não suportado e combinação de região

Se a sua aplicação funcionar numa implementação do Serviço de Aplicações onde o **PremiumV2** não está disponível, ou se a sua aplicação funcionar numa região que atualmente não suporta **o PremiumV2,** terá de voltar a implementar a sua app para tirar partido do **PremiumV2.**  Tem duas opções:

- Crie um **novo** grupo de recursos e, em seguida, crie um **novo** plano de app e App Service no **novo** grupo de recursos, escolhendo a região Azure desejada durante o processo de criação.  Tem **de** selecionar o plano **PremiumV2** no momento da criação do novo plano de serviço de aplicações.  Isto garante a combinação de grupo de recursos, plano de Serviço de Aplicações e região de Azure resultará na criação do plano de Serviço de Aplicações numa implementação do Serviço de Aplicações que suporta **o PremiumV2**.  Em seguida, reimplante o seu código de aplicação no plano de serviço de aplicações e aplicações recém-criado. Se desejar, poderá posteriormente reduzir o plano do Serviço de Aplicações a partir do **PremiumV2** para economizar custos, e ainda poderá voltar a escalar com sucesso no futuro usando o **PremiumV2**.
- Se a sua aplicação já funciona num nível **Premium** existente, então pode clonar a sua aplicação com todas as definições de aplicações, cordas de ligação e configuração de implementação num novo plano de serviço de aplicações que utilize **o PremiumV2**.

    ![Screenshot mostrando como clonar a sua aplicação.](media/app-service-configure-premium-tier/clone-app.png)

    Na página de **aplicações Clone,** pode criar um plano de Serviço de Aplicações utilizando o **PremiumV2** na região que pretende e especificar as definições e configurações da aplicação que pretende clonar.

## <a name="automate-with-scripts"></a>Automatizar com scripts

Pode automatizar a criação de aplicações no nível **PremiumV2** com scripts, utilizando o [Azure CLI](/cli/azure/install-azure-cli) ou [o Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>CLI do Azure

O seguinte comando cria um plano de Serviço de Aplicações em _P1V2_. Podes executá-lo na Cloud Shell. As opções `--sku` para são P1V2, _P2V2_e _P3V2_.

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
