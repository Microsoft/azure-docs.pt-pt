---
title: Boas práticas para segurança de rede - Microsoft Azure
description: Este artigo fornece um conjunto de boas práticas para a segurança da rede usando capacidades Azure incorporadas.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2019
ms.author: TomSh
ms.openlocfilehash: 3ded20f37a394e6adf726ad40c01aa36d41e4e8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299350"
---
# <a name="azure-best-practices-for-network-security"></a>As melhores práticas do Azure para a segurança da rede
Este artigo discute uma coleção de boas práticas azure para melhorar a segurança da sua rede. Estas boas práticas derivam da nossa experiência com o networking Azure e as experiências de clientes como você.

Para cada melhor prática, este artigo explica:

* Qual é a melhor prática
* Por que quer ativar as melhores práticas
* Qual pode ser o resultado se não permitir as melhores práticas
* Possíveis alternativas às melhores práticas
* Como pode aprender a permitir as melhores práticas

Estas boas práticas baseiam-se numa opinião consensual, e as capacidades da plataforma Azure e os conjuntos de funcionalidades, tal como existem no momento em que este artigo foi escrito. As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

## <a name="use-strong-network-controls"></a>Utilize controlos de rede fortes
Pode ligar [máquinas virtuais Azure (VMs)](https://azure.microsoft.com/services/virtual-machines/) a outros dispositivos em rede colocando-as em [redes virtuais Azure](../../virtual-network/index.yml). Ou seja, pode ligar cartões de interface de rede virtuais a uma rede virtual para permitir comunicações baseadas em TCP/IP entre dispositivos ativados por rede. As máquinas virtuais ligadas a uma rede virtual Azure podem ligar-se a dispositivos na mesma rede virtual, redes virtuais diferentes, na internet ou nas suas próprias redes no local.

Enquanto planeia a sua rede e a segurança da sua rede, recomendamos que centralize:

- Gestão de funções de rede core como ExpressRoute, fornecimento de rede virtual e sub-rede, e endereçoIP.
- Governação de elementos de segurança da rede, tais como funções de aparelhos virtuais de rede como expressRoute, fornecimento de rede virtual e sub-rede, e endereçoIP.

Se utilizar um conjunto comum de ferramentas de gestão para monitorizar a sua rede e a segurança da sua rede, obtém uma visibilidade clara em ambos. Uma estratégia de segurança simples e unificada reduz os erros porque aumenta a compreensão humana e a fiabilidade da automação.

## <a name="logically-segment-subnets"></a>Subredes de segmento logicamente
As redes virtuais Azure são semelhantes às LANs na sua rede no local. A ideia por trás de uma rede virtual Azure é que você cria uma rede, baseada num único espaço de endereço IP privado, no qual você pode colocar todas as suas máquinas virtuais Azure. Os espaços de endereço IP privados disponíveis estão nas gamas Classe A (10.0.0.0.8), Classe B (172.16.0.0.0.0/12) e Classe C (192.168.0.0.0/16).

As melhores práticas para segmentar logicamente subredes incluem:

**Boas práticas**: Não atribuir regras com amplas gamas (por exemplo, permitir 0,0.0 a 255.255.255.255.255).  
**Pormenores**: Certifique-se de que os procedimentos de resolução de problemas desencorajam ou proíbem a criação deste tipo de regras. Estas regras permitem uma falsa sensação de segurança e são frequentemente encontradas e exploradas por equipas vermelhas.

**Boas práticas**: Segmente o espaço de endereço maior em subredes.   
**Detalhe**: Utilize princípios de sub-rede baseados em [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)para criar as suas subredes.

**Boas práticas**: Criar controlos de acesso à rede entre subredes. O encaminhamento entre subredes acontece automaticamente e não é necessário configurar manualmente as tabelas de encaminhamento. Por predefinição, não existem controlos de acesso à rede entre as subredes que cria numa rede virtual Azure.   
**Detalhe**: Utilize um grupo de segurança de [rede](/azure/virtual-network/virtual-networks-nsg) para proteger contra o tráfego não solicitado nas subredes Azure. Os grupos de segurança da rede são dispositivos de inspeção simples e audais que utilizam a abordagem de 5 tuple (fonte IP, porta de origem, IP de destino, porta de destino e protocolo de camada 4) para criar regras de permitir/negar regras para o tráfego de rede. Permite ou nega o tráfego de e para um único endereço IP, de e para vários endereços IP, ou de e para subredes inteiras.

Quando utiliza grupos de segurança de rede para controlo de acesso à rede entre subredes, pode colocar recursos que pertencem à mesma zona de segurança ou papel nas suas próprias subredes.

**Boas práticas**: Evite pequenas redes virtuais e subredes para garantir a simplicidade e flexibilidade.   
**Detalhe**: A maioria das organizações adiciona mais recursos do que inicialmente planeado, e a reafectação de endereços é de mão de obra intensiva. A utilização de pequenas subredes adiciona um valor de segurança limitado, e mapear um grupo de segurança de rede a cada subnet adiciona sobrecarga. Defina as subredes em geral para garantir que tem flexibilidade para o crescimento.

**Boas práticas**: Simplificar a gestão das regras do grupo de segurança da rede definindo grupos de segurança de [aplicações](https://azure.microsoft.com/blog/applicationsecuritygroups/).  
**Detalhe**: Defina um Grupo de Segurança de Aplicações para listas de endereços IP que você pensa que podem mudar no futuro ou ser usados em muitos grupos de segurança da rede. Certifique-se de nomear claramente os Grupos de Segurança de Aplicações para que outros possam compreender o seu conteúdo e propósito.

## <a name="adopt-a-zero-trust-approach"></a>Adotar uma abordagem Zero Trust
As redes baseadas em perímetro supõe-se que todos os sistemas dentro de uma rede podem ser confiáveis. Mas os funcionários de hoje acedem aos recursos da sua organização a partir de qualquer lugar em vários dispositivos e aplicações, o que torna os controlos de segurança do perímetro irrelevantes. As políticas de controlo de acesso que se concentram apenas em quem pode aceder a um recurso não são suficientes. Para dominar o equilíbrio entre segurança e produtividade, os administradores de segurança também precisam de ter em conta a *forma como* um recurso está a ser acedido.

As redes precisam de evoluir a partir das defesas tradicionais porque as redes podem ser vulneráveis a violações: um intruso pode comprometer um único ponto final dentro da fronteira de confiança e, em seguida, expandir rapidamente uma posição de pé em toda a rede. As redes [Zero Trust](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) eliminam o conceito de confiança com base na localização da rede dentro de um perímetro. Em vez disso, as arquiteturas Zero Trust usam dispositivos e a confiança dos utilizadores reclama o acesso ao acesso a dados e recursos organizacionais. Para novas iniciativas, adote abordagens zero trust que validem a confiança no momento do acesso.

As melhores práticas são:

**Boas práticas**: Dar acesso condicional a recursos baseados em dispositivo, identidade, garantia, localização da rede e muito mais.  
**Detalhe**: [O Acesso Condicional Azure AD](/azure/active-directory/conditional-access/overview) permite-lhe aplicar os controlos de acesso adequados implementando decisões automatizadas de controlo de acesso com base nas condições exigidas. Para mais informações, consulte [Gerir o acesso à gestão do Azure com Acesso Condicional.](../../role-based-access-control/conditional-access-azure-management.md)

**Boas práticas**: Permitir o acesso à porta apenas após a aprovação do fluxo de trabalho.  
**Detalhe:** Pode utilizar [o acesso VM just-in-time no Azure Security Center](../../security-center/security-center-just-in-time.md) para bloquear o tráfego de entrada nos seus VMs Azure, reduzindo a exposição a ataques, proporcionando um fácil acesso à ligação aos VMs quando necessário.

**Boas práticas**: Conceda permissões temporárias para executar tarefas privilegiadas, o que impede que utilizadores maliciosos ou não autorizados tenham acesso após o termo das permissões. O acesso só é concedido quando os utilizadores precisam.  
**Detalhe**: Utilize o acesso just-in-time na Azure AD Privileged Identity Management ou numa solução de terceiros para conceder permissões para executar tarefas privilegiadas.

Zero Trust é a próxima evolução na segurança da rede. O estado dos ciberataques leva as organizações a assumir a mentalidade de "assumir a violação", mas esta abordagem não deve ser limitativa. As redes Zero Trust protegem os dados e recursos corporativos, garantindo ao mesmo tempo que as organizações podem construir um local de trabalho moderno, utilizando tecnologias que capacitem os colaboradores a serem produtivos a qualquer momento, em qualquer lugar, de qualquer forma.

## <a name="control-routing-behavior"></a>Controlar o comportamento de encaminhamento
Quando se coloca uma máquina virtual numa rede virtual Azure, o VM pode ligar-se a qualquer outro VM na mesma rede virtual, mesmo que os outros VMs estejam em subredes diferentes. Isto é possível porque uma coleção de rotas do sistema ativadas por padrão permite este tipo de comunicação. Estas rotas padrão permitem que os VMs na mesma rede virtual iniciem ligações entre si, e com a internet (apenas para comunicações de saída para a internet).

Embora as rotas padrão do sistema sejam úteis para muitos cenários de implementação, existem momentos em que pretende personalizar a configuração de encaminhamento para as suas implementações. Pode configurar o endereço de próximo salto para chegar a destinos específicos.

Recomendamos que configure [as rotas definidas pelo utilizador](../../virtual-network/virtual-networks-udr-overview.md) quando implementar um aparelho de segurança para uma rede virtual. Falamos sobre isso numa secção posterior intitulada garantir os seus recursos críticos de [serviço Azure apenas às suas redes virtuais.](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks)

> [!NOTE]
> Não são necessárias rotas definidas pelo utilizador e as rotas padrão do sistema geralmente funcionam.
>
>

## <a name="use-virtual-network-appliances"></a>Utilize aparelhos de rede virtuais
Os grupos de segurança da rede e o encaminhamento definido pelo utilizador podem fornecer uma certa medida de segurança da rede na rede e nas camadas de transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model). Mas em algumas situações, você quer ou precisa permitir a segurança em altos níveis da pilha. Nestas situações, recomendamos que implemente aparelhos de segurança de rede virtuais fornecidos pelos parceiros Azure.

Os aparelhos de segurança da rede Azure podem oferecer uma melhor segurança do que os controlos de nível de rede proporcionam. As capacidades de segurança da rede de aparelhos de segurança de rede virtuais incluem:


* Firewalling
* Deteção/prevenção de intrusão
* Gestão de vulnerabilidades
* Controlo de aplicações
* Deteção de anomalias baseada em rede
* Filtragem Web
* Antivírus
* Proteção botnet

Para encontrar aparelhos de segurança de rede virtual Azure disponíveis, vá ao [Azure Marketplace](https://azure.microsoft.com/marketplace/) e procure "segurança" e "segurança da rede".

## <a name="deploy-perimeter-networks-for-security-zones"></a>Implementar redes de perímetro para zonas de segurança
Uma rede de [perímetro](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) (também conhecida como DMZ) é um segmento de rede física ou lógica que fornece uma camada adicional de segurança entre os seus ativos e a internet. Dispositivos especializados de controlo de acesso à rede na borda de uma rede de perímetro permitem apenas tráfego desejado na sua rede virtual.

As redes de perímetro são úteis porque pode concentrar a sua gestão de controlo de acesso de rede, monitorização, registo e reporte nos dispositivos na borda da sua rede virtual Azure. Uma rede de perímetro é onde normalmente permite a prevenção de negação de serviço distribuído (DDoS), sistemas de deteção/intrusão de intrusão (IDS/IPS), regras e políticas de firewall, filtragem web, antimalware de rede, e muito mais. Os dispositivos de segurança da rede estão entre a internet e a sua rede virtual Azure e têm uma interface em ambas as redes.

Embora este seja o design básico de uma rede de perímetro, existem muitos projetos diferentes, como back-to-back, tri-homed e multi-homed.

Com base no conceito Zero Trust mencionado anteriormente, recomendamos que considere usar uma rede de perímetro para todas as implementações de alta segurança para aumentar o nível de segurança da rede e controlo de acesso para os seus recursos Azure. Pode utilizar o Azure ou uma solução de terceiros para fornecer uma camada adicional de segurança entre os seus ativos e a internet:

- Controlos nativos de Azure. [O Azure Firewall](/azure/firewall/overview) e a firewall de [aplicação web no Application Gateway](../../application-gateway/features.md#web-application-firewall) oferecem segurança básica com uma firewall totalmente audada como um serviço, alta disponibilidade incorporada, escalabilidade de nuvem sem restrições, filtragem FQDN, suporte para conjuntos de regras centrais OWASP, configuração e configuração simples.
- Ofertas de terceiros. Procure no [Mercado Azure](https://azuremarketplace.microsoft.com/) firewall de próxima geração (NGFW) e outras ofertas de terceiros que forneçam ferramentas de segurança familiares e níveis significativamente melhorados de segurança da rede. A configuração pode ser mais complexa, mas uma oferta de terceiros pode permitir-lhe usar as capacidades e habilidades existentes.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Evite exposição à internet com ligações WAN dedicadas
Muitas organizações escolheram a rota híbrida de TI. Com TI híbrido, alguns dos ativos de informação da empresa estão no Azure, e outros permanecem no local. Em muitos casos, alguns componentes de um serviço estão a funcionar em Azure, enquanto outros componentes permanecem no local.

Num cenário de TI híbrido, há geralmente algum tipo de conectividade transversal. A conectividade transversal permite à empresa ligar as suas redes no local às redes virtuais Azure. Estão disponíveis duas soluções de conectividade transfronteiriças:

* [VPN do site-para-site](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). É uma tecnologia de confiança, confiável e estabelecida, mas a ligação ocorre através da internet. A largura de banda está limitada a um máximo de cerca de 1,25 Gbps. O VPN do site-para-site é uma opção desejável em alguns cenários.
* **Azure ExpressRoute.** Recomendamos que utilize o [ExpressRoute](../../expressroute/expressroute-introduction.md) para a sua conectividade transversal. O ExpressRoute permite-lhe expandir as redes no local para a cloud da Microsoft através de uma ligação privada facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode estabelecer ligações a serviços de cloud da Microsoft como o Azure, Office 365 e Dynamics 365. ExpressRoute é uma ligação WAN dedicada entre a sua localização no local ou um fornecedor de hospedagem microsoft Exchange. Como esta é uma ligação telco, os seus dados não viajam pela internet, por isso não está exposto aos riscos potenciais das comunicações na Internet.

A localização da sua ligação ExpressRoute pode afetar a capacidade de firewall, escalabilidade, fiabilidade e visibilidade do tráfego da rede. Terá de identificar onde encerrar a ExpressRoute nas redes existentes (no local). Pode:

- Termine fora da firewall (o paradigma da rede do perímetro) se necessitar de visibilidade para o tráfego, se precisar de continuar uma prática existente de isolar datacenters, ou se estiver apenas a colocar recursos extranet no Azure.
- Terminar dentro da firewall (o paradigma de extensão da rede). Esta é a recomendação padrão. Em todos os outros casos, recomendamos tratar o Azure como um centro de dados.

## <a name="optimize-uptime-and-performance"></a>Otimizar o tempo e o desempenho
Se um serviço está em baixo, a informação não pode ser acedida. Se o desempenho for tão fraco que os dados são inutilizáveis, pode considerar os dados inacessíveis. Do ponto de vista da segurança, tem de fazer tudo o que puder para garantir que os seus serviços tenham um tempo de uptime e desempenho ideais.

Um método popular e eficaz para melhorar a disponibilidade e o desempenho é o equilíbrio de carga. O equilíbrio de carga é um método de distribuição do tráfego de rede através de servidores que fazem parte de um serviço. Por exemplo, se tiver servidores web front-end como parte do seu serviço, pode utilizar o equilíbrio de carga para distribuir o tráfego pelos seus múltiplos servidores web front-end.

Esta distribuição de tráfego aumenta a disponibilidade porque se um dos servidores web ficar indisponível, o balancer de carga deixa de enviar tráfego para esse servidor e redireciona-o para os servidores que ainda estão online. O equilíbrio de carga também ajuda no desempenho, porque o tamanho do processador, rede e memória para servir pedidos é distribuído por todos os servidores equilibrados em carga.

Recomendamos que utilize o equilíbrio de carga sempre que puder, e conforme apropriado para os seus serviços. Seguem-se cenários tanto a nível da rede virtual Azure como a nível global, juntamente com opções de equilíbrio de carga para cada um.

**Cenário**: Tem uma aplicação que:

- Requer pedidos da mesma sessão de utilizador/cliente para chegar à mesma máquina virtual de back-end. Exemplos disso são aplicações de carrinhos de compras e servidores de correio web.
- Aceita apenas uma ligação segura, por isso a comunicação não encriptada ao servidor não é uma opção aceitável.
- Requer vários pedidos http na mesma ligação TCP de longa duração para ser encaminhado ou carregado equilibrado para diferentes servidores back-end.

**Opção de equilíbrio de cargas:** Utilize o Gateway de [Aplicação Azure,](/azure/application-gateway/application-gateway-introduction)um equilibrador de carga de tráfego web HTTP. O Application Gateway suporta a encriptação SSL de ponta a ponta e a [rescisão ssl](/azure/application-gateway/application-gateway-introduction) no gateway. Os servidores web podem então ser descarregados da encriptação e desencriptação e do tráfego que flui desencriptado para os servidores de back-end.

**Cenário**: É necessário carregar ligações de entrada de saldo a partir da internet entre os seus servidores localizados numa rede virtual Azure. Os cenários são quando:

- Tenha aplicações apátridas que aceitem pedidos de entrada na internet.
- Não exija sessões pegajosas ou descarregamento sl. Sessões pegajosas é um método usado com o Equilíbrio de Carga de Aplicação, para alcançar a afinidade do servidor.

**Opção de equilíbrio de cargas:** Utilize o portal Azure para criar um [equilibrador](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) de carga externo que espalhe pedidos de entrada em vários VMs para fornecer um nível de disponibilidade mais elevado.

**Cenário**: É necessário carregar ligações de equilíbrio de VMs que não estão na internet. Na maioria dos casos, as ligações que são aceites para o equilíbrio de carga são iniciadas por dispositivos numa rede virtual Azure, como instâncias do SQL Server ou servidores internos da web.   
**Opção de equilíbrio de cargas:** Utilize o portal Azure para criar um [equilibrador de carga interno](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) que espalhe pedidos de entrada em vários VMs para fornecer um nível de disponibilidade mais elevado.

**Cenário**: Precisa de um equilíbrio global de carga porque:

- Tenha uma solução cloud que é amplamente distribuída por várias regiões e requer o maior nível de tempo de uptime (disponibilidade) possível.
- Precisa do nível mais alto possível para se certificar de que o seu serviço está disponível mesmo que todo um datacenter fique indisponível.

**Opção de equilíbrio de cargas**: Utilize o Gestor de Tráfego Azure. O Gestor de Tráfego permite carregar ligações de equilíbrio aos seus serviços com base na localização do utilizador.

Por exemplo, se o utilizador fizer um pedido ao seu serviço da UE, a ligação é direcionada aos seus serviços localizados num centro de dados da UE. Esta parte do equilíbrio global de carga do Traffic Manager ajuda a melhorar o desempenho porque a ligação ao datacenter mais próximo é mais rápida do que a ligação a datacenters que estão longe.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Desativar o acesso RDP/SSH a máquinas virtuais
É possível chegar às máquinas virtuais Azure utilizando o [Protocolo de Ambiente](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) de Trabalho Remoto (RDP) e o protocolo Secure [Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Estes protocolos ativam as VMs de gestão a partir de localizações remotas e são o padrão na computação de datacenters.

O potencial problema de segurança com a utilização destes protocolos através da internet é que os atacantes podem usar técnicas de [força bruta](https://en.wikipedia.org/wiki/Brute-force_attack) para ter acesso a máquinas virtuais Azure. Após os atacantes obterem acesso, podem utilizar a VM como um ponto de partida para comprometer outros computadores na rede virtual ou até mesmo atacar dispositivos em redes fora do Azure.

Recomendamos que desative o acesso direto de RDP e SSH às suas máquinas virtuais Azure a partir da internet. Depois de o acesso direto de RDP e SSH a partir da internet ser desativado, tem outras opções que pode utilizar para aceder a estes VMs para gestão remota.

**Cenário**: Permitir que um único utilizador se conectem a uma rede virtual Azure através da internet.   
**Opção**: [VpN ponto-a-site](/azure/vpn-gateway/vpn-gateway-point-to-site-create) é outro termo para uma ligação vpn/servidor de acesso remoto. Após a estabelecer a ligação ponto-a-local, o utilizador pode utilizar RDP ou SSH para se ligar a quaisquer VMs localizados na rede virtual Azure a que o utilizador ligado através de VPN ponto-a-local. Isto pressupõe que o utilizador está autorizado a chegar aos VMs.

A VPN ponto-a-site é mais segura do que ligações diretas de RDP ou SSH porque o utilizador tem de autenticar duas vezes antes de se ligar a um VM. Em primeiro lugar, o utilizador precisa de autenticar (e ser autorizado) para estabelecer a ligação VPN ponto-a-local. Em segundo lugar, o utilizador precisa de autenticar (e ser autorizado) para estabelecer a sessão de RDP ou SSH.

**Cenário**: Ative os utilizadores da sua rede no local a ligarem-se aos VMs na sua rede virtual Azure.   
**Opção**: Uma [VPN site-to-site](/azure/vpn-gateway/vpn-gateway-site-to-site-create) conecta toda uma rede a outra rede através da internet. Pode utilizar uma VPN site-to-site para ligar a sua rede no local a uma rede virtual Azure. Os utilizadores da sua rede no local ligam-se utilizando o protocolo RDP ou SSH através da ligação VPN site-to-site. Não é preciso permitir acesso direto a RDP ou SSH através da internet.

**Cenário**: Utilize um link WAN dedicado para fornecer funcionalidades semelhantes à VPN site-to-site.   
**Opção**: Utilize a [Via Expressa](https://azure.microsoft.com/documentation/services/expressroute/). Fornece funcionalidade semelhante à VPN site-to-site. As principais diferenças são:

- A ligação wan dedicada não atravessa a internet.
- As ligações WAN dedicadas são tipicamente mais estáveis e têm um melhor desempenho.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Proteja os seus recursos de serviço Azure críticos apenas às suas redes virtuais
Utilize pontos finais de serviço de rede virtual para estender o espaço de endereços privados da sua rede virtual e a identidade da sua rede virtual para os serviços Azure, através de uma ligação direta. Os pontos finais permitem-lhe obter os seus recursos críticos de serviço do Azure apenas para as suas redes virtuais. O tráfego da sua rede virtual para o serviço Azure permanece sempre na rede de espinhadorsal Microsoft Azure.

Os pontos finais de serviço oferecem as seguintes vantagens:

- **Segurança dos recursos de serviço do Azure melhorada**: com os pontos finais de serviço, pode proteger os recursos de serviço do Azure na rede virtual. A proteção dos recursos de serviço numa rede virtual oferece segurança melhorada ao remover totalmente o acesso de Internet pública a estes recursos e ao permitir o tráfego apenas a partir da rede virtual.
- **Encaminhamento ideal para tráfego**de serviço Azure a partir da sua rede virtual : Quaisquer rotas da sua rede virtual que forcem o tráfego de internet para os seus locais e/ou aparelhos virtuais, conhecidos como túneis forçados, também forçam o tráfego de serviço saque de serviço azure a tomar o mesmo caminho que o tráfego de internet. Os pontos finais de serviço fornecem encaminhamento ótimo para o tráfego do Azure.

  Os pontos finais levam sempre o tráfego de serviço diretamente da sua rede virtual para o serviço na rede de espinha dorsal Azure. Manter o tráfego na rede de espinha dorsal Azure permite-lhe continuar a auditar e monitorizar o tráfego de internet de saída das suas redes virtuais, através de túneis forçados, sem afetar o tráfego de serviços. Saiba mais sobre [rotas definidas pelo utilizador e túneis forçados.](../../virtual-network/virtual-networks-udr-overview.md)

- **Simples de configurar com menos despesas**de gestão : Já não precisa de endereços IP públicos reservados nas suas redes virtuais para garantir recursos Azure através de uma firewall IP. Não são necessários dispositivos NAT ou de gateway para configurar os pontos finais de serviço. Basta um clique numa sub-rede para configurar os pontos finais de serviço. Não há despesas adicionais para manter os pontos finais.

Para saber mais sobre os pontos finais de serviço e sobre os serviços e regiões azure para os quais estão disponíveis pontos finais de serviço, consulte [os pontos finais](../../virtual-network/virtual-network-service-endpoints-overview.md)do serviço de rede Virtual.

## <a name="next-steps"></a>Passos seguintes
Consulte as [melhores práticas e padrões](best-practices-and-patterns.md) de segurança azure para obter mais práticas de segurança para usar quando está a projetar, implementar e gerir as suas soluções em nuvem utilizando o Azure.
