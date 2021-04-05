---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: f002fec531a0355e803a1545990fc0b13b535742
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582751"
---
Se tiver problemas com o dispositivo, pode criar um pacote de suporte a partir dos registos do sistema. O Microsoft Support utiliza este pacote para resolver problemas. Siga estes passos para criar um pacote de suporte:

1. [Ligue-se à interface PowerShell do seu dispositivo.](#connect-to-the-powershell-interface)
2. Utilize o `Get-HcsNodeSupportPackage` comando para criar um pacote de suporte. A utilização do cmdlet é a seguinte:

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    O cmdlet recolhe registos do seu dispositivo e copia esses registos para uma rede especificada ou partilha local.

    Os parâmetros utilizados são os seguintes:

    - `-Path` - Especifique a rede ou o caminho local para copiar o pacote de suporte para. (obrigatório)
    - `-Credential` - Especificar as credenciais para aceder ao caminho protegido.
    - `-Zip` - Especificar para gerar um ficheiro zip.
    - `-Include` - Especificar para incluir os componentes a incluir no pacote de suporte. Se não for especificado, `Default` é assumido.
    - `-IncludeArchived` - Especificar para incluir registos arquivados no pacote de suporte.
    - `-IncludePeriodicStats` - Especificar para incluir registos periódicos de estatísticas no pacote de suporte.

    
