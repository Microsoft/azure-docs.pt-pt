---
title: Clonar aplicação com o PowerShell
description: Saiba como clonar a sua aplicação app App Service para uma nova aplicação utilizando o PowerShell. Uma variedade de cenários de clonagem são cobertos, incluindo a integração do Gestor de Tráfego.
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.custom: seodec18
ms.openlocfilehash: e3ae342e7cbd8a9c2e126de7666d07f0664be407
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103573647"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Clonagem de aplicativos Azure App usando PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Com o lançamento da versão 1.1.0 do Microsoft Azure PowerShell, foi adicionada uma nova opção `New-AzWebApp` que permite clonar uma aplicação de Serviço de Aplicações existente para uma aplicação recém-criada numa região diferente ou na mesma região. Esta opção permite que os clientes implementem uma série de aplicações em diferentes regiões de forma rápida e fácil.

A clonagem de aplicações é suportada para planos de serviço de aplicações Standard, Premium, Premium, Premium V2 e Isolados. A nova funcionalidade utiliza as mesmas limitações que a funcionalidade de Backup do Serviço de Aplicações, consulte [Back up uma aplicação no Azure App Service](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Clonagem de uma aplicação existente
Cenário: Uma aplicação existente na região sul dos EUA, e você quer clonar o conteúdo para uma nova app na região norte-americana central. Pode ser realizado utilizando a versão Azure Resource Manager do cmdlet PowerShell para criar uma nova app com a `-SourceWebApp` opção.

Conhecendo o nome do grupo de recursos que contém a aplicação de origem, pode utilizar o seguinte comando PowerShell para obter as informações da aplicação de origem (neste caso `source-webapp` denominado):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Para criar um novo Plano de Serviço de Aplicações, pode utilizar `New-AzAppServicePlan` o comando como no exemplo seguinte

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

Utilizando o `New-AzWebApp` comando, pode criar a nova aplicação na região norte dos EUA e ligá-la a um Plano de Serviço de Aplicações existente. Além disso, pode utilizar o mesmo grupo de recursos que a app de origem, ou definir um novo grupo de recursos, como mostrado no seguinte comando:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Para clonar uma aplicação existente, incluindo todas as ranhuras de implementação associadas, é necessário utilizar o `IncludeSourceWebAppSlots` parâmetro.  Note que o `IncludeSourceWebAppSlots` parâmetro só é suportado para clonagem de uma aplicação inteira, incluindo todas as suas ranhuras. O seguinte comando PowerShell demonstra a utilização desse parâmetro com o `New-AzWebApp` comando:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Para clonar uma aplicação existente na mesma região, é necessário criar um novo grupo de recursos e um novo plano de serviço de aplicações na mesma região e, em seguida, utilizar o seguinte comando PowerShell para clonar a app:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonagem de uma app existente para um Ambiente de Serviço de Aplicações
Cenário: Uma aplicação existente na região sul dos EUA, e pretende clonar os conteúdos para uma nova aplicação para um Ambiente de Serviço de Aplicações existente (ASE).

Conhecendo o nome do grupo de recursos que contém a aplicação de origem, pode utilizar o seguinte comando PowerShell para obter as informações da aplicação de origem (neste caso `source-webapp` denominado):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Conhecendo o nome da ASE, e o nome do grupo de recursos a que pertence o ASE, pode criar a nova app no ASE existente, como mostra o seguinte comando:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

O `Location` parâmetro é necessário devido a uma razão antiga, mas é ignorado quando cria a app num ASE. 

## <a name="cloning-an-existing-app-slot"></a>Clonagem de uma ranhura de aplicação existente
Cenário: Pretende clonar uma ranhura de implementação existente de uma aplicação para uma nova aplicação ou uma nova slot. A nova aplicação pode estar na mesma região que o slot original da aplicação ou numa região diferente.

Conhecendo o nome do grupo de recursos que contém a aplicação de origem, pode utilizar o seguinte comando PowerShell para obter as informações da ranhura da aplicação de origem (neste caso `source-appslot` denominada) ligadas `source-app` a:

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

O seguinte comando demonstra a criação de um clone da aplicação de origem para uma nova aplicação:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Configurar o Traffic Manager ao clonar uma aplicação
Criar aplicações multi-regiões e configurar o Azure Traffic Manager para encaminhar o tráfego para todas estas aplicações, é um cenário importante para garantir que as aplicações dos clientes estão altamente disponíveis. Ao clonar uma aplicação existente, tem a opção de ligar ambas as aplicações a um novo perfil de gestor de tráfego ou a uma existente. Apenas a versão Azure Resource Manager do Traffic Manager é suportada.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Criar um novo perfil de Gestor de Tráfego ao clonar uma aplicação
Cenário: Pretende clonar uma aplicação para outra região, ao mesmo tempo que configura um perfil de gestor de tráfego do Azure Resource Manager que inclui ambas as aplicações. O seguinte comando demonstra a criação de um clone da aplicação de origem para uma nova aplicação, ao mesmo tempo que configura um novo perfil de Gestor de Tráfego:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Adicionar nova app clonada a um perfil de Gestor de Tráfego existente
Cenário: Já tem um perfil de gestor de tráfego do Azure Resource Manager e quer adicionar ambas as aplicações como pontos finais. Para tal, primeiro tem de montar o iD do perfil do gestor de tráfego existente. Precisa do ID de subscrição, do nome do grupo de recursos e do nome do gestor de tráfego existente.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Depois de ter o ID da manjedoura de tráfego, o seguinte comando demonstra a criação de um clone da aplicação de origem para uma nova aplicação, adicionando-as a um perfil de Gestor de Tráfego existente:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```
> [!NOTE]
> Se estiver a receber um erro que indique que "a validação SSL no nome de anfitrião do gestor de tráfego está a falhar" então sugerimos que utilize -IgnoreCustomHostNames atributo no cmdlet powershell enquanto realiza a operação do clone ou então use o portal.

## <a name="current-restrictions"></a>Restrições atuais
Aqui estão as conhecidas restrições da clonagem de aplicações:

* As definições de escala automática não são clonadas
* As definições de agenda de backup não são clonadas
* As definições do VNET não são clonadas
* Os Insights da App não são automaticamente configuradas na aplicação de destino
* As configurações easy Auth não são clonadas
* A extensão de Kudu não é clonada
* As regras do TiP não são clonadas
* O conteúdo da base de dados não é clonado
* Endereços IP de saída alterações se a clonagem para uma unidade de escala diferente
* Não disponível para aplicativos Linux
* Identidades geridas não são clonadas

### <a name="references"></a>Referências
* [Clonagem de Serviços de Aplicações](app-service-web-app-cloning.md)
* [Fazer o back up de uma aplicação no Azure App Service](manage-backup.md)
* [Suporte do Gestor de Recursos Azure para pré-visualização do Gestor de Tráfego do Azure](../traffic-manager/traffic-manager-powershell-arm.md)
* [Introdução ao Ambiente do Serviço de Aplicações](environment/intro.md)
* [Utilizar o Azure PowerShell com o Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)

