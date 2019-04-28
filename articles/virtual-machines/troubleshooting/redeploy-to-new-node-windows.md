---
title: Voltar a implementar máquinas de virtuais do Windows no Azure | Documentos da Microsoft
description: Como voltar a implementar máquinas de virtuais do Windows no Azure para atenuar problemas de ligação de RDP.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: b71ca93ac3e3e6c77c5f87b4859a2e3e0e1040d9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104062"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Reimplementar a máquina de virtual do Windows para o novo nó do Azure
Se o ter sido enfrentando dificuldades resolução de problemas de ambiente de trabalho remoto (RDP) ligação ou a aplicação de acesso baseado em Windows máquinas virtuais do Azure (VM), reimplementação da VM pode ajudar. Quando voltar a implementar uma VM, Azure vai encerrar a VM, mova a VM para um novo nó dentro da infraestrutura do Azure e, em seguida, ligue-o novamente, mantendo a todas as suas opções de configuração e os recursos associados. Este artigo mostra-lhe como voltar a implementar uma VM com o Azure PowerShell ou o portal do Azure.

> [!NOTE]
> Depois de Reimplementar uma VM, o disco temporário é perdido e endereços IP dinâmicos associados à interface de rede virtual são atualizados. 


## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell
Certifique-se de que tem o Azure PowerShell mais recente 1.x instalado no seu computador. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

O exemplo seguinte implementa a VM com o nome `myVM` no grupo de recursos com o nome `myResourceGroup`:

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Passos Seguintes
Se estiver a ter problemas de ligação à sua VM, pode encontrar ajuda específica sobre [resolução de problemas de ligações de RDP](troubleshoot-rdp-connection.md) ou [detalhadas passos de resolução de problemas de RDP](detailed-troubleshoot-rdp.md). Se não conseguir aceder uma aplicação em execução na sua VM, pode ainda ler [resolução de problemas de aplicação](../windows/troubleshoot-app-connection.md).

