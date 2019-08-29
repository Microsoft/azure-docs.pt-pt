---
title: Baixar o modelo para uma VM do Azure | Microsoft Docs
description: Baixar a VM modelo a para ajudar na automatização de implantações no modelo de implantação do Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 37e833e5ba2fcbc9fcfe0a27d5b607a5c6cdd55b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079538"
---
# <a name="download-the-template-for-a-vm"></a>Transferir o modelo para uma VM
Quando você cria uma VM no Azure usando o portal ou o PowerShell, um modelo do Resource Manager é criado automaticamente para você. Você pode usar esse modelo para duplicar rapidamente uma implantação. O modelo contém informações sobre todos os recursos em um grupo de recursos. Para uma máquina virtual, isso significa que o modelo contém tudo o que é criado para dar suporte à VM nesse grupo de recursos, incluindo os recursos de rede.

## <a name="download-the-template-using-the-portal"></a>Baixar o modelo usando o portal
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu à esquerda, selecione **máquinas virtuais**.
3. Selecione a máquina virtual na lista.
4. Selecione **Exportar modelo**.
5. Selecione **baixar** no menu na parte superior e salve o arquivo. zip em seu computador local.
6. Abra o arquivo. zip e extraia os arquivos para uma pasta. O arquivo. zip contém:
   
   * deploy.ps1
   * deploy.sh 
   * implanter. rb
   * DeploymentHelper.cs
   * parameters.json
   * template.json

O arquivo template. JSON é o modelo.

## <a name="download-the-template-using-powershell"></a>Baixar o modelo usando o PowerShell
Você também pode baixar o arquivo de modelo. JSON usando o cmdlet [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) . Você pode usar o `-path` parâmetro para fornecer o nome de arquivo e o caminho para o arquivos. JSON. Este exemplo mostra como baixar o modelo para o grupo de recursos chamado **MyResource** Group para a pasta **C:\users\public\downloads** em seu computador local.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre a implantação de recursos usando modelos, confira [explicação do modelo do Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

