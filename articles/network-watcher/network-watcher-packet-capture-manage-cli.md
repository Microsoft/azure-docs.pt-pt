---
title: Gerir capturas de pacotes com O Observador da Rede Azure - Azure CLI Microsoft Docs
description: Esta página explica como gerir a funcionalidade de captura de pacotes do Observador de Rede utilizando o Azure CLI
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 838a5255c013b530aa3bfdb857a2ba34b7dbeeed
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98010970"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>Gerir capturas de pacotes com O Observador de Rede Azure usando o Azure CLI

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI do Azure](network-watcher-packet-capture-manage-cli.md)
> - [API REST do Azure](network-watcher-packet-capture-manage-rest.md)

A captura de pacotes do Network Watcher permite-lhe criar sessões de captura para rastrear o tráfego de e para uma máquina virtual. Os filtros são fornecidos para a sessão de captura para garantir que captura apenas o tráfego que deseja. A captura de pacotes ajuda a diagnosticar anomalias de rede reativas e proativamente. Outros usos incluem recolher estatísticas de rede, obter informações sobre intrusões de rede, depurar comunicações de servidores de clientes e muito mais. Ao ser capaz de ativar remotamente capturas de pacotes, esta capacidade alivia o fardo de executar uma captura de pacote manualmente e na máquina desejada, o que poupa tempo valioso.

Para executar os passos deste artigo, é necessário [instalar a Interface Azure Command-Line para Mac, Linux e Windows (Azure CLI)](/cli/azure/install-azure-cli).

Este artigo leva-o através das diferentes tarefas de gestão que estão atualmente disponíveis para captura de pacotes.

- [**Inicie uma captura de pacotes**](#start-a-packet-capture)
- [**Pare uma captura de pacotes**](#stop-a-packet-capture)
- [**Excluir uma captura de pacotes**](#delete-a-packet-capture)
- [**Faça o download de uma captura de pacotes**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que tem os seguintes recursos:

- Um exemplo de Network Watcher na região que pretende criar uma captura de pacotes
- Uma máquina virtual com a extensão de captura do pacote ativada.

> [!IMPORTANT]
> A captura de pacotes requer que um agente esteja a funcionar na máquina virtual. O Agente está instalado como uma extensão. Para obter instruções sobre extensões VM, visite [extensões e funcionalidades da Máquina Virtual](../virtual-machines/extensions/features-windows.md).

## <a name="install-vm-extension"></a>Instalar a extensão de VM

### <a name="step-1"></a>Passo 1

Executar o `az vm extension set` comando para instalar o agente de captura de pacotes na máquina virtual do hóspede.

Para máquinas virtuais windows:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Para máquinas virtuais Linux:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux --version 1.4
```

### <a name="step-2"></a>Passo 2

Para garantir a instalação do agente, executar o `vm extension show` comando e passá-lo o grupo de recursos e o nome da máquina virtual. Verifique a lista resultante para se certificar de que o agente está instalado.

Para máquinas virtuais windows:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

Para máquinas virtuais Linux:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name AzureNetworkWatcherExtension
```

A amostra a seguir é um exemplo da resposta de correr `az vm extension show`

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

## <a name="start-a-packet-capture"></a>Inicie uma captura de pacotes

Uma vez concluídos os passos anteriores, o agente de captura de pacotes é instalado na máquina virtual.

### <a name="step-1"></a>Passo 1

Recupere uma conta de armazenamento. Esta conta de armazenamento é usada para armazenar o ficheiro de captura de pacotes.

```azurecli-interactive
az storage account list
```

### <a name="step-2"></a>Passo 2

Neste momento, está pronto para criar uma captura de pacotes.  Primeiro, vamos examinar os parâmetros que talvez queira configurar. Os filtros são um desses parâmetros que podem ser usados para limitar os dados que são armazenados pela captura do pacote. O exemplo a seguir configura uma captura de pacote com vários filtros.  Os três primeiros filtros recolhem o tráfego TCP de saída apenas a partir do IP local 10.0.0.3 para os portos de destino 20, 80 e 443.  O último filtro recolhe apenas tráfego UDP.

```azurecli-interactive
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

O exemplo a seguir é a saída esperada de executar o `az network watcher packet-capture create` comando.

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

## <a name="get-a-packet-capture"></a>Obtenha uma captura de pacotes

Executando o `az network watcher packet-capture show-status` comando, recupera o estado de uma captura de pacote atualmente em execução ou concluída.

```azurecli-interactive
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

O exemplo a seguir é a saída do `az network watcher packet-capture show-status` comando. O exemplo a seguir é quando a captura é interrompida, com um StopReason de TimeExceeded.

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

## <a name="stop-a-packet-capture"></a>Pare uma captura de pacotes

Ao executar o `az network watcher packet-capture stop` comando, se uma sessão de captura estiver em andamento, é interrompido.

```azurecli-interactive
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> O comando não retorna a nenhuma resposta quando funcionada numa sessão de captura em execução ou numa sessão já existente que já parou.

## <a name="delete-a-packet-capture"></a>Excluir uma captura de pacotes

```azurecli-interactive
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> A eliminação de uma captura de pacote não apaga o ficheiro na conta de armazenamento.

## <a name="download-a-packet-capture"></a>Faça o download de uma captura de pacotes

Uma vez concluída a sessão de captura do pacote, o ficheiro de captura pode ser carregado para o armazenamento de bolhas ou para um ficheiro local no VM. A localização de armazenamento da captura do pacote é definida na criação da sessão. Uma ferramenta conveniente para aceder a estes ficheiros de captura guardados numa conta de armazenamento é o Microsoft Azure Storage Explorer, que pode ser descarregado aqui:  https://storageexplorer.com/

Se uma conta de armazenamento for especificada, os ficheiros de captura de pacotes são guardados numa conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Próximos passos

Saiba como automatizar capturas de pacotes com alertas de máquina virtual ao visualizar [Criar uma captura de pacotes desencadeados por alerta](network-watcher-alert-triggered-packet-capture.md)

Descubra se determinado tráfego é permitido dentro ou fora do seu VM visitando [verificar o fluxo IP](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->