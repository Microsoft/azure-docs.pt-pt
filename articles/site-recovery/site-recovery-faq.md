---
title: 'Recuperação de Site do Azure: Perguntas mais frequentes | Documentos da Microsoft'
description: Este artigo aborda perguntas comuns sobre o Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/08/2019
ms.author: raynew
ms.openlocfilehash: 824782e54f2cd989f9ab13857d9b894b215fc550
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361357"
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>O Azure Site Recovery: Perguntas mais frequentes (FAQ)
Este artigo resume as perguntas mais frequentes sobre o Azure Site Recovery. 

## <a name="general"></a>Geral

### <a name="what-does-site-recovery-do"></a>O que faz a Recuperação de Sites?
Site Recovery contribui para a continuidade do negócio e a estratégia de recuperação (BCDR) de desastres, ao orquestrar e automatizar a replicação de VMs do Azure entre regiões, máquinas virtuais no local e servidores físicos para o Azure e máquinas para um datacenter secundário. [Saiba mais](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Pode proteger uma máquina virtual que tem um disco de Docker?

Não, isso é um cenário não suportado.

## <a name="service-providers"></a>Fornecedores de serviços

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Sou um fornecedor de serviços. Recuperação de sites funciona para modelos de infraestrutura partilhados e dedicados?
Sim, a Recuperação de Sites suporta os modelos de infraestrutura partilhados e dedicados.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Para um fornecedor de serviços, é a identidade do meu inquilino partilhado com o serviço Site Recovery?
Não. Inquilino de identidade permanece anônima. Os seus inquilinos não precisam de acesso ao portal de recuperação de Recuperação de Sites. Apenas o administrador do fornecedor de serviços interage com o portal.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Dados de aplicação de inquilino serão nunca irão para o Azure?
Quando há replicação entre sites pertencentes ao fornecedor de serviços, os dados das aplicações nunca chegam ao Azure. Dados são encriptados em trânsito e replicados diretamente entre os sites do fornecedor de serviço.

Se está a replicar para o Azure, os dados da aplicação são enviados para armazenamento do Azure, mas não para o serviço de Recuperação de Sites. Dados são encriptado em trânsito e permanecem encriptados no Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Os meus inquilinos receberão uma fatura de quaisquer serviços do Azure?
Não. A relação de faturação do Azure é diretamente com o fornecedor de serviços. Os fornecedores de serviços são responsáveis por gerar faturas específicas para os seus inquilinos.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Se estiver a replicar para o Azure, necessitamos executar as máquinas virtuais no Azure todo o tempo?
Não, os dados são replicados para o armazenamento do Azure na sua subscrição. Quando efetua uma ativação pós-falha de teste (simulação de recuperação após desastre) ou uma ativação pós-falha real, a Recuperação de Sites cria automaticamente máquinas virtuais na sua subscrição.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Garantem o isolamento de inquilinos quando faço replicação para o Azure?
Sim.

### <a name="what-platforms-do-you-currently-support"></a>Quais as plataformas a que atualmente dão suporte?
Suportamos o pacote do Azure, Cloud Platform System, e System Center com base em implementações (2012 e posterior). [Saiba mais](https://technet.microsoft.com/library/dn850370.aspx) sobre a integração do Azure Pack e o Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Suportam implementações únicas de Azure Pack e de servidor VMM?
Sim, pode replicar máquinas virtuais Hyper-V para o Azure ou entre sites do fornecedor de serviço.  Tenha em atenção que se replicar entre sites do fornecedor de serviço, integração de runbook do Azure não está disponível.

## <a name="pricing"></a>Preços

### <a name="where-can-i-find-pricing-information"></a>Onde posso encontrar informações sobre os preços?
Revisão [os preços da recuperação de Site](https://azure.microsoft.com/pricing/details/site-recovery/) detalhes.


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Como calcular custos aproximados durante o uso do Site Recovery?

Pode utilizar o [Calculadora de preços](https://aka.ms/asr_pricing_calculator) de estimar os custos ao utilizar o Site Recovery.

Para obter estimativa detalhada nos custos, execute a ferramenta deployment planner para [VMware](https://aka.ms/siterecovery_deployment_planner) ou [Hyper-V](https://aka.ms/asr-deployment-planner)e utilizar o [relatório de estimativa de custos](https://aka.ms/asr_DP_costreport).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Agora, os discos geridos são utilizados para replicar VMs de VMware e servidores físicos. Existem custos adicionais para a conta de armazenamento de cache com discos geridos?

Não, não existem não existem custos adicionais para a cache. Ao replicar a conta de armazenamento standard, este armazenamento de cache faz parte da mesma conta de armazenamento de destino.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Sou utilizador do Azure Site Recovery há mais de um mês. Continuo a receber os primeiros 31 dias gratuitos para cada instância protegida?

Sim. Cada instância protegida não incorre em custos do Azure Site Recovery nos primeiros 31 dias. Por exemplo, se tem sido 10 instâncias 6 meses e ligar uma instância de 11 para o Azure Site Recovery, existem não existem encargos para a 11ª instância nos primeiros 31 dias. As primeiras 10 instâncias continuam a incorrer em custos de recuperação de sites do Azure, uma vez que eles já estão protegidos há mais de 31 dias.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante os primeiros 31 dias, terei quaisquer outros custos com o Azure?

Sim, apesar do Site Recovery seja gratuito durante os primeiros 31 dias de uma instância protegida, pode incorrer em tarifas de armazenamento do Azure, transações de armazenamento e transferência de dados. Uma máquina virtual recuperada também pode ter custos de computação do Azure.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Existe um custo associado para efetuar a ativação pós-falha de teste/explorações de recuperação após desastre?

Não existe nenhum custo separado para exploração de DR. Haverá custos de computação quando a VM é criada após a ativação pós-falha de teste.



## <a name="security"></a>Segurança

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Os dados de replicação são enviados para o serviço de Recuperação de Sites?
Não, a recuperação de sites não interceta os dados replicados e não tem quaisquer informações sobre o que está a ser executado em suas máquinas virtuais ou servidores físicos.
Os dados de replicação são trocados entre os anfitriões Hyper-V no local, os hipervisores VMware ou os servidores físicos, e o armazenamento do Azure ou o seu site secundário. A Recuperação de Sites não tem capacidade para intercetar esses dados. Apenas os metadados necessários para orquestrar a replicação e a ativação pós-falha são enviados para o serviço de Recuperação de Sites.  

Site Recovery é ISO 27001:2013, 27018, HIPAA, DPA certified e está no processo de SOC2 e FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Por motivos de conformidade, até mesmo nosso metadados no local têm de permanecer na mesma região geográfica. Pode o Site Recovery ajudar-nos?
Sim. Quando cria um cofre de recuperação de sites numa região, podemos assegurar que todos os metadados que é necessário ativar e orquestrar a replicação e ativação pós-falha permanece dentro dessa região de geográfica limites.

### <a name="does-site-recovery-encrypt-replication"></a>A Recuperação de Sites faz encriptação de replicação?
Para máquinas virtuais e servidores físicos, a replicar entre locais sites encriptação em trânsito é suportada. Para máquinas virtuais e servidores físicos que replicam para o Azure, os dois encriptação em trânsito e [encriptação em repouso (no Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption) são suportados.




## <a name="disaster-recovery"></a>Recuperação após desastre

### <a name="what-can-site-recovery-protect"></a>O que pode proteger Site Recovery?
* **VMs do Azure**: Recuperação de sites pode replicar qualquer carga de trabalho em execução numa VM do Azure suportada
* **Máquinas de virtuais de Hyper-V**: Recuperação de sites pode proteger qualquer carga de trabalho em execução numa VM do Hyper-V.
* **Servidores físicos**: Recuperação de sites pode proteger servidores físicos que executem Windows ou Linux.
* **Máquinas virtuais VMware**: Recuperação de sites pode proteger qualquer carga de trabalho em execução numa VM de VMware.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Quais as cargas de trabalho que podem ser protegidas com a Recuperação de Sites ?
Pode utilizar o Site Recovery para proteger a maioria das cargas de trabalho em execução num servidor físico ou VM suportada. Recuperação de sites fornece suporte para a replicação com suporte a aplicativos, para que estas possam ser recuperadas para um Estado inteligente. Ele integra-se com aplicações da Microsoft, como SharePoint, Exchange, Dynamics, SQL Server e Active Directory e trabalha diretamente com fornecedores importantes, incluindo a Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre proteção de carga de trabalho.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Posso gerir a recuperação após desastre para as minhas sucursais com a Recuperação de Sites?
Sim. Quando utiliza o Site Recovery para orquestrar a replicação e ativação pós-falha das sucursais, obterá uma orquestração unificada e exibição de todas as suas branch office cargas de trabalho num local central. Pode facilmente executar as ativações pós-falha e administrar a recuperação após desastre de todas as sucursais a partir da sede, sem visitar as sucursais.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Recuperação após desastre é suportada para VMs do Azure?

Sim, o Site Recovery suporta após desastre para VMs do Azure entre regiões do Azure. [Reveja as perguntas mais comuns](azure-to-azure-common-questions.md) sobre recuperação de desastres da VM do Azure.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Recuperação após desastre é suportada para VMs de VMware?

Sim, o Site Recovery suporta recuperação após desastre de VMs de VMware no local. [Reveja as perguntas mais comuns](vmware-azure-common-questions.md) para recuperação após desastre de VMs de VMware.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Recuperação após desastre é suportada para VMs de Hyper-V?
Sim, o Site Recovery suporta recuperação após desastre de VMs de Hyper-V no local. [Reveja as perguntas mais comuns](hyper-v-azure-common-questions.md) para recuperação após desastre de VMs de Hyper-V.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>Recuperação após desastre é suportada para servidores físicos?
Sim, o Site Recovery suporta recuperação após desastre de servidores físicos no local a executar o Windows e Linux para o Azure ou para um site secundário. Saiba mais sobre os requisitos para recuperação após desastre [Azure](vmware-physical-azure-support-matrix.md#replicated-machines)e a[um site secundário](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Tenha em atenção que os servidores físicos executarão como VMs no Azure após a ativação pós-falha. Atualmente não é suportada a reativação pós-falha do Azure para um servidor físico no local. Apenas poderá reativar para uma máquina virtual VMware.





## <a name="replication"></a>Replicação

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Pode replicar através de uma VPN de site a site para o Azure?
O Azure Site Recovery replica os dados para uma conta de armazenamento do Azure ou discos geridos, ao longo de um ponto final público. Não é a replicação através de uma VPN de site a site. 

### <a name="why-cant-i-replicate-over-vpn"></a>Por que não é possível replicar através de VPN?

Ao replicar para o Azure, o tráfego de replicação atinge os pontos finais públicos do armazenamento do Azure. Assim, apenas pode replicar através da internet pública com o ExpressRoute (peering público), e a VPN não funciona.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Pode utilizar Riverbed SteelHeads para a replicação?

O nosso parceiro Riverbed, fornece orientações detalhadas sobre como trabalhar com o Azure Site Recovery. Rever seus [guia de soluções](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Posso utilizar o ExpressRoute para replicar máquinas virtuais para o Azure?
Sim, [pode ser utilizado o ExpressRoute](concepts-expressroute-with-site-recovery.md) para replicar máquinas virtuais no local para o Azure.

- O Azure Site Recovery replica os dados ao armazenamento do Azure através de um ponto final público. Terá de configurar [peering público](../expressroute/expressroute-circuit-peerings.md#publicpeering) ou [peering da Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) para utilizar o ExpressRoute para replicação do Site Recovery.
- Peering da Microsoft é o domínio de encaminhamento recomendado para a replicação.
- Depois das máquinas virtuais têm falhadas a uma rede virtual do Azure pode acessá-los utilizando o [peering privado](../expressroute/expressroute-circuit-peerings.md#privatepeering) configuração com a rede virtual do Azure.
- Replicação não é suportada por peering privado.
- Se estiver a proteger máquinas do VMware ou máquinas físicas, certifique-se de que está em conformidade com o servidor de configuração [requisitos de rede](vmware-azure-configuration-server-requirements.md#network-requirements) para replicação. 



### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Se replicar para o Azure, que tipo de conta de armazenamento ou disco gerido é necessário?

Necessita de um armazenamento LRS ou GRS. Recomendamos GRS para que os dados sejam resilientes se ocorrer uma falha regional ou se a região primária não pode ser recuperada. A conta tem de ser na mesma região que o cofre de Serviços de Recuperação. Armazenamento Premium é suportado para a VM de VMware, a VM de Hyper-V e replicação do servidor físico, quando implementar o Site Recovery no portal do Azure. Discos geridos só suportam LRS.

### <a name="how-often-can-i-replicate-data"></a>Com que frequência posso replicar os dados?
* **Hyper-v:** VMs de Hyper-V podem ser replicadas a cada cinco minutos, o 30 segundos (exceto para o armazenamento premium)
* **Servidores físicos VMs, VMs de VMware, do Azure:** Uma frequência de replicação não é relevante aqui. A replicação é contínua.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Pode expandir a replicação do site de recuperação existente para outro site terciária?
Não é suportada a replicação expandida ou em cadeia. Pedir esta funcionalidade no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Posso fazer uma replicação offline pela primeira vez que faço a replicação para o Azure?
Tal não é suportado. Pedir esta funcionalidade no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Posso excluir discos específicos na replicação?
Isto é suportado quando está a replicar VMware VMs e VMs de Hyper-V para o Azure com o portal do Azure.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Pode replicar máquinas virtuais com discos dinâmicos?
Discos dinâmicos são suportados quando replicar máquinas virtuais Hyper-V e ao replicar VMware VMs e máquinas físicas para o Azure. O disco do sistema operativo tem de ser um disco básico.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Posso limitar a largura de banda atribuída para o tráfego de replicação?
Sim. Pode ler mais sobre limitação de largura de banda nestes artigos:

* [Planeamento da capacidade para replicar VMware VMs e servidores físicos](site-recovery-plan-capacity-vmware.md)
* [Planeamento da capacidade para replicar VMs Hyper-V para o Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Ativação pós-falha
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Se eu estou entrar em ativação pós-falha para o Azure, como posso acessar as VMs do Azure após a ativação pós-falha?

Pode aceder a VMs do Azure através de uma ligação de Internet segura, através de uma VPN de site para site ou através do ExpressRoute do Azure. Terá de preparar uma série de coisas para se ligar. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Se eu efetuar a ativação pós-falha para o Azure como o Azure garantir que meus dados são resilientes?
O Azure foi concebido para ser resiliente. Recuperação de sites já foi desenvolvida para ativação pós-falha para um datacenter secundário do Azure, de acordo com o SLA do Azure. Se isto acontecer, verificamos se os metadados e cofres permanecem na mesma região geográfica que escolheu para o cofre.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Se estiver a replicar entre dois centros de dados, o que acontece se o meu datacenter primário sofre uma falha inesperada?
Pode acionar uma ativação pós-falha não planeada do site secundário. A Recuperação de Sites não necessita de conectividade do site primário para efetuar a ativação pós-falha.

### <a name="is-failover-automatic"></a>A ativação pós-falha é automática?
A ativação pós-falha não é automática. Inicia as ativações pós-falha com um clique único no portal ou pode utilizar [PowerShell da recuperação de Site](/powershell/module/az.recoveryservices) para acionar uma ativação pós-falha. Reativação pós-falha é uma ação simple no portal do Site Recovery.

Para automatizar o pode utilizar o Orchestrator no local ou o Operations Manager para detetar uma falha de máquina virtual e, em seguida, acionar a ativação pós-falha utilizando o SDK.

* [Leia mais](site-recovery-create-recovery-plans.md) sobre os planos de recuperação.
* [Leia mais](site-recovery-failover.md) sobre a ativação pós-falha.
* [Leia mais](site-recovery-failback-azure-to-vmware.md) como realizar a ativação fazer cópias de VMs de VMware e servidores físicos

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Se o meu host local não está a responder ou falhou, pode, reativação pós-falha para outro anfitrião?
Sim, pode utilizar a recuperação de localização alternativa para reativação pós-falha para outro anfitrião do Azure.

* [Para máquinas de virtuais de VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Para máquinas de virtuais de Hyper-V](hyper-v-azure-failback.md#perform-failback)

## <a name="automation"></a>Automatização

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Pode automatizar os cenários de recuperação de sites com um SDK?
Sim. Pode automatizar fluxos de trabalho da Recuperação de Sites utilizando a API REST, PowerShell ou o SDK do Azure. Cenários suportados atualmente para implementar o Site Recovery com o PowerShell:

* [Replicar VMs de Hyper-V em clouds de VMMs para o Azure PowerShell Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Replicar VMs de Hyper-V sem o VMM para o Azure PowerShell Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [Replicar o VMware no Azure com o Gestor de recursos do PowerShell](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Atualização do componente/fornecedor

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Onde posso encontrar os versão notas/cumulativos de atualizações do Site Recovery

[Saiba mais](site-recovery-whats-new.md) sobre novas atualizações, e [obter informação de rollup](service-updates-how-to.md).

## <a name="next-steps"></a>Passos Seguintes
* Leia a [Descrição geral da Recuperação de Sites](site-recovery-overview.md)

