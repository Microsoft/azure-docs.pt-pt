---
title: Utilize a Azure PowerShell para permitir diagnósticos num VM do Windows
services: virtual-machines-windows
documentationcenter: ''
description: Saiba como utilizar o PowerShell para permitir o Azure Diagnostics numa máquina virtual que executa o Windows
author: mimckitt
manager: gwallace
editor: ''
ms.assetid: 2e6d88f2-1980-4a24-827e-a81616a0d247
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/15/2015
ms.author: mimckitt
ms.openlocfilehash: 3b46ae07d4bbbcd02b9880a394b72c790553f95c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87837028"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Utilize o PowerShell para permitir o Azure Diagnostics numa máquina virtual que executa o Windows

A azure Diagnostics é a capacidade dentro do Azure que permite a recolha de dados de diagnóstico numa aplicação implementada. Pode utilizar a extensão de diagnóstico para recolher dados de diagnóstico, como registos de aplicações ou contadores de desempenho de uma máquina virtual Azure (VM) que está a executar o Windows. 

 

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Ativar a extensão de diagnóstico se utilizar o modelo de implementação do Gestor de Recursos
Pode ativar a extensão de diagnóstico enquanto cria um VM do Windows através do modelo de implementação do Gestor de Recursos Azure, adicionando a configuração de extensão ao modelo de Gestor de Recursos. Consulte [Criar uma máquina virtual do Windows com monitorização e diagnóstico utilizando o modelo Azure Resource Manager](diagnostics-template.md).

Para ativar a extensão de diagnóstico de um VM existente que foi criado através do modelo de implementação do Gestor de Recursos, pode utilizar o cmdlet PowerShell de [Configuração Set-AzVMDiagnosticsExtension,](/powershell/module/az.compute/set-azvmdiagnosticsextension) como mostrado abaixo.

```azurepowershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path
```


*$diagnosticsconfig_path* é o caminho para o ficheiro que contém a configuração de diagnóstico em XML, como descrito na [amostra](#sample-diagnostics-configuration) abaixo.  

Se o ficheiro de configuração de diagnóstico especificar um elemento **de contagem de armazenamento** com um nome de conta de armazenamento, então o script *Set-AzVMDiagnosticsExtension* definirá automaticamente a extensão de diagnóstico para enviar dados de diagnóstico para essa conta de armazenamento. Para que isto funcione, a conta de armazenamento tem de estar na mesma subscrição que o VM.

Se não foi especificado o **Armazenamento Na** configuração do diagnóstico, então tem de passar no parâmetro *StorageAccountName* para o cmdlet. Se o parâmetro *StorageAccountName* for especificado, então o cmdlet utilizará sempre a conta de armazenamento que está especificada no parâmetro e não a que está especificada no ficheiro de configuração de diagnóstico.

Se a conta de armazenamento de diagnósticos estiver numa subscrição diferente da VM, então você precisa passar explicitamente nos parâmetros *StorageAccountName* e *StorageAccountKey* para o cmdlet. O parâmetro *StorageAccountKey* não é necessário quando a conta de armazenamento de diagnósticos está na mesma subscrição, uma vez que o cmdlet pode automaticamente consultar e definir o valor chave ao permitir a extensão de diagnóstico. No entanto, se a conta de armazenamento de diagnósticos estiver numa subscrição diferente, então o cmdlet pode não conseguir obter a chave automaticamente e precisa especificar explicitamente a chave através do parâmetro *StorageAccountKey.*  

```azurepowershell
Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

Uma vez ativada a extensão de diagnóstico num VM, pode obter as definições atuais utilizando o cmdlet [Get-AzVmDiagnosticsExtension.](/powershell/module/az.compute/get-azvmdiagnosticsextension)

```azurepowershell
Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name
```

O cmdlet devolve *PublicSettings,* que contém a configuração de diagnóstico. Existem dois tipos de configuração suportados, WadCfg e xmlCfg. WadCfg é a configuração JSON, e xmlCfg é configuração XML em um formato codificado base64. Para ler o XML, precisa descodificá-lo.

```azurepowershell
$publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
$encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
$xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
Write-Host $xmlconfig
```

O [cmdlet remove-AzVmDiagnosticsExtension](/powershell/module/az.compute/remove-azvmdiagnosticsextension) pode ser usado para remover a extensão de diagnóstico do VM.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Ativar a extensão de diagnóstico se utilizar o modelo de implementação clássico

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Pode utilizar o [cmdlet Set-AzureVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) para ativar uma extensão de diagnóstico num VM que cria através do modelo de implementação clássico. O exemplo a seguir mostra como criar um novo VM através do modelo de implementação clássico com a extensão de diagnóstico ativada.

```azurepowershell
$VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
$VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
$VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
New-AzVM -Location $Location -ServiceName $Service_Name -VM $VM
```

Para ativar a extensão de diagnóstico de um VM existente que foi criado através do modelo de implementação clássico, utilize primeiro o cmdlet [Get-AzureVM](/powershell/module/servicemanagement/azure.service/get-azurevm) para obter a configuração VM. Em seguida, atualize a configuração VM para incluir a extensão de diagnóstico utilizando o [cmdlet Set-AzureVMDiagnosticsExtension.](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) Por fim, aplique a configuração atualizada no VM utilizando [update-AzureVM](/powershell/module/servicemanagement/azure.service/update-azurevm).

```azurepowershell
$VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
$VM_Update = Set-AzureVMDiagnosticsExtension  -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM
```

## <a name="sample-diagnostics-configuration"></a>Configuração de diagnóstico de amostra
O XML a seguir pode ser utilizado para a configuração pública de diagnóstico com os scripts acima. Esta configuração da amostra transferirá vários contadores de desempenho para a conta de armazenamento de diagnósticos, juntamente com erros da aplicação, segurança e canais de sistema nos registos de eventos do Windows e quaisquer erros dos registos de infraestrutura de diagnóstico.

A configuração tem de ser atualizada para incluir o seguinte:

* O *atributoid* de recursos do elemento **Métricas** precisa de ser atualizado com o ID de recurso para o VM.
  
  * O ID de recursos pode ser construído utilizando o seguinte padrão: "/subscrições/{*ID de subscrição para a subscrição com o VM*}/resourceGroups/{*O nome do grupo de recursos para o VM*}/fornecedores/Microsoft.Compute/virtualMachines/{ O Nome*VM*}".
  * Por exemplo, se o ID de subscrição para a subscrição em execução for **111111111-1111-1111-1111-111111111111111111,** o nome do grupo de recursos para o grupo de recursos é **MyResourceGroup,** e o Nome VM é **MyWindowsVM,** então o valor para *o recursoID* seria:
    
      ```xml
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Para obter mais informações sobre como as métricas são geradas com base nos contadores de desempenho e na configuração das métricas, consulte [a tabela de métricas Azure Diagnostics no armazenamento](diagnostics-template.md#wadmetrics-tables-in-storage).
* O elemento **Armazenamentotcount** precisa de ser atualizado com o nome da conta de armazenamento de diagnósticos.
  
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

## <a name="next-steps"></a>Passos seguintes
* Para obter orientações adicionais sobre a utilização da capacidade de Diagnóstico Azure e outras técnicas para resolver [problemas, consulte Ativar diagnósticos em Serviços de Nuvem Azure e Máquinas Virtuais](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* [O esquema de configurações de diagnóstico](../../azure-monitor/platform/diagnostics-extension-versions.md) explica as várias opções de configurações XML para a extensão de diagnóstico.