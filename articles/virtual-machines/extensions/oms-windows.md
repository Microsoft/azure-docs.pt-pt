---
title: Extensão da máquina virtual do Azure Log Analytics para Windows | Documentos da Microsoft
description: Implemente o agente do Log Analytics na máquina de virtual do Windows com uma extensão da máquina virtual.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: roiyz
ms.openlocfilehash: 02fa19cad56f1a31ad60c597df1b004710cb5b50
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452093"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Extensão da máquina virtual de análise de registo para Windows

Log Analytics proporciona capacidades de monitorização na cloud e recursos no local. A extensão de máquina virtual do agente do Log Analytics para Windows é publicada e suportada pela Microsoft. A extensão instala o agente Log Analytics em máquinas virtuais do Azure e inscreve máquinas virtuais para uma área de trabalho do Log Analytics existente. Este documento detalha as plataformas suportadas, configurações e opções de implementação para a extensão de máquina virtual do Log Analytics para Windows.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão de agente do Log Analytics para o Windows podem ser executado em Windows Server 2008 R2, 2012 e 2012 R2 e 2016 versões.

### <a name="azure-security-center"></a>Centro de Segurança do Azure

Automaticamente, o Centro de segurança do Azure Aprovisiona o agente Log Analytics e liga-o com a área de trabalho da análise de registo predefinido da subscrição do Azure. Se estiver a utilizar o Centro de segurança do Azure, não execute os passos neste documento. Se o fizer, substitui a área de trabalho configurada e quebra a ligação com o Centro de segurança do Azure.

### <a name="internet-connectivity"></a>Conectividade Internet
A extensão de agente do Log Analytics para Windows requer que a máquina virtual de destino está ligada à internet. 

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão de agente do Log Analytics. A extensão requer o Id da área de trabalho e a chave de área de trabalho da área de trabalho do Log Analytics de destino. Estes podem ser encontrados nas definições de área de trabalho no portal do Azure. Uma vez que a chave da área de trabalho deve ser tratada como dados confidenciais, devem ser armazenado numa configuração de definição protegido. Dados de definição de protegidos de extensão VM do Azure são encriptados e desencriptados apenas na máquina de virtual de destino. Tenha em atenção que **workspaceId** e **workspaceKey** diferenciam maiúsculas de minúsculas.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publicador | Microsoft.EnterpriseCloud.Monitoring |
| tipo | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (e.g)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (por exemplo) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\* O workspaceId denomina-se a consumerId na API de análise do registo.

## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na secção anterior pode ser utilizado num modelo do Azure Resource Manager para executar a extensão de agente do Log Analytics durante uma implementação de modelo do Azure Resource Manager. Um modelo de exemplo que inclui a extensão de VM de agente do Log Analytics pode ser encontrado no [Galeria de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

O JSON para uma extensão de máquina virtual pode ser aninhado dentro do recurso de máquina virtual ou colocado na raiz ou de nível superior de um modelo do Resource Manager JSON. A colocação do JSON afeta o valor do tipo e nome do recurso. Para obter mais informações, consulte [defina o nome e tipo para recursos subordinados](../../azure-resource-manager/resource-manager-templates-resources.md#child-resources). 

O exemplo a seguir supõe que a extensão do Log Analytics é aninhada dentro do recurso de máquina virtual. Quando aninhar o recurso de extensão, o JSON é colocado no `"resources": []` objeto da máquina virtual.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

Quando coloca a extensão de JSON na raiz do modelo, o nome do recurso inclui uma referência à máquina virtual principal e o tipo reflete a configuração aninhada. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>Implementação do PowerShell

O `Set-AzureRmVMExtension` comando pode ser utilizado para implementar a extensão de máquina virtual do Log Analytics Agent para uma máquina virtual existente. Antes de executar o comando, as configurações de públicas e privadas tem de ser armazenados numa tabela de hash do PowerShell. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzureRmVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Podem ser obtidos dados sobre o estado das implementações de extensão do portal do Azure e utilizando o módulo Azure PowerShell. Para ver o estado de implementação de extensões para uma determinada VM, execute o seguinte comando com o módulo Azure PowerShell.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Resultado da execução de extensão é registado para arquivos encontrados no diretório seguinte:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
