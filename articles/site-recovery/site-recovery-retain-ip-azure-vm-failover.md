---
title: Manter os endereços IP durante a ativação pós-falha da VM do Azure com o Azure Site Recovery | Documentos da Microsoft
description: Descreve como manter os endereços IP quando efetuar a ativação pós-falha de VMs do Azure para recuperação após desastre para uma região secundária com o Azure Site Recovery
ms.service: site-recovery
ms.date: 11/27/2018
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: aefb0684ea065841824ad27d1105ef309418c6b9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090751"
---
# <a name="retain-ip-addresses-during-failover"></a>Manter os endereços IP durante a ativação pós-falha

[O Azure Site Recovery](site-recovery-overview.md) permite a recuperação após desastre para VMs do Azure ao replicar VMs para outra região do Azure, efetuar a ativação pós-falha se ocorrer uma falha e reativa para a região primária quando as coisas são volta ao normal.

Durante a ativação pós-falha, pode querer manter na região de destino é idêntica à região de origem de endereçamento IP:

- Por predefinição, quando ativar a recuperação após desastre para VMs do Azure, o Site Recovery cria os recursos de destino com base nas definições de recursos de origem. Para as VMs do Azure configurada com endereços IP estáticos, o Site Recovery tenta aprovisionar o mesmo endereço IP da VM, de destino se não está em utilização. Para obter uma explicação completa de como o Site Recovery trata endereçamento, [rever este artigo](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- Para aplicativos simples, a configuração predefinida é suficiente. Para aplicações mais complexas, poderá ter de aprovisionar recursos adicionais para se certificar de que a conectividade funciona como esperado após a ativação pós-falha.


Este artigo fornece alguns exemplos de retenção de endereços IP nos cenários de exemplo mais complexos. Os exemplos incluem:

- Ativação pós-falha para uma empresa com todos os recursos em execução no Azure
- Ativação pós-falha para uma empresa com uma implementação híbrida e recursos em execução no local e no Azure

## <a name="resources-in-azure-full-failover"></a>Recursos no Azure: ativação pós-falha completa

A empresa A tem todas as suas aplicações em execução no Azure.

### <a name="before-failover"></a>Antes da ativação pós-falha

Eis a arquitetura antes da ativação pós-falha.

- A empresa tem redes idênticos e de sub-redes de origem e regiões do Azure de destino.
- Para reduzir o objetivo de tempo de recuperação (RTO), a empresa utiliza nós de réplica para o SQL Server Always On, controladores de domínio, etc. Estes nós de réplica estão numa VNet diferente na região de destino, para que eles podem estabelecer a conectividade de site-site VPN entre as regiões de origem e de destino. Não é possível se o mesmo espaço de endereços IP é utilizado na origem e de destino.  
- Antes da ativação pós-falha, a arquitetura de rede é o seguinte:
    - Região primária é Azure Ásia Oriental
        - Ásia Oriental tem uma VNet (**VNet de origem**) com o endereço espaço 10.1.0.0/16.
        - Este asiático tem cargas de trabalho dividido em três sub-redes na VNet:
            - **Sub-rede 1**: 10.1.1.0/24
            - **Sub-rede 2**: 10.1.2.0/24,
            - **Sub-rede 3**: 10.1.3.0/24
    - Região secundária (destino) é o Azure Sudeste asiático
        - Sudeste asiático tem uma recuperação VNet (**VNet de recuperação**) idêntico ao **VNet de origem**.
        - Sudeste asiático tem uma VNet adicional (**VNet do Azure**) com o endereço espaço 10.2.0.0/16.
        - **VNet do Azure** contém uma sub-rede (**sub-rede 4**) com o endereço espaço 10.2.4.0/24.
        - Nós de réplica para o SQL Server Always On, o controlador de domínio etc estão localizados num **sub-rede 4**.
    - **VNet de origem** e **VNet do Azure** estão ligados com uma ligação de site-site VPN.
    - **VNet de recuperação** não está ligado com qualquer outra rede virtual.
    - **Da empresa A** atribui/verifica os endereços IP de destino para itens replicados. O IP de destino é o mesmo que o IP de origem para cada VM.

![Recursos no Azure antes da ativação pós-falha completo](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Após a ativação pós-falha

Se ocorrer uma falha regional de origem, a empresa pode falhar ao longo de todos os respetivos recursos para a região de destino.

- Com endereços IP de destino já em vigor antes da ativação pós-falha, a empresa A pode coordenar a ativação pós-falha e automaticamente estabelecer ligações após a ativação pós-falha entre **VNet de recuperação** e **VNet do Azure**. Isso é ilustrado no diagrama seguinte....
- Dependendo dos requisitos de aplicações, ligações entre as duas VNets (**VNet de recuperação** e **VNet do Azure**) no destino região pode ser estabelecido antes, durante a (como passo intermédio) ou após a ativação pós-falha.
  - A empresa pode utilizar [planos de recuperação](site-recovery-create-recovery-plans.md) para especificar quando serão possível estabelecer ligações.
  - Se possam ligar-se entre as VNets com peering de VNet ou VPN de site a site.
      - O VNet peering não utiliza um gateway de VPN e tem restrições de diferentes.
      - O VNet peering [preços](https://azure.microsoft.com/pricing/details/virtual-network) é calculado de forma diferente do que o Gateway de VPN de VNet a VNet [preços](https://azure.microsoft.com/pricing/details/vpn-gateway). Para as ativações pós-falha, aconselhamos em geral, para utilizar o mesmo método de conectividade como redes de origem, incluindo o tipo de ligação, para minimizar a incidentes de rede imprevisíveis.

    ![Recursos de ativação pós-falha completa do Azure](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Recursos no Azure: ativação pós-falha de aplicação em ambiente isolado

Poderá ter de efetuar a ativação pós-falha ao nível da aplicação. Por exemplo, para efetuar a ativação pós-falha de uma aplicação específica ou a camada de aplicação localizado numa sub-rede dedicada.

- Neste cenário, embora pode manter o endereçamento de IP, não é aconselhável em geral, uma vez que ele aumenta a possibilidade de inconsistências de conectividade. Também irá perder a conectividade de sub-rede para outras sub-redes dentro da mesma VNet do Azure.
- Uma maneira melhor de fazer a ativação pós-falha de aplicação de nível de sub-rede está a utilizar endereços IP de destino diferente para ativação pós-falha (se precisar de conectividade para outras sub-redes na VNet de origem), ou para isolar cada aplicação em sua própria VNet dedicada na região de origem. Com a última abordagem pode estabelecer conectividade entre redes na região de origem e emular o mesmo comportamento quando efetuar a ativação pós-falha para a região de destino.  

Neste exemplo, aplicações de locais de empresa A na região de origem no dedicado VNets e estabelece a conectividade entre as VNets. Com esta estrutura, podem efetuar ativação pós-falha de aplicação em ambiente isolado e manter os endereços IP privados de origem na rede de destino.

### <a name="before-failover"></a>Antes da ativação pós-falha

Antes da ativação pós-falha, a arquitetura é o seguinte:

- VMs de aplicação encontram-se na sua região do Azure Ásia Oriental primária:
    - **App1** VMs estão localizadas na VNet **1 de VNet de origem**: 10.1.0.0/16.
    - **App2** VMs estão localizadas na VNet **2 de VNet de origem**: 10.2.0.0/16.
    - **VNet 1 de origem** tem duas sub-redes.
    - **2 de VNet de origem** tem duas sub-redes.
- Região secundária (destino) é o Azure Sudeste da Ásia - Sudeste asiático tem uma recuperação VNets (**recuperação VNet 1** e **recuperação VNet 2**) que são idênticos aos **1 de VNet de origem** e **Da origem de VNet 2**.
        - **VNet de recuperação 1** e **recuperação VNet 2** cada uma tem duas sub-redes que correspondem as sub-redes **1 de VNet de origem** e **2 de VNet de origem** -Sudeste asiático tem um VNet adicional (**VNet do Azure**) com o endereço espaço 10.3.0.0/16.
        - **VNet do Azure** contém uma sub-rede (**sub-rede 4**) com o endereço espaço 10.3.4.0/24.
        -Nós de réplica para o SQL Server Always On, o controlador de domínio etc estão localizados num **sub-rede 4**.
- Há um número de ligações de VPN de site a site: 
    - **1 de VNet de origem** e **VNet do Azure**
    - **2 de VNet de origem** e **VNet do Azure**
    - **VNet 1 de origem** e **2 de VNet de origem** estão ligados com VPN site a site
- **VNet de recuperação 1** e **recuperação VNet 2** não estão ligadas a quaisquer outras VNets.
- **Da empresa A** configura gateways de VPN no **1 de VNet de recuperação** e **recuperação VNet 2**, para reduzir o RTO.  
- **Recuperação VNet1** e **recuperação VNet2** não estão ligados com qualquer outra rede virtual.
- Para reduzir o objetivo de tempo de recuperação (RTO), os gateways de VPN são configurados no **recuperação VNet1** e **recuperação VNet2** antes da ativação pós-falha.

    ![Recursos no Azure antes de ativação pós-falha de aplicação](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Após a ativação pós-falha

Em caso de falha ou um problema que afeta um único aplicativo (em * * 2 de VNet de origem no nosso exemplo), empresa pode recuperar a aplicação afetada da seguinte forma:


- Desligar as ligações de VPN entre **origem VNet1** e **origem VNet2**e entre **origem VNet2** e **VNet do Azure** .
- Estabelecer ligações de VPN entre **origem VNet1** e **recuperação VNet2**e entre **recuperação VNet2** e **VNet do Azure**.
- Ativação pós-falha de VMs no **origem VNet2** ao **recuperação VNet2**.

![Recursos de ativação pós-falha de aplicação do Azure](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Neste exemplo, pode ser expandido para incluir mais aplicações e ligações de rede. Recomenda-se seguir um modelo de como o tipo de ligação, máximo possível, quando efetuar a ativação pós-falha de origem ao destino.
- Gateways de VPN utilizam endereços IP públicos e saltos de gateway para estabelecer ligações. Se não quiser utilizar endereços IP públicos, ou deseja evitar saltos Extras, pode usar [peering de VNet do Azure](../virtual-network/virtual-network-peering-overview.md) para configurar o peering entre redes virtuais entre [regiões do Azure suportadas](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="hybrid-resources-full-failover"></a>Recursos de híbrida: ativação pós-falha completa

Neste cenário, **empresa B** é executado de uma empresa híbrida, com parte da infra-estrutura de aplicativo em execução no Azure e o resto em execução no local. 

### <a name="before-failover"></a>Antes da ativação pós-falha

Eis o aspeto de arquitetura de rede antes da ativação pós-falha.

- VMs de aplicação estão alojadas no Azure Ásia Oriental.
- Ásia Oriental tem uma VNet (**VNet de origem**) com o endereço espaço 10.1.0.0/16.
  - Este asiático tem cargas de trabalho dividido em três sub-redes na **VNet de origem**:
    - **Sub-rede 1**: 10.1.1.0/24
    - **Sub-rede 2**: 10.1.2.0/24,
    - **Sub-rede 3**: 10.1.3.0/24utilizing uma rede virtual com endereço espaço 10.1.0.0/16. Esta rede virtual com o nome **VNet de origem**
      - A região secundária (destino) é o Azure Sudeste asiático:
  - Sudeste asiático tem uma recuperação VNet (**VNet de recuperação**) idêntico ao **VNet de origem**.
- As VMs na Ásia Oriental estão ligadas a um datacenter no local com o Azure ExpressRoute ou o site para site VPN.
- Para reduzir o RTO, a empresa B Aprovisiona gateways de VNet de recuperação no Azure Sudeste asiático antes da ativação pós-falha.
- Endereços IP de destino de atribui/verifica de empresa B para VMs replicadas. O endereço IP de destino é o mesmo endereço IP de origem para cada VM.


![Conectividade no local-para-Azure antes da ativação pós-falha](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Após a ativação pós-falha


Se ocorrer uma falha regional de origem, B da empresa pode efetuar a ativação pós-falha de todos os respetivos recursos para a região de destino.

- Com endereços IP de destino já em vigor antes da ativação pós-falha, pode orquestrar a ativação pós-falha e automaticamente estabelecer ligações após a ativação pós-falha entre empresa B **VNet de recuperação** e **VNet do Azure**.
- Dependendo dos requisitos de aplicações, ligações entre as duas VNets (**VNet de recuperação** e **VNet do Azure**) no destino região pode ser estabelecido antes, durante a (como passo intermédio) ou após a ativação pós-falha. A empresa pode utilizar [planos de recuperação](site-recovery-create-recovery-plans.md) para especificar quando serão possível estabelecer ligações.
- A ligação original entre Azure Ásia Oriental e o datacenter no local deve ser desligada antes de estabelecer a ligação entre o Azure Sudeste asiático e o datacenter no local.
- O encaminhamento no local é reconfigurados para apontar para a região de destino e gateways após a ativação pós-falha.

![Conectividade no local-para-Azure após a ativação pós-falha](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Recursos de híbrida: ativação pós-falha de aplicação em ambiente isolado

Empresa B não é possível efetuar a ativação pós-falha isoladas aplicações ao nível da sub-rede. Isto acontece porque o espaço de endereços na origem e de recuperação VNets é o mesmo, e a origem original para a ligação no local está ativa.

 - Para resiliência de aplicação B da empresa precisará de colocar cada aplicação na sua própria VNet dedicada do Azure.
 - Com cada aplicação numa VNet separada, B da empresa pode efetuar a ativação pós-falha de aplicações isoladas e encaminhar as ligações de origem para a região de destino.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [planos de recuperação](site-recovery-create-recovery-plans.md).
