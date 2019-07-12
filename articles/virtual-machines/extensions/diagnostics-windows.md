---
title: Utilizar o Azure PowerShell para ativar o diagnóstico numa VM do Windows | Documentos da Microsoft
services: virtual-machines-windows
documentationcenter: ''
description: Saiba como utilizar o PowerShell para ativar o diagnóstico do Azure numa máquina virtual a executar o Windows
author: sbtron
manager: gwallace
editor: ''
ms.assetid: 2e6d88f2-1980-4a24-827e-a81616a0d247
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: saurabh
ms.openlocfilehash: 19d30d207e67e1dfd8cdec2fe9951c763a921a0e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706077"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Utilizar o PowerShell para ativar o Diagnóstico do Azure numa máquina virtual com o Windows

Diagnóstico do Azure é o recurso no Azure que permite a recolha de dados de diagnóstico num aplicativo implantado. Pode utilizar a extensão de diagnóstico para recolher dados de diagnóstico, como contadores de desempenho ou registos de aplicações a partir de uma máquina virtual (VM) do Azure que está a executar o Windows. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Ativar a extensão de diagnóstico, se usar o modelo de implementação do Resource Manager
Pode ativar a extensão de diagnóstico enquanto cria uma VM do Windows por meio do modelo de implementação Azure Resource Manager ao adicionar a configuração da extensão para o modelo do Resource Manager. Ver [criar uma máquina virtual do Windows com monitorização e diagnóstico com o modelo Azure Resource Manager](diagnostics-template.md).

Para ativar a extensão de diagnóstico numa VM existente que foi criada por meio do modelo de implementação do Resource Manager, pode utilizar o [Set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiagnosticsextension) cmdlet do PowerShell, conforme mostrado abaixo.

    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* é o caminho para o ficheiro que contém a configuração de diagnósticos em XML, conforme descrito no [exemplo](#sample-diagnostics-configuration) abaixo.  

Se o ficheiro de configuração de diagnósticos Especifica um **StorageAccount** elemento com um nome de conta de armazenamento, o *AzVMDiagnosticsExtension do conjunto* script irá definir automaticamente o diagnóstico extensão para enviar dados de diagnóstico para essa conta de armazenamento. Para isso funcionar, a conta de armazenamento tem de estar na mesma subscrição que a VM.

Se nenhum **StorageAccount** foi especificado na configuração de diagnósticos, tem de passar o *StorageAccountName* parâmetro para o cmdlet. Se o *StorageAccountName* parâmetro for especificado, em seguida, o cmdlet irá sempre utilizar a conta de armazenamento especificada no parâmetro e não o que é especificado no ficheiro de configuração de diagnósticos.

Se a conta de armazenamento de diagnóstico está numa subscrição diferente da VM, em seguida, precisa transmitir explicitamente o *StorageAccountName* e *StorageAccountKey* parâmetros para o cmdlet. O *StorageAccountKey* parâmetro não é necessária quando a conta de armazenamento de diagnóstico está na mesma subscrição, como o cmdlet automaticamente. pode consultar e definir o valor da chave ao ativar a extensão de diagnóstico. No entanto, se a conta de armazenamento de diagnóstico está numa subscrição diferente, em seguida, o cmdlet poderá não conseguir obter a chave automaticamente e precisa explicitamente especificar a chave através da *StorageAccountKey* parâmetro.  

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Assim que a extensão de diagnóstico está ativada numa VM, pode obter as definições atuais com o [Get-AzVmDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiagnosticsextension) cmdlet.

    Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

O cmdlet devolve *PublicSettings*, que contém a configuração de diagnósticos. Existem dois tipos de configuração suportada, WadCfg e xmlCfg. WadCfg é a configuração do JSON e xmlCfg é a configuração de XML num formato com codificação Base64. Para ler o XML, é preciso decodificá-la.

    $publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

O [Remove-AzVmDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdiagnosticsextension) cmdlet pode ser utilizado para remover a extensão de diagnóstico a partir da VM.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Ativar a extensão de diagnóstico, se usar o modelo de implementação clássica
Pode utilizar o [Set-AzureVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) cmdlet para ativar uma extensão de diagnóstico numa VM que tenha criado por meio do modelo de implementação clássica. O exemplo seguinte mostra como criar uma nova VM através do modelo de implementação clássica com a extensão de diagnóstico ativada.

    $VM = New-AzVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzVM -Location $Location -ServiceName $Service_Name -VM $VM

Para ativar a extensão de diagnóstico numa VM existente que foi criada por meio do modelo de implementação clássica, utilize o [Get-AzureVM](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azurevm) cmdlet para obter a configuração da VM. Em seguida, atualize a configuração da VM para incluir a extensão de diagnóstico através da [AzureVMDiagnosticsExtension conjunto](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) cmdlet. Por fim, aplicar a configuração atualizada para a VM utilizando [Update-AzureVM](https://docs.microsoft.com/powershell/module/servicemanagement/azure/update-azurevm).

    $VM = Get-AzVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Exemplo de configuração de diagnósticos
O seguinte XML pode ser utilizado para a configuração pública de diagnóstico com os scripts acima. Este exemplo de configuração irá transferir vários contadores de desempenho para a conta de armazenamento de diagnósticos, juntamente com os erros da aplicação, segurança e canais de sistema nos registos de eventos do Windows e os erros dos logs de infraestrutura de diagnósticos.

A configuração tem de ser atualizado para incluir o seguinte:

* O *resourceID* atributo da **métricas** elemento tem de ser atualizado com o ID de recurso para a VM.
  
  * O ID de recurso pode ser construído utilizando o seguinte padrão: "/ subscrições / {*ID de subscrição para a subscrição com a VM*} /resourceGroups/ {*o nome de resourcegroup para a VM*} / providers/Microsoft.Compute/virtualMachines/ {*o nome da VM*} ".
  * Por exemplo, se o ID de subscrição para a subscrição em que a VM está em execução será **11111111-1111-1111-1111-111111111111**, o nome do grupo de recursos para o grupo de recursos é **MyResourceGroup**e o Nome da VM é **MyWindowsVM**, em seguida, o valor de *resourceID* seria:
    
      ```xml
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Para obter mais informações sobre como as métricas são geradas com base na configuração de contadores e métricas de desempenho, consulte [tabela de métricas de diagnóstico do Azure no armazenamento](diagnostics-template.md#wadmetrics-tables-in-storage).
* O **StorageAccount** elemento tem de ser atualizado com o nome da conta de armazenamento de diagnóstico.
  
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>Passos Seguintes
* Para obter orientações adicionais sobre como utilizar a capacidade de diagnóstico do Azure e outras técnicas para solucionar problemas, consulte [ativar diagnósticos nos serviços Cloud do Azure e máquinas virtuais](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* [Esquema de configurações de diagnóstico](https://msdn.microsoft.com/library/azure/mt634524.aspx) explica as várias opções de configurações de XML para a extensão de diagnóstico.

