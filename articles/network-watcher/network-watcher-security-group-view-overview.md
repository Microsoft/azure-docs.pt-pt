---
title: Introdução à visão eficaz das regras de segurança na | do Observador da Rede Azure Microsoft Docs
description: Esta página fornece uma visão geral do Observador de Rede - Capacidade de visualização de regras de segurança eficazes
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
ms.openlocfilehash: 6e330d2595487366ff7b8979490cb8a897f89520
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503152"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Introdução à visão eficaz das regras de segurança no Observador da Rede Azure

Os grupos de segurança da rede estão associados a um nível de sub-rede ou a um nível NIC. Quando associado a um nível de sub-rede, aplica-se a todas as instâncias VM na sub-rede. A visão de regras de segurança eficazes devolve todos os NSGs configurados e regras que estão associados a um nível NIC e sub-rede para uma máquina virtual que fornece informações sobre a configuração. Além disso, as regras de segurança efetivas são devolvidas para cada um dos NICs num VM. Utilizando uma visão eficaz das regras de segurança, pode avaliar um VM para vulnerabilidades de rede, como portas abertas. Também pode validar se o seu Grupo de Segurança de Rede estiver a funcionar como esperado com base numa [comparação entre as regras de segurança configuradas e as regras de segurança aprovadas.](network-watcher-nsg-auditing-powershell.md)

Um caso de utilização mais alargado está na conformidade e auditoria de segurança. Pode definir um conjunto prescritivo de regras de segurança como um modelo de governação de segurança na sua organização. Uma auditoria periódica de conformidade pode ser implementada de forma programática, comparando as regras prescritivas com as regras efetivas para cada um dos VMs da sua rede.

Nas regras do portal são apresentadas para cada Interface de Rede e agrupadas por entrada vs saída. Isto proporciona uma visão simples das regras aplicadas a uma máquina virtual. Um botão de descarregamento é fornecido para descarregar facilmente todas as regras de segurança, independentemente do separador para um ficheiro CSV.

![vista de grupo de segurança][1]

As regras podem ser selecionadas e uma nova lâmina abre-se para mostrar o Grupo de Segurança da Rede e prefixos de origem e destino. A partir desta lâmina pode navegar diretamente para o recurso Network Security Group.

![perfuração][2]

### <a name="next-steps"></a>Passos seguintes

Também pode utilizar a funcionalidade *Grupos de Segurança Eficaz* através de outros métodos listados abaixo:
* [API REST](/rest/api/virtualnetwork/NetworkInterfaces/ListEffectiveNetworkSecurityGroups)
* [PowerShell](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)
* [CLI do Azure](/cli/azure/network/nic#az-network-nic-list-effective-nsg)

Saiba como auditar as definições do Grupo de Segurança de Rede visitando [as definições do Grupo de Segurança da Rede de Auditoria com o PowerShell](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png