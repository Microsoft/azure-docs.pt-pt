---
title: "Extensão de máquina virtual de agente do observador de rede do Azure para Windows | Microsoft Docs"
description: "Implemente o agente do observador de rede na máquina virtual do Windows com uma extensão da máquina virtual."
services: virtual-machines-windows
documentationcenter: 
author: dennisg
manager: amku
editor: 
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 508b3755556bcae6aa2c7d17a2d86a1430a8109a
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2017
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Extensão da máquina virtual de agente do observador de rede para Windows

## <a name="overview"></a>Descrição geral

[Observador de rede do Azure](https://review.docs.microsoft.com/azure/network-watcher/) é um serviço de monitorização, diagnóstico e análise de desempenho de rede que permite a monitorização de redes do Azure. A extensão de máquina virtual de agente do observador de rede é um requisito para algumas das funcionalidades de observador de rede em máquinas virtuais do Azure. Isto inclui capturar o tráfego de rede a pedido e outras funcionalidades avançadas.

Este documento fornece detalhes sobre as plataformas suportadas e as opções de implementação para a extensão de máquina virtual de agente do observador de rede para o Windows.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão de agente do observador de rede para o Windows pode ser executado no Windows Server 2008 R2, 2012, 2012 R2 e 2016 versões. Tenha em atenção que o servidor de Nano não é suportado neste momento.

### <a name="internet-connectivity"></a>Conectividade Internet

Algumas das funcionalidades do agente do observador de rede requer que a máquina virtual de destino estar ligado à Internet. Sem a capacidade para estabelecer ligações de saída algumas das funcionalidades do agente do observador de rede podem avaria ou ficar indisponíveis. Para obter mais detalhes, consulte o [documentação de observador de rede](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão de agente do observador de rede. A extensão não necessita de nem suporta quaisquer definições fornecido pelo utilizador neste momento e baseia-se na sua configuração predefinida.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentWindows",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| Fabricante | Microsoft.Azure.NetworkWatcher |
| tipo | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na secção anterior pode ser utilizado num modelo Azure Resource Manager para executar a extensão de agente do observador de rede durante uma implementação de modelo Azure Resource Manager.

## <a name="powershell-deployment"></a>Implementação de PowerShell

O `Set-AzureRmVMExtension` comando pode ser utilizado para implementar a extensão de máquina virtual de agente do observador de rede para uma máquina virtual existente.

```powershell
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup1" `
                       -Location "WestUS" `
                       -VMName "myVM1" `
                       -Name "networkWatcherAgent" `
                       -Publisher "Microsoft.Azure.NetworkWatcher" `
                       -Type "NetworkWatcherAgentWindows" `
                       -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshooting"></a>Resolução de problemas

É possível obter dados sobre o estado das implementações de extensão do portal do Azure e utilizando o módulo Azure PowerShell. Para ver o estado de implementação das extensões para uma determinada VM, execute o seguinte comando utilizando o módulo PowerShell do Azure.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

Resultado da execução de extensão é registado para ficheiros encontrados no diretório seguinte:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda, a qualquer altura neste artigo, pode consulte a documentação do guia de utilizador do observador de rede ou contactar as especialistas do Azure no [fóruns do MSDN Azure e Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Em alternativa, pode ficheiro um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/en-us/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/en-us/support/faq/).
