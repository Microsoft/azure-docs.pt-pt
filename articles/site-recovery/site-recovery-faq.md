---
title: Questões gerais sobre o serviço de recuperação do site Azure
description: Este artigo discute questões gerais populares sobre a Recuperação do Site Azure.
ms.topic: conceptual
ms.date: 1/24/2020
ms.author: raynew
ms.openlocfilehash: a9d0ae4a6e60a72bbb1148aca1a75c44506b2e9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79257683"
---
# <a name="general-questions-about-azure-site-recovery"></a>Questões gerais sobre recuperação do site azure

Este artigo resume frequentemente perguntas sobre a Recuperação do Site Azure. Para cenários específicos, reveja estes artigos

- [Perguntas sobre recuperação de desastres da VM Azure para Azure](azure-to-azure-common-questions.md)
- [Perguntas sobre recuperação de desastres VMware VM para Azure](vmware-azure-common-questions.md)
- [Perguntas sobre recuperação de desastres de Hiper-V VM para Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Geral

### <a name="what-does-site-recovery-do"></a>O que faz a Recuperação de Sites?
A Recuperação do Site contribui para a sua estratégia de continuidade de negócios e recuperação de desastres (BCDR), orquestrando e automatizando a replicação de VMs Azure entre regiões, máquinas virtuais no local e servidores físicos para O Azure, e máquinas no local para um centro de dados secundário. [Saiba mais](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Posso proteger uma máquina virtual que tem um disco do Docker?

Não, este é um cenário não apoiado.

## <a name="service-providers"></a>Fornecedores de serviços

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Sou um prestador de serviços. A Recuperação do Site funciona para modelos de infraestruturas dedicados e partilhados?
Sim, a Recuperação de Sites suporta os modelos de infraestrutura partilhados e dedicados.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Para um prestador de serviços, a identidade do meu inquilino é partilhada com o serviço de Recuperação do Site?
Não. A identidade do inquilino permanece anónima. Os seus inquilinos não precisam de acesso ao portal de recuperação de Recuperação de Sites. Apenas o administrador do fornecedor de serviços interage com o portal.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Os dados da candidatura do inquilino irão para o Azure?
Quando há replicação entre sites pertencentes ao fornecedor de serviços, os dados das aplicações nunca chegam ao Azure. Os dados são encriptados em trânsito e replicados diretamente entre os sites do prestador de serviços.

Se está a replicar para o Azure, os dados da aplicação são enviados para armazenamento do Azure, mas não para o serviço de Recuperação de Sites. Os dados são encriptados em trânsito e permanecem encriptados em Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Os meus inquilinos receberão uma fatura de quaisquer serviços do Azure?
Não. A relação de faturação do Azure é diretamente com o fornecedor de serviços. Os fornecedores de serviços são responsáveis por gerar faturas específicas para os seus inquilinos.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Se estiver a replicar para o Azure, necessitamos executar as máquinas virtuais no Azure todo o tempo?
Não, os Dados são replicados para o armazenamento Azure na sua subscrição. Quando efetua uma ativação pós-falha de teste (simulação de recuperação após desastre) ou uma ativação pós-falha real, a Recuperação de Sites cria automaticamente máquinas virtuais na sua subscrição.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Garantem o isolamento de inquilinos quando faço replicação para o Azure?
Sim.

### <a name="what-platforms-do-you-currently-support"></a>Quais as plataformas a que atualmente dão suporte?
Apoiamos implementações de Azure Pack, Cloud Platform System e System Center (2012 e superior). [Saiba mais](https://technet.microsoft.com/library/dn850370.aspx) sobre a integração do Azure Pack e da Recuperação do Site.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Suportam implementações únicas de Azure Pack e de servidor VMM?
Sim, pode replicar máquinas virtuais Hyper-V para Azure, ou entre sites de prestadores de serviços.  Note que se replicar entre sites de prestadores de serviços, a integração do livro de corridas azure não está disponível.

## <a name="pricing"></a>Preços

### <a name="where-can-i-find-pricing-information"></a>Onde posso encontrar informações sobre preços?
Rever detalhes de [preços de recuperação](https://azure.microsoft.com/pricing/details/site-recovery/) do site.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Como posso calcular os custos aproximados durante a utilização da Recuperação do Site?

Pode utilizar a [calculadora](https://aka.ms/asr_pricing_calculator) de preços para estimar os custos enquanto utiliza a Recuperação do Site.

Para uma estimativa detalhada dos custos, execute a ferramenta de planificador de implementação para [VMware](https://aka.ms/siterecovery_deployment_planner) ou [Hyper-V](https://aka.ms/asr-deployment-planner), e utilize o relatório de estimativa de [custos](https://aka.ms/asr_DP_costreport).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Os discos geridos são agora utilizados para replicar VMware VMs e servidores físicos. Incorrerei em encargos adicionais para a conta de armazenamento de cache com discos geridos?

Não, não há taxas adicionais para cache. Quando se replica para a conta de armazenamento padrão, este armazenamento de cache faz parte da mesma conta de armazenamento alvo.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Sou um utilizador de recuperação de sites azure há mais de um mês. Ainda tenho os primeiros 31 dias livres para cada instância protegida?

Sim. Todas as instâncias protegidas não incorrem em nenhuma carga de recuperação do site Azure nos primeiros 31 dias. Por exemplo, se tem protegido 10 instâncias nos últimos 6 meses e liga uma 11ª instância à Recuperação do Site Azure, não há encargos para a 11ª instância nos primeiros 31 dias. Os primeiros 10 casos continuam a incorrer em cargas de recuperação do site azure desde que estão protegidos por mais de 31 dias.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante os primeiros 31 dias, incorreria em outras acusações do Azure?

Sim, mesmo que a Recuperação do Site seja gratuita durante os primeiros 31 dias de uma instância protegida, poderá incorrer em encargos para o Armazenamento Azure, transações de armazenamento e transferência de dados. Uma máquina virtual recuperada também pode incorrer em cargas de computação Azure.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Existe algum custo associado para realizar exercícios de recuperação de desastres/falha de teste?

Não há nenhum custo separado para a broca DR. Haverá encargos computação após a criação do VM após a falha do teste.



## <a name="security"></a>Segurança

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Os dados de replicação são enviados para o serviço de Recuperação de Sites?
Não, a Recuperação do Site não interceta dados replicados e não tem nenhuma informação sobre o que está a ser feito nas suas máquinas virtuais ou servidores físicos.
Os dados de replicação são trocados entre os anfitriões Hyper-V no local, os hipervisores VMware ou os servidores físicos, e o armazenamento do Azure ou o seu site secundário. A Recuperação de Sites não tem capacidade para intercetar esses dados. Apenas os metadados necessários para orquestrar a replicação e a ativação pós-falha são enviados para o serviço de Recuperação de Sites.  

A Recuperação do Site é ISO 27001:2013, 27018, HIPAA, DPA certificada, e está em processo de avaliações SOC2 e FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Por razões de conformidade, mesmo os nossos metadados no local devem permanecer na mesma região geográfica. A Recuperação do Site pode ajudar-nos?
Sim. Quando se cria um cofre de recuperação de sítios numa região, garantimos que todos os metadados que precisamos para permitir e orquestrar a replicação e a falha permanecem dentro da fronteira geográfica daquela região.

### <a name="does-site-recovery-encrypt-replication"></a>A Recuperação de Sites faz encriptação de replicação?
Para máquinas virtuais e servidores físicos, é suportada a replicação entre sites no local de encriptação em trânsito. Para máquinas virtuais e servidores físicos que se replicam ao Azure, tanto a encriptação em trânsito como a [encriptação em repouso (em Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption) são suportadas.

### <a name="how-can-i-enforce-tls-12-on-all-on-premises-azure-site-recovery-components"></a>Como posso impor o TLS 1.2 em todos os componentes de recuperação do site Azure no local?
Os agentes de mobilidade instalados nos itens replicados comunicam ao Servidor de Processo apenas em TLS 1.2. No entanto, a comunicação do Servidor de Configuração para O Azure e do Servidor de Processo para o Azure pode estar em TLS 1.1 ou 1.0. Siga a [orientação](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) para impor o TLS 1.2 em todos os Servidores de Configuração e Servidores de Processos configurados por si.


## <a name="disaster-recovery"></a>Recuperação após desastre

### <a name="what-can-site-recovery-protect"></a>O que pode proteger a Recuperação do Site?
* **VMs Azure**: A recuperação do site pode replicar qualquer carga de trabalho em execução num Azure VM suportado
* **Máquinas virtuais hiper-V**: A recuperação do local pode proteger qualquer carga de trabalho em funcionamento num VM Hiper-V.
* **Servidores físicos**: A Recuperação do Site pode proteger servidores físicos que executam o Windows ou o Linux.
* **Máquinas virtuais VMware**: A recuperação do site pode proteger qualquer carga de trabalho em execução num VMware VM.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Quais as cargas de trabalho que podem ser protegidas com a Recuperação de Sites ?
Pode utilizar a Recuperação do Site para proteger a maioria das cargas de trabalho em funcionamento num VM ou servidor físico suportado. A Recovery do Site fornece suporte para replicação consciente da aplicação, para que as aplicações possam ser recuperadas para um estado inteligente. Integra-se com aplicações da Microsoft como SharePoint, Exchange, Dynamics, SQL Server e Ative Directory, e trabalha em estreita colaboração com fornecedores líderes, incluindo Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre proteção de carga de trabalho.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Posso gerir a recuperação após desastre para as minhas sucursais com a Recuperação de Sites?
Sim. Quando utilizar a Recuperação do Site para orquestrar a replicação e a falha nas suas filiais, terá uma orquestração unificada e uma visão de todas as suas cargas de trabalho em sucursais num local central. Pode facilmente executar as ativações pós-falha e administrar a recuperação após desastre de todas as sucursais a partir da sede, sem visitar as sucursais.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>A recuperação de desastres é apoiada para os VMs Azure?

Sim, a Recuperação do Local apoia o desastre para os VMs Azure entre as regiões de Azure. [Reveja as perguntas comuns](azure-to-azure-common-questions.md) sobre a recuperação de desastres da VM Azure.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>A recuperação de desastres é suportada para VMware VMs?

Sim, a Recuperação do Site apoia a recuperação de desastres de VMware VMs no local. [Reveja as questões comuns](vmware-azure-common-questions.md) para a recuperação de desastres de VMware VMs.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>A recuperação de desastres é suportada para VMs hiper-V?
Sim, a Recuperação do Local apoia a recuperação de desastres de Hiper-V V. no local. [Reveja as questões comuns](hyper-v-azure-common-questions.md) para a recuperação de desastres de VMs hiper-V.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>A recuperação de desastres é suportada para servidores físicos?
Sim, a Recovery do Site suporta a recuperação de desastres de servidores físicos no local que executam windows e Linux para Azure ou para um site secundário. Conheça os requisitos para a recuperação de desastres para [Azure](vmware-physical-azure-support-matrix.md#replicated-machines), e para[um local secundário.](vmware-physical-secondary-support-matrix.md#replicated-vm-support)
Note que os servidores físicos serão executados como VMs em Azure após a falha. O failback do Azure para um servidor físico no local não é suportado atualmente. Só pode falhar numa máquina virtual VMware.





## <a name="replication"></a>Replicação

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Posso replicar através de uma VPN site-to-site para Azure?
A Recuperação do Site Azure replica dados para uma conta de armazenamento Azure ou discos geridos, sobre um ponto final público. A replicação não é sobre uma VPN site-to-site. 

### <a name="why-cant-i-replicate-over-vpn"></a>Por que não posso replicar sobre VPN?

Quando se replica para o Azure, o tráfego de replicação atinge os pontos finais públicos de um Armazenamento Azure. Assim, só é possível replicar através da internet pública ou através do ExpressRoute (peering da Microsoft ou um público existente).

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Posso usar cabeças de aço riverbed para replicação?

O nosso parceiro, Riverbed, fornece orientações detalhadas sobre o trabalho com a Recuperação do Sítio Azure. Reveja o seu guia de [soluções.](https://community.riverbed.com/s/article/DOC-4627)

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Posso usar o ExpressRoute para replicar máquinas virtuais para o Azure?
Sim, o [ExpressRoute pode ser usado](concepts-expressroute-with-site-recovery.md) para replicar no local máquinas virtuais para Azure.

- A Recuperação do Site Azure replica dados para um Armazenamento Azure sobre um ponto final público. É necessário configurar o [microsoft peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) ou utilizar um [público](../expressroute/about-public-peering.md) existente (depreciado para novos circuitos) para utilizar a ExpressRoute para a replicação de Recovery do Site.
- O olhar da Microsoft é o domínio de encaminhamento recomendado para replicação.
- A replicação não é apoiada por um elo privado.
- Se estiver a proteger máquinas VMware ou máquinas físicas, certifique-se de que os [Requisitos](vmware-azure-configuration-server-requirements.md#network-requirements) de Rede para o Servidor de Configuração também são cumpridos. A conectividade com URLs específicos é exigida pelo Servidor de Configuração para a orquestração da replicação da Recuperação do Site. A ExpressRoute não pode ser utilizada para esta conectividade.
- Depois de as máquinas virtuais terem falhado numa rede virtual Azure, pode aceder-lhes através da configuração privada de [pares](../expressroute/expressroute-circuit-peerings.md#privatepeering) com a rede virtual Azure.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Se eu replicar para o Azure, que tipo de conta de armazenamento ou disco gerido preciso?

Precisa de um armazenamento LRS ou GRS. Recomendamos GRS para que os dados sejam resilientes se ocorrer uma falha regional ou se a região primária não pode ser recuperada. A conta tem de ser na mesma região que o cofre de Serviços de Recuperação. O armazenamento premium é suportado para VMware VM, Hyper-V VM e replicação de servidor físico, quando implementa a Recuperação do Site no portal Azure. Os discos geridos suportam apenas LRS.

### <a name="how-often-can-i-replicate-data"></a>Com que frequência posso replicar os dados?
* **Hiper-V:** Os VMs hiper-V podem ser replicados a cada 30 segundos (exceto para armazenamento premium), cinco minutos ou 15 minutos.
* **VMs Azure, VMware, servidores físicos:** Uma frequência de replicação não é relevante aqui. A replicação é contínua.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Posso estender a replicação do local de recuperação existente para outro local terciário?
Não é suportada a replicação expandida ou em cadeia. Solicite esta funcionalidade no [fórum de feedback](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Posso fazer uma replicação offline pela primeira vez que faço a replicação para o Azure?
Tal não é suportado. Solicite esta funcionalidade no fórum de [feedback](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Posso excluir discos específicos na replicação?
Isto é suportado quando está a replicar VMware VMs e VMs Hiper-V para Azure, utilizando o portal Azure.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Posso replicar máquinas virtuais com discos dinâmicos?
Os discos dinâmicos são suportados ao replicar máquinas virtuais Hyper-V e quando replicam VMware VMs e máquinas físicas para o Azure. O disco do sistema operativo deve ser um disco básico.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Posso estrangular a largura de banda atribuída para o tráfego de replicação?
Sim. Pode ler mais sobre a pulsação da largura de banda nestes artigos:

* [Planeamento de capacidade para replicar VMware VMs e servidores físicos](site-recovery-plan-capacity-vmware.md)
* [Planeamento de capacidade para replicar VMs Hiper-V para Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Ativação pós-falha
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Se estou a falhar com o Azure, como é que acedo aos VMs Azure depois do fracasso?

Pode aceder a VMs do Azure através de uma ligação de Internet segura, através de uma VPN de site para site ou através do ExpressRoute do Azure. Tens de preparar uma série de coisas para te ligares. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Se eu falhar com o Azure, como é que o Azure garante que os meus dados são resistentes?
O Azure foi concebido para ser resiliente. A Recuperação do Site já está concebida para falhar num centro de dados secundário do Azure, de acordo com o Azure SLA. Se isso acontecer, certificamo-nos de que os seus metadados e cofres permanecem dentro da mesma região geográfica que escolheu para o seu cofre.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Se estou a replicar entre dois centros de dados, o que acontece se o meu centro de dados principal tiver uma paragem inesperada?
Pode acionar uma ativação pós-falha não planeada do site secundário. A Recuperação de Sites não necessita de conectividade do site primário para efetuar a ativação pós-falha.

### <a name="is-failover-automatic"></a>A ativação pós-falha é automática?
A ativação pós-falha não é automática. Inicia falhas com um único clique no portal, ou pode utilizar o PowerShell de [Recuperação](/powershell/module/az.recoveryservices) do Site para desencadear uma falha. Falhar é uma simples ação no portal de recuperação do site.

Para automatizar, pode utilizar o Orquestrador ou Gestor de Operações no local para detetar uma falha virtual da máquina e, em seguida, desencadear a falha utilizando o SDK.

* [Leia mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.
* [Leia mais](site-recovery-failover.md) sobre o fracasso.
* [Leia mais](site-recovery-failback-azure-to-vmware.md) sobre falhas nas VMs e servidores físicos

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Se o meu anfitrião no local não está a responder ou a bater, posso falhar com um hospedeiro diferente?
Sim, você pode usar a recuperação de localização alternativa para falhar para um anfitrião diferente de Azure.

* [Para máquinas virtuais VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Para máquinas virtuais Hyper-V](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

## <a name="automation"></a>Automatização

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Posso automatizar cenários de recuperação do site com um SDK?
Sim. Pode automatizar fluxos de trabalho da Recuperação de Sites utilizando a API REST, PowerShell ou o SDK do Azure. Atualmente suportados cenários para implementar a recuperação do site usando powerShell:

* [Replicar VMs Hiper-V em nuvens vMMs para Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Replicar VMs Hiper-V sem VMM para Gestor de Recursos PowerShell Azure](hyper-v-azure-powershell-resource-manager.md)
* [Replicar VMware para Azure com PowerShell Resource Manager](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Atualização componente/fornecedor

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Onde posso encontrar as notas de lançamento/rollups de atualização de atualizações de recuperação do site

[Conheça](site-recovery-whats-new.md) novas atualizações e obtenha informações de [rollup.](service-updates-how-to.md)

## <a name="next-steps"></a>Passos seguintes
* Leia a [Descrição geral da Recuperação de Sites](site-recovery-overview.md)

