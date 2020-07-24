---
title: Novidades no Azure Load Balancer
description: Saiba quais as novidades com o Azure Load Balancer, como as últimas notas de lançamento, problemas conhecidos, correções de bugs, funcionalidades preprecadas e próximas alterações.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: overview
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: 8b44dc230dbee1b29b9889a1b81e35ebe25f6b97
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87078687"
---
# <a name="whats-new-in-azure-load-balancer"></a>Quais as novidades no Azure Load Balancer?

O Azure Load Balancer é atualizado regularmente. Mantenha-se atualizado com os últimos anúncios. Este artigo fornece-lhe informações sobre:

- Os últimos lançamentos
- Problemas conhecidos
- Correções de erros
- Funcionalidade preprecada (se aplicável)

Também pode encontrar as últimas atualizações do Azure Load Balancer e subscrever o feed RSS [aqui.](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer)

## <a name="recent-releases"></a>Lançamentos recentes

| Tipo |Nome |Descrição  |Data adicionada  |
| ------ |---------|---------|---------|
| Funcionalidade | Suporte para gestão de pool de backend baseada em IP (Preview) | O Azure Load Balancer suporta a adição e remoção de recursos de um pool de backend através de endereços IPv4 ou IPv6. Isto permite uma gestão fácil de contentores, máquinas virtuais e conjuntos de balanças de máquinas virtuais associados ao Balancer de Carga. Também permitirá que os endereços IP sejam reservados como parte de um pool de backend antes da criação dos recursos associados. Saiba mais [aqui](backend-pool-management.md)|Julho de 2020 |
| Funcionalidade| Insights do balanceador de carga Azure usando monitor Azure | Construído como parte do Azure Monitor for Networks, os clientes passam a ter mapas topológicos para todas as suas configurações de Balancer de Carga e dashboards de saúde para os seus Balanceadores de Carga Padrão pré-configurados com métricas no portal Azure. [Começar e aprender mais](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | Junho de 2020 |
| Validação | Adição de validação para portas HA | Foi adicionada uma validação para garantir que as regras portuárias ha e as regras portuárias não HA só são configuráveis quando o IP flutuante está ativado. Anteriormente, esta configuração passaria, mas não funcionaria como pretendido. Não foi feita qualquer alteração à funcionalidade. Pode aprender mais [aqui](load-balancer-ha-ports-overview.md#limitations)| Junho de 2020 |
| Funcionalidade| Suporte IPv6 para Azure Load Balancer (geralmente disponível) | Pode ter endereços IPv6 como frontend para os seus Balançadores de Carga Azure. Saiba como [criar uma aplicação de pilha dupla aqui](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) |Abril de 2020|
| Funcionalidade| Resets TCP no tempo limite de marcha lenta (geralmente disponível)| Utilize resets TCP para criar um comportamento de aplicação mais previsível. [Saiba mais](load-balancer-tcp-reset.md)| Fevereiro de 2020 |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure Load Balancer, [frequently asked questions](load-balancer-faqs.md)consulte [o que é o Equilibr de Carga Azure?](load-balancer-overview.md)
