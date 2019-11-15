---
title: Manter os endereços IP após o failover de VM do Azure com Azure Site Recovery
description: Descreve como reter endereços IP ao fazer failover de VMs do Azure para recuperação de desastre em uma região secundária com Azure Site Recovery
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 650fb7f0877a98ef53ed3868550f9c084ecb5885
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083679"
---
# <a name="retain-ip-addresses-during-failover"></a>Reter endereços IP durante o failover

O [Azure site Recovery](site-recovery-overview.md) permite a recuperação de desastres para VMs do Azure, replicando VMs para outra região do Azure, fazendo failover se ocorrer uma interrupção e fazendo failback para a região primária quando as coisas voltarem para o normal.

Durante o failover, talvez você queira manter o endereçamento IP na região de destino idêntica à região de origem:

- Por padrão, quando você habilita a recuperação de desastre para VMs do Azure, Site Recovery cria recursos de destino com base nas configurações de recurso de origem. Para VMs do Azure configuradas com endereços IP estáticos, Site Recovery tenta provisionar o mesmo endereço IP para a VM de destino, se não estiver em uso. Para obter uma explicação completa de como Site Recovery lida com o endereçamento, [Leia este artigo](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- Para aplicativos simples, a configuração padrão é suficiente. Para aplicativos mais complexos, talvez seja necessário provisionar recursos adicionais para garantir que a conectividade funcione conforme o esperado após o failover.


Este artigo fornece alguns exemplos de retenção de endereços IP em cenários de exemplo mais complexos. Os exemplos incluem:

- Failover para uma empresa com todos os recursos em execução no Azure
- Failover para uma empresa com uma implantação híbrida e recursos em execução no local e no Azure

## <a name="resources-in-azure-full-failover"></a>Recursos no Azure: failover completo

A empresa A tem todos os seus aplicativos em execução no Azure.

### <a name="before-failover"></a>Antes do failover

Aqui está a arquitetura antes do failover.

- A empresa A tem redes e sub-redes idênticas nas regiões do Azure de origem e de destino.
- Para reduzir o RTO (objetivo de tempo de recuperação), a empresa usa nós de réplica para SQL Server Always On, controladores de domínio, etc. Esses nós de réplica estão em uma VNet diferente na região de destino, para que eles possam estabelecer a conectividade VPN site a site entre as regiões de origem e de destino. Isso não será possível se o mesmo espaço de endereço IP for usado na origem e no destino.  
- Antes do failover, a arquitetura de rede é a seguinte:
    - A região primária é o Azure Ásia Oriental
        - Ásia Oriental tem uma VNet (**vnet de origem**) com espaço de endereço 10.1.0.0/16.
        - Ásia Oriental tem cargas de trabalho divididas em três sub-redes na VNet:
            - **Sub-rede 1**: 10.1.1.0/24
            - **Sub-rede 2**: 10.1.2.0/24
            - **Sub-rede 3**: 10.1.3.0/24
    - A região secundária (destino) é o sudeste asiático do Azure
        - O sudeste asiático tem uma VNet de recuperação (**vnet de recuperação**) idêntica à **VNet de origem**.
        - O sudeste asiático tem uma VNet adicional (**vnet do Azure**) com espaço de endereço 10.2.0.0/16.
        - A **VNet do Azure** contém uma sub-rede (**sub-rede 4**) com espaço de endereço 10.2.4.0/24.
        - Os nós de réplica para SQL Server Always On, controlador de domínio etc. estão localizados na **sub-rede 4**.
    - A **vnet de origem** e a **vnet do Azure** estão conectadas a uma conexão VPN site a site.
    - A **VNet de recuperação** não está conectada a nenhuma outra rede virtual.
    - **A empresa A** atribui/verifica endereços IP de destino para itens replicados. O IP de destino é o mesmo que o IP de origem para cada VM.

![Recursos no Azure antes do failover completo](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Após o failover

Se ocorrer uma interrupção regional de origem, a empresa A poderá fazer failover de todos os seus recursos para a região de destino.

- Com os endereços IP de destino já em vigor antes do failover, A empresa A pode orquestrar o failover e estabelecer automaticamente as conexões após o failover entre a **vnet de recuperação** e a **vnet do Azure**. Isso é ilustrado no diagrama a seguir.
- Dependendo dos requisitos do aplicativo, as conexões entre os dois VNets (**vnet de recuperação** e **vnet do Azure**) na região de destino podem ser estabelecidas antes, durante (como uma etapa intermediária) ou após o failover.
  - A empresa pode usar [planos de recuperação](site-recovery-create-recovery-plans.md) para especificar quando as conexões serão estabelecidas.
  - Eles podem se conectar entre o VNets usando o emparelhamento VNet ou VPN site a site.
      - O VNet peering não utiliza um gateway de VPN e tem restrições de diferentes.
      - O [preço](https://azure.microsoft.com/pricing/details/virtual-network) de emparelhamento VNet é calculado de maneira diferente do [Preço](https://azure.microsoft.com/pricing/details/vpn-gateway)de gateway de VPN de vnet a vnet. Para failovers, geralmente aconselhamos usar o mesmo método de conectividade que redes de origem, incluindo o tipo de conexão, para minimizar incidentes de rede imprevisíveis.

    ![Recursos no failover completo do Azure](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Recursos no Azure: failover de aplicativo isolado

Talvez seja necessário fazer failover no nível do aplicativo. Por exemplo, para fazer failover de um aplicativo específico ou de uma camada de aplicativo localizada em uma sub-rede dedicada.

- Nesse cenário, embora você possa reter o endereçamento IP, não é geralmente aconselhável, pois aumenta a chance de inconsistências de conectividade. Você também perderá a conectividade de sub-rede para outras sub-redes na mesma VNet do Azure.
- Uma maneira melhor de realizar o failover de aplicativo no nível de sub-rede é usar endereços IP de destino diferentes para failover (se você precisar de conectividade com outras sub-redes na VNet de origem) ou para isolar cada aplicativo em sua própria VNet dedicada na região de origem. Com a última abordagem, você pode estabelecer a conectividade entre redes na região de origem e emular o mesmo comportamento ao fazer failover para a região de destino.  

Neste exemplo, A empresa A coloca aplicativos na região de origem no VNets dedicado e estabelece a conectividade entre esses VNets. Com esse design, eles podem executar o failover de aplicativo isolado e manter os endereços IP privados de origem na rede de destino.

### <a name="before-failover"></a>Antes do failover

Antes do failover, a arquitetura é a seguinte:

- As VMs de aplicativo são hospedadas na região primária do Azure Ásia Oriental:
    - **App1** As VMs estão localizadas na **vnet de origem**de vnet 1:10.1.0.0/16.
    - **App2** As VMs estão localizadas em **vnet de origem**de vnet 2:10.2.0.0/16.
    - **VNet de origem 1** tem duas sub-redes.
    - A **VNet 2 de origem** tem duas sub-redes.
- A região secundária (de destino) é Azure sudeste asiático-sudeste asiático tem um VNets de recuperação (vnet**1** de recuperação e **vnet 2**) que são idênticos à vnet de **origem 1** e **vnet 2**.
        a **rede virtual de recuperação - e** a vnet de recuperação **2** têm duas sub-redes que correspondem às sub-redes **na vnet 1 de origem** e na **vnet 2** -sudeste da Ásia tem uma vnet adicional (**vnet do Azure**) com espaço de endereço 10.3.0.0/16.
        - **VNet do Azure** contém uma sub-rede (**sub-rede 4**) com espaço de endereço 10.3.4.0/24.
        -Os nós de réplica para SQL Server Always On, controlador de domínio etc. estão localizados na **sub-rede 4**.
- Há várias conexões VPN site a site: 
    - Vnet de **origem 1** e **vnet do Azure**
    - Vnet de **origem 2** e **vnet do Azure**
    - **Vnet de origem 1** e **vnet de origem 2** estão conectados com VPN site a site
- A vnet **1** de recuperação e a rede virtual de recuperação **2** não estão conectadas a nenhum outro VNets.
- A **empresa A** configura os gateways de VPN na vnet de **recuperação vnet 1** e na **rede virtual de recuperação 2**, para reduzir o RTO.  
- **VNet1 de recuperação** e **VNet2 de recuperação** não estão conectados a nenhuma outra rede virtual.
- Para reduzir o RTO (objetivo de tempo de recuperação), os gateways de VPN são configurados no **VNet1 de recuperação** e na **VNet2 de recuperação** antes do failover.

    ![Recursos no Azure antes do failover de aplicativo](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Após o failover

No caso de uma interrupção ou um problema que afete um único aplicativo (em * * rede virtual de origem 2 em nosso exemplo), a empresa A pode recuperar o aplicativo afetado da seguinte maneira:


- Desconecte as conexões VPN entre **VNet1** de origem e **VNet2**de origem e entre **VNet2 de origem** e **VNet do Azure** .
- Estabeleça conexões VPN entre **VNet1 de origem** e **VNet2 de recuperação**e entre VNet2 de **recuperação** e **VNet do Azure**.
- Failover de VMs no **VNet2 de origem** para **VNet2 de recuperação**.

![Recursos no failover de aplicativo do Azure](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Este exemplo pode ser expandido para incluir mais aplicativos e conexões de rede. A recomendação é seguir um modelo de conexão semelhante, o máximo possível, ao fazer failover da origem para o destino.
- Os gateways de VPN usam endereços IP públicos e saltos de gateway para estabelecer conexões. Se você não quiser usar endereços IP públicos ou se quiser evitar saltos extras, poderá usar o [emparelhamento VNet do Azure](../virtual-network/virtual-network-peering-overview.md) para emparelhar redes virtuais entre [regiões do Azure com suporte](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="hybrid-resources-full-failover"></a>Recursos híbridos: failover completo

Nesse cenário, a **empresa B** executa um negócio híbrido, com parte da infraestrutura do aplicativo em execução no Azure e o restante em execução no local. 

### <a name="before-failover"></a>Antes do failover

Aqui está a aparência da arquitetura de rede antes do failover.

- As VMs de aplicativo são hospedadas no Azure Ásia Oriental.
- Ásia Oriental tem uma VNet (**vnet de origem**) com espaço de endereço 10.1.0.0/16.
  - Ásia Oriental tem cargas de trabalho divididas em três sub-redes na **VNet de origem**:
    - **Sub-rede 1**: 10.1.1.0/24
    - **Sub-rede 2**: 10.1.2.0/24
    - **Sub-rede 3**: 10.1.3.0/24, utilizando uma rede virtual do Azure com espaço de endereço 10.1.0.0/16. Esta rede virtual é nomeada **VNet de origem**
      - A região secundária (destino) é o sudeste asiático do Azure:
  - O sudeste asiático tem uma VNet de recuperação (**vnet de recuperação**) idêntica à **VNet de origem**.
- As VMs no Ásia Oriental estão conectadas a um datacenter local com o Azure ExpressRoute ou VPN site a site.
- Para reduzir o RTO, a empresa B provisiona gateways na VNet de recuperação no Azure sudeste asiático antes do failover.
- A empresa B atribui/verifica endereços IP de destino para VMs replicadas. O endereço IP de destino é o mesmo que o endereço IP de origem para cada VM.


![Conectividade local para Azure antes do failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Após o failover


Se ocorrer uma interrupção regional de origem, a empresa B poderá fazer failover de todos os seus recursos para a região de destino.

- Com os endereços IP de destino já em vigor antes do failover, a empresa B pode orquestrar o failover e estabelecer automaticamente as conexões após o failover entre a **vnet de recuperação** e a **vnet do Azure**.
- Dependendo dos requisitos do aplicativo, as conexões entre os dois VNets (**vnet de recuperação** e **vnet do Azure**) na região de destino podem ser estabelecidas antes, durante (como uma etapa intermediária) ou após o failover. A empresa pode usar [planos de recuperação](site-recovery-create-recovery-plans.md) para especificar quando as conexões serão estabelecidas.
- A conexão original entre o Azure Ásia Oriental e o datacenter local deve ser desconectada antes de estabelecer a conexão entre o Azure Sudeste Asiático e o datacenter local.
- O roteamento local é reconfigurado para apontar para a região de destino e os gateways após o failover.

![Conectividade local para Azure após failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Recursos híbridos: failover de aplicativo isolado

A empresa B não pode fazer failover de aplicativos isolados no nível de sub-rede. Isso ocorre porque o espaço de endereço nos VNets de origem e de recuperação é o mesmo, e a origem original para a conexão local está ativa.

 - Para a resiliência de aplicativo, a empresa B precisará posicionar cada aplicativo em sua própria VNet do Azure dedicada.
 - Com cada aplicativo em uma VNet separada, a empresa B pode fazer failover de aplicativos isolados e rotear conexões de origem para a região de destino.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os [planos de recuperação](site-recovery-create-recovery-plans.md).
