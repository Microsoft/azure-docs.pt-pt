---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: aebb82690a7a49aba071ed64349d37d516208cca
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "67125624"
---
Para repor o dispositivo, terá de forma segura apagar todos os dados sobre o disco de dados e o disco de arranque do seu dispositivo. 

Utilize o `Reset-HcsAppliance` cmdlet para apagar os discos de dados e o disco de arranque ou apenas os discos de dados. O `ClearData` e `BootDisk` comutadores permitem-lhe apagar os discos de dados e o disco de arranque, respetivamente.

Se utilizar o dispositivo reposto na IU da web local, apenas os discos de dados forem eliminados em segurança, mas o disco de arranque é mantido intacto. O disco de arranque contém a configuração do dispositivo.

1. [Ligar a interface do PowerShell](#connect-to-the-powershell-interface).
2. Na linha de comandos, escreva:

    `Reset-HcsAppliance -ClearData -BootDisk`

    O exemplo seguinte mostra como utilizar este cmdlet:
    ```
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk
    
    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
