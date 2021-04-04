---
title: Visão geral do controlo de manutenção para upgrades de imagem de SO em conjuntos de escala de máquina virtual Azure
description: Aprenda a controlar quando as atualizações automáticas de imagem do SO forem lançadas para os conjuntos de escala de máquina virtual Azure utilizando o controlo de manutenção.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: 55cbc5db534dd1b05f91a24e0104b1f2dc110547
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90532707"
---
# <a name="preview-maintenance-control-for-azure-virtual-machine-scale-sets"></a>Pré-visualização: Controlo de manutenção para conjuntos de balanças de máquinas virtuais Azure 

Gerencie [as atualizações automáticas de imagens DE PARA](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) os conjuntos de escala de máquina virtual utilizando o controlo de manutenção.

O controlo de manutenção permite-lhe decidir quando aplicar atualizações aos discos DE nos seus conjuntos de escala de máquina virtual através de uma experiência mais fácil e previsível. 

As configurações de manutenção funcionam em assinaturas e grupos de recursos.

Todo o fluxo de trabalho se resume a estes passos: 
- Criar uma configuração de manutenção.
- Associar uma balança de máquina virtual definida a uma configuração de manutenção.
- Ativar as atualizações automáticas de SO.

> [!IMPORTANT]
> O controlo de manutenção para atualizações de imagem DO em conjuntos de escala de máquina virtual Azure está atualmente em Visualização Pública.
> Esta versão de pré-visualização é fornecida sem um contrato de nível de serviço, e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="limitations"></a>Limitações

- Os VMs devem estar num conjunto de escala.
- O utilizador deve ter acesso **ao Contribuinte de Recursos.**
- A duração da manutenção deve ser de 5 horas ou mais na configuração da manutenção.
- A recorrência da manutenção deve ser definida como 'Dia' na configuração da manutenção


## <a name="management-options"></a>Opções de gestão

Pode criar e gerir configurações de manutenção utilizando qualquer uma das seguintes opções:

- [Azure PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)


## <a name="next-steps"></a>Passos seguintes

Saiba como controlar quando a manutenção for aplicada aos conjuntos de balanças de máquinas virtuais Azure utilizando o controlo de manutenção e o PowerShell.

> [!div class="nextstepaction"]
> [Controlo de manutenção de conjunto de escala de máquina virtual utilizando o PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)
