---
title: Referência de cmdlets PowerShell
description: Saiba mais sobre os cmdlets powerShell para o Programador Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 577e2128cf7e4e9f914ec5504917053acb3c19d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898486"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>PowerShell cmdlets referência para O Scheduler Azure

> [!IMPORTANT]
> [A Azure Logic Apps](../logic-apps/logic-apps-overview.md) está a substituir o Programador Azure, que está [a ser reformado.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Para continuar a trabalhar com os trabalhos que criou no Scheduler, por [favor, emigre para as Aplicações Lógicas Azure](../scheduler/migrate-from-scheduler-to-logic-apps.md) o mais rapidamente possível. 
>
> O agendador já não está disponível no portal Azure, mas os cmdlets Rest [API](/rest/api/scheduler) e [Azure Scheduler PowerShell](scheduler-powershell-reference.md) permanecem disponíveis neste momento para que possa gerir os seus empregos e recolhas de emprego.

Para escrever scripts para criar e gerir empregos e recolhas de emprego, pode utilizar cmdlets PowerShell. Este artigo lista os principais cmdlets PowerShell para o Programador Azure com ligações aos seus artigos de referência. Para instalar o Azure PowerShell para a sua subscrição Azure, consulte como instalar e configurar o [Azure PowerShell](/powershell/azure/overview). Para obter mais informações sobre o [Azure Resource Manager cmdlets,](/powershell/azure/overview)consulte A Utilização de [PowerShell Azure com o Gestor](../powershell-azure-resource-manager.md)de Recursos Azure .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

| Cmdlet | Descrição |
|--------|-------------|
| [Desactivar-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Desativa uma coleção de emprego. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Permite uma recolha de emprego. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Arranja empregos de agendadores. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Arranja coleções de emprego. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Tem histórico de emprego. |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Cria um trabalho http. |
| [New-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Cria uma coleção de empregos. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Cria um trabalho de fila de ônibus de serviço. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Cria um trabalho tópico de ônibus de serviço. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Cria um trabalho de fila de armazenamento. |
| [Remover-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Remove um trabalho de Agendador. |
| [Remover-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Remove uma coleção de emprego. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Modifica um trabalho de Scheduler HTTP. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Modifica uma coleção de emprego. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Modifica um trabalho de fila de ônibus de serviço. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Modifica um trabalho de trabalho de ônibus de serviço. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Modifica um trabalho de fila de armazenamento. |
||| 

Para mais detalhes, pode executar qualquer um destes cmdlets: 

```text
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="next-steps"></a>Passos seguintes

* [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)
* [Limites, predefinições e códigos de erro do Agendador do Azure](scheduler-limits-defaults-errors.md)
* [Referência da API REST do Azure Scheduler](/rest/api/scheduler)