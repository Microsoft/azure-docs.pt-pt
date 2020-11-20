---
title: Utilize scripts AzureRM PowerShell para gerir dispositivos StorSimple
description: Saiba como utilizar scripts baseados em SDK para gerir o seu dispositivo da série StorSimple 8000.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: e3339f883d177366a64187e0c090752527802c8d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968779"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Utilize scripts baseados em SDK para gerir dispositivos StorSimple do Azure Resource Manager para gerir dispositivos StorSimple

Este artigo descreve como os scripts baseados em SDK do Azure Resource Manager podem ser usados para gerir o seu dispositivo da série StorSimple 8000. Um script de amostra também está incluído para te acompanhar através dos passos de configurar o teu ambiente para executar estes scripts.

Este artigo aplica-se apenas aos dispositivos da série StorSimple 8000 que estão a ser aplicados apenas no portal Azure.

## <a name="sample-scripts"></a>Scripts de exemplo

Os seguintes scripts de amostra estão disponíveis para automatizar vários trabalhos StorSimple.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Scripts de amostra baseados em SDK do Azure Resource Manager

| Script do gestor de recursos Azure                    | Descrição                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Authorize-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Este script permite-lhe autorizar o seu dispositivo StorSimple a alterar a chave de encriptação de dados de serviço.                                                                                                           |
| [Create-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Este script cria um 8010 ou um StorSimple Cloud Appliance 8020. O aparelho em nuvem pode então ser configurado e registado no seu serviço StorSimple Data Manager.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Este script cria ou modifica volumes StorSimple.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Este script lista todas as cópias de segurança de um dispositivo registado no seu serviço StorSimple Device Manager.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Este script todas as políticas de backup para o seu dispositivo StorSimple.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Este script obtém todos os trabalhos StorSimple em execução no seu serviço StorSimple Device Manager.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Este script digitaliza o servidor Update e informa-o se existem atualizações disponíveis para instalar no seu dispositivo StorSimple.                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Este script instala as atualizações disponíveis no seu dispositivo StorSimple.                                                                                                                                           |
| [Manage-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Este script inicia uma imagem em nuvem manual e elimina imagens de nuvem mais antigas do que os dias de retenção especificados.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Este script Azure Automation Runbook PowerShell informa o estado de todos os trabalhos de backup.                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Este script elimina um único objeto de reserva.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Este script inicia uma cópia de segurança manual no seu dispositivo StorSimple.                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Este script atualiza a chave de encriptação de dados de serviço para todos os Aparelhos em Nuvem StorSimple 8010/8020 registados no seu serviço StorSimple Device Manager.                                     |
| [Verify-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Este script destaca as cópias de segurança em falta depois de analisar todos os horários associados às políticas de backup. Também verifica o catálogo de backup com a lista de cópias de segurança disponíveis.             |




## <a name="configure-and-run-a-sample-script"></a>Configure e execute um roteiro de amostra

Esta secção toma um roteiro de exemplo e detalha os vários passos necessários para executar o script.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem:

*   Azure PowerShell instalado. Para instalar os módulos Azure PowerShell:
    * Num ambiente Windows, siga os passos na [Instalação e configuure Azure PowerShell](/powershell/azure/install-az-ps). Pode instalar o Azure PowerShell no seu anfitrião Windows Server para o seu StorSimple se utilizar um.
    * Num ambiente Linux ou MacOS, siga os passos na [Instalação e configuure Azure PowerShell no MacOS ou Linux](/powershell/azure/install-az-ps).

Para obter mais informações sobre a utilização do Azure PowerShell, vá a [Começar a utilizar a Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="run-azure-powershell-script"></a>Executar script Azure PowerShell

O script utilizado neste exemplo lista todos os trabalhos num dispositivo StorSimple. Isto inclui os postos de trabalho que foram bem sucedidos, fracassados ou estão em curso. Execute os seguintes passos para descarregar e executar o script.

1. Inicie o Azure PowerShell. Crie uma nova pasta e mude o diretório para a nova pasta.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Descarregue o NuGet CLI](https://www.nuget.org/downloads) sob a pasta criada no passo anterior. Existem várias versões de _nuget.exe._ Escolha a versão correspondente ao seu SDK. Cada link de descarregamento aponta diretamente para um ficheiro _.exe._ Certifique-se de clicar à direita e guardar o ficheiro para o seu computador em vez de executá-lo a partir do navegador.

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

5. Execute o script. Quando solicitado para autenticar, forneça as suas credenciais Azure. Este script deve fazer uma lista filtrada de todos os trabalhos no seu dispositivo StorSimple.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Saída de exemplo

A seguinte saída é apresentada quando o script da amostra é executado. A produção contém todos os trabalhos que funcionavam num dispositivo registado que começou em 25 de setembro de 2017 e terminou até 2 de outubro de 2017.

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

[Utilize o serviço StorSimple Device Manager para gerir o seu dispositivo StorSimple](storsimple-8000-manager-service-administration.md).