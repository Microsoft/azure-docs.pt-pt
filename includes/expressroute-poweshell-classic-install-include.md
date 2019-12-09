---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926224"
---
Instale as versões mais recentes de módulos do PowerShell de gestão de serviço do Azure (SM) e o módulo do ExpressRoute. Você não pode usar o ambiente CloudShell do Azure para executar módulos SM.

1. Use as instruções no artigo [instalando o módulo de gerenciamento de serviços](/powershell/azure/servicemanagement/install-azure-ps) para instalar o módulo de gerenciamento de serviços do Azure. Se você tiver o módulo AZ ou RM já instalado, certifique-se de usar '-AllowClobber '.
2. Importe os módulos instalados. Ao usar o exemplo a seguir, ajuste o caminho para refletir o local e a versão dos seus módulos do PowerShell instalados.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Para entrar em sua conta do Azure, abra o console do PowerShell com direitos elevados e conecte-se à sua conta. Use o exemplo a seguir para ajudá-lo a se conectar usando o módulo de gerenciamento de serviços:

   ```powershell
   Add-AzureAccount
   ```