---
title: Log Analytics virtual machine extension for Windows (Extensão de máquina virtual do Log Analytics para Windows)
description: Implemente o agente Log Analytics na máquina virtual do Windows utilizando uma extensão de máquina virtual.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 06/26/2020
ms.openlocfilehash: 7757bd765bcb02782b6199f71c4a6e460b7b8143
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559023"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Log Analytics virtual machine extension for Windows (Extensão de máquina virtual do Log Analytics para Windows)

O Azure Monitor Logs fornece capacidades de monitorização através de ativos de nuvem e no local. A extensão da máquina virtual do agente Log Analytics para o Windows é publicada e suportada pela Microsoft. A extensão instala o agente Log Analytics em máquinas virtuais Azure e inscreve máquinas virtuais num espaço de trabalho log analytics existente. Este documento detalha as plataformas, configurações e opções de implementação suportadas para a extensão da máquina virtual Log Analytics para windows.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

Para obter mais informações sobre os sistemas operativos windows suportados, consulte o artigo [dos agentes do Azure Monitor.](../../azure-monitor/agents/agents-overview.md#supported-operating-systems)

### <a name="agent-and-vm-extension-version"></a>Versão de extensão de agente e VM
A tabela seguinte fornece um mapeamento da versão da extensão VM do Windows Log Analytics e do pacote de agente Log Analytics para cada lançamento. 

| Versão do pacote de agente do Windows do Log Analytics | Versão de extensão do Windows VM do Log Analytics | Data da versão: | Notas de Versão |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18053| 1.0.18053.0 | Outubro de 2020   | <ul><li>Novo agente Troubleshooter</li><li>Atualizações da forma como o agente lida com as alterações dos certificados aos serviços da Azure</li></ul> |
| 10.20.18040 | 1.0.18040.2 | Agosto de 2020   | <ul><li>Resolve um problema no Arco Azure</li></ul> |
| 10.20.18038 | 1.0.18038 | Abril de 2020   | <ul><li>Permite a conectividade sobre o Link Privado utilizando âmbitos de ligação privados do Monitor Azure</li><li>Adiciona estrangulamento de ingestão para evitar um súbito e acidental afluxo na ingestão para um espaço de trabalho</li><li>Adiciona apoio a nuvens e regiões adicionais do Governo de Azure</li><li>Resolve um inseto onde HealthService.exe se despenhou</li></ul> |
| 10.20.18029 | 1.0.18029 | Março de 2020   | <ul><li>Adiciona suporte de assinatura de código SHA-2</li><li>Melhora a instalação e gestão da extensão VM</li><li>Resolve um bug em Azure Arc para integração de servidores</li><li>Adiciona uma ferramenta incorporada de resolução de problemas para suporte ao cliente</li><li>Acrescenta apoio a regiões adicionais do Governo de Azure</li> |
| 10.20.18018 | 1.0.18018 | Outubro de 2019 | <ul><li> Pequenas correções de insetos e melhorias na estabilização </li></ul> |
| 10.20.18011 | 1.0.18011 | Julho de 2019 | <ul><li> Pequenas correções de insetos e melhorias na estabilização </li><li> Aumento do MaxExpressionDepth para 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Junho de 2019 | <ul><li> Pequenas correções de insetos e melhorias na estabilização </li><li> Capacidade adicional de desativar credenciais predefinidos ao fazer ligação proxy (suporte para WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH) </li></ul>|
| 10.19.13515 | 1.0.13515 | Março de 2019 | <ul><li>Pequenas correções de estabilização </li></ul> |
| 10.19.10006 | n/a | Dez 2018 | <ul><li> Pequenas correções de estabilização </li></ul> | 
| 8.0.11136 | n/a | Setembro 2018 |  <ul><li> Suporte adicional para detetar a alteração do ID de recursos no movimento VM </li><li> Suporte adicionado para reportar iD de recursos ao utilizar a instalação de não extensão </li></ul>| 
| 8.0.11103 | n/a |  Abril de 2018 | |
| 8.0.11081 | 1.0.11081 | Nov 2017 | | 
| 8.0.11072 | 1.0.11072 | Setembro 2017 | |
| 8.0.11049 | 1.0.11049 | Fev 2017 | |


### <a name="azure-security-center"></a>Centro de Segurança do Azure

O Azure Security Center fornece automaticamente o agente Log Analytics e liga-o ao espaço de trabalho padrão do Log Analytics da subscrição Azure. Se estiver a utilizar o Centro de Segurança Azure, não passe pelos passos deste documento. Ao fazê-lo, substitui o espaço de trabalho configurado e quebra a ligação com o Centro de Segurança Azure.

### <a name="internet-connectivity"></a>Conectividade Internet
A extensão do agente Log Analytics para o Windows requer que a máquina virtual alvo esteja ligada à internet. 

## <a name="extension-schema"></a>Esquema de extensão

O JSON seguinte mostra o esquema para a extensão do agente Log Analytics. A extensão requer o ID do espaço de trabalho e a chave do espaço de trabalho a partir do espaço de trabalho target Log Analytics. Estas podem ser encontradas nas configurações para o espaço de trabalho no portal Azure. Uma vez que a chave do espaço de trabalho deve ser tratada como dados sensíveis, deve ser armazenada numa configuração de definição protegida. Os dados de definição protegidos por extensão Azure VM são encriptados e apenas desencriptados na máquina virtual alvo. Note que **o workspaceId** e **o workspaceKey** são sensíveis ao caso.

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
### <a name="property-values"></a>Valores patrimoniais

| Name | Valor / Exemplo |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publicador | Microsoft.EnterpriseCloud.Monitoring |
| tipo | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (por exemplo)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (por exemplo) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRsGo9TXffbrTahyrwv35W0pOqQAUUQ== |

\* O workspaceId é chamado de consumerId na API log Analytics.

> [!NOTE]
> Para obter propriedades adicionais consulte Azure [Connect Windows Computers to Azure Monitor](../../azure-monitor/agents/agent-windows.md).

## <a name="template-deployment"></a>Implementação de modelos

As extensões Azure VM podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na secção anterior pode ser usado num modelo de Gestor de Recursos Azure para executar a extensão do agente Log Analytics durante uma implementação do modelo do Azure Resource Manager. Um modelo de amostra que inclui a extensão VM do agente Log Analytics pode ser encontrado na [Galeria Azure Quickstart](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>O modelo não suporta especificar mais do que uma chave de ID de espaço de trabalho e espaço de trabalho quando pretende configurar o agente para reportar a vários espaços de trabalho. Para configurar o agente para reportar a vários espaços de trabalho, consulte [adicionar ou remover um espaço de trabalho](../../azure-monitor/agents/agent-manage.md#adding-or-removing-a-workspace).  

O JSON para uma extensão de máquina virtual pode ser aninhado dentro do recurso de máquina virtual, ou colocado no nível raiz ou superior de um modelo JSON do Gestor de Recursos. A colocação do JSON afeta o valor do nome e do tipo de recurso. Para obter mais informações, consulte [o nome definido e o tipo para obter recursos para crianças.](../../azure-resource-manager/templates/child-resource-name-type.md) 

O exemplo a seguir pressupõe que a extensão Log Analytics está aninhada dentro do recurso da máquina virtual. Ao nidificar o recurso de extensão, o JSON é colocado no `"resources": []` objeto da máquina virtual.


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

Ao colocar a extensão JSON na raiz do modelo, o nome do recurso inclui uma referência à máquina virtual do progenitor, e o tipo reflete a configuração aninhada. 

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

O `Set-AzVMExtension` comando pode ser utilizado para implantar a extensão da máquina virtual do agente Log Analytics a uma máquina virtual existente. Antes de executar o comando, as configurações públicas e privadas precisam de ser armazenadas numa tabela de haxixe PowerShell. 

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

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das extensões podem ser recuperados a partir do portal Azure e utilizando o módulo Azure PowerShell. Para ver o estado de implantação das extensões para um determinado VM, executar o seguinte comando utilizando o módulo Azure PowerShell.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A saída de execução de extensão é registada em ficheiros encontrados no seguinte diretório:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
