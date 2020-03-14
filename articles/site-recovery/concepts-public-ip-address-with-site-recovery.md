---
title: Atribuir endereços IP públicos após falha com recuperação do site azure
description: Descreve como configurar endereços IP públicos com a Recuperação do Site Azure e o Gestor de Tráfego azure para recuperação e migração de desastres
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: b1f3ffa6fc90fc0cab0217d1b71907342f2dbd0d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281954"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Configurar endereços IP públicos após a falha

Os endereços IP públicos permitem que os recursos da Internet comuniquem com os recursos do Azure à entrada. Os endereços IP públicos também permitem que os recursos do Azure comuniquem com a Internet à saída, bem como com serviços do Azure destinados ao público com um endereço IP atribuído ao recurso.
- Comunicação de entrada da Internet para o recurso, tais como Máquinas Virtuais Azure (VM), Gateways de Aplicação Azure, Equilíbrios de Carga Azure, Gateways VPN Azure, entre outros. Ainda pode comunicar com alguns recursos, como VMs, a partir da Internet, se um VM não tiver um endereço IP público atribuído a ele, desde que o VM faça parte de um pool de back-end do equilibrador de carga, e o equilibrador de carga seja atribuído a um endereço IP público.
- Conectividade de saída para a Internet usando um endereço IP previsível. Por exemplo, uma máquina virtual pode comunicar a saída para a Internet sem um endereço IP público atribuído à sua, mas o seu endereço é endereço de rede traduzido pelo Azure para um endereço público imprevisível, por padrão. Atribuir um endereço IP público a um recurso permite-lhe saber qual o endereço IP utilizado para a ligação de saída. Embora previsível, o endereço pode mudar, dependendo do método de atribuição escolhido. Para mais informações, consulte [Para obter informações](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)sobre o qual é o endereço IP. Para saber mais sobre as ligações de saída dos recursos azure, consulte [compreender as ligações de saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

No Azure Resource Manager, um endereço IP público é um recurso que tem propriedades próprias. Alguns dos recursos aos quais pode associar um recurso de endereço IP público são:

* Interfaces de rede de máquina virtual
* Balanceadores de carga com acesso à Internet
* Gateways de VPN
* Gateways de aplicação

Este artigo descreve como pode utilizar endereços IP públicos com recuperação do site.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Atribuição de endereços IP públicos usando plano de recuperação

O endereço IP público do pedido de produção não pode ser mantido no caso da **falha.** As cargas de trabalho criadas no âmbito do processo de failover devem ser atribuídas a um recurso IP público azure disponível na região-alvo. Este passo pode ser feito manualmente ou é automatizado com planos de recuperação. Um plano de recuperação reúne máquinas em grupos de recuperação. Ajuda-o a definir um processo de recuperação sistemática. Você pode usar um plano de recuperação para impor a ordem, e automatizar as ações necessárias em cada passo, usando livros de execução Azure Automation para failover para Azure, ou scripts.

A configuração é a seguinte:
- Crie um plano de [recuperação](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) e agroupa as suas cargas de trabalho conforme necessário no plano.
- Personalize o plano adicionando um passo para anexar um endereço IP público utilizando scripts de livros de [execução Da Automatização Azure](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) ao vM falhado.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Troca de ponto sintetário com encaminhamento de nível DNS

O Azure Traffic Manager permite o encaminhamento de nível DNS entre pontos finais e pode ajudar a [descer os seus RTOs](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) para um cenário de DR. 

Leia mais sobre cenários de failover com Traffic Manager:
1. [No local para Azure falha](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) com Gestor de Tráfego 
2. [Azure para Azure falha](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) com Gestor de Tráfego 

A configuração é a seguinte:
- Criar um [perfil de Gestor de Tráfego.](../traffic-manager/traffic-manager-create-profile.md)
- Utilizando o método de encaminhamento **prioritário,** crie dois pontos finais – **Primário** para fonte e **Failover** para Azure. **A Primária** é atribuída prioridade 1 e **a Failover** é atribuída prioridade 2.
- O ponto final **primário** pode ser [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) ou [External](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) dependendo se o seu ambiente de origem está dentro ou fora de Azure.
- O ponto final **failover** é criado como um ponto final **Azure.** Utilize um **endereço IP público estático,** uma vez que este será um ponto final externo virado para o Gestor de Tráfego no evento de desastre.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o Gestor de Tráfego com recuperação do site Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- Saiba mais sobre os métodos de [encaminhamento](../traffic-manager/traffic-manager-routing-methods.md)do Gestor de Tráfego.
- Saiba mais sobre [os planos](site-recovery-create-recovery-plans.md) de recuperação para automatizar falha na aplicação.
