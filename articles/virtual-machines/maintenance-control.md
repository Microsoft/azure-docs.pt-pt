---
title: Visão geral do controlo de manutenção das máquinas virtuais Azure utilizando o portal Azure
description: Aprenda a controlar quando a manutenção é aplicada aos seus VMs Azure utilizando o Controlo de Manutenção.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 4c5e30d0607db2d529ae41ebab6dc82e925ff2a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82139202"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>Gerir atualizações da plataforma com controlo de manutenção 

Gerencie as atualizações da plataforma, que não requerem um reboot, utilizando o controlo de manutenção. O Azure atualiza frequentemente a sua infraestrutura para melhorar a fiabilidade, desempenho, segurança ou lançar novas funcionalidades. A maioria das atualizações são transparentes para os utilizadores. Algumas cargas de trabalho sensíveis, como jogos, streaming de mídia e transações financeiras, não podem tolerar mesmo alguns segundos de um congelamento de VM ou desconexão para manutenção. O controlo de manutenção dá-lhe a opção de esperar nas atualizações da plataforma e aplicá-las dentro de uma janela de 35 dias. 

O controlo de manutenção permite-lhe decidir quando aplicar atualizações aos seus VMs isolados e anfitriões dedicados azure.

Com controlo de manutenção, pode:
- Atualizações do lote num pacote de atualização.
- Aguarde até 35 dias para aplicar atualizações. 
- Amate automaticamente as atualizações da plataforma para a sua janela de manutenção utilizando funções Azure.
- As configurações de manutenção funcionam em assinaturas e grupos de recursos. 

## <a name="limitations"></a>Limitações

- Os VMs devem estar num [hospedeiro dedicado,](./linux/dedicated-hosts.md)ou ser criados com um [tamanho VM isolado](./linux/isolation.md).
- Após 35 dias, será aplicada uma atualização automaticamente.
- O utilizador deve ter acesso ao Colaborador do **Recurso.**

## <a name="management-options"></a>Opções de gestão

Pode criar e gerir configurações de manutenção utilizando qualquer uma das seguintes opções:

- [CLI do Azure](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Portal do Azure](maintenance-control-portal.md)

## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte [Manutenção e atualizações.](maintenance-and-updates.md)
