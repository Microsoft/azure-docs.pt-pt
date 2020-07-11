---
title: Falhas de extensão do Windows VM de resolução de problemas
description: Conheça as falhas de extensão do Azure Windows VM
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: gwallace
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: 2fa87e860d0f5f5117840b9e230e383cdd6aae7c
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187562"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Falhas de extensão Azure Windows VM de resolução de problemas
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Estado de extensão de visualização
Os modelos do Gestor de Recursos Azure podem ser executados a partir do Azure PowerShell. Uma vez executado o modelo, o estado de extensão pode ser visualizado a partir do Azure Resource Explorer ou das ferramentas da linha de comando.

Segue-se um exemplo:

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

Segue-se o resultado do exemplo:

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>Falhas na extensão de resolução de problemas
### <a name="rerun-the-extension-on-the-vm"></a>Reencauma a extensão no VM
Se estiver a executar scripts no VM utilizando a Extensão de Script Personalizado, pode por vezes encontrar um erro em que o VM foi criado com sucesso, mas o script falhou. Nestas condições, a forma recomendada de recuperar deste erro é remover a extensão e voltar a repetir o modelo.
Nota: No futuro, esta funcionalidade seria melhorada para remover a necessidade de desinstalar a extensão.

#### <a name="remove-the-extension-from-azure-powershell"></a>Remova a extensão do Azure PowerShell
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

Uma vez removida a extensão, o modelo pode ser re-executado para executar os scripts no VM.

