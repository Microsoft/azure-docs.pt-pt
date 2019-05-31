---
title: FAQ - solução de VMware ao CloudSimple
description: Perguntas mais frequentes sobre solução de VMware do Azure por CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e727398e1b7bfa406166574ab40320c68dac5709
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358532"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Perguntas mais frequentes sobre a solução de VMware ao CloudSimple

Perguntas mais frequentes e respostas sobre a solução de VMware ao CloudSimple que o ajudam a compreender o serviço e como usá-lo.  As perguntas e respostas estão organizadas nas categorias a seguir.

* Serviço de CloudSimple
* Conectividade
* Redes
* Segurança
* Computação
* Armazenamento
* VMware
* Integração do Azure
  
## <a name="cloudsimple-service"></a>Serviço de CloudSimple

**O que é a solução de VMware ao CloudSimple?**

**Solução de VMware ao CloudSimple** transforma e estende as cargas de trabalho do VMware para nuvens privados e dedicados no Azure em minutos. Nós tratamos de aprovisionamento, a gerir a infraestrutura e a orquestração de cargas de trabalho entre no local e o Azure. Uma vez que as suas aplicações funcionam exatamente a mesmo no local e no Azure, beneficia de elasticidade e serviços da cloud sem a complexidade de rearquitetar as suas aplicações. CloudSimple reduz o custo total de propriedade com um modelo de consumo na cloud que fornece a pedido provisionamento, pay-como--crescer e a otimização de capacidade.  Ver [o que é a solução de VMware no Azure por CloudSimple](cloudsimple-vmware-solutions-overview.md) para recursos, benefícios e cenários.

**O que é uma nuvem privada CloudSimple?**

Aprovisionar uma nuvem privada e dedicada, que consiste de computação de elevado desempenho, armazenamento e de rede do ambiente implementado na infraestrutura do Microsoft Azure (espaço de hardware e do datacenter) em localizações do Azure.  Nuvem privada fornece uma plataforma de VMware nativa "como serviço". Em termos de VMware, cada nuvem privada que contém exatamente uma instância do vCenter Server. O vCenter Server gerencia vários nós de ESXi contidos num ou mais Clusters vSphere, juntamente com o armazenamento de SAN Virtual (vSAN) correspondente. Um serviço de CloudSimple pode conter várias nuvens privadas na sua subscrição do Azure.  Para obter mais informações sobre nuvens privadas, consulte [descrição geral da nuvem privada](cloudsimple-private-cloud.md).

**Onde está disponível serviço CloudSimple?**

CloudSimple está disponível nas regiões E.U.A. leste e E.U.A. oeste.

**Como posso ativar minha assinatura CloudSimple?**

Pode contactar o seu representante de conta da Microsoft em [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com) para ativar a sua subscrição do serviço de CloudSimple. Forneça o seu ID de subscrição do e-mail para o qual pretende que o serviço CloudSimple ativado.  

**Como posso aceder ao portal de CloudSimple?**

Acessar o CloudSimple portal a partir do portal do Azure.  Ver [aceder a solução de VMware ao CloudSimple portal a partir do portal do Azure](https://docs.azure.cloudsimple.com/access-cloudsimple-portal) artigo para obter detalhes sobre como aceder ao portal de CloudSimple. 

**Como posso aumentar a capacidade para uma nuvem privada?**

Adquirir nós partir do portal do Azure e expanda a sua nuvem privada a partir do portal do CloudSimple.  Pode expandir a sua nuvem privada, adicionar nós adicionais ao cluster de vSphere existente ou criando o novo cluster vSphere.  Ver [expanda uma nuvem privada CloudSimple](https://docs.azure.cloudsimple.com/expand-private-cloud) artigo para o procedimento.

**O que acontece ao meu nuvem privada durante a manutenção?**

CloudSimple fornece notificações periódicas de dias antes da manutenção agendada.  Manutenção é feita de forma não disruptivas para garantir a disponibilidade da sua nuvem privada.  Manutenção pode ser dos seguintes tipos:

1. **Infraestrutura de CloudSimple:**  Infraestrutura de CloudSimple foi concebida para ser elevada disponibilidade.  Durante a manutenção, conectividade e a disponibilidade da sua nuvem privada é garantido, atualizando componentes redundantes um por vez sem qualquer impacto.  Terá acesso ao seu vCenter de nuvem privada, todas as máquinas virtuais, ligação de internet da sua nuvem privada e ligações para o Azure ou no local.
2. **Portal de CloudSimple:** Durante a manutenção, alguns recursos no portal de CloudSimple podem não estar acessível ou desativado.  Notificação de manutenção incluirá os detalhes do que pode ser feito no portal.

## <a name="connectivity"></a>Conectividade

**Quais são minhas opções de conectividade à rede da região de CloudSimple?**

CloudSimple fornece três diferentes opções de conectividade para ligar à sua rede de região CloudSimple.  Todos os três podem ser utilizados em conjunto.

1. Ligação do ExpressRoute do seu datacenter no local para a rede de região CloudSimple - as ligação privada segura dos alta velocidade de baixa latência seu circuito do ExpressRoute no local com o seu circuito do ExpressRoute de CloudSimple com alcance Global de protocolo de bridge. Ver [ligar no local ao CloudSimple através do ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection) artigo para configurar a ligação.
2. Ligação de ExpressRoute de rede virtual do Azure à sua rede de região CloudSimple - as ligação privada segura dos alta velocidade de baixa latência a ponte de rede virtual no Azure com o seu circuito do ExpressRoute de CloudSimple com gateways de rede virtual.  Ver [ligar o seu ambiente de nuvem privada CloudSimple para a rede virtual do Azure com o ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection) artigo para configurar a ligação.
3. Ligação de VPN de site a Site entre o seu datacenter no local à sua rede de região CloudSimple - proteger a rede privada virtual do seu dispositivo VPN no local para a sua região de nuvem privada CloudSimple.  Consulte [configurar a ligação de VPN entre a sua rede no local e a nuvem privada do CloudSimple] o artigo para configurar a ligação VPN.

**Como posso ligar a uma nuvem privada?**

Pode ver os detalhes da sua nuvem privada no Portal do CloudSimple. Para ligar ao vCenter correspondente à sua nuvem privada, certifique-se de ligação de rede é estabelecida através de Site a Site, ponto a Site ou ExpressRoute. Em seguida, inicie o portal de CloudSimple no portal do Azure e clique em *inicie vSphere Client* na Home page ou na página de detalhes de nuvem privada.

**O que é a vantagem de circuito do ExpressRoute?**

Circuito do ExpressRoute do Azure fornece uma ligação segura de alta velocidade de baixa latência.  CloudSimple fornece um circuito ExpressRoute dedicado por região por cliente.  Utilizar este circuito, pode estabelecer uma ligação segura no local e/ou a sua subscrição do Azure.

**Quais são os custos de rede para ligar em CloudSimple. Quaisquer custos de saída de/para CloudSimple para o Azure? Em outras regiões?**

Não existe nenhum custo para qualquer saída de rede.  As taxas padrão do Azure aplicam-se a qualquer tráfego de saída da sua rede virtual ou de circuito do ExpressRoute no local.

## <a name="networking"></a>Redes

**Quais recursos de rede estão disponíveis para a minha nuvem privada?**

Pode aprovisionar VLANs (e suas sub-redes), tabelas, de firewall e atribuir endereços IP públicos e mapear a uma máquina virtual em execução na sua nuvem privada.  Para obter mais informações, consulte [descrição geral de VLANs/sub-redes](cloudsimple-vlans-subnets.md), [descrição geral de tabelas de Firewall](cloudsimple-firewall-tables.md), e [descrição geral de endereço IP público](cloudsimple-public-ip-address.md) artigos.

**Como posso configurar sub-redes diferentes para as minhas aplicações na minha nuvem privada?**

Pode criar VLANs na sua nuvem privada a partir do Portal de CloudSimple.  Depois de criar a VLAN, pode criar um grupo de portas distribuída no seu vCenter de nuvem privada com a VLAN e criar máquinas virtuais ligadas ao grupo de porta distribuída.  Pode ativar a tabela de firewall para a sub-rede/VLAN e definir as regras de firewall para proteger o tráfego de rede.

**Quais as definições da firewall estão disponíveis para meu nuvens particulares?**

Pode configurar regras para tráfego Norte-Sul e este-oeste.  As regras são definidas numa tabela de firewall.  A tabela de firewall pode ser anexada a VLAN(s) na sua nuvem privada.  Ver [configurar tabelas de firewall e regras para nuvens privadas](https://docs.azure.cloudsimple.com/firewall) artigo para configurar o procedimento.

**Posso atribuir endereços IP públicos para as VMs no meu ambiente de nuvem privada?**

No portal do CloudSimple, pode facilmente alocar um novo endereço IP público e associe-o com um endereço IP privado da sua máquina virtual ou aplicações.  Também pode criar novas regras de firewall ou aplicar regras de firewall existente para permitir o tráfego de portas específicas e/ou específicos do conjunto de endereços IP no Portal. Ver [alocar endereços IP públicos para o ambiente de nuvem privada](https://docs.azure.cloudsimple.com/public-ips) para configurar o procedimento.

## <a name="security"></a>Segurança

**Quais são minhas opções de segurança no CloudSimple?**

Nuvem privada CloudSimple fornece as seguintes funcionalidades de segurança para proteger o seu ambiente de nuvem privada:

1. **Dados de encriptação de Inativos**: Pode encriptar dados inativos, que residem no armazenamento vSAN na sua nuvem privada. servidor de gestão de chaves externas, que pode ser implementado no ambiente do Azure vNet ou no local suporta a vSAN.  Ver [configurar a encriptação de vSAN para a sua nuvem privada CloudSimple](https://docs.azure.cloudsimple.com/vsan-encryption) para obter mais detalhes.
2. **Segurança de rede**: Controle rede fluxo de tráfego de/para a sua nuvem privada da Internet, no local e em sub-redes da sua nuvem privada com regras de firewall.
3. **Proteger, ligação privada**: Proteger a ligação privada entre a rede no local e a sua subscrição do Azure.

## <a name="compute"></a>Computação

**Que tipo de anfitriões estão disponíveis?**

CloudSimple oferece dois tipos de anfitrião:

* **Nó de CS28:** CPU:2 x 2.2 GHz, Total de 28 núcleos, 48 HT.  RAM: 256 GB.  Armazenamento: Cache de 1600 GB NVMe, 5760 GB de dados (tudo Flash). Rede: 2x25Gbe NIC
* **Nó de CS36:** CPU 2 x 2.3 GHz, Total 36 de núcleos, 72 HT.  RAM: 512 GB.  Armazenamento: 3200 GB NVMe Cache 11,520 GB de dados (tudo Flash).  Rede: 2x25Gbe NIC

**Como são manipuladas quaisquer falhas de hardware?**

Toda a infraestrutura CloudSimple é monitorado continuamente, a plataforma de CloudSimple e as nossas equipas de operações de serviço.  Se for detetada uma falha de hardware, um novo nó é adicionado à sua nuvem privada e com falhas de nó é removida de garantindo a alta disponibilidade da sua nuvem privada.

## <a name="storage"></a>Armazenamento

**Que tipo de armazenamento é suportado numa nuvem privada?**

Oferece CloudSimple **armazenamento de vSAN da VMware tudo flash** com cada nuvem privada.  Cada vSphere é criado com seu próprio arquivo de dados de vSAN.  Ver [componentes de VMware de nuvem privada - armazenamento vSAN](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage) artigo para obter mais detalhes.

**É a encriptação de dados suportadas?**
Sim.  Pode configurar o armazenamento de vSAN na sua nuvem privada para utilizar um servidor de gestão de chaves (KMS), que é implementado no local ou no Azure para a encriptação dos dados armazenados no vSAN

**Como os discos com falhas são manipulados?**

Monitorização de CloudSimple monitoriza continuamente todos os componentes de hardware da nuvem privada.  Se for detetada qualquer falha de disco ou qualquer disco é identificado como com falhas (com base na heurística), um novo nó é adicionado automaticamente para a nuvem privada.  O nó com o disco com falha ou a falhar é removido da nuvem privada.

## <a name="vmware"></a>VMware

**Como posso executar em grande escala carregamento/migrar aplicações e dados no local?**

CloudSimple fornece uma solução de vSphere nativo do VMware.  Qualquer ferramenta usada para a migração de dados em massa pode ser utilizada com a nuvem privada CloudSimple.  Algumas das opções disponíveis são:

1. HCX de VMware para migração em massa de dados.
2. Fria migração de dados usando a vMotion de armazenamento no local para CloudSimple.

**Pode instalar ferramentas VMware?**

CloudSimple fornece uma solução de vSphere nativo do VMware.  Qualquer ferramenta usada para gerir vSphere ambiente no local pode ser utilizada no CloudSimple.  CloudSimple suporta o modelo de Bring-Your-Own-License (BYOL) para instalar as ferramentas do VMware.

**Como são atualizações e melhoramentos geridos?**

CloudSimple gere e atualiza todos os componentes da infraestrutura de sua nuvem particular de forma não disruptivas totalmente integrada.  Assim que ele está qualificado pelo CloudSimple qualquer patch de atualização ou de segurança lançada pela infraestrutura de VMware ou fornecedores será agendada para atualização.

CloudSimple não efetuar melhoramentos ou atualizações de aplicativos instalados na Cloud privada.

## <a name="azure-integration"></a>Integração do Azure

**Que serviços do Azure são suportados?**

CloudSimple fornece a ligação do ExpressRoute do Azure à sua subscrição no Azure.  Quaisquer serviços em execução na sua subscrição tem conectividade de rede para a sua nuvem privada e pode ligar a sua nuvem privada.  Exemplos:

1. **O Azure Active Directory** como uma origem de identidade para o seu vCenter CloudSimple
2. **O armazenamento do Azure** para armazenar cópias de segurança, imagens e outros dados da sua nuvem privada
3. **Aplicações híbridas** -é possível criar a arquitetura de aplicativo que abrange a nuvens públicas e privadas.  Por exemplo, pode criar servidores Web no Azure que acedem a aplicações e servidores de base de dados na nuvem privada CloudSimple.
4. **O Azure monitor** e **Centro de segurança do Azure** -carga de trabalho em execução no VMware pode utilizá-los para log, métricas de desempenho e gerenciamento de segurança.

**Como posso mapear meus inquilinos de VMware para o Azure?**

CloudSimple fornece a capacidade única de gerir as VMs de VMware em nuvem privada a partir do portal do Azure.  Um agrupamento de recursos do vCenter (configurado com restrições de recurso desejado) pode ser mapeado para a sua subscrição pelo administrador global.  

**Quais benefícios do licenciamento posso fazer com o Azure?**

Com CloudSimple, que pode tirar partido do benefício de utilização híbrida do Azure e poupar até 90% no licenças preservar seus investimentos em Microsoft Licenses, reduzindo o TCO em comparação comparado outras clouds. Além disso, obter outras atualizações de segurança do Windows Server 2008 e Microsoft SQL Server 2008.  Manter os custos de baixa com traga a sua própria licenças (BYOL) para a cloud para aplicações comuns, como o Veeam, Zerto e outros.  