---
title: Descarregue o modelo para um Azure VM
description: Descarregue o modelo para um VM utilizando o portal ou PowerShell.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: af6905f0ba62a9053e44134348721312ade6b9d7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82085387"
---
# <a name="download-the-template-for-a-vm"></a>Transferir o modelo para uma VM
Quando cria um VM em Azure utilizando o portal ou o PowerShell, um modelo de Gestor de Recursos é automaticamente criado para si. Pode utilizar este modelo para duplicar rapidamente uma implementação. O modelo contém informações sobre todos os recursos de um grupo de recursos. Para uma máquina virtual, isto significa que o modelo contém tudo o que é criado em suporte ao VM nesse grupo de recursos, incluindo os recursos de networking.

## <a name="download-the-template-using-the-portal"></a>Descarregue o modelo usando o portal
1. Faça login no [portal Azure](https://portal.azure.com/).
2. Um menu à esquerda, selecione **Máquinas Virtuais.**
3. Selecione a máquina virtual na lista.
4. Selecione **o modelo de exportação.**
5. Selecione **Descarregue** no menu superior e guarde o ficheiro .zip para o computador local.
6. Abra o ficheiro .zip e extrate os ficheiros para uma pasta. O ficheiro .zip contém:
   
   * parameters.jsem
   * template.jsem

O template.jsno ficheiro é o modelo.

## <a name="download-the-template-using-powershell"></a>Descarregue o modelo usando PowerShell
Também pode descarregar o ficheiro de modelo .json utilizando o cmdlet [Export-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) Pode utilizar o `-path` parâmetro para fornecer o nome de ficheiro e o caminho para o ficheiro .json. Este exemplo mostra como descarregar o modelo para o grupo de recursos denominado **myResourceGroup** para a pasta **C:\users\public\downloads** no seu computador local.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a implementação de recursos usando modelos, consulte [o modelo do Gestor de Recursos através do walkthrough](../../azure-resource-manager/resource-manager-template-walkthrough.md).

