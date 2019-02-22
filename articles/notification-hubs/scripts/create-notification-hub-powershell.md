---
title: 'Script do PowerShell: Criar um hub de notificação do Azure | Documentos da Microsoft'
description: Este script do PowerShell cria um hub de notificação do Azure.
services: notification-hubs
author: dimazaid
manager: kpiteira
editor: spelluru
ms.service: notification-hubs
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 93c5118962fed1bfda4d32afba942a6600664e5d
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586466"
---
# <a name="use-powershell-to-create-an-azure-notification-hub"></a>Utilize o PowerShell para criar um hub de notificação do Azure

Este script do PowerShell de exemplo cria um hub de notificação do Azure de exemplo. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Pré-requisitos
* **Subscrição do Azure** - se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/notification-hubs/create-notification-hub/create-notification-hub.ps1 "Create a notification hub")]


## <a name="clean-up-deployment"></a>Limpar a implementação

Depois de executar o script de exemplo, pode utilizar o seguinte comando para remover o grupo de recursos e todos os recursos associados à mesma:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explicação do script

Este script utiliza os seguintes comandos: 

| Comando | Notas |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzNotificationHubsNamespace](/powershell/module/az.notificationhubs/new-aznotificationhubsnamespace) | Cria um espaço de nomes para o hub de notificação. |
| [New-AzNotificationHub](/powershell/module/az.notificationhubs/new-aznotificationhub) | Cria um hub de notificação. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Elimina um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre o Azure PowerShell, veja [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).
