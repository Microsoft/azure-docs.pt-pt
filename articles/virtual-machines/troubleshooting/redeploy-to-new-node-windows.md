---
title: Reimplantar máquinas virtuais do Windows em Azure Microsoft Docs
description: Como recolocar máquinas virtuais do Windows em Azure para mitigar problemas de conexão RDP.
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
ms.openlocfilehash: afbea39a080e1dd768a14d6e0eacda1bad23c5a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074421"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Reimplementar a máquina virtual do Windows no novo nó do Azure
Se tiver tido dificuldades em resolver problemas de resolução da ligação de Ambiente de Trabalho Remoto (RDP) ou acesso de aplicações à máquina virtual Azure (VM) baseada no Windows, a recolocação do VM pode ajudar. Quando recolocar um VM, o Azure desligará o VM, move o VM para um novo nó dentro da infraestrutura Azure e, em seguida, liga-o de novo, mantendo todas as suas opções de configuração e recursos associados. Este artigo mostra-lhe como recolocar um VM utilizando a Azure PowerShell ou o portal Azure.

> [!NOTE]
> Depois de recolocar um VM, o disco temporário perde-se e os endereços IP dinâmicos associados à interface de rede virtual são atualizados. 


## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell
Certifique-se de que tem o mais recente Azure PowerShell 1.x instalado na sua máquina. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/).

O exemplo a seguir implementa o VM nomeado `myVM` no grupo de recursos `myResourceGroup` denominado:

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Passos seguintes
Se tiver problemas ligados ao seu VM, pode encontrar ajuda específica para [resolver as ligações RDP](troubleshoot-rdp-connection.md) ou para as [etapas detalhadas de resolução de problemas do RDP](detailed-troubleshoot-rdp.md). Se não conseguir aceder a uma aplicação em execução no seu VM, também pode ler [problemas de resolução de problemas de aplicação](./troubleshoot-app-connection.md).
