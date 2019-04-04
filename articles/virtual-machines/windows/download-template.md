---
title: Transferir o modelo para uma VM do Azure | Documentos da Microsoft
description: Transferir o templatefor uma VM para ajudar a automatizar as implementações no modelo de implementação do Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 4ef0c09d2d2ea2ed06708342ab45abcaf149c23e
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58903119"
---
# <a name="download-the-template-for-a-vm"></a>Transferir o modelo para uma VM
Quando cria uma VM no Azure com o portal ou PowerShell, um modelo do Resource Manager é criado automaticamente para. Pode utilizar este modelo rapidamente duplicar uma implementação. O modelo contém informações sobre todos os recursos num grupo de recursos. Para uma máquina virtual, isso significa que o modelo contém tudo o que é criado para oferecer suporte a VM no grupo de recursos, incluindo os recursos de rede.

## <a name="download-the-template-using-the-portal"></a>Transferir o modelo com o portal
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Um menu da esquerda, selecione **máquinas virtuais**.
3. Selecione a máquina virtual na lista.
4. Selecione **exportar modelo**.
5. Selecione **transferir** no menu na parte superior e guarde o ficheiro. zip para o computador local.
6. Abra o ficheiro. zip e extraia os ficheiros para uma pasta. O ficheiro. zip contém:
   
   * deploy.ps1
   * deploy.sh 
   * deployer.RB
   * DeploymentHelper.cs
   * parameters.json
   * template.json

O ficheiro Template JSON é o modelo.

## <a name="download-the-template-using-powershell"></a>Transferir o modelo com o PowerShell
Também pode transferir o modelo. JSON ficheiro com o [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) cmdlet. Pode utilizar o `-path` parâmetro para fornecer o nome de ficheiro e o caminho para o ficheiro. JSON. Este exemplo mostra como transferir o modelo para o grupo de recursos com o nome **myResourceGroup** para o **C:\users\public\downloads** pasta no seu computador local.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre como implementar recursos com modelos, veja [instruções do modelo do Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

