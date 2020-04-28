---
title: Falhas na extensão do Windows VM de resolução de problemas
description: Saiba mais sobre falhas na extensão do Azure Windows VM
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
ms.openlocfilehash: bc99a9c9e9ff985730ec97dbacd1d7c1de06a45e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74073659"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Falhas na extensão do Azure Windows VM
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Estado de extensão de visualização
Os modelos do Gestor de Recursos Azure podem ser executados a partir de Azure PowerShell. Uma vez executado o modelo, o estado de extensão pode ser visto a partir do Azure Resource Explorer ou das ferramentas de linha de comando.

Segue-se um exemplo:

Azure PowerShell:

      Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status

Segue-se o resultado do exemplo:

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
    }
  ]

## <a name="troubleshooting-extension-failures"></a>Falhas na extensão de resolução de problemas
### <a name="rerun-the-extension-on-the-vm"></a>Reexecutar a extensão do VM
Se estiver a executar scripts no VM utilizando a extensão personalizada do script, pode por vezes encontrar um erro em que o VM foi criado com sucesso, mas o script falhou. Nestas condições, a forma recomendada de recuperar deste erro é remover a extensão e repetir o modelo novamente.
Nota: No futuro, esta funcionalidade será melhorada para eliminar a necessidade de desinstalar a extensão.

#### <a name="remove-the-extension-from-azure-powershell"></a>Remova a extensão da Azure PowerShell
    Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Uma vez removida a extensão, o modelo pode ser reexecutado para executar os scripts no VM.

