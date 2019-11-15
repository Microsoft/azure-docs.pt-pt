---
title: Atribuir endereços IP públicos após o failover com Azure Site Recovery
description: Descreve como configurar endereços IP públicos com Azure Site Recovery e o Gerenciador de tráfego do Azure para recuperação de desastres e migração
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: b1f3ffa6fc90fc0cab0217d1b71907342f2dbd0d
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084237"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Configurar endereços IP públicos após o failover

Os endereços IP públicos permitem que os recursos da Internet comuniquem com os recursos do Azure à entrada. Os endereços IP públicos também permitem que os recursos do Azure comuniquem com a Internet à saída, bem como com serviços do Azure destinados ao público com um endereço IP atribuído ao recurso.
- Comunicação de entrada da Internet para o recurso, como VMS (máquinas virtuais) do Azure, gateways de Aplicativo Azure, balanceadores de carga do Azure, gateways de VPN do Azure e outros. Você ainda poderá se comunicar com alguns recursos, como VMs, da Internet, se uma VM não tiver um endereço IP público atribuído a ela, desde que a VM faça parte de um pool de back-end do balanceador de carga e o balanceador de carga receba um endereço IP público.
- Conectividade de saída para a Internet usando um endereço IP previsível. Por exemplo, uma máquina virtual pode comunicar a saída à Internet sem um endereço IP público atribuído a ela, mas seu endereço é o endereço de rede traduzido pelo Azure para um endereço público imprevisível, por padrão. A atribuição de um endereço IP público a um recurso permite que você saiba qual endereço IP é usado para a conexão de saída. Embora previsível, o endereço pode ser alterado, dependendo do método de atribuição escolhido. Para obter mais informações, consulte [criar um endereço IP público](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address). Para saber mais sobre as conexões de saída dos recursos do Azure, confira [entender as conexões de saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

No Azure Resource Manager, um endereço IP público é um recurso que tem suas próprias propriedades. Alguns dos recursos aos quais pode associar um recurso de endereço IP público são:

* Interfaces de rede de máquina virtual
* Balanceadores de carga com acesso à Internet
* Gateways de VPN
* Gateways de aplicação

Este artigo descreve como você pode usar endereços IP públicos com Site Recovery.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Atribuição de endereço IP público usando o plano de recuperação

O endereço IP público do aplicativo de produção **não pode ser retido no failover**. As cargas de trabalho trazidas como parte do processo de failover devem ser atribuídas a um recurso de IP público do Azure disponível na região de destino. Essa etapa pode ser feita manualmente ou automatizada com planos de recuperação. Um plano de recuperação reúne computadores em grupos de recuperação. Ele ajuda a definir um processo de recuperação sistemático. Você pode usar um plano de recuperação para impor a ordem e automatizar as ações necessárias em cada etapa, usando runbooks de automação do Azure para failover para o Azure ou scripts.

A configuração é a seguinte:
- Crie um [plano de recuperação](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) e agrupe suas cargas de trabalho conforme necessário no plano.
- Personalize o plano adicionando uma etapa para anexar um endereço IP público usando scripts de [runbooks de automação do Azure](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) à VM com failover.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Comutação de ponto de extremidade público com roteamento de nível de DNS

O Gerenciador de tráfego do Azure permite o roteamento de nível de DNS entre pontos de extremidade e pode ajudar a [conduzir seus RTOs](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) para um cenário de recuperação de desastres. 

Leia mais sobre cenários de failover com o Gerenciador de tráfego:
1. [Failover local para o Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) com o Gerenciador de tráfego 
2. [Failover do Azure para o Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) com o Gerenciador de tráfego 

A configuração é a seguinte:
- Criar um [perfil do Gerenciador de tráfego](../traffic-manager/traffic-manager-create-profile.md).
- Utilizando o método de roteamento de **prioridade** , crie dois pontos de extremidade – **primário** para origem e **failover** para o Azure. O **primário** é atribuído à prioridade 1 e o **failover** é atribuído à prioridade 2.
- O ponto de extremidade **primário** pode ser [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) ou [externo](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) , dependendo se o seu ambiente de origem está dentro ou fora do Azure.
- O ponto de extremidade de **failover** é criado como um ponto de extremidade **do Azure** . Use um **endereço IP público estático** , pois ele será um ponto de extremidade voltado para o Gerenciador de tráfego no evento de desastre.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o [Gerenciador de tráfego com o Azure site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- Saiba mais sobre os [métodos de roteamento](../traffic-manager/traffic-manager-routing-methods.md)do Traffic Manager.
- Saiba mais sobre os [planos de recuperação](site-recovery-create-recovery-plans.md) para automatizar o failover de aplicativos.
