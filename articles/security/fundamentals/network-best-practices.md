---
title: Melhores práticas para a segurança da rede - Microsoft Azure
description: Este artigo fornece um conjunto de boas práticas para a segurança da rede usando as capacidades do Azure.
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
ms.openlocfilehash: 4793216a12b17c4e4ea03f62d5a0ba512febc232
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735731"
---
# <a name="azure-best-practices-for-network-security"></a>Melhores práticas do Azure para segurança de rede
Este artigo discute uma coleção de boas práticas da Azure para aumentar a segurança da sua rede. Estas boas práticas derivam da nossa experiência com o networking da Azure e as experiências de clientes como você.

Para cada das melhores práticas, este artigo explica:

* Qual é a melhor prática
* Por que quer permitir a melhor prática
* Qual pode ser o resultado se não conseguires permitir as melhores práticas
* Possíveis alternativas às melhores práticas
* Como pode aprender a permitir as melhores práticas

Estas boas práticas baseiam-se numa opinião consensual, e as capacidades da plataforma Azure e os conjuntos de recursos, tal como existem no momento em que este artigo foi escrito. As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

## <a name="use-strong-network-controls"></a>Utilize controlos de rede fortes
Pode ligar [máquinas virtuais Azure (VMs)](https://azure.microsoft.com/services/virtual-machines/) e aparelhos a outros dispositivos em rede, colocando-os em [redes virtuais Azure](../../virtual-network/index.yml). Ou seja, pode ligar cartões de interface de rede virtual a uma rede virtual para permitir comunicações baseadas em TCP/IP entre dispositivos ativados em rede. Máquinas virtuais ligadas a uma rede virtual Azure podem ligar-se a dispositivos na mesma rede virtual, diferentes redes virtuais, internet ou redes próprias no local.

Ao planear a sua rede e a segurança da sua rede, recomendamos que centralize:

- Gestão de funções de rede core como ExpressRoute, fornecimento de rede virtual e sub-rede, e endereçamento IP.
- Governação de elementos de segurança da rede, tais como funções de aparelho virtual de rede como ExpressRoute, fornecimento de rede virtual e sub-rede, e endereçamento IP.

Se utilizar um conjunto comum de ferramentas de gestão para monitorizar a sua rede e a segurança da sua rede, obtém uma visibilidade clara em ambos. Uma estratégia de segurança simples e unificada reduz os erros porque aumenta a compreensão humana e a fiabilidade da automação.

## <a name="logically-segment-subnets"></a>Subesí redes de segmento logicamente
As redes virtuais Azure são semelhantes às DEN na sua rede no local. A ideia por trás de uma rede virtual Azure é que você cria uma rede, baseada num único espaço de endereço IP privado, no qual você pode colocar todas as suas máquinas virtuais Azure. Os espaços de endereço IP privados disponíveis estão nas gamas classe A (10.0.0.0/8), classe B (172.16.0.0/12) e classe C (192.168.0.0/16).

As melhores práticas para segmentar logicamente sub-redes incluem:

**Melhores práticas**: Não atribua regras de permitir com amplas gamas (por exemplo, permitir 0.0.0.0 a 255.255.255.255.255).  
**Pormenor:** Assegurar que os procedimentos de resolução de problemas desencorajam ou proíbem a criação deste tipo de regras. Estas regras permitem que as regras conduzam a uma falsa sensação de segurança e são frequentemente encontradas e exploradas por equipas vermelhas.

**Melhores práticas**: Segmentar o espaço de endereço maior em sub-redes.   
**Detalhe**: Utilize princípios de sub-rede baseados em [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)para criar as suas sub-redes.

**Melhores práticas**: Criar controlos de acesso à rede entre sub-redes. O encaminhamento entre sub-redes acontece automaticamente e não é necessário configurar manualmente as tabelas de encaminhamento. Por predefinição, não existem controlos de acesso à rede entre as sub-redes que cria numa rede virtual Azure.   
**Detalhe**: Utilize um [grupo de segurança](../../virtual-network/virtual-network-vnet-plan-design-arm.md) de rede para proteger contra o tráfego não solicitado nas sub-redes Azure. Os grupos de segurança da rede são simples e imponentes dispositivos de inspeção de pacotes que utilizam a abordagem de 5 tuple (fonte IP, porta de origem, porto de destino, porta de destino e protocolo de camada 4) para criar regras de permitir/negar para o tráfego de rede. Você permite ou nega tráfego de e para um único endereço IP, de e para vários endereços IP, ou de e para sub-redes inteiras.

Quando utiliza grupos de segurança de rede para controlo de acesso à rede entre sub-redes, pode colocar recursos que pertencem à mesma zona de segurança ou papel nas suas próprias sub-redes.

**Melhores práticas**: Evite pequenas redes virtuais e sub-redes para garantir a simplicidade e flexibilidade.   
**Detalhe**: A maioria das organizações adiciona mais recursos do que inicialmente previsto, e a reafectação de endereços é intensiva em termos de mão de obra. A utilização de pequenas sub-redes adiciona um valor de segurança limitado e o mapeamento de um grupo de segurança de rede a cada sub-rede adiciona sobrecarga. Defina sub-redes em geral para garantir que tem flexibilidade para o crescimento.

**Boas práticas**: Simplificar a gestão das regras do grupo de segurança da rede definindo [grupos de segurança de aplicações.](https://azure.microsoft.com/blog/applicationsecuritygroups/)  
**Detalhe**: Defina um Grupo de Segurança de Aplicações para listas de endereços IP que pensa poderem mudar no futuro ou ser utilizados em muitos grupos de segurança de rede. Certifique-se de nomear os Grupos de Segurança da Aplicação claramente para que outros possam entender o seu conteúdo e propósito.

## <a name="adopt-a-zero-trust-approach"></a>Adotar uma abordagem Zero Trust
As redes baseadas em perímetros operam partindo do pressuposto de que todos os sistemas dentro de uma rede podem ser confiáveis. Mas os colaboradores de hoje acedem aos recursos da sua organização a partir de qualquer lugar em uma variedade de dispositivos e aplicações, o que torna os controlos de segurança do perímetro irrelevantes. As políticas de controlo de acesso que se concentram apenas em quem pode aceder a um recurso não são suficientes. Para dominar o equilíbrio entre segurança e produtividade, os administradores de segurança também precisam de ter em conta a *forma como* um recurso está a ser acedido.

As redes precisam de evoluir a partir das defesas tradicionais porque as redes podem ser vulneráveis a violações: um intruso pode comprometer um único ponto final dentro da fronteira de confiança e, em seguida, expandir rapidamente uma posição de pé em toda a rede. [As](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) redes Zero Trust eliminam o conceito de confiança baseado na localização da rede dentro de um perímetro. Em vez disso, as arquiteturas Zero Trust usam dispositivos e a confiança do utilizador alega para gate access a dados e recursos organizacionais. Para novas iniciativas, adote abordagens da Zero Trust que validem a confiança no momento do acesso.

As melhores práticas são:

**Melhores práticas**: Dar Acesso Condicional aos recursos com base no dispositivo, identidade, garantia, localização da rede e muito mais.  
**Detalhe**: [O Acesso Condicionado AZURE AD](../../active-directory/conditional-access/overview.md) permite-lhe aplicar os controlos de acesso certos implementando decisões automatizadas de controlo de acesso com base nas condições exigidas. Para mais informações, consulte [Gerir o acesso à gestão do Azure com Acesso Condicional.](../../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md)

**Melhores práticas**: Permitir o acesso à porta apenas após a aprovação do fluxo de trabalho.  
**Detalhe**: Pode utilizar [o acesso VM just-in-time no Azure Security Center](../../security-center/security-center-just-in-time.md) para bloquear o tráfego de entrada nos seus VMs Azure, reduzindo a exposição a ataques, proporcionando fácil acesso à ligação aos VM quando necessário.

**Boas práticas**: Conceda permissões temporárias para executar tarefas privilegiadas, o que impede que utilizadores maliciosos ou não autorizados tenham acesso após o termo das permissões. O acesso só é concedido quando os utilizadores precisam.  
**Detalhe**: Utilize acesso just-in-time na Gestão de Identidade Privilegiada Azure AD ou numa solução de terceiros para conceder permissões para executar tarefas privilegiadas.

Zero Trust é a próxima evolução na segurança da rede. O estado dos ciberataques leva as organizações a assumirem a mentalidade de "assumir a violação", mas esta abordagem não deve ser limitada. As redes Zero Trust protegem os dados e recursos corporativos, garantindo ao mesmo tempo que as organizações podem construir um local de trabalho moderno, utilizando tecnologias que capacitam os colaboradores a serem produtivos a qualquer momento, em qualquer lugar, de qualquer forma.

## <a name="control-routing-behavior"></a>Controlo do comportamento do encaminhamento
Quando coloca uma máquina virtual numa rede virtual Azure, o VM pode ligar-se a qualquer outro VM na mesma rede virtual, mesmo que os outros VM estejam em sub-redes diferentes. Isto é possível porque uma recolha de rotas do sistema ativadas por padrão permite este tipo de comunicação. Estas rotas predefinidos permitem que os VMs na mesma rede virtual iniciem ligações entre si e com a internet (apenas para comunicações de saída para a internet).

Embora as rotas do sistema predefinidos sejam úteis para muitos cenários de implementação, existem momentos em que pretende personalizar a configuração de encaminhamento para as suas implementações. Você pode configurar o endereço de próximo lúpulo para chegar a destinos específicos.

Recomendamos que configuure [rotas definidas pelo utilizador](../../virtual-network/virtual-networks-udr-overview.md) quando implementar um aparelho de segurança para uma rede virtual. Falamos sobre isso numa secção posterior intitulada [garantir os seus recursos críticos de serviço Azure apenas às suas redes virtuais.](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks)

> [!NOTE]
> As rotas definidas pelo utilizador não são necessárias e as rotas do sistema predefinidas geralmente funcionam.
>
>

## <a name="use-virtual-network-appliances"></a>Utilize aparelhos de rede virtuais
Os grupos de segurança da rede e o encaminhamento definido pelo utilizador podem fornecer uma certa medida de segurança da rede na rede e camadas de transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model). Mas em algumas situações, você quer ou precisa permitir a segurança em altos níveis da pilha. Nestas situações, recomendamos que implemente aparelhos de segurança de rede virtuais fornecidos pelos parceiros Azure.

Os aparelhos de segurança da rede Azure podem proporcionar uma melhor segurança do que os controlos de nível de rede. As capacidades de segurança da rede de aparelhos de segurança de rede virtuais incluem:


* Firewalling
* Deteção/prevenção de intrusões
* Gestão de vulnerabilidades
* Controlo de aplicações
* Deteção de anomalias baseadas em rede
* Filtragem Web
* Antivírus
* Proteção botnet

Para encontrar aparelhos de segurança de rede virtuais Azure disponíveis, vá ao [Azure Marketplace](https://azure.microsoft.com/marketplace/) e procure por "segurança" e "segurança na rede".

## <a name="deploy-perimeter-networks-for-security-zones"></a>Implementar redes de perímetro para zonas de segurança
Uma [rede de perímetro](/azure/architecture/vdc/networking-virtual-datacenter) (também conhecida como DMZ) é um segmento de rede físico ou lógico que fornece uma camada adicional de segurança entre os seus ativos e a internet. Os dispositivos especializados de controlo de acesso à rede na borda de uma rede de perímetro permitem apenas o tráfego desejado na sua rede virtual.

As redes de perímetro são úteis porque pode concentrar a gestão do controlo de acesso à rede, monitorização, registo e reporte nos dispositivos na borda da sua rede virtual Azure. Uma rede de perímetro é onde normalmente permite a prevenção de negação de serviço distribuída (DDoS), sistemas de deteção/prevenção de intrusões (IDS/IPS), regras e políticas de firewall, filtragem web, antimalware de rede, e muito mais. Os dispositivos de segurança da rede situam-se entre a internet e a sua rede virtual Azure e têm uma interface em ambas as redes.

Embora este seja o design básico de uma rede de perímetro, existem muitos projetos diferentes, como back-to-back, tri-homed, e multi-homed.

Com base no conceito Zero Trust mencionado anteriormente, recomendamos que considere a utilização de uma rede de perímetro para todas as implementações de alta segurança para aumentar o nível de segurança da rede e o controlo de acesso para os seus recursos Azure. Pode utilizar o Azure ou uma solução de terceiros para fornecer uma camada adicional de segurança entre os seus ativos e a internet:

- Controlos nativos de Azure. [O Azure Firewall](../../firewall/overview.md) e a firewall da [aplicação web em Application Gateway](../../application-gateway/features.md#web-application-firewall) oferecem segurança básica com uma firewall totalmente declarada como um serviço, alta disponibilidade incorporada, escalabilidade de nuvem sem restrições, filtragem FQDN, suporte para conjuntos de regras centrais OWASP, e configuração e configuração simples.
- Ofertas de terceiros. Procure no [Mercado Azure](https://azuremarketplace.microsoft.com/) para firewall de próxima geração (NGFW) e outras ofertas de terceiros que fornecem ferramentas de segurança familiares e níveis significativamente melhorados de segurança da rede. A configuração pode ser mais complexa, mas uma oferta de terceiros pode permitir-lhe usar as capacidades e habilidades existentes.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Evite a exposição à internet com ligações WAN dedicadas
Muitas organizações escolheram a rota híbrida de TI. Com ti híbrido, alguns dos ativos de informação da empresa estão em Azure, e outros permanecem no local. Em muitos casos, alguns componentes de um serviço estão a funcionar em Azure enquanto outros componentes permanecem no local.

Num cenário de TI híbrido, existe geralmente algum tipo de conectividade transversal. A conectividade transversal permite à empresa ligar as suas redes no local às redes virtuais Azure. Estão disponíveis duas soluções de conectividade transversal:

* [VPN local- local](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). É uma tecnologia confiável, fiável e estabelecida, mas a ligação ocorre através da internet. A largura de banda está limitada a um máximo de cerca de 1,25 Gbps. A VPN local-a-local é uma opção desejável em alguns cenários.
* **Azure ExpressRoute**. Recomendamos que utilize [o ExpressRoute](../../expressroute/expressroute-introduction.md) para a conectividade das suas instalações cruzadas. O ExpressRoute permite-lhe expandir as redes no local para a Microsoft Cloud através de uma ligação privada facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode estabelecer ligações a serviços na nuvem da Microsoft como a Azure, Microsoft 365 e Dynamics 365. ExpressRoute é uma ligação WAN dedicada entre a sua localização no local ou um fornecedor de hospedagem microsoft Exchange. Como esta é uma ligação telco, os seus dados não viajam pela internet, por isso não está exposto aos riscos potenciais das comunicações na Internet.

A localização da sua ligação ExpressRoute pode afetar a capacidade de firewall, escalabilidade, fiabilidade e visibilidade do tráfego de rede. Terá de identificar onde terminar o ExpressRoute nas redes existentes (no local). Pode:

- Termine fora da firewall (o paradigma da rede de perímetro) se necessitar de visibilidade no tráfego, se precisar de continuar uma prática existente de isolar centros de dados, ou se estiver apenas a colocar recursos extranet no Azure.
- Terminar dentro da firewall (o paradigma de extensão da rede). Esta é a recomendação por defeito. Em todos os outros casos, recomendamos tratar o Azure como um centro de dados nº.

## <a name="optimize-uptime-and-performance"></a>Otimizar o tempo e o desempenho
Se um serviço estiver em baixo, a informação não pode ser acedida. Se o desempenho for tão fraco que os dados não forem utilizáveis, pode considerar os dados inacessíveis. Do ponto de vista da segurança, você precisa fazer tudo o que puder para garantir que os seus serviços têm o melhor tempo de tempo e desempenho.

Um método popular e eficaz para melhorar a disponibilidade e o desempenho é o equilíbrio de carga. O equilíbrio de carga é um método de distribuição do tráfego de rede através de servidores que fazem parte de um serviço. Por exemplo, se tiver servidores web front-end como parte do seu serviço, pode utilizar o equilíbrio de carga para distribuir o tráfego através dos seus múltiplos servidores web frontais.

Esta distribuição de tráfego aumenta a disponibilidade porque se um dos servidores da Web ficar indisponível, o balanceador de carga para de enviar tráfego para esse servidor e redireciona-o para os servidores que ainda estão online. O equilíbrio de carga também ajuda o desempenho, porque o processador, a rede e a despesa de memória para servir pedidos é distribuído por todos os servidores equilibrados de carga.

Recomendamos que utilize o equilíbrio de carga sempre que puder e, conforme apropriado para os seus serviços. Seguem-se cenários tanto a nível da rede virtual Azure como a nível global, juntamente com opções de equilíbrio de carga para cada um.

**Cenário**: Tem uma aplicação que:

- Requer pedidos da mesma sessão utilizador/cliente para chegar à mesma máquina virtual de back-end. Exemplos disso são aplicações de carrinhos de compras e servidores de correio web.
- Aceita apenas uma ligação segura, por isso a comunicação não encriptada para o servidor não é uma opção aceitável.
- Requer vários pedidos HTTP na mesma ligação TCP de longa duração para ser encaminhado ou carregado equilibrado para diferentes servidores de back-end.

**Opção de equilíbrio de** cargas : Utilize [o Azure Application Gateway](../../application-gateway/overview.md), um equilibrador de carga de tráfego web HTTP. O Application Gateway suporta encriptação TLS de ponta a ponta e [a terminação TLS](../../application-gateway/overview.md) no gateway. Os servidores web podem então ser descarregados da encriptação e desencriptação e o tráfego fluindo desencriptado para os servidores de back-end.

**Cenário**: É necessário carregar as ligações de entrada de equilíbrio a partir da internet entre os seus servidores localizados numa rede virtual Azure. Os cenários são quando:

- Ter aplicações apátridas que aceitam pedidos de entrada da internet.
- Não requer sessões pegajosas ou descarga de TLS. Sessões pegajosas é um método usado com o Equilíbrio de Carga de Aplicação, para alcançar a afinidade do servidor.

**Opção de equilíbrio de cargas**: Utilize o portal Azure para [criar um balanceador de carga externo](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) que difunde pedidos de entrada em vários VMs para proporcionar um nível de disponibilidade mais elevado.

**Cenário**: É necessário carregar ligações de equilíbrio a partir de VMs que não estejam na internet. Na maioria dos casos, as ligações que são aceites para o equilíbrio de carga são iniciadas por dispositivos numa rede virtual Azure, tais como casos de SQL Server ou servidores web internos.   
**Opção de equilíbrio de cargas**: Utilize o portal Azure para [criar um equilibrador de carga interno](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) que difunde pedidos de entrada em vários VMs para proporcionar um nível de disponibilidade mais elevado.

**Cenário**: Precisa de um equilíbrio global da carga porque:

- Tenha uma solução de nuvem que é amplamente distribuída por várias regiões e requer o mais alto nível de uptime (disponibilidade) possível.
- Precisa do mais alto nível possível de tempo para se certificar de que o seu serviço está disponível mesmo que todo um datacenter fique indisponível.

**Opção de equilíbrio de** carga : Utilize o Gestor de Tráfego Azure. O Gestor de Tráfego permite carregar as ligações de equilíbrio aos seus serviços com base na localização do utilizador.

Por exemplo, se o utilizador fizer um pedido ao seu serviço junto da UE, a ligação é direcionada para os seus serviços localizados num datacenter da UE. Esta parte do equilíbrio global de carga do Traffic Manager ajuda a melhorar o desempenho porque a ligação ao datacenter mais próximo é mais rápida do que ligar-se aos datacenters que estão longe.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Desativar o acesso de RDP/SSH a máquinas virtuais
É possível chegar a máquinas virtuais Azure utilizando o [Protocolo de Ambiente de Trabalho Remoto](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) e o protocolo Secure [Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Estes protocolos ativam as VMs de gestão a partir de localizações remotas e são o padrão na computação de datacenters.

O potencial problema de segurança com a utilização destes protocolos através da internet é que os atacantes podem usar técnicas [de força bruta](https://en.wikipedia.org/wiki/Brute-force_attack) para ter acesso a máquinas virtuais Azure. Após os atacantes obterem acesso, podem utilizar a VM como um ponto de partida para comprometer outros computadores na rede virtual ou até mesmo atacar dispositivos em redes fora do Azure.

Recomendamos que desative o acesso direto de RDP e SSH às suas máquinas virtuais Azure a partir da internet. Depois de o acesso direto de RDP e SSH a partir da internet ser desativado, tem outras opções que pode utilizar para aceder a estes VMs para gestão remota.

**Cenário**: Permitir que um único utilizador se conecte a uma rede virtual Azure através da internet.   
**Opção**: [VPN ponto-a-local](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md) é outro termo para uma ligação cliente/servidor VPN de acesso remoto. Após a ligação ponto-a-local, o utilizador pode utilizar RDP ou SSH para ligar a quaisquer VMs localizados na rede virtual Azure a que o utilizador ligou através de VPN ponto-a-local. Isto pressupõe que o utilizador está autorizado a chegar a esses VMs.

A VPN ponto-a-local é mais segura do que as ligações RDP ou SSH diretas porque o utilizador tem de autenticar duas vezes antes de ligar a um VM. Em primeiro lugar, o utilizador precisa de autenticar (e ser autorizado) para estabelecer a ligação VPN ponto a local. Em segundo lugar, o utilizador precisa de autenticar (e ser autorizado) para estabelecer a sessão RDP ou SSH.

**Cenário**: Permitir que os utilizadores da sua rede no local se conectem a VMs na sua rede virtual Azure.   
**Opção**: Uma [VPN site-to-site](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) liga toda uma rede a outra rede através da internet. Pode utilizar uma VPN site-to-site para ligar a sua rede no local a uma rede virtual Azure. Os utilizadores na sua rede no local ligam-se utilizando o protocolo RDP ou SSH sobre a ligação VPN site-to-site. Não é preciso permitir o acesso direto do RDP ou do SSH através da internet.

**Cenário**: Utilize uma ligação WAN dedicada para fornecer funcionalidades semelhantes à VPN site-to-site.   
**Opção**: Use [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Fornece funcionalidades semelhantes à VPN site-to-site. As principais diferenças são:

- A ligação dedicada wan não atravessa a internet.
- As ligações WAN dedicadas são tipicamente mais estáveis e têm um melhor desempenho.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Proteja os seus recursos críticos de serviço Azure apenas para as suas redes virtuais
Utilize o Azure Private Link para aceder aos Serviços Azure PaaS (por exemplo, Azure Storage e SQL Database) sobre um ponto final privado na sua rede virtual. Os Private Endpoints permitem-lhe assegurar os seus recursos críticos de serviço Azure apenas às suas redes virtuais. O tráfego da sua rede virtual para o serviço Azure permanece sempre na rede de espinha dorsal do Microsoft Azure. Expor a sua rede virtual à internet pública já não é necessário consumir os Serviços Azure PaaS. 

A Azure Private Link fornece os seguintes benefícios:
- **Segurança melhorada para os seus recursos de serviço Azure**: Com a Azure Private Link, os recursos de serviço Azure podem ser protegidos à sua rede virtual utilizando o ponto final privado. Garantir recursos de serviço a um ponto final privado na rede virtual proporciona uma segurança melhorada, removendo totalmente o acesso público à Internet aos recursos, e permitindo o tráfego apenas a partir de um ponto final privado na sua rede virtual.
- **Aceda privadamente aos recursos de serviço Azure na plataforma Azure**: Ligue a sua rede virtual a serviços em Azure utilizando pontos finais privados. Não há necessidade de um endereço IP público. A plataforma Private Link tratará da conectividade entre o consumidor e os serviços através da rede de espinha dorsal Azure.
- **Acesso a partir de instalações e redes vigiadas**: Serviços de acesso em funcionamento em Azure a partir de instalações através de peering privado ExpressRoute, túneis VPN e redes virtuais espreitadas usando pontos finais privados. Não há necessidade de configurar o ExpressRoute Microsoft a espreitar ou a atravessar a internet para chegar ao serviço. O Private Link fornece uma forma segura de migrar cargas de trabalho para Azure.
- **Proteção contra fugas de dados**: Um ponto final privado é mapeado para uma instância de um recurso PaaS em vez de todo o serviço. Os consumidores só podem ligar-se ao recurso específico. O acesso a qualquer outro recurso no serviço está bloqueado. Este mecanismo fornece proteção contra riscos de fuga de dados.
- **Alcance global**: Conecte-se privadamente aos serviços que correm noutras regiões. A rede virtual do consumidor pode estar na região A e pode ligar-se aos serviços na região B.
- **Simples de configurar e gerir**: Já não precisa de endereços IP públicos reservados nas suas redes virtuais para garantir os recursos do Azure através de uma firewall IP. Não existem dispositivos NAT ou gateway necessários para configurar os pontos finais privados. Os pontos finais privados são configurados através de um simples fluxo de trabalho. No lado de serviço, também pode gerir com facilidade os pedidos de ligação no seu recurso de serviço Azure. A Azure Private Link trabalha para consumidores e serviços pertencentes a diferentes inquilinos do Azure Ative Directory também. 
    
Para saber mais sobre os pontos finais privados e os serviços e regiões Azure para os quais os pontos finais privados estão disponíveis, consulte [a Azure Private Link](../../private-link/private-link-overview.md).


## <a name="next-steps"></a>Passos seguintes
Consulte as [melhores práticas e padrões](best-practices-and-patterns.md) de segurança da Azure para obter mais boas práticas de segurança quando estiver a desenhar, implementar e gerir as suas soluções em nuvem utilizando o Azure.