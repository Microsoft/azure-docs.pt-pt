---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129160"
---
Para repor o seu dispositivo, tem de limpar de forma segura todos os dados do disco de dados e do disco de arranque do seu dispositivo. 

Utilize o cmdlet `Reset-HcsAppliance` para limpar tanto os discos de dados como o disco de arranque ou apenas os discos de dados. Os comutadores `ClearData` e `BootDisk` permitem-lhe limpar os discos de dados e o disco de arranque, respetivamente.

O comutador `BootDisk` limpa o disco de arranque e torna o dispositivo inutilizável. Só deve utilizá-lo quando tiver de devolver o dispositivo à Microsoft. Para obter mais informações, veja [Devolver o dispositivo à Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Se utilizar a reposição do dispositivo na IU da Web local, apenas os discos de dados são limpos de forma segura, mas o disco de arranque mantém-se intacto. O disco de arranque contém a configuração do dispositivo.

1. [Ligar à interface do PowerShell](#connect-to-the-powershell-interface).
2. Na linha de comandos, escreva:

    `Reset-HcsAppliance -ClearData -BootDisk`

    O seguinte exemplo mostra como utilizar este cmdlet:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
