---
title: Gerir capturas de pacotes - Azure PowerShell
titleSuffix: Azure Network Watcher
description: Esta página explica como gerir a funcionalidade de captura de pacotes do Network Watcher usando powerShell
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 06263f85f7d6ad6cc80724baab01124833498739
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129650"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>Gerir capturas de pacotes com o Azure Network Watcher usando powerShell

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI do Azure](network-watcher-packet-capture-manage-cli.md)
> - [API de DESCANSO Azul](network-watcher-packet-capture-manage-rest.md)

A captura de pacotes do Network Watcher permite-lhe criar sessões de captura para rastrear o tráfego de e para uma máquina virtual. Os filtros são fornecidos para a sessão de captura para garantir que captura apenas o tráfego que deseja. A captura de pacotes ajuda a diagnosticar anomalias de rede tanto de forma reativa como proactiva. Outros usos incluem a recolha de estatísticas de rede, a obtenção de informação sobre intrusões de rede, para depurar comunicações de servidores de clientes e muito mais. Ao ser capaz de acionar remotamente as capturas de pacotes, esta capacidade alivia o fardo de executar uma captura manual de pacotes e sobre a máquina desejada, o que poupa tempo valioso.

Este artigo leva-o através das diferentes tarefas de gestão que estão atualmente disponíveis para captura de pacotes.

- [**Iniciar uma captura de pacote**](#start-a-packet-capture)
- [**Parar uma captura de pacote**](#stop-a-packet-capture)
- [**Eliminar uma captura de pacote**](#delete-a-packet-capture)
- [**Descarregue uma captura de pacotes**](#download-a-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que tem os seguintes recursos:

* Uma instância de Network Watcher na região que você quer criar uma captura de pacotes

* Uma máquina virtual com a extensão de captura do pacote ativada.

> [!IMPORTANT]
> A captura do pacote requer uma extensão virtual da máquina `AzureNetworkWatcherExtension`. Para instalar a extensão de um Windows VM visite a extensão virtual do Agente observador de [rede Azure para windows](../virtual-machines/windows/extensions-nwa.md) e para o Linux VM visite a extensão virtual do Agente observador de rede [Azure para o Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="install-vm-extension"></a>Instalar a extensão de VM

### <a name="step-1"></a>Passo 1

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>Passo 2

O exemplo seguinte recupera as informações de extensão necessárias para executar o `Set-AzVMExtension` cmdlet. Este cmdlet instala o agente de captura de pacotes na máquina virtual do hóspede.

> [!NOTE]
> O `Set-AzVMExtension` cmdlet pode demorar alguns minutos a ser concluído.

Para máquinas virtuais Windows:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.585.2
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Para máquinas virtuais Linux:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

O exemplo seguinte é uma resposta bem sucedida após executar o `Set-AzVMExtension` cmdlet.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>Passo 3

Para garantir a instalação do agente, passe a `Get-AzVMExtension` cmdlet e passe-lhe o nome da máquina virtual e o nome de extensão.

```powershell
Get-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

A amostra que se segue é um exemplo da resposta da execução `Get-AzVMExtension`

```
ResourceGroupName       : testrg
VMName                  : testvm1
Name                    : AzureNetworkWatcherExtension
Location                : westcentralus
Etag                    : null
Publisher               : Microsoft.Azure.NetworkWatcher
ExtensionType           : NetworkWatcherAgentWindows
TypeHandlerVersion      : 1.4
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1/
                          extensions/AzureNetworkWatcherExtension
PublicSettings          : 
ProtectedSettings       : 
ProvisioningState       : Succeeded
Statuses                : 
SubStatuses             : 
AutoUpgradeMinorVersion : True
ForceUpdateTag          : 
```

## <a name="start-a-packet-capture"></a>Iniciar uma captura de pacote

Uma vez concluídos os passos anteriores, o agente de captura do pacote é instalado na máquina virtual.

### <a name="step-1"></a>Passo 1

O próximo passo é recuperar a instância do Observador da Rede. Esta variável é passada para o `New-AzNetworkWatcherPacketCapture` cmdlet no passo 4.

```powershell
$networkWatcher = Get-AzResource -ResourceType "Microsoft.Network/networkWatchers" | Where {$_.Location -eq "WestCentralUS" }
```

### <a name="step-2"></a>Passo 2

Recuperar uma conta de armazenamento. Esta conta de armazenamento é usada para armazenar o ficheiro de captura do pacote.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>Passo 3

Os filtros podem ser utilizados para limitar os dados armazenados pela captura do pacote. O exemplo seguinte configura dois filtros.  Um filtro recolhe tráfego de TCP de saída apenas a partir do IP local 10.0.0.3 para os portos de destino 20, 80 e 443.  O segundo filtro recolhe apenas o tráfego uDP.

```powershell
$filter1 = New-AzPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Vários filtros podem ser definidos para uma captura de pacote.

### <a name="step-4"></a>Passo 4

Executar o `New-AzNetworkWatcherPacketCapture` cmdlet para iniciar o processo de captura do pacote, passando os valores necessários recuperados nos passos anteriores.
```powershell

New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filter $filter1, $filter2
```

O exemplo seguinte é a saída esperada de executar o `New-AzNetworkWatcherPacketCapture` cmdlet.

```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"3bf27278-8251-4651-9546-c7f369855e4e"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]


```

## <a name="get-a-packet-capture"></a>Obtenha uma captura de pacote

Executar o `Get-AzNetworkWatcherPacketCapture` cmdlet, recupera o estado de uma captura de pacote atualmente em execução ou concluída.

```powershell
Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

O exemplo que se segue é a saída do `Get-AzNetworkWatcherPacketCapture` cmdlet. O exemplo seguinte é que após a captura estar completa. O valor PacketCaptureStatus é interrompido, com uma StopReason of TimeExceeded. Este valor mostra que a captura do pacote foi bem sucedida e correu o seu tempo.
```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"4b9a81ed-dc63-472e-869e-96d7166ccb9b"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]
CaptureStartTime        : 2/1/2017 10:43:01 PM
PacketCaptureStatus     : Stopped
StopReason              : TimeExceeded
PacketCaptureError      : []
```

## <a name="stop-a-packet-capture"></a>Parar uma captura de pacote

Ao executar o `Stop-AzNetworkWatcherPacketCapture` cmdlet, se uma sessão de captura estiver em andamento, é interrompida.

```powershell
Stop-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> O cmdlet não responde quando correu numa sessão de captura em execução ou numa sessão existente que já parou.

## <a name="delete-a-packet-capture"></a>Eliminar uma captura de pacote

```powershell
Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> A eliminação de uma captura de pacote não elimina o ficheiro na conta de armazenamento.

## <a name="download-a-packet-capture"></a>Descarregue uma captura de pacotes

Uma vez concluída a sua sessão de captura de pacotes, o ficheiro de captura pode ser enviado para armazenamento de bolhas ou para um ficheiro local no VM. A localização de armazenamento da captura do pacote é definida na criação da sessão. Uma ferramenta conveniente para aceder a estes ficheiros de captura guardados numa conta de armazenamento é o Microsoft Azure Storage Explorer, que pode ser descarregado aqui: https://storageexplorer.com/

Se for especificada uma conta de armazenamento, os ficheiros de captura de pacotes são guardados numa conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Passos seguintes

Saiba automatizar capturas de pacotes com alertas de máquina virtual ao ver [Criar um alerta desencadeado pela captura de pacotes](network-watcher-alert-triggered-packet-capture.md)

Descubra se determinado tráfego é permitido dentro ou fora do seu VM visitando [check IP flow verificar](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->














