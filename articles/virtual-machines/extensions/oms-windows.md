---
title: Azure Monitor extensão da máquina virtual para Windows | Microsoft Docs
description: Implante o agente de Log Analytics na máquina virtual do Windows usando uma extensão de máquina virtual.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2019
ms.author: roiyz
ms.openlocfilehash: 700d8c6ea1527598591aa4300a977f80085e04b0
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990371"
---
# <a name="azure-monitor-virtual-machine-extension-for-windows"></a>Extensão de máquina virtual Azure Monitor para Windows

Os logs de Azure Monitor fornecem recursos de monitoramento em ativos de nuvem e locais. A extensão da máquina virtual do agente de Log Analytics para Windows é publicada e tem suporte da Microsoft. A extensão instala o agente Log Analytics em máquinas virtuais do Azure e inscreve máquinas virtuais para uma área de trabalho do Log Analytics existente. Este documento detalha as plataformas, configurações e opções de implantação com suporte para a extensão da máquina virtual Azure Monitor para Windows.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

Para obter detalhes sobre os sistemas operacionais Windows com suporte, consulte o artigo [visão geral do agente de log Analytics](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) .

### <a name="agent-and-vm-extension-version"></a>Versão de agente e a extensão de VM
A tabela a seguir fornece um mapeamento da versão da extensão de VM do Windows Azure Monitor e do pacote de Log Analytics agente para cada versão. 

| Log Analytics versão do pacote do agente do Windows | Azure Monitor versão da extensão de VM do Windows | Data de lançamento | Notas de Versão |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18011 | 1.0.18011 | Julho de 2019 | <ul><li> Correções de bugs e melhorias de estabilização secundárias </li><li> Aumento de MaxExpressionDepth para 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Junho de 2019 | <ul><li> Correções de bugs e melhorias de estabilização secundárias </li><li> Capacidade adicional de desabilitar as credenciais padrão ao fazer a conexão proxy (suporte para WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) </li></ul>|
| 10.19.13515 | 1.0.13515 | Março de 2019 | <ul><li>Correções secundárias de estabilização </li></ul> |
| 10.19.10006 | n/d | Dec 2018 | <ul><li> Correções secundárias de estabilização </li></ul> | 
| 8.0.11136 | n/d | 2018 de setembro |  <ul><li> Suporte adicionado para detectar a alteração da ID de recurso na movimentação da VM </li><li> Adicionado suporte para relatar a ID de recurso ao usar a instalação sem extensão </li></ul>| 
| 8.0.11103 | n/d |  Abril de 2018 | |
| 8.0.11081 | 1.0.11081 | 2017 de novembro | | 
| 8.0.11072 | 1.0.11072 | 2017 de setembro | |
| 8.0.11049 | 1.0.11049 | Fevereiro de 2017 | |

### <a name="azure-security-center"></a>Centro de Segurança do Azure

A central de segurança do Azure provisiona automaticamente o agente de Log Analytics e o conecta com o espaço de trabalho de Log Analytics padrão da assinatura do Azure. Se estiver a utilizar o Centro de segurança do Azure, não execute os passos neste documento. Isso substitui o espaço de trabalho configurado e interrompe a conexão com a central de segurança do Azure.

### <a name="internet-connectivity"></a>Conectividade Internet
A extensão do agente de Log Analytics para Windows requer que a máquina virtual de destino esteja conectada à Internet. 

## <a name="extension-schema"></a>Esquema de extensão

O JSON a seguir mostra o esquema para a extensão do agente de Log Analytics. A extensão requer a ID do espaço de trabalho e a chave do espaço de trabalho do destino Log Analytics espaço de trabalho. Eles podem ser encontrados nas configurações do espaço de trabalho no portal do Azure. Uma vez que a chave da área de trabalho deve ser tratada como dados confidenciais, devem ser armazenado numa configuração de definição protegido. Dados de definição de protegidos de extensão VM do Azure são encriptados e desencriptados apenas na máquina de virtual de destino. Tenha em atenção que **workspaceId** e **workspaceKey** diferenciam maiúsculas de minúsculas.

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
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceid (por exemplo, *) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (por exemplo) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\*O workspaceid é chamado consumidorid na API do Log Analytics.

## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na seção anterior pode ser usado em um modelo de Azure Resource Manager para executar a extensão do agente de Log Analytics durante uma implantação de modelo de Azure Resource Manager. Um modelo de exemplo que inclui a extensão de VM do agente Log Analytics pode ser encontrado na [Galeria de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>O modelo não dá suporte à especificação de mais de uma ID de espaço de trabalho e da chave do espaço de trabalho quando você deseja configurar o agente para relatar para vários espaços de trabalho. Para configurar o agente para relatar para vários espaços de trabalho, consulte [adicionando ou removendo um espaço de trabalho](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace).  

O JSON para uma extensão de máquina virtual pode ser aninhado dentro do recurso de máquina virtual ou colocado no nível raiz ou superior de um modelo JSON do Gerenciador de recursos. O posicionamento do JSON afeta o valor do nome do recurso e do tipo. Para obter mais informações, consulte [defina o nome e tipo para recursos subordinados](../../azure-resource-manager/child-resource-name-type.md). 

O exemplo a seguir pressupõe que a extensão de Azure Monitor esteja aninhada dentro do recurso de máquina virtual. Quando aninhar o recurso de extensão, o JSON é colocado no `"resources": []` objeto da máquina virtual.


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

## <a name="powershell-deployment"></a>Implantação do PowerShell

O `Set-AzVMExtension` comando pode ser usado para implantar a extensão de máquina virtual do agente de log Analytics em uma máquina virtual existente. Antes de executar o comando, as configurações públicas e privadas precisam ser armazenadas em uma tabela de hash do PowerShell. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
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

Os dados sobre o estado das implantações de extensão podem ser recuperados do portal do Azure e usando o módulo Azure PowerShell. Para ver o estado de implantação das extensões de uma determinada VM, execute o comando a seguir usando o módulo Azure PowerShell.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A saída de execução de extensão é registrada em arquivos encontrados no seguinte diretório:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
