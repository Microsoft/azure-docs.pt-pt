---
title: Extensão de máquina virtual do Agente do Observador de Rede do Azure para Windows
description: Implemente o Agente observador de rede na máquina virtual do Windows utilizando uma extensão de máquina virtual.
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: mimckitt
ms.openlocfilehash: 813fc7db2a028590797bef1c54fff20b231c3d7d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965906"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Extensão de máquina virtual do Agente observador de rede para Windows

## <a name="overview"></a>Descrição geral

[O Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) é um serviço de monitorização, diagnóstico e análise de desempenho de rede que permite a monitorização das redes Azure. A extensão da máquina virtual do Network Watcher Agent é um requisito para capturar o tráfego de rede a pedido, e outras funcionalidades avançadas em máquinas virtuais Azure.


Este documento detalha as plataformas suportadas e as opções de implementação da extensão da máquina virtual do Network Watcher Agent para o Windows. A instalação do agente não interrompe, nem requer um reinício, da máquina virtual. Pode implantar a extensão em máquinas virtuais que implementar. Se a máquina virtual for implantada por um serviço Azure, verifique a documentação do serviço para determinar se permite ou não instalar extensões na máquina virtual.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão do Agente observador de rede para windows pode ser executada contra as versões do Windows Server 2008 R2, 2012, 2012 R2, 2016 e 2019. Nano Server não é suportado neste momento.

### <a name="internet-connectivity"></a>Conectividade Internet

Algumas das funcionalidades do Agente observador de rede requerem que a máquina virtual-alvo seja ligada à Internet. Sem a capacidade de estabelecer ligações de saída, o Agente observador de rede não será capaz de enviar capturas de pacotes para a sua conta de armazenamento. Para mais detalhes, consulte a [documentação do Observador de Rede.](../../network-watcher/network-watcher-monitoring-overview.md)

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão do Agente observador de rede. A extensão não requer, nem suporta, quaisquer definições fornecidas pelo utilizador, e baseia-se na sua configuração padrão.

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

### <a name="property-values"></a>Valores patrimoniais

| Nome | Valor / Exemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publicador | Microsoft.Azure.NetworkWatcher |
| tipo | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Implementação de modelos

Pode implementar extensões Azure VM com modelos Azure Resource Manager. Pode utilizar o esquema JSON detalhado na secção anterior num modelo do Azure Resource Manager para executar a extensão do Agente observador de rede durante uma implementação do modelo do Azure Resource Manager.

## <a name="powershell-deployment"></a>Implementação do PowerShell

Utilize o `Set-AzVMExtension` comando para implantar a extensão da máquina virtual do Network Watcher Agent para uma máquina virtual existente:

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

## <a name="troubleshooting-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshooting"></a>Resolução de problemas

Pode obter dados sobre o estado das extensões do portal Azure e powerShell. Para ver o estado de implantação das extensões para um determinado VM, executar o seguinte comando utilizando o módulo Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

A saída de execução de extensão é registada em ficheiros encontrados no seguinte diretório:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode consultar a documentação do Guia do Utilizador do Observador de Rede ou contactar os especialistas do Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
