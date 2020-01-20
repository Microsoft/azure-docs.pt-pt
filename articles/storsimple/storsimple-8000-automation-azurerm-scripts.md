---
title: Usar scripts de Azure Resource Manager para gerenciar dispositivos StorSimple
description: Saiba como usar os scripts de Azure Resource Manager para automatizar trabalhos do StorSimple
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: d1c98aa8c9b635f08bb14db2bde5485640a5d24d
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276650"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Usar Azure Resource Manager scripts baseados em SDK para gerenciar dispositivos StorSimple

Este artigo descreve como Azure Resource Manager scripts baseados em SDK podem ser usados para gerenciar seu dispositivo StorSimple da série 8000. Um script de exemplo também está incluído para orientá-lo pelas etapas de configuração do seu ambiente para executar esses scripts.

Este artigo se aplica a dispositivos StorSimple da série 8000 em execução somente no portal do Azure.

## <a name="sample-scripts"></a>Scripts de exemplo

Os scripts de exemplo a seguir estão disponíveis para automatizar vários trabalhos do StorSimple.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabela de scripts de exemplo baseados em SDK Azure Resource Manager

| Azure Resource Manager script                    | Descrição                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Authorize-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Esse script permite autorizar seu dispositivo StorSimple a alterar a chave de criptografia de dados de serviço.                                                                                                           |
| [Create-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Esse script cria um dispositivo de nuvem StorSimple 8010 ou 8020. O dispositivo de nuvem pode ser configurado e registrado com seu serviço de Gerenciador de Dados do StorSimple.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Esse script cria ou modifica volumes do StorSimple.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Esse script lista todos os backups de um dispositivo registrado com o serviço de Device Manager do StorSimple.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Este script tem todas as políticas de backup para seu dispositivo StorSimple.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Esse script Obtém todos os trabalhos do StorSimple em execução no serviço StorSimple Device Manager.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Esse script examina o servidor de atualização e permite que você saiba se há atualizações disponíveis para instalação em seu dispositivo StorSimple.                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Esse script instala as atualizações disponíveis em seu dispositivo StorSimple.                                                                                                                                           |
| [Manage-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Esse script inicia um instantâneo de nuvem manual e exclui instantâneos de nuvem mais antigos que os dias de retenção especificados.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Este script do PowerShell do runbook de automação do Azure relata o status de todos os trabalhos de backup.                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Esse script exclui um único objeto de backup.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Esse script inicia um backup manual em seu dispositivo StorSimple.                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Esse script atualiza a chave de criptografia de dados de serviço para todos os 8010/8020 dispositivos de nuvem StorSimple registrados com o serviço StorSimple Device Manager.                                     |
| [Verify-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Esse script realça os backups ausentes depois de analisar todos os agendamentos associados às políticas de backup. Ele também verifica o catálogo de backup com a lista de backups disponíveis.             |




## <a name="configure-and-run-a-sample-script"></a>Configurar e executar um script de exemplo

Esta seção usa um script de exemplo e detalha as várias etapas necessárias para executar o script.

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem:

*   Azure PowerShell instalado. Para instalar os módulos do Azure PowerShell:
    * Em um ambiente do Windows, siga as etapas em [instalar e configurar Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Você pode instalar Azure PowerShell no host do Windows Server para seu StorSimple, se estiver usando um.
    * Em um ambiente Linux ou MacOS, siga as etapas em [instalar e configurar o Azure PowerShell no MacOS ou Linux](https://docs.microsoft.com/powershell/azure/azurerm/install-azurermps-maclinux).

Para obter mais informações sobre como usar Azure PowerShell, acesse [introdução ao uso de Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="run-azure-powershell-script"></a>Executar Azure PowerShell script

O script usado neste exemplo lista todos os trabalhos em um dispositivo StorSimple. Isso inclui os trabalhos que tiveram êxito, falharam ou estão em andamento. Execute as etapas a seguir para baixar e executar o script.

1. Inicie o Azure PowerShell. Crie uma nova pasta e altere o diretório para a nova pasta.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Baixe a CLI do NuGet](https://www.nuget.org/downloads) na pasta criada na etapa anterior. Há várias versões do _NuGet. exe_. Escolha a versão correspondente ao seu SDK. Cada link de download aponta diretamente para um arquivo _. exe_ . Certifique-se de clicar com o botão direito do mouse e salvar o arquivo em seu computador em vez de executá-lo no navegador.

    Você também pode executar o comando a seguir para baixar e armazenar o script na mesma pasta que você criou anteriormente.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Baixe o SDK dependente.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Baixe o script do projeto GitHub de exemplo.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Execute o script. Quando for solicitado a autenticar, forneça suas credenciais do Azure. Esse script deve gerar uma lista filtrada de todos os trabalhos em seu dispositivo StorSimple.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Resultado da amostra

A saída a seguir é apresentada quando o script de exemplo é executado. A saída contém todos os trabalhos que foram executados em um dispositivo registrado iniciado em 25 de setembro de 2017 e concluídos em 2 de outubro de 2017.

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

[Use o serviço de Device Manager do storsimple para gerenciar seu dispositivo storsimple](storsimple-8000-manager-service-administration.md).
