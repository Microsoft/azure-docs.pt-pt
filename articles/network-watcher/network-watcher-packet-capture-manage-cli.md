---
title: Gerir capturas de pacotes com o Azure Network Watcher - Azure CLI / Microsoft Docs
description: Esta página explica como gerir a funcionalidade de captura de pacotes do Network Watcher usando o Azure CLI
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 7a69610d1ac176354a9d7e388a12ccc7f064d848
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382720"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>Gerir capturas de pacotes com o Azure Network Watcher utilizando o Azure CLI

> [!div class="op_single_selector"]
> - [Portal Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [API REST do Azure](network-watcher-packet-capture-manage-rest.md)

A captura de pacotes do Network Watcher permite-lhe criar sessões de captura para rastrear o tráfego de e para uma máquina virtual. Os filtros são fornecidos para a sessão de captura para garantir que captura apenas o tráfego que deseja. A captura de pacotes ajuda a diagnosticar anomalias de rede tanto de forma reativa como proactiva. Outros usos incluem a recolha de estatísticas de rede, a obtenção de informação sobre intrusões de rede, para depurar comunicações de servidores de clientes e muito mais. Ao ser capaz de acionar remotamente as capturas de pacotes, esta capacidade alivia o fardo de executar uma captura manual de pacotes e sobre a máquina desejada, o que poupa tempo valioso.

Para realizar os passos deste artigo, é necessário instalar a Interface de [Linha de Comando Azure para Mac, Linux e Windows (Azure CLI)](/cli/azure/install-azure-cli).

Este artigo leva-o através das diferentes tarefas de gestão que estão atualmente disponíveis para captura de pacotes.

- [**Iniciar uma captura de pacote**](#start-a-packet-capture)
- [**Parar uma captura de pacote**](#stop-a-packet-capture)
- [**Eliminar uma captura de pacote**](#delete-a-packet-capture)
- [**Descarregue uma captura de pacotes**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Antes de começar

Este artigo assume que tem os seguintes recursos:

- Uma instância de Network Watcher na região que você quer criar uma captura de pacotes
- Uma máquina virtual com a extensão de captura do pacote ativada.

> [!IMPORTANT]
> A captura do pacote requer que um agente esteja a funcionar na máquina virtual. O Agente está instalado como uma extensão. Para obter instruções sobre extensões VM, visite [extensões e funcionalidades](../virtual-machines/windows/extensions-features.md)da Máquina Virtual .

## <a name="install-vm-extension"></a>Instalar a extensão de VM

### <a name="step-1"></a>Passo 1

Execute `az vm extension set` o comando para instalar o agente de captura de pacotes na máquina virtual do hóspede.

Para máquinas virtuais Windows:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Para máquinas virtuais Linux:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux --version 1.4
```

### <a name="step-2"></a>Passo 2

Para garantir que o agente está `vm extension show` instalado, execute o comando e passe-o pelo grupo de recursos e pelo nome da máquina virtual. Verifique a lista resultante para se certificar de que o agente está instalado.

Para máquinas virtuais Windows:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

Para máquinas virtuais Linux:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name AzureNetworkWatcherExtension
```

A amostra que se segue é um exemplo da resposta de correr`az vm extension show`

```json
{
  "autoUpgradeMinorVersion": true,
  "forceUpdateTag": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/extensions/NetworkWatcherAgentWindows",
  "instanceView": null,
  "location": "westcentralus",
  "name": "NetworkWatcherAgentWindows",
  "protectedSettings": null,
  "provisioningState": "Succeeded",
  "publisher": "Microsoft.Azure.NetworkWatcher",
  "resourceGroup": "{resourceGroupName}",
  "settings": null,
  "tags": null,
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "typeHandlerVersion": "1.4",
  "virtualMachineExtensionType": "NetworkWatcherAgentWindows"
}
```

## <a name="start-a-packet-capture"></a>Iniciar uma captura de pacote

Uma vez concluídos os passos anteriores, o agente de captura do pacote é instalado na máquina virtual.

### <a name="step-1"></a>Passo 1

Recuperar uma conta de armazenamento. Esta conta de armazenamento é usada para armazenar o ficheiro de captura do pacote.

```azurecli-interactive
az storage account list
```

### <a name="step-2"></a>Passo 2

Neste momento, está pronto para criar uma captura de pacotes.  Primeiro, vamos examinar os parâmetros que talvez queira configurar. Os filtros são um desses parâmetros que podem ser usados para limitar os dados armazenados pela captura do pacote. O exemplo seguinte configura uma captura de pacote com vários filtros.  Os três primeiros filtros recolhem tráfego de TCP de saída apenas desde IP local 10.0.0.3 para os portos de destino 20, 80 e 443.  O último filtro recolhe apenas o tráfego uDP.

```azurecli-interactive
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

O exemplo seguinte é a `az network watcher packet-capture create` saída esperada de executar o comando.

```json
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/p
roviders/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapture_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="get-a-packet-capture"></a>Obtenha uma captura de pacote

Executar `az network watcher packet-capture show-status` o comando, recuperar o estado de uma captura de pacote sinuosamente em execução ou concluída.

```azurecli-interactive
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

O exemplo seguinte é `az network watcher packet-capture show-status` a saída do comando. O exemplo seguinte é quando a captura é interrompida, com uma StopReason of TimeExceeded.

```
{
  "additionalProperties": {
    "status": "Succeeded"
  },
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "packetCaptureError": [],
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded"
}
```

## <a name="stop-a-packet-capture"></a>Parar uma captura de pacote

Ao executar `az network watcher packet-capture stop` o comando, se uma sessão de captura estiver em andamento, é interrompida.

```azurecli-interactive
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> O comando não responde quando foi feito numa sessão de captura em execução ou numa sessão existente que já parou.

## <a name="delete-a-packet-capture"></a>Eliminar uma captura de pacote

```azurecli-interactive
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> A eliminação de uma captura de pacote não elimina o ficheiro na conta de armazenamento.

## <a name="download-a-packet-capture"></a>Descarregue uma captura de pacotes

Uma vez concluída a sua sessão de captura de pacotes, o ficheiro de captura pode ser enviado para armazenamento de bolhas ou para um ficheiro local no VM. A localização de armazenamento da captura do pacote é definida na criação da sessão. Uma ferramenta conveniente para aceder a estes ficheiros de captura guardados numa conta de armazenamento é o Microsoft Azure Storage Explorer, que pode ser descarregado aqui:https://storageexplorer.com/

Se for especificada uma conta de armazenamento, os ficheiros de captura de pacotes são guardados numa conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Passos seguintes

Saiba automatizar capturas de pacotes com alertas de máquina virtual ao ver [Criar um alerta desencadeado pela captura de pacotes](network-watcher-alert-triggered-packet-capture.md)

Descubra se determinado tráfego é permitido dentro ou fora do seu VM visitando [check IP flow verificar](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->
