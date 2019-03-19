---
title: Utilizar scripts do Azure Resource Manager para gerir os dispositivos do StorSimple | Documentos da Microsoft
description: Saiba como utilizar scripts do Azure Resource Manager para automatizar tarefas de StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: b7cc47c449b91693831dda74bf987f595fe65681
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997873"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Utilizar scripts baseados no SDK do Azure Resource Manager para gerir dispositivos do StorSimple

Este artigo descreve como SDK do Azure Resource Manager com base em scripts podem ser utilizados para gerir o seu dispositivo da série StorSimple 8000. Um script de exemplo também está incluído para orientá-lo pelos passos de configuração de seu ambiente para executar esses scripts.

Este artigo aplica-se aos dispositivos da série 8000 do StorSimple em execução no portal do Azure apenas.

## <a name="sample-scripts"></a>Scripts de exemplo

Os scripts de exemplo seguintes estão disponíveis para automatizar várias tarefas do StorSimple.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabela de scripts de exemplo com base no SDK do Azure Resource Manager

| Script do Azure Resource Manager                    | Descrição                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Authorize-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Este script permite-lhe autorizar o dispositivo StorSimple para alterar a chave de encriptação de dados do serviço.                                                                                                           |
| [Create-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Este script cria um 8010 ou uma 8020 StorSimple Cloud Appliance. A aplicação da cloud, em seguida, pode ser configurada e registrada com o seu serviço do StorSimple Data Manager.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Este script cria ou modifica os volumes do StorSimple.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Este script apresenta uma lista de todas as cópias de segurança para um dispositivo registado com o seu serviço StorSimple Device Manager.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Este script todas as políticas de cópia de segurança para o dispositivo StorSimple.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Este script obtém todas as tarefas de StorSimple em execução no seu serviço StorSimple Device Manager.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Este script verifica o servidor de atualização e permite-lhe saber se as atualizações estiverem disponíveis para instalação no seu dispositivo StorSimple.                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Este script instala as atualizações disponíveis no dispositivo StorSimple.                                                                                                                                           |
| [Manage-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Este script inicia um instantâneo de manual cloud e elimina os instantâneos de cloud com mais de dias de retenção especificado.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Este script do PowerShell de Runbook de automatização do Azure comunica o estado de todas as tarefas de cópia de segurança.                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Este script elimina um único objeto de cópia de segurança.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Este script inicia uma cópia de segurança manual no dispositivo StorSimple.                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Este script a chave de encriptação de dados do serviço de atualizações para todas as StorSimple Cloud Appliances do 8010/8020 registadas com o seu serviço StorSimple Device Manager.                                     |
| [Verify-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Este script destaca as cópias de segurança em falta após analisar todos os agendamentos associados a políticas de cópia de segurança. Ele também verifica o catálogo de cópia de segurança com a lista de cópias de segurança disponíveis.             |




## <a name="configure-and-run-a-sample-script"></a>Configurar e executar um script de exemplo

Esta secção assume um script de exemplo e fornece detalhes sobre as diversas etapas necessárias para executar o script.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:

*   O Azure PowerShell instalado. Para instalar os módulos do Azure PowerShell:
    * Num ambiente Windows, siga os passos em [instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-4.4.0). Pode instalar o Azure PowerShell no seu anfitrião do Windows Server para o seu StorSimple se utilizar uma.
    * Num ambiente Linux ou MacOS, siga os passos em [instalar e configurar o Azure PowerShell no MacOS ou Linux](https://docs.microsoft.com/powershell/azure/azurerm/install-azurermps-maclinux?view=azurermps-4.4.0).

Para obter mais informações sobre como utilizar o Azure PowerShell, aceda a [introdução à utilização do Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-4.4.0).

### <a name="run-azure-powershell-script"></a>Executar script do PowerShell do Azure

O script utilizado neste exemplo apresenta uma lista de todos os trabalhos de um dispositivo StorSimple. Isto inclui as tarefas que foi concluída com êxito, falharam ou que estão em curso. Execute os seguintes passos para transferir e executar o script.

1. Inicie o Azure PowerShell. Crie uma nova pasta e altere o diretório para a nova pasta.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Transferir o CLI do NuGet](https://www.nuget.org/downloads) sob a pasta que criou no passo anterior. Existem várias versões do _nuget.exe_. Escolha a versão correspondente ao seu SDK. Cada ligação de transferência aponta diretamente para um _.exe_ ficheiro. Certifique-se de que com o botão direito e guarde o ficheiro para o seu computador, em vez de executá-lo a partir do browser.

    Também pode executar o seguinte comando para transferir e armazenar o script na mesma pasta que criou anteriormente.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Baixe o SDK dependente.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Transferir o script do projeto do GitHub de exemplo.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Execute o script. Quando solicitado para autenticar, forneça as credenciais do Azure. Este script deve produzir uma lista filtrada de todas as tarefas no dispositivo StorSimple.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Resultado da amostra

O resultado seguinte é apresentado quando o script de exemplo é executado. O resultado contém todas as tarefas que tenha sido executada num dispositivo registado que iniciou no dia 25 de Setembro de 2017 e concluído por 2 de Outubro de 2017.

```
-----------------------------------------
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1
PS C:\Scripts\StorSimpleSDKTools> .\Get-DeviceJobs.ps1 -SubscriptionId 1234ab5c-678d-910e-9fc4-0accc9c0166e -TenantId 12a345bc-67d8-91ef-01ab-2c7cd123ef45 -DeviceName 8600-ABC1234567D89EF -ResourceGroupName Contoso -ManagerName ContosoDeviceMgr -FilterByStartTime "09/25/2017 08:10:02" -FilterByEndTime "10/02/2017 08:10:02"


Status            : Succeeded
StartTime         : 10/2/2017 10:30:02 PM
EndTime           : 10/2/2017 10:31:36 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : ss-asr-policy1
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC/jobs/75905c48-b153-4af1-8b21-4b9a2ff9
                    825b
Name              : 75905c48-b153-4af1-8b21-4b9a2ff9825b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000
-------------------------------------------
CUT              CUT  
-------------------------------------------
Status            : Succeeded
StartTime         : 9/26/2017 5:00:02 PM
EndTime           : 9/26/2017 5:01:20 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : 8010 policy
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF/jobs/3cfd8108-db60-4e9a-a8da-6d8fe457
                    8d2b
Name              : 3cfd8108-db60-4e9a-a8da-6d8fe4578d2b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000



PS C:\Scripts\StorSimpleSDKTools>
--------------------------------------------

```


## <a name="next-steps"></a>Passos Seguintes

[Serviço de utilização StorSimple Device Manager para gerir o dispositivo StorSimple](storsimple-8000-manager-service-administration.md).