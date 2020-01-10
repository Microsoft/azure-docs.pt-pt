---
title: Perguntas comuns sobre a recuperação de desastres do Hyper-V com o Azure Site Recovery
description: Este artigo resume as perguntas comuns sobre como configurar a recuperação de desastre para VMs do Hyper-V locais para o Azure usando o site Azure Site Recovery.
ms.date: 11/12/2019
ms.topic: conceptual
ms.openlocfilehash: 7c5f55fbea67567ddf7a2afa6a61f6c76568d829
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75498202"
---
# <a name="common-questions---hyper-v-to-azure-disaster-recovery"></a>Perguntas comuns – Hyper-V para a recuperação de desastre do Azure

Este artigo fornece respostas a perguntas comuns que vemos ao replicar VMs do Hyper-V locais para o Azure. 

## <a name="general"></a>Geral

### <a name="how-is-site-recovery-priced"></a>Como o preço é Site Recovery?
Examine [Azure site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) detalhes de preços.

### <a name="how-do-i-pay-for-azure-vms"></a>Como fazer pagar pelas VMs do Azure?
Durante a replicação, os dados são replicados para o armazenamento do Azure e você não paga nenhuma alteração de VM. Quando você executa um failover no Azure, Site Recovery cria automaticamente máquinas virtuais IaaS do Azure. Depois disso, você será cobrado pelos recursos de computação que você consome no Azure.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Há alguma diferença no custo ao replicar para Uso Geral conta de armazenamento v2?

Normalmente, você verá um aumento no custo das transações incorridos nas contas de armazenamento GPv2, uma vez que Azure Site Recovery tem transações pesadas. [Leia mais](../storage/common/storage-account-upgrade.md#pricing-and-billing) para estimar a alteração.

## <a name="azure"></a>Azure

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>O que é necessário no Hyper-V para orquestrar a replicação com Site Recovery?

Para o servidor de anfitrião Hyper-V, o que precisa depende do cenário de implementação. Confira os pré-requisitos do Hyper-V para:

* [Replicando VMs do Hyper-V (sem VMM) para o Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replicando VMs do Hyper-V (com VMM) para o Azure](site-recovery-vmm-to-azure.md)
* [Replicando VMs do Hyper-V para um datacenter secundário](site-recovery-vmm-to-vmm.md)
* Se você estiver replicando para um datacenter secundário, leia sobre os [sistemas operacionais convidados com suporte para VMs do Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
* Se você estiver replicando para o Azure, o Site Recovery dará suporte a todos os sistemas operacionais convidados [com suporte do Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Posso proteger VMs quando o Hyper-V está em execução em um sistema operacional cliente?
Não, as VMs têm de estar localizadas num servidor de anfitrião Hyper-V que está a ser executado na máquina do servidor Windows suportada. Se você precisar proteger um computador cliente, poderá replicá-lo como um computador físico para o [Azure](site-recovery-vmware-to-azure.md) ou um [datacenter secundário](site-recovery-vmware-to-vmware.md).

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Os hosts do Hyper-V precisam estar em nuvens do VMM?
Se você quiser replicar para um datacenter secundário, as VMs do Hyper-V devem estar em servidores hosts Hyper-V localizados em uma nuvem do VMM. Se você quiser replicar para o Azure, poderá replicar VMs com ou sem nuvens do VMM. [Leia mais](tutorial-hyper-v-to-azure.md) sobre a replicação do Hyper-V para o Azure.


### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Pode replicar máquinas virtuais de 2.ª geração do Hyper-V para o Azure?
Sim. Site Recovery converte da geração 2 para a geração 1 durante o failover. No failback, o computador é convertido de volta para a geração 2. [Leia mais](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).


### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Posso implementar a Recuperação de Sites com o VMM se tiver apenas um servidor VMM?

Sim. Você pode replicar VMs em servidores Hyper-V na nuvem do VMM para o Azure ou pode replicar entre nuvens do VMM no mesmo servidor. Para replicação de local para local, recomendamos que você tenha um servidor VMM nos sites primário e secundário. 

### <a name="what-do-i-need-in-azure"></a>O que é necessário no Azure?
Você precisa de uma assinatura do Azure, um cofre dos serviços de recuperação, uma conta de armazenamento e uma rede virtual. O cofre, a conta de armazenamento e a rede devem estar na mesma região.

### <a name="what-azure-storage-account-do-i-need"></a>De que conta de armazenamento do Azure eu preciso?
Você precisa de uma conta de armazenamento LRS ou GRS. Recomendamos GRS para que os dados sejam resilientes se ocorrer uma falha regional ou se a região primária não pode ser recuperada. Há suporte para o armazenamento Premium.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Minha conta do Azure precisa de permissões para criar VMs?
Se você for um administrador de assinatura, terá as permissões de replicação de que precisa. Se você não estiver, precisará de permissões para criar uma VM do Azure no grupo de recursos e na rede virtual que você especificar ao configurar Site Recovery e permissões para gravar na conta de armazenamento selecionada. [Saiba mais](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="is-replication-data-sent-to-site-recovery"></a>Os dados de replicação são enviados para Site Recovery?
Não, Site Recovery não intercepta dados replicados e não tem nenhuma informação sobre o que está sendo executado em suas VMs. Os dados de replicação são trocados entre os hosts Hyper-V e o armazenamento do Azure. A Recuperação de Sites não tem capacidade para intercetar esses dados. Apenas os metadados necessários para orquestrar a replicação e a ativação pós-falha são enviados para o serviço de Recuperação de Sites.  

Site Recovery é ISO 27001:2013, 27018, HIPAA, DPA certificado e está no processo de avaliações SOC2 e FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-region"></a>Podemos manter os metadados locais em uma região geográfica?
Sim. Quando você cria um cofre em uma região, garantimos que todos os metadados usados pelo Site Recovery permaneçam dentro do limite geográfico da região.

### <a name="does-site-recovery-encrypt-replication"></a>A Recuperação de Sites faz encriptação de replicação?
Sim, há suporte para criptografia em trânsito e [criptografia no Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) .


## <a name="deployment"></a>Implementação

### <a name="what-can-i-do-with-hyper-v-to-azure-replication"></a>O que posso fazer com o Hyper-V para replicação do Azure?

- **Recuperação de desastre**: você pode configurar a recuperação de desastre completa. Nesse cenário, você Replica VMs Hyper-V locais para o armazenamento do Azure:
    - Você pode replicar VMs para o Azure. Se sua infraestrutura local não estiver disponível, faça o failover para o Azure.
    - Quando você faz failover, as VMs do Azure são criadas usando os dados replicados. Você pode acessar aplicativos e cargas de trabalho nas VMs do Azure.
    - Quando o datacenter local estiver disponível novamente, você poderá fazer failback do Azure para seu site local.
- **Migração**: você pode usar site Recovery para migrar VMs do Hyper-V locais para o armazenamento do Azure. Em seguida, faça failover do local para o Azure. Após o failover, seus aplicativos e cargas de trabalho estão disponíveis e em execução em VMs do Azure.


### <a name="what-do-i-need-on-premises"></a>O que é necessário no local?

Você precisa de uma ou mais VMs em execução em um ou mais hosts Hyper-V autônomos ou clusterizados. Você também pode replicar VMs em execução em hosts gerenciados pelo System Center Virtual Machine Manager (VMM).
- Se você não estiver executando o VMM, durante a implantação do Site Recovery, você coletará hosts e clusters do Hyper-V em sites do Hyper-V. Instale os agentes de Site Recovery (provedor de Azure Site Recovery e agente de serviços de recuperação) em cada host do Hyper-V.
- Se os hosts Hyper-V estiverem localizados em uma nuvem do VMM, você orquestrará a replicação no VMM. Você instala o provedor de Site Recovery no servidor do VMM e o agente dos serviços de recuperação em cada host do Hyper-V. Você mapeia entre redes lógicas/VM do VMM e VNets do Azure.
- [Saiba mais](hyper-v-azure-architecture.md) sobre a arquitetura do Hyper-V para o Azure.

### <a name="can-i-replicate-vms-located-on-a-hyper-v-cluster"></a>Posso replicar VMs localizadas em um cluster Hyper-V?

Sim, Site Recovery dá suporte a hosts Hyper-V clusterizados. Tenha em atenção que:

- Todos os nós do cluster devem ser registrados no mesmo cofre.
- Se você não estiver usando o VMM, todos os hosts Hyper-V no cluster deverão ser adicionados ao mesmo site do Hyper-V.
- Instale o provedor de Azure Site Recovery e o agente de serviços de recuperação em cada host Hyper-V no cluster e adicione cada host a um site do Hyper-V.
- Não é necessário realizar etapas específicas no cluster.
- Se você executar a ferramenta de Planejador de Implantações para Hyper-V, a ferramenta coletará os dados de perfil do nó que está em execução e onde a VM está em execução. A ferramenta não pode coletar dados de um nó desativado, mas ele acompanhará esse nó. Depois que o nó estiver em execução, a ferramenta começará a coletar os dados do perfil da VM dele (se a VM fizer parte da lista de VMs do perfil e estiver em execução no nó).
- Se uma VM em um host Hyper-V em um cofre de Site Recovery migrar para um host Hyper-V diferente no mesmo cluster ou para um host autônomo, a replicação para a VM não será afetada. O host Hyper-V deve atender aos [pré-requisitos](hyper-v-azure-support-matrix.md#on-premises-servers)e ser configurado em um cofre site Recovery. 


### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Posso proteger VMs quando o Hyper-V está em execução em um sistema operacional cliente?
Não, as VMs têm de estar localizadas num servidor de anfitrião Hyper-V que está a ser executado na máquina do servidor Windows suportada. Se você precisar proteger um computador cliente, poderá [replicá-lo como um computador físico para o](physical-azure-disaster-recovery.md) Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Pode replicar máquinas virtuais de 2.ª geração do Hyper-V para o Azure?
Sim. Site Recovery converte da geração 2 para a geração 1 durante o failover. No failback, o computador é convertido de volta para a geração 2.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Posso automatizar cenários de Site Recovery com um SDK?
Sim. Pode automatizar fluxos de trabalho da Recuperação de Sites utilizando a API REST, PowerShell ou o SDK do Azure. Cenários com suporte no momento para replicar o Hyper-V para o Azure usando o PowerShell:

- [Replicar o Hyper-V sem o VMM usando o PowerShell](hyper-v-azure-powershell-resource-manager.md)
- [Replicando o Hyper-V com o VMM usando o PowerShell](hyper-v-vmm-powershell-resource-manager.md)

## <a name="replication"></a>Replicação

### <a name="where-do-on-premises-vms-replicate-to"></a>Onde as VMs locais são replicadas para?
Os dados são replicados para o armazenamento do Azure. Quando você executa um failover, Site Recovery cria automaticamente VMs do Azure da conta de armazenamento.

### <a name="what-apps-can-i-replicate"></a>Quais aplicativos posso replicar?
Você pode replicar qualquer aplicativo ou carga de trabalho que esteja executando uma VM Hyper-V que esteja em conformidade com [os requisitos de replicação](hyper-v-azure-support-matrix.md#replicated-vms). Site Recovery fornece suporte para replicação com reconhecimento de aplicativo, para que os aplicativos possam fazer failover e failback para um estado inteligente. O Site Recovery integra-se com aplicativos da Microsoft, como o SharePoint, o Exchange, o Dynamics, o SQL Server e o Active Directory, e trabalha junto com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre proteção de carga de trabalho.

### <a name="whats-the-replication-process"></a>O que é o processo de replicação?

1. Quando a replicação inicial é disparada, um instantâneo de VM do Hyper-V é obtido.
2. Os discos rígidos virtuais na VM são replicados um a um, até que eles sejam copiados para o Azure. Isso pode demorar um pouco, dependendo do tamanho da VM e da largura de banda da rede. Saiba como aumentar a largura de banda da rede.
3. Se ocorrerem alterações no disco enquanto a replicação inicial estiver em andamento, o controlador de replicação de réplica do Hyper-V acompanhará as alterações como logs de replicação do Hyper-V (. HRL). Esses arquivos de log estão localizados na mesma pasta que os discos. Cada disco tem um arquivo. HRL associado que é enviado para o armazenamento secundário. Os ficheiros de instantâneo e de registo consomem recursos do disco quando a replicação inicial está em curso.
4. Quando a replicação inicial for concluída, o instantâneo da VM é eliminado.
5. Todas as alterações de disco no log são sincronizadas e mescladas ao disco pai.
6. Após a conclusão da replicação inicial, o trabalho finalizar a proteção na máquina virtual é executado. Ele configura a rede e outras configurações de pós-replicação, para que a VM seja protegida.
7. Neste estágio, você pode verificar as configurações da VM para certificar-se de que ela está pronta para failover. Você pode executar uma análise de recuperação de desastre (failover de teste) para a VM, para verificar se ela faz failover conforme o esperado.
8. Após a replicação inicial, a replicação delta começa, de acordo com a política de replicação.
9. As alterações são arquivos. HRL registrados. Cada disco que está configurado para replicação tem um ficheiro .hrl associado.
10. O log é enviado para a conta de armazenamento do cliente. Quando um log está em trânsito para o Azure, as alterações no disco primário são rastreadas em outro arquivo de log, na mesma pasta.
11. Durante a replicação inicial e Delta, você pode monitorar a VM no portal do Azure.

[Saiba mais](hyper-v-azure-architecture.md#replication-process) sobre o processo de replicação.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Posso replicar para o Azure com uma VPN site a site?

Site Recovery replica dados do local para o armazenamento do Azure por meio de um ponto de extremidade público ou usando o emparelhamento da Microsoft do ExpressRoute. Não há suporte para replicação em uma rede VPN site a site.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Posso replicar para o Azure com o ExpressRoute?

Sim, o ExpressRoute pode ser usado para replicar VMs no Azure. Site Recovery replica dados para uma conta de armazenamento do Azure em um ponto de extremidade público e você precisa configurar [o emparelhamento da Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) para site Recovery replicação. Após o failover das VMs para uma rede virtual do Azure, você pode acessá-las usando o [emparelhamento privado](../expressroute/expressroute-circuit-peerings.md#privatepeering).


### <a name="why-cant-i-replicate-over-vpn"></a>Por que não posso replicar pela VPN?

Quando você replica para o Azure, o tráfego de replicação atinge os pontos de extremidade públicos de uma conta de armazenamento do Azure. Assim, você só pode replicar pela Internet pública com o ExpressRoute (emparelhamento da Microsoft) e a VPN não funciona. 

### <a name="what-are-the-replicated-vm-requirements"></a>Quais são os requisitos de VM replicados?

Para a replicação, uma VM do Hyper-V deve estar executando um sistema operacional com suporte. Além disso, a VM deve atender aos requisitos para VMs do Azure. [Saiba mais](hyper-v-azure-support-matrix.md#replicated-vms) na matriz de suporte.

### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência eu posso replicar no Azure?

As VMs do Hyper-V podem ser replicadas a cada 30 segundos (exceto para armazenamento Premium), 5 minutos ou 15 minutos.

### <a name="can-i-extend-replication"></a>Posso estender a replicação?
Não é suportada a replicação expandida ou em cadeia. Solicite este recurso no [Fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>Posso fazer uma replicação inicial offline?
Tal não é suportado. Solicite esse recurso no [Fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Posso excluir discos?
Sim, você pode excluir discos da replicação. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Posso replicar VMs com discos dinâmicos?
Discos dinâmicos podem ser replicados. O disco do sistema operacional deve ser um disco básico.



## <a name="security"></a>Segurança

### <a name="what-access-does-site-recovery-need-to-hyper-v-hosts"></a>Que acesso Site Recovery precisa para hosts do Hyper-V

Site Recovery precisa acessar os hosts Hyper-V para replicar as VMs que você selecionar. Site Recovery instala o seguinte nos hosts Hyper-V:

- Se você não estiver executando o VMM, o provedor de Azure Site Recovery e o agente de serviços de recuperação serão instalados em cada host.
- Se você estiver executando o VMM, o agente dos serviços de recuperação será instalado em cada host. O provedor é executado no servidor do VMM.


### <a name="what-does-site-recovery-install-on-hyper-v-vms"></a>O que Site Recovery instalar em VMs do Hyper-V?

Site Recovery não instala explicitamente nada em VMs Hyper-V habilitadas para replicação.




## <a name="failover-and-failback"></a>Ativação pós-falha e reativação pós-falha


### <a name="how-do-i-fail-over-to-azure"></a>Como fazer fazer failover no Azure?

Você pode executar um failover planejado ou não planejado de VMs do Hyper-V locais para o Azure.

- Se executar uma ativação pós-falha planeada, as VMs de origem são desligadas para garantir que não há perda de dados.
- Você poderá executar um failover não planejado se o seu site primário não estiver acessível.
- Você pode fazer failover de um único computador ou criar planos de recuperação para orquestrar o failover de vários computadores.
- O failover está em duas partes:
    - Após a conclusão do primeiro estágio de failover, você poderá ver as VMs de réplica criadas no Azure. Pode atribuir um endereço IP público à VM, se necessário.
    - Em seguida, você confirma o failover para começar a acessar a carga de trabalho da VM do Azure de réplica.
   

### <a name="how-do-i-access-azure-vms-after-failover"></a>Como fazer acessar VMs do Azure após o failover?
Após o failover, você pode acessar as VMs do Azure por meio de uma conexão segura com a Internet, em uma VPN site a site ou no Azure ExpressRoute. Você precisará preparar várias coisas para se conectar. [Saiba mais](failover-failback-overview.md#connect-to-azure-after-failover).

### <a name="is-failed-over-data-resilient"></a>O failover de dados é resiliente?
O Azure foi concebido para ser resiliente. O Site Recovery é projetado para failover para um datacenter secundário do Azure, de acordo com o SLA do Azure. Quando ocorre o failover, verificamos se os seus metadados e cofres permanecem na mesma região geográfica que você escolheu para o cofre.

### <a name="is-failover-automatic"></a>A ativação pós-falha é automática?
O [failover](site-recovery-failover.md) não é automático. Você inicia failovers com um único clique no portal ou pode usar o [PowerShell](/powershell/module/az.recoveryservices) para disparar um failover.

### <a name="how-do-i-fail-back"></a>Como fazer failback?

Depois que sua infraestrutura local estiver funcionando novamente, você poderá fazer failback. O failback ocorre em três estágios:

1. Você começa um failover planejado do Azure para o site local usando duas opções diferentes:

    - Minimizar o tempo de inatividade: se você usar essa opção Site Recovery sincronizará os dados antes do failover. Ele verifica os blocos de dados alterados e os baixa no site local, enquanto a VM do Azure continua em execução, minimizando o tempo de inatividade. Quando você especifica manualmente que o failover deve ser concluído, a VM do Azure é desligada, todas as alterações delta finais são copiadas e o failover é iniciado.
    - Download completo: com esta opção, os dados são sincronizados durante o failover. Essa opção baixa todo o disco. É mais rápido porque nenhuma soma de verificação é calculada, mas há mais tempo de inatividade. Use esta opção se você estiver executando as VMs do Azure de réplica por algum tempo ou se a VM local foi excluída.

2. Você pode optar por fazer failback para a mesma VM ou para uma VM alternativa. Você pode especificar que Site Recovery deve criar a VM, caso ela ainda não exista.
3. Após a conclusão da sincronização inicial, selecione para concluir o failover. Após a conclusão, você pode entrar na VM local para verificar se tudo está funcionando conforme o esperado. No portal do Azure, você pode ver que as VMs do Azure foram interrompidas.
4. Você confirma o failover para concluir e começa a acessar a carga de trabalho da VM local novamente.
5. Após o failback das cargas de trabalho, você habilita a replicação inversa, para que as VMs locais sejam replicadas para o Azure novamente.

### <a name="can-i-fail-back-to-a-different-location"></a>Posso fazer failback para um local diferente?
Sim, se você fez failover no Azure, poderá fazer failback para um local diferente se o original não estiver disponível. [Saiba mais](hyper-v-azure-failback.md#fail-back-to-an-alternate-location).
