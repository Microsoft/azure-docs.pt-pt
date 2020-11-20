---
title: Extensão da máquina virtual Azure Monitor Dependency para Linux
description: Implementar o agente de dependência do Monitor Azure na máquina virtual Linux utilizando uma extensão de máquina virtual.
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: c8427e92f89804f64741ab86aa9903b0de23b3ec
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966858"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Extensão da máquina virtual Azure Monitor Dependency para Linux

A funcionalidade Azure Monitor for VMs Map obtém os seus dados do agente de dependência da Microsoft. A extensão da máquina virtual do agente Azure VM Dependency para o Linux é publicada e suportada pela Microsoft. A extensão instala o agente Dependency em máquinas virtuais Azure. Este documento detalha as plataformas, configurações e opções de implementação suportadas para a extensão virtual da máquina virtual do agente Azure VM Dependency para o Linux.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão do agente de dependência Azure VM para o Linux pode ser executada contra os sistemas operativos suportados listados na secção de [sistemas operativos suportados](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) do Azure Monitor para o artigo de implantação de VMs.

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão do agente de dependência Azure VM num Azure Linux VM. 

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

### <a name="property-values"></a>Valores patrimoniais

| Nome | Valor/Exemplo |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publicador | Microsoft.Azure.Monitoring.DependencyAgent |
| tipo | DependênciaAgentLinux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Implementação de modelos

Pode implementar extensões Azure VM com modelos Azure Resource Manager. Pode utilizar o esquema JSON detalhado na secção anterior num modelo do Azure Resource Manager para executar a extensão do agente de dependência Azure VM durante uma implementação do modelo do Azure Resource Manager.

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
        "type": "DependencyAgentLinux",
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
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Implantação do Azure CLI

Pode utilizar o CLI Azure para implantar a extensão VM do agente de dependência para uma máquina virtual existente.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das extensões podem ser recuperados a partir do portal Azure e utilizando o CLI Azure. Para ver o estado de implantação das extensões para um determinado VM, executar o seguinte comando utilizando o Azure CLI:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída de execução de extensão é registada no seguinte ficheiro:

```
/opt/microsoft/dependency-agent/log/install.log 
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, contacte os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Ou pode apresentar um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **suporte**. Para obter informações sobre como utilizar o Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
