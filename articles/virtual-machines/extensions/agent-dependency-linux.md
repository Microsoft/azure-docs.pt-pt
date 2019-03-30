---
title: Extensão de máquina virtual de dependência de Monitor do Azure para Linux | Documentos da Microsoft
description: Implemente o agente de dependência de Monitor do Azure na máquina virtual Linux utilizando uma extensão de máquina virtual.
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 16491ccc8a9cdc24b82c4c4e27705fa30da61088
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58671519"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Extensão de máquina virtual de dependência de Monitor do Azure para Linux

O Azure Monitor para a funcionalidade de mapa de VMs obtém seus dados do agente do Microsoft Dependency. A extensão de máquina virtual do agente de dependência de VM do Azure para Linux é publicada e suportada pela Microsoft. A extensão instala o agente de dependência em máquinas virtuais do Azure. Este documento detalha as plataformas suportadas, configurações e opções de implementação para a extensão de máquina virtual do agente de dependência de VM do Azure para Linux.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão de agente de dependência de VM do Azure para Linux pode ser executada em sistemas operativos suportados listados na [sistemas operativos suportados](../../azure-monitor/insights/vminsights-onboard.md#supported-operating-systems) secção do Monitor do Azure para o artigo de implementação de VMs.

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão de agente de dependência de VM do Azure numa VM do Linux do Azure. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
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
        "type": "DependencyAgentLinux",
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

| Nome | Valor / exemplo |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publicador | Microsoft.Azure.Monitoring.DependencyAgent |
| tipo | DependencyAgentLinux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na secção anterior pode ser utilizado num modelo do Azure Resource Manager para executar a extensão de agente de dependência de VM do Azure durante uma implementação de modelo do Azure Resource Manager. 

O JSON para uma extensão de máquina virtual pode ser aninhado dentro do recurso de máquina virtual ou colocado na raiz ou de nível superior de um modelo do Resource Manager JSON. A colocação do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, consulte [defina o nome e tipo para recursos subordinados](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources). 

O exemplo a seguir supõe que a extensão de agente de dependência é aninhada dentro do recurso de máquina virtual. Quando aninhar o recurso de extensão, o JSON é colocado no `"resources": []` objeto da máquina virtual.


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
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

Quando coloca a extensão de JSON na raiz do modelo, o nome do recurso inclui uma referência à máquina virtual principal e o tipo reflete a configuração aninhada. 

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
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

A CLI do Azure pode ser utilizada para implementar o agente de dependência extensão de VM para uma máquina virtual existente.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Podem ser obtidos dados sobre o estado das implementações de extensão do portal do Azure e com a CLI do Azure. Para ver o estado de implementação de extensões para uma determinada VM, execute o seguinte comando com a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Resultado da execução de extensão é registado para o ficheiro seguinte:

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
