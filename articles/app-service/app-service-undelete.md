---
title: Restaurar aplicativos excluídos
description: Saiba como restaurar um aplicativo excluído no serviço Azure App. Evite a dor de cabeça de um aplicativo excluído acidentalmente.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: c7d778a0afca4b3552976526d58a2cb2efe12161
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689609"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Restaurar o aplicativo do serviço de aplicativo excluído usando o PowerShell

Se você tiver excluído acidentalmente seu aplicativo no serviço Azure App, poderá restaurá-lo usando os comandos do [módulo AZ PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

> [!NOTE]
> Os aplicativos excluídos são limpos do sistema 30 dias após a exclusão inicial. Depois que um aplicativo tiver sido limpo, ele não poderá ser recuperado.
>

## <a name="re-register-app-service-resource-provider"></a>Registrar novamente o provedor de recursos do serviço de aplicativo
Alguns clientes podem chegar a um problema em que a recuperação da lista de aplicativos excluídos falha. Para resolver o problema, execute o seguinte comando:

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
