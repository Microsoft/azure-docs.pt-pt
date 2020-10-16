---
title: Atualizar a extensão do Observador de Rede para a versão mais recente
description: Saiba como atualizar a extensão do Observador de Rede Azure para a versão mais recente.
services: virtual-machines-windows
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: e367c348364d03cec6914c99e7ff112803fc58f6
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132436"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>Atualizar a extensão do Observador de Rede para a versão mais recente

## <a name="overview"></a>Descrição geral

[O Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) é um serviço de monitorização, diagnóstico e análise de desempenho de rede que monitoriza as redes Azure. A extensão da máquina virtual do Network Watcher Agent (VM) é um requisito para capturar o tráfego de rede a pedido e utilizar outra funcionalidade avançada em VMs Azure. A extensão do Observador de Rede é utilizada por funcionalidades como Monitor de Ligação, Monitor de Ligação (pré-visualização), resolução de problemas de ligação e captura de pacotes.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tem a extensão do Observador de Rede instalada no seu VM.

## <a name="latest-version"></a>Versão mais recente

A versão mais recente da extensão do Observador de Rede encontra-se atualmente `1.4.1654.1` .

## <a name="update-your-extension"></a>Atualize a sua extensão

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
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

Localizar a extensão AzureNetworkWatcher na saída. Identifique o número da versão no campo "TypeHandlerVersion" na saída.  

#### <a name="usepowershell"></a>Use PowerShell

Executar os seguintes comandos a partir de um pedido PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

Localizar a extensão AzureNetworkWatcher na saída. Identifique o número da versão no campo "TypeHandlerVersion" na saída.

### <a name="update-your-extension"></a>Atualize a sua extensão

Se a sua versão for mais cedo do `1.4.1654.1` que – que é a versão mais recente, atualize a sua extensão utilizando qualquer uma das seguintes opções.

#### <a name="option-1-use-powershell"></a>Opção 1: Utilizar PowerShell

Executar os seguintes comandos:

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
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