---
title: Extensão de Diagnóstico do Azure para Windows | Microsoft Docs
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
ms.openlocfilehash: 4230e2aac8d386c759a403b9008029d68049569c
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749406"
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

As extensões de VM do Azure podem ser implantadas com modelos de Azure Resource Manager. O esquema JSON detalhado na seção anterior pode ser usado em um modelo de Azure Resource Manager para executar a extensão de Diagnóstico do Azure durante uma implantação de modelo de Azure Resource Manager. Consulte [usar monitoramento e diagnóstico com uma VM do Windows e modelos de Azure Resource Manager](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Implantação de CLI do Azure

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

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das implantações de extensão podem ser recuperados do portal do Azure e usando o CLI do Azure. Para ver o estado de implantação das extensões de uma determinada VM, execute o comando a seguir usando o CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Consulte este artigo](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md) para obter um guia de solução de problemas mais abrangente para a extensão de diagnóstico do Azure.

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, você poderá entrar em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow do MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione obter suporte. Para obter informações sobre como usar o suporte do Azure, leia as [perguntas frequentes sobre suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Próximos Passos
* [Saiba mais sobre a extensão de Diagnóstico do Azure](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [Examinar o esquema de extensão e as versões](../../azure-monitor/platform/diagnostics-extension-schema.md)
