---
title: Extensão de máquina virtual do Agente do Observador de Rede do Azure para Windows
description: Implemente o Agente de Observador de Rede na máquina virtual do Windows utilizando uma extensão virtual da máquina.
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: mimckitt
ms.openlocfilehash: f226e240a59b33c2913919495410b1a4923b4902
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81261674"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Extensão virtual do Agente observador de rede para Windows

## <a name="overview"></a>Descrição geral

[O Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) é um serviço de monitorização, diagnóstico e análise de desempenho da rede que permite a monitorização das redes Azure. A extensão virtual da máquina do Agente observador de rede é um requisito para capturar o tráfego de rede a pedido, e outras funcionalidades avançadas em máquinas virtuais Azure.


Este documento detalha as plataformas suportadas e as opções de implementação para a extensão virtual da máquina virtual do Agente Observador de Rede para windows. A instalação do agente não interrompe, nem exige uma reinicialização da máquina virtual. Pode implantar a extensão em máquinas virtuais que implementa. Se a máquina virtual for implantada por um serviço Azure, verifique a documentação do serviço para determinar se permite ou não instalar extensões na máquina virtual.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão do Agente de Observador de Rede para Windows pode ser executada contra os lançamentos do Windows Server 2008 R2, 2012, 2012 R2 e 2016. Nano Server não é suportado neste momento.

### <a name="internet-connectivity"></a>Conectividade Internet

Algumas funcionalidades do Agente observador de rede requerem que a máquina virtual alvo seja ligada à Internet. Sem a capacidade de estabelecer ligações de saída, o Agente observador de rede não será capaz de enviar capturas de pacotes para a sua conta de armazenamento. Para mais detalhes, consulte a [documentação](../../network-watcher/network-watcher-monitoring-overview.md)do Observador da Rede .

## <a name="extension-schema"></a>Esquema de extensão

O seguinte JSON mostra o esquema para a extensão do Agente observador da rede. A extensão não requer, nem suporte, quaisquer definições fornecidas pelo utilizador, e baseia-se na sua configuração predefinida.

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
| tipo | Windows de Agente de NetworkWatcher |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Implementação de modelos

Pode implementar extensões Azure VM com modelos de Gestor de Recursos Azure. Pode utilizar o esquema JSON detalhado na secção anterior num modelo de Gestor de Recursos Azure para executar a extensão do Agente de Observador de Rede durante uma implementação do modelo do Gestor de Recursos Azure.

## <a name="powershell-deployment"></a>Implementação powerShell

Utilize `Set-AzVMExtension` o comando para implantar a extensão virtual da máquina virtual do Agente observador de rede para uma máquina virtual existente:

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

Pode obter dados sobre o estado das implementações de extensão do portal Azure e powerShell. Para ver o estado de implantação das extensões para um dado VM, execute o seguinte comando utilizando o módulo PowerShell Azure:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

A saída de execução de extensão é registada em ficheiros encontrados no seguinte diretório:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode consultar a documentação do Guia de Utilizadores do Observador da Rede ou contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
