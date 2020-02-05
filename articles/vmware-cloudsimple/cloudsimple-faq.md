---
title: FAQ - Soluções Azure VMware (AVS)
description: Perguntas frequentes para soluções Azure VMware (AVS)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c3808491c84f6c76a51c914aac6ee5e5ee370970
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025066"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-avs"></a>Perguntas frequentes sobre a Solução VMware pela AVS

## <a name="avs-service"></a>Serviço AVS

**O que é azure VMware Solutions (AVS)?**

A Azure VMware Solutions (AVS) transforma e alarga as cargas de trabalho vMware a nuvens privadas e dedicadas em Azure em minutos. A AVS cuida do provisionamento, gestão da infraestrutura e orquestração de cargas de trabalho entre as instalações e o Azure. Como as suas aplicações funcionam exatamente no mesmo local e em Azure, beneficia da elasticidade e serviços da nuvem sem a complexidade de rearquitetar as suas apps. A AVS reduz o seu custo total de propriedade com um modelo de consumo em nuvem que fornece provisionamento a pedido, pagamento-as-você-crescer e otimização de capacidade. Veja [o que é VMware Solution on Azure by AVS](cloudsimple-vmware-solutions-overview.md) para funcionalidades, benefícios e cenários.

**O que é uma Nuvem Privada AVS?**

Um AVS Private Cloud é uma nuvem privada e dedicada que consiste num ambiente de computação, armazenamento e networking de alto desempenho implantado na infraestrutura do Microsoft Azure (hardware e espaço datacenter) em localizações do Azure. Uma Nuvem Privada AVS fornece uma "plataforma vmware nativa como um serviço". Em termos VMware, cada Nuvem Privada AVS contém exatamente uma instância do servidor vCenter. O vCenter Server gere vários nós ESXi contidos num ou mais aglomerados de vSphere, juntamente com o armazenamento correspondente de SAN Virtual (vSAN). Um serviço AVS pode conter múltiplas Nuvens Privadas AVS na sua subscrição Azure. Para mais detalhes, consulte a [Visão Geral da Nuvem Privada da AVS](cloudsimple-private-cloud.md).

**Onde está disponível o serviço AVS?**

A AVS está disponível nas regiões leste dos EUA, Oeste e Europa Ocidental com regiões adicionais em breve.

**Como posso permitir a minha subscrição para AVS?**

Pode contactar o representante da sua conta Microsoft em [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) para ativar a sua subscrição para o serviço AVS. Forneça o seu ID de subscrição no e-mail para o qual pretende ativar o serviço AVS. 

**Como posso aceder ao portal AVS?**

Acede ao portal AVS do portal Azure. Para mais detalhes, consulte [O portal Access the VMware Solutions (AVS) a partir do portal Azure](access-cloudsimple-portal.md).

**Como aumentar a capacidade de uma Nuvem Privada AVS?**

Para aumentar a capacidade, compre nós adicionais no portal Azure e, em seguida, use os nós para expandir a sua Nuvem Privada AVS a partir do portal AVS. Pode adicionar nós adicionais a um cluster vSphere existente ou adicioná-los a um novo cluster vSphere. Para mais detalhes, consulte [Expandir uma Nuvem Privada AVS](expand-private-cloud.md).

**O que acontece com a minha Nuvem Privada AVS durante a manutenção?**

A AVS fornece notificação vários dias antes de um intervalo de manutenção programado. A manutenção é feita de forma não disruptiva para garantir a disponibilidade da sua Nuvem Privada AVS. A manutenção pode ser dos seguintes tipos:

* **Infraestrutura AVS.** A infraestrutura AVS foi concebida para estar altamente disponível. Durante este tipo de intervalo de manutenção, os componentes redundantes são atualizados um de cada vez para evitar qualquer interrupção do serviço. Você mantém acesso ao seu VCenter De Nuvem Privada AVS, todas as máquinas virtuais, a ligação à internet a partir da sua Nuvem Privada AVS, e ligações a instalações ou Azure.
* **Portal AVS.** Durante este tipo de intervalo de manutenção, algumas funcionalidades no portal AVS podem ser desativadas ou inacessíveis. A notificação anterior ao intervalo de manutenção inclui detalhes sobre as limitações da funcionalidade enquanto a manutenção está a decorrer.

## <a name="connectivity"></a>Ligação

**Quais são as minhas opções de conectividade para a rede da região AVS?**

A AVS fornece as seguintes opções de conectividade para ligar à sua rede da região AVS. Várias opções podem ser usadas ao mesmo tempo.

* **Ligação ExpressRoute do seu centro de dados no local para a rede da região AVS**. Esta é uma ligação privada segura de alta velocidade, de baixa latência, que utiliza o Global Reach para ligar o circuito ExpressRoute no local ao circuito AVS ExpressRoute. Para obter instruções sobre a instalação da ligação, consulte [O Connect de instalações para AVS utilizando expressroute](on-premises-connection.md).
* **Ligação ExpressRoute da sua rede virtual Azure à sua rede da região AVS**. Esta é uma ligação privada segura de alta velocidade, de baixa latência, que utiliza gateways de rede virtuais para fazer a ponte entre a sua rede virtual em Azure e o circuito AVS ExpressRoute. Para obter instruções sobre a configuração da ligação, consulte [Ligue o seu ambiente De nuvem privada AVS à rede virtual Azure utilizando expressRoute](azure-expressroute-connection.md).
* **Ligação VPN site-to-site do seu centro de dados no local para a sua rede da região AVS**. Esta é uma rede privada virtual segura desde o seu dispositivo VPN no local até à região da Nuvem Privada AVS. Para mais detalhes, consulte [Configurar gateways VPN na rede AVS](vpn-gateway.md).

**Como me ligo a uma Nuvem Privada AVS?**

Pode ver detalhes da sua Nuvem Privada AVS no portal AVS. Para ligar ao vCenter que corresponde à sua Nuvem Privada AVS, verifique primeiro que uma ligação de rede está estabelecida usando VPN site-to-site VPN, Point-to-Site VPN ou ExpressRoute. Em seguida, lance o portal AVS a partir do portal Azure e clique no **Launch vSphere Client** na página Inicial ou na página de detalhes da Nuvem Privada AVS.

**Qual é a vantagem dos circuitos ExpressRoute?**

Um circuito Azure ExpressRoute é uma ligação segura de alta velocidade, baixa latência. A AVS fornece um circuito ExpressRoute dedicado por região por cliente. Utilizando este circuito, pode estabelecer uma ligação segura a partir das instalações ou da sua subscrição Azure.

**Quais são os custos da rede para ligar ao AVS? Aplicam-se quaisquer encargos de saída entre a AVS e o Azure, ou entre regiões?**

Não existe nenhuma taxa AVS para a saída da rede. As tarifas padrão do Azure aplicam-se a qualquer tráfego de saída da sua rede virtual ou do circuito ExpressRoute no local.

## <a name="networking"></a>Funcionamento em Rede

**Que funcionalidades de networking estão disponíveis para a minha Nuvem Privada AVS?**

Você pode fornecer VLANs (e suas subredes) e tabelas de firewall, e atribuir endereços IP públicos que mapeiam para uma máquina virtual que funciona na sua Nuvem Privada AVS. Para mais detalhes sobre as funcionalidades de networking, consulte [vlaNs e subnets,](cloudsimple-vlans-subnets.md)visão geral das [tabelas de firewall](cloudsimple-firewall-tables.md)e visão geral do [endereço IP público.](cloudsimple-public-ip-address.md)

**Como configurar diferentes subredes para as minhas aplicações na minha Nuvem Privada AVS?**

Cria VLANs na sua Nuvem Privada AVS a partir do portal AVS. Depois de criar um VLAN, pode criar um grupo portuário distribuído no seu VCenter De Nuvem Privada AVS utilizando o VLAN e criar máquinas virtuais que estejam ligadas ao grupo portuário distribuído. Pode ativar as tabelas de firewall para a VLAN/subnet e definir regras de firewall para proteger o tráfego de rede.

**Que configurações de firewall estão disponíveis para as minhas Nuvens Privadas AVS?**

Pode configurar regras para o tráfego norte-sul e leste-oeste. As regras são definidas numa mesa de firewall. A tabela de firewall pode ser anexada a VLANs na sua Nuvem Privada AVS. Para mais detalhes, consulte [Configurar tabelas e regras de firewall para AVS Private Clouds](firewall.md).

**Posso atribuir endereços IP públicos a VMs no meu ambiente De Nuvem Privada AVS?**

No portal AVS, pode alocar um novo endereço IP público e associá-lo ao endereço IP privado de uma máquina virtual ou de um aparelho. Também pode criar novas regras de firewall ou aplicar as regras de firewall existentes para permitir o tráfego de portas específicas e endereços IP no portal. Para mais detalhes, consulte [Alocar endereços IP públicos para ambiente De Nuvem Privada AVS](public-ips.md).

## <a name="security"></a>Segurança

**Quais são as minhas opções de segurança no AVS?**

O AVS fornece as seguintes funcionalidades de segurança para garantir o seu ambiente AVS Private Cloud:

* **Dados em repouso encriptação**. Pode encriptar dados em repouso residente sanário no armazenamento vSAN na sua Nuvem Privada AVS. VSAN suporta servidores de gestão de chaves externas, que podem ser implantados no seu ambiente Azure vNet ou no local. Para mais detalhes, consulte [a encriptação Configure vSAN para a sua Nuvem Privada AVS](vsan-encryption.md).
* **Segurança da rede.** Controle o fluxo de tráfego da rede com regras de firewall que se aplicam entre a sua Nuvem Privada AVS e a internet, a sua Nuvem Privada AVS e o ambiente no local, ou dentro das subredes da sua Nuvem Privada AVS.
* **Ligação segura e privada.** Uma ligação segura e privada é estabelecida entre a sua rede no local e a sua subscrição Azure.

## <a name="compute"></a>Computação

**Que tipo de anfitriões estão disponíveis?**

A AVS oferece estes tipos de anfitriões:

* **Nó CS28:** CPU:2x 2,2 GHz, total de 28 núcleos, 48 HT.  RAM: 256 GB.  Armazenamento: 1600 GB NVMe cache, 5760 GB de dados (All-Flash). Rede: 4x25Gbe NIC
* **Nó CS36:** CPU 2x 2,3 GHz, total 36 núcleos, 72 HT.  RAM: 512 GB.  Armazenamento: 3200 GB NVMe cache 11520 GB dados (All-Flash).  Rede: 4x25Gbe NIC
* **Nó CS36m:** CPU 2x 2,3 GHz, total 36 núcleos, 72 HT.  RAM: 576 GB.  Armazenamento: 3200 GB NVMe cache 13360 GB dados (All-Flash).  Rede: 4x25Gbe NIC

**Como é que as falhas de hardware são tratadas?**

Toda a infraestrutura AVS é monitorizada continuamente pela plataforma AVS e pelas nossas equipas de operações de serviço. Se for detetada uma falha de hardware, um novo nó é adicionado ao seu AVS Private Cloud e o nó falhado é removido.

## <a name="storage"></a>Armazenamento

**Que tipo de armazenamento é suportado numa Nuvem Privada AVS?**

A AVS oferece armazenamento vSAN vSAN all-flash com cada Nuvem Privada AVS. Cada vSphere é criada com a sua própria loja de dados vSAN. Para mais detalhes, consulte [os componentes AVS Private Cloud VMware - vSAN storage](vmware-components.md#vsan-storage).

**A encriptação de dados é suportada?**
Sim. Pode configurar o armazenamento vSAN na sua Nuvem Privada AVS para utilizar um servidor de gestão de chaves (KMS) que é implantado no local ou no Azure para encriptar os dados armazenados no vSAN.

**Como são manuseados discos falhados?**

A AVS monitoriza continuamente todos os componentes de hardware da Nuvem Privada AVS. Se for detetada uma falha no disco ou se identificar um disco como falha (com base na heurística), um novo nó é automaticamente adicionado à Nuvem Privada AVS. O nó com o disco falhado ou em falha é removido da Nuvem Privada AVS.

## <a name="vmware"></a>VMware

**Como faço upload em larga escala ou migração de aplicações e dados a partir do local?**

A AVS fornece uma solução vSphere VSphere vSphere nativa. Todas as ferramentas VMware para migração de dados a granel podem ser usadas com a sua Nuvem Privada AVS. As opções incluem:

* VMware HCX para migração em massa de dados.
* Migração fria de dados utilizando o Armazenamento vMotion de instalações para AVS.

**Posso instalar alguma ferramenta VMware?**

A AVS fornece uma solução vSphere VSphere vSphere nativa. Todas as ferramentas VMware utilizadas para gerir o ambiente vSphere no local podem ser utilizadas em AVS. A AVS suporta um modelo de bring-your-own-license (BYOL) para a instalação de ferramentas VMware.

**Como são geridas atualizações e atualizações?**

A AVS gere e atualiza todos os componentes de infraestrutura da sua Nuvem Privada AVS de uma forma perfeita e não disruptiva. Todas as atualizações e patches de segurança lançados pela VMware ou pelos fornecedores de infraestruturas estão agendados para a atualização assim que forem qualificados pela AVS.

O AVS não realiza atualizações ou atualizações de aplicações instaladas na Nuvem Privada AVS.

## <a name="azure-integration"></a>Integração Azure

**Que serviços azure são apoiados?**

A AVS fornece uma ligação Azure ExpressRoute à sua subscrição no Azure. Todos os serviços em execução na sua subscrição podem ligar-se à sua Nuvem Privada AVS. Alguns exemplos:

* **Azure Ative Diretório** como fonte de identidade para o seu VCenter AVS.
* **Armazenamento azure** para armazenar backups, imagens e outros dados da sua Nuvem Privada AVS.
* **Aplicações híbridas** com uma arquitetura de aplicação que abrange nuvens públicas e avs privadas. Por exemplo, pode criar webservers no Azure que acedam a aplicações e servidores de base de dados na sua Nuvem Privada AVS.
* **O monitor Azure** e o **centro de segurança Azure** para cargas de trabalho em funcionamento em vMware suportam a exploração madeireira, métricas de desempenho e gestão de segurança.

**Como posso mapear os meus inquilinos VMware para O Azure?**

A AVS fornece a capacidade única de gerir os seus VMware VMs na Nuvem Privada AVS a partir do portal Azure. Um conjunto de recursos vCenter configurado com restrições de recursos desejadas pode ser mapeado para a sua subscrição pelo seu administrador global. 

**Que benefícios de licenciamento recebo com o Azure?**

Com a AVS, pode aproveitar o Azure Hybrid Usage Benefit e economizar até 90% em licenças. Este benefício preserva o seu investimento em licenças da Microsoft e reduz o seu TCO em relação a outras soluções na nuvem. Também obtém atualizações de segurança estendidas para windows Server 2008 e Microsoft SQL Server 2008. O modelo bring-your-your-license (BYOL) ajuda-o a manter os custos baixos para aplicações comuns como Veeam e Zerto. 
