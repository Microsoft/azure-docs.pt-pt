---
title: Atualizar a extensão do Observador de Rede para a versão mais recente
description: Saiba como atualizar a extensão do Observador de Rede Azure para a versão mais recente.
services: virtual-machines
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: 42efe2927b4d711f7fa66a96ebd25f1a62bf654a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563613"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>Atualizar a extensão do Observador de Rede para a versão mais recente

## <a name="overview"></a>Descrição Geral

[O Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) é um serviço de monitorização, diagnóstico e análise de desempenho de rede que monitoriza as redes Azure. A extensão da máquina virtual do Network Watcher Agent (VM) é um requisito para capturar o tráfego de rede a pedido e utilizar outra funcionalidade avançada em VMs Azure. A extensão do Observador de Rede é utilizada por funcionalidades como Monitor de Ligação, Monitor de Ligação (pré-visualização), resolução de problemas de ligação e captura de pacotes.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem a extensão do Observador de Rede instalada no seu VM.

## <a name="latest-version"></a>Versão mais recente

A versão mais recente da extensão do Observador de Rede encontra-se atualmente `1.4.1693.1` .

## <a name="update-your-extension-using-a-powershell-script"></a>Atualize a sua extensão utilizando um script PowerShell
Clientes com grandes implementações que precisam atualizar vários VMs de uma só vez. Para atualizar os VMs selecionados manualmente, consulte a secção seguinte 

```powershell
<#
    .SYNOPSIS
    This script will scan all VMs in the provided subscription and upgrade any out of date AzureNetworkWatcherExtensions

    .DESCRIPTION
    This script should be no-op if AzureNetworkWatcherExtensions are up to date
    Requires Azure PowerShell 4.2 or higher to be installed (e.g. Install-Module AzureRM).

    .EXAMPLE
    .\UpdateVMAgentsInSub.ps1 -SubID F4BC4873-5DAB-491E-B713-1358EF4992F2 -NoUpdate

#>
[CmdletBinding()]
param(
    [Parameter(Mandatory=$true)]
    [string] $SubID,
    [Parameter(Mandatory=$false)]
    [Switch] $NoUpdate = $false,
    [Parameter(Mandatory=$false)]
    [string] $MinVersion = "1.4.1654.1"
)


function NeedsUpdate($version)
{
    if ($version -eq $MinVersion)
    {
        return $false
    }

    $lessThan = $true;
    $versionParts = $version -split '\.';
    $minVersionParts = $MinVersion -split '\.';
    for ($i = 0; $i -lt $versionParts.Length; $i++)
    {
        if ([int]$versionParts[$i] -gt [int]$minVersionParts[$i])
        {
            $lessThan = $false;
            break;
        }
    }

    return $lessThan
}

Write-Host "Scanning all VMs in the subscription: $($SubID)"
Select-AzSubscription -SubscriptionId $SubID;
$vms = Get-AzVM;
$foundVMs = $false;
Write-Host "Starting VM search, this may take a while"

foreach ($vmName in $vms)
{
    # Get Detailed VM info
    $vm = Get-AzVM -ResourceGroupName $vmName.ResourceGroupName -Name $vmName.name -Status;
    $isWindows = $vm.OsVersion -match "Windows";
    foreach ($extension in $vm.Extensions)
    {
        if ($extension.Name -eq "AzureNetworkWatcherExtension")
        {
            if (NeedsUpdate($extension.TypeHandlerVersion))
            {
                $foundVMs = $true;
                if (-not ($NoUpdate))
                {
                    Write-Host "Found VM that needs to be updated: subscriptions/$($SubID)/resourceGroups/$($vm.ResourceGroupName)/providers/Microsoft.Compute/virtualMachines/$($vm.Name) -> Updating " -NoNewline
                    Remove-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "AzureNetworkWatcherExtension" -Force
                    Write-Host "... " -NoNewline
                    $type = if ($isWindows) { "NetworkWatcherAgentWindows" } else { "NetworkWatcherAgentLinux" };
                    Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -Location $vmName.Location -VMName $vm.Name -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type $type -typeHandlerVersion "1.4"
                    Write-Host "Done"
                }
                else
                {
                    Write-Host "Found $(if ($isWindows) {"Windows"} else {"Linux"}) VM that needs to be updated: subscriptions/$($SubID)/resourceGroups/$($vm.ResourceGroupName)/providers/Microsoft.Compute/virtualMachines/$($vm.Name)"
                }
            }
        }
    }
}

if ($foundVMs)
{
    Write-Host "Finished $(if ($NoUpdate) {"searching"} else {"updating"}) out of date AzureNetworkWatcherExtension on VMs"
}
else
{
    Write-Host "All AzureNetworkWatcherExtensions up to date"
}

```

## <a name="update-your-extension-manually"></a>Atualize a sua extensão manualmente

Para atualizar a sua extensão, precisa de saber a sua versão de extensão.

### <a name="check-your-extension-version"></a>Verifique a sua versão de extensão

Pode verificar a sua versão de extensão utilizando o portal Azure, o Azure CLI ou o PowerShell.

#### <a name="usetheazureportal"></a>Use o portal Azure

1. Vá ao painel **de extensões** do seu VM no portal Azure.
1. Selecione a extensão **AzureNetworkWatcher** para ver o painel de detalhes.
1. Localize o número da versão no campo **Versão.**  

#### <a name="use-the-azure-cli"></a>Utilizar a CLI do Azure

Executar o seguinte comando a partir de um pedido do Azure CLI:

```azurecli
az vm get-instance-view --resource-group  "SampleRG" --name "Sample-VM"
```
Localizar **"AzureNetworkWatcherExtension"** na saída e identificar o número da versão do campo *"TypeHandlerVersion"* na saída.  Nota: As informações sobre a extensão aparecem várias vezes na saída JSON. Por favor, procure no bloco de "extensões" e deverá ver o número completo da versão da extensão. 

Você deve ver algo como o seguinte: ![ Azure CLI Screenshot](./media/network-watcher/azure-cli-screenshot.png)

#### <a name="usepowershell"></a>Use PowerShell

Executar os seguintes comandos a partir de um pedido PowerShell:

```powershell
Get-AzVM -ResourceGroupName "SampleRG" -Name "Sample-VM" -Status
```
Localize a extensão do Observador de Rede Azure na saída e identifique o número de versão do campo *"TypeHandlerVersion"* na saída.   

Você deve ver algo como o seguinte: ![ PowerShell Screenshot](./media/network-watcher/powershell-screenshot.png)

### <a name="update-your-extension"></a>Atualize a sua extensão

Se a sua versão estiver abaixo da versão mais recente acima mencionada, atualize a sua extensão utilizando qualquer uma das seguintes opções.

#### <a name="option-1-use-powershell"></a>Opção 1: Utilizar PowerShell

Executar os seguintes comandos:

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"

#Windows command
Set-AzVMExtension -ResourceGroupName "myResourceGroup1" -Location "WestUS" -VMName "myVM1" -Name "NetworkWatcherAgentWindows" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -ForceRerun "True"

```

Se não funcionar. Retire e instale novamente a extensão, utilizando os passos abaixo. Isto irá automaticamente adicionar a versão mais recente.

Remoção da extensão 

```powershell
#Same command for Linux and Windows
Remove-AzVMExtension -ResourceGroupName "SampleRG" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension"
``` 

Instalação da extensão novamente

```powershell
#Linux command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux" -typeHandlerVersion "1.4"

#Windows command
Set-AzVMExtension -ResourceGroupName "SampleRG" -Location "centralus" -VMName "Sample-VM" -Name "AzureNetworkWatcherExtension" -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows" -typeHandlerVersion "1.4"
```

#### <a name="option-2-use-the-azure-cli"></a>Opção 2: Utilizar o Azure CLI

Forçar uma atualização.

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Se isso não funcionar, remova e instale novamente a extensão e siga estes passos para adicionar automaticamente a versão mais recente.

Retire a extensão.

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Volte a instalar a extensão.

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher"

```

#### <a name="option-3-reboot-your-vms"></a>Opção 3: Reiniciar os seus VMs

Se tiver uma atualização automática definida para a extensão do Observador de Rede, reinicie a sua instalação VM para a extensão mais recente.

## <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, consulte a documentação de extensão do Observador de Rede para [Linux](./network-watcher-linux.md) ou [Windows](./network-watcher-windows.md). Também pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, apresente um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione Obter **suporte**. Para obter informações sobre a utilização do Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
