---
title: Extensão de máquina virtual de dependência Azure Monitor para Windows | Microsoft Docs
description: Implante o Azure Monitor agente de dependência na máquina virtual do Windows usando uma extensão de máquina virtual.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: b2a86ffab0286559cfcce89252c889c530f00871
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750110"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Extensão de máquina virtual de dependência Azure Monitor para Windows

O recurso de mapa de Azure Monitor para VMs obtém seus dados do Microsoft Dependency Agent. A extensão da máquina virtual do agente de dependência de VM do Azure para Windows é publicada e tem suporte da Microsoft. A extensão instala o agente de dependência em máquinas virtuais do Azure. Este documento detalha as plataformas com suporte, as configurações e as opções de implantação para a extensão de máquina virtual do agente de dependência de VM do Azure para Windows.

## <a name="operating-system"></a>Sistema operativo

A extensão do agente de dependência de VM do Azure para Windows pode ser executada nos sistemas operacionais com suporte listados na seção [sistemas operacionais com suporte](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) no artigo Azure monitor para VMs implantação.

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão do agente de dependência de VM do Azure em uma VM do Windows do Azure.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/exemplo |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| Programa | Microsoft. Azure. Monitoring. DependencyAgent |
| tipo | DependencyAgentWindows |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Implementação de modelos

Você pode implantar as extensões de VM do Azure com modelos de Azure Resource Manager. Você pode usar o esquema JSON detalhado na seção anterior em um modelo de Azure Resource Manager para executar a extensão do agente de dependência de VM do Azure durante uma implantação de modelo de Azure Resource Manager.

O JSON para uma extensão de máquina virtual pode ser aninhado dentro do recurso de máquina virtual. Ou você pode colocá-lo no nível raiz ou superior de um modelo JSON do Resource Manager. O posicionamento do JSON afeta o valor do nome do recurso e do tipo. Para obter mais informações, consulte [definir nome e tipo para recursos filho](../../azure-resource-manager/child-resource-name-type.md).

O exemplo a seguir pressupõe que a extensão do agente de dependência está aninhada dentro do recurso de máquina virtual. Quando você Aninha o recurso de extensão, o JSON é colocado no objeto `"resources": []` da máquina virtual.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

Quando você coloca a extensão JSON na raiz do modelo, o nome do recurso inclui uma referência à máquina virtual pai. O tipo reflete a configuração aninhada.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>Implantação do PowerShell

Você pode usar o comando `Set-AzVMExtension` para implantar a extensão da máquina virtual do agente de dependência em uma máquina virtual existente. Antes de executar o comando, as configurações públicas e privadas precisam ser armazenadas em uma tabela de hash do PowerShell.

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das implantações de extensão podem ser recuperados do portal do Azure e usando o módulo Azure PowerShell. Para ver o estado de implantação das extensões de uma determinada VM, execute o seguinte comando usando o módulo Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A saída de execução de extensão é registrada em arquivos encontrados no seguinte diretório:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, você poderá entrar em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow do MSDN](https://azure.microsoft.com/support/forums/). Ou, você pode arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**. Para obter informações sobre como usar o suporte do Azure, leia as [perguntas frequentes sobre suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
