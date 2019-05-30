---
title: Alojamento de alta densidade usando por aplicação dimensionamento - serviço de aplicações do Azure | Documentos da Microsoft
description: Alojamento de alta densidade no App Service do Azure
author: btardif
manager: erikre
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 824abbdfd1b3980b419e6d6c46814bb0318adf13
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602325"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Alojamento de alta densidade no serviço de aplicações do Azure com o dimensionamento por aplicação

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ao utilizar o serviço de aplicações, pode dimensionar as suas aplicações através do dimensionamento do [plano do App Service](overview-hosting-plans.md) que são executados. Quando várias aplicações são executadas no mesmo plano de serviço de aplicações, cada instância de escalamento horizontal é executada todas as aplicações no plano.

*Dimensionar por aplicação* pode ser ativada ao nível do plano do serviço de aplicações para permitir dimensionar uma aplicação, independentemente do plano de serviço de aplicações que o hospeda. Dessa forma, um plano do serviço de aplicações pode ser dimensionado até 10 instâncias, mas uma aplicação pode ser definida para utilizar apenas cinco.

> [!NOTE]
> Dimensionar por aplicação só está disponível para **padrão**, **Premium**, **Premium V2** e **Isolated** escalões de preço.
>

Aplicações são alocadas ao plano de serviço de aplicações disponível com uma melhor abordagem de esforço para uma distribuição uniforme entre instâncias. Embora uma distribuição uniforme não é garantida, a plataforma será Certifique-se de que as duas instâncias da mesma aplicação não serão alojadas na mesma instância de plano de serviço de aplicações.

A plataforma não depende de métricas para decidir qual a alocação de trabalho. Aplicativos são reequilibrados apenas quando instâncias são adicionadas ou removidas do plano de serviço de aplicações.

## <a name="per-app-scaling-using-powershell"></a>Por aplicação de dimensionamento com o PowerShell

Criar um plano com o dimensionamento, passando por aplicação a ```-PerSiteScaling $true``` parâmetro para o ```New-AzAppServicePlan``` cmdlet.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Ativar o dimensionamento com um plano de serviço de aplicações existentes, passando por aplicação a `-PerSiteScaling $true` parâmetro para o ```Set-AzAppServicePlan``` cmdlet.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Ao nível da aplicação, configure o número de instâncias que a aplicação pode utilizar no plano do serviço de aplicações.

No exemplo abaixo, a aplicação está limitada a duas instâncias, independentemente de quantas instâncias do plano de serviço de aplicações subjacente aumenta horizontalmente para.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` é diferente do `$newapp.MaxNumberOfWorkers`. Utilizações de dimensionamento por aplicação `$newapp.SiteConfig.NumberOfWorkers` para determinar as características de dimensionamento da aplicação.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Por aplicação de dimensionamento com o Azure Resource Manager

Cria o modelo Azure Resource Manager seguinte:

- Um plano de serviço de aplicações é dimensionado para 10 instâncias
- uma aplicação que está configurada para escalar até um máximo de cinco instâncias.

O plano do serviço de aplicações é a configuração do **PerSiteScaling** propriedade como true `"perSiteScaling": true`. A aplicação é a configuração do **número de workers** a utilizar para 5 `"properties": { "numberOfWorkers": "5" }`.

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

## <a name="recommended-configuration-for-high-density-hosting"></a>Configuração recomendada para alojamento de alta densidade

Por aplicação dimensionamento é uma funcionalidade que está ativada em ambas as regiões do Azure global e [ambientes de serviço de aplicações](environment/app-service-app-service-environment-intro.md). No entanto, a estratégia recomendada é utilizar ambientes do serviço de aplicações para tirar partido dos seus recursos avançados e a maior capacidade de plano de serviço de aplicações.  

Siga estes passos para configurar o alojamento de alta densidade para as suas aplicações:

1. Designar um plano do serviço de aplicações como o plano de alta densidade e dimensione-a para a capacidade desejada.
1. Definir o `PerSiteScaling` sinalizador como true no plano de serviço de aplicações.
1. Novas aplicações são criadas e atribuídas a esse plano de serviço de aplicações com o **numberOfWorkers** definida como **1**.
   - Utilizando esta configuração produz a mais alta densidade possível.
1. O número de trabalhadores pode ser configurado de forma independente por aplicação para conceder recursos adicionais, conforme necessário. Por exemplo:
   - Pode definir uma aplicação de uso intenso **numberOfWorkers** ao **3** tenham maior capacidade de processamento para essa aplicação.
   - Aplicações de baixa utilização definiria **numberOfWorkers** ao **1**.

## <a name="next-steps"></a>Passos Seguintes

- [Descrição geral aprofundada dos planos do serviço de aplicações do Azure](overview-hosting-plans.md)
- [Introdução ao Ambiente do Serviço de Aplicações](environment/app-service-app-service-environment-intro.md)
