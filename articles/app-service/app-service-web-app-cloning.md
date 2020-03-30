---
title: Clonar aplicação com o PowerShell
description: Aprenda a clonar a sua app App Service para uma nova aplicação usando o PowerShell. Uma variedade de cenários de clonagem são cobertos, incluindo a integração do Gestor de Tráfego.
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.custom: seodec18
ms.openlocfilehash: e7ad45ea4cb1049ed7eeb454162e23e81ed35019
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255194"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Clonagem de aplicativos de aplicativo sinuoso usando powershell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Com o lançamento da versão 1.1.1.0 do Microsoft Azure PowerShell, foi adicionada uma nova opção que `New-AzWebApp` permite clonar uma aplicação app Service existente para uma aplicação recém-criada numa região diferente ou na mesma região. Esta opção permite que os clientes implementem uma série de aplicações em diferentes regiões de forma rápida e fácil.

A clonagem de aplicativos é suportada para planos de serviço de aplicações Standard, Premium, Premium V2 e Isolados. A nova funcionalidade utiliza as mesmas limitações que a funcionalidade de backup de serviço de aplicação, consulte [o Back up a uma aplicação no Azure App Service](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Clonagem de uma app existente
Cenário: Uma aplicação existente na região centro-sul dos EUA, e você quer clonar os conteúdos para uma nova app na região do Centro-Norte dos EUA. Pode ser realizado utilizando a versão Do Gestor de Recursos Azure do cmdlet PowerShell para criar uma nova app com a opção. `-SourceWebApp`

Conhecendo o nome do grupo de recursos que contém a aplicação fonte, pode utilizar o `source-webapp`seguinte comando PowerShell para obter as informações da aplicação de origem (neste caso nomeado):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Para criar um novo Plano de `New-AzAppServicePlan` Serviço de Aplicações, pode usar o comando como no exemplo seguinte

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

Utilizando `New-AzWebApp` o comando, pode criar a nova app na região centro-norte dos EUA e ligá-la a um Plano de Serviço de Aplicações existente. Além disso, pode utilizar o mesmo grupo de recursos que a aplicação fonte, ou definir um novo grupo de recursos, como mostra o seguinte comando:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Para clonar uma aplicação existente, incluindo todas as `IncludeSourceWebAppSlots` ranhuras de implementação associadas, é necessário utilizar o parâmetro.  Note que `IncludeSourceWebAppSlots` o parâmetro só é suportado para clonagem de uma aplicação inteira, incluindo todas as suas ranhuras. O seguinte comando PowerShell demonstra a utilização `New-AzWebApp` desse parâmetro com o comando:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Para clonar uma aplicação existente dentro da mesma região, é necessário criar um novo grupo de recursos e um novo plano de serviço de aplicações na mesma região e, em seguida, usar o seguinte comando PowerShell para clonar a app:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonagem de uma app existente para um ambiente de serviço de aplicações
Cenário: Uma aplicação existente na região centro-sul dos EUA, e você quer clonar os conteúdos para uma nova app para um App Service Environment (ASE) existente.

Conhecendo o nome do grupo de recursos que contém a aplicação fonte, pode utilizar o `source-webapp`seguinte comando PowerShell para obter as informações da aplicação de origem (neste caso nomeado):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Conhecendo o nome da ASE e o nome de grupo de recursos a que a ASE pertence, pode criar a nova app na ASE existente, como mostra o seguinte comando:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

O `Location` parâmetro é necessário devido à razão do legado, mas é ignorado quando se cria a aplicação numa ASE. 

## <a name="cloning-an-existing-app-slot"></a>Clonagem de uma ranhura de app existente
Cenário: Pretende clonar uma ranhura de implementação existente de uma aplicação para uma nova app ou uma nova ranhura. A nova aplicação pode estar na mesma região que a ranhura original da aplicação ou numa região diferente.

Conhecendo o nome do grupo de recursos que contém a aplicação fonte, pode utilizar o seguinte `source-appslot`comando `source-app`PowerShell para obter as informações da aplicação de origem (neste caso nomeado ) ligadas a:

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

O seguinte comando demonstra a criação de um clone da app de origem para uma nova aplicação:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Configurar o Gestor de Tráfego ao clonar uma aplicação
Criar aplicações multi-regiões e configurar o Azure Traffic Manager para direcionar o tráfego para todas estas aplicações, é um cenário importante para garantir que as aplicações dos clientes estão altamente disponíveis. Ao clonar uma aplicação existente, tem a opção de ligar ambas as aplicações a um novo perfil de gestor de tráfego ou a uma existente. Apenas a versão do Gestor de Recursos Azure do Traffic Manager é suportada.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Criar um novo perfil de Gestor de Tráfego ao clonar uma app
Cenário: Pretende clonar uma aplicação para outra região, ao mesmo tempo que configura um perfil de gestor de tráfego do Gestor de Recursos Azure que inclui ambas as aplicações. O seguinte comando demonstra a criação de um clone da app de origem para uma nova aplicação enquanto configura um novo perfil do Traffic Manager:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Adicionar nova app clonada a um perfil de Gestor de Tráfego existente
Cenário: Já tem um perfil de Gestor de Recursos Azure e quer adicionar ambas as aplicações como pontos finais. Para isso, primeiro é necessário montar o perfil de perfil do gestor de tráfego existente. Você precisa do ID de subscrição, o nome do grupo de recursos, e o nome de perfil do gestor de tráfego existente.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Depois de ter o ID de manjedoura de tráfego, o seguinte comando demonstra a criação de um clone da app de origem para uma nova aplicação, adicionando-as a um perfil de Traffic Manager existente:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Restrições atuais
Aqui estão as restrições conhecidas da clonagem de apps:

* As definições de escala automática não são clonadas
* As definições de horário de backup não são clonadas
* As definições vNET não são clonadas
* App Insights não são automaticamente configurados na aplicação de destino
* As configurações easy Auth não são clonadas
* Extensão de Kudu não são clonados
* As regras do TIP não são clonadas
* O conteúdo da base de dados não é clonado
* Endereços IP de saída mudam se clonagem para uma unidade de escala diferente
* Não disponível para Aplicativos Linux

### <a name="references"></a>Referências
* [Clonagem de serviço sinuoso](app-service-web-app-cloning.md)
* [Back up uma app no Azure App Service](manage-backup.md)
* [Suporte do Gestor de Recursos Azure para pré-visualização do Gestor de Tráfego do Azure](../traffic-manager/traffic-manager-powershell-arm.md)
* [Introdução ao Ambiente do Serviço de Aplicações](environment/intro.md)
* [Utilizar o Azure PowerShell com o Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)

