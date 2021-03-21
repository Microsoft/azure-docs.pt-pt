---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: b2c1ebe390b1a2dec7be678b5d6f3a991056a23b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102517577"
---
1. Antes de começar, certifique-se de que:

    1. Configuraste e [Ativaste o teu dispositivo Azure Stack Edge Pro](../articles/databox-online/azure-stack-edge-gpu-deploy-activate.md) com um recurso Azure Stack Edge em Azure.
    1. [Configurou o cálculo deste dispositivo no portal Azure.](../articles/databox-online/azure-stack-edge-deploy-configure-compute.md#configure-compute)
    
1. [Ligue-se à interface PowerShell](../articles/databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Utilize o seguinte comando para ativar MPS no seu dispositivo.

    ```powershell
    Start-HcsGpuMPS
    ```