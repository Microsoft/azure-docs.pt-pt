---
title: Extensão de máquina virtual de Dependência do Monitor Azure para Linux
description: Desloque o agente de dependência do Monitor Azure na máquina virtual Linux utilizando uma extensão virtual da máquina.
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 82f9c5a67cb056752cf8310be3b7c9f0bd2501e9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254043"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Extensão de máquina virtual de Dependência do Monitor Azure para Linux

O Azure Monitor para a funcionalidade de mapa de VMs obtém seus dados do agente do Microsoft Dependency. A extensão virtual da máquina virtual do agente de dependência Azure VM para o Linux é publicada e suportada pela Microsoft. A extensão instala o agente Dependency em máquinas virtuais Azure. Este documento detalha as plataformas, configurações e opções de implementação suportadas para a extensão virtual da máquina virtual do agente de dependência Azure VM para o Linux.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão do agente de dependência Azure VM para o Linux pode ser executada contra os sistemas operativos suportados listados na secção de [sistemas operativos suportados](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) do artigo de implantação do Monitor Azure para VMs.

## <a name="extension-schema"></a>Esquema de extensão

O seguinte JSON mostra o esquema para a extensão do agente de dependência Azure VM em um VM Azure Linux. 

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

| Nome | Valor/Exemplo |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publicador | Microsoft.Azure.Monitoring.DependencyAgent |
| tipo | DependencyAgentLinux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Implementação de modelos

Pode implementar extensões Azure VM com modelos de Gestor de Recursos Azure. Pode utilizar o esquema JSON detalhado na secção anterior num modelo de Gestor de Recursos Azure para executar a extensão do agente de dependência Azure VM durante uma implementação do modelo de gestor de recursos Azure.

O JSON para uma extensão virtual da máquina pode ser aninhado dentro do recurso virtual da máquina. Ou, pode colocá-lo no nível raiz ou superior de um modelo JSON do Gestor de Recursos. A colocação do JSON afeta o valor do nome e do tipo de recursos. Para mais informações, consulte o nome e o [tipo de definição para os recursos infantis.](../../azure-resource-manager/templates/child-resource-name-type.md)

O exemplo que se segue pressupõe que a extensão do agente dependency está aninhada dentro do recurso virtual da máquina. Ao nidificar o recurso de extensão, o JSON é colocado no objeto `"resources": []` da máquina virtual.


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

## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

Pode utilizar o AZURE CLI para implantar a extensão VM do agente de dependência para uma máquina virtual existente.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolver Problemas

Os dados sobre o estado das extensões podem ser recuperados a partir do portal Azure e utilizando o Azure CLI. Para ver o estado de implantação das extensões para um dado VM, execute o seguinte comando utilizando o Azure CLI:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Resultado da execução de extensão é registado para o ficheiro seguinte:

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda em algum ponto deste artigo, contacte os especialistas da Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Ou pode arquivar um incidente de apoio azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**. Para obter informações sobre como utilizar o Suporte Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
