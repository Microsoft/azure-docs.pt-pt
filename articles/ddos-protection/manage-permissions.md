---
title: Permissões do Plano de Proteção Azure DDoS
description: Saiba como gerir a permissão num plano de proteção.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 10b10309e438bab3c99cb2ed7202eaa272d24abe
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905439"
---
# <a name="manage-ddos-protection-plans-permissions-and-restrictions"></a>Gerir planos de proteção do DDoS: permissões e restrições

Um plano de proteção DDoS funciona em regiões e subscrições. O mesmo plano pode ser ligado a redes virtuais de outras subscrições em diferentes regiões, através do seu inquilino. A subscrição do plano está associada a incorrer na fatura mensal recorrente do plano, bem como taxas de sobrecarga, caso o número de endereços IP públicos protegidos exceda 100. Para obter mais informações sobre os preços do DDoS, consulte [os detalhes dos preços.](https://azure.microsoft.com/pricing/details/ddos-protection/)

## <a name="prerequisites"></a>Pré-requisitos

- Antes de poder completar os passos neste tutorial, tem primeiro de criar um [plano de proteção Azure DDoS Standard](manage-ddos-protection.md).

## <a name="permissions"></a>Permissões

Para trabalhar com planos de proteção DDoS, a sua conta deve ser atribuída à [função de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) de rede ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que seja atribuída às ações apropriadas listadas no quadro seguinte:

| Ação                                            | Nome                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Leia um plano de proteção DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Criar ou atualizar um plano de proteção DDoS  |
| Microsoft.Network/ddosProtectionPlans/delete      | Eliminar um plano de proteção DDoS            |
| Microsoft.Network/ddosProtectionPlans/join/action | Aderir a um plano de proteção DDoS              |

Para permitir a proteção do DDoS para uma rede virtual, a sua conta também deve ser atribuída [às ações apropriadas para redes virtuais.](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network#permissions)

## <a name="azure-policy"></a>Azure Policy

A criação de mais do que um plano não é necessária para a maioria das organizações. Um plano não pode ser movido entre subscrições. Se quiser alterar a subscrição em que se encontra um plano, tem de eliminar o plano existente e criar um novo.

Para clientes que tenham várias subscrições, e que queiram garantir que um único plano seja implementado em todo o seu inquilino para controlo de custos, você pode usar a Azure Policy para restringir a [criação de planos Azure DDoS Protection Standard](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Restrict%20creation%20of%20Azure%20DDoS%20Protection%20Standard%20Plans%20with%20Azure%20Policy). Esta política bloqueará a criação de quaisquer planos DDoS, a menos que a subscrição tenha sido previamente marcada como uma exceção. Esta política também mostrará uma lista de todas as subscrições que têm um plano DDoS implementado mas não devem, marcando-as como fora do cumprimento.


## <a name="next-steps"></a>Próximas etapas

Para aprender a visualizar e configurar a telemetria para o seu plano de proteção DDoS, continue para os tutoriais.

> [!div class="nextstepaction"]
> [Ver e configurar telemetria de proteção DDoS](telemetry-monitoring-alerting.md)
