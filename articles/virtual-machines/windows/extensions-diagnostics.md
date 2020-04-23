---
title: Extensão de diagnóstico azure para windows
description: Monitor Azure Windows VMs utilizando a extensão de diagnóstico azure
author: johnkemnetz
manager: ashwink
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.openlocfilehash: 1d38a3cac5525de6835bbb0f9873cbd0636d44a9
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869678"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Extensão de diagnóstico azure para VMs do Windows

## <a name="overview"></a>Descrição geral

A extensão VM Do Azure Diagnostics permite-lhe recolher dados de monitorização, tais como contadores de desempenho e registos de eventos, a partir do seu VM windows. Pode especificar granularmente quais os dados que pretende recolher e para onde pretende que os dados sejam recolhidos, como uma conta de Armazenamento Azure ou um Hub de Eventos Azure. Também pode utilizar estes dados para construir gráficos no portal Azure ou criar alertas métricos.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão de diagnóstico azure pode ser executada contra o Cliente Windows 10, Windows Server 2008 R2, 2012, 2012 R2 e 2016.

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão de diagnóstico azure requer que a máquina virtual alvo esteja ligada à internet. 

## <a name="extension-schema"></a>Esquema de extensão

[O esquema de extensão de diagnóstico sinuoso e os valores de propriedade do Azure São descritos neste documento.](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)

## <a name="template-deployment"></a>Implementação de modelos

As extensões VM azure podem ser implantadas com modelos de Gestor de Recursos Azure. O esquema JSON detalhado na secção anterior pode ser usado num modelo de Gestor de Recursos Azure para executar a extensão de Diagnóstico Azure durante uma implementação do modelo de gestor de recursos Azure. Consulte [a monitorização e diagnóstico de utilização com modelos de VM windows e Azure Resource Manager](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Implantação Azure CLI

O Azure CLI pode ser utilizado para implantar a extensão De diagnóstico azure a uma máquina virtual existente. Substitua as definições e configurações protegidas por JSON válido do esquema de extensão acima. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>Implementação powerShell

O `Set-AzVMDiagnosticsExtension` comando pode ser utilizado para adicionar a extensão de Diagnóstico Azure a uma máquina virtual existente. Consulte também [o PowerShell para ativar o Azure Diagnostics numa máquina virtual que executa o Windows](ps-extensions-diagnostics.md).

 


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

Os dados sobre o estado das extensões podem ser recuperados do portal Azure e utilizando o Azure CLI. Para ver o estado de implantação das extensões para um dado VM, execute o seguinte comando utilizando o Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Consulte este artigo](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md) para obter um guia de resolução de problemas mais abrangente para a extensão do Azure Diagnostics.

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos Seguintes
* [Saiba mais sobre a extensão de diagnóstico sinuoso](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [Reveja o esquema de extensão e as versões](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)
