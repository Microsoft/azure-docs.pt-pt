---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: c1b56cfb85595b8a17dc18f69a0b162d504c04ec
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028436"
---
Para repor o seu dispositivo, tem de limpar de forma segura todos os dados do disco de dados e do disco de arranque do seu dispositivo. 

Utilize o `Reset-HcsAppliance` cmdlet para eliminar tanto os discos de dados como o disco de arranque ou apenas os discos de dados. Os `ClearData` `BootDisk` interruptores permitem limpar os discos de dados e o disco de arranque, respectivamente.

O `BootDisk` interruptor limpa o disco de arranque e torna o dispositivo inutilizável. Só deve utilizá-lo quando tiver de devolver o dispositivo à Microsoft. Para obter mais informações, consulte [Devolver o dispositivo à Microsoft.](../articles/databox-online/azure-stack-edge-return-device.md)

Se utilizar a reposição do dispositivo na IU da Web local, apenas os discos de dados são limpos de forma segura, mas o disco de arranque mantém-se intacto. O disco de arranque contém a configuração do dispositivo.

1. [Ligue-se à interface PowerShell](#connect-to-the-powershell-interface).
2. Na linha de comandos, escreva:

    `Reset-HcsAppliance -ClearData -BootDisk`

    O exemplo a seguir mostra como utilizar este cmdlet:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```