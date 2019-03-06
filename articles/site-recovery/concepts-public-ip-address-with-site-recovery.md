---
title: Utilizar endereços IP públicos após a ativação pós-falha com o Azure Site Recovery | Documentos da Microsoft
description: Descreve como configurar endereços IP públicos com o Azure Site Recovery e o Gestor de tráfego do Azure para a migração e de recuperação após desastre
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: cba9a334ae014351b1156f34325e9f134b1592a9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439202"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Configurar endereços IP públicos após a ativação pós-falha

Os endereços IP públicos permitem que os recursos da Internet comuniquem com os recursos do Azure à entrada. Os endereços IP públicos também permitem que os recursos do Azure comuniquem com a Internet à saída, bem como com serviços do Azure destinados ao público com um endereço IP atribuído ao recurso.
- Comunicação de entrada da Internet para o recurso, tal como máquinas virtuais (VM) do Azure, os Gateways de aplicação do Azure, balanceadores de carga do Azure, Gateways de VPN do Azure e outros. Continua a pode comunicar com alguns recursos, tais como VMs, a partir da Internet, se uma VM não tem um endereço IP público atribuído ao mesmo, desde que a VM é a parte de um conjunto de back-end de Balanceador de carga e o Balanceador de carga é atribuído um endereço IP público.
- Conectividade de saída à Internet através de um endereço IP previsível. Por exemplo, uma máquina virtual podem comunicar saída à Internet sem um endereço IP público atribuído ao mesmo, mas o seu endereço é o endereço de rede traduzido pelo Azure para um endereço público imprevisível, por predefinição. Atribuir um endereço IP público a um recurso permite-lhe saber qual o endereço IP é utilizado para a ligação de saída. Embora previsível, pode alterar o endereço, dependendo do método de atribuição escolhido. Para obter mais informações, consulte [criar um endereço IP público](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address). Para saber mais sobre as ligações de saída dos recursos do Azure, veja [compreender as ligações de saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

No Azure Resource Manager, um endereço IP público é um recurso com suas próprias propriedades. Alguns dos recursos aos quais pode associar um recurso de endereço IP público são:

* Interfaces de rede de máquina virtual
* Balanceadores de carga com acesso à Internet
* Gateways de VPN
* Gateways de aplicação

Este artigo descreve como pode utilizar endereços IP públicos com o Site Recovery.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Atribuição de endereços IP pública com o plano de recuperação

Endereço IP público da aplicação de produção **não podem ser mantidas na ativação pós-falha**. Cargas de trabalho colocadas como parte do processo de ativação pós-falha deve ser atribuído um recurso de IP público do Azure disponível na região de destino. Este passo pode ser feito manualmente ou é automatizado com planos de recuperação. Um plano de recuperação reúne máquinas em grupos de recuperação. Ele ajuda a definir um processo de recuperação sistemática. Pode utilizar um plano de recuperação para impor a ordem e automatizar as ações necessárias em cada etapa, através de runbooks de automatização do Azure para a ativação pós-falha para o Azure ou scripts.

A configuração é o seguinte:
- Criar uma [plano de recuperação](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan) e suas cargas de trabalho conforme necessário para o plano de grupo.
- Personalizar o plano ao adicionar um passo para anexar um através de endereços IP público [runbooks de automatização do Azure](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) scripts para a VM ativação pós-falha.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Ponto final público mudar com encaminhamento ao nível do DNS

Do Azure Gestor de tráfego permite o DNS nível encaminhamento entre pontos de extremidade e pode ajudá-lo em [diminuir sua RTOs](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) para um cenário de DR. 

Leia mais sobre cenários de ativação pós-falha com o Gestor de tráfego:
1. [No local para a ativação pós-falha do Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) com o Gestor de tráfego 
2. [Ativação pós-falha do Azure para o Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) com o Gestor de tráfego 

A configuração é o seguinte:
- Criar uma [perfil do Traffic Manager](../traffic-manager/traffic-manager-create-profile.md).
- Utilizar o **prioridade** encaminhamento método, crie dois pontos de extremidade – **primário** para a origem e **ativação pós-falha** para o Azure. **Primário** é atribuída prioridade 1 e **ativação pós-falha** é atribuída a prioridade 2.
- O **primário** ponto final pode ser [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) ou [externo](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) dependendo se o seu ambiente de origem é dentro ou fora do Azure.
- O **ativação pós-falha** ponto final é criado como um **Azure** ponto final. Utilize um **endereço IP público estático** porque isso será externo destinada ao ponto final para o Gestor de tráfego no evento de desastre.

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [Gestor de tráfego com o Azure Site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md)
- Saiba mais sobre o Gestor de tráfego [métodos de encaminhamento](../traffic-manager/traffic-manager-routing-methods.md).
- Saiba mais sobre [planos de recuperação](site-recovery-create-recovery-plans.md) para automatizar a ativação pós-falha do aplicativo.
