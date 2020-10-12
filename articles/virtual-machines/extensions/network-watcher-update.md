---
title: Atualizar a Extensão do Observador de Rede para a versão mais recente
description: Saiba como atualizar a extensão do Observador de Rede para a versão mais recente
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
ms.openlocfilehash: c386685d63894472623ffc4392a529541a91391c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91410345"
---
# <a name="how-to-update-the-network-watcher-extension-to-the-latest-the-version"></a>Como atualizar a Extensão do Observador de Rede para a versão mais recente 

## <a name="overview"></a>Descrição geral

[O Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) é um serviço de monitorização, diagnóstico e análise de desempenho de rede que permite a monitorização das redes Azure. A extensão da máquina virtual do Network Watcher Agent é um requisito para capturar o tráfego de rede a pedido, e outras funcionalidades avançadas em máquinas virtuais Azure. A extensão do Observador de Rede é utilizada por funcionalidades como Monitor de Ligação, Monitor de Ligação (Pré-visualização), Resolução de Problemas de Ligação e Captura de Pacotes.   

## <a name="prerequisites"></a>Pré-requisitos
Este documento pressupõe que tem a Extensão do Observador de Rede instalada na sua máquina virtual e fornece instruções para a sua atualização para a versão mais recente. 

## <a name="latest-version"></a>Versão mais recente
A versão mais recente da extensão do Observador de Rede encontra-se atualmente `1.4.1654.1` .

## <a name="updating-your-extension"></a>Atualizar a sua extensão 

### <a name="check-your-extension-version"></a>Verifique a sua versão de extensão  

**Usando o portal Azure**

1. Aceda à lâmina 'Extensões' do seu VM no portal Azure.   
2. Clique na extensão 'AzureNetworkWatcher' para ver o painel de detalhes.  
3. Localize o número da versão no campo 'Versão'.  

**Usando O Azure CLI** Executar o comando abaixo a partir de um pedido de Azure CLI.   

```azurecli
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

Localize a extensão AzureNetworkWatcher na saída e identifique o número de versão do campo "TypeHandlerVersion" na saída.  


**Usando PowerShell** Executar os seguintes comandos a partir de um pedido PowerShell:   

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

Localize a extensão AzureNetworkWatcher na saída e identifique o número de versão do campo "TypeHandlerVersion" na saída.   


### <a name="update-your-extension"></a>Atualize a sua extensão

Caso a sua versão seja inferior `1.4.1654.1` à (a versão mais recente), atualize a sua extensão utilizando qualquer uma das seguintes opções. 

**Opção 1: Utilizar PowerShell**

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
```


**Opção 2: Utilizar Azure CLI**  

Atualização de força 

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Se não funcionar. Retire e instale novamente a extensão, utilizando os passos abaixo. Isto irá automaticamente adicionar a versão mais recente. 

Remoção da extensão 

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Instalação da extensão novamente

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"  

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" 

```

**Opção 3: Reiniciar os seus VMs**

Se tiver uma atualização automática definida para ser verdadeira para a extensão NetworkWatcher. Reiniciando o seu VM instale a última extensão.


## <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode consultar a documentação da Extensão do Observador de Rede[(Linux,](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) [Windows)](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)ou contactar os especialistas do Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
