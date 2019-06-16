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
ms.date: 05/05/2019
ms.author: TomSh
ms.openlocfilehash: 137b23432a80d3ceeb0f96cd863514c9ee824c9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67127231"
---
# <a name="azure-best-practices-for-network-security"></a>Melhores práticas do Azure para segurança de rede
Este artigo discute uma coleção de melhores práticas do Azure para melhorar a segurança de rede. Essas práticas recomendadas são derivadas da nossa experiência com redes do Azure e as experiências dos clientes, como mesmo.

Para cada melhor prática, este artigo explica:

* O que é a melhor prática
* Por que pretende ativar essa prática recomendada
* Se efetuar a ativação para ativar a melhor prática, o que pode ser o resultado
* Possíveis alternativas para a prática recomendada
* Como pode aprender permitir a melhor prática

Essas práticas recomendadas baseiam-se numa opinião de consenso e capacidades da plataforma Azure e conjuntos de recursos, tal como existem no momento que este artigo foi escrito. Opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

## <a name="use-strong-network-controls"></a>Utilizar controlos de rede segura
Pode ligar [máquinas virtuais do Azure (VMs)](https://azure.microsoft.com/services/virtual-machines/) e aplicações para outros dispositivos de rede, colocando-os no [redes virtuais do Azure](https://docs.microsoft.com/azure/virtual-network/). Ou seja, pode ligar placas de interface de rede virtual a uma rede virtual para permitir comunicações baseadas em TCP/IP entre dispositivos de rede ativada. As máquinas virtuais ligadas a uma rede virtual do Azure, pode ligar-se a dispositivos na mesma rede virtual, redes virtuais diferentes, internet ou suas próprias redes no local.

Quando planear a sua rede e a segurança da sua rede, recomendamos que centralize:

- Gestão de funções de rede de principais, como o ExpressRoute, rede virtual e aprovisionamento de sub-rede e endereçamento IP.
- Governação de elementos de segurança de rede, como as funções de aplicação virtual de rede, como o ExpressRoute, rede virtual e aprovisionamento de sub-rede e endereçamento IP.

Se utilizar um conjunto comum de ferramentas de gestão para monitorizar a sua rede e a segurança da sua rede, obtém visibilidade clara em ambos. Uma estratégia de segurança simples e unificada reduz erros porque aumenta compreensão humana e a confiabilidade de automatização.

## <a name="logically-segment-subnets"></a>Logicamente as sub-redes de segmento
Redes virtuais do Azure são semelhantes às redes locais na sua rede no local. A idéia por trás de uma rede virtual do Azure é o que cria uma rede, com base num único privado espaço de endereços IP, no qual pode colocar todas as suas máquinas virtuais do Azure. Os espaços de endereços IP privados disponíveis estão numa classe (10.0.0.0/8), classe B (172.16.0.0/12), e intervalos de classe C (192.168.0.0/16).

Melhores práticas para segmentar logicamente sub-redes incluem:

**Melhor prática**: Não atribua regras com intervalos de amplo de permissão (por exemplo, permitir 0.0.0.0 por meio de 255.255.255.255).  
**Detalhe**: Certifique-se de resolução de problemas de procedimentos desencorajar ou banir como configurar esses tipos de regras. Estas permitem líder de regras para uma falsa sensação de segurança e são frequentemente encontradas e exploradas por equipes de vermelhas.

**Melhor prática**: Segmente o espaço de endereçamento maior em sub-redes.   
**Detalhe**: Uso [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)-com base em princípios de sub-redes para criar as sub-redes.

**Melhor prática**: Crie controlos de acesso de rede entre sub-redes. O encaminhamento entre sub-redes ocorre automaticamente e não precisa de configurar manualmente as tabelas de roteamento. Por predefinição, não existem nenhum controle de acesso de rede entre as sub-redes que cria numa rede virtual do Azure.   
**Detalhe**: Utilize um [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) para proteger contra tráfego não solicitado em sub-redes do Azure. Grupos de segurança de rede são dispositivos de inspeção de pacotes simples e com monitorização de estado que utilizar a abordagem de 5 cadeias de identificação (IP de origem, porta de origem, IP de destino, porta de destino e protocolo de camada 4) para criar regras de permissão/negação de mensagens em fila para o tráfego de rede. Permitir ou negar o tráfego de e para um único endereço IP, para e de vários endereços IP, ou de e para sub-redes inteiras.

Quando utiliza grupos de segurança de rede para controlo de acesso de rede entre sub-redes, pode colocar recursos que pertencem à mesma zona de segurança ou função nas suas próprias sub-redes.

**Melhor prática**: Evite pequenas redes virtuais e sub-redes para garantir a simplicidade e flexibilidade.   
**Detalhe**: A maioria das organizações adicionar mais recursos do que inicialmente planeada e a alocação de endereços é intensamente trabalhoso. Utilização de sub-redes pequeno adiciona o valor de segurança limitada e o mapeamento de um grupo de segurança de rede para cada sub-rede adiciona sobrecarga. Defina sub-redes amplamente para se certificar de que tem a flexibilidade para crescimento.

**Melhor prática**: Simplificar o gerenciamento de regra de grupo de segurança de rede, definindo [grupos de segurança de aplicativo](https://docs.microsoft.com/rest/api/virtualnetwork/applicationsecuritygroups).  
**Detalhe**: Defina um grupo de segurança de aplicações para as listas de endereços IP que acha que podem ser alteradas no futuro ou ser utilizadas em vários grupos de segurança de rede. Certifique-se para o nome de segurança de aplicativos grupos claramente por isso, outras pessoas podem compreender seu conteúdo e o objetivo.

## <a name="adopt-a-zero-trust-approach"></a>Adotar uma abordagem de confiança do Zero
Redes baseadas no perímetro operam no pressuposto de que todos os sistemas dentro de uma rede podem ser confiáveis. Mas os funcionários de hoje em dia aceder aos recursos de suas organizações de qualquer lugar numa variedade de dispositivos e aplicações, que torna perímetro controlos de segurança irrelevante. Políticas de controlo de acesso, concentre-se apenas no quem podem aceder a um recurso não são suficientes. Para dominar o equilíbrio entre segurança e produtividade, os administradores de segurança também tem de considerar *como* um recurso está sendo acessado.

Redes precisam deixar de defesas tradicionais porque redes poderão ficar sujeitas a falhas: um atacante pode comprometer um único ponto final dentro dos limites fidedignos e, em seguida, expanda rapidamente sua posição em toda a rede. [Zero confiança](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) redes eliminam o conceito de confiança com base na localização de rede dentro de um perímetro. Em vez disso, arquiteturas de confiança de Zero utilizam afirmações de confiança de dispositivo e utilizador para impeçam o acesso aos dados organizacionais e de recursos. Para novas iniciativas, Adote as abordagens de confiança do Zero que validam a confiança no momento do acesso.

Melhores práticas são:

**Melhor prática**: Concede o acesso condicional aos recursos com base no dispositivo, identidade, garantia, localização de rede e muito mais.  
**Detalhe**: [Acesso condicional do Azure AD](../active-directory/conditional-access/overview.md) permite aplicar os controlos de acesso corretas através da implementação de decisões de controlo de acesso automatizados com base nas condições necessárias. Para obter mais informações, consulte [gerir o acesso a gestão do Azure com o acesso condicional](../role-based-access-control/conditional-access-azure-management.md).

**Melhor prática**: Ative o acesso de porta apenas após a aprovação de fluxo de trabalho.  
**Detalhe**: Pode usar [acesso VM just-in-time no Centro de segurança do Azure](../security-center/security-center-just-in-time.md) para bloquear o tráfego de entrada para as VMs do Azure, reduzindo a exposição a ataques, fornecendo acesso fácil para ligar a VMs quando necessário.

**Melhor prática**: Conceder permissões temporárias para executar tarefas com privilégios, que impede os utilizadores não autorizados ou mal-intencionados de obter acesso depois das permissões tiverem expirado. O acesso é concedido somente quando os utilizadores a necessidade.  
**Detalhe**: Utilize o acesso just-in-time no Azure AD Privileged Identity Management ou numa solução de terceiros para conceder permissões para executar tarefas com privilégios.

Zero confiança é a próxima evolução em segurança de rede. O estado dos ataques cibernéticos orienta as empresas obtenham a mentalidade "Falha assumida", mas essa abordagem não deve ser limitativos. Zero redes de confiança protegerem dados empresariais e recursos, garantindo que as organizações podem criar uma área de trabalho moderna através da utilização de tecnologias que capacitam os funcionários sejam produtivos em qualquer altura, em qualquer lugar, de qualquer forma.

## <a name="control-routing-behavior"></a>Controlar o comportamento de encaminhamento
Quando colocar uma máquina virtual numa rede virtual do Azure, a VM pode ligar a qualquer outra VM na mesma rede virtual, mesmo que as outras VMs estiverem em sub-redes diferentes. Isso é possível porque este tipo de comunicação de permite que uma coleção de rotas do sistema ativada por predefinição. Estas rotas predefinidas que permitem que as VMs na mesma rede virtual para iniciar as ligações entre si e com a internet (para comunicações de saída à internet apenas).

Embora as rotas de sistema predefinidas são úteis para muitos cenários de implementação, há horas em que pretende personalizar a configuração de roteamento para as suas implementações. Pode configurar o endereço de próximo salto para atingir destinos específicos.

Recomendamos que configure [rotas definidas pelo utilizador](../virtual-network/virtual-networks-udr-overview.md) quando implementa uma aplicação de segurança para uma rede virtual. Vamos falar sobre isso numa seção posterior intitulada [proteger os seus recursos críticos de serviço do Azure para apenas as suas redes virtuais](azure-security-network-security-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Rotas definidas pelo utilizador não são necessárias e as rotas de sistema padrão geralmente funcionam.
>
>

## <a name="use-virtual-network-appliances"></a>Utilizar aplicações de rede virtual
Grupos de segurança de rede e o encaminhamento definido pelo utilizador, podem fornecer uma determinada medida de segurança de rede em camadas de rede e transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model). Mas, em algumas situações, quer ou precisa de ativar a segurança nos níveis alto da pilha. Nesse tipo de situação, recomendamos que implemente aplicações de segurança de rede virtual fornecidas pelos parceiros do Azure.

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

Redes de perímetro são úteis porque pode concentrar-se a gestão de controlo de acesso de rede, monitorização, registo e relatórios sobre os dispositivos na borda da rede virtual do Azure. Uma rede de perímetro é onde normalmente permitem a ataques denial of prevenção de serviço (DDoS), sistemas de prevenção de intrusões/detecção de intrusões (IDS/IPS), as regras de firewall e as políticas, filtragem web, antimalware de rede e muito mais. Os dispositivos de segurança de rede ficam entre a internet e rede virtual do Azure e tem uma interface em ambas as redes.

Embora isso seja o projeto básico de uma rede de perímetro, existem vários designs diferentes, como agraciada, chamamos alojada e multihomed.

Com base no conceito de confiança Zero mencionado anteriormente, é recomendável que considere a utilização de uma rede de perímetro para todas as implementações de alta segurança para aumentar o nível de controlo de acesso e segurança de rede para os seus recursos do Azure. Pode utilizar o Azure ou uma solução de terceiros para fornecer uma camada adicional de segurança entre os seus recursos e da internet:

- Controles nativos do Azure. [Firewall do Azure](../firewall/overview.md) e o [firewall de aplicações web no Gateway de aplicação](../application-gateway/overview.md#web-application-firewall) oferecem básicos de segurança com um firewall totalmente com monitoração de estado como um serviço, a elevada disponibilidade incorporada, a escalabilidade da cloud sem restrições, a filtragem de FQDN , suporte para conjuntos de regras de núcleo OWASP e a configuração simples e a configuração.
- Ofertas de terceiros. Pesquisa o [do Azure Marketplace](https://azuremarketplace.microsoft.com/) para firewall de próxima geração (NGFW) e outras ofertas de terceiros que fornecem ferramentas de segurança familiar e significativamente aprimorados níveis de segurança de rede. Configuração pode ser mais complexa, mas uma oferta de terceiros pode permitir-lhe utilizar as capacidades existentes e conjuntos de competências.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Evitar exposição à internet com ligações WAN dedicadas
Muitas organizações optou pela híbrida rota IT. Com TI híbridos, alguns dos recursos de informações da empresa são no Azure e outros permanecem no local. Em muitos casos, alguns componentes de um serviço estão em execução no Azure, enquanto outros componentes permanecem no local.

Num cenário de TI híbrido, normalmente há algum tipo de conectividade em vários locais. Conectividade entre instalações, permite que a empresa ligar as redes no local a redes virtuais do Azure. Duas soluções de conectividade em vários locais estão disponíveis:

* [Site-to-site VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). É uma tecnologia estabelecida, fiável e fidedigna, mas a ligação tem lugar na Internet. Largura de banda é restrito a um máximo de aproximadamente 1.25 Gbps. VPN site a site é uma opção desejável em alguns cenários.
* **O Azure ExpressRoute**. Recomendamos que utilize [ExpressRoute](../expressroute/expressroute-introduction.md) para a conectividade em vários locais. O ExpressRoute permite-lhe expandir as redes no local para a cloud da Microsoft através de uma ligação privada facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode estabelecer ligações a serviços cloud da Microsoft, como o Azure, Office 365 e Dynamics 365. O ExpressRoute é uma WAN dedicada ligação entre a sua localização no local ou um fornecedor de alojamento do Microsoft Exchange. Como se trata de uma ligação de telecomunicações, seus dados não viajam através da internet, para que ele não é exposto a riscos potenciais de comunicações da internet.

A localização da sua ligação de ExpressRoute pode afetar a capacidade de firewall, a escalabilidade, confiabilidade e visibilidade de tráfego de rede. Precisará identificar onde encerrar ExpressRoute no existentes (redes no local). Pode:

- Termine fora do firewall (o paradigma de rede de perímetro), se necessitar de visibilidade para o tráfego, se tiver de continuar uma prática existente de isolar centros de dados, ou se está colocando unicamente extranet recursos no Azure.
- Termine dentro do firewall (o paradigma de extensão de rede). Esta é a recomendação predefinida. Em todos os outros casos, recomendamos que tratando do Azure como um centro de dados enésimo.

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

## <a name="next-steps"></a>Passos Seguintes
Ver [padrões e práticas recomendadas de segurança do Azure](security-best-practices-and-patterns.md) para obter mais melhores práticas de segurança a utilizar quando estiver conceber, implementar e gerir soluções na cloud ao utilizar o Azure.
