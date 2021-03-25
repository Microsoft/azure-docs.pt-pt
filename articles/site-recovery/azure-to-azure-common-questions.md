---
title: Questões comuns sobre recuperação de desastres em Azure VM com recuperação do local de Azure
description: Este artigo responde a perguntas comuns sobre a recuperação de desastres Azure VM quando utiliza a Recuperação do Site Azure.
author: sideeksh
manager: rochakm
ms.date: 11/03/2019
ms.topic: conceptual
ms.openlocfilehash: 5309fd60640c45ade42bab4c5727cf1f0a8d9d70
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025480"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Perguntas comuns: recuperação após desastre do Azure para Azure

Este artigo responde a questões comuns sobre a recuperação de desastres de Azure VMs para outra região de Azure, utilizando o serviço [de Recuperação do Local Azure.](site-recovery-overview.md)

## <a name="general"></a>Geral

### <a name="how-is-site-recovery-priced"></a>Como é que a Recuperação do Local tem preço?

Saiba mais sobre [os custos](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) para a recuperação de desastres da Azure VM.

### <a name="how-does-the-free-tier-work"></a>Como funciona o nível livre?

Todos os casos protegidos com recuperação do local são gratuitos para os primeiros 31 dias de proteção. Após esse período, a proteção para cada instância é das taxas resumidas nos [detalhes dos preços](https://azure.microsoft.com/pricing/details/site-recovery/). Pode estimar os custos utilizando [a calculadora de preços Azure](https://azure.microsoft.com/pricing/calculator/?service=site-recovery).

### <a name="do-i-incur-other-azure-charges-in-the-first-31-days"></a>Incorrerei em outras acusações do Azure nos primeiros 31 dias?

Sim. Mesmo que a Recuperação do Site Azure seja gratuita durante os primeiros 31 dias de uma instância protegida, poderá incorrer em encargos para o Armazenamento Azure, transações de armazenamento e transferências de dados. Um VM recuperado também pode incorrer em taxas de computação Azure. G

### <a name="how-do-i-get-started-with-azure-vm-disaster-recovery"></a>Como é que começo com a recuperação de desastres da Azure VM?

1. [Compreenda](azure-to-azure-architecture.md) a arquitetura de recuperação de desastres Azure VM.
2. [Rever os](azure-to-azure-support-matrix.md) requisitos de apoio.
3. [Confiúdo](azure-to-azure-how-to-enable-replication.md) de recuperação de desastres para os VMs do Azure.
4. [Faça um exercício de recuperação de desastres](azure-to-azure-tutorial-dr-drill.md) com um teste falhado.
5. [Executar um fracasso total](azure-to-azure-tutorial-failover-failback.md) para uma região secundária de Azure.
6. [Recue](azure-to-azure-tutorial-failback.md) da região secundária para a região primária.

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Como garantir a capacidade na região-alvo?

A equipa de Recuperação de Sítios, e a equipa de gestão de capacidades do Azure, planeiam capacidade de infraestrutura suficiente. Quando inicia uma falha, as equipas também ajudam a garantir que as instâncias VM protegidas pela Recuperação do Local se desloquem para a região alvo.

## <a name="replication"></a>Replicação

### <a name="can-i-replicate-vms-with-disk-encryption"></a>Posso replicar VMs com encriptação de disco?

Sim. A Recuperação do Site suporta a recuperação de desastres de VMs que têm encriptação de disco Azure (ADE) ativada. Quando ativa a replicação, o Azure copia todas as chaves e segredos de encriptação de disco necessários da região de origem para a região alvo, no contexto do utilizador. Se não tiver permissões necessárias, o seu administrador de segurança pode usar um script para copiar as chaves e segredos.

- A Recuperação do Site suporta ADE para VMs Azure que executam o Windows.
- Suportes de recuperação do local:
    - Versão ADE 0.1, que tem um esquema que requer diretório Azure Ative (Azure AD).
    - Versão ADE 1.1, que não requer Azure AD. Para a versão 1.1, os VMs do Windows Azure devem ter gerido discos.
    - [Saiba mais](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema). sobre os esquemas de extensão.

[Saiba mais](azure-to-azure-how-to-enable-replication-ade-vms.md) sobre como permitir a replicação de VMs encriptados.

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>Posso selecionar uma conta de automação de um grupo de recursos diferente?

Quando permite a Recuperação de Sítios para gerir atualizações para a extensão do serviço mobility em execução em VMs Azure replicados, implementa um runbook global (usado pelos serviços Azure), através de uma conta de automação Azure. Pode utilizar a conta de automatização que a Recuperação do Site cria ou selecionar para utilizar uma conta de automatização existente. 

Atualmente, no portal, só é possível selecionar uma conta de automação no mesmo grupo de recursos que o cofre. Pode selecionar uma conta de automatização de um grupo de recursos diferente utilizando o PowerShell. [Saiba mais](azure-to-azure-autoupdate.md#enable-automatic-updates).

### <a name="if-i-use-a-customer-automation-account-thats-not-in-the-vault-resource-group-can-i-delete-the-default-runbook"></a>Se eu usar uma conta de automação de clientes que não está no grupo de recursos do cofre, posso apagar o livro de contas predefinido?

Sim, pode apagá-lo se não precisar. 

### <a name="can-i-replicate-vms-to-another-subscription"></a>Posso replicar VMs para outra subscrição?

Sim, você pode replicar VMs Azure para qualquer subscrição dentro do mesmo inquilino AZure AD. Quando permite a recuperação de desastres para VMs, por padrão a subscrição-alvo mostrada é a da fonte VM. Pode modificar a subscrição-alvo, e outras configurações (como grupo de recursos e rede virtual), são povoadas automaticamente a partir da subscrição selecionada.

### <a name="can-i-replicate-vms-in-an-availability-zone-to-another-region"></a>Posso replicar VMs numa zona de disponibilidade para outra região?

Sim, você pode replicar VMs em zonas de disponibilidade para outra região de Azure. 

### <a name="can-i-replicate-non-zone-vms-to-a-zone-within-the-same-region"></a>Posso replicar VMs não-zona para uma zona dentro da mesma região? 

Isto não é suportado no portal. Pode utilizar a API/PowerShell REST para o fazer.

### <a name="can-i-replicate-zoned-vms-to-a-different-zone-in-the-same-region"></a>Posso replicar VMs de zona para uma zona diferente na mesma região?

O apoio a esta questão limita-se a algumas regiões. [Saiba mais](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

### <a name="can-i-exclude-disks-from-replication"></a>Posso excluir discos da replicação?

Sim, pode excluir discos quando configurar a replicação, utilizando o PowerShell. [Saiba mais](azure-to-azure-exclude-disks.md).

### <a name="can-i-replicate-new-disks-added-to-replicated-vms"></a>Posso replicar discos novos adicionados a VMs replicados?

Para VMs replicados com discos geridos, pode adicionar novos discos e permitir a replicação para eles. Quando se adiciona um disco novo, o VM replicado mostra uma mensagem de aviso de que um ou mais discos no VM estão disponíveis para proteção. 

- Se ativar a replicação dos discos adicionados, o aviso desaparece após a replicação inicial.
- Se não quiser ativar a replicação do disco, pode dispensar o aviso.
- Se falhar sobre um VM com discos adicionados, os pontos de replicação mostram os discos disponíveis para recuperação. Por exemplo, se adicionar um segundo disco a um VM com um disco, um ponto de replicação criado antes de adicionar mostra como "1 de 2 discos".

A Recuperação do Site não suporta a "remoção a quente" dos discos de um VM replicado. Se remover um disco VM, tem de desativar e, em seguida, a replicação reenerável para o VM.

### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência posso replicar-me ao Azure?

A replicação é contínua ao replicar VMs Azure para outra região do Azure. [Saiba mais](./azure-to-azure-architecture.md#replication-process) sobre como funciona a replicação.

### <a name="can-i-replicate-virtual-machines-within-a-region"></a>Posso replicar máquinas virtuais dentro de uma região? 

Não é possível utilizar a Recuperação do Site para replicar discos dentro de uma região.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Posso replicar os casos de VM em qualquer região de Azure?

Pode replicar e recuperar VMs entre duas regiões dentro do mesmo aglomerado geográfico. Os aglomerados geográficos são definidos com a latência dos dados e a soberania em mente. [Saiba mais](./azure-to-azure-support-matrix.md#region-support) sobre o apoio à região.

### <a name="does-site-recovery-need-internet-connectivity"></a>A Recuperação do Site precisa de conectividade com a Internet?

Não, mas os VMs precisam de acesso a URLs de Recuperação de Locais e gamas IP. [Saiba mais](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-tiered-across-resource-groups"></a>Posso replicar uma aplicação espalhada por grupos de recursos?

Sim, pode replicar a aplicação e manter a configuração de recuperação de desastres num grupo de recursos separado.

Por exemplo, se as aplicações tão bem têm três níveis (aplicação/base de dados/web) em diferentes grupos de recursos, é necessário ativar a replicação três vezes, para proteger todos os níveis. A Recuperação do Site replica os três níveis em três grupos de recursos diferentes.

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>Posso mover contas de armazenamento entre grupos de recursos?

Não, isto não é suportado. Se mover acidentalmente as contas de armazenamento para um grupo de recursos diferente e eliminar o grupo de recursos original, então pode criar um novo grupo de recursos com o mesmo nome que o antigo grupo de recursos e, em seguida, mover a conta de armazenamento para este grupo de recursos.

## <a name="replication-policy"></a>Política de replicação

### <a name="what-is-a-replication-policy"></a>O que é uma política de replicação?

Uma política de replicação define o histórico de retenção dos pontos de recuperação e a frequência de instantâneos consistentes com aplicações.  A Recuperação do Site cria uma política de replicação padrão da seguinte forma:

- Mantenha os pontos de recuperação durante 24 horas.
- Tire fotos consistentes de aplicativos a cada quatro horas.

[Saiba mais](azure-to-azure-how-to-enable-replication.md#customize-target-resources) sobre as definições de replicação.

### <a name="whats-a-crash-consistent-recovery-point"></a>O que é um ponto de recuperação consistente?

Um ponto de recuperação consistente contém dados no disco, como se tivesse retirado o cabo de alimentação do servidor durante a imagem. Não inclui nada que estivesse na memória quando a foto foi tirada.

Hoje em dia, a maioria das aplicações pode recuperar bem de instantâneos consistentes com falhas. Um ponto de recuperação consistente com falhas é geralmente suficiente para sistemas operativos não-base, e aplicações como servidores de ficheiros, servidores DHCP e servidores de impressão.

A Recuperação do Site cria automaticamente um ponto de recuperação consistente a cada cinco minutos.

### <a name="whats-an-application-consistent-recovery-point"></a>O que é um ponto de recuperação consistente de aplicações?

Os pontos de recuperação consistentes da aplicação são criados a partir de instantâneos consistentes com aplicações. Capturam os mesmos dados que os instantâneos consistentes com falhas e, além disso, capturam dados na memória e todas as transações em processo.

Devido a conteúdoexuais, as fotos consistentes com aplicações são as mais envolvidas e demoram mais tempo. Recomendamos pontos de recuperação consistentes de aplicações para sistemas operativos de base de dados e aplicações como o SQL Server. Para o Windows, as imagens consistentes com aplicações utilizam o Serviço de Cópia Sombra de Volume (VSS).

### <a name="do-app-consistent-recovery-points-impact-performance"></a>Os pontos de recuperação consistentes da aplicação têm um desempenho de impacto?

 Como os pontos de recuperação consistentes da aplicação captam todos os dados na memória e no processo, se capturarem com frequência, pode afetar o desempenho quando a carga de trabalho já está ocupada. Não recomendamos que capture com demasiada frequência para cargas de trabalho não-base. Mesmo para as cargas de dados, uma hora deve ser suficiente.

### <a name="whats-the-minimum-frequency-for-generating-app-consistent-recovery-points"></a>Qual é a frequência mínima para gerar pontos de recuperação consistentes com aplicações?

A Recuperação do Site pode criar pontos de recuperação consistentes com uma frequência mínima de uma hora.

### <a name="can-i-enable-app-consistent-replication-for-linux-vms"></a>Posso permitir a replicação consistente de aplicativos para Os VMs Linux?

Sim. O agente de mobilidade para o Linux suporta scripts personalizados para consistência de aplicações. Um script personalizado com pré e pós-opções é usado pelo agente. [Saiba mais](site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

### <a name="how-are-recovery-points-generated-and-saved"></a>Como são gerados e salvos pontos de recuperação?

Para entender como a Recuperação do Site gera pontos de recuperação, vamos usar um exemplo. 

- Uma política de replicação mantém os pontos de recuperação durante 24 horas, e leva uma imagem de frequência consistente a cada hora.
- A Recuperação do Local cria um ponto de recuperação consistente a cada cinco minutos. Não pode mudar esta frequência.
- A recuperação do local atinge os pontos de recuperação após uma hora, poupando um ponto por hora.

Assim, na última hora, pode escolher entre 12 pontos consistentes com falhas e um ponto consistente de aplicação, como mostra o gráfico.

   ![Lista de pontos de recuperação gerados](./media/azure-to-azure-common-questions/recovery-points.png)

### <a name="how-far-back-can-i-recover"></a>Até onde posso me recuperar?

O ponto de recuperação mais antigo que pode usar é de 72 horas.

### <a name="what-happens-if-site-recovery-cant-generate-recovery-points-for-more-than-24-hours"></a>O que acontece se a Recuperação do Site não conseguir gerar pontos de recuperação por mais de 24 horas? 

Se tiver uma política de replicação de 24 horas, e a Recuperação do Local não puder gerar pontos de recuperação por mais de 24 horas, os seus antigos pontos de recuperação permanecem. A Recuperação do Site só substitui o ponto mais antigo se gerar novos pontos. Até que haja novos pontos de recuperação, todos os pontos antigos permanecem depois de chegar à janela de retenção.

### <a name="can-i-change-the-replication-policy-after-replication-is-enabled"></a>Posso alterar a política de replicação após a replicação?

Sim. No cofre > políticas de replicação **da infraestrutura de**  >  **recuperação** do local, selecione e edite a política. As alterações aplicam-se também às políticas existentes.

### <a name="are-all-recovery-points-a-complete-vm-copy"></a>Todos os pontos de recuperação são uma cópia VM completa?

O primeiro ponto de recuperação gerado tem a cópia completa. Os sucessivos pontos de recuperação têm mudanças delta.

### <a name="do-increases-in-recovery-point-retention-increase-storage-costs"></a>Os aumentos na retenção de pontos de recuperação aumentam os custos de armazenamento?

Sim. Por exemplo, se aumentar a retenção de 24 horas para 72, a Recuperação do Site guarda pontos de recuperação por mais 48 horas. O tempo adicional incorre em alterações de armazenamento. Como exemplo, se um único ponto de recuperação tivesse alterações delta de 10 GB, com um custo por GB de $0,16 por mês, então os encargos adicionais seriam $1,60 × 48 por mês.

## <a name="multi-vm-consistency"></a>Consistência multi-VM

### <a name="what-is-multi-vm-consistency"></a>O que é consistência multi-VM?

A consistência multi-VM garante que os pontos de recuperação são consistentes em todas as máquinas virtuais replicadas.

- Quando ativa a consistência multi-VM, a Recuperação do Site cria um grupo de replicação de todas as máquinas com a opção ativada. 
- Quando falhas nas máquinas do grupo de replicação, elas têm pontos de recuperação consistentes e consistentes com falhas.

[Saiba](azure-to-azure-tutorial-enable-replication.md#enable-replication) como ativar a consistência multi-VM.

### <a name="can-i-fail-over-a-single-vm-in-a-replication-group"></a>Posso falhar por causa de um único VM num grupo de replicação?

N.º Quando ativa a consistência multi-VM, infere que uma aplicação tem uma dependência de todos os VMs do grupo de replicação, e não é permitida uma falha de VM única.

### <a name="how-many-vm-can-i-replicate-together-in-a-group"></a>Quantos VM posso replicar juntos num grupo?

Pode replicar 16 VMs juntos num grupo de replicação.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Quando devo permitir a consistência multi-VM?

A consistência multi-VM é intensiva em CPU, e permitir que possa afetar o desempenho da carga de trabalho. Ativar apenas se os VMs estiverem a executar a mesma carga de trabalho e necessitar de consistência em várias máquinas. Por exemplo, se tiver duas instâncias do SQL Server e dois servidores web numa aplicação, ative a consistência multi-VM apenas para as instâncias do SQL Server.

### <a name="can-i-add-a-replicating-vm-to-a-replication-group"></a>Posso adicionar um VM replicador a um grupo de replicação?

Quando ativa a replicação de um VM, pode adicioná-la a um novo grupo de replicação ou a um grupo existente. Não se pode adicionar um VM que já está a replicar-se num grupo. 
 
## <a name="failover"></a>Ativação pós-falha

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Como garantir a capacidade na região-alvo?

A equipa de Recuperação de Sítios, e a equipa de gestão de capacidades da Azure, planeiam capacidade de infraestrutura suficiente. Quando inicia uma falha, as equipas também ajudam a garantir que as instâncias VM protegidas pela Recuperação do Site possam ser implantadas na região alvo.

### <a name="is-failover-automatic"></a>A ativação pós-falha é automática?

A ativação pós-falha não é automática. Pode iniciar uma falha com um único clique no portal ou utilizar o  [PowerShell](azure-to-azure-powershell.md) para desencadear uma falha.

### <a name="can-i-keep-a-public-ip-address-after--failover"></a>Posso manter um endereço IP público depois do fracasso?

Não é possível manter o endereço IP público para uma aplicação de produção depois de uma falha.

Quando você levantar uma carga de trabalho como parte do processo de failover, você precisa atribuir um recurso de endereço IP público Azure para ele. O recurso deve estar disponível na região-alvo. Pode atribuir manualmente o recurso ip público Azure ou automatizá-lo com um plano de recuperação. [Saiba](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan) como configurar endereços IP públicos após o failover.

### <a name="can-i-keep-a-private-ip-address-after-failover"></a>Posso manter um endereço IP privado depois de ter falhado?

Sim. Por predefinição, quando permite a recuperação de desastres para VMs Azure, a Recuperação do Site cria recursos-alvo, com base nas definições de recursos de origem. Para VMs Azure configurados com endereços IP estáticos, a Recuperação do Site tenta providenciar o mesmo endereço IP para o VM alvo, se não estiver a ser utilizado.
[Saiba mais sobre](site-recovery-retain-ip-azure-vm-failover.md) manter os endereços IP após o failover.

### <a name="why-is-a-vm-assigned-a-new-ip-address-after-failover"></a>Por que é atribuído um VM um novo endereço IP após falha?

A Recuperação do Site tenta fornecer o endereço IP no momento da falha. Se outro VM utilizar esse endereço, a Recuperação do Site define o próximo endereço IP disponível como alvo.

[Saiba mais sobre](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms) a criação de mapeamento de rede e endereço IP para redes virtuais.

### <a name="whats-the-latest-recovery-point"></a>Qual é o *último* ponto de recuperação?

A *última opção (mais baixa do* ponto de recuperação de RPO) faz o seguinte:

1. Primeiro processa todos os dados que foram enviados para a Recuperação do Site.
2. Após o serviço processar os dados, cria um ponto de recuperação para cada VM, antes de falhar para o VM. Esta opção fornece o objetivo de ponto de recuperação mais baixo (RPO).
3. O VM criado após o failover tem todos os dados replicados para a Recuperação do Local, a partir do momento em que a falha foi desencadeada.

### <a name="do-latest-recovery-points-impact-failover-rto"></a>Os *últimos* pontos de recuperação têm impacto no RTO?

Sim. A Recuperação do Site processa todos os dados pendentes antes de falhar, pelo que esta opção tem um objetivo de tempo de recuperação mais elevado (RTO) do que outras opções.

### <a name="whats-the-latest-processed-recovery-option"></a>Qual é a última opção de recuperação *processada?*

A última opção *processada* faz o seguinte:

1. Falha em todos os VMs até ao último ponto de recuperação processado pela Recuperação do Local. Esta opção fornece um RTO baixo, porque não é gasto tempo a processar dados não processados.

### <a name="what-if-theres-an-unexpected-outage-in-the-primary-region"></a>E se houver uma paragem inesperada na região primária?

Pode começar a falhar. A Recuperação do Local não precisa de conectividade da região primária para fazer o failover.

### <a name="what-is-the-rto-of-a-vm-failover"></a>O que é o RTO de um VM failover?

A Recuperação do Site tem um RTO SLA de [duas horas.](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) Na maior parte do tempo, a Recuperação do Site falha sobre os VMs em poucos minutos. Para calcular o RTO, reveja o trabalho de failover, o que mostra o tempo que demorou a apresentar um VM. 

## <a name="recovery-plans"></a>Planos de recuperação

### <a name="whats-a-recovery-plan"></a>O que é um plano de recuperação?

Um [plano de recuperação](site-recovery-create-recovery-plans.md) no Local de Recuperação orquestra o failover e a recuperação de VMs. Ajuda a tornar a recuperação consistentemente precisa, repetível e automatizada. Faz o seguinte:

- Define um grupo de VMs que falham juntos
- Define as dependências entre VMs, de modo que a aplicação surge com precisão.
- Automatiza a recuperação, com a opção de ações manuais personalizadas para tarefas que não a VM falha. 


### <a name="how-does-sequencing-work"></a>Como funciona a sequenciação?

Num plano de recuperação, pode criar vários grupos de VM para sequenciação. Os grupos falham ao longo de um de cada vez, de modo que os VMs que fazem parte do mesmo grupo falham juntos. [Saiba mais](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Como posso encontrar o RTO de um plano de recuperação?

Para verificar o RTO de um plano de recuperação, faça um teste de failover para o plano de recuperação. Nos **trabalhos de recuperação do local,** verifique a duração do teste de failover. No exemplo, o teste **SAPTestRecoveryPlan** decorreu 8 minutos e 59 segundos.

![Lista de trabalhos que mostram a duração do teste de failover para RTO](./media/azure-to-azure-common-questions/recovery-plan-rto.png)

### <a name="can-i-add-automation-runbooks-to-recovery-plans"></a>Posso adicionar livros de automação a planos de recuperação?

Sim. [Saiba mais](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Reproteção e recuo

### <a name="after-failover-are-vms-in-the-secondary-region-protected-automatically"></a>Após o fracasso, os VM na região secundária estão protegidos automaticamente? 

N.º Quando se falham sobre os VM de uma região para outra, os VMs começam na região de recuperação de desastres alvo num estado desprotegido. Para [reprotecer](./azure-to-azure-how-to-reprotect.md) os VMs na região secundária, permite a replicação de volta à região primária.

### <a name="when-i-reprotect-is-all-data-replicated-from-the-secondary-region-to-primary"></a>Quando reproteto, todos os dados são replicados da região secundária para o primário? 

Depende. Se a região de origem VM existir, apenas as alterações entre o disco de origem e o disco-alvo são sincronizadas. A Recuperação do Site compara os discos com o que é diferente e depois transfere os dados. Este processo geralmente leva algumas horas. [Saiba mais](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection).

### <a name="how-long-does-it-take-fail-back"></a>Quanto tempo demora a falhar?

Após a reproteção, o recuo leva cerca do mesmo tempo que levou a falhar da região primária para uma região secundária.

## <a name="capacity"></a><a name="capacity"></a>Capacidade

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Como garantir a capacidade na região-alvo?

A equipa de recuperação do local e o plano da equipa de gestão de capacidades da Azure para capacidade de infraestrutura suficiente. Quando inicia uma falha, as equipas também ajudam a garantir que as instâncias VM protegidas pela Recuperação do Site possam ser implantadas na região alvo.

### <a name="does-site-recovery-work-with-reserved-instances"></a>A Recuperação do Local funciona com instâncias reservadas?

Sim, você pode comprar [VMs Azure reservados](https://azure.microsoft.com/pricing/reserved-vm-instances/) na região de recuperação de desastres, e as operações de failover de recuperação de locais usá-los. Não é necessária nenhuma configuração adicional.

## <a name="security"></a>Segurança

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Os dados de replicação são enviados para o serviço de Recuperação de Sites?

Não, a Recuperação do Site não interceta dados replicados, e não tem nenhuma informação sobre o que está a correr nos seus VMs. Apenas os metadados necessários para orquestrar a replicação e a ativação pós-falha são enviados para o serviço de Recuperação de Sites.

A Recuperação do Local é certificada pela ISO 27001:2013, 27018, HIPAA e DPA. O serviço está a ser submetido a avaliações SOC2 e FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>A Recuperação de Sites faz encriptação de replicação?

Sim, tanto a encriptação em trânsito como [a encriptação em repouso em Azure](../storage/common/storage-service-encryption.md) são suportadas.

## <a name="next-steps"></a>Passos seguintes

- [Reveja os requisitos de suporte Azure-to-Azure](azure-to-azure-support-matrix.md).
- [Configurar a replicação Azure-to-Azure](azure-to-azure-tutorial-enable-replication.md).
- Se tiver dúvidas depois de ler este artigo, publique-as na página de perguntas do [Microsoft Q&Uma página de perguntas para serviços de recuperação do Azure](/answers/topics/azure-site-recovery.html).
