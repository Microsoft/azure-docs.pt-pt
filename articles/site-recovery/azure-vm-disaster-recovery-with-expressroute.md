---
title: Integre a recuperação de desastres Azure ExpressRoute Azure VM com recuperação do local de Azure
description: Descreve como configurar a recuperação de desastres para VMs Azure usando Azure Site Recovery e Azure ExpressRoute
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 0e1f670f2ba5ad31f29d56b2de40acd6e2bf18a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88654383"
---
# <a name="integrate-expressroute-with-disaster-recovery-for-azure-vms"></a>Integrar ExpressRoute com recuperação de desastres para VMs Azure


Este artigo descreve como integrar o Azure ExpressRoute com [Azure Site Recovery,](site-recovery-overview.md)quando configurar a recuperação de desastres para os VMs Azure para uma região secundária de Azure.

A Recuperação do Site permite a recuperação de desastres dos VMs Azure replicando os dados Azure VM para a Azure.

- Se os VMs do Azure utilizarem [discos geridos aZure,](../virtual-machines/managed-disks-overview.md)os dados VM são replicados num disco gerido replicado na região secundária.
- Se os VMs do Azure não utilizarem discos geridos, os dados VM são replicados numa conta de armazenamento Azure.
- Os pontos finais de replicação são públicos, mas o tráfego de replicação de VMs Azure não atravessa a internet.

O ExpressRoute permite-lhe estender as redes no local para a nuvem Microsoft Azure através de uma ligação privada, facilitada por um fornecedor de conectividade. Se tiver o ExpressRoute configurado, integra-se com a Recuperação do Local da seguinte forma:

- **Durante a replicação entre as regiões de Azure**: O tráfego de replicação para a recuperação de desastres Azure VM está apenas dentro de Azure, e ExpressRoute não é necessário ou usado para replicação. No entanto, se estiver a ligar-se de um local ao Azure VMs no local primário de Azure, há uma série de questões a ter em conta quando está a preparar uma recuperação de desastres para os VMs Azure.
- **Falha entre as regiões de Azure**: Quando ocorrem interrupções, falha-se sobre os VMs do Azure da região primária para a região secundária de Azure. Depois de ter falhado numa região secundária, há uma série de medidas a tomar para aceder aos VMs Azure na região secundária utilizando o ExpressRoute.


## <a name="before-you-begin"></a>Antes de começar

Antes de começar, certifique-se de compreender os seguintes conceitos:

- Circuitos [](../expressroute/expressroute-circuit-peerings.md) ExpressRoute
- Domínios [de encaminhamento](../expressroute/expressroute-circuit-peerings.md#routingdomains) ExpressRoute
- Localizações [ExpressRoute](../expressroute/expressroute-locations.md).
- Arquitetura de [replicação](azure-to-azure-architecture.md) Azure VM
- Como [configurar](azure-to-azure-tutorial-enable-replication.md) a replicação para VMs Azure.
- Como [falhar sobre](azure-to-azure-tutorial-failover-failback.md) os VMs da Azure.


## <a name="general-recommendations"></a>Recomendações gerais

Para as melhores práticas, e para garantir objetivos eficientes de tempo de recuperação (RTOs) para recuperação de desastres, recomendamos que faça o seguinte quando configurar a Recuperação do Local para integrar com o ExpressRoute:

- Provisão de componentes de rede antes de ser insuportado para uma região secundária:
    - Quando ativa a replicação de VMs Azure, a Recuperação do Site pode implantar automaticamente recursos de rede, tais como redes, sub-redes e gateways na região de Azure alvo, com base nas definições de rede de fontes.
    - A Recuperação do Site não pode configurar automaticamente recursos de rede, tais como gateways VNet.
    - Recomendamos que asse estes recursos adicionais de rede antes do fracasso. Um pequeno tempo de inatividade está associado a esta implementação, e pode afetar o tempo de recuperação global, se você não o contabilizar durante o planeamento de implementação.
- Executar exercícios regulares de recuperação de desastres:
    - Um teste valida a sua estratégia de replicação sem perda de dados ou tempo de inatividade e não afeta o seu ambiente de produção. Ajuda a evitar problemas de configuração de última hora que podem impactar negativamente o RTO.
    - Quando executar uma falha de teste para a broca, recomendamos que utilize uma rede VM Azure separada, em vez da rede padrão que está configurada quando ativa a replicação.
- Utilize diferentes espaços de endereço IP se tiver um único circuito ExpressRoute.
    - Recomendamos que utilize um espaço de endereço IP diferente para a rede virtual alvo. Isto evita problemas ao estabelecer ligações durante as interrupções regionais.
    - Se não puder utilizar um espaço de endereço separado, certifique-se de que executará o teste de teste de recuperação de desastres numa rede de teste separada com diferentes endereços IP. Não é possível ligar dois VNets com espaço de endereço IP sobreposto ao mesmo circuito ExpressRoute.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Replicar VMs Azure ao utilizar o ExpressRoute


Se quiser configurar a replicação de VMs Azure num local primário, e estiver a ligar-se a estes VMs do seu site no local sobre o ExpressRoute, eis o que precisa de fazer:

1. [Ativar a replicação](azure-to-azure-tutorial-enable-replication.md) de cada Azure VM.
2. Opcionalmente, deixe a Recuperação do Site configurar a rede:
    - Ao configurar e permitir a replicação, a Recuperação do Site configura redes, sub-redes e sub-redes de gateway na região de Azure alvo, para combinar com as da região de origem. A Recuperação do Site também mapeia entre a origem e as redes virtuais alvo.
    - Se não quiser que a Recuperação do Site o faça automaticamente, crie os recursos de rede do lado alvo antes de ativar a replicação.
3. Criar outros elementos de rede:
    - A Recuperação do Site não cria tabelas de rotas, gateways VNet, ligações de gateway VNet, observação de VNet ou outros recursos e ligações de rede na região secundária.
    - É necessário criar estes elementos de rede adicionais na região secundária, a qualquer momento antes de executar uma falha da região primária.
    - Pode utilizar [planos de recuperação](site-recovery-create-recovery-plans.md) e scripts de automatização para configurar e ligar estes recursos de rede.
1. Se tiver um aparelho virtual de rede (NVA) implantado para controlar o fluxo do tráfego de rede, note que:
    - A rota do sistema padrão do Azure para a replicação do Azure VM é de 0.0.0.0/0.
    - Tipicamente, as implementações da NVA também definem uma rota padrão (0.0.0.0/0) que força o tráfego de internet de saída a fluir através da NVA. A rota predefinida é utilizada quando não é possível encontrar outra configuração específica da rota.
    - Se for esse o caso, o NVA poderá estar sobrecarregado se todo o tráfego de replicação passar pela NVA.
    - A mesma limitação também se aplica quando se utilizam as vias predefinidas para encaminhar todo o tráfego Azure VM para as implementações no local.
    - Neste cenário, recomendamos que [crie um ponto final de serviço](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) de rede na sua rede virtual para o serviço Microsoft.Storage, para que o tráfego de replicação não saia do limite de Azure.

## <a name="replication-example"></a>Exemplo de replicação

Normalmente, as implementações das empresas têm cargas de trabalho divididas em vários VNets Azure, com um centro de conectividade central para conectividade externa à internet e a sites no local. Um hub e topologia falada é tipicamente usado juntamente com ExpressRoute.

![No local-a-Azure com ExpressRoute antes do failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Região**. As aplicações são implementadas na região da Ásia Oriental de Azure.
- **Falou vNets.** As aplicações são implementadas em dois vNets falados:
    - **Fonte vNet1**: 10.1.0.0/24.
    - **Fonte vNet2**: 10.2.0.0/24.
    - Cada rede virtual falada está ligada ao **Hub vNet.**
- **Hub vNet**. Há um hub vNet **Source Hub vNet**: 10.10.10.0/24.
  - Este hub vNet funciona como o porteiro.
  - Todas as comunicações através das sub-redes passam por este centro.
    - **Subesí redes hub vNet**. O hub vNet tem duas sub-redes:
    - **Sub-rede NVA**: 10.10.10.0/25. Esta sub-rede contém um NVA (10.10.10.10).
    - **Sub-rede gateway**: 10.10.10.128/25. Esta sub-rede contém um gateway ExpressRoute ligado a uma ligação ExpressRoute que liga para o local através de um domínio de encaminhamento de perensamento privado.
- O datacenter no local tem uma ligação de circuito ExpressRoute através de uma borda parceira em Hong Kong.
- Todo o encaminhamento é controlado através das tabelas de rota Azure (UDR).
- Todo o tráfego de saída entre vNets, ou para o datacenter no local é encaminhado através da NVA.

### <a name="hub-and-spoke-peering-settings"></a>Hub e definições de observação de olhos

#### <a name="spoke-to-hub"></a>Spoke para hub

**Direção** | **Definição** | **Estado**
--- | --- | ---
Spoke para hub | Permitir endereço de rede virtual | Ativado
Spoke para hub | Permitir tráfego reencaminhado | Ativado
Spoke para hub | Permitir o trânsito de gateway | Desativado
Spoke para hub | Use gateways de remoção | Ativado

 ![Falei com a configuração de peering hub](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>Hub para spoke

**Direção** | **Definição** | **Estado**
--- | --- | ---
Hub para spoke | Permitir endereço de rede virtual | Ativado
Hub para spoke | Permitir tráfego reencaminhado | Ativado
Hub para spoke | Permitir o trânsito de gateway | Ativado
Hub para spoke | Use gateways de remoção | Desativado

 ![Hub para a configuração de peering falado](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Passos de exemplo

No nosso exemplo, o seguinte deve acontecer ao permitir a replicação de VMs Azure na rede de origem:

1. Permite [a replicação](azure-to-azure-tutorial-enable-replication.md) de um VM.
2. A Recuperação do Site criará réplicas de vNets, sub-redes e sub-redes de gateway na região alvo.
3. A Recuperação do Site cria mapeamentos entre as redes de origem e as redes-alvo de réplica que cria.
4. Cria manualmente gateways de rede virtuais, ligações de gateway de rede virtuais, observação de rede virtual ou quaisquer outros recursos ou ligações de rede.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Falha sobre os VMs Azure ao utilizar o ExpressRoute

Depois de falhar os VMs do Azure para a região alvo de Azure utilizando a Recuperação do Local, pode aceder-lhes através do [espremiamento privado](../expressroute/expressroute-circuit-peerings.md#privatepeering)ExpressRoute .

- Tem de ligar o ExpressRoute ao target vNet com uma nova ligação. A ligação ExpressRoute existente não é transferida automaticamente.
- A forma como configura a sua ligação ExpressRoute ao vNet alvo depende da sua topologia ExpressRoute.


### <a name="access-with-two-circuits"></a>Acesso com dois circuitos

#### <a name="two-circuits-with-two-peering-locations"></a>Dois circuitos com dois locais de observação

Esta configuração ajuda a proteger os circuitos ExpressRoute contra desastres regionais. Se o seu local principal de observação diminuir, as ligações podem continuar a partir do outro local.

- O circuito ligado ao ambiente de produção é geralmente o principal. O circuito secundário normalmente tem largura de banda mais baixa, que pode ser aumentada se ocorrer um desastre.
- Após o failover, pode estabelecer ligações do circuito ExpressRoute secundário ao vNet alvo. Em alternativa, pode ter ligações configuradas e prontas em caso de desastre, para reduzir o tempo de recuperação global.
- Com ligações simultâneas a vNets primários e alvo, certifique-se de que o seu encaminhamento no local apenas utiliza o circuito secundário e a ligação após a falha.
- Os vNets de origem e alvo podem receber novos endereços IP, ou manter os mesmos, após o failover. Em ambos os casos, as ligações secundárias podem ser estabelecidas antes do failover.


#### <a name="two-circuits-with-single-peering-location"></a>Dois circuitos com localização de espreitar única

Esta configuração ajuda a proteger contra falhas no circuito expressRoute primário, mas não se o único local de observação ExpressRoute diminuir, impactando ambos os circuitos.

- Pode ter ligações simultâneas desde o datacenter no local até à fonte vNEt com o circuito primário, e ao vNet alvo com o circuito secundário.
- Com ligações simultâneas ao primário e ao alvo, certifique-se de que o encaminhamento no local apenas utiliza o circuito secundário e a ligação após a falha.
-   Não é possível ligar os dois circuitos ao mesmo vNet quando os circuitos são criados no mesmo local de observação.

### <a name="access-with-a-single-circuit"></a>Acesso com um único circuito

Nesta configuração há apenas um circuito Expressroute. Embora o circuito tenha uma ligação redundante no caso de um cair, um único circuito de rota não fornecerá resiliência se a sua região de observação cair. Tenha em atenção que:

- Pode replicar VMs Azure em qualquer região de Azure na [mesma localização geográfica.](azure-to-azure-support-matrix.md#region-support) Se a região de Azure alvo não estiver no mesmo local que a fonte, tem de ativar o ExpressRoute Premium se estiver a utilizar um único circuito ExpressRoute. Saiba mais sobre [as localizações do ExpressRoute](../expressroute/expressroute-locations.md) e [os preços expressRoute](https://azure.microsoft.com/pricing/details/expressroute/).
- Não é possível ligar vNets de origem e alvo simultaneamente ao circuito se o mesmo espaço de endereço IP for utilizado na região alvo. Neste cenário:    
    -  Desligue a ligação lateral da fonte e, em seguida, estabeleça a ligação lateral do alvo. Esta mudança de ligação pode ser escrita como parte de um plano de recuperação do site. Tenha em atenção que:
        - Numa falha regional, se a região primária for inacessível, a operação de desconexão poderá falhar. Isto poderia ter impacto na criação de ligações à região-alvo.
        - Se criou a ligação na região alvo, e a região primária recuperar mais tarde, poderá experimentar quedas de pacotes se duas ligações simultâneas tentarem ligar-se ao mesmo espaço de endereço.
        - Para evitar isto, termine imediatamente a ligação primária.
        - Depois de o VM ter falhado na região primária, a ligação primária pode ser novamente estabelecida, depois de desligar a ligação secundária.
-   Se forem utilizados diferentes espaços de endereço no vNet alvo, pode ligar-se simultaneamente aos vNets de origem e alvo do mesmo circuito ExpressRoute.


## <a name="failover-example"></a>Exemplo de falha

No nosso exemplo, estamos a usar a seguinte topologia:

- Dois circuitos ExpressRoute diferentes em dois locais de observação diferentes.
- Reter endereços IP privados para os VMs Azure após o failover.
- A região de recuperação alvo é Azure SouthEast Asia.
- Uma ligação secundária do circuito ExpressRoute é estabelecida através de uma borda parceira em Singapura.

Para uma topologia simples que utiliza um único circuito ExpressRoute, com o mesmo endereço IP após falha, [reveja este artigo](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover).

### <a name="example-steps"></a>Passos de exemplo
Para automatizar a recuperação neste exemplo, eis o que precisa de fazer:

1. Siga os passos para configurar a replicação.
2. [Falha sobre os VMs Azure,](azure-to-azure-tutorial-failover-failback.md)com estes passos adicionais durante ou após a falha.

    a. Crie o Azure ExpressRoute Gateway no centro da região alvo VNet. Isto é necessário para ligar o hub alvo vNet ao circuito ExpressRoute.

    b. Crie a ligação do hub alvo vNet para o circuito Target ExpressRoute.

    c. Crie os olhos vnet entre o centro da região alvo e falou de redes virtuais. As propriedades de observação na região alvo serão as mesmas que na região de origem.

    d. Instale os UDRs no hub VNet, e os dois falaram VNets.

    - As propriedades dos UDRs laterais-alvo são as mesmas que estão no lado da fonte quando se utilizam os mesmos endereços IP.
    - Com diferentes endereços IP alvo, as UDRs devem ser modificadas em conformidade.


Os passos acima podem ser descritos como parte de um plano de [recuperação](site-recovery-create-recovery-plans.md). Dependendo dos requisitos de conectividade da aplicação e tempo de recuperação, os passos acima também podem ser concluídos antes de iniciar a falha.

#### <a name="after-recovery"></a>Após a recuperação

Depois de recuperar os VMs e completar a conectividade, o ambiente de recuperação é o seguinte.

![No local-para-Azure com ExpressRoute após Failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o uso de [planos de recuperação](site-recovery-create-recovery-plans.md) para automatizar o failover da aplicação.
