---
title: Clonar aplicação com o PowerShell - serviço de aplicações do Azure
description: Saiba como clonar a aplicação de serviço de aplicações para uma nova aplicação com o PowerShell.
services: app-service\web
documentationcenter: ''
author: ahmedelnably
manager: stefsch
editor: ''
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: 53cde81ed5df97c4cb6d8360c9bb639b8bdabe20
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818141"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Aplicação de serviço de aplicações do Azure com o PowerShell de clonagem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Com o lançamento do Microsoft Azure PowerShell versão 1.1.0, foi adicionada uma nova opção para `New-AzWebApp` que permite-lhe clonar uma aplicação de serviço de aplicações existente para uma aplicação criada recentemente numa região diferente ou na mesma região. Esta opção permite aos clientes implementar um número de aplicações em regiões diferentes, rápida e facilmente.

Clonagem de aplicação é atualmente suportado apenas para planos de serviço de aplicações de escalão premium. O novo recurso utiliza as mesmas limitações, como a funcionalidade de cópia de segurança de serviço de aplicações, consulte [cópia de segurança uma aplicação no App Service do Azure](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Clonar uma aplicação existente
Cenário: Uma aplicação existente na região Centro-Sul e deseja clonar os conteúdos para uma nova aplicação na região e.u.a. Centro-Norte. Pode ser feito ao utilizar a versão do Azure Resource Manager do cmdlet do PowerShell para criar uma nova aplicação com o `-SourceWebApp` opção.

Sabendo o nome do grupo de recursos que contém a aplicação de origem, pode utilizar o seguinte comando do PowerShell para obter informações da aplicação de origem (neste caso com o nome `source-webapp`):

```PowerShell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Para criar um novo plano de serviço de aplicações, pode utilizar `New-AzAppServicePlan` comando como no exemplo seguinte

```PowerShell
New-AzAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

Usando o `New-AzWebApp` de comando, pode criar a nova aplicação na região e.u.a. Centro-Norte e vinculá-lo a um existente. o escalão premium a plano do serviço de aplicações. Além disso, pode utilizar o mesmo grupo de recursos como a aplicação de origem ou definir um novo grupo de recursos, conforme mostrado no comando seguinte:

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Para clonar uma aplicação existente, incluindo todas as ranhuras de implementação associada, tem de utilizar o `IncludeSourceWebAppSlots` parâmetro. O seguinte comando PowerShell demonstra o uso desse parâmetro com o `New-AzWebApp` comando:

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Para clonar uma aplicação existente na mesma região, tem de criar um novo grupo de recursos e um novo serviço de aplicações planear na mesma região e, em seguida, utilize o seguinte comando do PowerShell para clonar a aplicação:

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonar uma aplicação existente para um ambiente de serviço de aplicações
Cenário: Uma aplicação existente na região Centro-Sul e deseja clonar os conteúdos para uma nova aplicação para um ambiente de serviço do existente aplicações (ASE).

Sabendo o nome do grupo de recursos que contém a aplicação de origem, pode utilizar o seguinte comando do PowerShell para obter informações da aplicação de origem (neste caso com o nome `source-webapp`):

```PowerShell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Sabendo o nome do ASE e o nome do grupo de recursos que o ASE pertence, pode criar a nova aplicação num ASE de existente, conforme mostrado no comando seguinte:

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

O `Location` parâmetro é necessário devido ao motivo herdado, mas ela é ignorada quando criar a aplicação num ASE. 

## <a name="cloning-an-existing-app-slot"></a>A clonagem de uma ranhura de aplicação existente
Cenário: Deseja clonar um bloco de implementação existente de uma aplicação a uma aplicação nova ou uma nova ranhura. A nova aplicação pode ser na mesma região que a ranhura de aplicação original ou numa região diferente.

Sabendo o nome do grupo de recursos que contém a aplicação de origem, pode utilizar o seguinte comando do PowerShell para obter informações a ranhura de aplicação de origem (neste caso com o nome `source-appslot`) associado ao `source-app`:

```PowerShell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

O comando seguinte demonstra a criação de um clone da aplicação de origem para uma nova aplicação:

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Configurar o Gestor de tráfego durante a clonagem de uma aplicação
Criar aplicações de várias regiões e configurar o Gestor de tráfego do Azure para encaminhar tráfego para estas aplicações, é um cenário importante para se certificar de que aplicações dos clientes são de elevada disponibilidade. Ao clonar uma aplicação existente, tem a opção para ligar a ambas as aplicações para um novo perfil do Gestor de tráfego ou um já existente. Apenas a versão do Azure Resource Manager do Gestor de tráfego é suportada.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Criar um novo perfil de Gestor de tráfego durante a clonagem de uma aplicação
Cenário: Deseja clonar uma aplicação para outra região, ao configurar um perfil do Gestor de tráfego do Azure Resource Manager que inclua a ambas as aplicações. O comando seguinte demonstra a criação de um clone da aplicação de origem para uma nova aplicação ao configurar um novo perfil do Gestor de tráfego:

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>A adicionar novas clonar a aplicação para um perfil existente do Gestor de tráfego
Cenário: Já tiver um perfil do Gestor de tráfego do Azure Resource Manager e pretender adicionar ambas as aplicações como pontos finais. Para isso, primeiro tem de montar o tráfego de existente perfil do Gestor de ID. Terá do ID de subscrição, o nome do grupo de recursos e o nome de perfil de Gestor de tráfego existentes.

```PowerShell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Depois de ter o ID de gerente de tráfego, o seguinte comando demonstra a criação de um clone da aplicação de origem para uma nova aplicação ao adicioná-los para um perfil existente do Gestor de tráfego:

```PowerShell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Restrições de atuais
Aqui estão as restrições de conhecidos de clonagem de aplicação:

* Definições de dimensionamento automático não são Clonadas
* Definições da agenda de cópia de segurança não são Clonadas
* Definições da VNET não são Clonadas
* O App Insights não são automaticamente configurado na aplicação de destino
* Não são Clonadas fácil definições de autenticação
* Extensão do kudu não são Clonadas
* Regras de sugestão não são Clonadas
* Conteúdo de base de dados não for clonado
* Endereços IP de saída é alterado se uma unidade de escala diferente para a clonagem

### <a name="references"></a>Referências
* [Clonagem de serviço de aplicações](app-service-web-app-cloning.md)
* [Fazer cópias de segurança de uma aplicação no App Service do Azure](manage-backup.md)
* [Suporte do Azure Resource Manager para a pré-visualização do Gestor de tráfego do Azure](../traffic-manager/traffic-manager-powershell-arm.md)
* [Introdução ao Ambiente do Serviço de Aplicações](environment/intro.md)
* [Utilizar o Azure PowerShell com o Azure Resource Manager](../azure-resource-manager/manage-resources-powershell.md)

