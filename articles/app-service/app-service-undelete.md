---
title: Restaurar aplicativos do serviço de aplicativo excluídos-serviço de Azure App
description: Saiba como restaurar um aplicativo do serviço de aplicativo excluído usando o PowerShell.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: 6a3a62053a488f95e22cae13ef9d0714a7b5dd05
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173729"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Restaurar o aplicativo do serviço de aplicativo excluído usando o PowerShell

Se você tiver excluído acidentalmente seu aplicativo no serviço Azure App, poderá restaurá-lo usando os comandos do [módulo AZ PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

## <a name="re-register-app-service-resource-provider"></a>Registrar novamente o provedor de recursos do serviço de aplicativo
Alguns clientes podem encontrar um problema em que a recuperação da lista de aplicativos excluídos falha. Para resolver o problema, execute o seguinte comando:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Listar aplicações eliminadas

Para obter a coleção de aplicativos excluídos, você pode usar `Get-AzDeletedWebApp`.

Para obter detalhes sobre um aplicativo específico excluído, você pode usar:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

As informações detalhadas incluem:

- **DeletedSiteId**: identificador exclusivo para o aplicativo, usado para cenários em que vários aplicativos com o mesmo nome foram excluídos
- **SubscriptionId**: assinatura que contém o recurso excluído
- **Local**: local do aplicativo original
- **ResourceGroupName**: nome do grupo de recursos original
- **Nome**: o nome do aplicativo original.
- **Slot**: o nome do slot.
- **Hora da exclusão**: quando o aplicativo foi excluído  

## <a name="restore-deleted-app"></a>Restaurar aplicativo excluído

Depois que o aplicativo que você deseja restaurar tiver sido identificado, você poderá restaurá-lo usando `Restore-AzDeletedWebApp`.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

As entradas para o comando são:

- **Grupo de recursos**: grupo de recursos de destino em que o aplicativo será restaurado
- **Nome**: o nome do aplicativo deve ser globalmente exclusivo.
- **TargetAppServicePlanName**: plano do serviço de aplicativo vinculado ao aplicativo

Por padrão `Restore-AzDeletedWebApp` restaurará a configuração de seu aplicativo, bem como um conteúdo. Se você quiser restaurar apenas o conteúdo, use o sinalizador `-RestoreContentOnly` com esse commandlet.

> [!NOTE]
> Se o aplicativo foi hospedado em e, em seguida, excluído de um Ambiente do Serviço de Aplicativo, ele só poderá ser restaurado se o Ambiente do Serviço de Aplicativo correspondente ainda existir.
>

Você pode encontrar a referência completa do commandlet aqui: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
