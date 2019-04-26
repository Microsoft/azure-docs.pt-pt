---
title: Extensão de diagnóstico do Azure para Windows | Documentos da Microsoft
description: Monitorizar VMs do Windows Azure utilizando a extensão de diagnóstico do Azure
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
ms.openlocfilehash: 58c520ecbaf764140748167e458c301ab56de375
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232869"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Extensão de diagnóstico do Azure para VMs do Windows

## <a name="overview"></a>Descrição geral

A extensão de VM de diagnóstico do Azure permite-lhe recolher dados de monitorização, como contadores de desempenho e registos de eventos, de sua VM do Windows. Forma granular, pode especificar quais os dados que pretende recolher e onde pretende que os dados adequada, como uma conta de armazenamento do Azure ou de um Hub de eventos do Azure. Também pode utilizar estes dados para criar gráficos no portal do Azure ou criar alertas de métricas.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão de diagnóstico do Azure podem ser executada para o cliente do Windows 10, Windows Server 2008 R2, 2012 e 2012 R2 e 2016.

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão de diagnóstico do Azure requer que a máquina virtual de destino está ligada à internet. 

## <a name="extension-schema"></a>Esquema de extensão

[Os valores de esquema e a propriedade de extensão de diagnóstico do Azure são descritos neste documento.](../../azure-monitor/platform/diagnostics-extension-schema-1dot3.md)

## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na secção anterior pode ser utilizado num modelo do Azure Resource Manager para executar a extensão de diagnóstico do Azure durante uma implementação de modelo do Azure Resource Manager. Ver [monitorização de utilização e diagnóstico com modelos de VM do Windows e do Azure Resource Manager](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

A CLI do Azure pode ser utilizada para implementar a extensão de diagnóstico do Azure para uma máquina virtual existente. Substitua as definições protegidas e propriedades de definições JSON válido do esquema de extensão acima. 

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

O `Set-AzVMDiagnosticsExtension` comando pode ser utilizado para adicionar a extensão de diagnóstico do Azure para uma máquina virtual existente. Consulte também [utilize o PowerShell para ativar o diagnóstico do Azure numa máquina virtual a executar o Windows](ps-extensions-diagnostics.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


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

[Veja este artigo](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md) para obter um guia de resolução de problemas mais abrangente para a extensão de diagnóstico do Azure.

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Próximos Passos
* [Saiba mais sobre a extensão de diagnóstico do Azure](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [Reveja o esquema da extensão e as versões](../../azure-monitor/platform/diagnostics-extension-schema.md)
