---
title: Referência de cmdlets do PowerShell - Azure Scheduler
description: Saiba mais sobre cmdlets do PowerShell do agendador do Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 53b68a5dc72277c9fd44b36d346e5b5c91b53a93
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344381"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Referência de cmdlets do PowerShell para o Azure Scheduler

> [!IMPORTANT]
> O [Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Microsoft Azure Scheduler, que está a ser descontinuado. Para agendar tarefas, [experimente antes o Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para criar scripts para criar e gerir tarefas do Scheduler e coleções de tarefas, pode utilizar os cmdlets do PowerShell. Este artigo lista os principais cmdlets do PowerShell do Azure Scheduler com links para os artigos de referência. Para instalar o Azure PowerShell para a sua subscrição do Azure, veja [como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Para obter mais informações sobre [cmdlets do Azure Resource Manager](/powershell/azure/overview), consulte [utilizar o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md).

| Cmdlet | Descrição |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Desativa uma coleção de tarefas. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Permite que uma coleção de tarefas. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Obtém as tarefas do Scheduler. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Obtém coleções de tarefas. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Obtém o histórico de tarefas. |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Cria uma tarefa HTTP. |
| [New-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Cria uma coleção de tarefas. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Cria uma tarefa de fila do Service Bus. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Cria uma tarefa de tópico do Service Bus. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Cria uma tarefa da fila de armazenamento. |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Remove uma tarefa do agendador. |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Remove uma coleção de tarefas. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Modifica uma tarefa do Scheduler HTTP. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Modifica uma coleção de tarefas. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Modifica uma tarefa de fila do Service Bus. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Modifica uma tarefa de tópico do Service Bus. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Modifica uma tarefa da fila de armazenamento. |
||| 

Para obter mais detalhes, pode executar qualquer um destes cmdlets: 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Consulte também

* [O que é o Microsoft Azure Scheduler?](scheduler-intro.md)
* [Conceitos, terminologia e hierarquia de entidades](scheduler-concepts-terms.md)
* [Criar e agendar a sua primeira tarefa - portal do Azure](scheduler-get-started-portal.md)
* [Referência da API REST do Azure Scheduler](https://msdn.microsoft.com/library/mt629143)
