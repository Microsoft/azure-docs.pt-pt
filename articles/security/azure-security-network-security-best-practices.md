---
title: Melhores práticas para segurança de rede – Microsoft Azure
description: Este artigo fornece um conjunto de melhores práticas para segurança de rede através do Azure recursos internos.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: TomSh
ms.openlocfilehash: 5bec7db1c4409165242416df16e437b121381b49
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872564"
---
# <a name="azure-network-security-best-practices"></a>Práticas recomendadas de segurança de rede do Azure
Este artigo aborda um conjunto de práticas recomendadas de segurança de rede do Azure. Essas práticas recomendadas são derivadas da nossa experiência com redes do Azure e as experiências dos clientes, como mesmo.

Para cada melhor prática, este artigo explica:

* O que é a melhor prática
* Por que pretende ativar essa prática recomendada
* Se efetuar a ativação para ativar a melhor prática, o que pode ser o resultado
* Possíveis alternativas para a prática recomendada
* Como pode aprender permitir a melhor prática

Este artigo de práticas recomendadas de segurança de rede do Azure baseia-se numa opinião de consenso e capacidades da plataforma Azure e conjuntos de recursos, tal como existem no momento que este artigo foi escrito. Opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

## <a name="logically-segment-subnets"></a>Logicamente as sub-redes de segmento
Redes virtuais do Azure são semelhantes a uma rede local na sua rede no local. A idéia por trás de uma rede virtual do Azure é que crie uma única privada com base no espaço em rede de endereços IP no qual pode colocar todas as suas máquinas virtuais do Azure. Os espaços de endereços IP privados disponíveis estão numa classe (10.0.0.0/8), classe B (172.16.0.0/12), e intervalos de classe C (192.168.0.0/16).

Melhores práticas para segmentar logicamente sub-redes incluem:

**Melhor prática**: Segmente o espaço de endereçamento maior em sub-redes.   
**Detalhe**: Uso [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)-com base em princípios de sub-redes para criar as sub-redes.

**Melhor prática**: Crie controlos de acesso de rede entre sub-redes. O encaminhamento entre sub-redes ocorre automaticamente e não precisa de configurar manualmente as tabelas de roteamento. Por predefinição, não existem nenhum controle de acesso de rede entre as sub-redes que criar na rede virtual do Azure.   
**Detalhe**: Utilize um [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) (NSG). NSGs são simples, abordam a dispositivos de inspeção de pacotes com monitoração de estado que utilizam a 5 cadeias de identificação (IP de origem, porta de origem, IP de destino, porta de destino e protocolo de camada 4) para criar regras de permissão/negação de mensagens em fila para o tráfego de rede. Permitir ou negar o tráfego de e para um único endereço IP, para e de vários endereços IP, ou de e para sub-redes inteiras.

Quando utilizar NSGs para controlo de acesso de rede entre sub-redes, pode colocar recursos que pertencem à mesma zona de segurança ou função nas suas próprias sub-redes.

## <a name="control-routing-behavior"></a>Controlar o comportamento de encaminhamento
Quando colocar uma máquina virtual numa rede virtual do Azure, a VM pode ligar a qualquer outra VM na mesma rede virtual, mesmo que as outras VMs estiverem em sub-redes diferentes. Isso é possível porque este tipo de comunicação de permite que uma coleção de rotas do sistema ativada por predefinição. Estas rotas predefinidas que permitem que as VMs na mesma rede virtual para iniciar as ligações entre si e com a internet (para comunicações de saída à internet apenas).

Embora as rotas de sistema predefinidas são úteis para muitos cenários de implementação, há horas em que pretende personalizar a configuração de roteamento para as suas implementações. Pode configurar o endereço de próximo salto para atingir destinos específicos.

Recomendamos que configure [rotas definidas pelo utilizador](../virtual-network/virtual-networks-udr-overview.md) quando implementa uma aplicação de segurança para uma rede virtual. Vamos falar sobre isso numa seção posterior intitulada [proteger os seus recursos críticos de serviço do Azure para apenas as suas redes virtuais](azure-security-network-security-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Rotas definidas pelo utilizador não são necessárias e as rotas de sistema padrão geralmente funcionam.
>
>

## <a name="enable-forced-tunneling"></a>Ativar o protocolo de túnel forçado
Para compreender melhor o túnel forçado, é útil para entender é que "Dividir túnel". O exemplo mais comum de divisão de túnel é visto com ligações de rede privada virtual (VPN). Imagine que estabelecer uma ligação VPN o quarto de hotel à sua rede empresarial. Esta ligação permite-lhe aceder a recursos empresariais. Vão todas as comunicações à sua rede empresarial através do túnel VPN.

O que acontece quando pretende ligar a recursos na internet? Quando a divisão de túnel estiver ativada, essas ligações aceda diretamente à internet e não através do túnel VPN. Alguns especialistas em segurança, considere esta opção para ser um risco potencial. Eles Recomendamos desativar dividir túnel e certificar-se de que todas as ligações, os destinado à internet e os destinado a recursos da empresa, aceda através do túnel VPN. A vantagem de desabilitar a divisão de túnel é que as ligações à internet, em seguida, são forçadas através de dispositivos de segurança da rede empresarial. Isso não seria o caso se o cliente VPN ligado à internet do túnel de VPN.

Agora vamos colocar isso novamente para as VMs numa rede virtual do Azure. As rotas predefinidas para uma rede virtual do Azure permitem que as VMs iniciar o tráfego para a internet. Isso também pode representar um risco de segurança, porque estas ligações de saída podem aumentar a superfície de ataque de uma VM e ser utilizadas pelos atacantes. Por esse motivo, recomendamos que [enable túnel forçado](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) nas suas VMs quando tiver conectividade em vários locais entre a rede virtual do Azure e a sua rede no local. Podemos falar sobre a conectividade entre instalações, mais tarde nas melhores práticas de rede.

Se não tiver uma ligação em vários locais, certifique-se de que tire partido de NSGs (discutido anteriormente) ou virtuais do Azure (abordadas a seguir) para impedir que as ligações de saída de aplicações de segurança de rede para a internet das suas máquinas virtuais do Azure.

## <a name="use-virtual-network-appliances"></a>Utilizar aplicações de rede virtual
Os NSGs e o encaminhamento definido pelo utilizador podem fornecer uma determinada medida de segurança de rede em camadas de rede e transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model). Mas, em algumas situações, quer ou precisa de ativar a segurança nos níveis alto da pilha. Nesse tipo de situação, recomendamos que implemente aplicações de segurança de rede virtual fornecidas pelos parceiros do Azure.

Aplicações de segurança de rede do Azure podem fornecer uma melhor segurança que o que fornecem controles de nível de rede. Capacidades de segurança de rede de aplicações de segurança de rede virtual incluem:


* Firewall
* Prevenção contra intrusões/detecção de intrusões
* Gestão de vulnerabilidades
* Controlo de aplicações
* Deteção de anomalias baseada em rede
* Filtragem da Web
* Antivírus
* Proteção do Botnet

Para encontrar aplicações de segurança de rede virtual do Azure disponíveis, vá para o [do Azure Marketplace](https://azure.microsoft.com/marketplace/) e procure "security" e "segurança de rede".

## <a name="deploy-perimeter-networks-for-security-zones"></a>Implementar redes de perímetro para zonas de segurança
R [rede de perímetro](https://docs.microsoft.com/azure/best-practices-network-security) (também conhecida como DMZ) é um segmento de rede física ou lógica que fornece uma camada adicional de segurança entre os seus recursos e da internet. Dispositivos de controlo de acesso de rede especializado na borda da rede de perímetro permitem apenas tráfego desejado em sua rede virtual.

Redes de perímetro são úteis porque pode concentrar-se a gestão de controlo de acesso de rede, monitorização, registo e relatórios sobre os dispositivos na borda da rede virtual do Azure. Aqui, normalmente, ativar a ataques denial of prevenção de serviço (DDoS), sistemas de prevenção de intrusões/detecção de intrusões (IDS/IPS), as regras de firewall e as políticas, filtragem web, antimalware de rede e muito mais. Os dispositivos de segurança de rede ficam entre a internet e rede virtual do Azure e tem uma interface em ambas as redes.

Embora isso seja o projeto básico de uma rede de perímetro, existem vários designs diferentes, como agraciada, chamamos alojada e multihomed.

Recomendamos para todas as implementações de alta segurança que considere a utilização de uma rede de perímetro para aumentar o nível de segurança de rede para os seus recursos do Azure.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Evitar exposição à Internet com ligações WAN dedicadas
Muitas organizações optou pela híbrida rota IT. Em TI híbridos, alguns dos ativos de informações da empresa são no Azure, enquanto outros permanecem no local. Em muitos casos, alguns componentes de um serviço estão em execução no Azure, enquanto outros componentes permanecem no local.

No cenário de TI a híbrida, normalmente há algum tipo de conectividade em vários locais. Conectividade entre instalações, permite que a empresa ligar as redes no local a redes virtuais do Azure. Duas soluções de conectividade em vários locais estão disponíveis:

* **Site-to-site VPN**: É uma tecnologia estabelecida, fiável e fidedigna, mas a ligação tem lugar na Internet. Largura de banda é restrito a um máximo de cerca de 200 Mbps. VPN site a site é uma opção desejável em alguns cenários e será discutido mais na seção [RDP/SSH de desativar o acesso às máquinas virtuais](#disable-rdpssh-access-to-virtual-machines).
* **O Azure ExpressRoute**: Recomendamos que utilize [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) para a conectividade em vários locais. O ExpressRoute é uma WAN dedicada ligação entre a sua localização no local ou um fornecedor de alojamento do Exchange. Como se trata de uma ligação de telecomunicações, seus dados não viajam através da internet e, portanto, não são expostos a riscos potenciais de comunicações da internet.

## <a name="optimize-uptime-and-performance"></a>Otimizar o tempo de atividade e o desempenho
Se um serviço estiver desativado, não não possível aceder a informações. Se o desempenho é tão ruim que os dados não são utilizáveis, pode considerar os dados ficará inacessível. Da perspectiva de segurança, terá de fazer qualquer coisa que pode certificar-se de que seus serviços têm tempo de atividade ideal e o desempenho.

Um método eficaz e popular para melhorar a disponibilidade e o desempenho é o balanceamento de carga. Balanceamento de carga é um método de distribuição de tráfego de rede entre servidores que fazem parte de um serviço. Por exemplo, se tiver servidores web front-end como parte do seu serviço, pode utilizar o balanceamento de carga para distribuir o tráfego entre seus vários servidores web front-end.

Esta distribuição de tráfego aumenta a disponibilidade porque se um dos servidores web ficar indisponível, o Balanceador de carga deixará de enviar tráfego para esse servidor e redireciona-o para os servidores que ainda estão online. Balanceamento de carga também ajuda a desempenho, porque a sobrecarga de memória, rede e processador para servir pedidos é distribuída por todos os servidores com balanceamento de carga.

Recomendamos que empregam o balanceamento de carga sempre que possível e, conforme adequado para os seus serviços. Seguem-se cenários tanto a nível da rede virtual do Azure, como a nível global, juntamente com as opções de balanceamento de carga para cada um.

**Cenário**: Tem uma aplicação que:

- Requer pedidos a partir da mesma sessão de utilizador/cliente para atingir a mesma máquina virtual de back-end. Exemplos disso são compras carrinho aplicações e servidores de email da web.
- Aceita apenas uma ligação segura, para que a comunicação sem encriptação para o servidor não é uma opção aceitável.
- Requer vários pedidos HTTP na mesma ligação de TCP de execução longa a serem encaminhadas ou de carga balanceada para diferentes servidores de back-end.

**Opção de balanceamento de carga**: Uso [Gateway de aplicação Azure](../application-gateway/application-gateway-introduction.md), um balanceador de carga de tráfego de web HTTP. Gateway de aplicação suporta a encriptação SSL de ponta a ponta e [terminação de SSL](../application-gateway/application-gateway-introduction.md) no gateway. Servidores Web, em seguida, podem ser livres de encriptação e de sobrecarga de desencriptação e de tráfego que flui não encriptada para servidores de back-end.

**Cenário**: Terá de ligações de entrada do balanceamento de carga da internet entre os servidores localizados numa rede virtual do Azure. Os cenários são quando:

- Ter aplicativos sem monitoração de estado que aceitam pedidos recebidos da internet.
- Não necessitam de sessões adesivas ou descarga de SSL. Sessões adesivas é um método usado com balanceamento de carga de aplicação, para alcançar a afinidade de servidor.

**Opção de balanceamento de carga**: Utilizar o portal do Azure para [criar um balanceador de carga externo](../load-balancer/quickstart-create-basic-load-balancer-portal.md) que se propaga pedidos recebidos em várias VMs para fornecer um nível mais elevado de disponibilidade.

**Cenário**: Terá de ligações de balanceamento de carga de VMs que não estão na internet. Na maioria dos casos, as ligações que são aceites para balanceamento de carga são iniciadas por dispositivos numa rede virtual do Azure, como a instâncias do SQL Server ou servidores web internos.   
**Opção de balanceamento de carga**: Utilizar o portal do Azure para [criar um balanceador de carga interno](../load-balancer/quickstart-create-basic-load-balancer-powershell.md) que se propaga pedidos recebidos em várias VMs para fornecer um nível mais elevado de disponibilidade.

**Cenário**: Tem de balanceamento de carga global porque:

- Ter uma solução de cloud que é amplamente distribuída por várias regiões e requer o nível mais alto de tempo de atividade (disponibilidade) possível.
- Tem do nível mais alto de tempo de atividade possíveis para se certificar de que o seu serviço está disponível, mesmo que todo o datacenter fica indisponível.

**Opção de balanceamento de carga**: Utilize o Gestor de tráfego do Azure. O Gestor de tráfego torna possível carregar saldo ligações aos seus serviços com base na localização do utilizador.

Por exemplo, se o usuário faz uma solicitação ao seu serviço da UE, a ligação é direcionada para seus serviços localizados num datacenter da UE. Esta parte do Gestor de tráfego global carregar balanceamento ajuda a melhorar o desempenho porque a ligação para o datacenter mais próximo é mais rápido do que ligar a centros de dados que estão perto de.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Desativar o acesso RDP/SSH para máquinas virtuais
É possível aceder a máquinas virtuais do Azure, utilizando [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) e o [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) protocolo (SSH). Esses protocolos ativar a gestão de VMs a partir de localizações remotas e são padrão na computação de datacenter.

O problema de segurança potencial do uso estes protocolos através da internet é que os atacantes podem utilizar [força bruta](https://en.wikipedia.org/wiki/Brute-force_attack) técnicas para obter acesso a máquinas virtuais do Azure. Depois dos invasores obtêm acesso, pode utilizar a VM como um ponto de início para comprometer a outras máquinas na sua rede virtual ou até mesmo dispositivos em rede fora do Azure de ataques.

Recomendamos que desative o acesso direto de RDP e SSH para máquinas virtuais do Azure da internet. Depois de ter acesso direto de RDP e SSH da internet é desabilitado, tem outras opções que pode utilizar para aceder a estas VMs para a gestão remota.

**Cenário**: Permitir que um único utilizador ligar a uma rede virtual do Azure através da internet.   
**Opção**: [VPN Point-to-site](../vpn-gateway/vpn-gateway-point-to-site-create.md) é outro termo para uma ligação de cliente/servidor VPN de acesso remoto. Após é estabelecida a ligação de ponto a site, o utilizador pode utilizar RDP ou SSH para ligar a quaisquer VMs localizadas na rede virtual do Azure que o utilizador ligou através de VPN ponto a site. Isso assume que o utilizador está autorizado a atingem aqueles VMs.

VPN Point-to-site é mais seguro do que as ligações de RDP ou SSH diretas porque o utilizador tem de se autenticar duas vezes antes de ligar a uma VM. Em primeiro lugar, o utilizador tem de autenticar (e ser autorizado) para estabelecer a ligação de VPN ponto a site. Em segundo lugar, o utilizador tem de autenticar (e ser autorizado) para estabelecer a sessão RDP ou SSH.

**Cenário**: Permita aos utilizadores na sua rede no local ligar a VMs na rede virtual do Azure.   
**Opção**: R [VPN site a site](../vpn-gateway/vpn-gateway-site-to-site-create.md) liga uma rede de toda a outra rede através da internet. Pode utilizar uma VPN de site a site para ligar a sua rede no local a uma rede virtual do Azure. Os utilizadores na sua rede no local para se ligar através do protocolo RDP ou SSH através da ligação de VPN de site a site. Não tem de permitir o acesso direto de RDP ou SSH através da internet.

**Cenário**: Utilize um link WAN dedicado para fornecer funcionalidade semelhante à VPN site a site.   
**Opção**: Uso [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Ele fornece uma funcionalidade semelhante à VPN site a site. As principais diferenças são:

- A ligação WAN dedicada não atravessa a internet.
- Links WAN dedicados são normalmente mais estáveis e têm um desempenho melhor.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Proteger os seus recursos críticos de serviço do Azure para apenas as suas redes virtuais
Utilize pontos finais de serviço de rede virtual para expandir o seu espaço de endereços privados da rede virtual e a identidade de rede virtual para os serviços do Azure, através de uma ligação direta. Os pontos finais permitem-lhe obter os seus recursos críticos de serviço do Azure apenas para as suas redes virtuais. Tráfego de rede virtual para o serviço do Azure permanece sempre na rede backbone do Microsoft Azure.

Os pontos finais de serviço oferecem as seguintes vantagens:

- **Segurança para os seus recursos de serviço do Azure melhorada**: Com os pontos finais de serviço, os recursos de serviço do Azure podem obtidos para a sua rede virtual. Proteger recursos de serviço a uma rede virtual fornece segurança melhorada ao remover totalmente acesso de internet público a recursos e a permitir o tráfego apenas a partir da sua rede virtual.
- **Encaminhamento ideal para o tráfego de serviço do Azure da sua rede virtual**: Quaisquer rotas na sua rede virtual que imponham o tráfego de internet para no local e/ou aplicações virtuais, conhecidas como imposição de túnel, também forçam o tráfego de serviço do Azure para seguir a mesma rota que o tráfego de internet. Os pontos finais de serviço fornecem encaminhamento ótimo para o tráfego do Azure.

  Os pontos finais assumem sempre o tráfego de serviço diretamente a partir da sua rede virtual para o serviço na rede backbone do Azure. Manter o tráfego na rede principal do Azure permite-lhe continuar a auditar e monitorizar tráfego de internet de saída das suas redes virtuais, através de imposição de túnel, sem afetar o tráfego de serviço. Saiba mais sobre [rotas definidas pelo utilizador e o protocolo de túnel forçado](../virtual-network/virtual-networks-udr-overview.md).

- **Fácil de configurar com menos overhead de gestão**: Não precisa mais endereços IP públicos reservados nas suas redes virtuais para proteger os recursos do Azure através de uma firewall do IP. Não são necessários dispositivos NAT ou de gateway para configurar os pontos finais de serviço. Basta um clique numa sub-rede para configurar os pontos finais de serviço. Não há nenhum overhead adicional para manter os pontos de extremidade.

Para saber mais sobre pontos finais de serviço e os serviços do Azure e regiões que, pontos finais de serviço estão disponíveis para, veja [pontos finais de serviço de rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="next-step"></a>Passo seguinte
Ver [padrões e práticas recomendadas de segurança do Azure](security-best-practices-and-patterns.md) para obter mais melhores práticas de segurança a utilizar quando estiver conceber, implementar e gerir soluções na cloud ao utilizar o Azure.
