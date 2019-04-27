---
title: Extensão de máquina virtual do agente do observador de rede do Azure para Windows | Documentos da Microsoft
description: Implemente o agente do observador de rede na máquina de virtual do Windows com uma extensão da máquina virtual.
services: virtual-machines-windows
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 6e02f5a5b42da9c99a08782903cdc05ee32ec9d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743331"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Extensão de máquina virtual do agente do observador de rede para Windows

## <a name="overview"></a>Descrição geral

[O observador de rede do Azure](../../network-watcher/network-watcher-monitoring-overview.md) é um serviço de monitorização, diagnóstico e análise de desempenho de rede que permite a monitorização de redes do Azure. A extensão de máquina virtual do agente do observador de rede é um requisito para capturar o tráfego de rede a pedido e outras funcionalidades avançadas em máquinas virtuais do Azure.


Este documento detalha as plataformas suportadas e as opções de implementação para a extensão de máquina virtual do agente do observador de rede para Windows. Instalação do agente não interromper ou exigir uma reinicialização, da máquina virtual. Pode implementar a extensão em máquinas virtuais que implementar. Se a máquina virtual é implementada por um serviço do Azure, consulte a documentação para o serviço para determinar se é ou não permite a instalação de extensões na máquina virtual.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão de agente do observador de rede para o Windows podem ser executado em Windows Server 2008 R2, 2012 e 2012 R2 e 2016 versões. O servidor nano não é suportado neste momento.

### <a name="internet-connectivity"></a>Conectividade Internet

Algumas das funcionalidades do agente do observador de rede requer que a máquina virtual de destino estejam ligados à Internet. Sem a capacidade de estabelecer ligações de saída, o agente do observador de rede não poderá carregar capturas de pacotes para a conta de armazenamento. Para obter mais detalhes, consulte a [documentação do observador de rede](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão de agente do observador de rede. A extensão não necessita de, nem suporta, quaisquer definições fornecido pelo usuário e baseia-se na configuração predefinida.

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
| publicador | Microsoft.Azure.NetworkWatcher |
| tipo | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Implementação de modelos

É possível implementar extensões de VM do Azure com modelos Azure Resource Manager. Pode utilizar o esquema JSON detalhado na secção anterior num modelo Azure Resource Manager para executar a extensão de agente do observador de rede durante uma implementação de modelo do Azure Resource Manager.

## <a name="powershell-deployment"></a>Implementação do PowerShell

Utilize o `Set-AzVMExtension` comando para implementar a extensão de máquina virtual do agente do observador de rede para uma máquina virtual existente:

```powershell
Set-AzVMExtension `
  -ResourceGroupName "myResourceGroup1" `
  -Location "WestUS" `
  -VMName "myVM1" `
  -Name "networkWatcherAgent" `
  -Publisher "Microsoft.Azure.NetworkWatcher" `
  -Type "NetworkWatcherAgentWindows" `
  -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshooting"></a>Resolução de problemas

Pode obter dados sobre o estado das implementações de extensão do portal do Azure e PowerShell. Para ver o estado de implementação de extensões para uma determinada VM, execute o seguinte comando com o módulo Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

Resultado da execução de extensão é registado para arquivos encontrados no diretório seguinte:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode consultar a documentação do guia de utilizador do observador de rede ou entre em contato com os especialistas do Azure no [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
