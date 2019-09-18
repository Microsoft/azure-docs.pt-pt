---
title: Reimplantar máquinas virtuais do Windows no Azure | Microsoft Docs
description: Como reimplantar máquinas virtuais do Windows no Azure para atenuar problemas de conexão de RDP.
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
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058630"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Reimplantar a máquina virtual do Windows no novo nó do Azure
Se você estiver enfrentando dificuldades para solucionar problemas de conexão Área de Trabalho Remota (RDP) ou acesso de aplicativo à VM (máquina virtual) do Azure baseada no Windows, a reimplantação da VM poderá ajudar. Quando você reimplanta uma VM, o Azure desligará a VM, moverá a VM para um novo nó na infraestrutura do Azure e, em seguida, a ligará, retendoá todas as opções de configuração e os recursos associados. Este artigo mostra como reimplantar uma VM usando Azure PowerShell ou o portal do Azure.

> [!NOTE]
> Depois de reimplantar uma VM, o disco temporário é perdido e os endereços IP dinâmicos associados à interface de rede virtual são atualizados. 


## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell
Verifique se você tem o Azure PowerShell 1. x mais recente instalado em seu computador. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

O exemplo a seguir implanta a VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`:

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Passos seguintes
Se você estiver tendo problemas para se conectar à sua VM, poderá encontrar ajuda específica sobre [como solucionar problemas de conexões RDP](troubleshoot-rdp-connection.md) ou [etapas detalhadas de solução de problemas de RDP](detailed-troubleshoot-rdp.md). Se você não puder acessar um aplicativo em execução em sua VM, você também poderá ler [problemas de solução](../windows/troubleshoot-app-connection.md)de problemas de aplicativo.

