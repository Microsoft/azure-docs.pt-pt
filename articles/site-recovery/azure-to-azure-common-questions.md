---
title: Perguntas comuns sobre a recuperação de desastres de VM do Azure com o Azure Site Recovery
description: Este artigo responde a perguntas comuns sobre a recuperação de desastres de VM do Azure usando Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 8ed5df15b8ae8e9836c5b8ac8e7d6ad0111f63e1
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941902"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Perguntas comuns: recuperação de desastre do Azure para o Azure

Este artigo fornece respostas a perguntas comuns sobre a recuperação de desastre de VMs do Azure para outra região do Azure usando [site Recovery](site-recovery-overview.md). 


## <a name="general"></a>Geral

### <a name="how-is-site-recovery-priced"></a>Como o preço é Site Recovery?
Examine [Azure site Recovery](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) detalhes de preços.
### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Como funciona o escalão gratuito do Azure Site Recovery?
Cada uma das instâncias que está protegida pelo Azure Site Recovery é gratuíta nos primeiros 31 dias de proteção. A partir do 32.º dia, a proteção para a instância é cobrada aos preços acima.
### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante os primeiros 31 dias, terei quaisquer outros custos com o Azure?
Sim, embora o Azure Site Recovery seja gratuito durante os primeiros 31 dias de uma instância protegida, pode ter custos para o Armazenamento do Azure, transações de armazenamento e transferência de dados. Uma máquina virtual recuperada também pode ter custos de computação do Azure. Obtenha detalhes completos sobre os preços [aqui](https://azure.microsoft.com/pricing/details/site-recovery)

### <a name="where-can-i-find-best-practices-for-azure-vm-disaster-recovery"></a>Onde posso encontrar as práticas recomendadas para a recuperação de desastres de VM do Azure? 
1. [Entender a arquitetura do Azure para o Azure](azure-to-azure-architecture.md)
2. [Examine as configurações com suporte e sem suporte](azure-to-azure-support-matrix.md)
3. [Configurar a recuperação de desastre para VMs do Azure](azure-to-azure-how-to-enable-replication.md)
4. [Executar uma ativação pós-falha de teste](azure-to-azure-tutorial-dr-drill.md)
5. [Fazer failover e failback para a região primária](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-guaranteed-in-the-target-region"></a>Como a capacidade é garantida na região de destino?
A equipe de Site Recovery trabalha com a equipe de gerenciamento de capacidade do Azure para planejar a capacidade de infraestrutura suficiente e para ajudar a garantir que as VMs protegidas pelo Site Recovery para serão implantadas com êxito na região de destino quando o failover for iniciado.

## <a name="replication"></a>Replicação

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Posso replicar VMs habilitadas por meio da criptografia de disco do Azure?

Sim, Site Recovery dá suporte à recuperação de desastre de VMs com o Azure Disk Encryption (ADE) habilitado. Quando você habilita a replicação, todas as chaves de criptografia de disco e os segredos necessários são copiados da região de origem para a região de destino no contexto do usuário. Se você não tiver a permissão apropriada, um script pronto para uso poderá ser enviado ao administrador de segurança para copiar as chaves e os segredos.

- O Site Recovery dá suporte a ADE para VMs do Azure que executam o Windows.
- O Site Recovery oferece suporte à versão 0,1 do ADE, com um esquema usando o AAD (Azure Active Directory) e a versão 1,1, sem o AAD. [Saiba mais](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schemata).
- ADE versão 1,1, as VMs do Windows devem ser usadas em discos gerenciados.
- [Saiba mais](azure-to-azure-how-to-enable-replication-ade-vms.md) sobre como habilitar a replicação para VMs criptografadas.



### <a name="can-i-replicate-vms-to-another-subscription"></a>Posso replicar VMs para outra assinatura?
Sim, você pode replicar VMs do Azure para uma assinatura diferente dentro do mesmo locatário do Azure AD.
A configuração de DR [entre assinaturas](https://azure.microsoft.com/blog/cross-subscription-dr) é simples. Você pode selecionar outra assinatura no momento da replicação.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Posso replicar VMs do Azure fixadas por zona para outra região?
Sim, você pode [replicar VMs fixadas pela zona](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) para outra região.

### <a name="can-i-exclude-disks"></a>Posso excluir discos?

Sim, você pode excluir discos no momento da proteção usando o PowerShell. Para obter mais informações, consulte o [artigo](azure-to-azure-exclude-disks.md)

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Posso adicionar novos discos a VMs replicadas e habilitar a replicação para eles?

Sim, há suporte para as VMs do Azure com discos gerenciados. Quando você adiciona um novo disco a uma VM do Azure que está habilitada para replicação, a integridade da replicação para a VM mostra um aviso, com uma observação especificando que um ou mais discos na VM estão disponíveis para proteção. Você pode habilitar a replicação para discos adicionados.
- Se você habilitar a proteção para os discos adicionados, o aviso desaparecerá após a replicação inicial.
- Se optar por não habilitar a replicação para o disco, você poderá optar por ignorar o aviso.
- Quando você faz failover de uma VM para a qual adiciona um disco e habilita a replicação para ele, os pontos de replicação mostrarão os discos que estão disponíveis para recuperação. Por exemplo, se uma VM tiver um único disco e você adicionar um novo, os pontos de replicação criados antes de adicionar o disco mostrarão que o ponto de replicação consiste em "1 de 2 discos".

Site Recovery não dá suporte a "remoção a quente" de um disco de uma VM replicada. Se você remover um disco de VM, será necessário desabilitar e reabilitar a replicação para a VM.


### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência eu posso replicar no Azure?
A replicação é contínua quando você está replicando VMs do Azure para outra região do Azure. Para obter mais informações, consulte a [arquitetura de replicação do Azure para o Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-to-migrate-vms"></a>Posso replicar máquinas virtuais em uma região? Preciso disso para migrar as VMs.
Você não pode usar uma solução de recuperação de desastre do Azure para o Azure para replicar VMs em uma região.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>Posso replicar VMs para qualquer região do Azure?
Com o Site Recovery, você pode replicar e recuperar VMs entre duas regiões no mesmo cluster geográfico. Os clusters geográficos são definidos com a latência de dados e a soberania em mente. Para obter mais informações, consulte a [matriz de suporte](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)do site Recovery Region.

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery requer conectividade com a Internet?

Não, Site Recovery não requer conectividade com a Internet. Mas ele requer acesso a Site Recovery URLs e intervalos de IP, conforme mencionado neste [artigo](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

### <a name="can-i-replicate-the-application-having-separate-resource-group-for-separate-tiers"></a>Posso replicar o aplicativo que tem um grupo de recursos separado para camadas separadas?
Sim, você pode replicar o aplicativo e manter a configuração de recuperação de desastre em um grupo de recursos separado também.
Por exemplo, se você tiver um aplicativo com cada aplicativo de camadas, BD e Web em um grupo de recursos separado, você precisará clicar no [Assistente de replicação](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) três vezes por para proteger todas as camadas. Site Recovery replicará essas três camadas em três grupos de recursos diferentes.

## <a name="replication-policy"></a>Política de replicação

### <a name="what-is-a-replication-policy"></a>O que é uma política de replicação?
Ele define as configurações para o histórico de retenção de pontos de recuperação e a frequência de instantâneos consistentes com o aplicativo. Por padrão, Azure Site Recovery cria uma nova política de replicação com as configurações padrão de:

* 24 horas para o histórico de retenção de pontos de recuperação.
* 60 minutos para a frequência de instantâneos consistentes com o aplicativo.

[Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>O que é um ponto de recuperação consistente com falhas?
Um ponto de recuperação consistente com falhas representa os dados em disco como se a VM falhasse ou o cabo de alimentação foi puxado do servidor no momento em que o instantâneo foi tirado. Ele não inclui nada que estava na memória quando o instantâneo foi tirado.

Hoje, a maioria dos aplicativos pode se recuperar bem de instantâneos consistentes com falhas. Um ponto de recuperação consistente com falhas geralmente é suficiente para sistemas operacionais sem banco de dados e aplicativos como servidores de arquivos, servidores DHCP e servidores de impressão.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Qual é a frequência da geração de ponto de recuperação consistente com falhas?
Site Recovery cria um ponto de recuperação consistente com falhas a cada 5 minutos.

### <a name="what-is-an-application-consistent-recovery-point"></a>O que é um ponto de recuperação consistente com o aplicativo?
Os pontos de recuperação consistentes com o aplicativo são criados a partir de instantâneos consistentes com o aplicativo. Os pontos de recuperação consistentes com o aplicativo capturam os mesmos dados que os instantâneos consistentes com falhas, com a adição de todos os dados na memória e todas as transações em andamento.
Devido a seu conteúdo extra, os instantâneos consistentes com o aplicativo são os mais envolvidos e levam mais tempo para serem executados. Recomendamos pontos de recuperação consistentes com o aplicativo para sistemas operacionais de banco de dados e aplicativos como SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Qual é o impacto dos pontos de recuperação consistentes com o aplicativo sobre o desempenho do aplicativo?
Considerar que pontos de recuperação consistentes com o aplicativo captura todos os dados na memória e, em processo, requer que a estrutura como o VSS no Windows feche o aplicativo. Isso, se feito com muita frequência, poderá afetar o desempenho se a carga de trabalho já estiver muito ocupada. Geralmente, é recomendável não usar baixa frequência para pontos de recuperação consistentes com o aplicativo para cargas de trabalho que não sejam de banco de dados e até mesmo para a carga de trabalho de banco de dados de 1 hora é suficiente

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Qual é a frequência mínima da geração de ponto de recuperação consistente com o aplicativo?
Site Recovery pode criar um ponto de recuperação consistente com o aplicativo com uma frequência mínima de, em 1 hora.

### <a name="how-are-recovery-points-generated-and-saved"></a>Como os pontos de recuperação são gerados e salvos?
Para entender como Site Recovery gera pontos de recuperação, vamos pegar um exemplo de uma política de replicação que tem uma janela de retenção de ponto de recuperação de 24 horas e um instantâneo de frequência consistente com o aplicativo de 1 hora.

Site Recovery cria um ponto de recuperação consistente com falhas a cada 5 minutos. O usuário não pode alterar essa frequência. Portanto, para as últimas 1 hora, o usuário terá 12 pontos consistentes com falhas e 1 ponto consistente com o aplicativo para escolher. À medida que o tempo progride, Site Recovery remove todos os pontos de recuperação além das últimas 1 hora e salva apenas 1 ponto de recuperação por hora.

A captura de tela a seguir ilustra o exemplo. Na captura de tela:

1. Para um tempo menor do que a última hora, há pontos de recuperação com uma frequência de 5 minutos.
2. Para um período posterior à última hora, Site Recovery mantém apenas um ponto de recuperação.

   ![Lista de pontos de recuperação gerados](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Até que distância posso recuperar?
O ponto de recuperação mais antigo que você pode usar é de 72 horas.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>O que acontecerá se eu tiver uma política de replicação de 24 horas e um problema impedir Site Recovery de gerar pontos de recuperação por mais de 24 horas? Meus pontos de recuperação anteriores serão perdidos?
Não, Site Recovery manterá todos os pontos de recuperação anteriores. Dependendo da janela de retenção dos pontos de recuperação, 24 horas nesse caso, Site Recovery substituirá o ponto mais antigo somente se houver uma geração de novos pontos. Nesse caso, como não haverá nenhum novo ponto de recuperação gerado devido a algum problema, todos os pontos antigos permanecerão intactos quando chegarmos à janela de retenção.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Depois que a replicação é habilitada em uma VM, como altero a política de replicação?
Vá para **site Recovery cofre** > **site Recovery infraestrutura** > **políticas de replicação**. Selecione a política que você deseja editar e salve as alterações. Qualquer alteração também será aplicada a todas as replicações existentes.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Todos os pontos de recuperação são uma cópia completa da VM ou um diferencial?
O primeiro ponto de recuperação gerado tem a cópia completa. Quaisquer pontos de recuperação sucessivos têm alterações delta.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Aumentar o período de retenção dos pontos de recuperação aumenta o custo de armazenamento?
Sim. Se você aumentar o período de retenção de 24 horas para 72 horas, Site Recovery salvará os pontos de recuperação por um adicional de 48 horas. O tempo adicionado incorrerá em encargos de armazenamento. Por exemplo, se um único ponto de recuperação tiver alterações delta de 10 GB e o custo por GB for de $0.16 por mês, os encargos adicionais serão $1.06 * 48 por mês.

## <a name="multi-vm-consistency"></a>Consistência de várias VMs

### <a name="what-is-multi-vm-consistency"></a>O que é consistência de várias VMs?
Isso significa garantir que o ponto de recuperação seja consistente em todas as máquinas virtuais replicadas.
Site Recovery fornece uma opção de "consistência de várias VMs", que, quando você a seleciona, cria um grupo de replicação para replicar todos os computadores que fazem parte do grupo.
Todas as máquinas virtuais terão pontos de recuperação consistentes com o aplicativo e com falhas compartilhadas quando forem efetuadas failover.
Siga o tutorial para [habilitar a consistência de várias VMs](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm).

### <a name="can-i-failover-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Posso fazer failover de uma única máquina virtual em um grupo de replicação de consistência de várias VMs?
Ao selecionar a opção "consistência de várias VMs", você está informando que o aplicativo tem uma dependência em todas as máquinas virtuais dentro de um grupo. Portanto, o failover de máquina virtual única não é permitido.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Quantas máquinas virtuais posso replicar como parte de um grupo de replicação de consistência de várias VMs?
Você pode replicar 16 máquinas virtuais juntas em um grupo de replicação.

### <a name="when-should-i-enable-multi-vm-consistency-"></a>Quando devo habilitar a consistência de várias VMs?
Como a CPU é intensiva, a habilitação da consistência de várias VMs pode afetar o desempenho da carga de trabalho. Ele deve ser usado somente se os computadores estiverem executando a mesma carga de trabalho e você precisar de consistência em vários computadores. Por exemplo, se você tiver duas instâncias de SQL Server e dois servidores Web em um aplicativo, deverá ter consistência de várias VMs somente para as instâncias de SQL Server.


## <a name="failover"></a>Ativação Pós-Falha

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Como a capacidade é garantida na região de destino para VMs do Azure?
A equipe de Site Recovery trabalha com a equipe de gerenciamento de capacidade do Azure para planejar a capacidade de infraestrutura suficiente, para ajudar a garantir que as VMs habilitadas para a recuperação de desastre serão implantadas com êxito na região de destino quando o failover for iniciado.

### <a name="is-failover-automatic"></a>A ativação pós-falha é automática?

A ativação pós-falha não é automática. Você inicia failovers com um único clique no portal ou pode usar o [PowerShell](azure-to-azure-powershell.md) para disparar um failover.

### <a name="can-i-retain-a-public-ip-address-after-failover"></a>Posso reter um endereço IP público após o failover?

O endereço IP público do aplicativo de produção não pode ser retido após o failover.
- As cargas de trabalho trazidas como parte do processo de failover devem ser atribuídas a um recurso de IP público do Azure que está disponível na região de destino.
- Você pode fazer isso manualmente ou automatizá-lo com um plano de recuperação.
- Saiba como [configurar endereços IP públicos após o failover](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).  

### <a name="can-i-retain-a-private-ip-address-during-failover"></a>Posso reter um endereço IP privado durante o failover?
Sim, você pode manter um endereço IP privado. Por padrão, quando você habilita a recuperação de desastre para VMs do Azure, Site Recovery cria recursos de destino com base nas configurações de recurso de origem. -Para VMs do Azure configuradas com endereços IP estáticos, Site Recovery tenta provisionar o mesmo endereço IP para a VM de destino, se não estiver em uso.
Saiba mais sobre a [retenção de endereços IP durante o failover](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-failover-why-is-the-server-assigned-a-new-ip-address"></a>Após o failover, por que o servidor atribuiu um novo endereço IP?

Site Recovery tenta fornecer o endereço IP no momento do failover. Se outra máquina virtual estiver levando esse endereço, Site Recovery definirá o próximo endereço IP disponível como o destino.
Saiba mais sobre como [Configurar o mapeamento de rede e o endereçamento IP para VNets](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Quais são os pontos **de recuperação mais recentes (RPO mais baixo)** ?
A opção **mais recente (RPO mais baixo)** primeiro processa todos os dados que foram enviados para o serviço de site Recovery, para criar um ponto de recuperação para cada VM antes de fazer failover para ele. Essa opção fornece o RPO (objetivo de ponto de recuperação) mais baixo, pois a VM criada após o failover tem todos os dados replicados para Site Recovery quando o failover foi disparado.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Os pontos de recuperação **mais recentes (RPO mais baixo)** têm um impacto no RTO de failover?
Sim. O Site Recovery processa todos os dados pendentes antes do failover; portanto, essa opção tem um RTO (objetivo de tempo de recuperação) maior em comparação com outras opções.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>O que significa a opção **mais recente processada** nos pontos de recuperação?
A **última opção processada** faz failover em todas as VMs no plano para o último ponto de recuperação que site Recovery processado. Para ver o ponto de recuperação mais recente de uma VM específica, verifique os **pontos de recuperação mais recentes** nas configurações da VM. Essa opção fornece um RTO baixo, porque nenhum tempo é gasto no processamento de dados não processados.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>O que acontece se minha região primária apresentar uma interrupção inesperada?
Você pode disparar um failover após a interrupção. Site Recovery não precisa de conectividade da região primária para executar o failover.

### <a name="what-is-a-rto-of-a-vm-failover-"></a>O que é um RTO de um failover de VM?
Site Recovery tem um [SLA de RTO de 2 horas](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). No entanto, na maioria das vezes, Site Recovery fazer failover de máquinas virtuais em minutos. Você pode calcular o RTO acessando os trabalhos de failover que mostram o tempo necessário para abrir a VM. Para o RTO do plano de recuperação, consulte a seção abaixo.

## <a name="recovery-plans"></a>Planos de recuperação

### <a name="what-is-a-recovery-plan"></a>O que é um plano de recuperação?
Um plano de recuperação no Site Recovery orquestra a recuperação de failover de VMs. Ele ajuda a tornar a recuperação consistente, Reproduzível e automatizada. Um plano de recuperação atende às seguintes necessidades para o usuário:

- Definindo um grupo de máquinas virtuais que fazem failover juntas
- Definindo as dependências entre máquinas virtuais para que o aplicativo seja exibido com precisão
- Automatizar a recuperação junto com ações manuais personalizadas para realizar tarefas diferentes do failover de máquinas virtuais

[Saiba mais](site-recovery-create-recovery-plans.md) sobre os planos de recuperação.

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Como o sequenciamento é alcançado em um plano de recuperação?

Em um plano de recuperação, você pode criar vários grupos para obter o sequenciamento. Cada grupo faz failover ao mesmo tempo. As VMs que fazem parte do mesmo grupo fazem failover juntas, seguidas por outro grupo. Para saber como modelar um aplicativo usando um plano de recuperação, consulte [sobre planos de recuperação](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Como posso encontrar o RTO de um plano de recuperação?
Para verificar o RTO de um plano de recuperação, faça um failover de teste para o plano de recuperação e vá para **site Recovery trabalhos**.
No exemplo a seguir, o trabalho chamado SAPTestRecoveryPlan levou 8 minutos e 59 segundos para fazer failover de todas as máquinas virtuais e executar ações especificadas.

![Lista de trabalhos de Site Recovery](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Posso adicionar runbooks de automação ao plano de recuperação?
Sim, você pode integrar runbooks de automação do Azure em seu plano de recuperação. [Saiba mais](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Nova proteção e failback

### <a name="after-a-failover-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Após um failover da região primária para uma região de recuperação de desastre, as VMs em uma região de DR são protegidas automaticamente?
Não. Quando você [faz failover de](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) VMs do Azure de uma região para outra, as VMs são iniciadas na região de recuperação de desastres em um estado desprotegido. Para fazer failback das VMs para a região primária, você precisa [proteger](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) novamente as VMs na região secundária.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>No momento da nova proteção, o Site Recovery replicar dados completos da região secundária para a região primária?
Depende da situação. Por exemplo, se a VM da região de origem existir, somente as alterações entre o disco de origem e o disco de destino serão sincronizadas. Site Recovery computa os diferenciais comparando os discos e, em seguida, transfere os dados. Esse processo geralmente leva algumas horas. Para obter mais informações sobre o que acontece durante a nova proteção, consulte [proteger novamente as VMs do Azure com failover para a região primária]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Quanto tempo demora para fazer failback?
Após a nova proteção, a quantidade de tempo para o failback geralmente é semelhante ao tempo necessário para o failover da região primária para uma região secundária.

## <a name="capacity"></a>Recurso

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Como a capacidade é garantida na região de destino para VMs do Azure?
A equipe de Site Recovery trabalha com a equipe de gerenciamento de capacidade do Azure para planejar a capacidade de infraestrutura suficiente, para ajudar a garantir que as VMs habilitadas para recuperação de desastres serão implantadas com êxito na região de destino quando o failover for iniciado.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery funciona com instâncias reservadas?
Sim, você pode comprar [instâncias de reserva](https://azure.microsoft.com/pricing/reserved-vm-instances/) na região de recuperação de desastre e site Recovery operações de failover as usarão. </br> Não é necessária nenhuma configuração adicional.


## <a name="security"></a>Segurança

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Os dados de replicação são enviados para o serviço de Recuperação de Sites?
Não, Site Recovery não intercepta dados replicados e não tem nenhuma informação sobre o que está sendo executado em suas VMs. Apenas os metadados necessários para orquestrar a replicação e a ativação pós-falha são enviados para o serviço de Recuperação de Sites.  
Site Recovery é ISO 27001:2013, 27018, HIPAA, DPA certificado e está no processo de avaliações SOC2 e FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>A Recuperação de Sites faz encriptação de replicação?
Sim, há suporte para criptografia em trânsito e [criptografia em repouso no Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) .

## <a name="next-steps"></a>Passos seguintes
* [Examine](azure-to-azure-support-matrix.md) os requisitos de suporte.
* [Configurar](azure-to-azure-tutorial-enable-replication.md) Replicação do Azure para o Azure.
- Se você tiver dúvidas depois de ler este artigo, poste-os no [Fórum dos serviços de recuperação do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).
