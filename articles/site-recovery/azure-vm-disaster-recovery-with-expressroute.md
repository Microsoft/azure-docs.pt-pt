---
title: Integrar a recuperação de desastres azure Azure VM com recuperação do site Azure
description: Descreve como configurar a recuperação de desastres para os VMs Azure usando a Recuperação do Site Azure e a Azure ExpressRoute
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: bf12a5b7850a56d945e1082be6c522c31738669c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954087"
---
# <a name="integrate-expressroute-with-disaster-recovery-for-azure-vms"></a>Integrar a ExpressRoute com recuperação de desastres para Os VMs Azure


Este artigo descreve como integrar o Azure ExpressRoute com a Recuperação do [Site Azure,](site-recovery-overview.md)quando você configura a recuperação de desastres para Os VMs Azure para uma região secundária de Azure.

A Recuperação do Site permite a recuperação de desastres de VMs Azure replicando dados de VM Azure para o Azure.

- Se os VMs Azure utilizarem [discos geridos pelo Azure,](../virtual-machines/windows/managed-disks-overview.md)os dados vm são replicados para um disco gerido replicado na região secundária.
- Se os VMs Azure não utilizarem discos geridos, os dados vm são replicados numa conta de armazenamento Azure.
- Os pontos finais de replicação são públicos, mas o tráfego de replicação para Os VMs Azure não atravessa a internet.

O ExpressRoute permite-lhe estender as redes no local à nuvem do Microsoft Azure através de uma ligação privada, facilitada por um fornecedor de conectividade. Se tiver o ExpressRoute configurado, integra-se com a Recuperação do Site da seguinte forma:

- **Durante a replicação entre as regiões de Azure**: O tráfego de replicação para a recuperação de desastres do Azure VM é apenas dentro de Azure, e a ExpressRoute não é necessária nem usada para replicação. No entanto, se estiver a ligar-se de um local no local para os VMs Azure no local principal do Azure, há uma série de questões a ter em conta quando está a preparar a recuperação de desastres para os VMs Azure.
- **Falha entre as regiões de Azure**: Quando ocorrem interrupções, falha-se sobre os VMs Azure da região primária para secundária de Azure. Depois de ter falhado uma região secundária, há uma série de medidas a tomar para aceder aos VMs Azure na região secundária utilizando a ExpressRoute.


## <a name="before-you-begin"></a>Antes de começar

Antes de começar, certifique-se de compreender os seguintes conceitos:

- [Circuitos](../expressroute/expressroute-circuit-peerings.md) ExpressRoute
- [Domínios de encaminhamento](../expressroute/expressroute-circuit-peerings.md#routingdomains) ExpressRoute
- [Localizações](../expressroute/expressroute-locations.md)ExpressRoute .
- Arquitetura de [replicação](azure-to-azure-architecture.md) Azure VM
- Como [configurar](azure-to-azure-tutorial-enable-replication.md) a replicação para Os VMs Azure.
- Como [falhar sobre](azure-to-azure-tutorial-failover-failback.md) os VMs Azure.


## <a name="general-recommendations"></a>Recomendações gerais

Para as melhores práticas, e para garantir objetivos eficientes de tempo de recuperação (RTOs) para recuperação de desastres, recomendamos que faça o seguinte quando configurar a Recuperação do Site para integrar com a ExpressRoute:

- Fornecer componentes de rede antes de falhar numa região secundária:
    - Quando ativa a replicação para VMs Azure, a Recuperação do Site pode implementar automaticamente recursos de rede, tais como redes, subredes e gateways na região-alvo do Azure, com base nas definições da rede de origem.
    - A Recuperação do Site não pode configurar automaticamente recursos de rede, tais como gateways VNet.
    - Recomendamos que disponibilize estes recursos adicionais de rede antes da falha. Um pequeno tempo de inatividade está associado a esta implementação, e pode afetar o tempo de recuperação global, se não o contabilizou durante o planeamento de implementação.
- Executar exercícios regulares de recuperação de desastres:
    - Um teste valida a sua estratégia de replicação sem perda de dados ou tempo de inatividade e não afeta o seu ambiente de produção. Ajuda a evitar problemas de configuração de última hora que podem afetar negativamente o RTO.
    - Quando executa uma falha de teste para a broca, recomendamos que utilize uma rede Azure VM separada, em vez da rede predefinida que é configurada quando ativa a replicação.
- Utilize diferentes espaços de endereçoIP se tiver um único circuito ExpressRoute.
    - Recomendamos que utilize um espaço de endereço IP diferente para a rede virtual alvo. Isto evita problemas ao estabelecer ligações durante as interrupções regionais.
    - Se não puder utilizar um espaço de endereço separado, certifique-se de executar o teste de recuperação de desastres falha numa rede de teste separada com diferentes endereços IP. Não é possível ligar dois VNets com espaço de endereço IP sobreposto ao mesmo circuito ExpressRoute.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Replicar VMs Azure ao utilizar o ExpressRoute


Se quiser configurar a replicação para Os VMs Azure num local primário, e estiver a ligar-se a estes VMs do seu site no local sobre a ExpressRoute, eis o que precisa de fazer:

1. [Ativar a replicação](azure-to-azure-tutorial-enable-replication.md) para cada VM Azure.
2. Opcionalmente, deixe a Recuperação do Site configurar a rede:
    - Quando configura e permite a replicação, a Recuperação do Site configura redes, subredes e subredes de gateway na região-alvo de Azure, para corresponder às da região de origem. A Recuperação do Site também mapeia entre a fonte e as redes virtuais alvo.
    - Se não quiser que a Recuperação do Site o faça automaticamente, crie os recursos de rede do lado do alvo antes de ativar a replicação.
3. Criar outros elementos de networking:
    - A Recuperação do Site não cria tabelas de rotas, gateways VNet, ligações de gateway VNet, peering VNet ou outros recursos de networking e conexões na região secundária.
    - É necessário criar estes elementos adicionais de networking na região secundária, a qualquer momento antes de executar uma falha da região primária.
    - Pode utilizar planos de [recuperação](site-recovery-create-recovery-plans.md) e scripts de automação para configurar e ligar estes recursos de networking.
1. Se tiver um aparelho virtual de rede (NVA) implantado para controlar o fluxo de tráfego de rede, note que:
    - A rota padrão do sistema azure para a replicação do Azure VM é de 0.0.0.0/0.
    - Tipicamente, as implantações de NVA também definem uma rota padrão (0.0.0.0/0) que força o tráfego de internet de saída a fluir através da NVA. A rota predefinida é utilizada quando não é possível encontrar outra configuração específica da rota.
    - Se for esse o caso, a NVA pode estar sobrecarregada se todo o tráfego de replicação passar pela NVA.
    - A mesma limitação aplica-se também ao utilizar rotas padrão para encaminhamento de todo o tráfego de VM Azure para as instalações.
    - Neste cenário, recomendamos que [crie um ponto final](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) de serviço de rede na sua rede virtual para o serviço Microsoft.Storage, para que o tráfego de replicação não deixe o limite do Azure.

## <a name="replication-example"></a>Exemplo de replicação

Normalmente, as implementações empresariais têm cargas de trabalho divididas em vários VNets Azure, com um centro de conectividade central para conectividade externa com a internet e para sites no local. Um hub e topologia falada é normalmente usado juntamente com expressRoute.

![No local-para-Azure com ExpressRoute antes do fracasso](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Região.** As aplicações são implantadas na região de Azure East Asia.
- **VNets falados**. As aplicações são implementadas em dois vNets falados:
    - **Fonte vNet1:** 10.1.0.0/24.
    - **Fonte vNet2**: 10.2.0.0/24.
    - Cada rede virtual falada está ligada ao **Hub vNet**.
- **Hub vNet**. Há um hub vNet **Source Hub vNet:** 10.10.10.0/24.
  - Este hub vNet atua como porteiro.
  - Todas as comunicações através de subredes passam por este centro.
    - **Redes de hub vNet**. O hub vNet tem duas subredes:
    - **Sub-rede NVA:** 10.10.10.0/25. Esta sub-rede contém um NVA (10.10.10.10.10).
    - **Sub-rede gateway:** 10.10.10.128/25. Esta sub-rede contém um gateway ExpressRoute ligado a uma ligação ExpressRoute que se direciona para o local através de um domínio de encaminhamento de observação privado.
- O centro de dados no local tem uma ligação de circuito ExpressRoute através de uma borda de parceiro em Hong Kong.
- Todo o encaminhamento é controlado através de mesas de rota Azure (UDR).
- Todo o tráfego de saída entre vNets, ou para o centro de dados no local é encaminhado através da NVA.

### <a name="hub-and-spoke-peering-settings"></a>Hub e conjuntos de espreitação falado

#### <a name="spoke-to-hub"></a>Spoke para hub

**Direção** | **Definição** | **Estado**
--- | --- | ---
Spoke para hub | Permitir endereço de rede virtual | Ativado
Spoke para hub | Permitir o tráfego encaminhada | Ativado
Spoke para hub | Permitir o trânsito de gateway | Desativado
Spoke para hub | Utilize os portais de remoção | Ativado

 ![Falou com a configuração de peering do hub](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>Hub para spoke

**Direção** | **Definição** | **Estado**
--- | --- | ---
Hub para spoke | Permitir endereço de rede virtual | Ativado
Hub para spoke | Permitir o tráfego encaminhada | Ativado
Hub para spoke | Permitir o trânsito de gateway | Ativado
Hub para spoke | Utilize os portais de remoção | Desativado

 ![Hub para configuração de peering falado](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Passos de exemplo

No nosso exemplo, deve acontecer o seguinte ao permitir a replicação para VMs Azure na rede de origem:

1. [Permite a replicação](azure-to-azure-tutorial-enable-replication.md) de um VM.
2. A Recuperação do Site criará réplicas de vNets, subredes e subredes de gateway na região alvo.
3. A Recuperação do Site cria mapeamentos entre as redes de origem e as redes-alvo de réplica que cria.
4. Cria manualmente gateways de rede virtuais, ligações de gateway de rede virtuais, peering de rede virtual ou quaisquer outros recursos ou ligações de rede.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Falhar sobre os VMs Azure ao utilizar o ExpressRoute

Depois de falhar os VMs Azure na região-alvo do Azure utilizando a Recuperação do Site, pode aceder-lhes através de [um peering privado](../expressroute/expressroute-circuit-peerings.md#privatepeering)ExpressRoute .

- Tem de ligar o ExpressRoute ao target vNet com uma nova ligação. A ligação ExpressRoute existente não é transferida automaticamente.
- A forma como configura a sua ligação ExpressRoute ao alvo vNet depende da sua topologia ExpressRoute.


### <a name="access-with-two-circuits"></a>Acesso com dois circuitos

#### <a name="two-circuits-with-two-peering-locations"></a>Dois circuitos com dois locais de observação

Esta configuração ajuda a proteger os circuitos ExpressRoute contra desastres regionais. Se a sua localização principal de observação descer, as ligações podem continuar a partir do outro local.

- O circuito ligado ao ambiente de produção é geralmente o principal. O circuito secundário normalmente tem menor largura de banda, o que pode ser aumentado se ocorrer um desastre.
- Após a falha, pode estabelecer ligações desde o circuito secundário expressRoute até ao alvo vNet. Em alternativa, pode ter ligações configuradas e prontas em caso de catástrofe, para reduzir o tempo de recuperação global.
- Com ligações simultâneas a vNets primários e alvo, certifique-se de que o seu encaminhamento no local utiliza apenas o circuito secundário e a ligação após a falha.
- A fonte e os vNets alvo podem receber novos endereços IP, ou manter os mesmos, após a falha. Em ambos os casos, as ligações secundárias podem ser estabelecidas antes da falha.


#### <a name="two-circuits-with-single-peering-location"></a>Dois circuitos com localização única de observação

Esta configuração ajuda a proteger contra falhas do circuito primário expressRoute, mas não se a localização de peering do ExpressRoute descer, impactando ambos os circuitos.

- Pode ter ligações simultâneas desde o datacenter no local até fonte vNEt com o circuito primário, e para o alvo vNet com o circuito secundário.
- Com ligações simultâneas ao primário e ao alvo, certifique-se de que o encaminhamento no local utiliza apenas o circuito secundário e a ligação após a falha.
-   Não é possível ligar os dois circuitos ao mesmo vNet quando os circuitos são criados no mesmo local de observação.

### <a name="access-with-a-single-circuit"></a>Acesso com um único circuito

Nesta configuração só há um circuito expressroute. Embora o circuito tenha uma ligação redundante no caso de um cair, um único circuito de rota não fornecerá resiliência se a sua região de observação descer. Tenha em atenção que:

- Pode replicar VMs Azure para qualquer região de Azure na [mesma localização geográfica.](azure-to-azure-support-matrix.md#region-support) Se a região-alvo do Azure não estiver no mesmo local que a fonte, tem de ativar o ExpressRoute Premium se estiver a utilizar um único circuito ExpressRoute. Saiba mais sobre [as localizações](../expressroute/expressroute-locations.md) do ExpressRoute e [os preços expressRoute.](https://azure.microsoft.com/pricing/details/expressroute/)
- Não é possível ligar as redes de origem e os vNets de destino simultaneamente ao circuito se o mesmo espaço de endereço IP for utilizado na região alvo. Neste cenário:    
    -  Desligue a ligação lateral da fonte e, em seguida, estabeleça a ligação lateral do alvo. Esta alteração de ligação pode ser escrita como parte de um plano de recuperação de site. Tenha em atenção que:
        - Numa falha regional, se a região primária estiver inacessível, a operação de desconexão poderá falhar. Isto poderia ter impacto na criação de ligação à região-alvo.
        - Se criou a ligação na região alvo, e a região primária recuperar mais tarde, poderá experimentar gotas de pacote se duas ligações simultâneas tentarem ligar-se ao mesmo espaço de endereço.
        - Para evitar isto, termine imediatamente a ligação primária.
        - Depois de a VM falhar na região primária, a ligação primária pode voltar a ser estabelecida, depois de desligar a ligação secundária.
-   Se forem utilizados diferentes espaços de endereço no vNet alvo, pode simultaneamente ligar-se à fonte e alvo vNets do mesmo circuito ExpressRoute.


## <a name="failover-example"></a>Exemplo de failover

No nosso exemplo, estamos a usar a seguinte topologia:

- Dois circuitos expressroute diferentes em dois locais diferentes de observação.
- Mantenha endereços IP privados para os VMs Azure após a falha.
- A região de recuperação alvo é azure SouthEast Asia.
- Uma ligação secundária do circuito ExpressRoute é estabelecida através de uma borda de parceiro em Singapura.

Para uma topologia simples que usa um único circuito ExpressRoute, com o mesmo endereço IP após o failover, [reveja este artigo](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover).

### <a name="example-steps"></a>Passos de exemplo
Para automatizar a recuperação neste exemplo, eis o que precisa de fazer:

1. Siga os passos para configurar a replicação.
2. [Fail over the Azure VMs,](azure-to-azure-tutorial-failover-failback.md)with these steps adicionais during or after the failover.

    a. Crie o Portal azure ExpressRoute no centro da região alvo VNet. Isto é necessário para ligar o hub-alvo vNet ao circuito ExpressRoute.

    b. Crie a ligação desde o hub-alvo vNet até ao circuito target ExpressRoute.

    c. Criar os vislumrementos vnet entre o centro da região alvo e as redes virtuais faladas. As propriedades de observação na região alvo serão as mesmas que as da região-fonte.

    d. Instale os UDRs no centro VNet, e os dois falaram VNets.

    - As propriedades dos UDRs do lado alvo são as mesmas que as do lado da fonte quando utilizam os mesmos endereços IP.
    - Com diferentes endereços IP-alvo, os UDRs devem ser modificados em conformidade.


Os passos acima podem ser escritos como parte de um plano de [recuperação.](site-recovery-create-recovery-plans.md) Dependendo dos requisitos de tempo de conectividade e recuperação da aplicação, os passos acima podem também ser concluídos antes de iniciar a falha.

#### <a name="after-recovery"></a>Após recuperação

Depois de recuperar os VMs e completar a conectividade, o ambiente de recuperação é o seguinte.

![No local-para-Azure com ExpressRoute após Failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a utilização de planos de [recuperação](site-recovery-create-recovery-plans.md) para automatizar falhas na app.
