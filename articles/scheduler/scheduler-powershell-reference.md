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
ms.openlocfilehash: 5c80e86699d671994a0989b99c0f97ebe2680592
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045002"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Referência de cmdlets do PowerShell para o Azure Scheduler

> [!IMPORTANT]
> O [Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Microsoft Azure Scheduler, que está a ser descontinuado. Para agendar tarefas, [experimente antes o Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para criar scripts para criar e gerir tarefas do Scheduler e coleções de tarefas, pode utilizar os cmdlets do PowerShell. Este artigo lista as principais [cmdlets do PowerShell para o Azure Scheduler](/powershell/module/azurerm.scheduler) com links para os artigos de referência. Para instalar o Azure PowerShell para a sua subscrição do Azure, veja [como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Para obter mais informações sobre [cmdlets do Azure Resource Manager](/powershell/azure/overview), consulte [utilizar o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md).

| Cmdlet | Descrição |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/az.scheduler/disable-azschedulerjobcollection) |Desativa uma coleção de tarefas. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/az.scheduler/enable-azschedulerjobcollection) |Permite que uma coleção de tarefas. |
| [Get-AzSchedulerJob](/powershell/module/az.scheduler/get-azschedulerjob) |Obtém as tarefas do Scheduler. |
| [Get-AzSchedulerJobCollection](/powershell/module/az.scheduler/get-azschedulerjobcollection) |Obtém coleções de tarefas. |
| [Get-AzSchedulerJobHistory](/powershell/module/az.scheduler/get-azschedulerjobhistory) |Obtém o histórico de tarefas. |
| [New-AzSchedulerHttpJob](/powershell/module/az.scheduler/new-azschedulerhttpjob) |Cria uma tarefa HTTP. |
| [New-AzSchedulerJobCollection](/powershell/module/az.scheduler/new-azschedulerjobcollection) |Cria uma coleção de tarefas. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/az.scheduler/new-azschedulerservicebusqueuejob) | Cria uma tarefa de fila do Service Bus. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/az.scheduler/new-azschedulerservicebustopicjob) |Cria uma tarefa de tópico do Service Bus. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/az.scheduler/new-azschedulerstoragequeuejob) |Cria uma tarefa da fila de armazenamento. |
| [Remove-AzSchedulerJob](/powershell/module/az.scheduler/remove-azschedulerjob) |Remove uma tarefa do agendador. |
| [Remove-AzSchedulerJobCollection](/powershell/module/az.scheduler/remove-azschedulerjobcollection) |Remove uma coleção de tarefas. |
| [Set-AzSchedulerHttpJob](/powershell/module/az.scheduler/set-azschedulerhttpjob) |Modifica uma tarefa do Scheduler HTTP. |
| [Set-AzSchedulerJobCollection](/powershell/module/az.scheduler/set-azschedulerjobcollection) |Modifica uma coleção de tarefas. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/az.scheduler/set-azschedulerservicebusqueuejob) |Modifica uma tarefa de fila do Service Bus. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/az.scheduler/set-azschedulerservicebustopicjob) |Modifica uma tarefa de tópico do Service Bus. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/az.scheduler/set-azschedulerstoragequeuejob) |Modifica uma tarefa da fila de armazenamento. |
||| 

Para obter mais detalhes, pode executar qualquer um destes cmdlets: 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Consulte também

* [O que é o Agendador do Azure?](scheduler-intro.md)
* [Conceitos, terminologia e hierarquia de entidades](scheduler-concepts-terms.md)
* [Criar e agendar a sua primeira tarefa - portal do Azure](scheduler-get-started-portal.md)
* [Referência da API REST do Agendador do Azure](https://msdn.microsoft.com/library/mt629143)
