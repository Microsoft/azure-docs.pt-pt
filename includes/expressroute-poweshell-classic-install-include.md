---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74926224"
---
Instale as versões mais recentes dos módulos PowerShell de Gestão de Serviços Azure (SM) e do módulo ExpressRoute. Não é possível utilizar o ambiente Azure CloudShell para executar módulos SM.

1. Utilize as instruções no artigo do módulo de [gestão de serviços](/powershell/azure/servicemanagement/install-azure-ps) para instalar o Módulo de Gestão de Serviços Azure. Se tiver o módulo Az ou RM já instalado, certifique-se de que utiliza o 'AllowClobber'.
2. Importar os módulos instalados. Ao utilizar o seguinte exemplo, ajuste o caminho para refletir a localização e versão dos módulos PowerShell instalados.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Para iniciar sessão na sua conta Azure, abra a consola PowerShell com direitos elevados e ligue-se à sua conta. Utilize o seguinte exemplo para ajudá-lo a ligar utilizando o módulo de Gestão de Serviços:

   ```powershell
   Add-AzureAccount
   ```