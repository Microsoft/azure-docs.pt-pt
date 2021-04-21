---
title: Escala por aplicação para hospedagem de alta densidade
description: Dimensione as aplicações independentemente dos planos do Serviço de Aplicações e otimize as instâncias dimensionais do seu plano.
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 756117a2a231fcb406fd3e3102a16c318c621aa0
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832613"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Hospedagem de alta densidade no Azure App Service usando escalamento por aplicação

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ao utilizar o Serviço de Aplicações, pode escalar as suas aplicações escalando o plano de Serviço de [Aplicações](overview-hosting-plans.md) em que funcionam. Quando várias aplicações são executadas no mesmo plano de Serviço de Aplicações, cada instância em escala executa todas as aplicações no plano.

*O dimensionamento por aplicação* pode ser ativado ao nível do plano do Serviço de Aplicações para permitir o dimensionamento de uma aplicação independentemente do plano de Serviço de Aplicações que a acolhe. Desta forma, um plano de Serviço de Aplicações pode ser dimensionado para 10 instâncias, mas uma aplicação pode ser configurada para usar apenas cinco.

> [!NOTE]
> O escalonamento por aplicação só está disponível para os níveis de preços **Standard,** **Premium,** **Premium, Premium V2** e **Isolados.**
>

As aplicações são atribuídas ao plano de Serviço de Aplicações disponível usando uma melhor abordagem de esforço para uma distribuição uniforme entre instâncias. Apesar de não estar garantida uma distribuição uniforme, a plataforma irá certificar-se de que duas instâncias da mesma aplicação não serão hospedadas na mesma instância do plano de Aplicações.

A plataforma não depende de métricas para decidir sobre a atribuição de trabalhadores. As aplicações só são reequilibradas quando as instâncias são adicionadas ou removidas do plano de Serviço de Aplicações.

## <a name="per-app-scaling-using-powershell"></a>Por escala de aplicativo usando PowerShell

Crie um plano com escala por aplicação, passando o ```-PerSiteScaling $true``` parâmetro para o ```New-AzAppServicePlan``` cmdlet.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Ativar o dimensionamento por aplicação com um Plano de Serviço de Aplicações existente, passando o `-PerSiteScaling $true` parâmetro para o ```Set-AzAppServicePlan``` cmdlet.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

A nível de aplicações, configurar o número de casos que a aplicação pode usar no plano de Serviço de Aplicações.

No exemplo abaixo, a aplicação está limitada a duas instâncias, independentemente de quantas instâncias o plano de serviço de aplicações subjacente se escasseia.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` é diferente de `$newapp.MaxNumberOfWorkers` . Por aplicação de escala utiliza `$newapp.SiteConfig.NumberOfWorkers` para determinar as características da escala da aplicação.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Escala por aplicação usando Azure Resource Manager

O modelo azure resource manager cria:

- Um plano de Serviço de Aplicações que foi reduzido a 10 instâncias
- uma aplicação configurada para escalar a um máximo de cinco instâncias.

O plano de Serviço de Aplicações está a definir a propriedade **PerSiteScaling** como `"perSiteScaling": true` verdadeira. A aplicação está a definir o **número de trabalhadores** a utilizar para 5 `"properties": { "numberOfWorkers": "5" }` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Configuração recomendada para hospedagem de alta densidade

Por escala de aplicações é uma funcionalidade que é ativada tanto nas regiões globais de Azure como em [Ambientes de Serviço de Aplicações.](environment/app-service-app-service-environment-intro.md) No entanto, a estratégia recomendada é usar os Ambientes de Serviço de Aplicações para tirar partido das suas funcionalidades avançadas e da maior capacidade do plano de serviço de aplicações.  

Siga estes passos para configurar hospedagem de alta densidade para as suas aplicações:

1. Designe um plano de Serviço de Aplicações como o plano de alta densidade e dimensione-o para a capacidade desejada.
1. Desajei a `PerSiteScaling` bandeira ao plano do Serviço de Aplicações.
1. As novas aplicações são criadas e atribuídas a esse plano de Serviço de Aplicações com o **número de propriedadesofWorkers** definido para **1**.
   - A utilização desta configuração produz a maior densidade possível.
1. O número de trabalhadores pode ser configurado de forma independente por app para conceder recursos adicionais, se necessário. Por exemplo:
   - Uma aplicação de alta utilização pode definir **o númeroOfWorkers** para **3** para ter mais capacidade de processamento para essa aplicação.
   - Aplicações de baixo uso definiriam **o númeroOfWorkers** para **1**.

## <a name="next-steps"></a>Passos seguintes

- [Azure App Service planeia uma visão geral aprofundada](overview-hosting-plans.md)
- [Introdução ao Ambiente do Serviço de Aplicações](environment/app-service-app-service-environment-intro.md)
