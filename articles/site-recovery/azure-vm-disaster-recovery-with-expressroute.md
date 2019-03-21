---
title: Integrar o Azure ExpressRoute com recuperação após desastre para VMs do Azure com o serviço Azure Site Recovery | Documentos da Microsoft
description: Descreve como configurar a recuperação após desastre para VMs do Azure com o Azure Site Recovery e o Azure ExpressRoute
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 4622809f0e261236d6753daf5bb2e00ff814c849
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58087878"
---
# <a name="integrate-azure-expressroute-with-disaster-recovery-for-azure-vms"></a>Integrar o Azure ExpressRoute com recuperação após desastre para VMs do Azure


Este artigo descreve como integrar o Azure ExpressRoute com [do Azure Site Recovery](site-recovery-overview.md), quando configurar a recuperação após desastre para VMs do Azure para uma região secundária do Azure.

Recuperação de sites permite a recuperação após desastre de VMs do Azure através da replicação de dados de VM do Azure para o Azure.

- Se utilizar VMs do Azure [discos geridos do Azure](../virtual-machines/windows/managed-disks-overview.md), dados da VM são replicados para um disco gerido replicado na região secundária.
- Se as VMs do Azure não utilizam discos geridos, os dados da VM são replicados para uma conta de armazenamento do Azure.
- Pontos finais de replicação são públicos, mas o tráfego de replicação de VMs do Azure não cruzam a internet.

O ExpressRoute permite-lhe expandir redes no local para a cloud do Microsoft Azure através de uma ligação privada facilitada por um fornecedor de conectividade. Se tiver o ExpressRoute configurado, ele se integra com o Site Recovery da seguinte forma:

- **Durante a replicação entre regiões do Azure**: Tráfego de replicação para a recuperação após desastre de VM do Azure é apenas no Azure e ExpressRoute não é necessário ou utilizado para replicação. No entanto, se estiver a ligar a partir de um site no local para as VMs do Azure no site primário do Azure, há uma série de problemas a ter em consideração quando estiver configurando a recuperação após desastre para essas VMs do Azure.
- **Ativação pós-falha entre regiões do Azure**: Quando ocorrem falhas, realizar a ativação pós-falha de VMs do Azure do primário para a região secundária do Azure. Após a ativação pós-falha para uma região secundária, há uma série de passos a efetuar para acessar as VMs do Azure na região secundária através do ExpressRoute.


## <a name="before-you-begin"></a>Antes de começar

Antes de começar, certifique-se de que compreende os seguintes conceitos:

- ExpressRoute [circuitos](../expressroute/expressroute-circuit-peerings.md)
- ExpressRoute [domínios de encaminhamento](../expressroute/expressroute-circuit-peerings.md#routingdomains)
- ExpressRoute [localizações](../expressroute/expressroute-locations.md).
- VM do Azure [arquitetura da replicação](azure-to-azure-architecture.md)
- Como [configurar a replicação](azure-to-azure-tutorial-enable-replication.md) para VMs do Azure.
- Como [efetuar a ativação pós-falha](azure-to-azure-tutorial-failover-failback.md) VMs do Azure.


## <a name="general-recommendations"></a>Recomendações gerais

Para melhor prática e para garantir eficiente objetivos de tempo de recuperação (RTOs) para recuperação após desastre, recomendamos que faça o seguinte quando configurar a recuperação de Site para integrar com o ExpressRoute:

- Aprovisione os componentes de rede antes da ativação pós-falha para uma região secundária:
    - Quando ativa a replicação das VMs do Azure, o Site Recovery pode implementar automaticamente recursos de rede como redes, sub-redes e gateways no destino região do Azure, com base nas definições de rede de origem.
    - Recuperação de sites não é possível configurar automaticamente a recursos de rede, tais como gateways de VNet.
    - Recomendamos que Aprovisiona estes recursos de rede adicionais antes da ativação pós-falha. Um pequeno período de indisponibilidade é associado a esta implementação, e pode afetar o tempo de recuperação geral, se imprevista-lo durante o planejamento de implantação.
- Execute testes de recuperação de desastres regulares:
    - Um teste valida a sua estratégia de replicação sem perda de dados ou tempo de inatividade e não afeta o seu ambiente de produção. Ele ajuda a evitar problemas de configuração de última hora que podem afetar negativamente o RTO.
    - Ao executar uma ativação pós-falha de teste para a exploração, recomendamos que utilize uma rede de VM do Azure separada, em vez da rede predefinida que é definida quando ativa a replicação.
- Utilize espaços de endereços IP diferentes se tiver um único circuito ExpressRoute.
    - Recomendamos que utilize um espaço de endereços IP diferente para a rede virtual de destino. Isso evita problemas durante o estabelecimento de conexões durante falhas regionais.
    - Se não é possível utilizar um espaço de endereço separados, certifique-se de que executar a ativação de pós-falha de teste de exploração de recuperação do desastre numa rede de teste separada com endereços IP diferentes. Não é possível ligar duas VNets com sobreposição de espaços de endereços IP para o mesmo circuito do ExpressRoute.

## <a name="replicate-azure-vms-when-using-expressroute"></a>Replicar VMs do Azure quando utilizam o ExpressRoute


Se pretende configurar a replicação das VMs do Azure num site primário e estiver a ligar a estas VMs do seu site no local através do ExpressRoute, eis o que precisa fazer:

1. [Ativar a replicação](azure-to-azure-tutorial-enable-replication.md) para cada VM do Azure.
2. Opcionalmente, permitir que a recuperação de Site configurar as redes:
    - Ao configurar e ativar a replicação, Site Recovery configura as sub-redes de gateways, sub-redes e redes no destino região do Azure, que correspondam na região de origem. Recuperação de site também mapeia entre as redes virtuais de origem e de destino.
    - Se não desejar que o Site Recovery para fazê-lo automaticamente, crie os recursos de rede do lado do destino antes de ativar a replicação.
3. Crie outros elementos de rede:
    - Recuperação de sites não cria tabelas de rotas, os gateways de VNet, ligações de gateway de VNet, o VNet peering, ou outros rede recursos e ligações na região secundária.
    - Tem de criar estes elementos de rede adicionais na região secundária, a qualquer momento antes de executar uma ativação pós-falha da região primária.
    - Pode usar [planos de recuperação](site-recovery-create-recovery-plans.md) e scripts de automatização para configurar e ligar estes recursos de rede.
1. Se tiver uma aplicação virtual de rede (NVA) implementada para controlar o fluxo de tráfego de rede, tenha em atenção que:
    - Rota de sistema padrão do Azure para a replicação de VM do Azure é 0.0.0.0/0.
    - Normalmente, as implementações da NVA também definem uma rota predefinida (0.0.0.0/0) que força o tráfego de Internet de saída a fluir através da NVA. A rota padrão é utilizada quando nenhuma outra configuração de rota específica pode ser encontrada.
    - Se for este o caso, a NVA poderá estar sobrecarregada se todo o tráfego de replicação passa através da NVA.
    - A limitação mesmo também se aplica ao usar as rotas predefinidas para encaminhar todo o tráfego de VM do Azure para implementações no local.
    - Neste cenário, recomendamos que [criar um ponto de final de serviço de rede](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) na sua rede virtual para o serviço da Microsoft. Storage, para que o tráfego de replicação não deixe de limites do Azure.

## <a name="replication-example"></a>Exemplo de replicação

Implementações em empresas têm normalmente cargas de trabalho de dividir em várias VNets do Azure, com um concentrador central de conectividade para conectividade externa à internet e para sites no local. Uma topologia hub- and -spoke é normalmente utilizada em conjunto com o ExpressRoute.

![No local-para-Azure com o ExpressRoute antes da ativação pós-falha](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Região**. As aplicações são implementadas na região do Azure Ásia Oriental.
- **Spoke vNets**. Aplicações são implementadas em duas vNets spoke:
    - **Origem vNet1**: 10.1.0.0/24.
    - **Origem vNet2**: 10.2.0.0/24.
    - Cada rede virtual do spoke está ligado à **vNet do Hub**.
- **VNet do hub**. Existe uma vNet do hub **vNet do Hub de origem**: 10.10.10.0/24.
  - Esta vNet do hub atua como o controlador de chamadas.
  - Todas as comunicações entre sub-redes percorrer este hub.
    - Hub vNet sub-redes * *. A vNet do hub tiver duas sub-redes:
    - **Sub-rede da NVA**: 10.10.10.0/25. Esta sub-rede contém uma NVA (10.10.10.10).
    - **A sub-rede de gateway**: 10.10.10.128/25. Esta sub-rede contém um gateway do ExpressRoute ligado a uma ligação do ExpressRoute que encaminha para o site no local através de um domínio de encaminhamento de peering privado.
- O Centro de dados no local tem uma ligação de circuito do ExpressRoute através de um limite de parceiro em RAE de Hong Kong.
- Encaminhamento de todas as é controlada por meio de tabelas de rota do Azure (UDR).
- Todo o tráfego de saída entre vNets ou para o datacenter no local é encaminhado através da NVA.

### <a name="hub-and-spoke-peering-settings"></a>Hub- and -spoke as definições de peering

#### <a name="spoke-to-hub"></a>Spoke para hub

**Direção** | **Definição** | **Estado**
--- | --- | ---
Spoke para hub | Permitir o endereço de rede virtual | Ativado
Spoke para hub | Permitir tráfego reencaminhado | Ativado
Spoke para hub | Permitir trânsito de gateway | Desativado
Spoke para hub | Utilizar gateways de remover | Ativado

 ![-Spoke para configuração de peering do hub](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>Hub para spoke

**Direção** | **Definição** | **Estado**
--- | --- | ---
Hub para spoke | Permitir o endereço de rede virtual | Ativado
Hub para spoke | Permitir tráfego reencaminhado | Ativado
Hub para spoke | Permitir trânsito de gateway | Ativado
Hub para spoke | Utilizar gateways de remover | Desativado

 ![Hub para falava configuração de peering](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>Passos de exemplo

No nosso exemplo, o seguinte deve acontecer quando ativar a replicação das VMs do Azure na rede de origem:

1. [Ativar a replicação](azure-to-azure-tutorial-enable-replication.md) para uma VM.
2. Recuperação de sites irá criar a réplica vNets, sub-redes e sub-redes do gateway na região de destino.
3. Site Recovery cria mapeamentos entre as redes de origem e as redes de destino da réplica que é criado.
4. Criar manualmente gateways de rede virtual, ligações de gateway de rede virtual, peering de rede virtual, ou qualquer outro sistema de rede recursos ou ligações.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>Efetuar a ativação pós-falha de VMs do Azure quando utilizam o ExpressRoute

Depois de failover VMs do Azure para a região do Azure com o Site Recovery de destino, pode acessá-los através do ExpressRoute [peering privado](../expressroute/expressroute-circuit-peerings.md#privatepeering).

- Terá de ligar o ExpressRoute para a vNet de destino com uma nova ligação. A ligação de ExpressRoute existente não é transferida automaticamente.
- A maneira na qual configurou a ligação do ExpressRoute para a vNet de destino depende da sua topologia do ExpressRoute.


### <a name="access-with-two-circuits"></a>Acesso com dois circuitos

#### <a name="two-circuits-with-two-peering-locations"></a>Dois circuitos com duas localizações de peering

Esta configuração ajuda protege os circuitos do ExpressRoute contra que desastre regional. Se a sua localização de peering principal ficar inativa, ligações podem continuar a partir da localização de outra.

- O circuito ligado ao ambiente de produção, normalmente, é o principal. O circuito secundário normalmente tem a menor largura de banda, que pode ser aumentada se ocorrer um desastre.
- Após a ativação pós-falha, pode estabelecer ligações de circuito do ExpressRoute secundário para a vNet de destino. Em alternativa, pode ter ligações configuradas e pronto em caso de desastre, para reduzir o tempo de recuperação total.
- Com ligações simultâneas a ambos os principal e vNets de destino, certifique-se de que no local apenas encaminhamento usa o circuito secundário e a ligação após a ativação pós-falha.
- As vNets de origem e destino podem receber novos endereços IP ou manter os mesmos que, após a ativação pós-falha. Em ambos os casos, as ligações secundárias podem ser estabelecidas antes da ativação pós-falha.


#### <a name="two-circuits-with-single-peering-location"></a>Dois circuitos com uma única localização de peering

Esta configuração ajuda a proteger contra falhas do circuito de ExpressRoute primário, mas não se a localização de peering do ExpressRoute única ficar inativo, afetar ambos os circuitos.

- Pode ter ligações simultâneas a partir do datacenter no local para vNEt de origem com o circuito primário e para a vNet de destino com o circuito secundário.
- Com ligações simultâneas a primária e de destino, certifique-se de que no local apenas encaminhamento utiliza o circuito secundário e a ligação após a ativação pós-falha.
-   Não é possível ligar os dois circuitos para a mesma vNet quando circuitos são criados na mesma localização de peering.

### <a name="access-with-a-single-circuit"></a>Acesso com um único circuito

Nesta configuração, existe apenas um circuito do Expressroute. Apesar do circuito ter uma ligação com redundância de, no caso de um for desativado, um circuito de expressroute único não irá fornecer resiliência se a sua região peering ficar inativo. Tenha em atenção que:

- Pode replicar VMs do Azure para qualquer região do Azure no [mesma localização geográfica](azure-to-azure-support-matrix.md#region-support). Se a região do Azure de destino não estiver na mesma localização como a origem, tem de ativar o ExpressRoute Premium se estiver a utilizar um único circuito ExpressRoute. Saiba mais sobre [localizações do ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) e [preços do ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).
- Não é possível ligar vNets de origem e de destino em simultâneo para o circuito se o mesmo espaço de endereços IP é utilizado na região de destino. Neste cenário:    
    -  Desligue a ligação de lado de origem e, em seguida, estabelecer a ligação de lado de destino. Esta alteração de ligação pode ser colocado em script como parte de um plano de recuperação do Site Recovery. Tenha em atenção que:
        - Numa falha regional, se a região primária não estiver acessível, a operação desligar poderá falhar. Isto poderia afetar a criação da ligação para a região de destino.
        - Se criou a ligação na região de destino e a região primária recupera mais tarde, podem ocorrer quedas de pacotes se duas conexões simultâneas tentam estabelecer ligação ao mesmo espaço de endereço.
        - Para evitar esta situação, encerrar imediatamente a conexão principal.
        - Após a reativação pós-falha VM para a região primária, a conexão principal pode novamente ser estabelecida, depois de desligar a ligação secundária.
-   Se os espaços de endereço diferente é utilizada na vNet de destino, pode ligar em simultâneo para vNets em modo de origem e destino do mesmo circuito do ExpressRoute.


## <a name="failover-example"></a>Exemplo de ativação pós-falha

No nosso exemplo, estamos a utilizar a seguinte topologia:

- Circuitos do ExpressRoute diferentes duas em duas localizações de peering diferentes.
- Endereços IP privados por manter as VMs do Azure após a ativação pós-falha.
- Região de destino de recuperação é Azure Sudeste asiático.
- É estabelecida uma ligação de circuito de ExpressRoute secundária através de um limite de parceiro em Singapura.

Para uma topologia simples que utiliza um único circuito ExpressRoute, com o mesmo endereço IP após a ativação pós-falha, [rever este artigo](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover).

### <a name="example-steps"></a>Passos de exemplo
Para automatizar a recuperação neste exemplo, aqui do que precisa fazer:

1. Siga os passos para configurar a replicação.
2. [Efetuar a ativação pós-falha de VMs do Azure](azure-to-azure-tutorial-failover-failback.md), com estes passos adicionais durante ou após a ativação pós-falha.

    a. Crie o Gateway do ExpressRoute do Azure na VNet do hub de região de destino. Isso é necessário para ligar a vNet do hub de destino para o circuito do ExpressRoute.

    b. Crie a ligação a partir da vNet do hub de destino para o circuito do ExpressRoute de destino.

    c. Configure os peerings de VNet entre o hub a região de destino e redes virtuais indicadas. As propriedades do peering na região de destino serão iguais na região de origem.

    d. Configure as UDRs na VNet do hub e spoke duas VNets.

    - As propriedades do lado do destino UDRs são iguais do lado de origem quando o mesmo IP de através de endereços.
    - Com os endereços IP de destino diferente, as UDRs devem ser modificadas em conformidade.


Os passos acima podem ser colocado em script como parte de um [plano de recuperação](site-recovery-create-recovery-plans.md). Consoante a conectividade de aplicativo e requisitos de tempo de recuperação, os passos acima também podem ser concluídos antes de iniciar a ativação pós-falha.

#### <a name="after-recovery"></a>Após a recuperação

Depois de recuperar as VMs e conectividade de conclusão, o ambiente de recuperação é o seguinte.

![No local-para-Azure com o ExpressRoute após a ativação pós-falha](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como utilizar [planos de recuperação](site-recovery-create-recovery-plans.md) para automatizar a ativação pós-falha de aplicação.
