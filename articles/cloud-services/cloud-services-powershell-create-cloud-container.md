---
title: Crie um recipiente de serviço em nuvem (clássico) com | PowerShell Microsoft Docs
description: Este artigo explica como criar um recipiente de serviço em nuvem com PowerShell. O contentor acolhe funções web e trabalhadora.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 39fe439e37b1af4e833396ef83205729af8c7ad3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102610421"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-classic-container"></a>Use um comando Azure PowerShell para criar um recipiente de serviço de nuvem vazio (clássico)

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Este artigo explica como criar rapidamente um recipiente cloud Services usando cmdlets Azure PowerShell. Siga os passos abaixo:

1. Instale o cmdlet Microsoft Azure PowerShell a partir da página [de descarregamentos Azure PowerShell.](https://aka.ms/webpi-azps)
2. Abra o pedido de comando PowerShell.
3. Utilize o [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount) para iniciar sação.

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

* Para gerir a implementação do serviço de nuvem, consulte os comandos [Get-AzureService](/powershell/module/servicemanagement/azure.service/Get-AzureService), [Remove-AzureService](/powershell/module/servicemanagement/azure.service/Remove-AzureService)e [Set-AzureService.](/powershell/module/servicemanagement/azure.service/set-azureservice) Também pode consultar como [configurar serviços na nuvem](cloud-services-how-to-configure-portal.md) para mais informações.
* Para publicar o seu projeto de serviço na nuvem ao Azure, consulte a amostra de código  **PublishCloudService.ps1** do [repositório](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery)de serviços de nuvem arquivado .