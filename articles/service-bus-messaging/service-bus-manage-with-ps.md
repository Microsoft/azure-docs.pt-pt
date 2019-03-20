---
title: Utilizar o PowerShell para gerir os recursos do Azure Service Bus | Documentos da Microsoft
description: Utilizar o módulo do PowerShell para criar e gerir recursos do Service Bus
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
ms.date: 09/21/2018
ms.author: aschhab
ms.openlocfilehash: 28bed5503c5c798f244a4ba3a070894aa4538b00
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57873031"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Utilize o PowerShell para gerir recursos do Service Bus

Microsoft Azure PowerShell é um ambiente de script que pode utilizar para controlar e automatizar a implementação e gestão de serviços do Azure. Este artigo descreve como utilizar o [módulo do PowerShell do Gestor de recursos do Service Bus](/powershell/module/azurerm.servicebus) para aprovisionar e gerir entidades do Service Bus (espaços de nomes, filas, tópicos e subscrições) através de uma consola local do Azure PowerShell ou o script.

Também pode gerir entidades do Service Bus utilizando modelos Azure Resource Manager. Para obter mais informações, consulte o artigo [recursos de criar o Service Bus com modelos Azure Resource Manager](service-bus-resource-manager-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, terá os seguintes pré-requisitos:

* Uma subscrição do Azure. Para obter mais informações sobre como obter uma subscrição, veja [opções de compra][purchase options], [ofertas para membros][member offers], ou [gratuita conta][free account].
* Um computador com o Azure PowerShell. Para obter instruções, consulte [introdução aos cmdlets do Azure PowerShell](/powershell/azure/get-started-azureps).
* Uma compreensão geral de scripts do PowerShell, pacotes NuGet e o .NET Framework.

## <a name="get-started"></a>Introdução

A primeira etapa é utilizar o PowerShell para iniciar sessão na sua conta do Azure e subscrição do Azure. Siga as instruções em [introdução aos cmdlets do Azure PowerShell](/powershell/azure/get-started-azureps) para iniciar sessão na sua conta do Azure e obter e aceder aos recursos na sua subscrição do Azure.

## <a name="provision-a-service-bus-namespace"></a>Aprovisionar um espaço de nomes do Service Bus

Ao trabalhar com espaços de nomes do Service Bus, pode utilizar o [Get-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace), [New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace), [Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace), e [Set-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespace) cmdlets.

Este exemplo cria algumas variáveis locais no script; `$Namespace` e `$Location`.

* `$Namespace` é o nome do espaço de nomes do Service Bus com a qual queremos trabalhar.
* `$Location` identifica o Datacenter no qual vamos provisionar o espaço de nomes.
* `$CurrentNamespace` armazena o espaço de nomes de referência que podemos recuperar (ou criar).

Num script real, `$Namespace` e `$Location` podem ser passados como parâmetros.

Esta parte do script faz o seguinte:

1. Tenta recuperar um espaço de nomes do Service Bus com o nome especificado.
2. Se não for encontrado o espaço de nomes, comunica o que foi encontrado.
3. Se o espaço de nomes não for encontrado, ele cria o espaço de nomes e, em seguida, obtém o espaço de nomes criado recentemente.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Criar uma regra de autorização de espaço de nomes

O exemplo seguinte mostra como gerir regras de autorização de espaço de nomes com o [New-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespaceauthorizationrule), [Get-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespaceauthorizationrule), [Set-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespaceauthorizationrule), e [cmdlets de Remove-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespaceauthorizationrule).

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzureRmServiceBusKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzureRmServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
}
```

## <a name="create-a-queue"></a>Criar uma fila

Para criar uma fila ou tópico, efetue uma verificação de espaço de nomes utilizando o script na secção anterior. Em seguida, crie a fila:

```powershell
# Check if queue already exists
$CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Modificar as propriedades da fila

Depois de executar o script na secção anterior, pode utilizar o [Set-AzureRmServiceBusQueue](/powershell/module/azurerm.servicebus/set-azurermservicebusqueue) cmdlet para atualizar as propriedades de uma fila, como no exemplo seguinte:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Aprovisionamento de outras entidades do Service Bus

Pode utilizar o [módulo do PowerShell do Service Bus](/powershell/module/azurerm.servicebus) para aprovisionar outras entidades, como tópicos e subscrições. Estes cmdlets são sintaticamente semelhantes para os cmdlets de criação de fila demonstrados na secção anterior.

## <a name="next-steps"></a>Passos Seguintes

- Consulte a documentação do módulo PowerShell do Gestor de recursos de barramento de serviço completa [aqui](/powershell/module/azurerm.servicebus). Esta página apresenta uma lista de todos os cmdlets disponíveis.
- Para obter informações sobre como utilizar os modelos Azure Resource Manager, consulte o artigo [recursos de criar o Service Bus com modelos Azure Resource Manager](service-bus-resource-manager-overview.md).
- Informações sobre [bibliotecas de gestão do Service Bus .NET](service-bus-management-libraries.md).

Existem algumas maneiras alternativas para gerir entidades do Service Bus, conforme descrito em dessas postagens de blog:

* [Como criar filas, tópicos e subscrições através de um script do PowerShell do Service Bus](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Como criar um espaço de nomes do Service Bus e um Hub de eventos utilizando um script do PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Scripts do PowerShell do Service Bus](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: https://azure.microsoft.com/pricing/purchase-options/
[member offers]: https://azure.microsoft.com/pricing/member-offers/
[free account]: https://azure.microsoft.com/pricing/free-trial/
