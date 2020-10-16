---
title: Criar um recipiente de serviço em nuvem com PowerShell Microsoft Docs
description: Este artigo explica como criar um recipiente de serviço em nuvem com PowerShell. O contentor acolhe funções web e trabalhadora.
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
ms.openlocfilehash: d40a5b64cc8018f45bf08158ce808b2baae27962
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87049087"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Use um comando Azure PowerShell para criar um recipiente de serviço de nuvem vazia

Este artigo explica como criar rapidamente um recipiente cloud Services usando cmdlets Azure PowerShell. Siga os passos abaixo:

1. Instale o cmdlet Microsoft Azure PowerShell a partir da página [de descarregamentos Azure PowerShell.](https://aka.ms/webpi-azps)
2. Abra o pedido de comando PowerShell.
3. Utilize o [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount?view=azuresmps-4.0.0) para iniciar sação.

   > [!NOTE]
   > Para obter mais instruções sobre a instalação do cmdlet Azure PowerShell e a ligação à sua subscrição Azure, consulte como [instalar e configurar a Azure PowerShell](/powershell/azure/).
   >
   >
4. Utilize o **cmdlet New-AzureService** para criar um recipiente de serviço de nuvem Azure vazio.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Siga este exemplo para invocar o cmdlet:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Para obter mais informações sobre a criação do serviço de nuvem Azure, corra:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Passos seguintes

* Para gerir a implementação do serviço de nuvem, consulte os comandos [Get-AzureService](/powershell/module/servicemanagement/azure.service/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/servicemanagement/azure.service/Remove-AzureService?view=azuresmps-4.0.0)e [Set-AzureService.](/powershell/module/servicemanagement/azure.service/set-azureservice?view=azuresmps-4.0.0) Também pode consultar como [configurar serviços na nuvem](cloud-services-how-to-configure-portal.md) para mais informações.
* Para publicar o seu projeto de serviço na nuvem ao Azure, consulte a amostra de código  **PublishCloudService.ps1** do [repositório](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery)de serviços de nuvem arquivado .