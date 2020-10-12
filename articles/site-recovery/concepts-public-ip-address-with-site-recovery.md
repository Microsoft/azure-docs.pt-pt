---
title: Atribuir endereços IP públicos após falha com recuperação do site Azure
description: Descreve como configurar endereços IP públicos com Azure Site Recovery e Azure Traffic Manager para recuperação de desastres e migração
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 01c2f61dcf024e8c9dbbd5b2ee11a479b3c16305
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86130278"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Configurar endereços IP públicos após falha

Os endereços IP públicos permitem que os recursos da Internet comuniquem com os recursos do Azure à entrada. Os endereços IP públicos também permitem que os recursos do Azure comuniquem com a Internet à saída, bem como com serviços do Azure destinados ao público com um endereço IP atribuído ao recurso.
- Comunicação de entrada da Internet para o recurso, tais como Azure Virtual Machines (VM), Azure Application Gateways, Azure Load Balancers, Azure VPN Gateways, entre outros. Você ainda pode comunicar com alguns recursos, tais como VMs, da Internet, se um VM não tiver um endereço IP público atribuído a ele, desde que o VM faça parte de um pool de back-end do balanceador de carga, e o equilibrador de carga é atribuído um endereço IP público.
- Conectividade de saída para a Internet usando um endereço IP previsível. Por exemplo, uma máquina virtual pode comunicar saída para a Internet sem um endereço IP público que lhe seja atribuído, mas o seu endereço é o endereço de rede traduzido pelo Azure para um endereço público imprevisível, por padrão. A atribuição de um endereço IP público a um recurso permite-lhe saber qual o endereço IP utilizado para a ligação de saída. Embora previsível, o endereço pode mudar, dependendo do método de atribuição escolhido. Para mais informações, consulte [Criar um endereço IP público.](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address) Para saber mais sobre as ligações de saída a partir dos recursos Azure, consulte [as ligações de saída.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

No Azure Resource Manager, um endereço IP público é um recurso que tem propriedades próprias. Alguns dos recursos aos quais pode associar um recurso de endereço IP público são:

* Interfaces de rede de máquina virtual
* Balanceadores de carga com acesso à Internet
* Gateways de VPN
* Gateways de aplicação

Este artigo descreve como pode utilizar endereços IP públicos com recuperação do site.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Atribuição de endereços IP públicos usando Plano de Recuperação

O endereço IP público do pedido de produção **não pode ser mantido em insumoção**. As cargas de trabalho apresentadas no âmbito do processo de failover devem ser atribuídas a um recurso IP público Azure disponível na região-alvo. Este passo pode ser feito manualmente ou é automatizado com planos de recuperação. Um plano de recuperação reúne máquinas em grupos de recuperação. Ajuda-o a definir um processo de recuperação sistemática. Você pode usar um plano de recuperação para impor a ordem, e automatizar as ações necessárias em cada passo, usando os runbooks da Azure Automation para failover para Azure, ou scripts.

A configuração é a seguinte:
- Crie um [plano de recuperação](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) e agrupe as suas cargas de trabalho conforme necessário no plano.
- Personalize o plano adicionando um passo para anexar um endereço IP público utilizando scripts de scripts de scripts de [scripts Azure Automation](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) para o falhado sobre VM.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Commutação de ponto final público com encaminhamento de nível DNS

O Azure Traffic Manager permite o encaminhamento de nível DNS entre pontos finais e pode ajudar a [conduzir os seus RTOs](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) para um cenário DR. 

Leia mais sobre os cenários de failover com o Traffic Manager:
1. [No local para a Azure falha com](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) o Gerente de Trânsito 
2. [Azure para Azure falha com](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) gerente de tráfego 

A configuração é a seguinte:
- Criar um [perfil de Gestor de Tráfego.](../traffic-manager/quickstart-create-traffic-manager-profile.md)
- Utilizando o método de encaminhamento **prioritário,** crie dois pontos finais – **Primário** para fonte e **Failover** para Azure. **A prioridade** 1 é atribuída e **a Falha** é atribuída prioridade 2.
- O ponto final **primário** pode ser [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) ou [External](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) dependendo se o seu ambiente de origem está dentro ou fora de Azure.
- O ponto final **failover** é criado como um ponto final **Azure.** Utilize um **endereço IP público estático,** pois este será o ponto final externo virado para o Gestor de Tráfego no evento de desastre.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [o Gestor de Tráfego com recuperação do site Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- Saiba mais sobre [os métodos de encaminhamento](../traffic-manager/traffic-manager-routing-methods.md)do Traffic Manager .
- Saiba mais sobre [planos de recuperação](site-recovery-create-recovery-plans.md) para automatizar o failover da aplicação.
