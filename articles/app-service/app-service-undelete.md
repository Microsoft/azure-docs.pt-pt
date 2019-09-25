---
title: Restaurar aplicativos do serviço de aplicativo excluídos-serviço de Azure App
description: Saiba como restaurar um aplicativo do serviço de aplicativo excluído usando o PowerShell.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: a766f7ed7a82874df8ef7506226de0d6f38a5847
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219548"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Restaurar o aplicativo do serviço de aplicativo excluído usando o PowerShell

Se você tiver excluído acidentalmente seu aplicativo no serviço Azure App, poderá restaurá-lo usando os comandos do [módulo AZ PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

## <a name="list-deleted-apps"></a>Listar aplicações eliminadas

Para obter a coleção de aplicativos excluídos, você pode usar `Get-AzDeletedWebApp`.

Para obter detalhes sobre um aplicativo específico excluído, você pode usar:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app>
```

As informações detalhadas incluem:

- **DeletedSiteId**: Identificador exclusivo para o aplicativo, usado para cenários em que vários aplicativos com o mesmo nome foram excluídos
- **SubscriptionId**: Assinatura que contém o recurso excluído
- **Local**: Local do aplicativo original
- **ResourceGroupName**: Nome do grupo de recursos original
- **Nome**: Nome do aplicativo original.
- **Slot**: o nome do slot.
- **Hora da exclusão**: Quando o aplicativo foi excluído  

## <a name="restore-deleted-app"></a>Restaurar aplicativo excluído

Depois que o aplicativo que você deseja restaurar tiver sido identificado, você poderá restaurá- `Restore-AzDeletedWebApp`lo usando o.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

As entradas para o comando são:

- **Grupo de recursos**: Grupo de recursos de destino onde o aplicativo será restaurado
- **Nome**: O nome do aplicativo deve ser globalmente exclusivo.
- **TargetAppServicePlanName**: Plano do serviço de aplicativo vinculado ao aplicativo

Por padrão `Restore-AzDeletedWebApp` , o também restaurará a configuração do aplicativo como um conteúdo. Se você quiser restaurar apenas o conteúdo, use o `-RestoreContentOnly` sinalizador com este commandlet.
