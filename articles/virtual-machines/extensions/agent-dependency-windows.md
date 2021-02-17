---
title: Extensão da máquina virtual Azure Monitor Dependency para Windows
description: Implementar o agente de dependência do Monitor Azure na máquina virtual do Windows utilizando uma extensão de máquina virtual.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 0151e7b3a30cd4b00dba75b7490563923cd5b8ff
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580300"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Extensão da máquina virtual Azure Monitor Dependency para Windows

A funcionalidade Azure Monitor for VMs Map obtém os seus dados do agente de dependência da Microsoft. A extensão da máquina virtual do agente Azure VM Dependency para windows é publicada e suportada pela Microsoft. A extensão instala o agente Dependency em máquinas virtuais Azure. Este documento detalha as plataformas, configurações e opções de implementação suportadas para a extensão da máquina virtual do agente Azure VM Dependency para windows.

## <a name="operating-system"></a>Sistema operativo

A extensão do agente de dependência Azure VM para windows pode ser executada contra os sistemas operativos suportados listados na secção de [sistemas operativos suportados](../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) do Azure Monitor para o artigo de implantação de VMs.

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão do agente de dependência Azure VM num Azure Windows VM.

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

### <a name="property-values"></a>Valores patrimoniais

| Name | Valor/Exemplo |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publicador | Microsoft.Azure.Monitoring.DependencyAgent |
| tipo | DependênciaAgentWindows |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Implementação de modelos

Pode implementar as extensões Azure VM com modelos Azure Resource Manager. Pode utilizar o esquema JSON detalhado na secção anterior num modelo do Azure Resource Manager para executar a extensão do agente de dependência Azure VM durante uma implementação do modelo do Azure Resource Manager.

O JSON para uma extensão de máquina virtual pode ser aninhado dentro do recurso da máquina virtual. Ou, pode colocá-lo no nível raiz ou superior de um modelo JSON do Gestor de Recursos. A colocação do JSON afeta o valor do nome e do tipo de recurso. Para obter mais informações, consulte [o nome definido e o tipo para obter recursos para crianças.](../../azure-resource-manager/templates/child-resource-name-type.md)

O exemplo a seguir pressupõe que a extensão do agente De dependência está aninhada dentro do recurso da máquina virtual. Quando se aninha o recurso de extensão, o JSON é colocado no `"resources": []` objeto da máquina virtual.


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

Quando coloca a extensão JSON na raiz do modelo, o nome do recurso inclui uma referência à máquina virtual dos pais. O tipo reflete a configuração aninhada.

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

## <a name="powershell-deployment"></a>Implementação do PowerShell

Pode utilizar o `Set-AzVMExtension` comando para implantar a extensão da máquina virtual do agente Desadependante a uma máquina virtual existente. Antes de executar o comando, as configurações públicas e privadas precisam de ser armazenadas numa tabela de haxixe PowerShell.

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das extensões podem ser recuperados a partir do portal Azure e utilizando o módulo Azure PowerShell. Para ver o estado de implantação das extensões para um determinado VM, executar o seguinte comando utilizando o módulo Azure PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A saída de execução de extensão é registada em ficheiros encontrados no seguinte diretório:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Ou pode apresentar um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **suporte**. Para obter informações sobre como utilizar o Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
