---
title: 'Perguntas mais comuns: Recuperação após desastre do Azure para o Azure com o Azure Site Recovery | Documentos da Microsoft'
description: Este artigo resume as perguntas mais comuns ao configurar a recuperação após desastre de VMs do Azure para outra região do Azure com o Azure Site Recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 03/18/2019
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: 2c1890570f153de68d187c37dc0a7bca156c2d47
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312058"
---
# <a name="common-questions-azure-to-azure-replication"></a>Perguntas mais comuns: Replicação do Azure para o Azure

Este artigo fornece respostas a perguntas comuns sobre a implementação de recuperação após desastre (DR) de VMs do Azure para outra região do Azure com o Azure Site Recovery. Se tiver questões depois de ler este artigo, publique o [fórum dos serviços de recuperação do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Geral

### <a name="how-is-site-recovery-priced"></a>Como é que o Site Recovery é cobrado?
Revisão [preços do Azure Site Recovery](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) detalhes.
### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Como funciona o escalão gratuito do Azure Site Recovery?
Todas as instâncias protegidas pelo Azure Site Recovery são gratuitas nos primeiros 31 dias de proteção. A partir do 32.º dia, a proteção para a instância é cobrada aos preços acima.
### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante os primeiros 31 dias, terei quaisquer outros custos com o Azure?
Sim, embora o Azure Site Recovery seja gratuito durante os primeiros 31 dias de uma instância protegida, pode incorrer em custos de Armazenamento do Azure, de transações de armazenamento e de transferência de dados. Uma máquina virtual recuperada também pode ter custos de computação do Azure. Obter detalhes completos sobre os preços [aqui](https://azure.microsoft.com/pricing/details/site-recovery)

### <a name="what-are-the-best-practices-for-configuring-site-recovery-on-azure-vms"></a>Quais são as melhores práticas para configuração da recuperação de Site em VMs do Azure?
1. [Compreender a arquitetura do Azure para o Azure](azure-to-azure-architecture.md)
2. [Reveja as configurações suportadas e não suportadas](azure-to-azure-support-matrix.md)
3. [Configurar a recuperação após desastre para VMs do Azure](azure-to-azure-how-to-enable-replication.md)
4. [Executar uma ativação pós-falha de teste](azure-to-azure-tutorial-dr-drill.md)
5. [Ativação pós-falha e reativação pós-falha para a região primária](azure-to-azure-tutorial-failover-failback.md)

## <a name="replication"></a>Replicação

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Pode replicar VMs ativadas através de encriptação de disco do Azure?
Sim, pode replicá-los. Consulte o artigo [ativada a encriptação de disco de Azure replicar máquinas virtuais para outra região do Azure](azure-to-azure-how-to-enable-replication-ade-vms.md). Atualmente, o Azure Site Recovery suporta apenas as VMs do Azure que executem um sistema operacional do Windows e ativado para a encriptação com aplicações do Azure Active Directory (Azure AD).

### <a name="can-i-replicate-vms-to-another-subscription"></a>Pode replicar VMs para outra subscrição?
Sim, pode replicar VMs do Azure para uma subscrição diferente no mesmo inquilino do Azure AD.
Configurar DR [entre subscrições](https://azure.microsoft.com/blog/cross-subscription-dr) é simples. Pode selecionar outra subscrição no momento da replicação.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Pode replicar VMs do Azure afixadas por zona para outra região?
Sim, pode [replicar VMs de afixadas por zona](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) para outra região.

### <a name="can-i-exclude-disks"></a>Pode excluir discos?

Sim, pode excluir discos no momento da proteção com o PowerShell. Para obter mais informações, consulte [artigo](azure-to-azure-exclude-disks.md)

### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência posso replicar para o Azure?
A replicação é contínua quando está a replicar VMs do Azure para outra região do Azure. Para obter mais informações, consulte a [arquitetura de replicação do Azure para o Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-to-migrate-vms"></a>Pode replicar máquinas virtuais dentro de uma região? Eu preciso, esta opção para migrar VMs.
Não é possível utilizar uma solução de DR do Azure para o Azure para replicar VMs dentro de uma região.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>Pode replicar VMs para qualquer região do Azure?
Com o Site Recovery, pode replicar e recuperar VMs entre quaisquer duas regiões dentro do mesmo cluster geográfico. Clusters geográficas são definidas com latência de dados e soberania de dados em mente. Para obter mais informações, consulte o Site Recovery [matriz de suporte de região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support).

### <a name="does-site-recovery-require-internet-connectivity"></a>Recuperação de sites necessitam de conectividade à internet?

Não, recuperação de sites não necessita de conectividade à internet. Mas requer acesso aos intervalos IP e URLs de recuperação de Site, conforme mencionado na [este artigo](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

### <a name="can-i-replicate-the-application-having-separate-resource-group-for-separate-tiers"></a>Pode replicar o aplicativo ter o grupo de recursos separado para camadas separadas? 
Sim, pode replicar o aplicativo e manter a configuração da recuperação após desastre no grupo de recursos separado demasiado.
Por exemplo, se tiver um aplicativo com cada camadas de aplicação, db e web no grupo de recursos separados, em seguida, ter de clicar no [Assistente de replicação](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) três vezes por para proteger todas as camadas. ASR irá replicar esses três camadas em três grupo de recursos diferente.

## <a name="replication-policy"></a>Política de replicação

### <a name="what-is-a-replication-policy"></a>O que é uma política de replicação?
Define as definições para o histórico de retenção de pontos de recuperação e a frequência de instantâneos consistentes com a aplicação. Por predefinição, o Azure Site Recovery cria uma nova política de replicação com as predefinições de:

* 24 horas para que o histórico de retenção de pontos de recuperação.
* 60 minutos para a frequência de instantâneos consistentes com a aplicação.

[Saiba mais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>O que é um ponto de recuperação consistentes com falhas?
Um ponto de recuperação consistentes com falhas representa os dados no disco, como se a VM falhou ou o cabo de alimentação foi obtido a partir do servidor no momento do instantâneo foi tirado. Ele não inclui tudo o que estava na memória, quando o instantâneo foi tirado. 

Hoje em dia, a maioria dos aplicativos pode recuperar bem a partir de instantâneos consistentes com falhas. Um ponto de recuperação consistentes com falhas geralmente é suficiente para sistemas de operativos de não-base de dados e aplicações, como servidores de arquivos, servidores DHCP e servidores de impressão.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>O que é a frequência de geração de ponto de recuperação consistentes com falhas?
Site Recovery cria um ponto de recuperação consistentes com falhas, a cada 5 minutos.

### <a name="what-is-an-application-consistent-recovery-point"></a>O que é um ponto de recuperação consistentes com aplicações? 
Pontos de recuperação consistentes com a aplicação são criados a partir de instantâneos consistentes com aplicações. Pontos de recuperação consistentes com aplicações capturam os mesmos dados como instantâneos consistentes com falhas, com a adição de todos os dados na memória e todas as transações no processo. 

Devido aos conteúdos adicionais, os instantâneos consistentes com aplicações mais estejam e segue há mais tempo para executar. Recomendamos que os pontos de recuperação consistentes com aplicações de sistemas operacionais de base de dados e aplicações como o SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>O que é o impacto de pontos de recuperação consistentes com aplicações no desempenho de aplicações?
Considerando a pontos de recuperação consistentes com aplicações captura todos os dados na memória e no processo requer o framework, como o VSS no windows para silenciar o aplicativo. Isso, caso sejam feitas com bastante freqüência pode ter impacto no desempenho se a carga de trabalho já está muito ocupada. Normalmente, recomenda-se para não utilizar baixa frequência para pontos de recuperação consistente com a aplicação para cargas de trabalho não base de dados e para a carga de trabalho de base de dados de 1 hora é suficiente. 

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>O que é a frequência mínima de geração de ponto de recuperação consistentes com aplicações?
Site Recovery pode cria um ponto de recuperação consistentes com aplicações com uma frequência mínima de na hora.

### <a name="how-are-recovery-points-generated-and-saved"></a>Como pontos de recuperação gerados e guardados?
Para compreender como o Site Recovery gera os pontos de recuperação, vamos dar um exemplo de uma política de replicação que tenha uma janela de retenção de 24 horas e um instantâneo consistente com a aplicação de frequência de 1 hora de ponto de recuperação.

Site Recovery cria um ponto de recuperação consistentes com falhas, a cada 5 minutos. O utilizador não é possível alterar essa frequência. Portanto, para a última 1 hora, o usuário terá 12 consistentes de falhas pontos e 1 consistente com a aplicação de ponto à sua escolha. Conforme o andamento do tempo, prunes de recuperação de Site, a recuperação aponta para além da última 1 hora e guarda apenas 1 recuperação apontem por hora.

Captura de ecrã seguinte ilustra o exemplo. Na captura de ecrã:

1. Por tempo inferior a última 1 hora, isso significa que existem pontos de recuperação com uma frequência de 5 minutos.
2. Por tempo além da última 1 hora, o Site Recovery mantém ponto de recuperação apenas 1.

   ![Lista de pontos de recuperação gerada](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Qual a amplitude posso recuperar?
O ponto de recuperação mais antigo que pode usar é 72 horas.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>O que acontecerá se eu tiver uma política de replicação de 24 horas e um problema impede que o Site Recovery da geração de pontos de recuperação durante mais de 24 horas? Meus pontos de recuperação anteriores serão perdidos?
Não, o Site Recovery irá manter todos os pontos de recuperação anterior. Dependendo da janela de retenção de pontos de recuperação, 24 horas neste caso, a recuperação de Site substitui o ponto mais antigo apenas se existir uma geração de pontos de nova. Neste caso, pois não haverá qualquer novo ponto de recuperação gerado devido a algum problema, todos os pontos de antigos permanecerão intactos uma vez que aceder a janela de retenção.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Após a replicação estiver ativada numa VM, como posso alterar a política de replicação? 
Aceda a **Cofre de recuperação de sites** > **infraestrutura do Site Recovery** > **políticas de replicação**. Selecione a política que pretende editar e guardar as alterações. Qualquer alteração será aplicada a todas as replicações existentes também. 

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>São todos os pontos de recuperação de uma cópia completa de VM ou um valor diferencial?
O primeiro ponto de recuperação que é gerado tem a cópia completa. Pontos de recuperação sucessivas tem alterações delta.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Aumentar o período de retenção de pontos de recuperação aumenta o custo de armazenamento?
Sim. Se aumentar o período de retenção de 24 horas para 72 horas, o Site Recovery irá guardar os pontos de recuperação para um adicional de 48 horas. O tempo extra incorre em custos de armazenamento. Por exemplo, se um ponto de recuperação de único tem alterações de delta de 10 GB e o custo por GB é de US $0.16 por mês, os encargos adicionais seria US $1.6 * 48 por mês.

## <a name="multi-vm-consistency"></a>Consistência de multi-VMs 

### <a name="what-is-multi-vm-consistency"></a>O que é a consistência multi-VM?
Isso significa certificar-se de que o ponto de recuperação é consistente em todas as máquinas virtuais replicadas.
Recuperação de sites fornece uma opção de "Consistência multi-VM," que, quando selecioná-lo, cria um grupo de replicação para replicar todas as máquinas em conjunto, que fazem parte do grupo.
Todas as máquinas virtuais irão partilhar pontos de recuperação consistentes com falhas e consistente com a aplicação quando são efetuadas a ativação pós-falha.
Avance para o tutorial para [ativar a consistência multi-VM](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication).

### <a name="can-i-failover-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Posso ativação pós-falha única máquina virtual dentro de um grupo de replicação de consistência de multi-VMS?
Ao selecionar a opção "Consistência de multi-VMS", estão a indicar que a aplicação tem uma dependência em todas as máquinas virtuais dentro de um grupo. Por conseguinte, a ativação pós-falha da máquina virtual individual não é permitida. 

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Número de máquinas virtuais pode replicar como parte de um grupo de replicação de consistência de multi-VMS
Pode replicar 16 máquinas de virtuais em conjunto num grupo de replicação.

### <a name="when-should-i-enable-multi-vm-consistency-"></a>Quando devo ativar a consistência multi-VM?
Como é exigente em termos de CPU, a ativar a consistência multi-VM pode afetar o desempenho da carga de trabalho. Ele deve ser usado apenas se as máquinas estão a executar a mesma carga de trabalho e precisar de consistência entre várias máquinas. Por exemplo, se tiver duas instâncias do SQL Server e dois servidores web num aplicativo, deve ter a consistência multi-VM para apenas as instâncias do SQL Server.


## <a name="failover"></a>Ativação pós-falha

### <a name="is-failover-automatic"></a>A ativação pós-falha é automática?

A ativação pós-falha não é automática. Iniciar ativações pós-falha com um único clique no portal ou pode utilizar [PowerShell](azure-to-azure-powershell.md) para acionar uma ativação pós-falha. 

### <a name="can-i-retain-a-public-ip-address-after-failover"></a>Pode manter um endereço IP público após a ativação pós-falha?

O endereço IP público da aplicação de produção *não podem ser mantidas na ativação pós-falha*. Cargas de trabalho colocadas como parte do processo de ativação pós-falha deve ser atribuído um recurso IP público do Azure que está disponível na região de destino. Pode efetuar este passo manualmente ou automatizá-lo por meio de um plano de recuperação. Para atribuir um endereço IP público através de um plano de recuperação, veja [configurar endereços IP públicos após a ativação pós-falha](https://docs.microsoft.com/azure/site-recovery/concepts-public-ip-address-with-site-recovery#public-ip-address-assignment-using-recovery-plan).  

### <a name="can-i-retain-a-private-ip-address-during-failover"></a>Pode manter um endereço IP privado durante a ativação pós-falha?
Sim, pode manter um endereço IP privado. Por predefinição, ao ativar a DR para as VMs do Azure, o Site Recovery cria os recursos de destino com base nas definições de recursos de origem. Para as VMs do Azure configurada com endereços IP estáticos, o Site Recovery tenta aprovisionar o mesmo endereço IP da VM, de destino se não está em utilização. Para manter o endereço IP privado em diferentes condições, consulte [endereços IP de manter durante a ativação pós-falha](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-failover-the-server-doesnt-have-the-same-ip-address-as-the-source-vm-why-is-it-assigned-a-new-ip-address"></a>Após a ativação pós-falha, o servidor não tem o mesmo endereço IP da VM de origem. Por que ele está atribuído um novo endereço IP?

Recuperação de site tenta fornecer o endereço IP no momento da ativação pós-falha. Se outra máquina virtual está a demorar esse endereço, o Site Recovery define o endereço IP disponível seguinte como o destino. Para obter uma explicação completa de como o Site Recovery trata endereçamento de mensagens em fila, consulte [configurar o mapeamento da rede e endereçamento IP para redes virtuais](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-network-mapping#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>O que são **mais recente (RPO mais baixo)** pontos de recuperação?
O **mais recente (RPO mais baixo)** opção processa primeiro todos os dados que tenham sido enviados para o serviço Site Recovery, para criar um ponto de recuperação para cada VM antes de realizar a ativação pós-falha para o mesmo. Esta opção fornece o objetivo de ponto de recuperação (RPO), mais baixo, porque a VM criada após a ativação pós-falha tem todos os dados replicados para o Site Recovery quando a ativação pós-falha foi acionada.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Fazer **mais recente (RPO mais baixo)** pontos de recuperação tem um impacto na ativação pós-falha RTO?
Sim. Recuperação de site processa todos os dados pendentes antes de efetuar a ativação pós-falha, para que esta opção tem um objetivo de tempo recuperação de superior (RTO) em comparação com outras opções.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>O que faz a **processado mais recentemente** opção na recuperação aponta significam?
O **processada pela última vez** esse Site Recovery processados do ponto de falha de opção ao longo de todas as VMs no plano para a recuperação mais recente. Para ver o ponto para uma VM específica de recuperação mais recente, verifique **pontos de recuperação mais recentes** nas definições de VM. Esta opção proporciona um RTO baixo, porque não é despendido tempo a processar dados não processados.

### <a name="if-im-replicating-between-two-azure-regions-what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Se estiver a replicar entre duas regiões do Azure, o que acontece se minha região primária sofre uma falha inesperada?
Pode acionar uma ativação pós-falha após a falha. Recuperação de sites não necessita de conectividade da região primária para efetuar a ativação pós-falha.

### <a name="what-is-a-rto-of-a-virtual-machine-failover-"></a>O que é um RTO de uma ativação pós-falha de máquinas virtuais?
Site Recovery tem um [RTO SLA de 2 horas](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). No entanto, a maioria das vezes, a recuperação de Site a ativação pós-falha máquinas virtuais em minutos. Pode calcular o RTO indo para a ativação pós-falha de tarefas que mostra o tempo que levou para criar a VM. Para a recuperação planear RTO, consulte abaixo de secção. 

## <a name="recovery-plans"></a>Planos de recuperação

### <a name="what-is-a-recovery-plan"></a>O que é um plano de recuperação?
Um plano de recuperação no Site Recovery organiza a recuperação de ativação pós-falha de VMs. Ele ajuda a tornar a recuperação consistentemente precisos, repetíveis e automatizadas. Um plano de recuperação aborda as seguintes necessidades para o utilizador:

- Definir um grupo de máquinas virtuais com ativação pós-falha em conjunto
- Definindo as dependências entre as máquinas virtuais para que o aplicativo é exibido com precisão
- Automatizar a recuperação juntamente com as ações manuais personalizadas para atingir as tarefas que não seja a ativação pós-falha de máquinas virtuais

[Saiba mais](site-recovery-create-recovery-plans.md) sobre os planos de recuperação.

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Como o sequenciamento é obtido num plano de recuperação?

Num plano de recuperação, pode criar vários grupos para alcançar a sequenciação. Cada grupo faz a ativação pós-falha de uma só vez. VMs que fazem parte do mesmo falhe de grupo ao longo em conjunto, seguido de outro grupo. Para saber como modelar um aplicativo usando um plano de recuperação, veja [sobre os planos de recuperação](recovery-plan-overview.md#model-apps). 

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Como posso encontrar o RTO de um plano de recuperação?
Para verificar o RTO de um plano de recuperação, fazer uma ativação pós-falha de teste para o plano de recuperação e aceda a **tarefas de recuperação de Site**.
No exemplo seguinte, a tarefa denominada SAPTestRecoveryPlan demorou 8 minutos e 59 segundos para efetuar a ativação pós-falha de todas as máquinas virtuais e executar ações especificadas.

![Lista de tarefas do Site Recovery](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Pode adicionar runbooks de automatização ao plano de recuperação?
Sim, pode integrar os runbooks de automatização do Azure no seu plano de recuperação. [Saiba mais](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Reproteção e reativação pós-falha 

### <a name="after-a-failover-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Após uma ativação pós-falha da região primária para uma região de recuperação após desastre, são VMs numa região de DR protegida automaticamente?
Não. Quando [efetuar a ativação pós-falha](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) as VMs do Azure de uma região para outra, as VMs arrancar na região DR num estado desprotegido. Para efetuar a reativação pós-falha de VMs para a região primária, precisa [voltar a proteger](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) as VMs na região secundária.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>No momento da nova proteção, é a recuperação de Site replicado completa de dados da região secundária para a região primária?
Depende da situação. Por exemplo, se a região de origem VM existir, apenas as alterações entre o disco de origem e o disco de destino são sincronizadas. Recuperação de site calcula os diferenciais ao comparar os discos e, em seguida, transferir os dados. Este processo normalmente demora algumas horas. Para obter mais informações sobre o que acontece durante a nova proteção, consulte [Reproteção efetuar a ativação pós-falha de VMs do Azure para a região primária]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Quanto tempo isso take para a reativação pós-falha?
Após a nova proteção, a quantidade de tempo para a reativação pós-falha é, normalmente, semelhante a hora de ativação pós-falha da região primária para uma região secundária. 

## <a name="capacity"></a>Capacidade
### <a name="does-site-recovery-work-with-reserved-instance"></a>Site Recovery funciona com a instância reservada?
Sim, pode comprar [reservar instâncias](https://azure.microsoft.com/pricing/reserved-vm-instances/) a DR região e as operações de ativação pós-falha do ASR utilizará-los. </br> Nenhuma configuração adicional é necessária dos clientes.


## <a name="security"></a>Segurança
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Os dados de replicação são enviados para o serviço de Recuperação de Sites?
Não, recuperação de sites não interceta os dados replicados, e ele não tem quaisquer informações sobre o que está em execução nas suas máquinas virtuais. Apenas os metadados necessários para orquestrar a replicação e a ativação pós-falha são enviados para o serviço de Recuperação de Sites.  
Site Recovery é ISO 27001:2013, 27018, HIPAA, DPA certified e está no processo de SOC2 e FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>A Recuperação de Sites faz encriptação de replicação?
Sim, ambas as encriptação em trânsito e [encriptação no Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) são suportados.

## <a name="next-steps"></a>Passos Seguintes
* [Revisão](azure-to-azure-support-matrix.md) dar suporte aos requisitos.
* [Configurar](azure-to-azure-tutorial-enable-replication.md) replicação do Azure para o Azure.
