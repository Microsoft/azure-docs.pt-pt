---
title: Perguntas gerais sobre o serviço de Azure Site Recovery
description: Este artigo discute perguntas gerais populares sobre Azure Site Recovery.
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: f64b885e82d2f790d7d146e16bb6ccb44e207465
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497531"
---
# <a name="general-questions-about-azure-site-recovery"></a>Perguntas gerais sobre Azure Site Recovery

Este artigo resume as perguntas frequentes sobre Azure Site Recovery. Para cenários específicos, examine estes artigos

- [Perguntas sobre a recuperação de desastre de VM do Azure para o Azure](azure-to-azure-common-questions.md)
- [Perguntas sobre a recuperação de desastre de VM do VMware para o Azure](vmware-azure-common-questions.md)
- [Perguntas sobre a recuperação de desastre de VM do Hyper-V para o Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Geral

### <a name="what-does-site-recovery-do"></a>O que faz a Recuperação de Sites?
Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre), orquestrando e automatizando a replicação de VMs do Azure entre regiões, máquinas virtuais locais e servidores físicos para o Azure e máquinas locais para um datacenter secundário. [Saiba mais](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Posso proteger uma máquina virtual que tem um disco do Docker?

Não, este é um cenário sem suporte.

## <a name="service-providers"></a>Fornecedores de serviços

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Sou um provedor de serviços. Site Recovery funciona para modelos de infraestrutura dedicada e compartilhada?
Sim, a Recuperação de Sites suporta os modelos de infraestrutura partilhados e dedicados.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Para um provedor de serviços, é a identidade do meu locatário compartilhado com o serviço de Site Recovery?
Não. A identidade do locatário permanece anônima. Os seus inquilinos não precisam de acesso ao portal de recuperação de Recuperação de Sites. Apenas o administrador do fornecedor de serviços interage com o portal.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Os dados de aplicativo locatários vão para o Azure?
Quando há replicação entre sites pertencentes ao fornecedor de serviços, os dados das aplicações nunca chegam ao Azure. Os dados são criptografados em trânsito e replicados diretamente entre os sites do provedor de serviços.

Se está a replicar para o Azure, os dados da aplicação são enviados para armazenamento do Azure, mas não para o serviço de Recuperação de Sites. Os dados são criptografados em trânsito e permanecem criptografados no Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Os meus inquilinos receberão uma fatura de quaisquer serviços do Azure?
Não. A relação de faturação do Azure é diretamente com o fornecedor de serviços. Os fornecedores de serviços são responsáveis por gerar faturas específicas para os seus inquilinos.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Se estiver a replicar para o Azure, necessitamos executar as máquinas virtuais no Azure todo o tempo?
Não, os dados são replicados para o armazenamento do Azure em sua assinatura. Quando efetua uma ativação pós-falha de teste (simulação de recuperação após desastre) ou uma ativação pós-falha real, a Recuperação de Sites cria automaticamente máquinas virtuais na sua subscrição.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Garantem o isolamento de inquilinos quando faço replicação para o Azure?
Sim.

### <a name="what-platforms-do-you-currently-support"></a>Quais as plataformas a que atualmente dão suporte?
Damos suporte a implantações do Azure Pack, sistema de plataforma de nuvem e com base no System Center (2012 e superior). [Saiba mais](https://technet.microsoft.com/library/dn850370.aspx) sobre a integração do Azure Pack e do site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Suportam implementações únicas de Azure Pack e de servidor VMM?
Sim, você pode replicar máquinas virtuais Hyper-V para o Azure ou entre sites de provedor de serviços.  Observe que, se você replicar entre sites de provedor de serviços, a integração de runbook do Azure não estará disponível.

## <a name="pricing"></a>Preços

### <a name="where-can-i-find-pricing-information"></a>Onde posso encontrar informações sobre preços?
Examine [site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) detalhes de preços.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Como posso calcular os encargos aproximados durante o uso de Site Recovery?

Você pode usar a [calculadora de preços](https://aka.ms/asr_pricing_calculator) para estimar os custos ao usar site Recovery.

Para obter uma estimativa detalhada dos custos, execute a ferramenta planejador de implantação para [VMware](https://aka.ms/siterecovery_deployment_planner) ou [Hyper-V](https://aka.ms/asr-deployment-planner)e use o [relatório estimativa de custo](https://aka.ms/asr_DP_costreport).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Os Managed disks agora são usados para replicar VMs VMware e servidores físicos. Eu incorre em encargos adicionais para a conta de armazenamento em cache com discos gerenciados?

Não, não há encargos adicionais para o cache. Quando você replica para a conta de armazenamento Standard, esse armazenamento de cache faz parte da mesma conta de armazenamento de destino.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Sou utilizador do Azure Site Recovery há mais de um mês. Continuo a receber os primeiros 31 dias gratuitos para cada instância protegida?

Sim. Cada instância protegida não incorre em custos do Azure Site Recovery nos primeiros 31 dias. Por exemplo, se você estiver protegendo 10 instâncias nos últimos 6 meses e conectar uma instância de 11 a Azure Site Recovery, não haverá encargos para a instância de 11 durante os primeiros 31 dias. As primeiras 10 instâncias continuam a incorrer Azure Site Recovery encargos, pois eles foram protegidos por mais de 31 dias.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante os primeiros 31 dias, terei quaisquer outros custos com o Azure?

Sim, mesmo que Site Recovery seja gratuito durante os primeiros 31 dias de uma instância protegida, você poderá incorrer em encargos pelo armazenamento do Azure, transações de armazenamento e transferência de dados. Uma máquina virtual recuperada também pode ter custos de computação do Azure.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Há um custo associado para executar testes de recuperação de desastres/failover de teste?

Não há custo separado para a análise de DR. Haverá cobranças de computação depois que a VM for criada após o failover de teste.



## <a name="security"></a>Segurança

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Os dados de replicação são enviados para o serviço de Recuperação de Sites?
Não, Site Recovery não intercepta dados replicados e não tem nenhuma informação sobre o que está sendo executado em suas máquinas virtuais ou servidores físicos.
Os dados de replicação são trocados entre os anfitriões Hyper-V no local, os hipervisores VMware ou os servidores físicos, e o armazenamento do Azure ou o seu site secundário. A Recuperação de Sites não tem capacidade para intercetar esses dados. Apenas os metadados necessários para orquestrar a replicação e a ativação pós-falha são enviados para o serviço de Recuperação de Sites.  

Site Recovery é ISO 27001:2013, 27018, HIPAA, DPA certificado e está no processo de avaliações SOC2 e FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Por motivos de conformidade, até nossos metadados locais devem permanecer na mesma região geográfica. Site Recovery pode nos ajudar?
Sim. Quando você cria um cofre de Site Recovery em uma região, garantimos que todos os metadados que precisamos para habilitar e orquestrar a replicação e o failover permaneçam dentro do limite geográfico da região.

### <a name="does-site-recovery-encrypt-replication"></a>A Recuperação de Sites faz encriptação de replicação?
Para máquinas virtuais e servidores físicos, há suporte para a replicação entre os sites locais de criptografia em trânsito. Para máquinas virtuais e servidores físicos replicando para o Azure, há suporte para criptografia em trânsito e [criptografia em repouso (no Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption) .




## <a name="disaster-recovery"></a>Recuperação após desastre

### <a name="what-can-site-recovery-protect"></a>O que o Site Recovery pode proteger?
* **VMs do Azure**: Site Recovery pode replicar qualquer carga de trabalho em execução em uma VM do Azure com suporte
* **Máquinas virtuais do Hyper-v**: Site Recovery pode proteger qualquer carga de trabalho em execução em uma VM do Hyper-v.
* **Servidores físicos**: Site Recovery pode proteger servidores físicos que executam Windows ou Linux.
* **Máquinas virtuais VMware**: Site Recovery pode proteger qualquer carga de trabalho em execução em uma VM VMware.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Quais as cargas de trabalho que podem ser protegidas com a Recuperação de Sites ?
Você pode usar Site Recovery para proteger a maioria das cargas de trabalho em execução em uma VM ou servidor físico com suporte. Site Recovery fornece suporte para replicação com reconhecimento de aplicativo, para que os aplicativos possam ser recuperados para um estado inteligente. Ele se integra aos aplicativos da Microsoft, como o SharePoint, o Exchange, o Dynamics, o SQL Server e o Active Directory, e trabalha junto com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre proteção de carga de trabalho.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Posso gerir a recuperação após desastre para as minhas sucursais com a Recuperação de Sites?
Sim. Ao usar Site Recovery para orquestrar a replicação e o failover em suas filiais, você obterá uma orquestração unificada e uma exibição de todas as suas cargas de trabalho de filiais em um local central. Pode facilmente executar as ativações pós-falha e administrar a recuperação após desastre de todas as sucursais a partir da sede, sem visitar as sucursais.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Há suporte para a recuperação de desastres para VMs do Azure?

Sim, Site Recovery dá suporte a desastres para VMs do Azure entre regiões do Azure. [Examine as perguntas comuns](azure-to-azure-common-questions.md) sobre a recuperação de desastres de VM do Azure.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>A recuperação de desastre é compatível com VMs VMware?

Sim, Site Recovery dá suporte à recuperação de desastres de VMs VMware locais. [Examine as perguntas comuns](vmware-azure-common-questions.md) de recuperação de desastres de VMs VMware.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Há suporte para recuperação de desastre para VMs do Hyper-V?
Sim, Site Recovery dá suporte à recuperação de desastre de VMs do Hyper-V locais. [Examine as perguntas comuns](hyper-v-azure-common-questions.md) para a recuperação de desastres de VMs do Hyper-V.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>Há suporte para recuperação de desastres para servidores físicos?
Sim, Site Recovery dá suporte à recuperação de desastre de servidores físicos locais que executam o Windows e o Linux no Azure ou em um site secundário. Saiba mais sobre os requisitos de recuperação de desastres para o [Azure](vmware-physical-azure-support-matrix.md#replicated-machines)e para[um site secundário](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Observe que os servidores físicos serão executados como VMs no Azure após o failover. Atualmente, não há suporte para failback do Azure para um servidor físico local. Você só pode fazer failback para uma máquina virtual VMware.





## <a name="replication"></a>Replicação

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Posso replicar em uma VPN site a site para o Azure?
Azure Site Recovery replica dados para uma conta de armazenamento do Azure ou Managed disks, em um ponto de extremidade público. A replicação não está em uma VPN site a site. 

### <a name="why-cant-i-replicate-over-vpn"></a>Por que não posso replicar pela VPN?

Quando você replica para o Azure, o tráfego de replicação atinge os pontos de extremidade públicos de um armazenamento do Azure. Assim, você só pode replicar pela Internet pública com o ExpressRoute (emparelhamento da Microsoft ou um emparelhamento público existente) e a VPN não funciona.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Posso usar o Riverbed SteelHeads para replicação?

Nosso parceiro, Riverbed, fornece orientações detalhadas sobre como trabalhar com Azure Site Recovery. Examine o [guia da solução](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Posso usar o ExpressRoute para replicar máquinas virtuais para o Azure?
Sim, o [ExpressRoute pode ser usado](concepts-expressroute-with-site-recovery.md) para replicar máquinas virtuais locais para o Azure.

- Azure Site Recovery replica dados para um armazenamento do Azure por meio de um ponto de extremidade público. Você precisa configurar [o emparelhamento da Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) ou usar um [emparelhamento público](../expressroute/about-public-peering.md) existente (preterido para novos circuitos) para usar o ExpressRoute para replicação de site Recovery.
- O emparelhamento da Microsoft é o domínio de roteamento recomendado para replicação.
- Não há suporte para replicação em emparelhamento privado.
- Se você estiver protegendo máquinas VMware ou máquinas físicas, verifique se os [requisitos de rede](vmware-azure-configuration-server-requirements.md#network-requirements) do servidor de configuração também são atendidos. A conectividade com URLs específicas é exigida pelo servidor de configuração para orquestração da replicação do Site Recovery. O ExpressRoute não pode ser usado para essa conectividade.
- Após o failover das máquinas virtuais para uma rede virtual do Azure, você pode acessá-las usando a configuração de [emparelhamento privado](../expressroute/expressroute-circuit-peerings.md#privatepeering) com a rede virtual do Azure.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Se eu replicar para o Azure, que tipo de conta de armazenamento ou disco gerenciado eu preciso?

Você precisa de um armazenamento LRS ou GRS. Recomendamos GRS para que os dados sejam resilientes se ocorrer uma falha regional ou se a região primária não pode ser recuperada. A conta tem de ser na mesma região que o cofre de Serviços de Recuperação. O armazenamento Premium tem suporte para VM VMware, VM Hyper-V e replicação de servidor físico, quando você implanta Site Recovery no portal do Azure. O Managed disks dá suporte apenas a LRS.

### <a name="how-often-can-i-replicate-data"></a>Com que frequência posso replicar os dados?
* **Hyper-V:** As VMs do Hyper-V podem ser replicadas a cada 30 segundos (exceto para armazenamento Premium), cinco minutos ou 15 minutos.
* **VMs do Azure, VMS VMware, servidores físicos:** Uma frequência de replicação não é relevante aqui. A replicação é contínua.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Posso estender a replicação do site de recuperação existente para outro site terciário?
Não é suportada a replicação expandida ou em cadeia. Solicite este recurso no [Fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Posso fazer uma replicação offline pela primeira vez que faço a replicação para o Azure?
Tal não é suportado. Solicite esse recurso no [Fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Posso excluir discos específicos na replicação?
Isso é suportado quando você está replicando VMs VMware e VMs do Hyper-V para o Azure, usando o portal do Azure.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Posso replicar máquinas virtuais com discos dinâmicos?
Discos dinâmicos têm suporte ao replicar máquinas virtuais Hyper-V e ao replicar VMs VMware e máquinas físicas para o Azure. O disco do sistema operacional deve ser um disco básico.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Posso limitar a largura de banda alocada para o tráfego de replicação?
Sim. Você pode ler mais sobre a limitação de largura de banda nestes artigos:

* [Planejamento de capacidade para replicar VMs VMware e servidores físicos](site-recovery-plan-capacity-vmware.md)
* [Planejamento de capacidade para replicar VMs do Hyper-V para o Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Ativação Pós-Falha
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Se eu estiver fazendo o failover para o Azure, como posso acessar as VMs do Azure após a tolerância a falhas?

Pode aceder a VMs do Azure através de uma ligação de Internet segura, através de uma VPN de site para site ou através do ExpressRoute do Azure. Você precisa preparar várias coisas para se conectar. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Se eu fizer failover no Azure, como o Azure garantirá que meus dados sejam resilientes?
O Azure foi concebido para ser resiliente. O Site Recovery já foi projetado para failover para um datacenter secundário do Azure, de acordo com o SLA do Azure. Se isso acontecer, garantimos que seus metadados e cofres permaneçam na mesma região geográfica que você escolheu para seu cofre.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Se eu estiver replicando entre dois data centers, o que acontecerá se meu datacenter primário sofrer uma interrupção inesperada?
Pode acionar uma ativação pós-falha não planeada do site secundário. A Recuperação de Sites não necessita de conectividade do site primário para efetuar a ativação pós-falha.

### <a name="is-failover-automatic"></a>A ativação pós-falha é automática?
A ativação pós-falha não é automática. Você inicia failovers com um único clique no portal ou pode usar [site Recovery PowerShell](/powershell/module/az.recoveryservices) para disparar um failover. O failback é uma ação simples no portal de Site Recovery.

Para automatizar, você pode usar o orquestrador local ou Operations Manager para detectar uma falha de máquina virtual e disparar o failover usando o SDK.

* [Leia mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.
* [Leia mais](site-recovery-failover.md) sobre failover.
* [Leia mais](site-recovery-failback-azure-to-vmware.md) sobre o failback de VMs VMware e servidores físicos

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Se meu host local não está respondendo ou falhou, posso fazer failback para um host diferente?
Sim, você pode usar a recuperação de local alternativo para fazer failback para um host diferente do Azure.

* [Para máquinas virtuais VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Para máquinas virtuais do Hyper-V](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

## <a name="automation"></a>Automatização

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Posso automatizar cenários de Site Recovery com um SDK?
Sim. Pode automatizar fluxos de trabalho da Recuperação de Sites utilizando a API REST, PowerShell ou o SDK do Azure. Cenários com suporte no momento para implantar Site Recovery usando o PowerShell:

* [Replicar VMs do Hyper-V em nuvens VMMs para Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Replicar VMs do Hyper-V sem VMM para Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [Replicar o VMware no Azure com o PowerShell Resource Manager](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Atualização de componente/provedor

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Onde posso encontrar as notas de versão/pacotes cumulativos de atualizações de Site Recovery upgrades

[Saiba mais](site-recovery-whats-new.md) sobre novas atualizações e [Obtenha informações de rollup](service-updates-how-to.md).

## <a name="next-steps"></a>Passos seguintes
* Leia a [Descrição geral da Recuperação de Sites](site-recovery-overview.md)

