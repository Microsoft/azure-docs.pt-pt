---
title: Extensão de Diagnóstico do Azure para Windows
description: Monitorar VMs do Windows do Azure usando a extensão Diagnóstico do Azure
services: virtual-machines-windows
documentationcenter: ''
author: johnkemnetz
manager: ashwink
editor: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.openlocfilehash: 3fcdcd4565ed0512b3efcf9b23ca349a3bbf3a6c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033483"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Extensão de Diagnóstico do Azure para VMs do Windows

## <a name="overview"></a>Descrição geral

A extensão de VM Diagnóstico do Azure permite coletar dados de monitoramento, como contadores de desempenho e logs de eventos, da sua VM do Windows. Você pode especificar detalhadamente quais dados deseja coletar e onde deseja que os dados sejam acessados, como uma conta de armazenamento do Azure ou um hub de eventos do Azure. Você também pode usar esses dados para criar gráficos no portal do Azure ou criar alertas de métrica.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão de Diagnóstico do Azure pode ser executada em relação ao cliente Windows 10, Windows Server 2008 R2, 2012, 2012 R2 e 2016.

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão Diagnóstico do Azure requer que a máquina virtual de destino esteja conectada à Internet. 

## <a name="extension-schema"></a>Esquema de extensão

[O esquema de extensão Diagnóstico do Azure e os valores de propriedade são descritos neste documento.](../../azure-monitor/platform/diagnostics-extension-schema-1dot3.md)

## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na seção anterior pode ser usado em um modelo de Azure Resource Manager para executar a extensão de Diagnóstico do Azure durante uma implantação de modelo de Azure Resource Manager. Consulte [usar monitoramento e diagnóstico com uma VM do Windows e modelos de Azure Resource Manager](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

O CLI do Azure pode ser usado para implantar a extensão de Diagnóstico do Azure em uma máquina virtual existente. Substitua as configurações protegidas e as propriedades de configurações por JSON válido do esquema de extensão acima. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>Implantação do PowerShell

O comando `Set-AzVMDiagnosticsExtension` pode ser usado para adicionar a extensão de Diagnóstico do Azure a uma máquina virtual existente. Consulte também [usar o PowerShell para habilitar diagnóstico do Azure em uma máquina virtual que executa o Windows](ps-extensions-diagnostics.md).

 


```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Podem ser obtidos dados sobre o estado das implementações de extensão do portal do Azure e com a CLI do Azure. Para ver o estado de implementação de extensões para uma determinada VM, execute o seguinte comando com a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Consulte este artigo](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md) para obter um guia de solução de problemas mais abrangente para a extensão de diagnóstico do Azure.

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos Seguintes
* [Saiba mais sobre a extensão de Diagnóstico do Azure](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [Examinar o esquema de extensão e as versões](../../azure-monitor/platform/diagnostics-extension-schema.md)
