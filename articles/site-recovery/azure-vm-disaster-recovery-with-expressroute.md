---
title: Integrar o Azure ExpressRoute com a recuperação de desastre para VMs do Azure usando o serviço de Azure Site Recovery | Microsoft Docs
description: Descreve como configurar a recuperação de desastre para VMs do Azure usando o Azure Site Recovery e o Azure ExpressRoute
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 0974e2ed78e557168357c51b5c77a94de2f56dc5
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722107"
---
# <a name="integrate-azure-expressroute-with-disaster-recovery-for-azure-vms"></a>Integrar o Azure ExpressRoute com a recuperação de desastre para VMs do Azure


Este artigo descreve como integrar o Azure ExpressRoute com o [Azure site Recovery](site-recovery-overview.md), quando você configura a recuperação de desastres para VMs do Azure para uma região secundária do Azure.

Site Recovery permite a recuperação de desastre de VMs do Azure replicando dados de VM do Azure para o Azure.

- Se as VMs do Azure usarem [discos gerenciados do Azure](../virtual-machines/windows/managed-disks-overview.md), os dados da VM serão replicados para um disco gerenciado replicado na região secundária.
- Se as VMs do Azure não usam discos gerenciados, os dados da VM são replicados para uma conta de armazenamento do Azure.
- Os pontos de extremidade de replicação são públicos, mas o tráfego de replicação para VMs do Azure não cruza a Internet.

O ExpressRoute permite que você estenda redes locais para a nuvem Microsoft Azure por uma conexão privada, facilitada por um provedor de conectividade. Se você tiver configurado o ExpressRoute, ele se integrará com Site Recovery da seguinte maneira:

- **Durante a replicação entre as regiões do Azure**: O tráfego de replicação para a recuperação de desastres de VM do Azure é somente dentro do Azure e o ExpressRoute não é necessário ou usado para replicação. No entanto, se você estiver se conectando de um site local para as VMs do Azure no site primário do Azure, há vários problemas a serem considerados quando você estiver configurando a recuperação de desastre para essas VMs do Azure.
- **Failover entre regiões do Azure**: Quando ocorrem interrupções, você faz failover de VMs do Azure da região primária para a secundária do Azure. Após o failover para uma região secundária, há várias etapas a serem executadas para acessar as VMs do Azure na região secundária usando o ExpressRoute.


## <a name="before-you-begin"></a>Antes de começar

Antes de começar, certifique-se de entender os seguintes conceitos:

- [Circuitos](../expressroute/expressroute-circuit-peerings.md) do ExpressRoute
- [Domínios de roteamento](../expressroute/expressroute-circuit-peerings.md#routingdomains) do ExpressRoute
- [Locais](../expressroute/expressroute-locations.md)do ExpressRoute.
- Arquitetura de [replicação](azure-to-azure-architecture.md) de VM do Azure
- Como [Configurar a replicação](azure-to-azure-tutorial-enable-replication.md) para VMs do Azure.
- Como fazer [failover](azure-to-azure-tutorial-failover-failback.md) de VMs do Azure.


## <a name="general-recommendations"></a>Recomendações gerais

Para obter uma prática recomendada e assegurar RTOs (objetivos de tempo de recuperação) eficientes para a recuperação de desastres, recomendamos que você faça o seguinte ao configurar o Site Recovery para integrar-se ao ExpressRoute:

- Provisione componentes de rede antes do failover para uma região secundária:
    - Quando você habilita a replicação para VMs do Azure, o Site Recovery pode implantar automaticamente recursos de rede, como redes, sub-redes e gateways na região do Azure de destino, com base nas configurações de rede de origem.
    - Site Recovery não pode configurar automaticamente recursos de rede, como gateways de VNet.
    - Recomendamos que você provisione esses recursos de rede adicionais antes do failover. Um pequeno tempo de inatividade é associado a essa implantação, e ele pode ter um impacto geral na recuperação, caso você não tenha contado durante o planejamento da implantação.
- Executar análises regulares de recuperação de desastre:
    - Um teste valida a sua estratégia de replicação sem perda de dados ou tempo de inatividade e não afeta o seu ambiente de produção. Isso ajuda a evitar problemas de configuração de última hora que podem afetar negativamente o RTO.
    - Quando você executa um failover de teste para a análise, recomendamos que você use uma rede de VM do Azure separada, em vez da rede padrão que é configurada quando você habilita a replicação.
- Use espaços de endereço IP diferentes se você tiver um único circuito do ExpressRoute.
    - Recomendamos que você use um espaço de endereço IP diferente para a rede virtual de destino. Isso evita problemas ao estabelecer conexões durante interrupções regionais.
    - Se você não puder usar um espaço de endereço separado, certifique-se de executar o failover de teste de análise de recuperação de desastre em uma rede de teste separada com endereços IP diferentes. Não é possível conectar dois VNets com espaço de endereço IP sobreposto ao mesmo circuito de ExpressRoute.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Replicar VMs do Azure ao usar o ExpressRoute


Se você quiser configurar a replicação para VMs do Azure em um site primário e estiver se conectando a essas VMs do site local por meio do ExpressRoute, veja o que você precisa fazer:

1. [Habilite a replicação](azure-to-azure-tutorial-enable-replication.md) para cada VM do Azure.
2. Opcionalmente, permita Site Recovery configurar a rede:
    - Quando você configura e habilita a replicação, o Site Recovery configura redes, sub-redes e sub-redes de gateway na região do Azure de destino, para corresponder às da região de origem. Site Recovery também mapeia entre as redes virtuais de origem e de destino.
    - Se você não quiser que Site Recovery faça isso automaticamente, crie os recursos de rede do lado do destino antes de habilitar a replicação.
3. Criar outros elementos de rede:
    - Site Recovery não cria tabelas de rotas, gateways de VNet, conexões de gateway de VNet, emparelhamento de VNet ou outros recursos de rede e conexões na região secundária.
    - Você precisa criar esses elementos de rede adicionais na região secundária, a qualquer momento antes de executar um failover da região primária.
    - Você pode usar [planos de recuperação](site-recovery-create-recovery-plans.md) e scripts de automação para configurar e conectar esses recursos de rede.
1. Se você tiver uma solução de virtualização de rede (NVA) implantada para controlar o fluxo de tráfego de rede, observe que:
    - A rota do sistema padrão do Azure para replicação de VM do Azure é 0.0.0.0/0.
    - Normalmente, as implantações de NVA também definem uma rota padrão (0.0.0.0/0) que força o tráfego de Internet de saída a fluir pelo NVA. A rota padrão é usada quando nenhuma outra configuração de rota específica pode ser encontrada.
    - Se esse for o caso, o NVA poderá ser sobrecarregado se todo o tráfego de replicação passar pelo NVA.
    - A mesma limitação também se aplica ao usar rotas padrão para rotear todo o tráfego de VM do Azure para implantações locais.
    - Nesse cenário, recomendamos que você [crie um ponto de extremidade de serviço de rede](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) em sua rede virtual para o serviço Microsoft. Storage, de modo que o tráfego de replicação não deixe o limite do Azure.

## <a name="replication-example"></a>Exemplo de replicação

Normalmente, as implantações corporativas têm cargas de trabalho divididas em vários VNets do Azure, com um hub de conectividade central para conectividade externa com a Internet e para sites locais. Uma topologia de Hub e spoke normalmente é usada junto com o ExpressRoute.

![Local para Azure com ExpressRoute antes do failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Região**. Os aplicativos são implantados na região de Ásia Oriental do Azure.
- **VNets de spoke**. Os aplicativos são implantados em dois vNets spoke:
    - **VNet1 de origem**: 10.1.0.0/24.
    - **VNet2 de origem**: 10.2.0.0/24.
    - Cada rede virtual spoke é conectada à **vNet do Hub**.
- **VNet do Hub**. Há uma **vnet do hub de origem**da vnet do Hub: 10.10.10.0/24.
  - Essa vNet do Hub atua como o gatekeeper.
  - Todas as comunicações entre sub-redes passam por esse Hub.
    - **Sub-redes vNet do Hub**. A vNet do Hub tem duas sub-redes:
    - **Sub-rede NVA**: 10.10.10.0/25. Essa sub-rede contém um NVA (10.10.10.10).
    - **A sub-rede de gateway**: 10.10.10.128/25. Essa sub-rede contém um gateway de ExpressRoute conectado a uma conexão de ExpressRoute que roteia para o site local por meio de um domínio de roteamento de emparelhamento privado.
- O datacenter local tem uma conexão de circuito do ExpressRoute por meio de uma borda de parceiro em Hong Kong.
- Todo o roteamento é controlado por meio de UDR (tabelas de rotas do Azure).
- Todo o tráfego de saída entre vNets ou o datacenter local é roteado por meio do NVA.

### <a name="hub-and-spoke-peering-settings"></a>Configurações de emparelhamento de Hub e spoke

#### <a name="spoke-to-hub"></a>Spoke para hub

**Direção** | **Definição** | **Status**
--- | --- | ---
Spoke para hub | Permitir endereço de rede virtual | Enabled
Spoke para hub | Permitir tráfego reencaminhado | Enabled
Spoke para hub | Permitir trânsito de gateway | Desativado
Spoke para hub | Usar remover gateways | Enabled

 ![Configuração de emparelhamento de spoke para Hub](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>Hub para spoke

**Direção** | **Definição** | **Status**
--- | --- | ---
Hub para spoke | Permitir endereço de rede virtual | Enabled
Hub para spoke | Permitir tráfego reencaminhado | Enabled
Hub para spoke | Permitir trânsito de gateway | Enabled
Hub para spoke | Usar remover gateways | Desativado

 ![Configuração de emparelhamento de Hub para spoke](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Etapas de exemplo

Em nosso exemplo, o seguinte deve ocorrer ao habilitar a replicação para VMs do Azure na rede de origem:

1. Você [habilita a replicação](azure-to-azure-tutorial-enable-replication.md) para uma VM.
2. Site Recovery criará vNets de réplica, sub-redes e sub-redes de gateway na região de destino.
3. Site Recovery cria mapeamentos entre as redes de origem e as redes de destino de réplica que ele cria.
4. Você cria manualmente gateways de rede virtual, conexões de gateway de rede virtual, emparelhamento de rede virtual ou quaisquer outros recursos de rede ou conexões.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Fazer failover de VMs do Azure ao usar o ExpressRoute

Após a falha de VMs do Azure para a região do Azure de destino usando Site Recovery, você pode acessá-las usando o [emparelhamento privado](../expressroute/expressroute-circuit-peerings.md#privatepeering)do ExpressRoute.

- Você precisa conectar o ExpressRoute à vNet de destino com uma nova conexão. A conexão de ExpressRoute existente não é automaticamente transferida.
- A maneira na qual você configura sua conexão do ExpressRoute com a vNet de destino depende da sua topologia do ExpressRoute.


### <a name="access-with-two-circuits"></a>Acesso com dois circuitos

#### <a name="two-circuits-with-two-peering-locations"></a>Dois circuitos com dois locais de emparelhamento

Essa configuração ajuda a proteger os circuitos do ExpressRoute contra desastres regionais. Se o seu local de emparelhamento primário falhar, as conexões poderão continuar a partir do outro local.

- O circuito conectado ao ambiente de produção geralmente é o primário. O circuito secundário normalmente tem menor largura de banda, o que pode ser aumentado se ocorrer um desastre.
- Após o failover, você pode estabelecer conexões do circuito de ExpressRoute secundário para a vNet de destino. Como alternativa, você pode ter conexões configuradas e prontas em caso de desastre, para reduzir o tempo de recuperação geral.
- Com conexões simultâneas com vNets primária e de destino, verifique se o roteamento local usa apenas o circuito secundário e a conexão após o failover.
- O vNets de origem e de destino podem receber novos endereços IP ou manter os mesmos, após o failover. Em ambos os casos, as conexões secundárias podem ser estabelecidas antes do failover.


#### <a name="two-circuits-with-single-peering-location"></a>Dois circuitos com um único local de emparelhamento

Essa configuração ajuda a proteger contra falhas do circuito de ExpressRoute primário, mas não se o local de emparelhamento de ExpressRoute único ficar inativo, afetando ambos os circuitos.

- Você pode ter conexões simultâneas do datacenter local para a vNEt de origem com o circuito primário e para a vNet de destino com o circuito secundário.
- Com conexões simultâneas para primária e destino, verifique se o roteamento local usa apenas o circuito secundário e a conexão após o failover.
-   Você não pode conectar ambos os circuitos à mesma vNet quando os circuitos são criados no mesmo local de emparelhamento.

### <a name="access-with-a-single-circuit"></a>Acesso com um único circuito

Nessa configuração, há apenas um circuito do Expressroute. Embora o circuito tenha uma conexão redundante no caso de um falhar, um único circuito de rota não fornecerá resiliência se a região de emparelhamento falhar. Tenha em atenção que:

- Você pode replicar VMs do Azure para qualquer região do Azure na [mesma localização geográfica](azure-to-azure-support-matrix.md#region-support). Se a região do Azure de destino não estiver no mesmo local que a origem, você precisará habilitar o ExpressRoute Premium se estiver usando um único circuito do ExpressRoute. Saiba mais sobre os [locais do expressroute](../expressroute/expressroute-locations.md) e os [preços do expressroute](https://azure.microsoft.com/pricing/details/expressroute/).
- Você não pode conectar vNets de origem e de destino simultaneamente ao circuito se o mesmo espaço de endereço IP for usado na região de destino. Neste cenário:    
    -  Desconecte a conexão no lado da origem e, em seguida, estabeleça a conexão do lado de destino. Essa alteração de conexão pode ser inserida no script como parte de um plano de recuperação Site Recovery. Tenha em atenção que:
        - Em uma falha regional, se a região primária estiver inacessível, a operação de desconexão poderá falhar. Isso poderia afetar a criação da conexão para a região de destino.
        - Se você tiver criado a conexão na região de destino e a região primária for recuperada posteriormente, você poderá enfrentar descartes de pacotes se duas conexões simultâneas tentarem se conectar ao mesmo espaço de endereço.
        - Para evitar isso, encerre a conexão primária imediatamente.
        - Após o failback de VM para a região primária, a conexão primária poderá ser estabelecida novamente, depois que você desconectar a conexão secundária.
-   Se um espaço de endereço diferente for usado na vNet de destino, você poderá se conectar simultaneamente à origem e ao vNets de destino do mesmo circuito de ExpressRoute.


## <a name="failover-example"></a>Exemplo de failover

Em nosso exemplo, estamos usando a seguinte topologia:

- Dois circuitos de ExpressRoute diferentes em dois locais de emparelhamento diferentes.
- Reter endereços IP privados para as VMs do Azure após o failover.
- A região de recuperação de destino é o sudeste da Ásia do Azure.
- Uma conexão de circuito do ExpressRoute secundário é estabelecida por meio de uma borda de parceiro em Cingapura.

Para uma topologia simples que usa um único circuito do ExpressRoute, com o mesmo endereço IP após o failover, [Leia este artigo](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover).

### <a name="example-steps"></a>Etapas de exemplo
Para automatizar a recuperação neste exemplo, veja o que você precisa fazer:

1. Siga as etapas para configurar a replicação.
2. Faça failover [das VMs do Azure](azure-to-azure-tutorial-failover-failback.md), com estas etapas adicionais durante ou após o failover.

    a. Crie o gateway de ExpressRoute do Azure na VNet do Hub região de destino. Isso é necessário para conectar a vNet do hub de destino ao circuito do ExpressRoute.

    b. Crie a conexão da vNet do hub de destino para o circuito de ExpressRoute de destino.

    c. Configure os emparelhamentos de VNet entre o Hub da região de destino e as redes virtuais spoke. As propriedades de emparelhamento na região de destino serão as mesmas da região de origem.

    d. Configure o UDRs na VNet do Hub e os dois VNets de spoke.

    - As propriedades do UDRs do lado de destino são as mesmas que as do lado da origem ao usar os mesmos endereços IP.
    - Com endereços IP de destino diferentes, o UDRs deve ser modificado de acordo.


As etapas acima podem ser inseridas no script como parte de um [plano de recuperação](site-recovery-create-recovery-plans.md). Dependendo dos requisitos de tempo de recuperação e conectividade do aplicativo, as etapas acima também podem ser concluídas antes de iniciar o failover.

#### <a name="after-recovery"></a>Após a recuperação

Depois de recuperar as VMs e concluir a conectividade, o ambiente de recuperação é o seguinte.

![Local para Azure com ExpressRoute após o failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como usar [planos de recuperação](site-recovery-create-recovery-plans.md) para automatizar o failover de aplicativo.
