---
title: Extensão da máquina virtual do agente do observador de rede do Azure para Windows
description: Implante o agente do observador de rede na máquina virtual do Windows usando uma extensão de máquina virtual.
services: virtual-machines-windows
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 998e160edce25b9d466a1db090abcefeb7870172
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073693"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Extensão da máquina virtual do agente do observador de rede para Windows

## <a name="overview"></a>Descrição geral

O [observador de rede do Azure](../../network-watcher/network-watcher-monitoring-overview.md) é um serviço de monitoramento, diagnóstico e análise de desempenho de rede que permite o monitoramento de redes do Azure. A extensão da máquina virtual do agente do observador de rede é um requisito para capturar o tráfego de rede sob demanda e outras funcionalidades avançadas em máquinas virtuais do Azure.


Este documento detalha as plataformas com suporte e as opções de implantação para a extensão da máquina virtual do agente do observador de rede para Windows. A instalação do agente não interrompe nem exige uma reinicialização da máquina virtual. Você pode implantar a extensão em máquinas virtuais implantadas. Se a máquina virtual for implantada por um serviço do Azure, verifique a documentação do serviço para determinar se ele permite ou não instalar extensões na máquina virtual.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão do agente do observador de rede para Windows pode ser executada em versões do Windows Server 2008 R2, 2012, 2012 R2 e 2016. No momento, não há suporte para o nano Server.

### <a name="internet-connectivity"></a>Conectividade Internet

Algumas das funcionalidades do agente do observador de rede exigem que a máquina virtual de destino esteja conectada à Internet. Sem a capacidade de estabelecer conexões de saída, o agente do observador de rede não será capaz de carregar as capturas de pacotes para sua conta de armazenamento. Para obter mais detalhes, consulte a [documentação do observador de rede](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão do agente do observador de rede. A extensão não requer, nem dá suporte a, quaisquer configurações fornecidas pelo usuário e depende de sua configuração padrão.

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
| publisher | Microsoft.Azure.NetworkWatcher |
| tipo | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Implementação de modelos

Você pode implantar extensões de VM do Azure com modelos de Azure Resource Manager. Você pode usar o esquema JSON detalhado na seção anterior em um modelo de Azure Resource Manager para executar a extensão do agente do observador de rede durante uma implantação de modelo de Azure Resource Manager.

## <a name="powershell-deployment"></a>Implantação do PowerShell

Use o comando `Set-AzVMExtension` para implantar a extensão da máquina virtual do agente do observador de rede em uma máquina virtual existente:

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

Você pode recuperar dados sobre o estado das implantações de extensão do portal do Azure e do PowerShell. Para ver o estado de implantação das extensões de uma determinada VM, execute o seguinte comando usando o módulo Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

A saída de execução de extensão é registrada em arquivos encontrados no seguinte diretório:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, consulte a documentação do guia do usuário do observador de rede ou entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow do MSDN](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
