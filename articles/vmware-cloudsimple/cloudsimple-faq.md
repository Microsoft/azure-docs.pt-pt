---
title: FAQ - solução de VMware ao CloudSimple
description: Perguntas mais frequentes sobre solução de VMware do Azure por CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e187a4a75a27e206a632388f1e20a94da032eb08
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595352"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Perguntas mais frequentes sobre a solução de VMware ao CloudSimple

Perguntas mais frequentes e respostas sobre a solução de VMware do Azure por CloudSimple que o ajudam a compreender o serviço e como usá-lo. As perguntas e respostas estão organizadas nas seguintes categorias:

* Serviço de CloudSimple
* Conectividade
* Redes
* Segurança
* Computação
* Armazenamento
* VMware
* Integração do Azure
 
## <a name="cloudsimple-service"></a>Serviço de CloudSimple

**O que é a solução de VMware do Azure por CloudSimple?**

Solução de VMware do Azure por CloudSimple transforma e estende as cargas de trabalho do VMware para nuvens privados e dedicados no Azure em minutos. A solução Aprovisiona, gere a infraestrutura e orquestra a cargas de trabalho entre no local e o Azure. Uma vez que as suas aplicações funcionam exatamente a mesmo no local e no Azure, beneficia de elasticidade e serviços da cloud sem a complexidade de rearquitetar as suas aplicações. CloudSimple reduz o custo total de propriedade com um modelo de consumo na cloud que fornece a pedido provisionamento, pay-como--crescer e a otimização de capacidade. Para recursos, benefícios e cenários, consulte [o que é a solução de VMware do Azure por CloudSimple?](cloudsimple-vmware-solutions-overview.md).

**O que é uma nuvem privada CloudSimple?**

Aprovisionar uma nuvem privada e dedicada que consiste numa computação de alto desempenho, armazenamento e ambiente de rede implementado na infraestrutura do Microsoft Azure (espaço de hardware e do datacenter) em localizações do Azure. A nuvem privada fornece uma plataforma nativa do VMware, como um serviço. Em termos de VMware, cada nuvem privada contém exatamente uma instância do vCenter Server. O vCenter Server gerencia vários nós de ESXi contidos num ou mais clusters vSphere, juntamente com o armazenamento de vSAN correspondente. Um serviço de CloudSimple pode conter várias nuvens privadas na sua subscrição do Azure. Para obter mais informações sobre nuvens privadas, consulte [descrição geral da nuvem privada](cloudsimple-private-cloud.md).

**Onde o serviço de CloudSimple está disponível?**

CloudSimple está disponível nas regiões E.U.A. leste e E.U.A. oeste.

**Como posso ativar minha assinatura CloudSimple?**

Contacte o seu representante de conta da Microsoft em [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com) para ativar a sua subscrição para o serviço de CloudSimple. Forneça o seu ID de subscrição do e-mail para o qual pretende que o serviço CloudSimple ativado. 

**Como posso aceder ao portal de CloudSimple?**

Aceder ao portal de CloudSimple do portal do Azure. Para obter informações sobre como aceder ao portal de CloudSimple, consulte [CloudSimple portal a partir do portal do Azure para aceder a solução de VMware](https://docs.azure.cloudsimple.com/access-cloudsimple-portal).

**Como posso aumentar a capacidade para uma nuvem privada?**

Aprovisionar nós a partir do portal do Azure e expanda a sua nuvem privada a partir do portal do CloudSimple. Pode expandir a sua nuvem privada, adicionar nós a um cluster vSphere existente ou criando um novo cluster vSphere. Para obter informações sobre o procedimento, consulte [expanda uma nuvem privada CloudSimple](https://docs.azure.cloudsimple.com/expand-private-cloud).

**O que acontece ao meu nuvem privada durante a manutenção?**

CloudSimple oferece dias de notificações periódicas antes da manutenção agendada. Manutenção é feita de forma disruptivos para garantir a disponibilidade da sua cloud privada. Manutenção pode ser dos seguintes tipos:

- **Infraestrutura de CloudSimple**: A infraestrutura de CloudSimple foi concebida para ser elevada disponibilidade. Durante a manutenção, conectividade e a disponibilidade da sua cloud privada é garantido, atualizando componentes redundantes um por vez sem qualquer impacto. Terá acesso ao seu vCenter de nuvem privada, todas as máquinas virtuais, ligação de internet da sua nuvem privada e ligações para o Azure ou no local.
- **Portal de CloudSimple**: Durante a manutenção, algumas funcionalidades no portal do CloudSimple podem não estar acessíveis ou podem estar desativadas. A notificação de manutenção inclui informações sobre o que pode ser feito no portal.

## <a name="connectivity"></a>Conectividade

**Quais são minhas opções de conectividade à minha rede de região CloudSimple?**

CloudSimple fornece três diferentes opções de conectividade para ligar à sua rede de região CloudSimple. Todas as três opções podem ser utilizadas em conjunto:

- Ligação do Azure ExpressRoute do seu datacenter no local para a rede de região CloudSimple: Uma baixa latência segura privada conexão de alta velocidade que preenche o seu circuito do ExpressRoute no local com o seu circuito do CloudSimple ExpressRoute utilizando o alcance Global. Para configurar a ligação, veja [ligar no local ao CloudSimple com o ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection).
- Ligação do ExpressRoute de rede virtual do Azure à sua rede de região CloudSimple: Uma alta velocidade e de baixa latência privada ligação segura que se associa a rede virtual no Azure com o seu circuito do CloudSimple ExpressRoute através de gateways de rede virtual. Para configurar a ligação, veja [ligar o seu ambiente de nuvem privada de CloudSimple à rede virtual do Azure com o ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection).
- Site-site VPN ligação do seu datacenter no local à sua rede de região CloudSimple: Uma rede privada virtual do seu dispositivo VPN no local para a sua região de nuvem privada CloudSimple. Para configurar a ligação, veja [configurar uma ligação VPN entre a sua rede no local e a nuvem privada do CloudSimple](https://docs.azure.cloudsimple.com/set-up-vpn).

**Como posso ligar a uma nuvem privada?**

Pode ver os detalhes da sua cloud privada no portal do CloudSimple. Para ligar ao vCenter que corresponde à sua nuvem privada, certifique-se de que é estabelecida uma ligação de rede com o site a site, ponto a site ou ExpressRoute. Em seguida, inicie o portal de CloudSimple do portal do Azure. Selecione **inicie vSphere Client** na home page ou na página de detalhes de nuvem privada.

**O que é a vantagem de um circuito do ExpressRoute?**

Um circuito do ExpressRoute do Azure fornece uma ligação segura de alta velocidade e de baixa latência. CloudSimple fornece um circuito ExpressRoute dedicado por região por cliente. Utilizar este circuito, pode estabelecer uma ligação segura de aplicações no local e a sua subscrição do Azure.

**Quais são os custos de rede para ligar de e para CloudSimple? Existem quaisquer custos de saída de e para CloudSimple para o Azure? Existem quaisquer custos de saída em várias regiões?**

Não existe nenhum custo associado para a saída de rede. As taxas padrão do Azure aplicam-se a qualquer tráfego de saída da sua rede virtual ou a partir de um circuito do ExpressRoute no local.

## <a name="networking"></a>Redes

**Quais recursos de rede estão disponíveis para minha nuvem privada?**

Pode aprovisionar VLANs e as sub-redes e tabelas de firewall. Pode atribuir endereços IP públicos e mapear para uma máquina virtual que é executado na sua cloud privada. Para obter mais informações, consulte [descrição geral de VLANs e sub-redes](cloudsimple-vlans-subnets.md), [descrição geral de tabelas de Firewall](cloudsimple-firewall-tables.md), e [descrição geral de endereço IP público](cloudsimple-public-ip-address.md).

**Como posso configurar sub-redes diferentes para as minhas aplicações na minha nuvem privada?**

Pode criar VLANs na sua nuvem privada a partir do portal de CloudSimple. Depois de criar a VLAN, pode criar um grupo de portas distribuída no seu vCenter de nuvem privada com a VLAN e criar máquinas virtuais ligadas ao grupo de porta distribuída. Pode ativar uma tabela de firewall para a sub-rede ou VLAN e definir as regras de firewall para proteger o tráfego de rede.

**Quais configurações de firewall estão disponíveis para meu nuvens privadas?**

Pode configurar regras para tráfego Norte-Sul e este-oeste. As regras são definidas numa tabela de firewall. A tabela de firewall pode ser anexada a VLANs na sua nuvem privada. Para o procedimento de configuração, consulte [configurar tabelas de firewall e regras para nuvens privadas](https://docs.azure.cloudsimple.com/firewall).

**Posso atribuir endereços IP públicos para as VMs no meu ambiente de nuvem privada?**

No portal do CloudSimple, pode facilmente alocar um novo endereço IP público e associe-o com um endereço IP privado da sua máquina virtual ou aplicações. Também pode criar novas regras de firewall ou aplicar regras de firewall existente para permitir tráfego a partir de portas específicas e específicos conjuntos de endereços IP no portal. Para o procedimento de configuração, consulte [alocar endereços IP públicos para um ambiente de nuvem privada](https://docs.azure.cloudsimple.com/public-ips).

## <a name="security"></a>Segurança

**Quais são minhas opções de segurança no CloudSimple?**

Uma nuvem privada CloudSimple fornece as seguintes funcionalidades de segurança para proteger o seu ambiente de nuvem privada:

- **Dados de encriptação de Inativos:** Pode encriptar dados Inativos que residem no armazenamento de vSAN na sua cloud privada. vSAN oferece suporte a um servidor de gestão de chaves externas, que pode ser implementado no seu ambiente do Azure virtual de rede ou no local. Para obter mais informações, consulte [configurar a encriptação de vSAN para a sua nuvem privada CloudSimple](https://docs.azure.cloudsimple.com/vsan-encryption).
- **Segurança de rede:** Controle rede fluxo de tráfego de e para a sua nuvem privada a partir da internet, no local e entre sub-redes da sua nuvem privada utilizando regras de firewall.
- **Ligação segura e privada:** Proteger, uma ligação privada entre a rede no local e a sua subscrição do Azure.

## <a name="compute"></a>Computação

**Que tipo de anfitriões estão disponíveis?**

CloudSimple oferece dois tipos de anfitrião:

* **Nó de CS28**: CPU:2 x 2.2 GHz, total de 28 núcleos, 48 HT. RAM: 256 GB. Armazenamento: Cache de NVMe de 1600 GB, 5760-GB de dados (tudo Flash). Rede: 2x25Gbe NIC.
* **Nó de CS36**: CPU 2 x 2.3 GHz, o total de 36 núcleos, 72 HT. RAM: 512 GB. Armazenamento: Cache de NVMe 3200 GB 11,520-GB de dados (tudo Flash). Rede: 2x25Gbe NIC.

**Como são tratadas as falhas de hardware?**

Toda a infraestrutura CloudSimple é monitorado continuamente, a plataforma de CloudSimple e suas equipes de operações de serviço. Se for detetada uma falha de hardware, um novo nó é adicionado à sua nuvem privada. O nó com falha for removido, para assegurar a elevada disponibilidade da sua nuvem privada.

## <a name="storage"></a>Armazenamento

**Que tipo de armazenamento é suportado numa nuvem privada?**

Oferece CloudSimple **armazenamento de vSAN da VMware tudo flash** com cada nuvem privada. Cada vSphere é criado com seu próprio arquivo de dados de vSAN. Para obter mais informações, consulte [componentes de VMware de nuvem privada - armazenamento vSAN](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage).

**É a encriptação de dados suportadas?**
Sim. Pode configurar o armazenamento de vSAN na sua nuvem privada para utilizar um servidor de gestão de chaves (KMS), que é implementado no local ou no Azure para a encriptação dos dados armazenados no vSAN.

**Como os discos com falhas são manipulados?**

Monitorização de CloudSimple monitoriza continuamente todos os componentes de hardware da nuvem privada. Se for detetada falha de disco ou um disco é identificado como a falhar com base na heurística, um novo nó é adicionado automaticamente à nuvem privada. O nó com um disco com falha ou a falhar é removido da nuvem privada.

## <a name="vmware"></a>VMware

**Como posso executar carregamento de grande escala e a migração das aplicações e dados no local?**

CloudSimple fornece uma solução de vSphere nativa do VMware. Qualquer ferramenta usada para a migração de dados em massa pode ser utilizada com uma nuvem privada CloudSimple. Algumas das opções disponíveis são:

- HCX de VMware para migração em massa de dados.
- Fria migração de dados utilizando a vMotion de armazenamento no local para CloudSimple.

**Pode instalar ferramentas VMware?**

CloudSimple fornece uma solução de vSphere nativa do VMware. Qualquer ferramenta usada para gerenciar um ambiente de vSphere no local pode ser utilizado em CloudSimple. CloudSimple suporta um modelo do bring-your-own-license (BYOL) para instalar as ferramentas do VMware.

**Como são atualizações e melhoramentos geridos?**

CloudSimple gere e atualiza todos os componentes da infraestrutura da sua cloud privada, de forma totalmente integrada disruptivos. Qualquer patch de atualização ou de segurança lançado por fornecedores de VMware ou infraestrutura está agendado para atualização assim que ele está qualificado pelo CloudSimple.

CloudSimple não efetuar melhoramentos ou atualizações de aplicativos instalados na cloud privada.

## <a name="azure-integration"></a>Integração do Azure

**Que serviços do Azure são suportados?**

CloudSimple fornece a ligação do ExpressRoute do Azure à sua subscrição no Azure. Quaisquer serviços que são executados na sua subscrição tem conectividade de rede para a sua nuvem privada e podem ligar a sua nuvem privada. Exemplos:

- **Azure Active Directory**: Utilize o Azure Active Directory como uma origem de identidades para seu vCenter CloudSimple.
- **O armazenamento do Azure**: Utilize o armazenamento para armazenar cópias de segurança, imagens e outros dados da sua nuvem privada.
- **Aplicações híbridas**: É possível criar a arquitetura de aplicativo que abrange a nuvens públicas e privadas. Por exemplo, pode criar servidores web no Azure que aplicação de acesso e servidores de base de dados numa nuvem privada CloudSimple.
- **O Azure Monitor** e **Centro de segurança do Azure**: Carga de trabalho que é executado no VMware pode utilizar o Monitor e o Centro de segurança para log, métricas de desempenho e gerenciamento de segurança.

**Como posso mapear meus inquilinos de VMware para o Azure?**

CloudSimple fornece a capacidade única de gerir as VMs de VMware numa nuvem privada a partir do portal do Azure. Um agrupamento de recursos do vCenter configurado com as restrições de recurso que pretende que podem ser mapeados para a sua subscrição, o administrador global. 

**Quais benefícios do licenciamento posso fazer com o Azure?**

Com CloudSimple, pode tirar partido do benefício híbrido do Azure e poupe até 90 por cento nas licenças para preservar seus investimentos em licenças da Microsoft e reduza o custo total de propriedade em comparação comparada outras clouds. Obter mais atualizações de segurança do Windows Server 2008 e Microsoft SQL Server 2008. Manter os custos de baixa, com BYOL para a cloud para aplicações comuns, como o Veeam, Zerto e outros. 
