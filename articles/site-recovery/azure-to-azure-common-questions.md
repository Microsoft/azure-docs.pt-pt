---
title: Perguntas comuns sobre recuperação de desastres da VM Azure com recuperação do site Azure
description: Este artigo responde a perguntas comuns sobre a recuperação de desastres do Azure VM quando utiliza a Recuperação do Site Azure.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: bc2acc4303a270a2bf71f0c9ff249b60a0328a09
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983283"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Questões comuns: Recuperação de desastres Azure-to-Azure

Este artigo responde a perguntas comuns sobre a recuperação de desastres de VMs Azure para outra região de Azure para quando você usar [A Recuperação do Site Azure](site-recovery-overview.md).

## <a name="general"></a>Geral

### <a name="how-is-site-recovery-priced"></a>Como é que a Recuperação do Site tem o preço?

Rever os preços de recuperação do [site Azure para VMs](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Como funciona o nível gratuito para a Recuperação do Site Azure?

Todas as instâncias protegidas com a Recuperação do Site Azure são gratuitas durante os primeiros 31 dias de proteção. Após esse período, a proteção para cada instância está às taxas de fixação do [site Azure para máquinas virtuais Azure](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante os primeiros 31 dias, incorreria em outras acusações do Azure?

Sim. Embora a Recuperação do Site Azure seja gratuita durante os primeiros 31 dias de uma instância protegida, poderá incorrer em encargos para o Armazenamento Azure, transações de armazenamento e transferências de dados. Uma máquina virtual recuperada também pode incorrer em cargas de computação Azure. Obtenha detalhes completos sobre os preços no preço de recuperação do [site do Azure](https://azure.microsoft.com/pricing/details/site-recovery).

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Quais são as melhores práticas para a recuperação de desastres das Máquinas Virtuais Azure?

1. [Compreender a arquitetura Azure-to-Azure](azure-to-azure-architecture.md)
1. [Reveja as configurações suportadas e não suportadas](azure-to-azure-support-matrix.md)
1. [Configurar recuperação de desastres para VMs Azure](azure-to-azure-how-to-enable-replication.md)
1. [Executar uma ativação pós-falha de teste](azure-to-azure-tutorial-dr-drill.md)
1. [Falhar e falhar na região primária](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>Como é assegurada a capacidade na região-alvo?

A equipa de recuperação do site e o plano de equipa de gestão de capacidade seletiva Azure para uma capacidade de infraestrutura suficiente. Quando inicia uma falha, as equipas também ajudam a garantir que os casos de VM protegidos pela Recuperação do Local serão implantados para a região alvo.

## <a name="replication"></a>Replicação

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Posso replicar VMs ativados através da encriptação do disco Azure?

Sim. A Recuperação do Site suporta a recuperação de desastres de VMs que têm encriptação de disco azure ativada. Ao ativar a replicação, o Azure copia todas as chaves e segredos de encriptação de disco necessários da região de origem para a região alvo no contexto do utilizador. Se não tiver as permissões apropriadas, o seu administrador de segurança pode usar um guião para copiar as chaves e segredos.

- A Recuperação do Site suporta a encriptação do disco Azure para VMs Azure que estão a executar o Windows.
- O Site Recovery suporta a versão 0.1 da encriptação do disco Azure, que tem um esquema que requer o Diretório Ativo Azure (Azure AD). O Site Recovery também suporta a versão 1.1, que não requer AD Azure. Saiba mais sobre o esquema de [extensão para encriptação](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema)de disco Azure .
  - Para a versão 1.1 da Encriptação do Disco Azure, tem de utilizar os VMs do Windows com discos geridos.
  - [Saiba mais](azure-to-azure-how-to-enable-replication-ade-vms.md) sobre a replicação de VMs encriptados.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Posso replicar VMs para outra subscrição?

Sim, pode replicar Os VMs Azure a uma subscrição diferente dentro do mesmo inquilino azure AD.

Configure a recuperação de [desastres através](https://azure.microsoft.com/blog/cross-subscription-dr) de subscrições selecionando outra subscrição no momento da replicação.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Posso replicar VMs Azure encurralados por zonas para outra região?

Sim, pode [supor vMs de zona](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) para outra região.

### <a name="can-i-exclude-disks"></a>Posso excluir discos?

Sim, pode excluir discos no momento da proteção utilizando o PowerShell. Para mais informações, consulte [como excluir discos da replicação](azure-to-azure-exclude-disks.md).

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Posso adicionar novos discos a VMs replicados e permitir a replicação para eles?

Sim, adicionar novos discos a VMs replicados e permitir a replicação para eles é suportado para VMs Azure com discos geridos. Quando se adiciona um novo disco a um VM Azure que está habilitado para a replicação, a saúde de replicação para o VM mostra um aviso. Este aviso diz que um ou mais discos no VM estão disponíveis para proteção. Pode ativar a replicação de discos adicionados.

- Se ativar a proteção dos discos adicionados, o aviso desaparecerá após a replicação inicial.
- Se não ativar a replicação do disco, pode dispensar o aviso.
- Se falhar sobre um VM que tem um disco adicional e replicação ativado, existem pontos de replicação. Os pontos de replicação mostrarão os discos disponíveis para recuperação.

Por exemplo, digamos que um VM tem um único disco e você adiciona um novo. Pode haver um ponto de replicação que foi criado antes de adicionar o disco. Este ponto de replicação mostrará que é composto por "1 de 2 discos".

A Recuperação do Site não suporta "remoção a quente" de um disco de um VM replicado. Se remover um disco VM, tem de desativar e, em seguida, reativar a replicação para o VM.

### <a name="how-often-can-i-replicate-to-azure"></a>Quantas vezes posso replicar o Azure?

A replicação é contínua quando se está a replicar VMs Azure para outra região de Azure. Para mais informações, consulte a [arquitetura de replicação Azure-to-Azure.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process)

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>Posso replicar máquinas virtuais dentro de uma região? Preciso desta funcionalidade para migrar VMs.

Não é possível utilizar uma solução de recuperação de discos Azure-to-Azure para replicar VMs dentro de uma região.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Posso replicar casos vm em qualquer região de Azure?

Ao utilizar a Recuperação do Site, pode replicar e recuperar VMs entre duas regiões dentro do mesmo aglomerado geográfico. Os clusters geográficos são definidos com latência de dados e soberania em mente. Para mais informações, consulte a matriz de suporte da [região](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)de Recuperação do Local.

### <a name="does-site-recovery-require-internet-connectivity"></a>A Recuperação do Site requer conectividade à Internet?

Não, a Recuperação do Site não requer conectividade na Internet. Mas requer acesso a URLs de Recuperação do Site e intervalos IP, como mencionado em rede na recuperação de desastres do [Azure VM.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-urls)

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>Posso replicar uma aplicação que tem um grupo de recursos separados para níveis separados?

Sim, pode replicar a aplicação e manter a configuração de recuperação de desastres num grupo de recursos separados também.

Por exemplo, se a sua aplicação tiver a aplicação, base de dados e web de cada nível num grupo de recursos separado, então terá de selecionar o assistente de [replicação](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) três vezes para proteger todos os níveis. A Recuperação do Site irá replicar estes três níveis em três grupos de recursos diferentes.

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>Posso mover contas de armazenamento através de grupos de recursos?

Não, este é um cenário não apoiado. No entanto, se transferir acidentalmente as contas de armazenamento para um grupo de recursos diferentes e eliminar o grupo de recursos originais, então pode criar um novo grupo de recursos com o mesmo nome que o antigo grupo de recursos e, em seguida, mover a conta de armazenamento para este grupo de recursos.

## <a name="replication-policy"></a>Política de replicação

### <a name="what-is-a-replication-policy"></a>O que é uma política de replicação?

Uma política de replicação define as definições para o histórico de retenção dos pontos de recuperação. A política também define a frequência de instantâneos consistentes com aplicações. Por padrão, a Recuperação do Site Azure cria uma nova política de replicação com definições padrão de:

- 24 horas para o histórico de retenção de pontos de recuperação.
- 60 minutos para a frequência de instantâneos consistentes com aplicações.

[Saiba mais sobre as definições de replicação](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>O que é um ponto de recuperação consistente?

Um ponto de recuperação consistente com falhas tem os dados do disco como se tivesse retirado o cabo de alimentação do servidor durante o instantâneo. O ponto de recuperação consistente não inclui nada que estivesse na memória quando a foto foi tirada.

Hoje, a maioria das aplicações pode recuperar bem de instantâneos consistentes com acidentes. Um ponto de recuperação consistente com falhas é geralmente suficiente para sistemas operativos sem base de dados e aplicações como servidores de ficheiros, servidores DHCP e servidores de impressão.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Qual é a frequência da geração de pontos de recuperação consistente?

A Recuperação do Local cria um ponto de recuperação consistente a cada 5 minutos.

### <a name="what-is-an-application-consistent-recovery-point"></a>O que é um ponto de recuperação consistente com aplicações?

Os pontos de recuperação consistentes com aplicação são criados a partir de instantâneos consistentes com aplicações. Os pontos de recuperação consistentes com aplicações captam os mesmos dados que instantâneos consistentes com o crash, ao mesmo tempo que capturam dados na memória e em todas as transações em processo.

Devido ao seu conteúdo extra, as fotos consistentes com aplicações são as mais envolvidas e demoram mais tempo. Recomendamos pontos de recuperação consistentes com aplicações para sistemas operativos de base de dados e aplicações como o SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Qual é o impacto dos pontos de recuperação consistentes com aplicações no desempenho da aplicação?

Os pontos de recuperação consistentes com aplicação captam todos os dados na memória e no processo. Como os pontos de recuperação captam esses dados, eles requerem uma estrutura como o Serviço de Cópia sonora do Volume shadow no Windows para quiesce da aplicação. Se o processo de captura for frequente, pode afetar o desempenho quando a carga de trabalho já está ocupada. Não recomendamos que utilize baixa frequência para pontos de recuperação consistentes com aplicações para cargas de trabalho não base de dados. Mesmo para a carga de trabalho na base de dados, 1 hora é suficiente.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Qual é a frequência mínima da geração de pontos de recuperação consistente sinuosa?

A Recuperação do Site pode criar um ponto de recuperação consistente com aplicação com uma frequência mínima de 1 hora.

### <a name="how-are-recovery-points-generated-and-saved"></a>Como são gerados e guardados os pontos de recuperação?

Para entender como a Recuperação do Site gera pontos de recuperação, vamos ver um exemplo de uma política de replicação. Esta política de replicação tem um ponto de recuperação com uma janela de retenção 24 horas e uma imagem de frequência consistente com aplicações de 1 hora.

A Recuperação do Local cria um ponto de recuperação consistente a cada 5 minutos. Não pode mudar esta frequência. Durante a última hora, pode escolher entre 12 pontos consistentes com acidentes e 1 ponto consistente com aplicações. À medida que o tempo avança, a Recuperação do Site poda todos os pontos de recuperação para além da última hora e poupa apenas 1 ponto de recuperação por hora.

A imagem que se segue ilustra o exemplo. Na imagem:

- Na última hora, há pontos de recuperação com uma frequência de 5 minutos.
- Para além da última hora, a Recuperação do Site mantém apenas 1 ponto de recuperação.

   ![Lista de pontos de recuperação gerados](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Até onde posso me recuperar?

O ponto de recuperação mais antigo que pode usar é de 72 horas.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Tenho uma política de replicação de 24 horas. O que acontecerá se um problema impedir a Recuperação do Site de gerar pontos de recuperação por mais de 24 horas? Os meus pontos de recuperação anteriores perder-se-ão?

Não, a Recuperação do Site manterá todos os seus pontos de recuperação anteriores. Dependendo da janela de retenção dos pontos de recuperação, a Recuperação do Site substitui o ponto mais antigo apenas se gerar novos pontos. Devido ao problema, a Recuperação do Site não pode gerar novos pontos de recuperação. Até que haja novos pontos de recuperação, todos os pontos antigos permanecerão depois de chegar à janela de retenção.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Depois de a replicação ser ativada num VM, como posso alterar a política de replicação?

Vá para as políticas de**replicação**de**infraestruturade recuperação** > do local do cofre do **cofre.** >  Selecione a política que pretende editar e guarde as alterações. Qualquer alteração aplicar-se-á a todas as replicações existentes também.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Todos os pontos de recuperação são uma cópia completa do VM ou um diferencial?

O primeiro ponto de recuperação gerado tem a cópia completa. Quaisquer pontos de recuperação sucessivos têm mudanças delta.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>O aumento do período de retenção dos pontos de recuperação aumenta o custo de armazenamento?

Sim, se aumentar o período de retenção de 24 horas para 72 horas, a Recuperação do Local poupará os pontos de recuperação por mais 48 horas. O tempo adicional incorrerá em taxas de armazenamento. Por exemplo, um único ponto de recuperação pode ter alterações delta de 10 GB com um custo por GB de $0,16 por mês. Encargos adicionais seriam $1,60 × 48 por mês.

## <a name="multi-vm-consistency"></a>Consistência multi-VM

### <a name="what-is-multi-vm-consistency"></a>O que é a consistência multi-VM?

A consistência multi-VM garante que o ponto de recuperação é consistente em todas as máquinas virtuais replicadas.

A Recuperação do Site fornece uma opção **de consistência Multi-VM,** que cria um grupo de replicação de todas as máquinas.

Quando falhares nas máquinas virtuais, elas terão pontos de recuperação consistentes e consistentes com aplicações.

Percorra o tutorial para permitir a [consistência multi-VM.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm)

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Posso falhar uma única máquina virtual dentro de um grupo de replicação de consistência multi-VM?

Ao selecionar a opção **de consistência Multi-VM,** está a afirmar que a aplicação tem uma dependência de todas as máquinas virtuais dentro de um grupo. Não é permitida uma única falha na máquina virtual.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Quantas máquinas virtuais posso replicar como parte de um grupo de replicação de consistência multi-VM?

Pode replicar 16 máquinas virtuais juntas num grupo de replicação.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Quando devo permitir a consistência multi-VM?

Uma vez que a consistência multi-VM é intensiva em CPU, permitindo-lhe afetar o desempenho da carga de trabalho. Utilize a consistência multi-VM apenas se as máquinas estiverem a funcionar a mesma carga de trabalho e precisar de consistência em várias máquinas. Por exemplo, se tiver duas instâncias do SQL Server e dois servidores web numa aplicação, deve ter consistência multi-VM apenas para as instâncias do Servidor SQL.

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>Pode adicionar um VM já replicando a um grupo de replicação?
Pode adicionar um VM a um novo grupo de replicação, permitindo a replicação. Também pode adicionar um VM a um grupo de replicação existente, permitindo a replicação. No entanto, não é possível adicionar um VM já replicado a um novo grupo de replicação ou grupo de replicação existente.
 
## <a name="failover"></a>Ativação pós-falha


### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Como é assegurada a capacidade na região-alvo para os VMs Azure?

A equipa de recuperação do site e o plano de equipa de gestão de capacidade seletiva Azure para uma capacidade de infraestrutura suficiente. Quando inicia uma falha, as equipas também ajudam a garantir que os casos de VM protegidos pela Recuperação do Local serão implantados para a região alvo.

### <a name="is-failover-automatic"></a>A ativação pós-falha é automática?

A ativação pós-falha não é automática. Pode iniciar falhas com um único clique no portal, ou pode utilizar o [PowerShell](azure-to-azure-powershell.md) para desencadear uma falha.

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>Posso manter um endereço IP público depois de uma falha?

Não pode manter o endereço IP público do pedido de produção após uma falha.

Quando se traz uma carga de trabalho como parte do processo de failover, é necessário atribuir um recurso IP público Azure à carga de trabalho. O recurso IP público azure tem de estar disponível na região-alvo. Pode atribuir manualmente o recurso IP público azure, ou pode automatizar com um plano de recuperação. Saiba como [configurar endereços IP públicos após o failover](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>Posso manter um endereço IP privado durante uma falha?

Sim, pode ficar com um endereço IP privado. Por padrão, ao ativar a recuperação de desastres para VMs Azure, a Recuperação do Site cria recursos-alvo baseados em configurações de recursos de origem. Para máquinas virtuais Azure configuradas com endereços IP estáticos, a Recuperação do Site tenta fornecer o mesmo endereço IP para o VM alvo se não estiver a ser utilizado.
Saiba mais sobre [manter endereços IP durante](site-recovery-retain-ip-azure-vm-failover.md)a failover .

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>Depois de uma falha, por que o servidor é atribuído um novo endereço IP?

A Recuperação do Site tenta fornecer o endereço IP no momento da falha. Se outra máquina virtual estiver a tomar esse endereço, a Recuperação do Site define o próximo endereço IP disponível como alvo.

Saiba mais sobre a criação de mapeamento de [rede e endereços IP para redes virtuais.](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Quais são os pontos de recuperação **mais recentes (rpo) mais baixos?**

A **opção Mais Recente (RPO mais baixa)** processa primeiro todos os dados que foram enviados para a Recuperação do Site. Após o serviço processar os dados, cria um ponto de recuperação para cada VM antes de falhar com o VM. Esta opção fornece o menor objetivo de ponto de recuperação (RPO). O VM criado após a falha tem todos os dados replicados para a Recuperação do Site a partir do momento em que a falha foi desencadeada.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Os pontos de recuperação **mais recentes (RPO) mais baixos** têm impacto no RTO falhado?

Sim. A Recuperação do Site processa todos os dados pendentes antes de falhar, pelo que esta opção tem um objetivo de tempo de recuperação mais elevado (RTO) em comparação com outras opções.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>O que significa a última opção **processada** nos pontos de recuperação?

A **mais recente** opção processada falha em todos os VMs no plano para o mais recente ponto de recuperação que a Recuperação do Site processou. Para ver o mais recente ponto de recuperação de um VM específico, verifique os **últimos pontos** de recuperação nas definições vM. Esta opção fornece um RTO baixo, porque não é gasto tempo a processar dados não processados.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>O que acontece se a minha região primária tiver uma paragem inesperada?

Pode desencadear uma falha após a paragem. A Recuperação do Site não precisa de conectividade da região primária para fazer o failover.

### <a name="what-is-an-rto-of-a-vm-failover"></a>O que é um RTO de um VM failover?

A Recuperação do Local tem um [RTO SLA de 2 horas.](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) No entanto, na maior parte do tempo, a Recuperação do Site falha em máquinas virtuais em poucos minutos. Pode calcular o RTO indo para os empregos falhados, que mostram o tempo que levou para trazer a VM. Para o plano de recuperação RTO, consulte a secção seguinte.

## <a name="recovery-plans"></a>Planos de recuperação

### <a name="what-is-a-recovery-plan"></a>O que é um plano de recuperação?

Um plano de recuperação no Site Recovery orquestra a recuperação falhada de VMs. Ajuda a tornar a recuperação consistentemente precisa, repetível e automatizada. Um plano de recuperação aborda as seguintes necessidades:

- Definindo um grupo de máquinas virtuais que falham juntas
- Definindo as dependências entre máquinas virtuais para que a aplicação apareça com precisão
- Automatizar a recuperação juntamente com ações manuais personalizadas para realizar tarefas que não a falha das máquinas virtuais

Saiba mais sobre a criação de planos de [recuperação.](site-recovery-create-recovery-plans.md)

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Como é que a sequenciação é alcançada num plano de recuperação?

Num plano de recuperação, pode criar vários grupos para conseguir a sequenciação. Todos os grupos falham de uma vez. As máquinas virtuais que fazem parte do mesmo grupo falham juntas, seguidas por outro grupo. Para aprender a modelar uma aplicação utilizando um plano de recuperação, consulte [planos de recuperação.](recovery-plan-overview.md#model-apps)

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Como posso encontrar o RTO de um plano de recuperação?

Para verificar o RTO de um plano de recuperação, faça um teste falhado para o plano de recuperação e vá para **os trabalhos**de Recuperação do Local.
No exemplo seguinte, consulte o trabalho **SAPTestRecoveryPlan**. O trabalho demorou 8 minutos e 59 segundos a falhar em todas as máquinas virtuais e a fazer ações especificadas.

![Lista de trabalhos de recuperação do site](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Posso adicionar livros de automação ao plano de recuperação?

Sim, pode integrar os livros de execução da Azure Automation no seu plano de recuperação. Saiba mais sobre a adição de livros de [execução da Automação Azure.](site-recovery-runbook-automation.md)

## <a name="reprotection-and-failback"></a>Reproteção e recuo

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Falhei desde a região primária até uma região de recuperação de desastres. Os VMs numa região de DR são protegidos automaticamente?

Não. Quando se [falha mA](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) Azure de uma região para outra, os VMs começam na região de DR em estado desprotegido. Para não repor os VMs na região primária, é necessário [reproteger](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) os VMs na região secundária.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>No momento da reproteção, a Recuperação do Site replica dados completos da região secundária para a região primária?

Depende da situação. Se a região de origem VM existir, apenas as alterações entre o disco de origem e o disco-alvo são sincronizadas. A Recuperação do Site calcula os diferenciais comparando os discos e, em seguida, transfere os dados. Este processo geralmente leva algumas horas. Para obter mais informações sobre o que acontece durante a reproteção, consulte a Reprotect falhada sobre os casos de [VM Azure para a região primária](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Quanto tempo demora a reativação pós-falha?

Após a reprotecção, o recuo leva cerca do mesmo tempo que leva para falhar da região primária para uma região secundária.

## <a name="capacity"></a><a name="capacity"></a>Capacidade

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Como é assegurada a capacidade na região-alvo para os VMs Azure?

A equipa de recuperação do site e o plano de equipa de gestão de capacidade seletiva Azure para uma capacidade de infraestrutura suficiente. Quando inicia uma falha, as equipas também ajudam a garantir que os casos de VM protegidos pela Recuperação do Local serão implantados para a região alvo.

### <a name="does-site-recovery-work-with-reserved-instances"></a>A Recuperação do Site funciona com instâncias reservadas?

Sim, você pode comprar [VMs Azure reservados](https://azure.microsoft.com/pricing/reserved-vm-instances/) na região de recuperação de desastres, e operações de failover do Site Recovery irão usá-los. Não é necessária nenhuma configuração adicional.

## <a name="security"></a>Segurança

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Os dados de replicação são enviados para o serviço de Recuperação de Sites?

Não, a Recuperação do Site não interceta dados replicados, e não tem nenhuma informação sobre o que está a ser feito nos seus VMs. Apenas os metadados necessários para orquestrar a replicação e a ativação pós-falha são enviados para o serviço de Recuperação de Sites.

A Recuperação do Site é certificada iso 27001:2013, 27018, HIPAA e DPA certificada. O serviço está a ser submetido a avaliações soc2 e FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>A Recuperação de Sites faz encriptação de replicação?

Sim, tanto a encriptação em trânsito como a [encriptação em repouso em Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) são suportadas.

## <a name="next-steps"></a>Passos seguintes

- [Rever os requisitos de suporte Azure-to-Azure](azure-to-azure-support-matrix.md).
- [Instale a replicação Azure-to-Azure](azure-to-azure-tutorial-enable-replication.md).
- Se tiver dúvidas depois de ler este artigo, publique-as no fórum dos Serviços de [Recuperação do Azure.](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)
