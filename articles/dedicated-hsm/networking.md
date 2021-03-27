---
title: Considerações de networking - Azure Dedicado HSM | Microsoft Docs
description: Visão geral das considerações de networking aplicáveis às implementações do HSM dedicado à Azure
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 3370389027805cfb5a68b5b0551d14dc31154804
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105611842"
---
# <a name="azure-dedicated-hsm-networking"></a>Rede HSM dedicada Azure

O Azure Dedicado HSM requer um ambiente de networking altamente seguro. Isto é verdade quer seja da nuvem Azure de volta ao ambiente de TI do cliente (no local), usando aplicações distribuídas ou para cenários de alta disponibilidade. O Azure Networking fornece este facto e há quatro áreas distintas que devem ser abordadas.

- Criar dispositivos HSM dentro da sua Rede Virtual (VNet) em Azure
- Ligação no local a recursos baseados na nuvem para a configuração e gestão de dispositivos HSM
- Criação e ligação de redes virtuais para recursos de aplicações interligar e dispositivos HSM
- Ligação de redes virtuais entre regiões para inter-comunicações e também para permitir cenários de elevada disponibilidade

## <a name="virtual-network-for-your-dedicated-hsms"></a>Rede virtual para os seus HSMs dedicados

Os HSMs dedicados estão integrados numa Rede Virtual e colocados na própria rede privada de clientes em Azure. Isto permite o acesso aos dispositivos a partir de máquinas virtuais ou recursos de computação na rede virtual.  
Para obter mais informações sobre a integração dos serviços Azure na rede virtual e as capacidades que fornece, consulte a rede Virtual para documentação [dos serviços Azure.](../virtual-network/virtual-network-for-azure-services.md)

### <a name="virtual-networks"></a>Redes virtuais

Antes de a provisionar um dispositivo HSM dedicado, os clientes terão primeiro de criar uma Rede Virtual em Azure ou usar um que já existe na subscrição dos clientes. A rede virtual define o perímetro de segurança do dispositivo dedicado HSM. Para obter mais informações sobre a criação de redes virtuais, consulte [a documentação da rede virtual.](../virtual-network/virtual-networks-overview.md)

### <a name="subnets"></a>Sub-redes

Sub-redes segmentam a rede virtual em espaços de endereço separados utilizáveis pelos recursos Azure que coloca neles. Os HSMs dedicados são implantados numa sub-rede na rede virtual. Cada dispositivo HSM dedicado que seja implantado na sub-rede do cliente receberá um endereço IP privado a partir desta sub-rede. A sub-rede em que o dispositivo HSM é implantado precisa de ser explicitamente delegada no serviço: Microsoft.HardwareSecurityModules/DEDICADOHSMs. Isto concede determinadas permissões ao serviço HSM para implantação na sub-rede. A delegação aos HSMs dedicados impõe certas restrições políticas à sub-rede. Os Grupos de Segurança de Rede (NSGs) e User-Defined Rotas (UDRs) não são atualmente suportados em sub-redes delegadas. Como resultado, uma vez que uma sub-rede é delegada em HSMs dedicados, ele só pode ser usado para implantar recursos HSM. A implantação de quaisquer outros recursos do cliente na sub-rede falhará.


### <a name="expressroute-gateway"></a>Gateway ExpressRoute

Um requisito da arquitetura atual é a configuração de uma porta de entrada ER na sub-rede de clientes onde é necessário colocar um dispositivo HSM para permitir a integração do dispositivo HSM em Azure. Este gateway ER não pode ser utilizado para ligar locais no local aos dispositivos HSM dos clientes em Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Ligar o seu TI no local ao Azure

Ao criar recursos baseados na nuvem, é um requisito típico para uma ligação privada de volta aos recursos de TI no local. No caso do HSM dedicado, este será predominantemente para o software cliente HSM configurar os dispositivos HSM e também para atividades como backups e puxar registos de HSMs para análise. Um ponto de decisão fundamental aqui é a natureza da ligação, uma vez que existem opções.  A opção mais flexível é a VPN site-to-site, uma vez que provavelmente haverá vários recursos no local que requerem uma comunicação segura com recursos (incluindo HSMs) na nuvem Azure. Isto exigirá que uma organização de clientes tenha um dispositivo VPN para facilitar a ligação. Uma ligação VPN ponto-a-local pode ser utilizada se houver apenas um único ponto final no local, como uma única estação de trabalho administrativa.
Para obter mais informações sobre opções de conectividade, consulte [as opções de planeamento do Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> Neste momento, o ExpressRoute não é uma opção para a ligação aos recursos no local. Note-se também que o Portal ExpressRoute utilizado como acima descrito não se destina a ligações com infraestruturas no local.

### <a name="point-to-site-vpn"></a>VPN ponto-a-local

Uma rede privada virtual de ponto a local é a forma mais simples de ligação segura a um único ponto final no local. Isto pode ser relevante se pretender ter apenas uma estação de trabalho de administração única para HSMs dedicados baseados em Azure.

### <a name="site-to-site-vpn"></a>VPN site a site

Uma Rede Privada Virtual para site permite uma comunicação segura entre os HSMs dedicados baseados em Azure e as suas TI no local. Uma razão para isso é ter uma instalação de reserva para o HSM no local e precisar de uma ligação entre os dois para executar o backup.

## <a name="connecting-virtual-networks"></a>Ligação de redes virtuais

Uma arquitetura típica de implantação para O HSM dedicado começará com uma única rede virtual e sub-rede correspondente na qual os dispositivos HSM são criados e a provisionados. Dentro dessa mesma região, poderia muito bem existirem redes virtuais e sub-redes adicionais para componentes de aplicação que utilizariam o HSM dedicado. Para ativar a comunicação através destas redes, utilizamos o Virtual Network Peering.

### <a name="virtual-network-peering"></a>Peering de rede virtual

Quando existem várias redes virtuais dentro de uma região que precisam de aceder aos recursos uns dos outros, o Virtual Network Peering pode ser usado para criar canais de comunicação seguros entre eles.  O espreitamento de rede virtual fornece não só comunicações seguras, mas também garante uma baixa latência e ligações de alta largura de banda entre os recursos em Azure.

![espreitar a rede](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Ligação através das Regiões de Azure

Os dispositivos HSM têm a capacidade, através de bibliotecas de software, de redirecionar o tráfego para um HSM alternativo. A reorientação do tráfego é útil se os dispositivos falharem ou o acesso a um dispositivo for perdido. Os cenários de insuficiência de nível regional podem ser atenuados através da implantação de HSMs noutras regiões e da comunicação entre redes virtuais entre regiões.

### <a name="cross-region-ha-using-vpn-gateway"></a>Região transversal HA usando gateway VPN

Para aplicações distribuídas globalmente ou para cenários de falha regional de alta disponibilidade, é necessário ligar redes virtuais entre regiões. Com o Azure Dedicado HSM, a alta disponibilidade pode ser alcançada usando um Gateway VPN que fornece um túnel seguro entre as duas redes virtuais. Para obter mais informações sobre as ligações Vnet-to-Vnet utilizando o Gateway VPN, consulte o artigo intitulado [O que é VPN Gateway?](../vpn-gateway/design.md#V2V)

> [!NOTE]
> O perspitamento global da Vnet não está disponível em cenários de conectividade entre regiões com HSMs dedicados neste momento e o gateway VPN deve ser usado em vez disso. 

![O diagrama mostra duas regiões ligadas por dois gateways V P N. Cada região contém redes virtuais espreitadas.](media/networking/global-vnet.png)

## <a name="networking-restrictions"></a>Restrições de rede
> [!NOTE]
> É imposta uma restrição do serviço dedicado ao HSM utilizando a delegação de sub-redes que deve ser considerada na conceção da arquitetura da rede-alvo para uma implantação do HSM. A utilização da delegação de sub-redes significa que os NSGs, UDRs e Global VNet Peering não são suportados para o HSM dedicado. As secções abaixo ajudam com técnicas alternativas para alcançar o mesmo ou um resultado semelhante para estas capacidades. 

O HSM NIC que reside no HSM VNet dedicado não pode utilizar grupos de segurança de rede ou rotas definidas pelo utilizador. Isto significa que não é possível definir políticas de negação por defeito do ponto de vista do HSM VNet dedicado, e que outros segmentos de rede devem ser autorizados a aceder ao serviço dedicado do HSM. 

A adição da solução Proxy de Aparelhos Virtuais de Rede (NVA) também permite que uma firewall NVA no centro de trânsito/DMZ seja colocada logicamente em frente ao HSM NIC, fornecendo assim a alternativa necessária aos NSGs e UDRs.

### <a name="solution-architecture"></a>Arquitetura da Solução
Este design de rede requer os seguintes elementos:
1.  Um VNet de trânsito ou hub DMZ com um nível de procuração NVA. Idealmente, dois ou mais NVAs estão presentes. 
2.  Um circuito ExpressRoute com um espremiado privado habilitado e uma ligação ao centro de trânsito VNet.
3.  Um VNet a espreitar entre o hub de trânsito VNet e o Dedicado HSM VNet.
4.  Uma firewall NVA ou Azure Firewall pode ser implantada oferecendo serviços DMZ no centro como uma opção. 
5.  VNets de carga de trabalho adicionais podem ser espreitados para o hub VNet. O cliente Gemalto pode aceder ao serviço dedicado HSM através do hub VNet.

![O diagrama mostra um VNet hub DMZ com um nível de procuração NVA para solução de Saúde NSG e UDR](media/networking/network-architecture.png)

Uma vez que a adição da solução de procuração NVA também permite que uma firewall NVA no centro de trânsito/DMZ seja logicamente colocada em frente ao HSM NIC, fornecendo assim as políticas de negação padrão necessárias. No nosso exemplo, usaremos o Azure Firewall para este fim e precisaremos dos seguintes elementos no lugar:
1. Uma Firewall Azure implantada na sub-rede "AzureFirewallSubnet" no hub DMZ VNet
2. Uma tabela de encaminhamento com uma UDR que direciona o tráfego para o ponto de terminação privado Azure ILB para a Firewall Azure. Esta Tabela de Encaminhamento será aplicada à GatewaySubnet onde reside o cliente ExpressRoute Virtual Gateway
3. Regras de segurança da rede dentro da AzureFirewall para permitir o reencaminhamento entre uma gama de fontes fidedigna e o ponto final privado Azure IBL escutando na porta TCP 1792. Esta lógica de segurança adicionará a política de "negação por defeito" necessária ao serviço dedicado do HSM. Ou seja, apenas os intervalos IP de origem fidedigna serão permitidos no serviço dedicado hSM. Todas as outras gamas serão largadas.  
4. Uma tabela de encaminhamento com um UDR que direciona o tráfego para o pré-pré-m para a Firewall Azure. Esta tabela de encaminhamento será aplicada na sub-rede de procuração NVA. 
5. Um NSG aplicado à sub-rede Proxy NVA para confiar apenas a gama de sub-redes do Azure Firewall como fonte, e apenas permitir o encaminhamento para o endereço IP HSM NIC sobre a porta TCP 1792. 

> [!NOTE]
> Como o nível de procuração NVA irá SNAT o endereço IP do cliente à medida que se encaminha para o HSM NIC, não são necessárias UDRs entre o VNet HSM e o DMZ hub VNet.  

### <a name="alternative-to-udrs"></a>Alternativa aos UDRs
A solução de nível NVA acima mencionada funciona como uma alternativa às UDRs. Há alguns pontos importantes a notar.
1.  A tradução do endereço de rede deve ser configurada na NVA para permitir que o tráfego de retorno seja encaminhado corretamente.
2. Os clientes devem desativar o ip-check do cliente na configuração Luna HSM para utilizar o VNA para NAT. Os seguintes comandos serviam como exemplo.
```
Disable:
[hsm01] lunash:>ntls ipcheck disable
NTLS client source IP validation disabled
Command Result : 0 (Success)

Show:
[hsm01] lunash:>ntls ipcheck show
NTLS client source IP validation : Disable
Command Result : 0 (Success)
```
3.  Implemente uDRs para o tráfego de entrada no nível NVA. 
4. De acordo com o design, as sub-redes HSM não iniciarão um pedido de ligação de saída ao nível da plataforma.

### <a name="alternative-to-using-global-vnet-peering"></a>Alternativa à utilização do Global VNET Peering
Existem algumas arquiteturas que você pode usar como alternativa ao olhar global VNet.
1.  Utilize [a ligação de gateway Vnet-to-Vnet VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) 
2.  Ligue o HSM VNET a outro VNET com um circuito ER. Isto funciona melhor quando é necessário um caminho direto no local ou vNET VPN. 

#### <a name="hsm-with-direct-express-route-connectivity"></a>HSM com conectividade rota expressa direta
![Diagrama mostra HSM com conectividade rota expressa direta](media/networking/expressroute-connectivity.png)

## <a name="next-steps"></a>Passos seguintes

- [Perguntas mais frequentes](faq.md)
- [Suportabilidade](supportability.md)
- [Elevada disponibilidade](high-availability.md)
- [Segurança Física](physical-security.md)
- [Monitorização](monitoring.md)
- [Arquitetura de implantação](deployment-architecture.md)
