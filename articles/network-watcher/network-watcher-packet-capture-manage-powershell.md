---
title: Gerenciar capturas de pacote com o observador de rede do Azure-PowerShell | Microsoft Docs
description: Esta página explica como gerenciar o recurso de captura de pacote do observador de rede usando o PowerShell
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 04d82085-c9ea-4ea1-b050-a3dd4960f3aa
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 4158c2c5ce69d1811b20c9937c1d064f4fe657ee
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163949"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>Gerenciar capturas de pacote com o observador de rede do Azure usando o PowerShell

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI do Azure](network-watcher-packet-capture-manage-cli.md)
> - [API REST do Azure](network-watcher-packet-capture-manage-rest.md)

A captura de pacotes do observador de rede permite que você crie sessões de captura para controlar o tráfego de e para uma máquina virtual. Os filtros são fornecidos para a sessão de captura para garantir que você capture somente o tráfego desejado. A captura de pacotes ajuda a diagnosticar anomalias de rede de forma reativa e proativa. Outros usos incluem a coleta de estatísticas de rede, a obtenção de informações sobre invasões de rede, a depuração de comunicações cliente-servidor e muito mais. Ao ser capaz de disparar remotamente capturas de pacotes, esse recurso facilita a carga de executar uma captura de pacotes manualmente e na máquina desejada, o que economiza tempo valioso.

Este artigo orienta você pelas diferentes tarefas de gerenciamento que estão disponíveis atualmente para a captura de pacotes.

- [**Iniciar uma captura de pacote**](#start-a-packet-capture)
- [**Parar uma captura de pacote**](#stop-a-packet-capture)
- [**Excluir uma captura de pacote**](#delete-a-packet-capture)
- [**Baixar uma captura de pacote**](#download-a-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha os seguintes recursos:

* Uma instância do observador de rede na região em que você deseja criar uma captura de pacote

* Uma máquina virtual com a extensão de captura de pacote habilitada.

> [!IMPORTANT]
> A captura de pacote requer uma extensão `AzureNetworkWatcherExtension`de máquina virtual. Para instalar a extensão em uma VM do Windows, visite [extensão da máquina virtual do agente do observador de rede do Azure para Windows](../virtual-machines/windows/extensions-nwa.md) e para VM do Linux visite a [extensão da máquina virtual do agente do observador de rede do Azure para Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="install-vm-extension"></a>Instalar a extensão de VM

### <a name="step-1"></a>Passo 1

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>Passo 2

O exemplo a seguir recupera as informações de extensão necessárias para `Set-AzVMExtension` executar o cmdlet. Esse cmdlet instala o agente de captura de pacote na máquina virtual convidada.

> [!NOTE]
> O `Set-AzVMExtension` cmdlet pode levar vários minutos para ser concluído.

Para máquinas virtuais do Windows:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.585.2
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Para máquinas virtuais do Linux:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

O exemplo a seguir é uma resposta bem-sucedida após a `Set-AzVMExtension` execução do cmdlet.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>Passo 3

Para garantir que o agente esteja instalado, execute o `Get-AzVMExtension` cmdlet e passe o nome da máquina virtual e o nome da extensão.

```powershell
Get-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

O exemplo a seguir é um exemplo da resposta da execução`Get-AzVMExtension`

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

Depois que as etapas anteriores forem concluídas, o agente de captura de pacote será instalado na máquina virtual.

### <a name="step-1"></a>Passo 1

A próxima etapa é recuperar a instância do observador de rede. Essa variável é passada para o `New-AzNetworkWatcherPacketCapture` cmdlet na etapa 4.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
```

### <a name="step-2"></a>Passo 2

Recuperar uma conta de armazenamento. Essa conta de armazenamento é usada para armazenar o arquivo de captura de pacote.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>Passo 3

Os filtros podem ser usados para limitar os dados armazenados pela captura de pacotes. O exemplo a seguir configura dois filtros.  Um filtro coleta o tráfego TCP de saída somente do 10.0.0.3 IP local para as portas de destino 20, 80 e 443.  O segundo filtro coleta apenas o tráfego UDP.

```powershell
$filter1 = New-AzPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Vários filtros podem ser definidos para uma captura de pacote.

### <a name="step-4"></a>Passo 4

Execute o `New-AzNetworkWatcherPacketCapture` cmdlet para iniciar o processo de captura de pacotes, passando os valores necessários recuperados nas etapas anteriores.
```powershell

New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filter $filter1, $filter2
```

O exemplo a seguir é a saída esperada da `New-AzNetworkWatcherPacketCapture` execução do cmdlet.

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

## <a name="get-a-packet-capture"></a>Obter uma captura de pacote

Executar o `Get-AzNetworkWatcherPacketCapture` cmdlet recupera o status de uma captura de pacote em execução no momento ou concluído.

```powershell
Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

O exemplo a seguir é a saída do `Get-AzNetworkWatcherPacketCapture` cmdlet. O exemplo a seguir é após a conclusão da captura. O valor de PacketCaptureStatus foi interrompido, com um stopReason de excedido. Esse valor mostra que a captura de pacote foi bem-sucedida e executou seu tempo.
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

Ao executar o `Stop-AzNetworkWatcherPacketCapture` cmdlet, se uma sessão de captura estiver em andamento, ela será interrompida.

```powershell
Stop-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> O cmdlet não retorna nenhuma resposta quando executado em uma sessão de captura atualmente em execução ou em uma sessão existente que já foi interrompida.

## <a name="delete-a-packet-capture"></a>Excluir uma captura de pacote

```powershell
Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> A exclusão de uma captura de pacote não exclui o arquivo na conta de armazenamento.

## <a name="download-a-packet-capture"></a>Baixar uma captura de pacote

Depois que a sessão de captura de pacote for concluída, o arquivo de captura poderá ser carregado no armazenamento de BLOBs ou em um arquivo local na VM. O local de armazenamento da captura de pacote é definido na criação da sessão. Uma ferramenta conveniente para acessar esses arquivos de captura salvos em uma conta de armazenamento é Gerenciador de Armazenamento do Microsoft Azure, que pode ser baixada aqui: https://storageexplorer.com/

Se uma conta de armazenamento for especificada, os arquivos de captura de pacote serão salvos em uma conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Passos seguintes

Saiba como automatizar as capturas de pacote com alertas de máquina virtual exibindo [criar uma captura de pacote](network-watcher-alert-triggered-packet-capture.md) disparada por alerta

Localize se determinado tráfego é permitido dentro ou fora de sua VM visitando verificar [fluxo de IP verificar](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->














