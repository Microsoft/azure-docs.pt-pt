---
title: Visão geral do controlo de manutenção para máquinas virtuais Azure usando o portal Azure
description: Saiba como controlar quando a manutenção é aplicada aos seus VMs Azure utilizando o Controlo de Manutenção.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: c3e914b904b0f6f1d3a4fae6c43c81cdf4eae819
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87080017"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>Gerir atualizações da plataforma com o Controlo de Manutenção 

Gerencie as atualizações da plataforma, que não requerem um reboot, utilizando o controlo de manutenção. O Azure atualiza frequentemente a sua infraestrutura para melhorar a fiabilidade, desempenho, segurança ou lançar novas funcionalidades. A maioria das atualizações são transparentes para os utilizadores. Algumas cargas de trabalho sensíveis, como jogos, streaming de mídia e transações financeiras, não podem tolerar mesmo alguns segundos de um congelamento de VM ou desconexão para manutenção. O controlo de manutenção dá-lhe a opção de aguardar as atualizações da plataforma e aplicá-las dentro de uma janela de 35 dias. 

O controlo de manutenção permite-lhe decidir quando aplicar atualizações aos seus VMs isolados e anfitriões dedicados ao Azure.

Com o controlo de manutenção, pode:
- Atualizações de lote num pacote de atualização.
- Aguarde até 35 dias para aplicar atualizações. 
- Automatizar as atualizações da plataforma para a sua janela de manutenção utilizando [funções Azure](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).
- As configurações de manutenção funcionam em assinaturas e grupos de recursos. 

## <a name="limitations"></a>Limitações

- Os VMs devem estar num [hospedeiro dedicado,](./linux/dedicated-hosts.md)ou ser criados com um [tamanho VM isolado](isolation.md).
- Após 35 dias, uma atualização será automaticamente aplicada.
- O utilizador deve ter acesso **ao Contribuinte de Recursos.**

## <a name="management-options"></a>Opções de gestão

Pode criar e gerir configurações de manutenção utilizando qualquer uma das seguintes opções:

- [CLI do Azure](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Portal do Azure](maintenance-control-portal.md)

Para obter uma amostra de Funções Azure, consulte [Atualizações de Manutenção de Agendamento com Controlo de Manutenção e Funções Azure](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).

## <a name="next-steps"></a>Passos seguintes

Para saber mais, consulte [Manutenção e atualizações.](maintenance-and-updates.md)
