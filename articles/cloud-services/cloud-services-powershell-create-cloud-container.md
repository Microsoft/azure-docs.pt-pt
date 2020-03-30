---
title: Crie um recipiente de serviço em nuvem com powerShell / Microsoft Docs
description: Este artigo explica como criar um recipiente de serviço em nuvem com a PowerShell. O contentor acolhe funções web e trabalhadores.
services: cloud-services
documentationcenter: .net
author: cawaMS
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: ef9d3c7c479df9c71a855f0a243b5b9d0da947d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68359498"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Utilize um comando Azure PowerShell para criar um recipiente de serviço em nuvem vazio

Este artigo explica como criar rapidamente um recipiente de Serviços cloud usando cmdlets Azure PowerShell. Siga os passos abaixo:

1. Instale o cmdlet Microsoft Azure PowerShell na página de [downloads do Azure PowerShell.](https://aka.ms/webpi-azps)
2. Abra o pedido de comando PowerShell.
3. Utilize o [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-4.0.0) para iniciar sessão.

   > [!NOTE]
   > Para mais instruções sobre a instalação do cmdlet Azure PowerShell e a ligação à sua subscrição Azure, consulte como instalar e configurar o [Azure PowerShell](/powershell/azure/overview).
   >
   >
4. Utilize o cmdlet **New-AzureService** para criar um recipiente de serviço de nuvem Azure vazio.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Siga este exemplo para invocar o cmdlet:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Para mais informações sobre a criação do serviço de nuvem Azure, corra:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Passos seguintes

* Para gerir a implementação do serviço na nuvem, consulte os comandos [Get-AzureService,](/powershell/module/servicemanagement/azure/Get-AzureService?view=azuresmps-4.0.0) [Remove-AzureService](/powershell/module/servicemanagement/azure/Remove-AzureService?view=azuresmps-4.0.0)e [Set-AzureService.](/powershell/module/servicemanagement/azure/set-azureservice?view=azuresmps-4.0.0) Também pode consultar [como configurar serviços na nuvem](cloud-services-how-to-configure-portal.md) para mais informações.
* Para publicar o seu projeto de serviço na nuvem para o Azure, consulte a amostra de código **PublishCloudService.ps1** do [repositório](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery)de serviços em nuvem arquivado .