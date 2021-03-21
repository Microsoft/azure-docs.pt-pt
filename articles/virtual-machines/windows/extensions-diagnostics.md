---
title: Extensão de diagnóstico Azure para Windows
description: Monitor Azure Windows VMs usando a extensão de diagnóstico azure
author: johnkemnetz
manager: ashwink
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e703a8f91f18467660ef7e1b91fdb034feeb00b1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549537"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Extensão de diagnóstico Azure para VMs windows

## <a name="overview"></a>Descrição geral

A extensão VM do Azure Diagnostics permite-lhe recolher dados de monitorização, tais como contadores de desempenho e registos de eventos, a partir do seu VM do Windows. Pode especificar granulitivamente quais os dados que pretende recolher e para onde pretende que os dados possam ir, como uma conta de Armazenamento Azure ou um Azure Event Hub. Também pode utilizar estes dados para construir gráficos no portal Azure ou criar alertas métricos.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão de diagnóstico Azure pode ser executada contra o Cliente Windows 10, Windows Server 2008 R2, 2012, 2012 R2 e 2016.

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão de diagnóstico Azure requer que a máquina virtual alvo esteja ligada à internet. 

## <a name="extension-schema"></a>Esquema de extensão

[O esquema de extensão de diagnóstico azure e os valores de propriedade são descritos neste documento.](../../azure-monitor/agents/diagnostics-extension-schema-windows.md)

## <a name="template-deployment"></a>Implementação de modelos

As extensões Azure VM podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na secção anterior pode ser usado num modelo de Gestor de Recursos Azure para executar a extensão Azure Diagnostics durante uma implementação do modelo do Azure Resource Manager. Consulte [a monitorização e diagnósticos de utilização com modelos de Gestor de Recursos Windows VM e Azure](../extensions/diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Implantação do Azure CLI

O CLI Azure pode ser usado para implantar a extensão Azure Diagnostics a uma máquina virtual existente. Substitua as definições e as propriedades de configurações protegidas por JSON válido do esquema de extensão acima. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>Implementação do PowerShell

O `Set-AzVMDiagnosticsExtension` comando pode ser utilizado para adicionar a extensão Azure Diagnostics a uma máquina virtual existente. Consulte também [o PowerShell para ativar o Azure Diagnostics numa máquina virtual que executa o Windows](../extensions/diagnostics-windows.md).

 


```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das extensões podem ser recuperados a partir do portal Azure e utilizando o CLI Azure. Para ver o estado de implantação das extensões para um determinado VM, executar o seguinte comando utilizando o Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Consulte este artigo](../../azure-monitor/agents/diagnostics-extension-troubleshooting.md) para obter um guia de resolução de problemas mais abrangente para a extensão Azure Diagnostics.

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos Seguintes
* [Saiba mais sobre a Extensão de Diagnóstico Azure](../../azure-monitor/agents/diagnostics-extension-overview.md)
* [Rever o esquema de extensão e versões](../../azure-monitor/agents/diagnostics-extension-schema-windows.md)
