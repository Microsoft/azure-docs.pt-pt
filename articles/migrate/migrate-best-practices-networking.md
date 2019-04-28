---
title: Melhores práticas para configurar a rede para cargas de trabalho migradas para o Azure | Documentos da Microsoft
description: Depois de migrar para o Azure, obtenha as práticas recomendadas para configurar o funcionamento em rede para as cargas de trabalho migradas.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: raynew
ms.openlocfilehash: 302445038dc9767bd412e232f62fc5249a1a7f09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61296539"
---
# <a name="best-practices-to-set-up-networking-for-workloads-migrated-to-azure"></a>Melhores práticas para configurar as redes para cargas de trabalho migradas para o Azure

Como planear e conceber para a migração, além da migração em si, uma das etapas mais importantes é o design e a implementação da rede do Azure. Este artigo descreve as melhores práticas para funcionamento em rede ao migrar para implementações de IaaS e PaaS no Azure.

> [!IMPORTANT]
> As melhores práticas e opiniões descritos neste artigo baseiam-se na plataforma do Azure e funcionalidades disponíveis no momento da escrita de serviço. Funcionalidades e capacidades mudam ao longo do tempo. Nem todas as recomendações podem ser aplicáveis para a sua implementação, por isso, selecione aqueles que funcionam para si.


## <a name="design-virtual-networks"></a>Design de redes virtuais

O Azure fornece redes virtuais (VNets):
- Recursos do Azure comuniquem em privado, diretamente e de forma segura entre si através de VNets.
- Pode configurar as ligações de ponto final em VNets para VMs e serviços que necessitem de comunicação da internet.
- Uma VNet é um isolamento lógico da cloud do Azure dedicado à sua subscrição.
- É possível implementar várias VNets dentro de cada subscrição do Azure e a região do Azure.
- Cada VNet é isolada de outras VNets.
- VNets pode conter endereços IP públicos e privados, definidos na [RFC 1918](https://tools.ietf.org/html/rfc1918), expresso na notação CIDR. Endereços IP públicos não são diretamente acessíveis a partir da internet.
- VNets pode ligar-se entre si através de VNet peering. VNets ligadas pode ser nas regiões idêntica ou diferentes. Portanto, os recursos numa VNet podem ligar a recursos em outras VNets.
- Por predefinição, o Azure encaminha o tráfego entre sub-redes numa VNet, ligados a VNets, de redes no local e na internet.


Há uma série de coisas que precisa pensar sobre o quando planear a topologia de VNet, incluindo como dispor o endereço IP de espaços, como implementar uma rede hub-and-spoke, como segmentar VNets em sub-redes, configurar o DNS e implementar as zonas de disponibilidade do Azure.

## <a name="best-practice-plan-ip-addressing"></a>Prática recomendada: Planear o endereçamento IP

Quando cria VNets como parte da sua migração, é importante planejar seu espaço de endereços IP de VNet.

- Deve atribuir um espaço de endereços que não é maior do que um intervalo CIDR de /16 para cada VNet. VNets permitir a utilização de 65536 endereços IP e atribuir um prefixo menor que/16 iria resultar na perda de endereços IP. É importante para não perder os endereços IP, mesmo que estejam nos intervalos privados definidos pelo RFC 1918.
- O espaço de endereços da VNet não deve se sobrepõem aos intervalos de rede no local.
- Tradução de endereços de rede (NAT) não deve ser usado.
- A sobreposição de endereços pode fazer com que redes que não não possível ligar e encaminhamento que não funciona corretamente. Se existir sobreposição de redes, terá de Refazer o design da rede ou utilizar a tradução de endereços de rede (NAT).

**Saiba mais:**

- [Obtenha uma visão geral](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) de VNets do Azure.
- [Leitura](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq) a FAQ da rede.
- [Saiba mais sobre](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) limitações de rede.


## <a name="best-practice-implement-a-hub-spoke-network-topology"></a>Prática recomendada: Implementar uma topologia de rede hub-and-spoke

Uma topologia de rede hub-and-spoke isola as cargas de trabalho, compartilhando serviços, como a identidade e segurança.
- O hub é uma VNet do Azure que age como um ponto central de conectividade.
- Os spokes são VNets que se ligam à VNet do hub utilização de VNet peering.
- São implementados serviços partilhados no hub, enquanto as cargas de trabalho individuais são implementadas como spokes. 

Considere o seguinte:
- Implementar uma topologia hub- and -spoke no Azure centraliza serviços comuns, tais como ligações a redes no local, firewalls e isolamento entre VNets. A VNet do hub fornece um ponto central de conectividade para redes no local e um local para utilização de serviços de host, cargas de trabalho alojadas nas spoke VNets.
- Uma configuração de hub- and -spoke é normalmente utilizada pelas empresas maiores. Redes menores, poderão considerar um design mais simples para poupar nos custos e complexidade.
- Spoke VNets podem ser utilizados para isolar cargas de trabalho, com cada spoke gerido separadamente em relação a outros spokes. Cada carga de trabalho pode incluir várias camadas e várias sub-redes que estão ligadas com balanceadores de carga do Azure.
- Hub- and -spoke VNets podem ser implementadas em grupos de recursos diferentes e até mesmo subscrições diferentes. Quando configurar o peering de redes virtuais em diferentes subscrições, as subscrições podem ser associadas para os inquilinos do Azure Active Directory (AD) mesmo ou em diferentes. Isto permite uma gestão descentralizada de cada carga de trabalho, ao mesmo tempo que partilha serviços mantidos na rede do concentrador.

![Gerenciamento de alterações](./media/migrate-best-practices-networking/hub-spoke.png)
*topologia Hub- and -spoke*

**Saiba mais:**

- [Leia sobre](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) uma topologia hub- and -spoke.
- Obter recomendações de rede para a execução do Azure [Windows](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/windows-vm) e [Linux](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/linux-vm) VMs.
- [Saiba mais sobre](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) o VNet peering.


## <a name="best-practice-design-subnets"></a>Prática recomendada: Sub-redes de design

Para fornecer isolamento dentro de uma VNet, pode segmentá-lo num ou mais sub-redes e atribuir uma parte do espaço de endereços da VNet para cada sub-rede.
- Pode criar várias sub-redes dentro de cada VNet.
- Por predefinição, o Azure encaminha o tráfego de rede entre todas as sub-redes numa VNet.
- Suas decisões de sub-rede baseiam-se nos seus requisitos organizacionais e técnicos.  
- Crie sub-redes com a notação CIDR.
- Ao decidir no intervalo de rede para sub-redes, é importante observar que o Azure mantém cinco endereços IP de cada sub-rede que não pode ser utilizado. Por exemplo, se criar a sub-rede disponível mais pequeno de/29 (com oito endereços IP), o Azure irá reter cinco endereços, para que tenha apenas três endereços utilizáveis que podem ser atribuídos a hosts da sub-rede.
- Na maioria dos casos, /28 como a sub-rede menor é recomendado utilizar.

### <a name="example"></a>Exemplo

A tabela mostra um exemplo de uma VNet com um espaço de endereços de 10.245.16.0/20 segmentada em sub-redes, para uma migração planeada.

**Sub-rede** | **CIDR** | **Endereços** | **Utilizar**
--- | --- | --- | ---
DEV-FE-EUS2 | 10.245.16.0/22 | 1019 | VMs de camada de front-end/web
DEV-APLICAÇÃO-EUS2 | 10.245.20.0/22 | 1019 | VMs de camada de aplicação
DEV-DB-EUS2 | 10.245.24.0/23 | 507 | VMs de base de dados

**Saiba mais:**
- [Saiba mais sobre](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#segmentation) conceber sub-redes.
- [Saiba como](https://docs.microsoft.com/azure/migrate/contoso-migration-infrastructure) numa empresa fictícia (Contoso) preparada sua infraestrutura de rede para migração.


## <a name="best-practice-set-up-a-dns-server"></a>Prática recomendada: Configurar um servidor DNS

O Azure adiciona um servidor DNS por predefinição quando implementar uma VNet. Isto permite-lhe rapidamente criar VNets e implementar recursos. No entanto, este servidor DNS oferece apenas serviços para os recursos nessa VNet. Se pretender ligar várias VNets entre si, ou ligar a um servidor no local a partir de VNets, tem capacidades de resolução de nome adicionais. Por exemplo, poderá ter o Active Directory para resolver nomes DNS entre redes virtuais. Para tal, implemente o seu próprio servidor DNS personalizado no Azure.

- Servidores DNS numa VNet podem encaminhar consultas DNS para resoluções de recursiva no Azure. Isto permite-lhe resolver os nomes de anfitrião nessa VNet. Por exemplo, um controlador de domínio em execução no Azure pode responder a consultas DNS para os seus próprios domínios e reencaminhar todas as outras consultas para o Azure.
- Encaminhamento de DNS permite que as VMs ver os seus recursos no local (por meio do controlador de domínio) e nomes de host fornecido com o Azure (com o reencaminhador). Acesso para as resoluções recursivas no Azure é fornecido com o endereço IP virtual 168.63.129.16.
- Encaminhamento de DNS também permite a resolução DNS entre VNets e permite que as máquinas no local resolver os nomes de anfitrião fornecidos pelo Azure.
    - Para resolver um nome de anfitrião VM, VM do servidor DNS tem de residir na mesma VNet e de ser configurado para consultas de nome de anfitrião de encaminhamento para o Azure.
    - Porque o sufixo DNS é diferente em cada VNet, pode utilizar regras de reencaminhamento condicional para enviar consultas DNS para a VNet correta para a resolução.
- Ao usar seus próprios servidores DNS, pode especificar vários servidores DNS para cada VNet. Também é possível especificar vários servidores DNS por interface de rede (para o Azure Resource Manager), ou por serviço cloud (para o modelo de implementação clássica).
- Servidores DNS especificados para um serviço de interface ou na cloud de rede têm precedência sobre os servidores DNS especificados para a VNet.
- No modelo de implementação Azure Resource Manager, pode especificar servidores DNS para uma VNet e a interface de rede, mas a prática recomendada é utilizar a definição apenas em VNets.

    ![Servidores DNS](./media/migrate-best-practices-networking/dns2.png) *servidores DNS para a VNet*

**Saiba mais:**
- [Saiba mais sobre](https://docs.microsoft.com/azure/migrate/contoso-migration-infrastructure) resolução de nomes ao utilizar o seu próprio servidor DNS.
- [Saiba mais sobre](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-subscriptions) DNS regras e restrições de nomenclatura.


## <a name="best-practice-set-up-availability-zones"></a>Prática recomendada: Configure as zonas de disponibilidade

As zonas de disponibilidade aumentam a elevada disponibilidade para proteger as aplicações e dados de falhas de datacenter.

- As zonas de disponibilidade são localizações físicas únicas dentro de uma região do Azure.
- Cada zona é constituída por um ou mais datacenters equipados com energia, refrigeração e redes.
- Para garantir a resiliência, existe um mínimo de três zonas separadas em todas as regiões ativadas.
- A separação física das zonas de disponibilidade numa região protege as aplicações e dados de falhas de datacenter.
- Serviços com redundância de zona replicar os seus dados e aplicações em zonas de disponibilidade para proteger contra pontos únicos de falha. – Com zonas de disponibilidade, o Azure oferece um SLA de 99,99% de tempo de atividade VM.

    ![Zona de disponibilidade](./media/migrate-best-practices-networking/availability-zone.png) *zona de disponibilidade*

- Planear e criar alta disponibilidade em sua arquitetura de migração ao colocalizar computação, armazenamento, redes e recursos de dados dentro de uma zona e replicá-los noutras zonas. Serviços do Azure que suportam zonas de disponibilidade enquadram-se em duas categorias:
    - Serviços zonais: Associar um recurso uma zona específica. Para VMs de exemplo, os discos geridos, endereços IP).
    - Serviços com redundância de zona: O recurso é replicada automaticamente nas zonas. Por exemplo, com redundância de zona de armazenamento, base de dados do Azure SQL.
- Pode implementar uma padrão com a com cargas de trabalho de acesso à internet ou de camadas de aplicação, para fornecer tolerância a falhas zonal de balanceamento de carga do Azure.

    ![Balanceador de carga](./media/migrate-best-practices-networking/load-balancer.png) *Balanceador de carga*

**Saiba mais:**
-   [Obtenha uma visão geral](https://docs.microsoft.com/azure/availability-zones/az-overview) das zonas de disponibilidade.



## <a name="design-hybrid-cloud-networking"></a>Da cloud híbrida de design de rede

Para uma migração com êxito, é fundamental para ligar a redes empresariais no local ao Azure. Esta ação cria uma conexão sempre ativa conhecida como uma rede na cloud híbrida, quais serviços são fornecidos do Azure na cloud para os usuários corporativos. Existem duas opções para criar este tipo de rede:

- **VPN de site a site:** Estabelecer uma ligação site a site entre o dispositivo VPN compatível no local e um gateway de VPN do Azure que é implementado numa VNet. Qualquer autorizado no local podem aceder a recursos VNets. Comunicações de site a site são enviadas através de um túnel encriptado através da internet. 
- **O Azure ExpressRoute:** Estabelecer uma ligação do ExpressRoute do Azure entre a rede no local e o Azure, através de um parceiro do ExpressRoute. Esta ligação é privada e o tráfego não entram na Internet.

**Saiba mais:**

- [Saiba mais](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/vpn) sobre redes na cloud híbrida.

## <a name="best-practice-implement-a-highly-available-site-to-site-vpn"></a>Prática recomendada: Implementar uma VPN de site a site de elevada disponibilidade

Para implementar uma VPN de site a site, configurar um gateway de VPN no Azure.
- Um gateway de VPN é um tipo específico de gateway de VNet, que é utilizada para enviar tráfego encriptado entre uma VNet do Azure e uma localização no local através da Internet pública.
- Também pode utilizar um gateway de VPN para enviar tráfego encriptado entre as VNets do Azure através da rede da Microsoft.
- Cada VNet pode ter apenas um gateway VPN.
- Pode criar várias ligações para o mesmo gateway VPN. Quando cria várias ligações, todos os túneis VPN partilham a largura de banda do gateway disponível.
- Todos os Gateways de VPN do Azure consistem em duas instâncias numa configuração ativa-em espera.
    - Para a manutenção planeada ou uma interrupção não planeada para a instância do Active Directory, ocorre a ativação pós-falha e a instância em espera assume automaticamente e retoma o site a site ou uma ligação VNet a VNet. 
    - A alternância de faz com que uma breve interrupção.
    - Para a manutenção planeada, a conectividade deve ser restaurada dentro de 10 a 15 segundos.
    - Para problemas não planeados, a recuperação da ligação será maior, cerca de um-para-1,5 minutos na pior das hipóteses.
    - Ligações de cliente VPN ponto a site (P2S) para o gateway serão desligadas e os utilizadores terá de voltar a ligar a partir de máquinas de cliente.

Quando configurar uma VPN de site a site, faça o seguinte:
 - Precisa de uma VNet quem intervalo de endereços não sobrepõem-se com a rede no local para o qual irá ligar a VPN.
 - Crie uma sub-rede de gateway na rede.
 - Criar um gateway de VPN, especifique o tipo de gateway (VPN) e se o gateway é baseada em políticas ou baseado na rota. Uma VPN RouteBased é recomendada como mais com capacidade de e para o futuro.
 - Criar um gateway de rede local no local e configurar o dispositivo VPN no local. 
 - Criar uma ligação de VPN de site a site de ativação pós-falha entre o gateway de VNet e o dispositivo no local. Utilização de VPN baseado na rota permite ligações ativa-passiva ou ativa-ativa para o Azure. Baseado na rota também oferece suporte a site a site (a partir de qualquer computador) e ligações ponto a site (a partir de um único computador) em simultâneo.
 - Especificar o SKU de gateway que pretende utilizar. Isso dependerá seus requisitos de carga de trabalho, taxas de transferência, funcionalidades e SLAs.
 - Border gateway protocol (BGP) é uma funcionalidade opcional pode utilizar com o Azure ExpressRoute e gateways de VPN baseado na rota para propagar as rotas do BGP no local para as suas VNets.

![VPN](./media/migrate-best-practices-networking/vpn.png)
*VPN Site a site*
 
**Saiba mais:**

- [Revisão](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) dispositivos VPN compatível no local.
- [Obtenha uma visão geral](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) de gateways de VPN.
- [Saiba mais sobre](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable) ligações de VPN de elevada disponibilidade.
- [Saiba mais sobre](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) planear e estruturar um gateway de VPN.
- [Revisão](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku) as definições do gateway VPN.
- [Revisão](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) SKUs de gateway.
- [Leia sobre](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview) configurar o BGP com gateways de VPN do Azure.


### <a name="best-practice-configure-a-gateway-for-vpn-gateways"></a>Prática recomendada: Configurar um gateway para Gateways de VPN

Quando cria um gateway de VPN no Azure, tem de utilizar uma sub-rede especial com o nome GatewaySubnet. Ao criar esta nota de sub-rede essas práticas recomendadas:

- O comprimento do prefixo da sub-rede do gateway pode ter um comprimento de prefixo máximo de 29 (por exemplo, 10.119.255.248/29). A recomendação atual é que use um comprimento de prefixo de 27 (por exemplo, 10.119.255.224/27).
- Quando define o espaço de endereços da sub-rede do gateway, utilize a última parte do espaço de endereços da VNet.
- Ao utilizar o Azure GatewaySubnet, nunca implemente qualquer VMs ou outros dispositivos, como o Gateway de aplicação para a sub-rede do gateway.
- Não atribua um grupo de segurança de rede (NSG) para esta sub-rede. Ele fará com que o gateway deixe de funcionar.

**Saiba mais:**
- [Utilize esta ferramenta](https://gallery.technet.microsoft.com/scriptcenter/Address-prefix-calculator-a94b6eed) para determinar o seu espaço de endereços IP.

## <a name="best-practice-implement-azure-virtual-wan-for-branch-offices"></a>Prática recomendada: Implementar o WAN Virtual do Azure para sucursais

Para várias ligações de VPN, o WAN Virtual do Azure é um serviço de rede que fornece conectividade de ramificação para ramificação otimizada e automatizada através do Azure.
- A WAN Virtual permite-lhe ligar e configurar dispositivos de ramo para comunicar com o Azure. Isso pode ser feito manualmente ou através de dispositivos do fornecedor preferencial através de um parceiro de Virtual WAN.
- Utilizar dispositivos de fornecedor preferencial permite gerenciamento de configuração, conectividade e utilização simple.
- O dashboard incorporado do Azure WAN fornece informações de resolução de problemas instantâneas que economizar tempo e fornecem uma forma fácil de controlar a conectividade de site a site em larga escala. 

**Saiba mais:**
[Saiba mais sobre](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about) WAN Virtual do Azure.

### <a name="best-practice-implement-expressroute-for-mission-critical-connections"></a>Prática recomendada: Implementar o ExpressRoute para ligações de missão crítica

O serviço Azure ExpressRoute permite-lhe expandir a sua infraestrutura no local para a cloud da Microsoft através da criação de ligações privadas entre o datacenter virtual do Azure e redes no local.
- As ligações do ExpressRoute podem ser através de uma rede de qualquer a qualquer (VPN de IP), uma rede de Ethernet ponto a ponto, ou através de um fornecedor de conectividade. Eles não passam pela internet pública.
- As ligações ExpressRoute oferecem maior segurança, confiabilidade e velocidades superiores (até 10 Gbps), juntamente com latência consistente.
- O ExpressRoute é útil para datacenters virtuais, como os clientes podem obter os benefícios de regras de conformidade associados a ligações privadas.
- Com o ExpressRoute Direct pode ligar diretamente para os routers da Microsoft em 100Gbps, para as necessidades de largura de banda maiores.
- ExpressRoute utiliza o BGP para trocar rotas entre redes no local, as instâncias do Azure e endereços públicos da Microsoft.

Implementar as ligações do ExpressRoute, normalmente, envolve a começar a usar um provedor de serviços do ExpressRoute. Para um início rápido, é comum usar inicialmente uma VPN de site a site para estabelecer a conectividade entre o datacenter virtual e recursos no local e, em seguida, migrar para uma ligação ExpressRoute, quando é de uma interconexão físico com o seu fornecedor de serviços estabelecida.

**Saiba mais:**
- [Obtenha uma visão geral](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) do ExpressRoute.
- [Saiba mais sobre](https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about) direta do ExpressRoute.

### <a name="best-practice-optimize-expressroute-routing-with-bgp-communities"></a>Prática recomendada: Otimizar o encaminhamento do ExpressRoute com as Comunidades de BGP

Se tem vários circuitos do ExpressRoute, significa que tem mais do que um caminho para se ligar à Microsoft. Como resultado, o encaminhamento inferior ao ideal pode acontecer e o seu tráfego, poderá demorar um caminho mais longo para chegar à Microsoft e Microsoft à sua rede. Quanto mais tempo o caminho de rede, maior será a latência. A latência tem impacto direto na experiência de utilizador e de desempenho da aplicação.

#### <a name="example"></a>Exemplo

Vejamos um exemplo.

- Tem dois escritórios nos EUA, um em Los Angeles e outro em Nova Iorque.
- Os escritórios estão ligados numa WAN, que pode ser sua própria rede principal ou VPN de IP do seu fornecedor de serviços.
- Tem dois circuitos do ExpressRoute, um nos EUA Oeste e o outro nos EUA Leste, que também estão ligados através da WAN. Tem, obviamente, dois caminhos para se ligar à rede da Microsoft.


 
**Problema** Agora imagine que tem a uma implementação do Azure (por exemplo, serviço de aplicações do Azure) em E.u.a. oeste e EUA Leste.
- Pretende que os utilizadores em cada escritório aceder aos seus serviços do Azure mais próximos para uma experiência ideal.
- Assim que pretende ligar utilizadores em Los Angeles ao Azure dos EUA oeste e os utilizadores em Nova Iorque ao Azure dos EUA Leste.
- Isso funciona para os utilizadores da costa leste, mas não para o oeste. O problema é o seguinte:
    - Em cada circuito do ExpressRoute, estamos a anunciar ambos os prefixos no Azure dos EUA Leste (23.100.0.0/16) e o Azure dos EUA oeste (13.100.0.0/16).
    - Sem saber que prefixo corresponde a que região, prefixos não são tratados de forma diferente.
    - Sua rede WAN pode assumir que ambos os prefixos estão mais próximos dos E.U. a leste que dos EUA oeste e, portanto, encaminham os utilizadores de ambos os escritórios para o circuito do ExpressRoute nos EUA leste, fornecendo um inferior a uma experiência ideal para os utilizadores no escritório de Los Angeles.

![VPN](./media/migrate-best-practices-networking/bgp1.png)
*Comunidades BGP não otimizados ligação*

**Solução**

Para otimizar o encaminhamento para ambos os utilizadores do office, precisa saber que prefixo corresponde a Azure dos EUA oeste e qual é a partir do Azure dos EUA Leste. Pode codificar estas informações com valores de Comunidade do BGP.
- Atribuir um valor de Comunidade do BGP exclusivo para cada região do Azure. Por exemplo 12076:51004 para EUA leste; 12076:51006 para E.U.A. oeste.
- Agora que fique claro que prefixo pertence à região do Azure, pode configurar um circuito de ExpressRoute preferencial.
- Uma vez que estiver a utilizar o BGP para trocar informações de encaminhamento, pode utilizar a preferência local do BGP para influenciar o encaminhamento.
- No nosso exemplo, atribuir um valor de preferência local mais alto para 13.100.0.0/16 no Oeste dos EUA que nos EUA leste e, da mesma forma, um valor de preferência local mais alto para 23.100.0.0/16 nos EUA leste do que nos EUA oeste. 
- Esta configuração garante que, quando os caminhos para a Microsoft estão disponíveis, os utilizadores em Los Angeles irão ligar ao Azure dos EUA Oeste utilizando o circuito de oeste e utilizadores Nova Iorque ligam ao Azure dos EUA Leste usando o circuito de Leste. O encaminhamento fica otimizado em ambos os lados.

![VPN](./media/migrate-best-practices-networking/bgp2.png)
*Comunidades do BGP com otimização de ligação*


**Saiba mais:**
- [Saiba mais sobre](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing) otimizar o encaminhamento

## <a name="securing-vnets"></a>Protegendo VNets

A responsabilidade de proteger VNETs é compartilhada entre a Microsoft e. A Microsoft fornece muitas funcionalidades de rede, bem como serviços que ajudam a manter segura a recursos. Ao conceber a segurança para VNets, há um número de melhores práticas que deve seguir, incluindo a implementação de uma rede de perímetro, utilizar grupos de segurança e filtragem, protegendo o acesso a recursos e os endereços IP e implementar a proteção de ataque.

**Saiba mais:**

- [Obtenha uma visão geral](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices) de melhores práticas para segurança de rede.
- [Saiba como](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#security) design para redes seguras.

## <a name="best-practice-implement-an-azure-perimeter-network"></a>Prática recomendada: Implementar uma rede de perímetro do Azure

Embora a Microsoft investe intensamente a proteger a infraestrutura de nuvem, também terá de proteger seus serviços cloud e os grupos de recursos. Uma abordagem de várias camadas para segurança fornece o melhor defesa. Colocar uma rede de perímetro no local é uma parte importante dessa estratégia de defesa.

- Uma rede de perímetro protege os recursos da rede interna de uma rede não fidedigna. 
- É a camada mais externa que é exposta à internet. Que geralmente se encontra entre a internet e a infraestrutura empresarial, geralmente com alguma forma de proteção em ambos os lados. 
- Numa topologia de rede típicos de empresas, a infraestrutura principal intensamente é fortified em perímetros, com várias camadas de dispositivos de segurança. O limite de cada camada consiste em dispositivos e os pontos de imposição de política.
- Cada camada pode incluir uma combinação das soluções de segurança de rede que incluem firewalls, prevenção de Denial of Service (DoS), sistemas de proteção de intrusão/deteção de intrusões (IDS/IPS) e dispositivos VPN.
- Imposição de políticas na rede de perímetro pode utilizar as políticas de firewall, listas de controlo de acesso (ACLs) ou encaminhamento específico.
- Como o tráfego recebido chega a partir da internet, tem interceptado e manipulado por uma combinação de solução de defesa contra ataques de bloqueio e prejudicial tráfego, enquanto permite que os pedidos legítimos na rede.
- Pode encaminhar o tráfego de entrada diretamente a recursos na rede de perímetro. O recurso de rede de perímetro, em seguida, pode comunicar com outros recursos mais profundo na rede, movendo reencaminhar tráfego para a rede após a validação.

A figura a seguir mostra um exemplo de uma rede de perímetro única sub-rede numa rede empresarial, com dois limites de segurança.

![VPN](./media/migrate-best-practices-networking/perimeter.png)
*implementação de rede de perímetro*

**Saiba mais:**
- [Saiba mais sobre](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) implementar uma rede de perímetro entre o Azure e o seu datacenter no local.


## <a name="best-practice-filter-vnet-traffic-with-nsgs"></a>Prática recomendada: Filtrar o tráfego de VNet com NSGs

Grupos de segurança de rede (NSG) contêm várias regras de segurança de entrada e saída que filtrar o tráfego que passa para e de recursos. Filtragem pode ser por origem e endereço IP de destino, porta e protocolo. 
- Os NSGs contêm regras de segurança que permitem ou negam o tráfego de rede de entrada (ou tráfego de rede de saída do) vários tipos de recursos do Azure. Para cada regra, pode especificar a origem e o destino, a porta e o protocolo.
- As regras do NSG são avaliadas por prioridade com informações de cinco cadeias de identificação (origem, porta de origem, destino, porta de destino e protocolo) para permitir ou negar o tráfego.
- É criado um registo de fluxo para as ligações existentes. A comunicação é permitida ou negada com base no estado da ligação do registo do fluxo.
- Um registo de fluxo permite que um NSG a ser com monitoração de estado. Por exemplo, se especificar uma regra de segurança de saída para qualquer endereço através da porta 80, não terá uma regra de segurança de entrada para responder ao tráfego de saída. Só tem de especificar uma regra de segurança de entrada se a comunicação for iniciada externamente.
- O oposto também se aplica. Se o tráfego de entrada for permitido através de uma porta, não precisa de especificar uma regra de segurança de saída para responder ao tráfego através da porta.
- Ligações existentes não sejam interrompidas quando remove uma regra de segurança ativado o fluxo. Fluxos de tráfego são interrompidos quando as ligações são paradas e nenhum tráfego estiver fluindo em ambas as direções para, pelo menos, alguns minutos.
- Ao criar NSGs, crie, no mínimo possível, mas o máximo que são necessárias.

### <a name="best-practice-secure-northsouth-and-eastwest-traffic"></a>Prática recomendada: Proteger o tráfego de Norte/Sul e este/oeste

Ao proteger VNets, é importante considerar vetores de ataque.
- Usando apenas a sub-rede NSGs simplifica o seu ambiente, mas só protege o tráfego para a sub-rede. Isso é conhecido como Norte/Sul tráfego.
- Tráfego entre VMs na mesma sub-rede é conhecido como tráfego este/oeste.
- É importante aproveitar as duas formas de proteção, para que se um hacker obtiver acesso a partir de fora vai ser parados durante a tentativa de ligar máquinas localizadas na mesma sub-rede.

### <a name="use-service-tags-on-nsgs"></a>Utilizar etiquetas de serviço em NSGs

A etiqueta de serviço representa um grupo de prefixos de endereços IP. Usando uma marca de serviço ajuda a minimizar a complexidade ao criar regras NSG.
- Pode utilizar etiquetas de serviço em vez de endereços IP específicos, ao criar regras.
- A Microsoft gere os prefixos de endereço associados com a etiqueta de serviço e atualiza automaticamente a etiqueta de serviço, como endereços são alterados.
- Não é possível criar suas próprias etiquetas de serviço, ou especificar que endereços IP estão incluídos nas mesmas.

Etiquetas de serviço demorar trabalho manual de atribuição de uma regra a grupos de serviços do Azure. Por exemplo, se pretender permitir que uma sub-rede de VNet que contém o acesso de servidores web para uma base de dados do SQL do Azure, criar uma regra de saída para a porta 1433 e, utilizar o **Sql** etiqueta de serviço.
- Isso **Sql** etiqueta denota os prefixos de endereços dos serviços do Azure SQL Database e o Azure SQL Data Warehouse.
- Se especificar **Sql** como o valor, o tráfego é permitido ou negado para o Sql.
- Se quiser apenas permitir o acesso ao **Sql** numa região específica, é possível especificar essa região. Por exemplo, se quiser permitir o acesso apenas a base de dados SQL do Azure na região E.U.A. leste, pode especificar **eastus** como uma etiqueta de serviço.
- A etiqueta representa o serviço, mas não instâncias específicas do mesmos. Por exemplo, a etiqueta representa o serviço de base de dados do Azure SQL, mas não representa um determinado banco de dados SQL ou o servidor.
- Todos os prefixos de endereço representados por esta etiqueta também são representados pela etiqueta **Internet**.


**Saiba mais:**

- [Leia sobre](https://docs.microsoft.com/azure/virtual-network/security-overview) NSGs.
- [Revisão](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) as etiquetas de serviço disponíveis para NSGs.


## <a name="best-practice-use-application-security-groups"></a>Prática recomendada: Utilizar grupos de segurança de aplicação

Grupos de segurança de aplicações permitem-lhe configurar a segurança de rede como uma extensão natural de uma estrutura de aplicação.

- Pode agrupar VMs e definir políticas de segurança de rede com base nos grupos de segurança do aplicativo.
- Grupos de segurança de aplicações permitem-lhe reutilizar a política de segurança em escala sem manutenção manual de endereços IP explícitos.
- Grupos de segurança de aplicação de lidar com a complexidade de endereços IP explícitos e vários conjuntos de regras, permitindo que se concentre na lógica de negócio. 

### <a name="example"></a>Exemplo

![Grupo de segurança de aplicativo](./media/migrate-best-practices-networking/asg.png)
*exemplo de grupo de segurança de aplicação*

**Interface de rede** | **Grupo de segurança de aplicações**
--- | ---
NIC1 | AsgWeb
NIC2 | AsgWeb
NIC3 | AsgLogic
NIC4 | AsgDb 

- No nosso exemplo, cada interface de rede pertence a apenas um grupo de segurança de aplicações, mas na verdade uma interface pode pertencer a vários grupos, em conformidade com os limites do Azure.
- Nenhuma das interfaces de rede tem um NSG associado. NSG1 é associado a ambas as sub-redes e contém as seguintes regras.

    **Nome da regra** | **Objetivo** | **Detalhes**
    --- | --- | ---   
    Allow-HTTP-Inbound-Internet | Permitir o tráfego da internet para os servidores web. Tráfego de entrada da internet é negado pela regra de segurança do DenyAllInbound predefinida, pelo que nenhuma regra adicional é necessária para os grupos de segurança de aplicativo AsgLogic ou AsgDb. | Prioridade: 100<br/><br/> Origem: internet<br/><br/> Porta de origem: *<br/><br/> Destino: AsgWeb<br/><br/> Porta de destino: 80<br/><br/> Protocolo: TCP<br/><br/> Acesso: Permita.
    Deny-Database-All | Regra de segurança predefinida AllowVNetInBound permite todas as comunicações entre os recursos na mesma VNet, esta regra é necessária para negar o tráfego de todos os recursos. | Prioridade: 120<br/><br/> Origem: *<br/><br/> Porta de origem: *<br/><br/> Destino: AsgDb<br/><br/> Porta de destino: 1433<br/><br/> Protocolo: Todos<br/><br/> Acesso: Negar.
    Allow-Database-BusinessLogic | Permita o tráfego do grupo de segurança de aplicativo de AsgLogic para o grupo de segurança de aplicações AsgDb. A prioridade para esta regra é maior do que a regra de negação-base de dados-All e vai ser processada antes essa regra, para que o tráfego do grupo de segurança de aplicativo de AsgLogic é permitido e todos os outros tráfegos está bloqueado. | Prioridade: 110<br/><br/> Origem: AsgLogic<br/><br/> Porta de origem: *<br/><br/> Destino: AsgDb<br/><br/> Porta de destino: 1433<br/><br/> Protocolo: TCP<br/><br/> Acesso: Permita.

- As regras que especifiquem grupos de segurança de aplicações como a origem ou o destino só são aplicadas às interfaces de rede que são membros do grupo de segurança de aplicações. Se a interface de rede não for membro de um grupo de segurança de aplicações, a regra não é aplicada à interface de rede, mesmo que o grupo de segurança de rede esteja associado à sub-rede.

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) grupos de segurança do aplicativo.


### <a name="best-practice-secure-access-to-paas-using-vnet-service-endpoints"></a>Prática recomendada: Acesso seguro a PaaS com pontos finais de serviço de VNet

Pontos finais de serviço de VNet expandem o seu espaço de endereços privados da VNet e a identidade para serviços do Azure através de uma ligação direta.

- Pontos de extremidade permitem-lhe proteger os recursos críticos de serviço do Azure para as suas VNets apenas. O tráfego da sua VNet para o serviço do Azure permanece sempre na rede backbone do Microsoft Azure.
- Espaço de endereços privados da VNet pode ser uma sobreposição e, portanto, não pode ser utilizado para identificar exclusivamente o tráfego proveniente de uma VNet.
- Depois de ativar pontos finais de serviço na sua VNet, pode proteger os recursos de serviço do Azure ao adicionar uma regra de VNet para os recursos de serviço. Isso fornece segurança melhorada ao remover totalmente acesso de internet público a recursos e a permitir o tráfego apenas a partir da sua VNet.

![Pontos finais de serviço](./media/migrate-best-practices-networking/endpoint.png)
*pontos finais de serviço*

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) pontos finais de serviço da VNet.


## <a name="best-practice-control-public-ip-addresses"></a>Prática recomendada: Endereços IP públicos do controlo

Endereços IP públicos no Azure podem ser associados a VMs, balanceadores de carga, gateways de aplicação e gateways de VPN.

- Endereços IP públicos permitem que os recursos de internet para comunicam na entrada de recursos do Azure e recursos do Azure para comunicar com a Internet à saída.
- Endereços IP públicos são criados com um SKU básico ou padrão, com algumas diferenças entre eles. SKUs padrão podem ser atribuídos a qualquer serviço, mas mais, normalmente, são configurados em VMs, balanceadores de carga e gateways de aplicação.
- É importante observar que um endereço IP público básico não tem um NSG configurado automaticamente. Terá de configurar a sua própria e atribuir regras para controlar o acesso. Endereços IP de SKU Standard têm um NSG e regras atribuídas por predefinição.
- Como melhor prática, as VMs não devem ser configuradas com um endereço IP público.
    - Se precisar de uma porta aberta, deve ser apenas para serviços da web, como a porta 80 ou 443.
    - Portas de gestão remota padrão como RDP (. 3389) e de SSH (22) devem ser definidas para negar, juntamente com todas as outras portas, utilize os NSGs.
- Uma prática recomendada é colocar as VMs por trás de um gateway de aplicação ou Balanceador de carga do Azure. Em seguida, se for necessário acesso às portas de gestão remota, pode utilizar o acesso à VM just-in-time no Centro de segurança do Azure.

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) endereços IP públicos no Azure.
- [Leia mais](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) no acesso VM just-in-time no Centro de segurança do Azure.


## <a name="leverage-azure-security-features-for-networking"></a>Tirar partido das funcionalidades de segurança do Azure para funcionamento em rede

O Azure tem recursos de segurança de plataforma que são fáceis de usar e fornecem contramedidas avançadas para ataques de rede comuns. Estes incluem-se ao Firewall do Azure, o Firewall de aplicações Web e o observador de rede.

## <a name="best-practice-deploy-azure-firewall"></a>Prática recomendada: Implantar o Firewall do Azure

Firewall do Azure é um serviço de segurança de rede gerido e baseado na nuvem que protege os seus recursos da VNet. É um totalmente com monitoração de estado firewall-como-serviço com elevada disponibilidade incorporada e a escalabilidade da cloud sem restrições.

![Pontos finais de serviço](./media/migrate-best-practices-networking/firewall.png)
*Firewall do Azure*

- Na Firewall do Azure, pode centralmente criar, impor e políticas de conectividade de rede e aplicativos de registo entre subscrições e VNets.
- Firewall do Azure utiliza um endereço IP público estático para os seus recursos da VNet, permitindo que fora firewalls para identificar o tráfego com origem na sua VNet.
- Firewall do Azure está totalmente integrado com o Azure Monitor para o registo e análise.
- Como melhor prática ao criar regras de Firewall do Azure, use as marcas FQDN para criar regras.
    - Uma etiqueta do FQDN representa um grupo de FQDNs associados a serviços bem conhecidos do Microsoft.
    - Pode usar uma etiqueta do FQDN para permitir o tráfego de rede de saída necessárias através da firewall.
- Por exemplo, para permitir o tráfego de rede do Windows Update através da firewall manualmente, precisaria criar várias regras de aplicação. Utilização de etiquetas do FQDN, criar uma regra de aplicação e incluem a marca de atualizações do Windows. Com esta regra no local, o tráfego de rede para pontos de extremidade do Microsoft Windows Update pode fluir através da firewall.

**Saiba mais:**

- [Obtenha uma visão geral](https://docs.microsoft.com/azure/firewall/overview) de Firewall do Azure.
- [Saiba mais sobre](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN etiquetas.


## <a name="best-practice-deploy-azure-web-application-firewall-waf"></a>Prática recomendada: Implementar Firewall de aplicação Web do Azure (WAF)

Aplicativos Web são cada vez mais alvos de ataques maliciosos que exploram vulnerabilidades conhecidas comuns. Explorações incluem ataques de injeção de SQL e ataques de script entre sites. Impedir esses ataques no código da aplicação pode ser um desafio e pode exige manutenção rigorosa, correção e monitorização em várias camadas da topologia da aplicação. Uma firewall de aplicações web centralizada ajuda a tornar o gerenciamento de segurança muito mais simples e ajuda os administradores de aplicações protegem contra ameaças ou intrusões. Uma firewall de aplicação web pode reagir a ameaças de segurança mais rapidamente, ao corrigir vulnerabilidades conhecidas num local central, em vez de proteção de aplicações web individualmente. Os gateways de aplicações existentes podem ser facilmente convertidos num gateway de aplicação com firewall de aplicações Web ativada.

Firewall de aplicações do Azure Web (WAF) é um recurso do gateway de aplicação do Azure.
- WAF fornece proteção centralizada das suas aplicações web, de vulnerabilidades e exploits comuns.
- WAF protege sem modificações ao código de back-end.
- Ele pode proteger várias aplicações web em simultâneo por trás de um gateway de aplicação
- WAF está integrada no Centro de segurança do Azure.
- Pode personalizar regras WAF e grupos de regras para atender às suas necessidades de aplicação.
- Como melhor prática, deve utilizar uma WAF na frente em qualquer aplicação com acesso à web, incluindo aplicações em VMs do Azure ou como um serviço de aplicações do Azure.

**Saiba mais:**
- [Saiba mais sobre](https://docs.microsoft.com/azure/application-gateway/waf-overview) WAF.
- [Revisão](https://docs.microsoft.com/azure/application-gateway/application-gateway-waf-configuration) WAF limitações e exclusões.


## <a name="best-practice-implement-azure-network-watcher"></a>Prática recomendada: Implementar o observador de rede do Azure

O observador de rede do Azure fornece ferramentas para monitorizar os recursos e comunicações numa VNet do Azure. Por exemplo, pode monitorizar as comunicações entre uma VM e um ponto de extremidade como outra VM ou FQDN, ver recursos e as relações de recursos numa VNet, ou diagnosticar problemas de tráfego de rede.

![Observador de rede](./media/migrate-best-practices-networking/network-watcher.png)
*observador de rede*

- Com o observador de rede pode monitorizar e diagnosticar problemas de rede sem iniciar sessão no VMs.
- Pode acionar a captura de pacotes, definindo alertas e obter acesso a informações de desempenho em tempo real ao nível do pacote. Quando vir um problema, pode investigar em detalhes.
- Como melhor prática, deve utilizar o observador de rede para rever os registos de fluxo do NSG.
    - Registos de fluxo NSG no observador de rede permitem-lhe ver informações sobre o tráfego IP de entrada e de saída através de um NSG.
    - Os registos de fluxo são escritos no formato json.
    - Os registos de fluxo mostram fluxos de saída e entrados numa base por regras, a interface de rede (NIC) ao qual o fluxo se aplica, informações de 5 cadeias de identificação sobre o fluxo (IP de origem/destino, porta de origem/destino e protocolo), e se o tráfego foi permitido ou negado.

**Saiba mais:**

- [Obtenha uma visão geral](https://docs.microsoft.com/azure/network-watcher) do observador de rede.
- [Saiba mais](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) sobre NSG registos de fluxo.

## <a name="use-partner-tools-in-the-azure-marketplace"></a>Utilizar ferramentas de parceiros no Azure Marketplace

Para topologias de rede mais complexas, poderá utilizar os produtos de segurança de parceiros da Microsoft, em aplicações virtuais de rede específico (NVAs).

- Uma NVA é uma VM que executa uma função de rede, como uma firewall, otimização de WAN ou outra função de rede.
- NVAs reforçar as funções de segurança e rede da VNet. Eles podem ser implementados para firewalls de elevada disponibilidade, prevenção contra intrusões, deteção de intrusões, firewalls de aplicações web (WAFs), otimização de WAN, encaminhamento, balanceamento de carga, VPN, gestão de certificados, Active Directory e a autenticação multifator.
- NVA está disponível a partir de vários fornecedores no [do Azure Marketplace](https://azuremarketplace.microsoft.com/). 
 

## <a name="best-practice-implement-firewalls-and-nvas-in-hub-networks"></a>Prática recomendada: Implementar firewalls e de NVAs em redes de hub

No hub, rede de perímetro (com acesso à internet) normalmente é gerenciada por meio de uma Firewall do Azure, um farm de firewall, ou com Firewalls de aplicações Web (WAFs). Considere as seguintes comparações.

**Tipo de firewall** | **Detalhes**
--- | ---
WAFs | Aplicações Web são comuns e tendem a ser prejudicadas vulnerabilidades e explorações potenciais.<br/><br/> WAFs foram concebidos para detetar ataques contra aplicações web (HTTP/HTTPS), mais especificamente que um firewall genérico.<br/><br/> Em comparação com a tecnologia de firewall tradição, WAFs tem um conjunto de recursos específicos que proteger servidores web interno de ameaças.
Azure Firewall | À semelhança de farms de servidores de firewall NVA, o Firewall do Azure utiliza um mecanismo de administração comuns e um conjunto de regras de segurança para proteger cargas de trabalho alojadas em redes "spoke" e para controlar o acesso a redes no local.<br/><br/> O Firewall do Azure tem escalabilidade incorporada.
NVA firewalls | Como o Azure Firewall NVA farms de servidores de firewall têm de mecanismo de administração comuns e um conjunto de regras de segurança para proteger cargas de trabalho alojadas em redes "spoke" e para controlar o acesso a redes no local.<br/><br/> NVA firewalls podem ser dimensionados manualmente por trás de um balanceador de carga.<br/><br/> Embora um firewall NVA tem menos software especializada que uma WAF, ele tem escopo mais amplo do aplicativo para filtrar e inspecionar qualquer tipo de tráfego de saída e entrada.<br/><br/> Se quiser usar a NVA pode encontrá-los no Azure Marketplace.

Recomendamos que utilize um conjunto de Firewalls do Azure (ou NVAs) para o tráfego com origem na internet, e outro para o tráfego com origem no local.
- Usando apenas um conjunto de firewalls para ambos é um risco de segurança, pois fornece não perímetro de segurança entre os dois conjuntos de tráfego de rede.
- Utilizando as camadas de firewall separado reduz a complexidade de regras de segurança de verificação e, fica claro as regras que correspondem à qual solicitação de rede de entrada.

**Saiba mais:**
- [Saiba mais sobre](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) com NVAs de numa VNet do Azure.

## <a name="next-steps"></a>Passos Seguintes 

Reveja a outras práticas recomendadas:

- [Melhores práticas](migrate-best-practices-security-management.md) para segurança e gestão após a migração.
- [Melhores práticas](migrate-best-practices-costs.md) do cost management após a migração.
