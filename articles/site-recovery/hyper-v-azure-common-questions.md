---
title: Questões comuns para recuperação de desastres hiper-V com recuperação do local de Azure
description: Este artigo resume questões comuns sobre a criação de recuperação de desastres para os Hiper-V VMs para Azure no local, utilizando o site de recuperação do local de Azure.
ms.date: 11/12/2019
ms.topic: conceptual
ms.openlocfilehash: e4f5ee71fc56cad247b57f94bf08a68bed8775f9
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754356"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Perguntas comuns: recuperação após desastre de Hyper-V para o Azure

Este artigo fornece respostas a perguntas comuns que vemos ao replicar no local Hiper-V VMs para Azure. 

## <a name="general"></a>Geral

### <a name="how-is-site-recovery-priced"></a>Como é que a Recuperação do Local tem preço?
Reveja os detalhes [dos preços da recuperação do local do Azure.](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="how-do-i-pay-for-azure-vms"></a>Como pago os VMs do Azure?
Durante a replicação, os dados são replicados para o armazenamento do Azure, e não pagas alterações em VM. Quando corre uma falha no Azure, a Recuperação do Site cria automaticamente máquinas virtuais Azure IaaS. Depois disso, és cobrado pelos recursos computacional que consomes no Azure.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Existe alguma diferença de custos ao replicar-se na conta de armazenamento V2 de Finalidade Geral?

Normalmente, verá um aumento do custo das transações incorridas nas contas de armazenamento do GPv2, uma vez que a Recuperação do Site Azure é pesada. [Leia mais](../storage/common/storage-account-upgrade.md#pricing-and-billing) para estimar a mudança.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>O que preciso no Hyper-V para orquestrar a replicação com a Recuperação do Local?

Para o servidor de anfitrião Hyper-V, o que precisa depende do cenário de implementação. Confira os pré-requisitos do Hyper-V para:

* [Replicar VMs Hyper-V (sem o VMM) para o Azure](./hyper-v-azure-tutorial.md)
* [Replicar VMs Hyper-V (com o VMM) para o Azure](./hyper-v-vmm-disaster-recovery.md)
* [Replicar VMs Hyper-V para um datacenter secundário](./hyper-v-vmm-disaster-recovery.md)
* Se estiver a replicar-se num centro de dados secundário, leia-se sobre [sistemas operativos de hóspedes suportados para Hiper-VMs](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/mt126277(v=ws.11)).
* Se estiver a replicar o Azure, a Recuperação do Site suporta todos os sistemas operativos convidados que são [suportados pelo Azure.](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794868(v=ws.10))

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Posso proteger os VM quando o Hyper-V está a funcionar num sistema operativo de clientes?
Não, as VMs têm de estar localizadas num servidor de anfitrião Hyper-V que está a ser executado na máquina do servidor Windows suportada. Se precisar de proteger um computador cliente, poderá replicá-lo como uma máquina física para o [Azure](./vmware-azure-tutorial.md) ou um [centro de dados secundário](./vmware-physical-secondary-disaster-recovery.md).

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Os anfitriões Hiper-V precisam de estar nas nuvens de VMM?
Se pretender replicar-se num centro de dados secundário, então os VMs Hiper-V devem estar nos servidores de anfitriões Hiper-V localizados numa nuvem VMM. Se quiser replicar para Azure, então pode replicar VMs com ou sem nuvens VMM. [Leia mais](./hyper-v-azure-tutorial.md) sobre a replicação do Hiper-V para Azure.


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Pode replicar máquinas virtuais de 2.ª geração do Hyper-V para o Azure?
Yes. A Recuperação do Site converte-se da geração 2 para a geração 1 durante o failover. No failback, a máquina é convertida de volta para a geração 2. [Leia mais.](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Posso implementar a Recuperação de Sites com o VMM se tiver apenas um servidor VMM?

Yes. Pode replicar VMs em servidores Hiper-V na nuvem VMM para Azure, ou pode replicar-se entre nuvens VMM no mesmo servidor. Para a replicação no local, recomendamos que tenha um servidor VMM nos locais primários e secundários. 

### <a name="what-do-i-need-in-azure"></a>O que preciso em Azure?
Precisa de uma assinatura Azure, um cofre dos Serviços de Recuperação, uma conta de armazenamento e uma rede virtual. O cofre, a conta de armazenamento e a rede devem estar na mesma região.

### <a name="what-azure-storage-account-do-i-need"></a>Que conta de armazenamento Azure preciso?
Precisa de uma conta de armazenamento LRS ou GRS. Recomendamos GRS para que os dados sejam resilientes se ocorrer uma falha regional ou se a região primária não pode ser recuperada. O armazenamento premium é suportado.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>A minha conta Azure precisa de permissões para criar VMs?
Se for um administrador de subscrição, tem as permissões de replicação de que necessita. Caso contrário, precisa de permissões para criar um VM Azure no grupo de recursos e na rede virtual que especifica quando configurar a Recuperação do Site e permissões para escrever na conta de armazenamento selecionada. [Saiba mais](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>Os dados de replicação são enviados para a Recuperação do Local?
Não, a Recuperação do Site não interceta dados replicados, e não tem nenhuma informação sobre o que está a correr nos seus VMs. Os dados de replicação são trocados entre os anfitriões Hiper-V e o armazenamento Azure. A Recuperação de Sites não tem capacidade para intercetar esses dados. Apenas os metadados necessários para orquestrar a replicação e a ativação pós-falha são enviados para o serviço de Recuperação de Sites.  

A Recuperação do Local é ISO 27001:2013, 27018, HIPAA, DPA certificada, e está em processo de avaliações SOC2 e FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>Podemos manter no local metadados dentro de uma região geográfica?
Yes. Quando cria um cofre numa região, garantimos que todos os metadados utilizados pela Recuperação do Site permanecem dentro do limite geográfico daquela região.

### <a name="does-site-recovery-encrypt-replication"></a>A Recuperação de Sites faz encriptação de replicação?
Sim, tanto a encriptação em trânsito como [a encriptação em Azure](../storage/common/storage-service-encryption.md) são suportadas.


## <a name="deployment"></a>Implementação

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>O que posso fazer com a replicação do Hyper-V para o Azure?

- **Recuperação de desastres**: Pode configurar uma recuperação total de desastres. Neste cenário, você replica no local Hiper-V VMs para armazenamento Azure:
    - Pode replicar VMs para Azure. Se a sua infraestrutura no local não estiver disponível, falhe em Azure.
    - Quando falha, os VM do Azure são criados utilizando os dados replicados. Você pode aceder a aplicativos e cargas de trabalho nos VMs Azure.
    - Quando o seu datacenter no local estiver novamente disponível, pode falhar de volta de Azure para o seu site no local.
- **Migração**: Pode utilizar a Recuperação do Local para migrar no local Hiper-V VMs para o armazenamento de Azure. Depois, falhas de entradas no local para o Azure. Após o failover, as suas apps e cargas de trabalho estão disponíveis e em execução em VMs Azure.


### <a name="what-do-i-need-on-premises"></a>O que preciso no local?

Precisa de um ou mais VMs em execução em um ou mais anfitriões Hiper-V autónomos ou agrupados. Também pode replicar VMs em execução em anfitriões geridos pelo System Center Virtual Machine Manager (VMM).
- Se não estiver a executar O VMM, durante a implementação da Recuperação do Local, recolhe anfitriões e agrupamentos de Hiper-V em sites de Hiper-V. Instala os agentes de recuperação do local (Fornecedor de Recuperação do Local Azure e agente dos Serviços de Recuperação) em cada anfitrião Hiper-V.
- Se os anfitriões Hiper-V estiverem localizados numa nuvem VMM, você orquestra a replicação em VMM. Instala o Fornecedor de Recuperação do Site no servidor VMM e o agente dos Serviços de Recuperação em cada anfitrião Hiper-V. Mapeia entre as redes lógicas/VM da VMM e as VNets Azure.
- [Saiba mais](hyper-v-azure-architecture.md) sobre a arquitetura Hyper-V para Azure.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Posso replicar VMs localizados num aglomerado de Hiper-V?

Sim, a Recuperação do Site suporta anfitriões hiper-V agrupados. Tenha em atenção que:

- Todos os nós do aglomerado devem estar registados no mesmo cofre.
- Se não estiver a utilizar o VMM, todos os anfitriões Hiper-V do cluster devem ser adicionados ao mesmo site Hyper-V.
- Instale o agente do Fornecedor de Recuperação do Local Azure e dos Serviços de Recuperação em cada anfitrião Hiper-V no cluster e adicione cada hospedeiro a um site hiper-V.
- Não é necessário dar passos específicos no aglomerado.
- Se executar a ferramenta Desativação de Implementação para Hiper-V, a ferramenta recolhe os dados de perfil do nó que está em execução e onde o VM está a funcionar. A ferramenta não pode recolher nenhum dado de um nó que esteja desligado, mas irá rastrear esse nó. Depois de o nó estar em funcionamento, a ferramenta começa a recolher os dados do perfil VM a partir dele (se o VM fizer parte da lista de VM de perfil e estiver a funcionar no nó).
- Se um VM num hospedeiro Hiper-V num cofre de recuperação do local migrar para um hospedeiro Hiper-V diferente no mesmo cluster, ou para um hospedeiro autónomo, a replicação para o VM não é impactada. O anfitrião Hiper-V deve cumprir [os requisitos pré-requisitos](hyper-v-azure-support-matrix.md#on-premises-servers)e ser configurado num cofre de recuperação do local. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Posso proteger os VM quando o Hyper-V está a funcionar num sistema operativo de clientes?
Não, as VMs têm de estar localizadas num servidor de anfitrião Hyper-V que está a ser executado na máquina do servidor Windows suportada. Se precisar de proteger um computador cliente, pode [replicá-lo como uma máquina física](physical-azure-disaster-recovery.md) para o Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Pode replicar máquinas virtuais de 2.ª geração do Hyper-V para o Azure?
Yes. A Recuperação do Site converte-se da geração 2 para a geração 1 durante o failover. No failback, a máquina é convertida de volta para a geração 2.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Posso automatizar cenários de recuperação de sítios com um SDK?
Yes. Pode automatizar fluxos de trabalho da Recuperação de Sites utilizando a API REST, PowerShell ou o SDK do Azure. Atualmente, os cenários suportados para replicar o Hyper-V para O Azure utilizando o PowerShell:

- [Replicar Hiper-V sem VMM usando PowerShell](hyper-v-azure-powershell-resource-manager.md)
- [Replicação do Hiper-V com VMM utilizando PowerShell](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replicação

### <a name="where-do-on-premises-vms-replicate-to"></a>Para onde se replicam os VMs no local?
Os dados replicam-se no armazenamento do Azure. Quando executa um failover, a Recuperação do Site cria automaticamente VMs Azure a partir da conta de armazenamento.

### <a name="what-apps-can-i-replicate"></a>Que aplicativos posso replicar?
Pode replicar qualquer aplicação ou carga de trabalho executando um Hiper-V VM que cumpra os [requisitos de replicação](hyper-v-azure-support-matrix.md#replicated-vms). A Recuperação do Site fornece suporte para replicação consciente da aplicação, para que as aplicações possam ser falhadas e falhadas de volta a um estado inteligente. A Recuperação de Sites integra-se com aplicações da Microsoft como SharePoint, Exchange, Dynamics, SQL Server e Ative Directory, e trabalha em estreita colaboração com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre proteção de carga de trabalho.

### <a name="whats-the-replication-process"></a>Qual é o processo de replicação?

1. Quando a replicação inicial é desencadeada, é tirada uma imagem hyper-VM.
2. Os discos rígidos virtuais no VM são replicados um a um, até que todos sejam copiados para o Azure. Isto pode demorar algum tempo, dependendo do tamanho de VM e largura de banda de rede. Aprenda a aumentar a largura de banda da rede.
3. Se as alterações do disco ocorrerem enquanto a replicação inicial está em andamento, o Localizador de Replicação de Réplica Hiper-V rastreia as alterações como registos de replicação de Hiper-V (.hrl). Estes ficheiros de registo estão localizados na mesma pasta que os discos. Cada disco tem um ficheiro .hrl associado que é enviado para armazenamento secundário. Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso.
4. Quando a replicação inicial for concluída, o instantâneo da VM é eliminado.
5. Quaisquer alterações de disco no registo são sincronizadas e fundidas no disco principal.
6. Após o final da replicação inicial, a proteção finalize no trabalho da máquina virtual. Configura a rede e outras definições de pós-replicação, de modo a que o VM seja protegido.
7. Nesta fase, pode verificar as definições de VM para se certificar de que está pronto para o failover. Pode executar um exercício de recuperação de desastres (teste failover) para o VM, para verificar se falha como esperado.
8. Após a replicação inicial, a replicação delta começa, de acordo com a política de replicação.
9. As alterações são registadas nos ficheiros .hrl. Cada disco que está configurado para replicação tem um ficheiro .hrl associado.
10. O registo é enviado para a conta de armazenamento do cliente. Quando um registo está em trânsito para Azure, as alterações no disco primário são rastreadas noutro ficheiro de registo, na mesma pasta.
11. Durante a replicação inicial e delta, pode monitorizar o VM no portal Azure.

[Saiba mais](hyper-v-azure-architecture.md#replication-process) sobre o processo de replicação.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Posso replicar para Azure com uma VPN local?-to-site?

A Recuperação do Site replica dados das instalações para o armazenamento do Azure através de um ponto final público, ou usando o olho da Microsoft ExpressRoute. A replicação sobre uma rede VPN local-a-local não é suportada.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Posso replicar para Azure com ExpressRoute?

Sim, o ExpressRoute pode ser usado para replicar VMs para Azure. A Recuperação do Site replica dados numa Conta de Armazenamento Azure sobre um ponto final público, e precisa de configurar [a Microsoft a espreitar](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) para a replicação da Recuperação do Site. Depois de os VM falharem numa rede virtual Azure, pode aceder-lhes através de [um espremiamento privado.](../expressroute/expressroute-circuit-peerings.md#privatepeering)


### <a name="why-cant-i-replicate-over-vpn"></a>Por que não posso replicar sobre VPN?

Quando se replica para Azure, o tráfego de replicação atinge os pontos finais públicos de uma conta de Armazenamento Azure. Assim, só pode replicar-se através da internet pública com o ExpressRoute (Microsoft a espreitar) e a VPN não funciona. 

### <a name="what-are-the-replicated-vm-requirements"></a>Quais são os requisitos de VM replicados?

Para a replicação, um Hiper-VM deve estar a executar um sistema operativo suportado. Além disso, o VM deve satisfazer os requisitos para os VMs Azure. [Saiba mais](hyper-v-azure-support-matrix.md#replicated-vms) na matriz de suporte.

### <a name="why-is-an-additional-standard-storage-account-required-if-i-replicate-my-virtual-machine-disks-to-premium-storage"></a>Por que é necessária uma conta de armazenamento padrão adicional se eu replicar os meus discos de máquina virtual para armazenamento premium?

Quando replica as suas máquinas virtuais/servidores físicos no local para armazenamento premium, todos os dados que residem nos discos da máquina protegida são replicados na conta de armazenamento premium. É necessária uma conta de armazenamento padrão adicional para armazenar registos de replicação. Após a fase inicial de replicação dos dados do disco estar concluída, todas as alterações nos dados do disco no local são rastreadas continuamente e armazenadas como registos de replicação nesta conta de armazenamento padrão adicional.

### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência posso replicar-me ao Azure?

Os VMs hiper-V podem ser replicados a cada 30 segundos (exceto para armazenamento premium) ou 5 minutos.

### <a name="can-azure-site-recovery-and-hyper-v-replica-be-configured-together-on-a-hyper-v-machine"></a>A recuperação do local de Azure e a réplica do Hiper-V podem ser configuradas juntas numa máquina De Hiper-V?

Sim, tanto a Recuperação do Local Azure como a Réplica Hiper-V podem ser configuradas em conjunto para uma máquina. Mas a máquina terá de ser protegida como uma máquina física e será replicada para o Azure utilizando um servidor de Configuração/Processo. Saiba mais sobre a proteção das máquinas físicas [aqui.](https://docs.microsoft.com/azure/site-recovery/physical-azure-architecture)

### <a name="can-i-extend-replication"></a>Posso estender a replicação?
Não é suportada a replicação expandida ou em cadeia. Solicite esta funcionalidade no [fórum de comentários.](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959)

### <a name="can-i-do-an-offline-initial-replication"></a>Posso fazer uma replicação inicial offline?
Tal não é suportado. Solicite esta funcionalidade no [fórum de comentários.](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from)

### <a name="can-i-exclude-disks"></a>Posso excluir discos?
Sim, pode excluir discos da replicação. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Posso replicar VMs com discos dinâmicos?
Os discos dinâmicos podem ser replicados. O disco do sistema operativo deve ser um disco básico.



## <a name="security"></a>Segurança

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>O que o acesso da Recuperação do Site precisa para os anfitriões Do Hiper-V

A Recuperação do Site precisa de acesso a anfitriões Hiper-V para replicar os VMs que seleciona. A Recuperação do Site instala o seguinte em anfitriões Hiper-V:

- Se não estiver a executar o VMM, o agente do Fornecedor de Recuperação do Local Azure e do Serviço de Recuperação está instalado em cada hospedeiro.
- Se estiver a executar o VMM, o agente dos Serviços de Recuperação está instalado em cada hospedeiro. O Fornecedor funciona no servidor VMM.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>O que é que a Recuperação do Site instala em Hiper-VMs?

A Recuperação do Site não instala explicitamente nada em Hiper-VMs habilitados para replicação.




## <a name="failover-and-failback"></a>Ativação pós-falha e reativação pós-falha


### <a name="how-do-i-fail-over-to-azure"></a>Como posso falhar com Azure?

Executa uma ativação pós-falha planeada ou não planeada a partir das VMs de Hyper-V no local para o Azure.

- Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados.
- Pode executar uma falha não planeada se o seu site principal não estiver acessível.
- Pode fazer a ativação pós-falha de uma máquina individual ou criar planos de recuperação para orquestrar a ativação pós-falha de várias máquinas.
- A falha é em duas partes:
    - Após a conclusão da primeira fase de failover, deverá ser capaz de ver os VMs de réplica criados em Azure. Pode atribuir um endereço IP público à VM, se necessário.
    - Em seguida, você comete o failover, para começar a aceder à carga de trabalho a partir da réplica Azure VM.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Como acedo aos VMs Azure após o fracasso?
Após falha, pode aceder a VMs Azure através de uma ligação à Internet segura, através de uma VPN site-to-site ou através do Azure ExpressRoute. Tens de preparar uma série de coisas para te ligares. [Saiba mais](failover-failback-overview.md#connect-to-azure-after-failover).

### <a name="is-failed-over-data-resilient"></a>Falhou em relação aos dados resilientes?
O Azure foi concebido para ser resiliente. A Recuperação do Local é concebida para falhas num centro de dados secundário do Azure, de acordo com o Azure SLA. Quando ocorrer uma falha, certificamo-nos de que os seus metadados e cofres permanecem na mesma região geográfica que escolheu para o seu cofre.

### <a name="is-failover-automatic"></a>A ativação pós-falha é automática?
[Falhar](site-recovery-failover.md) não é automático. Inicia falhas com um único clique no portal, ou pode utilizar o [PowerShell](/powershell/module/az.recoveryservices) para desencadear uma falha.

### <a name="how-do-i-fail-back"></a>Como é que eu recuo?

Depois de a sua infraestrutura no local estar a funcionar novamente, pode falhar. O recuo ocorre em três fases:

1. Inicia-se uma falha planeada de Azure para o local do local, utilizando algumas opções diferentes:

    - Minimizar o tempo de inatividade: Se utilizar esta opção, a Recuperação do Site sincroniza os dados antes do failover. Verifica os blocos de dados alterados e transfere-os para o local, enquanto o VM Azure continua a funcionar, minimizando o tempo de inatividade. Quando especificar manualmente que a falha deve ser concluída, o VM Azure é desligado, quaisquer alterações delta finais são copiadas e a falha começa.
    - Download completo: Com esta opção os dados são sincronizados durante o failover. Esta opção descarrega todo o disco. É mais rápido porque não são calculadas as contas, mas há mais tempo de inatividade. Utilize esta opção se estiver a executar a réplica Azure VMs há algum tempo, ou se o VM no local foi eliminado.

2. Pode selecionar para voltar a falhar no mesmo VM ou num VM alternativo. Pode especificar que a Recuperação do Site deve criar o VM se já não existir.
3. Após o final da sincronização inicial, selecione para completar o failover. Depois de concluído, pode entrar no VM no local para verificar se está tudo funcionando como esperado. No portal Azure, pode ver que os VMs do Azure foram parados.
4. Compromete-se a terminar e começa a aceder à carga de trabalho a partir do VM no local novamente.
5. Depois de as cargas de trabalho terem falhado, permite a replicação inversa, de modo a que os VMs no local se reproduzam novamente para Azure.

### <a name="can-i-fail-back-to-a-different-location"></a>Posso voltar a um local diferente?
Sim, se falhaste no Azure, podes voltar a um local diferente se o original não estiver disponível. [Saiba mais](hyper-v-azure-failback.md#fail-back-to-an-alternate-location).
