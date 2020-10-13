---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "79129160"
---
Para repor o seu dispositivo, tem de limpar de forma segura todos os dados do disco de dados e do disco de arranque do seu dispositivo. 

Utilize o `Reset-HcsAppliance` cmdlet para eliminar tanto os discos de dados como o disco de arranque ou apenas os discos de dados. Os `ClearData` `BootDisk` interruptores permitem limpar os discos de dados e o disco de arranque, respectivamente.

O `BootDisk` interruptor limpa o disco de arranque e torna o dispositivo inutilizável. Só deve utilizá-lo quando tiver de devolver o dispositivo à Microsoft. Para obter mais informações, consulte [Devolver o dispositivo à Microsoft.](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device)

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
