---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555310"
---
Se ocorrerem problemas de qualquer dispositivo, pode criar um pacote de suporte dos registos do sistema. Support da Microsoft utiliza este pacote para solucionar os problemas. Siga estes passos para criar um pacote de suporte:

1. [Ligar a interface do PowerShell do seu dispositivo](#connect-to-the-powershell-interface).
2. Utilize o `Get-HcsNodeSupportPackage` comando para criar um pacote de suporte. Segue-se a utilização do cmdlet:

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

    O cmdlet recolhe registos a partir do seu dispositivo e copia esses registos para uma rede especificada ou partilha local.

    Os parâmetros utilizados são os seguintes:

    - `-Path` -Especifique a rede ou o caminho local para copiar o pacote de suporte para. (necessário)
    - `-Credential` -Especifique as credenciais para aceder ao caminho protegido.
    - `-Zip` -Especifique para gerar um ficheiro zip.
    - `-Include` -Especifique a incluir os componentes a serem incluídos no pacote de suporte. Se não for especificado, `Default` pressupõe-se.
    - `-IncludeArchived` -Especifique para incluir registos arquivados no pacote de suporte.
    - `-IncludePeriodicStats` -Especifique para incluir registos stat periódicos no pacote de suporte.

    
