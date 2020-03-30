---
title: FAQ - Solução Azure VMware by CloudSimple
description: Perguntas frequentes para azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 95d8c2974ea372dd59d15a9f2cc31ed171acd932
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025066"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Perguntas frequentes sobre a Solução VMware pela CloudSimple

## <a name="cloudsimple-service"></a>Serviço CloudSimple

**O que é a Solução Azure VMware da CloudSimple?**

A Solução Azure VMware da CloudSimple transforma e alarga as cargas de trabalho vMware a nuvens privadas e dedicadas no Azure em minutos. A CloudSimple cuida do provisionamento, gestão da infraestrutura e orquestrando cargas de trabalho entre as instalações e o Azure. Como as suas aplicações funcionam exatamente no mesmo local e em Azure, beneficia da elasticidade e serviços da nuvem sem a complexidade de rearquitetar as suas apps. A CloudSimple reduz o seu custo total de propriedade com um modelo de consumo em nuvem que fornece fornecimento a pedido, pagamento-as-você-crescer e otimização de capacidade.  Veja [o que é vMware Solution on Azure by CloudSimple](cloudsimple-vmware-solutions-overview.md) para funcionalidades, benefícios e cenários.

**O que é uma Nuvem Privada CloudSimple?**

A CloudSimple Private Cloud é uma nuvem privada e dedicada que consiste num ambiente de computação, armazenamento e networking de alto desempenho implantado na infraestrutura do Microsoft Azure (hardware e espaço datacenter) em localizações do Azure.  Uma Nuvem Privada fornece uma "plataforma vmware nativa como um serviço". Em termos VMware, cada Nuvem Privada contém exatamente uma instância do servidor vCenter. O vCenter Server gere vários nós ESXi contidos num ou mais aglomerados de vSphere, juntamente com o armazenamento correspondente de SAN Virtual (vSAN). Um serviço CloudSimple pode conter múltiplas Nuvens Privadas na sua subscrição Azure.  Para mais detalhes, consulte a [visão geral da Private Cloud.](cloudsimple-private-cloud.md)

**Onde está disponível o serviço CloudSimple?**

CloudSimple está disponível nas regiões leste dos EUA, Oeste dos EUA e Europa Ocidental com regiões adicionais em breve.

**Como posso permitir a minha subscrição para o CloudSimple?**

Pode contactar o representante [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) da sua conta Microsoft para ativar a sua subscrição para o serviço CloudSimple. Forneça o seu ID de subscrição no e-mail para o qual pretende que o serviço CloudSimple esteja ativado.  

**Como acedo ao portal CloudSimple?**

Acede ao portal CloudSimple a partir do portal Azure.  Para mais detalhes, consulte [Access the VMware Solution by CloudSimple portal from the Azure portal](access-cloudsimple-portal.md).

**Como aumentar a capacidade de uma Nuvem Privada?**

Para aumentar a capacidade, compre nós adicionais no portal Azure e, em seguida, use os nós para expandir a sua Nuvem Privada a partir do portal CloudSimple.  Pode adicionar nós adicionais a um cluster vSphere existente ou adicioná-los a um novo cluster vSphere.  Para mais detalhes, consulte [Expandir uma CloudSimple Private Cloud](expand-private-cloud.md).

**O que acontece à minha Nuvem Privada durante a manutenção?**

A CloudSimple fornece notificação vários dias antes de um intervalo de manutenção programado.  A manutenção é feita de forma não disruptiva para garantir a disponibilidade da sua Cloud Privada.  A manutenção pode ser dos seguintes tipos:

* **Infraestrutura CloudSimple.**  A infraestrutura CloudSimple foi concebida para estar altamente disponível.  Durante este tipo de intervalo de manutenção, os componentes redundantes são atualizados um de cada vez para evitar qualquer interrupção do serviço. Você mantém acesso ao seu vCenter De Nuvem Privada, todas as máquinas virtuais, a ligação à internet a partir da sua Nuvem Privada, e ligações a instalações ou Azure.
* **Portal CloudSimple**. Durante este tipo de intervalo de manutenção, algumas funcionalidades no portal CloudSimple podem ser desativadas ou inacessíveis.  A notificação anterior ao intervalo de manutenção inclui detalhes sobre as limitações da funcionalidade enquanto a manutenção está a decorrer.

## <a name="connectivity"></a>Conectividade

**Quais são as minhas opções de conectividade para a rede da região CloudSimple?**

A CloudSimple fornece as seguintes opções de conectividade para ligar à sua rede da região CloudSimple. Várias opções podem ser usadas ao mesmo tempo.

* **Ligação ExpressRoute do seu datacenter no local para a rede da região CloudSimple**. Esta é uma ligação privada segura de alta velocidade, de baixa latência, que utiliza o Global Reach para fazer a ponte entre o circuito ExpressRoute no local e o circuito CloudSimple ExpressRoute. Para obter instruções sobre a configuração da ligação, consulte [Connect from on-premises to CloudSimple utilizando expressRoute](on-premises-connection.md).
* **Ligação ExpressRoute da sua rede virtual Azure à sua rede da região CloudSimple**. Esta é uma ligação privada segura de alta velocidade, de baixa latência, que utiliza gateways de rede virtuais para fazer a ponte entre a sua rede virtual em Azure e o circuito CloudSimple ExpressRoute. Para obter instruções sobre a configuração da ligação, consulte [Connect your Cloud Private Cloud ambiente para a rede virtual Azure utilizando expressRoute](azure-expressroute-connection.md).
* **Ligação VPN site-to-site do seu datacenter no local para a sua rede da região CloudSimple**. Esta é uma rede privada virtual segura desde o seu dispositivo VPN no local até à sua região cloudSimple Private Cloud.  Para mais detalhes, consulte [Configurar gateways VPN na rede CloudSimple](vpn-gateway.md).

**Como me ligo a uma nuvem privada?**

Pode ver detalhes da sua Nuvem Privada no portal CloudSimple. Para ligar ao vCenter que corresponde à sua Cloud Privada, verifique primeiro que uma ligação de rede está estabelecida usando VPN site-to-site, VPN ponto-a-site ou ExpressRoute. Em seguida, lance o portal CloudSimple a partir do portal Azure e clique no **Launch vSphere Client** na página Inicial ou na página de detalhes da Cloud Privada.

**Qual é a vantagem dos circuitos ExpressRoute?**

Um circuito Azure ExpressRoute é uma ligação segura de alta velocidade, baixa latência.  A CloudSimple fornece um circuito ExpressRoute dedicado por região por cliente.  Utilizando este circuito, pode estabelecer uma ligação segura a partir das instalações ou da sua subscrição Azure.

**Quais são os custos da rede para ligar à CloudSimple?  Aplicam-se quaisquer encargos de saída entre a CloudSimple e o Azure, ou entre regiões?**

Não existe nenhuma carga CloudSimple para a saída da rede.  As tarifas padrão do Azure aplicam-se a qualquer tráfego de saída da sua rede virtual ou do circuito ExpressRoute no local.

## <a name="networking"></a>Redes

**Que funcionalidades de networking estão disponíveis para a minha Cloud Privada?**

Você pode fornecer VLANs (e suas subredes) e tabelas de firewall, e atribuir endereços IP públicos que mapeiam para uma máquina virtual que funciona na sua Nuvem Privada. Para mais detalhes sobre as funcionalidades de networking, consulte [vlaNs e subnets,](cloudsimple-vlans-subnets.md)visão geral das [tabelas de firewall](cloudsimple-firewall-tables.md)e visão geral do [endereço IP público.](cloudsimple-public-ip-address.md)

**Como configurarei subredes diferentes para as minhas candidaturas na minha Nuvem Privada?**

Cria VLANs na sua Nuvem Privada a partir do portal CloudSimple.  Depois de criar um VLAN, pode criar um grupo portuário distribuído no seu vCenter De Nuvem Privada utilizando o VLAN e criar máquinas virtuais que estejam ligadas ao grupo portuário distribuído.  Pode ativar as tabelas de firewall para a VLAN/subnet e definir regras de firewall para proteger o tráfego de rede.

**Que configurações de firewall estão disponíveis para as minhas Nuvens Privadas?**

Pode configurar regras para o tráfego norte-sul e leste-oeste.  As regras são definidas numa mesa de firewall.  A tabela de firewall pode ser anexada a VLANs na sua Nuvem Privada.  Para mais detalhes, consulte [Configurar tabelas e regras de firewall para nuvens privadas](firewall.md).

**Posso atribuir endereços IP públicos a VMs no meu ambiente private Cloud?**

No portal CloudSimple, pode alocar um novo endereço IP público e associá-lo ao endereço IP privado de uma máquina virtual ou de um aparelho.  Também pode criar novas regras de firewall ou aplicar as regras de firewall existentes para permitir o tráfego de portas específicas e endereços IP no portal. Para mais detalhes, consulte [Alocar endereços IP públicos para ambiente cloud privado](public-ips.md).

## <a name="security"></a>Segurança

**Quais são as minhas opções de segurança na CloudSimple?**

A CloudSimple fornece as seguintes funcionalidades de segurança para garantir o seu ambiente De Nuvem Privada:

* **Dados em repouso encriptação**. Pode encriptar dados em repouso residente sanário no armazenamento vSAN na sua Nuvem Privada. VSAN suporta servidores de gestão de chaves externas, que podem ser implantados no seu ambiente Azure vNet ou no local.  Para mais detalhes, consulte [a encriptação Configure vSAN para a sua CloudSimple Private Cloud](vsan-encryption.md).
* **Segurança da rede.** Controle o fluxo de tráfego da rede com regras de firewall que se aplicam entre a sua Cloud Privada e a internet, a sua Nuvem Privada e o ambiente no local, ou dentro das subredes da sua Cloud Privada.
* **Ligação segura e privada.** Uma ligação segura e privada é estabelecida entre a sua rede no local e a sua subscrição Azure.

## <a name="compute"></a>Computação

**Que tipo de anfitriões estão disponíveis?**

A CloudSimple oferece estes tipos de anfitriões:

* **Nó CS28:** CPU:2x 2,2 GHz, total de 28 núcleos, 48 HT.  RAM: 256 GB.  Armazenamento: 1600 GB NVMe cache, 5760 GB de dados (All-Flash). Rede: 4x25Gbe NIC
* **Nó CS36:** CPU 2x 2,3 GHz, total 36 núcleos, 72 HT.  RAM: 512 GB.  Armazenamento: 3200 GB NVMe cache 11520 GB dados (All-Flash).  Rede: 4x25Gbe NIC
* **Nó CS36m:** CPU 2x 2,3 GHz, total 36 núcleos, 72 HT.  RAM: 576 GB.  Armazenamento: 3200 GB NVMe cache 13360 GB dados (All-Flash).  Rede: 4x25Gbe NIC

**Como é que as falhas de hardware são tratadas?**

Toda a infraestrutura CloudSimple é monitorizada continuamente pela plataforma CloudSimple e pelas nossas equipas de operações de serviço.  Se for detetada uma falha de hardware, um novo nó é adicionado à sua Cloud Privada e o nó falhado é removido.

## <a name="storage"></a>Storage

**Que tipo de armazenamento é suportado numa Nuvem Privada?**

A CloudSimple oferece armazenamento vSAN vSAN all-flash com cada Nuvem Privada.  Cada vSphere é criada com a sua própria loja de dados vSAN.  Para mais detalhes, consulte [os componentes Private Cloud VMware - armazenamento vSAN](vmware-components.md#vsan-storage).

**A encriptação de dados é suportada?**
Sim.  Pode configurar o armazenamento vSAN na sua Cloud Privada para utilizar um servidor de gestão de chaves (KMS) que é implantado no local ou no Azure para encriptar os dados armazenados no vSAN.

**Como são manuseados discos falhados?**

A CloudSimple monitoriza continuamente todos os componentes de hardware da Cloud Privada.  Se for detetada uma falha no disco ou se identificar um disco como falha (com base na heurística), um novo nó é automaticamente adicionado à Nuvem Privada.  O nó com o disco falhado ou em falha é removido da Nuvem Privada.

## <a name="vmware"></a>VMware

**Como faço upload em larga escala ou migração de aplicações e dados a partir do local?**

A CloudSimple fornece uma solução vSphere VSphere vSphere nativa.  Todas as ferramentas VMware para migração de dados em massa podem ser usadas com a sua Cloud Privada.  As opções incluem:

* VMware HCX para migração em massa de dados.
* Migração fria de dados usando armazenamento vMotion de instalações para CloudSimple.

**Posso instalar alguma ferramenta VMware?**

A CloudSimple fornece uma solução vSphere VSphere vSphere nativa.  Todas as ferramentas VMware utilizadas para gerir o ambiente vSphere no local podem ser utilizadas no CloudSimple.  A CloudSimple suporta um modelo de bring-your-own-license (BYOL) para instalar ferramentas VMware.

**Como são geridas atualizações e atualizações?**

A CloudSimple gere e atualiza todos os componentes de infraestrutura da sua Cloud Privada de forma perfeita e não disruptiva.  Todas as atualizações e patches de segurança lançados pela VMware ou pelos fornecedores de infraestruturas estão agendados para a atualização assim que forem qualificados pela CloudSimple.

A CloudSimple não realiza atualizações ou atualizações de aplicações instaladas na Cloud Privada.

## <a name="azure-integration"></a>Azure Integration (Integração do Azure)

**Que serviços azure são apoiados?**

A CloudSimple fornece uma ligação Azure ExpressRoute à sua subscrição no Azure.  Todos os serviços em execução na sua subscrição podem ligar-se à sua Cloud Privada.  Os exemplos incluem:

* **Azure Ative Diretório** como fonte de identidade para o seu vCenter CloudSimple.
* **Armazenamento azure** para armazenar backups, imagens e outros dados da sua Cloud Privada.
* **Aplicações híbridas** com uma arquitetura de aplicação que abrange nuvens públicas e privadas.  Por exemplo, pode criar webservers no Azure que acedam a aplicações e servidores de base de dados na sua Cloud Privada.
* **O monitor Azure** e o **centro de segurança Azure** para cargas de trabalho em funcionamento em vMware suportam a exploração madeireira, métricas de desempenho e gestão de segurança.

**Como posso mapear os meus inquilinos VMware para O Azure?**

A CloudSimple oferece a capacidade única de gerir os seus VMware VMs em Cloud Privada a partir do portal Azure.  Um conjunto de recursos vCenter configurado com restrições de recursos desejadas pode ser mapeado para a sua subscrição pelo seu administrador global.  

**Que benefícios de licenciamento recebo com o Azure?**

Com a CloudSimple, pode aproveitar o Benefício de Utilização Híbrido Azure e economizar até 90% em licenças. Este benefício preserva o seu investimento em licenças da Microsoft e reduz o seu TCO em relação a outras soluções na nuvem. Também obtém atualizações de segurança estendidas para windows Server 2008 e Microsoft SQL Server 2008.  O modelo bring-your-your-license (BYOL) ajuda-o a manter os custos baixos para aplicações comuns como Veeam e Zerto.  
