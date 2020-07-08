---
title: Visão geral do controlo de manutenção para máquinas virtuais Azure usando o portal Azure
description: Saiba como controlar quando a manutenção é aplicada aos seus VMs Azure utilizando o Controlo de Manutenção.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 950f4cfda73b40db0de8ba035868573cda1a5017
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84675804"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>Gerir atualizações da plataforma com o Controlo de Manutenção 

Gerencie as atualizações da plataforma, que não requerem um reboot, utilizando o controlo de manutenção. O Azure atualiza frequentemente a sua infraestrutura para melhorar a fiabilidade, desempenho, segurança ou lançar novas funcionalidades. A maioria das atualizações são transparentes para os utilizadores. Algumas cargas de trabalho sensíveis, como jogos, streaming de mídia e transações financeiras, não podem tolerar mesmo alguns segundos de um congelamento de VM ou desconexão para manutenção. O controlo de manutenção dá-lhe a opção de aguardar as atualizações da plataforma e aplicá-las dentro de uma janela de 35 dias. 

O controlo de manutenção permite-lhe decidir quando aplicar atualizações aos seus VMs isolados e anfitriões dedicados ao Azure.

Com o controlo de manutenção, pode:
- Atualizações de lote num pacote de atualização.
- Aguarde até 35 dias para aplicar atualizações. 
- Automatizar as atualizações da plataforma para a sua janela de manutenção utilizando funções Azure.
- As configurações de manutenção funcionam em assinaturas e grupos de recursos. 

## <a name="limitations"></a>Limitações

- Os VMs devem estar num [hospedeiro dedicado,](./linux/dedicated-hosts.md)ou ser criados com um [tamanho VM isolado](./linux/isolation.md).
- Após 35 dias, uma atualização será automaticamente aplicada.
- O utilizador deve ter acesso **ao Contribuinte de Recursos.**

## <a name="management-options"></a>Opções de gestão

Pode criar e gerir configurações de manutenção utilizando qualquer uma das seguintes opções:

- [CLI do Azure](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Portal do Azure](maintenance-control-portal.md)

## <a name="next-steps"></a>Próximos passos

Para saber mais, consulte [Manutenção e atualizações.](maintenance-and-updates.md)
