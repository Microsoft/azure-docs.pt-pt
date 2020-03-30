---
title: Utilize scripts AzureRM PowerShell para gerir dispositivos StorSimple
description: Saiba como usar scripts do Gestor de Recursos Azure para automatizar trabalhos StorSimple
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: 03a5ef49b2d58d351d882b30b5d11e4a5ba90264
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471963"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Utilize scripts baseados em SDK do Gestor de Recursos Azure para gerir dispositivos StorSimple

Este artigo descreve como os scripts baseados em SDK do Gestor de Recursos Azure podem ser usados para gerir o seu dispositivo da série StorSimple 8000. Um script de amostra também está incluído para acompanhá-lo através dos passos de configurar o seu ambiente para executar estes scripts.

Este artigo aplica-se a dispositivos da série StorSimple 8000 que estão a penas no portal Azure.

## <a name="sample-scripts"></a>Scripts de exemplo

Os seguintes scripts de amostra estão disponíveis para automatizar vários trabalhos StorSimple.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabela de scripts de amostra sdk baseados em Recursos Azure

| Script de Gestor de Recursos Azure                    | Descrição                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Autorizar-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Este script permite autorizar o seu dispositivo StorSimple a alterar a chave de encriptação de dados de serviço.                                                                                                           |
| [Create-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Este script cria um 8010 ou um StorSimple Cloud Appliance 8020. O aparelho em nuvem pode então ser configurado e registado com o seu serviço StorSimple Data Manager.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Este script cria ou modifica volumes StorSimple.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Este script lista todas as cópias de segurança para um dispositivo registado no seu serviço StorSimple Device Manager.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Este script todas as políticas de backup para o seu dispositivo StorSimple.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Este script obtém todos os trabalhos StorSimple em execução no seu serviço StorSimple Device Manager.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Este script digitaliza o servidor Update e permite-lhe saber se as atualizações estão disponíveis para instalar no seu dispositivo StorSimple.                                                                                          |
| [Instalar-DispositivoUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Este script instala as atualizações disponíveis no seu dispositivo StorSimple.                                                                                                                                           |
| [Manage-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Este script inicia um instantâneo manual de nuvem e elimina instantâneos de nuvem mais antigos do que os dias de retenção especificados.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Este script de PowerShell de Automação Azure Automation reporta o estado de todos os trabalhos de backup.                                                                                                              |
| [Remover-DispositivoBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Este script elimina um único objeto de reserva.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Este script inicia uma cópia de segurança manual no seu dispositivo StorSimple.                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Este script atualiza a chave de encriptação de dados do serviço para todos os eletrodomésticos StorSimple Cloud 8010/8020 registados com o seu serviço StorSimple Device Manager.                                     |
| [Check-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Este script destaca os backups em falta após analisar todos os horários associados às políticas de backup. Também verifica o catálogo de cópias de segurança com a lista de cópias de segurança disponíveis.             |




## <a name="configure-and-run-a-sample-script"></a>Configure e executar um script de amostra

Esta secção tem um roteiro de exemplo e detalha os vários passos necessários para executar o script.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:

*   Azure PowerShell instalado. Para instalar módulos Azure PowerShell:
    * Num ambiente Windows, siga os passos em [Instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Pode instalar o Azure PowerShell no seu anfitrião do Windows Server para o seu StorSimple se utilizar um.
    * Num ambiente Linux ou MacOS, siga os passos em [Instalar e configurar o Azure PowerShell no MacOS ou no Linux](https://docs.microsoft.com/powershell/azure/azurerm/install-azurermps-maclinux).

Para mais informações sobre a utilização do Azure PowerShell, vá a [Get com a utilização do Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="run-azure-powershell-script"></a>Executar script PowerShell Azure

O script usado neste exemplo lista todos os trabalhos num dispositivo StorSimple. Isto inclui os empregos que sucederam, falharam ou estão em curso. Execute os seguintes passos para descarregar e executar o script.

1. Inicie o Azure PowerShell. Crie uma nova pasta e mude o diretório para a nova pasta.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Baixe nuGet CLI](https://www.nuget.org/downloads) sob a pasta criada no passo anterior. Existem várias versões de _nuget.exe._ Escolha a versão correspondente ao seu SDK. Cada link de descarregamento aponta diretamente para um ficheiro _.exe._ Certifique-se de clicar à direita e guardar o ficheiro para o seu computador em vez de executá-lo a partir do navegador.

    Também pode executar o seguinte comando para descarregar e armazenar o script na mesma pasta que criou anteriormente.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Descarregue o SDK dependente.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Descarregue o script do projeto GitHub da amostra.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Execute o script. Quando for solicitado para autenticar, forneça as suas credenciais Azure. Este script deve ser uma lista filtrada de todos os trabalhos no seu dispositivo StorSimple.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Resultado da amostra

A saída seguinte é apresentada quando o script da amostra é executado. A produção contém todos os postos de trabalho que funcionavam num dispositivo registado que começou em 25 de setembro de 2017 e foi concluído até 2 de outubro de 2017.

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


## <a name="next-steps"></a>Passos seguintes

Utilize o [serviço StorSimple Device Manager para gerir o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
