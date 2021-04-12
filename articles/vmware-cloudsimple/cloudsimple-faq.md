---
title: FAQ - Azure VMware Solution by CloudSimple
description: Estas perguntas frequentes para Azure VMware Solution by CloudSimple incluem conectividade, networking, segurança, armazenamento, integração Azure, entre outros.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3551327f9aa07fe218d3b05f73057e36f92abcad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895568"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Perguntas frequentes sobre VMware Solution by CloudSimple

## <a name="cloudsimple-service"></a>Serviço CloudSimple

**O que é Azure VMware Solution by CloudSimple?**

A Azure VMware Solution by CloudSimple transforma e estende cargas de trabalho VMware a nuvens privadas e dedicadas em Azure em minutos. A CloudSimple cuida do fornecimento, gestão da infraestrutura e orquestração de cargas de trabalho entre as instalações e o Azure. Como as suas aplicações funcionam exatamente da mesma forma no local e em Azure, beneficia da elasticidade e serviços da nuvem sem a complexidade de rearquitecar as suas apps. A CloudSimple reduz o seu custo total de propriedade com um modelo de consumo em nuvem que fornece provisão a pedido, pay-as-you-grow e otimização de capacidade.  Veja [o que é A solução VMware em Azure by CloudSimple](cloudsimple-vmware-solutions-overview.md) para funcionalidades, benefícios e cenários.

**O que é uma Nuvem Privada CloudSimple?**

A CloudSimple Private Cloud é uma nuvem privada e dedicada que consiste num ambiente de computação, armazenamento e networking de alto desempenho implantado na infraestrutura do Microsoft Azure (espaço de hardware e datacenter) em locais do Azure.  A Private Cloud fornece uma 'plataforma' VMware nativa como um serviço. Em termos VMware, cada Nuvem Privada contém exatamente uma instância do servidor vCenter. O vCenter Server gere vários nós ESXi contidos num ou mais aglomerados vSphere, juntamente com o armazenamento virtual correspondente san (vSAN). Um serviço CloudSimple pode conter várias Nuvens Privadas na sua subscrição Azure.  Para mais detalhes, consulte [a visão geral da Private Cloud.](cloudsimple-private-cloud.md)

**Onde está disponível o serviço CloudSimple?**

CloudSimple está disponível nas regiões leste dos EUA, Eua Ocidental e Europa Ocidental, com regiões adicionais em breve.

**Como posso habilitar a minha subscrição para CloudSimple?**

Pode contactar o seu representante da conta microsoft [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) para ativar a sua subscrição para o serviço CloudSimple. Forneça o seu ID de subscrição no e-mail para o qual pretende que o serviço CloudSimple esteja ativado.  

**Como acesso ao portal CloudSimple?**

Aceda ao portal CloudSimple a partir do portal Azure.  Para mais informações, consulte [o Portal VMware Solution by CloudSimple a partir do portal Azure](access-cloudsimple-portal.md).

**Como posso aumentar a capacidade de uma Nuvem Privada?**

Para aumentar a capacidade, compre nós adicionais a partir do portal Azure e, em seguida, use os nós para expandir a sua Nuvem Privada a partir do portal CloudSimple.  Pode adicionar nós adicionais a um cluster vSphere existente ou adicioná-los a um novo cluster vSphere.  Para mais detalhes, consulte [expandir uma nuvem privada CloudSimple.](expand-private-cloud.md)

**O que acontece com a minha Nuvem Privada durante a manutenção?**

CloudSimple fornece notificação vários dias antes de um intervalo de manutenção programado.  A manutenção é feita de forma não disruptiva para garantir a disponibilidade da sua Nuvem Privada.  A manutenção pode ser dos seguintes tipos:

* **Infraestrutura CloudSimple**.  A infraestrutura CloudSimple foi projetada para estar altamente disponível.  Durante este tipo de intervalo de manutenção, os componentes redundantes são atualizados um de cada vez para evitar qualquer interrupção de serviço. Mantém acesso ao seu VCenter Private Cloud, todas as máquinas virtuais, a ligação à Internet a partir da sua Nuvem Privada e ligações a instalações ou Azure.
* **Portal CloudSimple**. Durante este tipo de intervalo de manutenção, algumas funcionalidades no portal CloudSimple podem ser desativadas ou inacessíveis.  A notificação anterior ao intervalo de manutenção inclui detalhes sobre as limitações da funcionalidade durante a manutenção.

## <a name="connectivity"></a>Conectividade

**Quais são as minhas opções de conectividade para a rede da região CloudSimple?**

O CloudSimple fornece as seguintes opções de conectividade para ligar à sua rede de região CloudSimple. Várias opções podem ser usadas ao mesmo tempo.

* **Ligação ExpressRoute do seu datacenter no local para a rede da região CloudSimple**. Trata-se de uma ligação privada segura e de alta velocidade, que utiliza o Global Reach para fazer a ponte entre o circuito ExpressRoute no local até ao circuito CloudSimple ExpressRoute. Para obter instruções sobre a configuração da ligação, consulte [Ligar das instalações à CloudSimple utilizando o ExpressRoute](on-premises-connection.md).
* **Ligação ExpressRoute da sua rede virtual Azure à sua rede de região CloudSimple**. Trata-se de uma ligação privada segura e de alta velocidade, de alta velocidade, que utiliza gateways de rede virtuais para fazer a ponte entre a sua rede virtual no Azure e o circuito CloudSimple ExpressRoute. Para obter instruções sobre a configuração da ligação, consulte [ligue o ambiente cloudSimple Private Cloud à rede virtual Azure utilizando o ExpressRoute](azure-expressroute-connection.md).
* **Ligação VPN site-to-site do seu centro de dados no local para a sua rede de região CloudSimple**. Esta é uma rede privada virtual segura desde o seu dispositivo VPN no local até à região cloudSimple Private Cloud.  Para mais detalhes, consulte [configurar as portas VPN na rede CloudSimple](vpn-gateway.md).

**Como me posso relacionar com uma Nuvem Privada?**

Pode ver detalhes da sua Nuvem Privada no portal CloudSimple. Para ligar ao vCenter que corresponde à sua Nuvem Privada, verifique primeiro se uma ligação de rede é estabelecida usando VPN site-to-site, Point-to-Site VPN ou ExpressRoute. Em seguida, lance o portal CloudSimple a partir do portal Azure e clique em **Lançar vSphere Client** na página Inicial ou na página de detalhes da Cloud Privada.

**Qual é a vantagem dos circuitos ExpressRoute?**

Um circuito Azure ExpressRoute é uma ligação segura de alta velocidade, baixa latência e segurança.  A CloudSimple fornece um circuito ExpressRoute dedicado por região por cliente.  Utilizando este circuito, pode estabelecer uma ligação segura a partir do local ou da sua subscrição Azure.

**Quais são os custos da rede para ligar ao CloudSimple?  Alguma carga de saída aplica-se entre CloudSimple e Azure, ou em todas as regiões?**

Não existe nenhuma taxa CloudSimple para a saída da rede.  As tarifas padrão Azure aplicam-se a qualquer tráfego de saída da sua rede virtual ou do circuito ExpressRoute no local.

## <a name="networking"></a>Rede

**Que funcionalidades de networking estão disponíveis para a minha Nuvem Privada?**

Você pode providenciar VLANs (e suas sub-redes) e tabelas de firewall, e atribuir endereços IP públicos que mapeiam para uma máquina virtual em execução na sua Nuvem Privada. Para obter detalhes sobre funcionalidades de networking, consulte [VLANs e sub-redes, visão](cloudsimple-vlans-subnets.md) [geral das tabelas de firewall](cloudsimple-firewall-tables.md)e [visão geral do endereço IP público](cloudsimple-public-ip-address.md).

**Como posso configurar diferentes sub-redes para as minhas aplicações na minha Nuvem Privada?**

Cria VLANs na sua Nuvem Privada a partir do portal CloudSimple.  Depois de criar um VLAN, pode criar um grupo de portas distribuído no seu VCenter Private Cloud utilizando o VLAN e criar máquinas virtuais que estejam ligadas ao grupo portuário distribuído.  Pode ativar as tabelas de firewall para a sub-rede VLAN/sub-rede e definir regras de firewall para garantir o tráfego da rede.

**Que definições de firewall estão disponíveis para as minhas Nuvens Privadas?**

Pode configurar regras para o tráfego norte-sul e leste-oeste.  As regras são definidas numa mesa de firewall.  A tabela de firewall pode ser anexada a VLANs na sua Nuvem Privada.  Para mais detalhes, consulte [configurar tabelas de firewall e regras para Nuvens Privadas](firewall.md).

**Posso atribuir endereços IP públicos a VMs no meu ambiente Private Cloud?**

No portal CloudSimple, pode alocar um novo endereço IP público e associá-lo ao endereço IP privado de uma máquina virtual ou de um aparelho.  Também pode criar novas regras de firewall ou aplicar as regras de firewall existentes para permitir o tráfego de portas específicas e endereços IP no portal. Para mais informações, consulte [alocar endereços IP públicos para ambiente Private Cloud](public-ips.md).

## <a name="security"></a>Segurança

**Quais são as minhas opções de segurança no CloudSimple?**

O CloudSimple fornece as seguintes funcionalidades de segurança para garantir o seu ambiente Private Cloud:

* **Dados em repouso encriptação**. Pode encriptar dados em repouso que residem no armazenamento vSAN na sua Nuvem Privada. vSAN suporta servidores de gestão de chaves externos, que podem ser implementados no seu ambiente Azure vNet ou no local.  Para mais detalhes, consulte [a encriptação Configure vSAN para a sua CloudSimple Private Cloud](vsan-encryption.md).
* **Segurança da rede**. Controle o fluxo de tráfego da rede com regras de firewall que se aplicam entre a sua Nuvem Privada e a internet, o seu ambiente Private Cloud e no local, ou dentro de sub-redes da sua Nuvem Privada.
* **Ligação segura e privada.** Estabelece-se uma ligação segura e privada entre a sua rede no local e a sua subscrição Azure.

## <a name="compute"></a>Computação

**Que tipo de anfitriões estão disponíveis?**

CloudSimple oferece estes tipos de anfitriões:

* **Nó CS28:** CPU:2x 2.2 GHz, total de 28 núcleos, 48 HT.  RAM: 256 GB.  Armazenamento: 1600 GB cache NVMe, dados de 5760 GB (All-Flash). Rede: 4x25Gbe NIC
* **Nó CS36:** CPU 2x 2.3 GHz, total de 36 núcleos, 72 HT.  RAM: 512 GB.  Armazenamento: 3200 GB NVMe cache 11520 GB dados (All-Flash).  Rede: 4x25Gbe NIC
* **Nó CS36m:** CPU 2x 2.3 GHz, total de 36 núcleos, 72 HT.  RAM: 576 GB.  Armazenamento: 3200 GB NVMe cache 13360 GB dados (All-Flash).  Rede: 4x25Gbe NIC

**Como são tratadas as falhas de hardware?**

Todas as infraestruturas CloudSimple são monitorizadas continuamente pela plataforma CloudSimple e pelas nossas equipas de operações de serviço.  Se for detetada uma falha de hardware, é adicionado um novo nó à sua Nuvem Privada e o nó falhado é removido.

## <a name="storage"></a>Armazenamento

**Que tipo de armazenamento é suportado numa Nuvem Privada?**

O CloudSimple oferece armazenamento VMware vSAN com todas as Cloud Privadas.  Cada vSphere é criado com a sua própria loja de dados vSAN.  Para mais detalhes, consulte [os componentes Private Cloud VMware - armazenamento vSAN](vmware-components.md#vsan-storage).

**A encriptação dos dados é suportada?**
Sim.  Pode configurar o armazenamento vSAN na sua Nuvem Privada para utilizar um servidor de gestão de chaves (KMS) que seja implantado no local ou no Azure para encriptar dados armazenados em vSAN.

**Como são tratados os discos falhados?**

O CloudSimple monitoriza continuamente todos os componentes de hardware da Nuvem Privada.  Se for detetada uma falha do disco ou se um disco for identificado como falha (baseado na heurística), um novo nó é automaticamente adicionado à Nuvem Privada.  O nó com o disco falhado ou falhado é removido da Nuvem Privada.

## <a name="vmware"></a>VMware

**Como faço o upload em larga escala ou a migração de aplicações e dados a partir do local?**

CloudSimple fornece uma solução vFware vSphere nativa.  Todas as ferramentas VMware para migração de dados em massa podem ser usadas com a sua Nuvem Privada.  As opções incluem:

* VMware HCX para migração a granel de dados.
* Migração fria de dados usando Storage vMotion de instalações para CloudSimple.

**Posso instalar alguma ferramenta VMware?**

CloudSimple fornece uma solução vFware vSphere nativa.  Todas as ferramentas VMware utilizadas para gerir o seu ambiente vSphere no local podem ser utilizadas no CloudSimple.  O CloudSimple suporta um modelo de "bring-your-your-own-license" (BYOL) para instalar ferramentas VMware.

**Como são geridas as atualizações e atualizações?**

O CloudSimple gere e atualiza todos os componentes de infraestrutura da sua Nuvem Privada de uma forma não-disruptiva.  Todas as atualizações e patches de segurança lançados pela VMware ou fornecedores de infraestruturas estão agendados para a atualização assim que forem qualificados pela CloudSimple.

O CloudSimple não realiza atualizações ou atualizações de aplicações instaladas na Cloud Privada.

## <a name="azure-integration"></a>Azure Integration (Integração do Azure)

**Que serviços da Azure são apoiados?**

O CloudSimple fornece uma ligação Azure ExpressRoute à sua subscrição no Azure.  Todos os serviços em execução na sua subscrição podem ligar-se à sua Cloud Privada.  Os exemplos incluem:

* **Azure Ative Directory** como fonte de identidade para o seu CloudSimple vCenter.
* **Armazenamento Azure** para armazenar backups, imagens e outros dados da sua Nuvem Privada.
* **Aplicações híbridas** com uma arquitetura de aplicações que abrange nuvens públicas e privadas.  Por exemplo, pode criar webservers no Azure que acedam a servidores de aplicações e bases de dados na sua Cloud Privada.
* **Monitor Azure** e **centro de segurança Azure** para cargas de trabalho em execução no registo de suporte VMware, métricas de desempenho e gestão de segurança.

**Como mapear os meus inquilinos VMware para Azure?**

O CloudSimple oferece a capacidade única de gerir os seus VMware VMs em Private Cloud a partir do portal Azure.  Um conjunto de recursos vCenter configurado com as restrições de recursos desejadas pode ser mapeado para a sua subscrição pelo seu administrador global.  

**Que benefícios de licenciamento recebo com o Azure?**

Com o CloudSimple, pode aproveitar o Benefício de Utilização Híbrida Azure e economizar até 90% em licenças. Este benefício preserva o seu investimento em licenças da Microsoft e reduz o seu TCO em relação a outras soluções em nuvem. Também obtém atualizações de segurança alargadas para o Windows Server 2008 e Microsoft SQL Server 2008.  O modelo bring-your-your-own-license (BYOL) ajuda-o a manter os custos baixos para aplicações comuns como Veeam e Zerto.  
