---
title: Reimplantar máquinas virtuais windows em Azure Microsoft Docs
description: Como reimplantar máquinas virtuais do Windows em Azure para mitigar problemas de ligação RDP.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 36af0eeb43fb209ed65f950576f2dc9e97ec3633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058630"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Reimplementar a máquina virtual do Windows no novo nó do Azure
Se tiver enfrentado dificuldades na resolução de problemas na ligação remote Desktop (RDP) ou no acesso à aplicação à máquina virtual Azure (VM) baseada no Windows, a reimplantação do VM pode ajudar. Quando reimplantar um VM, o Azure desligará o VM, deslocará o VM para um novo nó dentro da infraestrutura Azure, e depois volte a ligá-lo, mantendo todas as suas opções de configuração e recursos associados. Este artigo mostra-lhe como reimplantar um VM utilizando o Azure PowerShell ou o portal Azure.

> [!NOTE]
> Depois de reimplantar um VM, o disco temporário perde-se e os endereços IP dinâmicos associados à interface de rede virtual são atualizados. 


## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell
Certifique-se de que tem o mais recente Azure PowerShell 1.x instalado na sua máquina. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

O exemplo seguinte implanta `myVM` o VM `myResourceGroup`nomeado no grupo de recursos denominado:

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Passos seguintes
Se estiver a ter problemas de ligação ao seu VM, pode encontrar ajuda específica na resolução de [ligações RDP](troubleshoot-rdp-connection.md) ou em passos detalhados de resolução de [problemas do RDP](detailed-troubleshoot-rdp.md). Se não conseguir aceder a uma aplicação em execução no seu VM, também pode ler problemas de resolução de problemas de [aplicação.](../windows/troubleshoot-app-connection.md)

