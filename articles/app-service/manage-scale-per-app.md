---
title: Escala por app para hospedagem de alta densidade
description: Dimensione as aplicações independentemente dos planos do App Service e otimize as instâncias de escala no seu plano.
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: f1ca4958fe2608d0c040ef5b93827a7e71a4151c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74672356"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Hospedagem de alta densidade no Serviço de Aplicações Azure usando escala por app

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ao utilizar o App Service, pode escalar as suas aplicações escalando o plano de Serviço de [Aplicações](overview-hosting-plans.md) em que funcionam. Quando várias aplicações são executadas no mesmo plano de App Service, cada instância em escala executa todas as aplicações do plano.

*A escala por aplicação* pode ser ativada ao nível do plano do App Service para permitir a escala de uma aplicação independentemente do plano de Serviço de Aplicações que a acolhe. Desta forma, um plano de Serviço de Aplicações pode ser dimensionado para 10 instâncias, mas uma aplicação pode ser definida para usar apenas cinco.

> [!NOTE]
> A escala por app está disponível apenas para os níveis de preços **Standard,** **Premium,** **Premium V2** e **Isolados.**
>

As aplicações são atribuídas ao plano de Serviço de Aplicações disponível, utilizando uma melhor abordagem de esforço para uma distribuição uniforme em instâncias. Embora não seja garantida uma distribuição uniforme, a plataforma garantirá que duas instâncias da mesma aplicação não serão alojadas na mesma instância do plano de app Service.

A plataforma não depende de métricas para decidir sobre a atribuição de trabalhadores. As aplicações só são reequilibradas quando as instâncias são adicionadas ou removidas do plano de Serviço de Aplicações.

## <a name="per-app-scaling-using-powershell"></a>Por escala de aplicativo usando PowerShell

Crie um plano com escala por app ```-PerSiteScaling $true``` passando ```New-AzAppServicePlan``` o parâmetro para o cmdlet.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Ative a escala por app com um Plano de `-PerSiteScaling $true` Serviço de ```Set-AzAppServicePlan``` Aplicações existente, passando o parâmetro para o cmdlet.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Ao nível da aplicação, configure o número de casos que a aplicação pode usar no plano do Serviço de Aplicações.

No exemplo abaixo, a aplicação está limitada a duas instâncias, independentemente de quantas instâncias o plano de serviço de aplicações subjacente se baseia.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers`é diferente `$newapp.MaxNumberOfWorkers`de . A escala por app `$newapp.SiteConfig.NumberOfWorkers` utiliza para determinar as características de escala da aplicação.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Escala por app usando O Gestor de Recursos Azure

O seguinte modelo de Gestor de Recursos Azure cria:

- Um plano de serviço de aplicações que é escalado para 10 instâncias
- uma aplicação que está configurada para escalar para um máximo de cinco instâncias.

O plano do Serviço de Aplicações está `"perSiteScaling": true`a definir a propriedade **PerSiteScaling** como verdadeira . A aplicação está a definir o `"properties": { "numberOfWorkers": "5" }`número de **trabalhadores** para usar para 5 .

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

Por escala de aplicativos é uma funcionalidade que está ativada tanto nas regiões globais do Azure como nos Ambientes de Serviços de [Aplicações.](environment/app-service-app-service-environment-intro.md) No entanto, a estratégia recomendada é utilizar os Ambientes de Serviço de Aplicações para tirar partido das suas funcionalidades avançadas e da maior capacidade do plano de aplicações.  

Siga estes passos para configurar hospedagem de alta densidade para as suas apps:

1. Designe um plano de Serviço de Aplicações como o plano de alta densidade e escalda-o para a capacidade desejada.
1. Coloque `PerSiteScaling` a bandeira verdadeirano plano de Serviço de Aplicações.
1. Novas aplicações são criadas e atribuídas a esse plano de Serviço de Aplicações com o número de propriedades **Dos Trabalhadores** definido para **1**.
   - A utilização desta configuração produz a maior densidade possível.
1. O número de trabalhadores pode ser configurado de forma independente por app para conceder recursos adicionais conforme necessário. Por exemplo:
   - Uma aplicação de alta utilização pode definir **o númeroOfWorkers** para **3** para ter mais capacidade de processamento para essa aplicação.
   - As aplicações de baixo uso definiriam **o númeroOfWorkers** para **1**.

## <a name="next-steps"></a>Passos seguintes

- [Planos de serviço de aplicação azure em profundidade](overview-hosting-plans.md)
- [Introdução ao Ambiente do Serviço de Aplicações](environment/app-service-app-service-environment-intro.md)
