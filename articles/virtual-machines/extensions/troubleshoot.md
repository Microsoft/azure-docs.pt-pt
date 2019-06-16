---
title: Resolução de problemas de falhas de extensões de VM do Windows | Documentos da Microsoft
description: Saiba mais sobre a resolução de problemas de falhas de extensões de VM do Windows Azure
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: cf53df30dfccb76a6f33621038ba7f031a69f6de
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62107249"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Resolução de problemas de falhas de extensões de VM do Windows Azure
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Visualizar o estado da extensão
Modelos Azure Resource Manager podem ser executados a partir do Azure PowerShell. Assim que o modelo é executado, o estado da extensão pode ser visualizado no Explorador de recursos do Azure ou as ferramentas de linha de comandos.

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

## <a name="troubleshooting-extension-failures"></a>Resolução de problemas de falhas de extensões
### <a name="rerun-the-extension-on-the-vm"></a>Executar novamente a extensão na VM
Se estiver a executar scripts na VM com a extensão de Script personalizado, por vezes, pode executar um erro em que a VM foi criada com êxito mas o script falhou. Nestas condições, a forma recomendada para recuperar deste erro é remover a extensão e volte a executar o modelo.
Nota: Futuramente, essa funcionalidade seria ser aprimorada para remover a necessidade de desinstalar a extensão.

#### <a name="remove-the-extension-from-azure-powershell"></a>Remova a extensão do Azure PowerShell
    Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Assim que a extensão foi removida, o modelo pode ser novamente executado para executar os scripts na VM.

