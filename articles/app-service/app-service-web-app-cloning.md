---
title: Clonar aplicação com o PowerShell
description: Saiba como clonar seu aplicativo do serviço de aplicativo para um novo aplicativo usando o PowerShell. Uma variedade de cenários de clonagem é abordada, incluindo A integração do Gerenciador de tráfego.
author: ahmedelnably
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: ccff07009d2f46f6d91b8e3c57158aa6ede3607e
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671439"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Clonagem de aplicativo de serviço Azure App usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Com o lançamento do Microsoft Azure PowerShell versão 1.1.0, uma nova opção foi adicionada ao `New-AzWebApp` que permite clonar um aplicativo do serviço de aplicativo existente em um aplicativo recém-criado em uma região diferente ou na mesma região. Essa opção permite que os clientes implantem vários aplicativos em diferentes regiões de forma rápida e fácil.

A clonagem de aplicativo tem suporte para planos de serviço de aplicativo padrão, Premium, Premium V2 e isolados. O novo recurso usa as mesmas limitações que o recurso de backup do serviço de aplicativo, consulte [fazer backup de um aplicativo no serviço de Azure app](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Clonando um aplicativo existente
Cenário: um aplicativo existente na região do Sul EUA Central e você deseja clonar o conteúdo para um novo aplicativo na região do Norte EUA Central. Isso pode ser feito usando a versão Azure Resource Manager do cmdlet do PowerShell para criar um novo aplicativo com a opção `-SourceWebApp`.

Sabendo o nome do grupo de recursos que contém o aplicativo de origem, você pode usar o seguinte comando do PowerShell para obter as informações do aplicativo de origem (nesse caso, denominada `source-webapp`):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Para criar um novo plano do serviço de aplicativo, você pode usar `New-AzAppServicePlan` comando como no exemplo a seguir

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

Usando o comando `New-AzWebApp`, você pode criar o novo aplicativo na região do Norte EUA Central e associá-lo a um plano do serviço de aplicativo existente. Além disso, você pode usar o mesmo grupo de recursos que o aplicativo de origem, ou definir um novo grupo de recursos, conforme mostrado no seguinte comando:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Para clonar um aplicativo existente, incluindo todos os slots de implantação associados, você precisa usar o parâmetro `IncludeSourceWebAppSlots`.  Observe que o parâmetro `IncludeSourceWebAppSlots` só tem suporte para clonagem de um aplicativo inteiro, incluindo todos os seus slots. O comando do PowerShell a seguir demonstra o uso desse parâmetro com o comando `New-AzWebApp`:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Para clonar um aplicativo existente na mesma região, você precisa criar um novo grupo de recursos e um novo plano do serviço de aplicativo na mesma região e, em seguida, usar o seguinte comando do PowerShell para clonar o aplicativo:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonando um aplicativo existente para um Ambiente do Serviço de Aplicativo
Cenário: um aplicativo existente na região do Sul EUA Central e você deseja clonar o conteúdo para um novo aplicativo em um Ambiente do Serviço de Aplicativo (ASE) existente.

Sabendo o nome do grupo de recursos que contém o aplicativo de origem, você pode usar o seguinte comando do PowerShell para obter as informações do aplicativo de origem (nesse caso, denominada `source-webapp`):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Sabendo o nome do ASE e o nome do grupo de recursos ao qual o ASE pertence, você pode criar o novo aplicativo no ASE existente, conforme mostrado no seguinte comando:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

O parâmetro `Location` é necessário devido ao motivo herdado, mas é ignorado quando você cria o aplicativo em um ASE. 

## <a name="cloning-an-existing-app-slot"></a>Clonando um slot de aplicativo existente
Cenário: você deseja clonar um slot de implantação existente de um aplicativo para um novo aplicativo ou um novo slot. O novo aplicativo pode estar na mesma região que o slot do aplicativo original ou em uma região diferente.

Sabendo o nome do grupo de recursos que contém o aplicativo de origem, você pode usar o seguinte comando do PowerShell para obter as informações do slot do aplicativo de origem (nesse caso, denominado `source-appslot`) vinculado a `source-app`:

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

O comando a seguir demonstra como criar um clone do aplicativo de origem para um novo aplicativo:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Configurando o Gerenciador de tráfego ao clonar um aplicativo
A criação de aplicativos de várias regiões e a configuração do Gerenciador de tráfego do Azure para rotear o tráfego para todos esses aplicativos é um cenário importante para garantir que os aplicativos dos clientes estejam altamente disponíveis. Ao clonar um aplicativo existente, você tem a opção de conectar os dois aplicativos a um novo perfil do Gerenciador de tráfego ou a um existente. Há suporte apenas para Azure Resource Manager versão do Gerenciador de tráfego.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Criando um novo perfil do Gerenciador de tráfego ao clonar um aplicativo
Cenário: você deseja clonar um aplicativo para outra região, enquanto configura um Azure Resource Manager perfil do Gerenciador de tráfego que inclui ambos os aplicativos. O comando a seguir demonstra como criar um clone do aplicativo de origem para um novo aplicativo ao configurar um novo perfil do Gerenciador de tráfego:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Adicionando novo aplicativo clonado a um perfil existente do Gerenciador de tráfego
Cenário: você já tem um Azure Resource Manager perfil do Gerenciador de tráfego e deseja adicionar ambos os aplicativos como pontos de extremidade. Para fazer isso, primeiro você precisa montar a ID do perfil do Gerenciador de tráfego existente. Você precisa da ID da assinatura, do nome do grupo de recursos e do nome do perfil do Gerenciador de tráfego existente.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Depois de ter a ID do Gerenciador de tráfego, o comando a seguir demonstra como criar um clone do aplicativo de origem para um novo aplicativo ao adicioná-lo a um perfil existente do Gerenciador de tráfego:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Restrições atuais
Aqui estão as restrições conhecidas da clonagem de aplicativo:

* As configurações de dimensionamento automático não são clonadas
* As configurações de agendamento de backup não são clonadas
* As configurações de VNET não são clonadas
* O app insights não é configurado automaticamente no aplicativo de destino
* As configurações de autenticação fácil não são clonadas
* A extensão kudu não é clonada
* As regras TiP não são clonadas
* O conteúdo do banco de dados não é clonado
* Os endereços IP de saída são alterados se estiver clonando para uma unidade de escala diferente
* Não disponível para aplicativos do Linux

### <a name="references"></a>Referências
* [Clonagem do serviço de aplicativo](app-service-web-app-cloning.md)
* [Fazer backup de um aplicativo no serviço Azure App](manage-backup.md)
* [Suporte de Azure Resource Manager para a versão prévia do Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-powershell-arm.md)
* [Introdução ao Ambiente do Serviço de Aplicações](environment/intro.md)
* [Utilizar o Azure PowerShell com o Azure Resource Manager](../azure-resource-manager/manage-resources-powershell.md)

