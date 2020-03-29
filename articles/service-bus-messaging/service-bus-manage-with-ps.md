---
title: Use a PowerShell para gerir os recursos do Azure Service Bus [ Microsoft Docs
description: Este artigo explica como usar o módulo Azure PowerShell para criar e gerir entidades de Ônibus de serviço (espaços de nome, filas, tópicos, subscrições).
services: service-bus-messaging
documentationcenter: .NET
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: e333dfb109840538fd5dec8110e1c32adedce989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759266"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Use a PowerShell para gerir os recursos do Ônibus de serviço

O Microsoft Azure PowerShell é um ambiente de scripts que pode usar para controlar e automatizar a implementação e gestão dos serviços Azure. Este artigo descreve como usar o [módulo PowerShell do Gestor](/powershell/module/az.servicebus) de Recursos de Ônibus de serviço para fornecer e gerir entidades de ônibus de serviço (espaços de nome, filas, tópicos e subscrições) usando uma consola ou script Azure PowerShell local.

Também pode gerir entidades de ônibus de serviço usando modelos de Gestor de Recursos Azure. Para mais informações, consulte o artigo [Criar recursos de ônibus de serviço utilizando modelos](service-bus-resource-manager-overview.md)de Gestor de Recursos Azure .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, precisará dos seguintes pré-requisitos:

* Uma subscrição do Azure. Para obter mais informações sobre a obtenção de uma subscrição, consulte opções de [compra,][purchase options] [ofertas de membros][member offers]ou [conta gratuita.][free account]
* Um computador com Azure PowerShell. Para obter instruções, consulte Iniciar com [cmdlets Azure PowerShell](/powershell/azure/get-started-azureps).
* Uma compreensão geral dos scripts PowerShell, pacotes NuGet e o .NET Framework.

## <a name="get-started"></a>Introdução

O primeiro passo é usar o PowerShell para iniciar sessão na sua conta Azure e na subscrição do Azure. Siga as instruções em [Get started com cmdlets Azure PowerShell](/powershell/azure/get-started-azureps) para iniciar o login na sua conta Azure, e recuperar e aceder aos recursos na sua subscrição Azure.

## <a name="provision-a-service-bus-namespace"></a>Provision a Service Bus namespace

Ao trabalhar com espaços de nome service Bus, pode utilizar o [Espaço Get-AzServiceBusNamespace,](/powershell/module/az.servicebus/get-azservicebusnamespace) [New-AzServiceBusNamespace,](/powershell/module/az.servicebus/new-azservicebusnamespace) [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace)e [Set-AzServiceBusNamespace](/powershell/module/az.servicebus/set-azservicebusnamespace) cmdlets.

Este exemplo cria algumas variáveis locais no script; `$Namespace` e. `$Location`

* `$Namespace`é o nome do espaço de nome service bus com o qual queremos trabalhar.
* `$Location`identifica o centro de dados no qual disponibilizamos o espaço de nome.
* `$CurrentNamespace`armazena o espaço de nome de referência que recuperamos (ou criamos).

Num guião `$Namespace` real, e `$Location` pode ser passado como parâmetros.

Esta parte do guião faz o seguinte:

1. Tenta recuperar um espaço de nome do Ônibus de serviço com o nome especificado.
2. Se o espaço do nome for encontrado, relata o que foi encontrado.
3. Se o espaço de nome não for encontrado, cria o espaço de nome e depois recupera o espaço de nome recém-criado.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Criar uma regra de autorização de espaço-nome

O exemplo seguinte mostra como gerir as regras de autorização de espaço de nome utilizando a [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule), [Get-AzServiceBusAuthorizationRule,](/powershell/module/az.servicebus/get-azservicebusauthorizationrule) [Set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule)e [Remove-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule) cmdlets.

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzServiceBusKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
}
```

## <a name="create-a-queue"></a>Criar uma fila

Para criar uma fila ou tópico, execute uma verificação de espaço de nome utilizando o script na secção anterior. Em seguida, criar a fila:

```powershell
# Check if queue already exists
$CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Modificar propriedades da fila

Depois de executar o script na secção anterior, pode utilizar o cmdlet [Set-AzServiceBusQueue](/powershell/module/az.servicebus/set-azservicebusqueue) para atualizar as propriedades de uma fila, como no seguinte exemplo:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Provisionamento de outras entidades de ônibus de serviço

Pode utilizar o [módulo Service Bus PowerShell](/powershell/module/az.servicebus) para fornecer outras entidades, como tópicos e subscrições. Estes cmdlets são sintáticamente semelhantes às cmdlets de criação de fila demonstrados na secção anterior.

## <a name="next-steps"></a>Passos seguintes

- Consulte [aqui](/powershell/module/az.servicebus)a documentação completa do módulo PowerShell do Gestor de Recursos de Autocarros de Serviços . Esta página lista todos os cmdlets disponíveis.
- Para obter informações sobre a utilização de modelos do Gestor de Recursos Azure, consulte o artigo Criar recursos de ônibus de serviço utilizando modelos de [Gestor de Recursos Azure](service-bus-resource-manager-overview.md).
- Informações sobre bibliotecas de [gestão service Bus .NET.](service-bus-management-libraries.md)

Existem algumas formas alternativas de gerir as entidades de Ônibus de serviço, como descrito nestas publicações de blog:

* [Como criar filas de ônibus de serviço, tópicos e subscrições usando um script PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Como criar um espaço de nome de ônibus de serviço e um hub de eventousando um script PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Scripts PowerShell de ônibus de serviço](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: https://azure.microsoft.com/pricing/purchase-options/
[member offers]: https://azure.microsoft.com/pricing/member-offers/
[free account]: https://azure.microsoft.com/pricing/free-trial/
