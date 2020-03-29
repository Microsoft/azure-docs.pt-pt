---
title: Introdução à visão do grupo de segurança no Vigilante da Rede Azure [ Microsoft Docs
description: Esta página fornece uma visão geral da capacidade de visão de segurança do Observador de Rede
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: damendo
ms.openlocfilehash: 18619cc0f7a21a39d0802575c484c68c5fa1a624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840762"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Introdução a regras de segurança eficazes vistas no Observador da Rede Azure

Os grupos de segurança da rede estão associados a um nível de sub-rede ou a um nível NIC. Quando associado a um nível de sub-rede, aplica-se a todas as instâncias vm na subrede. As regras de segurança eficazes vêem que devolve todos os NSGs configurados e regras que estão associadas a nível NIC e subnet para uma máquina virtual que fornece informações sobre a configuração. Além disso, as regras de segurança efetivas são devolvidas para cada um dos NICs num VM. Utilizando a visão eficaz das regras de segurança, pode avaliar um VM para vulnerabilidades de rede, tais como portas abertas. Também pode validar se o seu Grupo de Segurança de Rede estiver a trabalhar como esperado com base numa [comparação entre as regras de segurança configuradas e aprovadas.](network-watcher-nsg-auditing-powershell.md)

Um caso de utilização mais alargado está na conformidade e auditoria de segurança. Pode definir um conjunto prescritivo de regras de segurança como um modelo de governação de segurança na sua organização. Uma auditoria periódica de conformidade pode ser implementada de forma programática comparando as regras prescritivas com as regras efetivas para cada um dos VMs da sua rede.

No portal são apresentadas regras para cada Interface de Rede e agruparadas por entrada vs saída. Isto fornece uma visão simples das regras aplicadas a uma máquina virtual. Um botão de descarregamento é fornecido para descarregar facilmente todas as regras de segurança, independentemente do separador num ficheiro CSV.

![visão de grupo de segurança][1]

As regras podem ser selecionadas e abre-se uma nova lâmina para mostrar ao Grupo de Segurança da Rede e prefixos de origem e destino. A partir desta lâmina pode navegar diretamente para o recurso do Grupo de Segurança da Rede.

![perfuração][2]

### <a name="next-steps"></a>Passos seguintes

Saiba como auditar as definições do Grupo de Segurança da Rede visitando [as definições](network-watcher-nsg-auditing-powershell.md) do Grupo de Segurança da Rede de Auditoria com a PowerShell

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









