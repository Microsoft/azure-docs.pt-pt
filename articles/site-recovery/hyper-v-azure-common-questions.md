---
title: Perguntas comuns para recuperação de desastres hiper-V com recuperação do site Azure
description: Este artigo resume questões comuns sobre a criação de uma recuperação de desastres para os VMs hiper-V no local para Azure usando o site de recuperação do site Azure.
ms.date: 11/12/2019
ms.topic: conceptual
ms.openlocfilehash: 7c5f55fbea67567ddf7a2afa6a61f6c76568d829
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75498202"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Perguntas comuns: recuperação após desastre de Hyper-V para o Azure

Este artigo fornece respostas a perguntas comuns que vemos ao replicar no local VMs Hiper-V para Azure. 

## <a name="general"></a>Geral

### <a name="how-is-site-recovery-priced"></a>Como é que a Recuperação do Site tem o preço?
Reveja os detalhes de preços de recuperação do [site azure.](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="how-do-i-pay-for-azure-vms"></a>Como pago os VMs Azure?
Durante a replicação, os dados são replicados para o armazenamento do Azure, e não se paga alterações vm. Quando executa uma falha no Azure, a Recuperação do Site cria automaticamente máquinas virtuais Azure IaaS. Depois disso, és cobrado pelos recursos de computação que consomes em Azure.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Existe alguma diferença de custo sintetizada na reparação da conta de armazenamento V2 general Purpose?

Normalmente, verá um aumento do custo das transações incorridos nas contas de armazenamento GPv2, uma vez que a Recuperação do Site Azure é pesada. [Leia mais](../storage/common/storage-account-upgrade.md#pricing-and-billing) para estimar a mudança.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>O que preciso no Hyper-V para orquestrar a replicação com a Recuperação do Site?

Para o servidor de anfitrião Hyper-V, o que precisa depende do cenário de implementação. Confira os pré-requisitos do Hyper-V para:

* [Replicar VMs Hyper-V (sem o VMM) para o Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replicar VMs Hyper-V (com o VMM) para o Azure](site-recovery-vmm-to-azure.md)
* [Replicar VMs Hyper-V para um datacenter secundário](site-recovery-vmm-to-vmm.md)
* Se estiver a replicar-se para um centro de dados secundário, leia sobre [sistemas operativos de hóspedes suportados para VMs Hiper-V](https://technet.microsoft.com/library/mt126277.aspx).
* Se estiver a replicar-se no Azure, a Recovery do Site suporta todos os sistemas operativos dos hóspedes que são [suportados pelo Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Posso proteger os VMs quando o Hyper-V está a funcionar num sistema operativo de clientes?
Não, as VMs têm de estar localizadas num servidor de anfitrião Hyper-V que está a ser executado na máquina do servidor Windows suportada. Se precisar de proteger um computador cliente, pode replicá-lo como uma máquina física para [o Azure](site-recovery-vmware-to-azure.md) ou para um centro de [dados secundário](site-recovery-vmware-to-vmware.md).

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Os anfitriões hyper-V precisam estar nas nuvens vmm?
Se quiser replicar-se para um centro de dados secundário, então os VMs Hiper-V devem estar em servidores de anfitriões Hyper-V localizados numa nuvem VMM. Se quiser replicar para Azure, então pode replicar VMs com ou sem nuvens vmm. [Leia mais](tutorial-hyper-v-to-azure.md) sobre a replicação hyper-V para Azure.


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Pode replicar máquinas virtuais de 2.ª geração do Hyper-V para o Azure?
Sim. A Recuperação do Site converte-se da geração 2 para a geração 1 durante o failover. No failback a máquina é convertida de volta para a geração 2. [Leia mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Posso implementar a Recuperação de Sites com o VMM se tiver apenas um servidor VMM?

Sim. Pode replicar VMs em servidores Hyper-V na nuvem VMM para Azure, ou pode replicar entre nuvens vmm no mesmo servidor. Para a replicação no local, recomendamos que tenha um servidor VMM nos sites primários e secundários. 

### <a name="what-do-i-need-in-azure"></a>O que preciso em Azure?
Você precisa de uma assinatura Azure, um cofre de Serviços de Recuperação, uma conta de armazenamento, e uma rede virtual. O cofre, a conta de armazenamento e a rede devem estar na mesma região.

### <a name="what-azure-storage-account-do-i-need"></a>Que conta de armazenamento azure preciso?
Precisa de uma conta de armazenamento LRS ou GRS. Recomendamos GRS para que os dados sejam resilientes se ocorrer uma falha regional ou se a região primária não pode ser recuperada. O armazenamento premium é suportado.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>A minha conta Azure precisa de permissões para criar VMs?
Se é administrador de subscrição, tem as permissões de replicação de que necessita. Caso não esteja, precisa de permissões para criar um VM Azure no grupo de recursos e rede virtual que especifique quando configura a Recuperação do Site e permissões para escrever na conta de armazenamento selecionada. [Saiba mais](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>Os dados de replicação são enviados para a Recuperação do Site?
Não, a Recuperação do Site não interceta dados replicados e não tem nenhuma informação sobre o que está a ser feito nos seus VMs. Os dados de replicação são trocados entre os anfitriões Hyper-V e o armazenamento azure. A Recuperação de Sites não tem capacidade para intercetar esses dados. Apenas os metadados necessários para orquestrar a replicação e a ativação pós-falha são enviados para o serviço de Recuperação de Sites.  

A Recuperação do Site é ISO 27001:2013, 27018, HIPAA, DPA certificada, e está em processo de avaliações SOC2 e FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>Podemos manter no local metadados dentro de uma região geográfica?
Sim. Quando se cria um cofre numa região, garantimos que todos os metadados utilizados pela Recuperação do Site permanecem dentro do limite geográfico daquela região.

### <a name="does-site-recovery-encrypt-replication"></a>A Recuperação de Sites faz encriptação de replicação?
Sim, tanto a encriptação em trânsito como a [encriptação em Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) são suportadas.


## <a name="deployment"></a>Implementação

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>O que posso fazer com a replicação hyper-V a Azure?

- **Recuperação de desastres:** Pode configurar a recuperação total de desastres. Neste cenário, você replica no local Hiper-V VMs para armazenamento Azure:
    - Podes replicar VMs para o Azure. Se a sua infraestrutura no local não estiver disponível, falha o Azure.
    - Quando falha, os VMs Azure são criados usando os dados replicados. Pode aceder a apps e cargas de trabalho nos VMs Azure.
    - Quando o seu centro de dados no local estiver novamente disponível, pode voltar do Azure para o seu site no local.
- **Migração**: Você pode usar a Recuperação do Site para migrar no local VMs Hiper-V para o armazenamento Azure. Depois, falhas das instalações para o Azure. Após o failover, as suas aplicações e cargas de trabalho estão disponíveis e em execução em VMs Azure.


### <a name="what-do-i-need-on-premises"></a>O que preciso no local?

Você precisa de um ou mais VMs correndo em um ou mais anfitriões hiper-V autónomos ou agrupados. Também pode replicar VMs em anfitriões geridos pelo System Center Virtual Machine Manager (VMM).
- Se não estiver a executar VMM, durante a implantação da Recuperação do Local, reúne anfitriões e aglomerados hyper-V em sites hiper-V. Instala os agentes de recuperação do site (Fornecedor de Recuperação de Sítios Azure e agente de serviços de recuperação) em cada anfitrião hyper-V.
- Se os anfitriões Hyper-V estiverem localizados numa nuvem VMM, orquestrar a replicação em VMM. Instala o Fornecedor de Recuperação do Site no servidor VMM e o agente de Serviços de Recuperação em cada anfitrião Hyper-V. Mapeia entre redes lógica/VM VM e VNets Azure.
- [Saiba mais](hyper-v-azure-architecture.md) sobre a arquitetura Hyper-V para Azure.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Posso replicar VMs localizados num aglomerado hyper-V?

Sim, a Recuperação do Local suporta os anfitriões hiper-V agrupados. Tenha em atenção que:

- Todos os nós do aglomerado devem estar registados no mesmo cofre.
- Se não estiver a usar VMM, todos os anfitriões hyper-V do cluster devem ser adicionados ao mesmo site Hyper-V.
- Instala o agente azure de recuperação e serviços de recuperação de sítios em cada anfitrião hyper-V do cluster e adiciona cada anfitrião a um site Hyper-V.
- Não há passos específicos a fazer no cluster.
- Se executar a ferramenta De implantação para hyper-V, a ferramenta recolhe os dados de perfil do nó que está em execução e onde o VM está em execução. A ferramenta não pode recolher dados de um nó desligado, mas vai rastrear o nó. Depois do nó estar a funcionar, a ferramenta começa a recolher os dados do perfil VM a partir dele (se o VM fizer parte da lista vM de perfil e estiver a funcionar no nó).
- Se um VM num hospedeiro hyper-V num cofre de recuperação de sítios migra para um hospedeiro hiper-V diferente no mesmo cluster, ou para um hospedeiro autónomo, a replicação para o VM não é afetada. O hospedeiro Hyper-V deve satisfazer [os pré-requisitos](hyper-v-azure-support-matrix.md#on-premises-servers)e ser configurado num cofre de recuperação do local. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Posso proteger os VMs quando o Hyper-V está a funcionar num sistema operativo de clientes?
Não, as VMs têm de estar localizadas num servidor de anfitrião Hyper-V que está a ser executado na máquina do servidor Windows suportada. Se precisar de proteger um computador cliente, pode [replicá-lo como uma máquina física](physical-azure-disaster-recovery.md) para o Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Pode replicar máquinas virtuais de 2.ª geração do Hyper-V para o Azure?
Sim. A Recuperação do Site converte-se da geração 2 para a geração 1 durante o failover. No failback a máquina é convertida de volta para a geração 2.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Posso automatizar cenários de recuperação do site com um SDK?
Sim. Pode automatizar fluxos de trabalho da Recuperação de Sites utilizando a API REST, PowerShell ou o SDK do Azure. Atualmente, cenários suportados para replicar Hyper-V a Azure usando powerShell:

- [Replicar Hyper-V sem VMM usando PowerShell](hyper-v-azure-powershell-resource-manager.md)
- [Replicando Hyper-V com VMM usando Powershell](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replicação

### <a name="where-do-on-premises-vms-replicate-to"></a>Para onde se replicam os VMs no local?
Os dados replicam-se ao armazenamento do Azure. Quando executa uma falha, a Recuperação do Site cria automaticamente VMs Azure a partir da conta de armazenamento.

### <a name="what-apps-can-i-replicate"></a>Que aplicativos posso replicar?
Pode replicar qualquer aplicação ou carga de trabalho com um VM Hiper-V que cumpra os requisitos de [replicação](hyper-v-azure-support-matrix.md#replicated-vms). A Recovery do Site fornece suporte para replicação consciente da aplicação, para que as aplicações possam ser falhadas e falhadas de volta a um estado inteligente. A Recovery do Site integra-se com aplicações da Microsoft como SharePoint, Exchange, Dynamics, SQL Server e Ative Directory, e trabalha em estreita colaboração com fornecedores líderes, incluindo Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre proteção de carga de trabalho.

### <a name="whats-the-replication-process"></a>Qual é o processo de replicação?

1. Quando a replicação inicial é desencadeada, é tirada uma fotografia De VM Hyper-V.
2. Discos rígidos virtuais no VM são replicados um a um, até serem todos copiados para o Azure. Isto pode demorar algum tempo, dependendo do tamanho vm e largura de banda da rede. Aprenda a aumentar a largura de banda da rede.
3. Se ocorrerem alterações no disco durante a replicação inicial, o Rastreador de Replicação de Replicação de Réplica seleções Hyper-V rastreia as alterações à medida que os registos de replicação Hyper-V (.hrl). Estes ficheiros de registo estão localizados na mesma pasta que os discos. Cada disco tem um ficheiro .hrl associado que é enviado para armazenamento secundário. Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso.
4. Quando a replicação inicial for concluída, o instantâneo da VM é eliminado.
5. Quaisquer alterações no disco no registo são sincronizadas e fundidas no disco principal.
6. Após o final da replicação inicial, a proteção finalização no trabalho da máquina virtual funciona. Confunde a rede e outras definições de pós-replicação, de modo a que o VM esteja protegido.
7. Nesta fase, pode verificar as definições de VM para se certificar de que está pronto para a falha. Pode executar um exercício de recuperação de desastres (falha de teste) para o VM, para verificar se falha como esperado.
8. Após a replicação inicial, começa a replicação delta, de acordo com a política de replicação.
9. As alterações são registadas .hrl files. Cada disco que está configurado para replicação tem um ficheiro .hrl associado.
10. O registo é enviado para a conta de armazenamento do cliente. Quando um registo está em trânsito para o Azure, as alterações no disco primário são rastreadas noutro ficheiro de registo, na mesma pasta.
11. Durante a replicação inicial e delta, pode monitorizar o VM no portal Azure.

[Saiba mais](hyper-v-azure-architecture.md#replication-process) sobre o processo de replicação.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Posso replicar para Azure com uma VPN site-to-site?

A Recovery do Site replica dados do local para o armazenamento do Azure sobre um ponto final público, ou usando o peering expressRoute Microsoft. A replicação através de uma rede VPN site-to-site não é suportada.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Posso replicar para Azure com ExpressRoute?

Sim, a ExpressRoute pode ser usada para replicar VMs para Azure. A Recuperação do Site replica dados para uma Conta de Armazenamento Azure sobre um ponto final público, e você precisa configurar o [microsoft peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) para a replicação de Recovery do Site. Depois de os VMs falharem numa rede virtual Azure, pode aceder-lhes através de [um peering privado](../expressroute/expressroute-circuit-peerings.md#privatepeering).


### <a name="why-cant-i-replicate-over-vpn"></a>Por que não posso replicar sobre VPN?

Quando se replica para o Azure, o tráfego de replicação atinge os pontos finais públicos de uma conta de Armazenamento Azure. Assim, só é possível replicar através da internet pública com o ExpressRoute (peering da Microsoft) e a VPN não funciona. 

### <a name="what-are-the-replicated-vm-requirements"></a>Quais são os requisitos de VM replicados?

Para a replicação, um VM Hiper-V deve estar a executar um sistema operativo suportado. Além disso, o VM deve satisfazer os requisitos para os VMs Azure. [Saiba mais](hyper-v-azure-support-matrix.md#replicated-vms) na matriz de suporte.

### <a name="how-often-can-i-replicate-to-azure"></a>Quantas vezes posso replicar o Azure?

Os VMs hiper-V podem ser replicados a cada 30 segundos (exceto para armazenamento premium), 5 minutos ou 15 minutos.

### <a name="can-i-extend-replication"></a>Posso estender a replicação?
Não é suportada a replicação expandida ou em cadeia. Solicite esta funcionalidade no [fórum de feedback](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Posso fazer uma replicação inicial offline?
Tal não é suportado. Solicite esta funcionalidade no fórum de [feedback](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Posso excluir discos?
Sim, pode excluir discos da replicação. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Posso replicar VMs com discos dinâmicos?
Discos dinâmicos podem ser replicados. O disco do sistema operativo deve ser um disco básico.



## <a name="security"></a>Segurança

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Que acesso é que a Recuperação do Site precisa para os anfitriões hiper-V

A Recuperação do Site precisa de acesso aos anfitriões hyper-V para replicar os VMs que selecionar. A Recuperação do Site instala o seguinte nos anfitriões do Hyper-V:

- Se não estiver a executar o VMM, o agente do Fornecedor de Recuperação e Serviços de Recuperação do Site Azure está instalado em cada anfitrião.
- Se estiver a executar VMM, o agente de Serviços de Recuperação está instalado em cada hospedeiro. O Fornecedor funciona no servidor VMM.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>O que instala a Recuperação do Site em VMs Hiper-V?

A Recuperação do Site não instala explicitamente nada em VMs Hiper-V habilitados para replicação.




## <a name="failover-and-failback"></a>Ativação pós-falha e reativação pós-falha


### <a name="how-do-i-fail-over-to-azure"></a>Como é que falho com o Azure?

Executa uma ativação pós-falha planeada ou não planeada a partir das VMs de Hyper-V no local para o Azure.

- Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados.
- Pode executar uma falha não planeada se o seu site principal não estiver acessível.
- Pode fazer a ativação pós-falha de uma máquina individual ou criar planos de recuperação para orquestrar a ativação pós-falha de várias máquinas.
- A failover está em duas partes:
    - Após a primeira fase de failover complete, você deve ser capaz de ver as VMs réplicas criadas em Azure. Pode atribuir um endereço IP público à VM, se necessário.
    - Em seguida, compromete-se a falha, para começar a aceder à carga de trabalho da réplica Azure VM.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Como posso aceder aos VMs Azure depois do fracasso?
Após o failover, você pode aceder a VMs Azure através de uma conexão de Internet segura, através de um vpN site-to-site, ou sobre Azure ExpressRoute. Tens de preparar uma série de coisas para te ligares. [Saiba mais](failover-failback-overview.md#connect-to-azure-after-failover).

### <a name="is-failed-over-data-resilient"></a>Falha sobre os dados resilientes?
O Azure foi concebido para ser resiliente. A Recuperação do Site é projetada para falha num centro de dados secundário do Azure, de acordo com o Azure SLA. Quando ocorre uma falha, certificamo-nos de que os seus metadados e cofres permanecem dentro da mesma região geográfica que escolheu para o seu cofre.

### <a name="is-failover-automatic"></a>A ativação pós-falha é automática?
[Falhar](site-recovery-failover.md) não é automático. Inicia falhas com um único clique no portal, ou pode utilizar o [PowerShell](/powershell/module/az.recoveryservices) para desencadear uma falha.

### <a name="how-do-i-fail-back"></a>Como posso voltar?

Depois da sua infraestrutura no local estar a funcionar novamente, pode falhar. O failback ocorre em três fases:

1. Você inicia uma falha planeada de Azure para o local no local usando um par de opções diferentes:

    - Minimizar o tempo de inatividade: Se utilizar esta opção A recuperação do site sincroniza os dados antes do failover. Verifica os blocos de dados alterados e transfere-os para o local, enquanto o Azure VM continua a funcionar, minimizando o tempo de inatividade. Quando especifica manualmente que a falha deve ser completada, o VM Azure é desligado, quaisquer alterações delta finais são copiadas e a falha começa.
    - Download completo: Com esta opção os dados são sincronizados durante a failover. Esta opção descarrega todo o disco. É mais rápido porque não são calculados cheques, mas há mais tempo de inatividade. Use esta opção se estiver a executar as réplicas de VMs Azure há algum tempo, ou se o VM no local foi eliminado.

2. Pode selecionar para falhar de volta ao mesmo VM ou a um VM alternativo. Pode especificar que a Recuperação do Site deve criar o VM se já não existir.
3. Após os acabamentos iniciais da sincronização, selecione-se para completar a falha. Depois de concluído, pode iniciar sessão no VM no local para verificar se está tudo a funcionar como esperado. No portal Azure, pode ver-se que os VMs Azure foram parados.
4. Compromete-se a falhar para terminar, e começa a aceder à carga de trabalho do VM no local novamente.
5. Depois de as cargas de trabalho terem falhado, permite a replicação inversa, de modo que os VMs no local se reproduzam novamente para Azure.

### <a name="can-i-fail-back-to-a-different-location"></a>Posso voltar a um local diferente?
Sim, se falhares com o Azure, podes falhar num local diferente se o original não estiver disponível. [Saiba mais](hyper-v-azure-failback.md#fail-back-to-an-alternate-location).
