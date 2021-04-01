---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018179"
---
Instale as versões mais recentes dos módulos PowerShell (Gestão de Serviços Azure) e do módulo ExpressRoute. Não é possível utilizar o ambiente Azure CloudShell para executar módulos SM.

1. Utilize as instruções no artigo [de instalação do módulo de Gestão de Serviços](/powershell/azure/servicemanagement/install-azure-ps) para instalar o Módulo de Gestão de Serviços Azure. Se tiver o módulo Az ou RM já instalado, certifique-se de que utiliza '-AllowClobber'.
2. Importe os módulos instalados. Ao utilizar o exemplo seguinte, ajuste o caminho para refletir a localização e a versão dos seus módulos PowerShell instalados.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Para iniciar seduca na sua conta Azure, abra a sua consola PowerShell com direitos elevados e ligue-se à sua conta. Utilize o seguinte exemplo para o ajudar a ligar utilizando o módulo de Gestão de Serviços:

   ```powershell
   Add-AzureAccount
   ```