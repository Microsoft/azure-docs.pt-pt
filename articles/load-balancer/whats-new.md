---
title: Novidades no Azure Load Balancer
description: Saiba quais as novidades com o Azure Load Balancer, como as últimas notas de lançamento, problemas conhecidos, correções de bugs, funcionalidades preprecadas e próximas alterações.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: a30a42e8a8c4049b53274da512089dd29965e775
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573158"
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
| Funcionalidade | Apoio a movimentos através de grupos de recursos | Balanceador de Carga Padrão e suporte IP público padrão para [movimentos de grupo de recursos](https://azure.microsoft.com/updates/standard-resource-group-move/). | Outubro de 2020 |
| Funcionalidade | Suporte para gestão de pool de backend baseada em IP (Preview) | O Azure Load Balancer suporta a adição e remoção de recursos de um pool de backend através de endereços IPv4 ou IPv6. Isto permite uma gestão fácil de contentores, máquinas virtuais e conjuntos de balanças de máquinas virtuais associados ao Balancer de Carga. Também permitirá que os endereços IP sejam reservados como parte de um pool de backend antes da criação dos recursos associados. Saiba mais [aqui](backend-pool-management.md)|Julho de 2020 |
| Funcionalidade| Insights do balanceador de carga Azure usando monitor Azure | Construído como parte do Azure Monitor for Networks, os clientes passam a ter mapas topológicos para todas as suas configurações de Balancer de Carga e dashboards de saúde para os seus Balanceadores de Carga Padrão pré-configurados com métricas no portal Azure. [Começar e aprender mais](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | Junho de 2020 |
| Validação | Adição de validação para portas HA | Foi adicionada uma validação para garantir que as regras portuárias ha e as regras portuárias não HA só são configuráveis quando o IP flutuante está ativado. Anteriormente, esta configuração passaria, mas não funcionaria como pretendido. Não foi feita qualquer alteração à funcionalidade. Pode aprender mais [aqui](load-balancer-ha-ports-overview.md#limitations)| Junho de 2020 |
| Funcionalidade| Suporte IPv6 para Azure Load Balancer (geralmente disponível) | Pode ter endereços IPv6 como frontend para os seus Balançadores de Carga Azure. Saiba como [criar uma aplicação de pilha dupla aqui](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) |Abril de 2020|
| Funcionalidade| Resets TCP no tempo limite de marcha lenta (geralmente disponível)| Utilize resets TCP para criar um comportamento de aplicação mais previsível. [Saiba mais](load-balancer-tcp-reset.md)| Fevereiro de 2020 |

## <a name="known-issues"></a>Problemas conhecidos

O grupo de produtos está a trabalhar ativamente em resoluções para as seguintes questões conhecidas:

|Problema |Descrição  |Mitigação  |
| ---------- |---------|---------|
| Evento de alerta de balançador de carga e registos de estado da sonda de saúde | O registo não funciona para eventos de alerta de balanceadores de carga para balanceadores de carga básicos e padrão, nem registos de estado da sonda de saúde para balanceador de carga básico  | [Utilize o Azure Monitor para métricas multidimensionais para o seu Balancer de Carga Padrão](load-balancer-standard-diagnostics.md). O Azure Monitor fornece visualização para um rico conjunto de métricas multidimensionais que também podem ser exportadas como troncos. Pode alavancar o painel de métricas pré-configurado através da sub-lâmina insights do seu Balancer de Carga. Se utilizar o [upgrade](upgrade-basic-standard.md) do Balancer de Carga Básica para standard para monitorização de métricas de nível de produção.

  

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o Azure Load Balancer, [frequently asked questions](load-balancer-faqs.md)consulte [o que é o Equilibr de Carga Azure?](load-balancer-overview.md)
