---
title: Gerir capturas de pacotes - Azure PowerShell
titleSuffix: Azure Network Watcher
description: Esta página explica como gerir a funcionalidade de captura de pacotes do Network Watcher usando o PowerShell
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 8779381425d4180cc7320f251de9c2cfdd1f0510
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99223428"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>Gerir capturas de pacotes com O Observador de Rede Azure usando o PowerShell

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI do Azure](network-watcher-packet-capture-manage-cli.md)
> - [API REST do Azure](network-watcher-packet-capture-manage-rest.md)

A captura de pacotes do Network Watcher permite-lhe criar sessões de captura para rastrear o tráfego de e para uma máquina virtual. Os filtros são fornecidos para a sessão de captura para garantir que captura apenas o tráfego que deseja. A captura de pacotes ajuda a diagnosticar anomalias de rede reativas e proativamente. Outros usos incluem recolher estatísticas de rede, obter informações sobre intrusões de rede, depurar comunicações de servidores de clientes e muito mais. Ao ser capaz de ativar remotamente capturas de pacotes, esta capacidade alivia o fardo de executar uma captura de pacote manualmente e na máquina desejada, o que poupa tempo valioso.

Este artigo leva-o através das diferentes tarefas de gestão que estão atualmente disponíveis para captura de pacotes.

- [**Inicie uma captura de pacotes**](#start-a-packet-capture)
- [**Pare uma captura de pacotes**](#stop-a-packet-capture)
- [**Excluir uma captura de pacotes**](#delete-a-packet-capture)
- [**Faça o download de uma captura de pacotes**](#download-a-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem os seguintes recursos:

* Um exemplo de Network Watcher na região que pretende criar uma captura de pacotes

* Uma máquina virtual com a extensão de captura do pacote ativada.

> [!IMPORTANT]
> A captura de pacotes requer uma extensão virtual da `AzureNetworkWatcherExtension` máquina. Para instalar a extensão num Windows VM visite [a extensão da máquina virtual do Observador de Redes Azure para windows](../virtual-machines/extensions/network-watcher-windows.md) e para Linux VM visite a [extensão virtual do Agente de Observadores de Rede Azure para o Linux](../virtual-machines/extensions/network-watcher-linux.md).

## <a name="install-vm-extension"></a>Instalar a extensão de VM

### <a name="step-1"></a>Passo 1

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>Passo 2

O exemplo a seguir recupera as informações de extensão necessárias para executar o `Set-AzVMExtension` cmdlet. Este cmdlet instala o agente de captura de pacotes na máquina virtual do hóspede.

> [!NOTE]
> O `Set-AzVMExtension` cmdlet pode demorar vários minutos a ser concluído.

Para máquinas virtuais windows:

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

O exemplo a seguir é uma resposta bem sucedida após a execução do `Set-AzVMExtension` cmdlet.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>Passo 3

Para garantir a instalação do agente, passe o `Get-AzVMExtension` cmdlet e passe-lhe o nome da máquina virtual e o nome da extensão.

```powershell
Get-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

A amostra a seguir é um exemplo da resposta de correr `Get-AzVMExtension`

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

## <a name="start-a-packet-capture"></a>Inicie uma captura de pacotes

Uma vez concluídos os passos anteriores, o agente de captura de pacotes é instalado na máquina virtual.

### <a name="step-1"></a>Passo 1

O próximo passo é recuperar a instância do Observador de Rede. Esta variável é passada para o `New-AzNetworkWatcherPacketCapture` cmdlet no passo 4.

```powershell
$networkWatcher = Get-AzNetworkWatcher  | Where {$_.Location -eq "westcentralus" }
```

### <a name="step-2"></a>Passo 2

Recupere uma conta de armazenamento. Esta conta de armazenamento é usada para armazenar o ficheiro de captura de pacotes.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>Passo 3

Os filtros podem ser utilizados para limitar os dados que são armazenados pela captura do pacote. O exemplo a seguir estabelece dois filtros.  Um filtro recolhe o tráfego TCP de saída apenas a partir do IP local 10.0.0.3 para os portos de destino 20, 80 e 443.  O segundo filtro recolhe apenas o tráfego UDP.

```powershell
$filter1 = New-AzPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Vários filtros podem ser definidos para uma captura de pacotes.

### <a name="step-4"></a>Passo 4

Executar o `New-AzNetworkWatcherPacketCapture` cmdlet para iniciar o processo de captura do pacote, passando os valores necessários recuperados nos degraus anteriores.
```powershell

New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filter $filter1, $filter2
```

Segue-se o exemplo esperado da saída prevista para a execução do `New-AzNetworkWatcherPacketCapture` cmdlet.

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

## <a name="get-a-packet-capture"></a>Obtenha uma captura de pacotes

Executando o `Get-AzNetworkWatcherPacketCapture` cmdlet, recupera o estado de uma captura de pacote atualmente em execução ou concluída.

```powershell
Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

Segue-se a saída do `Get-AzNetworkWatcherPacketCapture` cmdlet. O exemplo a seguir é depois de a captura estar completa. O valor PacketCaptureStatus é interrompido, com um StopReason of TimeExceededed. Este valor mostra que a captura do pacote foi bem sucedida e correu o seu tempo.
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

## <a name="stop-a-packet-capture"></a>Pare uma captura de pacotes

Ao executar o `Stop-AzNetworkWatcherPacketCapture` cmdlet, se uma sessão de captura estiver em andamento, é interrompido.

```powershell
Stop-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> O cmdlet não retorna a nenhuma resposta quando funcionado numa sessão de captura em execução ou numa sessão já existente que já parou.

## <a name="delete-a-packet-capture"></a>Excluir uma captura de pacotes

```powershell
Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> A eliminação de uma captura de pacote não apaga o ficheiro na conta de armazenamento.

## <a name="download-a-packet-capture"></a>Faça o download de uma captura de pacotes

Uma vez concluída a sessão de captura do pacote, o ficheiro de captura pode ser carregado para o armazenamento de bolhas ou para um ficheiro local no VM. A localização de armazenamento da captura do pacote é definida na criação da sessão. Uma ferramenta conveniente para aceder a estes ficheiros de captura guardados numa conta de armazenamento é o Microsoft Azure Storage Explorer, que pode ser descarregado aqui:  https://storageexplorer.com/

Se uma conta de armazenamento for especificada, os ficheiros de captura de pacotes são guardados numa conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Passos seguintes

Saiba como automatizar capturas de pacotes com alertas de máquina virtual ao visualizar [Criar uma captura de pacotes desencadeados por alerta](network-watcher-alert-triggered-packet-capture.md)

Descubra se determinado tráfego é permitido dentro ou fora do seu VM visitando [verificar o fluxo IP](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->
