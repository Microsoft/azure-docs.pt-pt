---
title: Configurar o escalão PremiumV3
description: Saiba como melhorar o desempenho da sua aplicação web, móvel e API no Azure App Service, escalando para o novo nível de preços PremiumV3.
keywords: serviço de aplicações, serviço de aplicações do azure, dimensionar, dimensionável, plano do serviço de aplicações, custo do serviço de aplicações
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: e6b8c7d54cf24d810a1f32082d816c908966f63c
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92739692"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>Configure nível PremiumV3 para serviço de aplicações Azure

O novo nível de preços **PremiumV3** dá-lhe processadores mais rápidos, armazenamento SSD e quadruplicar a relação memória-core dos níveis de preços existentes (o dobro do nível **PremiumV2).** Com a vantagem de desempenho, poderia economizar dinheiro executando as suas aplicações em menos instâncias. Neste artigo, aprende-se a criar uma app no nível **PremiumV3** ou a escalar uma aplicação para o nível **PremiumV3.**

## <a name="prerequisites"></a>Pré-requisitos

Para aumentar uma aplicação para **o PremiumV3,** é necessário ter uma aplicação do Azure App Service que funciona num nível de preços inferior ao **PremiumV3** , e a aplicação deve estar a ser executada numa implementação do App Service que suporte o PremiumV3.

<a name="availability"></a>

## <a name="premiumv3-availability"></a>Disponibilidade PremiumV3

O nível **PremiumV3** está disponível para aplicações nativas e contentores, incluindo contentores Windows e linux.

> [!NOTE]
> Os recipientes Windows que funcionam no nível **do Recipiente Premium** durante o período de pré-visualização continuam a funcionar como está, mas o nível de Recipiente **Premium** continuará a ser pré-visualizado. O nível **PremiumV3** é a substituição oficial do nível **de Recipiente Premium.** 

**O PremiumV3** está disponível em algumas regiões do Azure e a disponibilidade em regiões adicionais está a ser continuamente adicionada. Para ver se está disponível na sua região, corra o seguinte comando Azure CLI na [Azure Cloud Shell:](../cloud-shell/overview.md)

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>Criar uma app no nível PremiumV3

O nível de preços de uma aplicação de Serviço de Aplicações está definido no plano de Serviço de [Aplicações](overview-hosting-plans.md) que executa. Pode criar um plano de Serviço de Aplicações por si só ou como parte da criação de aplicações.

Ao configurar o plano de Serviço de Aplicações no <a href="https://portal.azure.com" target="_blank">portal Azure,</a>selecione **o nível de preços** . 

Selecione **Produção** , em seguida, selecione **P1V3,** **P2V3** ou **P3V3** , em seguida, clique em **Aplicar** .

![Screenshot mostrando os níveis de preços recomendados para a sua aplicação.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> Se não vir **o P1V3,** **P2V3** e **P3V3** como opções, ou se as opções estiverem cinzentas, então o **PremiumV3** provavelmente não está disponível na implementação subjacente do Serviço de Aplicações que contém o plano de Serviço de Aplicações. Consulte [a Scale up a partir de um grupo de recursos não suportado e combinação de região](#unsupported) para obter mais detalhes.

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>Dimensione uma app existente para o nível PremiumV3

Antes de escalonar uma aplicação existente para o nível **PremiumV3,** certifique-se de que **o PremiumV3** está disponível. Para obter informações, consulte [a disponibilidade do PremiumV3.](#availability) Se não estiver disponível, consulte [Scale up de um grupo de recursos não suportado e combinação de região.](#unsupported)

Dependendo do ambiente de hospedagem, a escalada pode exigir passos extra. 

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>abra a sua página de aplicações do Serviço de Aplicações.

Na navegação à esquerda da sua página de aplicações do Serviço de Aplicações, selecione **Scale up (Plano de Serviço de Aplicações)** .

![Screenshot mostrando como aumentar o seu plano de serviço de aplicações.](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

Selecione **Produção** , em seguida, selecione **P1V3,** **P2V3** ou **P3V3** , em seguida, clique em **Aplicar** .

![Screenshot mostrando os níveis de preços recomendados para a sua aplicação.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

Se a sua operação terminar com sucesso, a página geral da sua aplicação mostra que está agora num nível **PremiumV3.**

![Screenshot mostrando o nível de preços PremiumV3 na página geral da sua aplicação.](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>Se tiver um erro

Alguns planos do Serviço de Aplicações não podem escalar até ao nível PremiumV3 se a implementação subjacente do Serviço de Aplicações não suportar o PremiumV3. Consulte [a Scale up a partir de um grupo de recursos não suportado e combinação de região](#unsupported) para obter mais detalhes.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>Dimensione a partir de um grupo de recursos não suportado e combinação de região

Se a sua aplicação funcionar numa implementação do Serviço de Aplicações onde o **PremiumV3** não está disponível, ou se a sua aplicação funciona numa região que atualmente não suporta **o PremiumV3,** tem de voltar a implementar a sua app para tirar partido do **PremiumV3** .  Tem duas opções:

- Crie uma aplicação num novo grupo de recursos e com um novo plano de Serviço de Aplicações. Ao criar o plano de Serviço de Aplicações, selecione um nível **PremiumV3.** Este passo garante que o plano de Serviço de Aplicações é implantado numa unidade de implantação que suporta **o PremiumV3** . Em seguida, recolocar o seu código de aplicação na aplicação recém-criada. Mesmo que reduza o plano do Serviço de Aplicações para um nível mais baixo para economizar custos, pode sempre voltar a escalar para **o PremiumV3** porque a unidade de implantação o suporta.
- Se a sua aplicação já funciona num nível **Premium** existente, então pode clonar a sua aplicação com todas as definições de aplicações, cordas de ligação e configuração de implementação num novo plano de serviço de aplicações que utilize **o PremiumV3** .

    ![Screenshot mostrando como clonar a sua aplicação.](media/app-service-configure-premium-tier/clone-app.png)

    Na página de **aplicações Clone,** pode criar um plano de Serviço de Aplicações utilizando o **PremiumV3** na região que pretende e especificar as definições e configurações da aplicação que pretende clonar.

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>Passar de Recipiente Premium para Premium V3 SKU

Se tiver uma aplicação que está a utilizar o Pré-visualização Premium Container SKU e gostaria de se mudar para o novo Premium V3 SKU, precisa de recolocar a sua app para tirar partido do **PremiumV3** . Para isso, consulte a primeira opção em [Escala a partir de um grupo de recursos não suportado e combinação de região](#scale-up-from-an-unsupported-resource-group-and-region-combination)

## <a name="automate-with-scripts"></a>Automatizar com scripts

Pode automatizar a criação de aplicações no nível **PremiumV3** com scripts, utilizando o [Azure CLI](/cli/azure/install-azure-cli) ou [o Azure PowerShell](/powershell/azure/).

### <a name="azure-cli"></a>CLI do Azure

O seguinte comando cria um plano de Serviço de Aplicações em _P1V2_ . Podes executá-lo na Cloud Shell. As opções `--sku` para são P1V3, _P2V3_ e _P3V3._

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O seguinte comando cria um plano de Serviço de Aplicações em _P1V3_ . As opções `-WorkerSize` para são _Pequenas,_ _Médias_ e _Grandes._

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>Mais recursos

[Dimensione uma app em Azure](manage-scale-up.md) 
 [Contagem de instância de escala manual ou automática](../azure-monitor/platform/autoscale-get-started.md)
