---
title: Perguntas frequentes-solução VMware por CloudSimple
description: Perguntas frequentes sobre a solução do Azure VMware por CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 80380a1c33927029e000e59a5834f297340f5be3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816240"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Perguntas frequentes sobre a solução VMware por CloudSimple

Perguntas frequentes e respostas sobre a solução do Azure VMware por CloudSimple que ajudam você a entender o serviço e como usá-lo. As perguntas e respostas são organizadas nas seguintes categorias:

* Serviço CloudSimple
* Conectividade
* Redes
* Segurança
* Computação
* Armazenamento
* VMware
* Integração do Azure
 
## <a name="cloudsimple-service"></a>Serviço CloudSimple

**O que é a solução do Azure VMware por CloudSimple?**

A solução Azure VMware por CloudSimple transforma e estende cargas de trabalho do VMware para nuvens privadas e dedicadas no Azure em minutos. A solução provisiona, gerencia a infraestrutura e orquestra as cargas de trabalho entre o local e o Azure. Como seus aplicativos são executados exatamente no mesmo local e no Azure, você se beneficia da elasticidade e dos serviços da nuvem sem a complexidade de rearquitetar seus aplicativos. O CloudSimple reduz o custo total de propriedade com um modelo de consumo de nuvem que fornece provisionamento sob demanda, pagamento conforme o crescimento e otimização da capacidade. Para recursos, benefícios e cenários, consulte [o que é a solução do Azure VMware por CloudSimple?](cloudsimple-vmware-solutions-overview.md).

**O que é uma nuvem privada CloudSimple?**

Você provisiona uma nuvem privada e dedicada que consiste em um ambiente de computação, armazenamento e rede de alto desempenho implantado em Microsoft Azure infraestrutura (hardware e espaço do Datacenter) em locais do Azure. A nuvem privada fornece uma plataforma VMware nativa como um serviço. Nos termos do VMware, cada nuvem privada contém exatamente uma instância do vCenter Server. O vCenter Server gerencia vários nós ESXi contidos em um ou mais clusters do vSphere, juntamente com o armazenamento do vSAN correspondente. Um serviço CloudSimple pode conter várias nuvens privadas em sua assinatura do Azure. Para obter mais informações sobre nuvens privadas, consulte [visão geral da nuvem privada](cloudsimple-private-cloud.md).

**Onde o serviço CloudSimple está disponível?**

O CloudSimple está disponível nas regiões leste dos EUA e oeste dos EUA.

**Como fazer habilitar minha assinatura para CloudSimple?**

Entre em contato com seu [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) representante de conta Microsoft em para habilitar sua assinatura para o serviço CloudSimple. Forneça sua ID de assinatura no email para o qual você deseja que o serviço CloudSimple seja habilitado. 

**Como fazer acessar o portal do CloudSimple?**

Você acessa o portal do CloudSimple da portal do Azure. Para obter informações sobre como acessar o portal do CloudSimple, consulte [acessar a solução VMware pelo portal do CloudSimple da portal do Azure](https://docs.azure.cloudsimple.com/access-cloudsimple-portal).

**Como fazer aumentar a capacidade de uma nuvem privada?**

Você provisiona nós do portal do Azure e expande sua nuvem privada no portal do CloudSimple. Você pode expandir sua nuvem privada adicionando nós a um cluster vSphere existente ou criando um novo cluster vSphere. Para obter informações sobre o procedimento, consulte [expandir uma nuvem privada CloudSimple](https://docs.azure.cloudsimple.com/expand-private-cloud).

**O que acontece com minha nuvem privada durante a manutenção?**

O CloudSimple fornece dias de notificações periódicas antes da manutenção agendada. A manutenção é feita em uma forma sem interrupções para garantir a disponibilidade da sua nuvem privada. A manutenção pode ser dos seguintes tipos:

- **Infraestrutura de CloudSimple**: A infraestrutura CloudSimple foi projetada para ser altamente disponível. Durante a manutenção, a conectividade e a disponibilidade da sua nuvem privada são garantidas pela atualização de componentes redundantes, um de cada vez, sem nenhum impacto. Você tem acesso ao vCenter de nuvem privada, a todas as máquinas virtuais, à conexão com a Internet de sua nuvem privada e a conexões locais ou do Azure.
- **Portal do CloudSimple**: Durante a manutenção, alguns recursos no portal do CloudSimple podem não estar acessíveis ou podem estar desabilitados. A notificação de manutenção inclui informações sobre o que pode ser feito no Portal.

## <a name="connectivity"></a>Conectividade

**Quais são minhas opções de conectividade para minha rede de região CloudSimple?**

O CloudSimple fornece três opções de conectividade diferentes para se conectar à sua rede de região do CloudSimple. Todas as três opções podem ser usadas juntas:

- Conexão do ExpressRoute do Azure do datacenter local para a rede de região CloudSimple: Uma conexão privada segura de alta velocidade de baixa latência que preenche seu circuito do ExpressRoute local com o circuito do ExpressRoute CloudSimple usando Alcance Global. Para configurar a conexão, consulte [conectar-se do local para CloudSimple usando o ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection).
- Conexão de ExpressRoute de sua rede virtual do Azure para sua rede de região CloudSimple: Uma conexão privada segura de alta velocidade e baixa latência que une sua rede virtual no Azure com o circuito do ExpressRoute CloudSimple usando gateways de rede virtual. Para configurar a conexão, consulte [conectar seu ambiente de nuvem privada do CloudSimple à rede virtual do Azure usando o ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection).
- Conexão VPN site a site do seu datacenter local para sua rede de região CloudSimple: Uma rede virtual privada segura do seu dispositivo VPN local para sua região de nuvem privada do CloudSimple. Para configurar a conexão, consulte [Configurar uma conexão VPN entre sua rede local e a nuvem privada CloudSimple](https://docs.azure.cloudsimple.com/set-up-vpn).

**Como fazer conectar-se a uma nuvem privada?**

Você pode exibir detalhes de sua nuvem privada no portal do CloudSimple. Para se conectar ao vCenter que corresponde à sua nuvem privada, certifique-se de que uma conexão de rede seja estabelecida usando site a site, ponto a site ou ExpressRoute. Em seguida, inicie o portal do CloudSimple no portal do Azure. Selecione **Iniciar cliente do vSphere** no Home Page ou na página de detalhes da nuvem privada.

**Qual é a vantagem de um circuito do ExpressRoute?**

Um circuito do Azure ExpressRoute fornece uma conexão segura de alta velocidade e baixa latência. O CloudSimple fornece um circuito de ExpressRoute dedicado por região por cliente. Usando esse circuito, você pode estabelecer uma conexão segura do local e da sua assinatura do Azure.

**Quais são os custos de rede para se conectar e de CloudSimple? Há algum encargo de saída de e para o CloudSimple para o Azure? Há algum encargo de egresso entre regiões?**

Não há nenhum encargo para a saída da rede. As taxas padrão do Azure se aplicam a qualquer tráfego de saída de sua rede virtual ou de um circuito de ExpressRoute local.

## <a name="networking"></a>Redes

**Quais recursos de rede estão disponíveis para minha nuvem privada?**

Você pode provisionar VLANs e suas sub-redes e tabelas de firewall. Você pode atribuir endereços IP públicos e mapear para uma máquina virtual que é executada em sua nuvem privada. Para obter mais informações, consulte [visão geral de VLANs e sub-redes](cloudsimple-vlans-subnets.md), [visão geral das tabelas de firewall](cloudsimple-firewall-tables.md)e [visão geral do endereço IP público](cloudsimple-public-ip-address.md).

**Como fazer configurar sub-redes diferentes para meus aplicativos na minha nuvem privada?**

Você pode criar VLANs em sua nuvem privada por meio do seu portal do CloudSimple. Depois de criar a VLAN, você pode criar um grupo de portas distribuídas em seu vCenter de nuvem privada usando a VLAN e criar máquinas virtuais conectadas ao grupo de portas distribuídas. Você pode habilitar uma tabela de firewall para a VLAN ou sub-rede e definir regras de firewall para proteger o tráfego de rede.

**Quais configurações de firewall estão disponíveis para minhas nuvens privadas?**

Você pode configurar regras para o tráfego Norte-Sul e leste-oeste. As regras são definidas em uma tabela de firewall. A tabela de firewall pode ser anexada a VLANs em sua nuvem privada. Para o procedimento de instalação, consulte [configurar as tabelas e regras de firewall para nuvens privadas](https://docs.azure.cloudsimple.com/firewall).

**Posso atribuir endereços IP públicos para VMs em meu ambiente de nuvem privada?**

No portal do CloudSimple, você pode facilmente alocar um novo endereço IP público e associá-lo a um endereço IP privado de sua máquina virtual ou de um dispositivo. Você também pode criar novas regras de firewall ou aplicar regras de firewall existentes para permitir o tráfego de portas específicas e de conjuntos específicos de endereços IP no Portal. Para o procedimento de instalação, consulte [alocar endereços IP públicos para um ambiente de nuvem privada](https://docs.azure.cloudsimple.com/public-ips).

## <a name="security"></a>Segurança

**Quais são minhas opções de segurança no CloudSimple?**

Uma nuvem privada do CloudSimple fornece os seguintes recursos de segurança para proteger seu ambiente de nuvem privada:

- **Criptografia de dados em repouso:** Você pode criptografar dados em repouso que residem no armazenamento do vSAN em sua nuvem privada. a vSAN dá suporte a um servidor de gerenciamento de chaves externas, que pode ser implantado em sua rede virtual do Azure ou no ambiente local. Para obter mais informações, consulte [Configurar a criptografia vSAN para sua nuvem privada do CloudSimple](https://docs.azure.cloudsimple.com/vsan-encryption).
- **Segurança de rede:** Controle o fluxo de tráfego de rede de e para sua nuvem privada da Internet, local e dentro de sub-redes de sua nuvem privada usando regras de firewall.
- **Conexão segura e privada:** Conexão segura e privada entre sua rede local e sua assinatura do Azure.

## <a name="compute"></a>Computação

**Que tipos de hosts estão disponíveis?**

O CloudSimple oferece dois tipos de host:

* **Nó CS28**: CPU: 2x 2,2 GHz, total de 28 núcleos, 48 HT. RAM: 256 GB. Armazenamento: Cache NVMe de 1600 GB, dados de 5760 GB (todos-flash). Rede NIC 2x25Gbe.
* **Nó CS36**: CPU 2x 2,3 GHz, total de 36 núcleos, 72 HT. RAM: 512 GB. Armazenamento: 3200-GB NVMe cache de 11.520-GB de dados (todos-flash). Rede NIC 2x25Gbe.

**Como as falhas de hardware são tratadas?**

Toda a infraestrutura do CloudSimple é monitorada continuamente pela plataforma CloudSimple e suas equipes de operações de serviço. Se uma falha de hardware for detectada, um novo nó será adicionado à sua nuvem privada. O nó com falha é removido para garantir a alta disponibilidade de sua nuvem privada.

## <a name="storage"></a>Armazenamento

**Que tipo de armazenamento tem suporte em uma nuvem privada?**

O CloudSimple oferece **todo-flash de armazenamento da vSAN do VMware** com cada nuvem privada. Cada vSphere é criado com seu próprio armazenamento de datavsan. Para obter mais informações, consulte [componentes do VMware na nuvem privada – armazenamento do vSAN](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage).

**Há suporte para a criptografia de dados?**
Sim. Você pode configurar o armazenamento vSAN em sua nuvem privada para usar um servidor de gerenciamento de chaves (KMS), que é implantado localmente ou no Azure para criptografia de dados armazenados no vSAN.

**Como os discos com falha são manipulados?**

O monitoramento de CloudSimple monitora continuamente todos os componentes de hardware da nuvem privada. Se uma falha de disco for detectada ou um disco for identificado como com falha com base na heurística, um novo nó será adicionado automaticamente à nuvem privada. O nó com um disco que falhou ou com falha é removido da nuvem privada.

## <a name="vmware"></a>VMware

**Como fazer executar o carregamento em larga escala e a migração de aplicativos e dados do local?**

O CloudSimple fornece uma solução de VMware vSphere nativa. Qualquer ferramenta usada para migração de dados em massa pode ser usada com uma nuvem privada CloudSimple. Algumas das opções disponíveis são:

- VMware HCX para migração em massa de dados.
- Migração fria de dados usando o Storage vMotion do local para o CloudSimple.

**Posso instalar as ferramentas do VMware?**

O CloudSimple fornece uma solução de VMware vSphere nativa. Qualquer ferramenta usada para gerenciar um ambiente vSphere local pode ser usada em CloudSimple. O CloudSimple dá suporte a um modelo BYOL (traga sua própria licença) para a instalação das ferramentas do VMware.

**Como as atualizações são gerenciadas?**

O CloudSimple gerencia e atualiza todos os componentes de infraestrutura da sua nuvem privada de maneira contínua e sem interrupções. Qualquer patch de atualização ou de segurança lançado por fornecedores VMware ou de infraestrutura é agendado para atualização assim que é qualificado pelo CloudSimple.

O CloudSimple não executa atualizações ou atualizações de aplicativos instalados na nuvem privada.

## <a name="azure-integration"></a>Integração do Azure

**Quais serviços do Azure têm suporte?**

O CloudSimple fornece a conexão do Azure ExpressRoute à sua assinatura no Azure. Todos os serviços que são executados em sua assinatura têm conectividade de rede para sua nuvem privada e podem se conectar à sua nuvem privada. Exemplos:

- **Azure Active Directory**: Use Azure Active Directory como uma fonte de identidade para seu CloudSimple vCenter.
- **Armazenamento do Azure**: Use o armazenamento para armazenar backups, imagens e outros dados de sua nuvem privada.
- **Aplicativos híbridos**: Você pode criar uma arquitetura de aplicativo que abranja nuvens públicas e privadas. Por exemplo, você pode criar servidores Web no Azure que acessam aplicativos e servidores de banco de dados em uma nuvem privada do CloudSimple.
- **Azure monitor** e a **central de segurança do Azure**: A carga de trabalho executada no VMware pode usar o monitor e a central de segurança para registro em log, métricas de desempenho e gerenciamento de segurança.

**Como fazer mapear meus locatários VMware para o Azure?**

O CloudSimple fornece a capacidade exclusiva de gerenciar suas VMs VMware em uma nuvem privada da portal do Azure. Um pool de recursos do vCenter configurado com as restrições de recursos que você deseja pode ser mapeado para sua assinatura pelo administrador global. 

**Quais benefícios de licenciamento obtenho com o Azure?**

Com o CloudSimple, você pode aproveitar o Benefício Híbrido do Azure e economizar até 90% em licenças para preservar seu investimento em licenças da Microsoft e reduzir o custo total de propriedade em comparação com outras nuvens. Você também obtém atualizações de segurança estendidas para o Windows Server 2008 e o Microsoft SQL Server 2008. Mantenha seus custos baixos com o BYOL para a nuvem para aplicativos comuns como Veeam, zerto e outros. 
