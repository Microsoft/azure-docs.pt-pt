---
title: Configurar a ligação após a recuperação após desastre e ativação pós-falha do Azure com o Azure Site Recovery de endereçamento IP | Documentos da Microsoft
description: Descreve como configurar a ligar-se para VMs do Azure após a recuperação após desastre e de ativação pós-falha do local com o Azure Site Recovery de endereçamento IP
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 334537e77d5f291dc38e008af7fed9a4ea973265
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58108853"
---
# <a name="set-up-ip-addressing-to-connect-to-azure-vms-after-failover"></a>Configurar para ligar a VMs do Azure após a ativação pós-falha de endereçamento IP

Este artigo explica os requisitos de rede para ligar a VMs do Azure, depois de utilizar o [do Azure Site Recovery](site-recovery-overview.md) serviço para a replicação e ativação pós-falha do Azure.

Neste artigo aprenderá sobre:

> [!div class="checklist"]
> * Pode utilizar os métodos de ligação
> * Como utilizar um endereço IP diferente para replicar VMs do Azure
> * Como manter os endereços IP para VMs do Azure após a ativação pós-falha

## <a name="connecting-to-replica-vms"></a>Ligar a VMs de réplica

Ao planear a replicação e a estratégia de ativação pós-falha, uma das principais perguntas é como se pode ligar à VM do Azure após a ativação pós-falha. Existem duas opções de ao planear a estratégia de rede de VMs do Azure de réplica:

- **Utilizar endereço IP diferente**: Pode optar por utilizar um intervalo de endereços IP diferente para a rede de VM do Azure replicada. Neste cenário a VM obtém um novo endereço IP após a ativação pós-falha, e é necessária uma atualização DNS.
- **Manter o mesmo endereço IP**: Pode querer utilizar o mesmo intervalo de endereços IP que no seu site primário no local, para a rede do Azure após a ativação pós-falha. Manter o mesmo IP endereços simplifica a recuperação ao reduzir a rede problemas relacionados com após a ativação pós-falha. No entanto, quando está a replicar para o Azure, terá de atualizar as rotas com a nova localização dos endereços IP após a ativação pós-falha.

## <a name="retaining-ip-addresses"></a>Mantendo os endereços IP

Site Recovery fornece a capacidade de manter o IP fixo endereços quando efetuar a ativação pós-falha para o Azure, com uma sub-rede de ativação pós-falha.

- Com a sub-rede de ativação pós-falha, uma sub-rede específica esteja presente no Site 1 ou 2 de Site, mas nunca em ambos os sites em simultâneo.
- Para manter o espaço de endereços IP em caso de uma ativação pós-falha, por meio de programação fazer com que a infraestrutura de router para mover as sub-redes de um site para outro.
- Durante a ativação pós-falha, as sub-redes mover-se com as VMs protegidas associadas. A principal desvantagem é que, em caso de falha, terá de mover a sub-rede de toda.


### <a name="failover-example"></a>Exemplo de ativação pós-falha

Vamos examinar um exemplo para a ativação pós-falha para o Azure através de uma empresa fictícia, o Woodgrove Bank.

- O Woodgrove Bank aloja as aplicações de negócio num site no local. Eles hospedam as aplicações móveis no Azure.
- Não existe conectividade de site-site VPN entre a rede de borda no local e a rede virtual do Azure. Devido a ligação de VPN, a rede virtual no Azure é apresentada como uma extensão da rede no local.
- O Woodgrove quer replicar cargas de trabalho no local para o Azure com o Site Recovery.
  - O Woodgrove tem as aplicações que dependem endereços IP embutido em, pelo que precisam para manter os endereços IP para as aplicações, após a ativação pós-falha para o Azure.
  - Recursos em execução no Azure utilizam o IP endereço intervalo 172.16.1.0/24, 172.16.2.0/24.

![Antes da ativação pós-falha de sub-rede](./media/site-recovery-network-design/network-design7.png)

**Infraestrutura antes da ativação pós-falha**


O Woodgrove precisasse ser capaz de replicar respetivas VMs para o Azure, mantendo os endereços IP, aqui que a empresa precisa de fazer:


1. Crie rede virtual do Azure na qual as VMs do Azure serão criadas após a ativação pós-falha de máquinas no local. Deve ser uma extensão da rede no local, para que os aplicativos podem efetuar a ativação pós-falha totalmente integrada.
2. Antes da ativação pós-falha, no Site Recovery, que atribuir o mesmo endereço IP nas propriedades da máquina. Após a ativação pós-falha, o Site Recovery atribui este endereço à VM do Azure.
3. Depois de ativação pós-falha é executado e as VMs do Azure são criadas com o mesmo endereço IP, se ligam à rede, com um [ligação Vnet a Vnet](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Esta ação pode ser colocado em script.
4. É necessário modificar as rotas, para refletir que essa 192.168.1.0/24 agora foi movido para o Azure.


**Infraestrutura após a ativação pós-falha**

![Após a ativação pós-falha de sub-rede](./media/site-recovery-network-design/network-design9.png)

#### <a name="site-to-site-connection"></a>Ligação site a site

Para além da ligação de vnet a vnet, após a ativação pós-falha, o Woodgrove pode definir a conectividade VPN de site a site:
- Quando configurar uma ligação site a site, na rede do Azure que apenas pode encaminhar tráfego para a localização no local (rede local) se o intervalo de endereços IP é diferente do intervalo de endereços IP no local. Isto acontece porque o Azure não suporta a sub-redes Stretch. Então, se tiver de sub-rede 192.168.1.0/24 no local, não é possível adicionar um 192.168.1.0/24 local da rede na rede do Azure. Isso é esperado, porque o Azure não sabe que não há nenhum VMs do Active Directory na sub-rede e que a sub-rede está a ser criada para apenas a recuperação após desastre.
- Para ser capaz de encaminhar corretamente o tráfego de rede fora de uma rede do Azure, as sub-redes na rede e a rede local não podem entrar em conflito.




## <a name="assigning-new-ip-addresses"></a>Atribuir novos endereços IP

Isso [mensagem de blogue](https://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) explica como configurar a infraestrutura de rede do Azure quando não precisar de manter os endereços IP após a ativação pós-falha. Ele começa com uma descrição da aplicação, analisa como configurar redes no local e no Azure e termina com informações sobre como executar as ativações pós-falha.

## <a name="next-steps"></a>Passos Seguintes
[Executar uma ativação pós-falha](site-recovery-failover.md)
