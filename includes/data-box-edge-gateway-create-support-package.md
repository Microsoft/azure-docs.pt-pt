---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184718"
---
Se tiver algum problema com o dispositivo, pode criar um pacote de suporte a partir dos registos do sistema. O Microsoft Support utiliza este pacote para resolver problemas. Siga estes passos para criar um pacote de suporte:

1. [Ligue-se à interface PowerShell do seu dispositivo](#connect-to-the-powershell-interface).
2. Utilize `Get-HcsNodeSupportPackage` o comando para criar um pacote de suporte. O uso do cmdlet é o seguinte:

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

    O cmdlet recolhe registos do seu dispositivo e copia esses registos para uma rede determinada ou uma parte local.

    Os parâmetros utilizados são os seguintes:

    - `-Path`- Especificar a rede ou o caminho local para copiar o pacote de suporte para. (obrigatório)
    - `-Credential`- Especificar as credenciais para aceder ao caminho protegido.
    - `-Zip`- Especifique para gerar um ficheiro zip.
    - `-Include`- Especificar incluir os componentes a incluir no pacote de apoio. Se não especificado, `Default` é assumido.
    - `-IncludeArchived`- Especificar para incluir registos arquivados no pacote de suporte.
    - `-IncludePeriodicStats`- Especificar para incluir registos periódicos de estatísticas no pacote de suporte.

    
