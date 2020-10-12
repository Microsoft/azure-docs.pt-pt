---
title: Questões comuns sobre recuperação de desastres em Azure VM com recuperação do local de Azure
description: Este artigo responde a perguntas comuns sobre a recuperação de desastres Azure VM quando utiliza a Recuperação do Site Azure.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 7bc8427a51a9931ca82155232569767f12a8e266
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87534027"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Perguntas comuns: recuperação após desastre do Azure para Azure

Este artigo responde a questões comuns sobre a recuperação de desastres de Azure VMs para outra região de Azure para quando utilizar [a Recuperação do Sítio azul](site-recovery-overview.md).

## <a name="general"></a>Geral

### <a name="how-is-site-recovery-priced"></a>Como é que a Recuperação do Local tem preço?

Rever [os preços de recuperação do local de Azure para VMs](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Como funciona o nível gratuito para a Recuperação do Local de Azure?

Todos os casos protegidos com recuperação do local de Azure são gratuitos durante os primeiros 31 dias de proteção. Após esse período, a proteção para cada instância encontra-se às taxas de fixação do [preço de recuperação do local de Azure para máquinas virtuais Azure](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante os primeiros 31 dias, vou incorrer em outras acusações do Azure?

Sim. Mesmo que a Recuperação do Site Azure seja gratuita durante os primeiros 31 dias de uma instância protegida, poderá incorrer em encargos para o Armazenamento Azure, transações de armazenamento e transferências de dados. Uma Máquina Virtual recuperada também pode incorrer em taxas de computação Azure. Obtenha detalhes completos sobre os preços nos [preços de recuperação do local de Azure](https://azure.microsoft.com/pricing/details/site-recovery).

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Quais são as melhores práticas para a recuperação de desastres das Máquinas Virtuais Azure?

1. [Compreender a arquitetura Azure-to-Azure](azure-to-azure-architecture.md)
1. [Reveja as configurações suportadas e não suportadas](azure-to-azure-support-matrix.md)
1. [Preparar a recuperação de desastres para os VMs do Azure](azure-to-azure-how-to-enable-replication.md)
1. [Executar uma ativação pós-falha de teste](azure-to-azure-tutorial-dr-drill.md)
1. [Falha e falha de volta à região primária](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>Como é assegurada a capacidade na região-alvo?

A equipa de recuperação do local e o plano da equipa de gestão de capacidades da Azure para capacidade de infraestrutura suficiente. Quando iniciar uma falha, as equipas também ajudam a garantir que as instâncias VM protegidas pela Recuperação do Local serão implantadas na região alvo.

## <a name="replication"></a>Replicação

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Posso replicar VMs ativados através da encriptação do disco Azure?

Sim. A Recuperação do Site suporta a recuperação de desastres de VMs que têm encriptação de disco Azure ativada. Quando ativa a replicação, o Azure copia todas as chaves e segredos de encriptação de disco necessários da região de origem para a região alvo no contexto do utilizador. Se não tiver as permissões apropriadas, o seu administrador de segurança pode usar um script para copiar as chaves e segredos.

- A Recuperação do Site suporta a encriptação do disco Azure para VMs Azure que estão a executar o Windows.
- A Recuperação do Site suporta a versão 0.1 de encriptação do disco Azure, que tem um esquema que requer diretório Azure Ative (Azure AD). A Recuperação do Site também suporta a versão 1.1, que não requer Azure AD. [Saiba mais sobre o esquema de extensão para encriptação do disco Azure](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema).
  - Para a versão 1.1 de encriptação do disco Azure, tem de utilizar os VMs do Windows com discos geridos.
  - [Saiba mais](azure-to-azure-how-to-enable-replication-ade-vms.md) sobre como permitir a replicação de VMs encriptados.

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>Posso selecionar uma conta De automação de um grupo de recursos diferente?

Atualmente não é suportado via portal, mas pode escolher uma conta Demôm automação de um grupo de recursos diferente através da Powershell.

### <a name="after-specifying-an-automation-account-that-is-in-a-different-resource-group-than-the-vault-am-i-permitted-to-delete-the-runbook-if-there-is-no-other-vault-to-specify"></a>Depois de especificar uma conta Automation que está num grupo de recursos diferente do cofre, posso apagar o livro de execução se não houver outro cofre para especificar?

O runbook personalizado criado é uma ferramenta e é seguro apagar se o mesmo já não for necessário.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Posso replicar VMs para outra subscrição?

Sim, você pode replicar VMs Azure para uma subscrição diferente dentro do mesmo inquilino AZure AD.

Configure a recuperação de [desastres através](https://azure.microsoft.com/blog/cross-subscription-dr) de subscrições selecionando outra subscrição no momento da replicação.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Posso replicar VMs Azure presos em zona para outra região?

Sim, pode [replicar VMs presos](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) em zona para outra região.

### <a name="can-i-replicate-vms-in-a-region-that-has-zones-from-non-zone-to-zonal-configuration"></a>Posso replicar VMs numa região que tem zonas de não-zona para configuração zonal?

Não, isto não é apoiado hoje. Como uma solução alternativa, pode replicar o VM usando o ASR para uma configuração zonal noutra região e, em seguida, desativar a replicação. Em seguida, reesibilizar a replicação daquela região para a região original, e escolher uma configuração zonal para failover.

### <a name="can-i-exclude-disks"></a>Posso excluir discos?

Sim, pode excluir discos no momento da proteção utilizando o PowerShell. Para obter mais informações, consulte [como excluir discos da replicação.](azure-to-azure-exclude-disks.md)

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Posso adicionar discos novos a VMs replicados e permitir a replicação para eles?

Sim, adicionar novos discos a VMs replicados e permitir a replicação para eles é suportado para VMs Azure com discos geridos. Quando se adiciona um novo disco a um VM Azure que está habilitado para a replicação, a saúde de replicação para o VM mostra um aviso. Este aviso indica que um ou mais discos no VM estão disponíveis para proteção. Pode ativar a replicação para discos adicionados.

- Se ativar a proteção dos discos adicionados, o aviso desaparecerá após a replicação inicial.
- Se não ativar a replicação do disco, pode dispensar o aviso.
- Se falhar sobre um VM que tenha um disco adicionado e replicação ativado, existem pontos de replicação. Os pontos de replicação mostrarão os discos disponíveis para recuperação.

Por exemplo, digamos que um VM tem um único disco e adiciona-se um novo. Pode haver um ponto de replicação que foi criado antes de adicionar o disco. Este ponto de replicação mostrará que consiste em "1 de 2 discos".

A Recuperação do Site não suporta a "remoção a quente" de um disco de um VM replicado. Se remover um disco VM, tem de desativar e, em seguida, voltar a ativar a replicação para o VM.

### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência posso replicar-me ao Azure?

A replicação é contínua quando se está a replicar VMs Azure para outra região de Azure. Para mais informações, consulte a [arquitetura de replicação Azure-to-Azure.](./azure-to-azure-architecture.md#replication-process)

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>Posso replicar máquinas virtuais dentro de uma região? Preciso desta funcionalidade para migrar VMs.

Não é possível utilizar uma solução de recuperação de disco Azure-to-Azure para replicar VMs dentro de uma região.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Posso replicar os casos de VM em qualquer região de Azure?

Ao utilizar a Recuperação do Local, pode replicar e recuperar VMs entre duas regiões dentro do mesmo cluster geográfico. Os aglomerados geográficos são definidos com a latência dos dados e a soberania em mente. Para obter mais informações, consulte a matriz de suporte da [região](./azure-to-azure-support-matrix.md#region-support)de Recuperação do Local.

### <a name="does-site-recovery-require-internet-connectivity"></a>A Recuperação do Site requer conectividade à Internet?

Não, a Recuperação do Site não requer conectividade na Internet. Mas requer acesso a URLs de Recuperação de Sítios e gamas IP, como mencionado em rede na recuperação de [desastres Azure VM](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>Posso replicar uma aplicação que tem um grupo de recursos separado para níveis separados?

Sim, pode replicar a aplicação e manter a configuração de recuperação de desastres num grupo de recursos separado também.

Por exemplo, se a sua aplicação tiver a aplicação, base de dados e web de cada nível num grupo de recursos separados, então terá de selecionar o [assistente de replicação](./azure-to-azure-how-to-enable-replication.md#enable-replication) três vezes para proteger todos os níveis. A Recuperação do Site irá replicar estes três níveis em três grupos de recursos diferentes.

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>Posso mover contas de armazenamento entre grupos de recursos?

Não, este é um cenário não apoiado. No entanto, se mover acidentalmente as contas de armazenamento para um grupo de recursos diferentes e eliminar o grupo de recursos originais, então pode criar um novo grupo de recursos com o mesmo nome que o antigo grupo de recursos e, em seguida, mover a conta de armazenamento para este grupo de recursos.

## <a name="replication-policy"></a>Política de replicação

### <a name="what-is-a-replication-policy"></a>O que é uma política de replicação?

Uma política de replicação define as definições para o histórico de retenção dos pontos de recuperação. A política também define a frequência de instantâneos consistentes com aplicações. Por predefinição, a Azure Site Recovery cria uma nova política de replicação com definições predefinidas de:

- 24 horas para o histórico de retenção de pontos de recuperação.
- 4 horas para a frequência de instantâneos consistentes com aplicações.

[Saiba mais sobre as definições de replicação.](./azure-to-azure-tutorial-enable-replication.md#configure-replication-settings)

### <a name="what-is-a-crash-consistent-recovery-point"></a>O que é um ponto de recuperação consistente?

Um ponto de recuperação consistente com falhas tem os dados do disco como se tivesse retirado o cabo de alimentação do servidor durante a imagem. O ponto de recuperação consistente não inclui nada que estivesse na memória quando a foto foi tirada.

Hoje, a maioria das aplicações pode recuperar bem de instantâneos consistentes. Um ponto de recuperação consistente com falhas é geralmente suficiente para sistemas operativos sem base de dados e aplicações como servidores de ficheiros, servidores DHCP e servidores de impressão.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Qual é a frequência da geração de pontos de recuperação consistentes?

A Recuperação do Local cria um ponto de recuperação consistente a cada 5 minutos.

### <a name="what-is-an-application-consistent-recovery-point"></a>O que é um ponto de recuperação consistente de aplicações?

Os pontos de recuperação consistentes da aplicação são criados a partir de instantâneos consistentes com aplicações. Os pontos de recuperação consistentes da aplicação captam os mesmos dados que os instantâneos consistentes com falhas, ao mesmo tempo que capturam dados na memória e todas as transações em processo.

Devido ao seu conteúdo extra, as fotos consistentes com aplicações são as mais envolvidas e demoram mais tempo. Recomendamos pontos de recuperação consistentes de aplicações para sistemas operativos de base de dados e aplicações como o SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Qual é o impacto dos pontos de recuperação consistentes da aplicação no desempenho da aplicação?

Os pontos de recuperação consistentes da aplicação captam todos os dados na memória e no processo. Como os pontos de recuperação capturam esses dados, eles requerem uma estrutura como o Serviço de Cópia Sombra de Volume no Windows para quiesce a aplicação. Se o processo de captura for frequente, pode afetar o desempenho quando a carga de trabalho já está ocupada. Não recomendamos que utilize baixa frequência para pontos de recuperação consistentes com aplicações para cargas de trabalho não-base. Mesmo para a carga de trabalho da base de dados, uma hora é suficiente.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Qual é a frequência mínima de geração de pontos de recuperação consistentes de aplicação?

A Recuperação do Site pode criar um ponto de recuperação consistente com uma frequência mínima de 1 hora.

### <a name="how-are-recovery-points-generated-and-saved"></a>Como são gerados e salvos pontos de recuperação?

Para entender como a Recuperação do Site gera pontos de recuperação, vamos ver um exemplo de uma política de replicação. Esta política de replicação tem um ponto de recuperação com uma janela de retenção de 24 horas e um instantâneo de frequência consistente de 1 hora.

A Recuperação do Local cria um ponto de recuperação consistente a cada 5 minutos. Não pode mudar esta frequência. Durante a última hora, pode escolher entre 12 pontos consistentes com falhas e 1 ponto consistente com aplicações. À medida que o tempo avança, a Recuperação do Local adcade todos os pontos de recuperação para além da última hora e poupa apenas 1 ponto de recuperação por hora.

A imagem que se segue ilustra o exemplo. Na imagem:

- Na última hora, há pontos de recuperação com uma frequência de 5 minutos.
- Além da última hora, a Recuperação do Local mantém apenas 1 ponto de recuperação.

   ![Lista de pontos de recuperação gerados](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Até onde posso me recuperar?

O ponto de recuperação mais antigo que pode usar é de 72 horas.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Tenho uma política de replicação de 24 horas. O que acontecerá se um problema impedir a Recuperação do Local de gerar pontos de recuperação por mais de 24 horas? Os meus pontos de recuperação anteriores vão perder-se?

Não, a Recuperação do Site manterá todos os seus pontos de recuperação anteriores. Dependendo da janela de retenção dos pontos de recuperação, a Recuperação do Local substitui o ponto mais antigo apenas se gerar novos pontos. Devido ao problema, a Recuperação do Site não pode gerar novos pontos de recuperação. Até que haja novos pontos de recuperação, todos os pontos antigos permanecerão depois de chegar à janela de retenção.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Depois de a replicação ser ativada num VM, como posso alterar a política de replicação?

Aceda às políticas **de**  >  replicação da infraestrutura do local**de recuperação do local de recuperação do local.**  >  **Replication policies** Selecione a política que pretende editar e guarde as alterações. Qualquer alteração aplicar-se-á também a todas as replicações existentes.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Todos os pontos de recuperação são uma cópia completa do VM ou um diferencial?

O primeiro ponto de recuperação gerado tem a cópia completa. Quaisquer pontos de recuperação sucessivos têm mudanças delta.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>O aumento do período de retenção dos pontos de recuperação aumenta o custo de armazenamento?

Sim, se aumentar o período de retenção de 24 horas para 72 horas, a Recuperação do Local poupará os pontos de recuperação por mais 48 horas. O tempo adicional incorrerá em taxas de armazenamento. Por exemplo, um único ponto de recuperação pode ter alterações delta de 10 GB com um custo por GB de $0,16 por mês. Os custos adicionais seriam $1,60 × 48 por mês.

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>Posso permitir a replicação com consistência de aplicações nos servidores Linux?

Sim. A Azure Site Recovery for Linux Operation System suporta scripts personalizados de aplicações para consistência de aplicações. O script personalizado com pré e pós-opções será usado pelo Agente de Mobilidade de Recuperação do Site Azure durante a consistência da aplicação. [Saiba mais](./site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

## <a name="multi-vm-consistency"></a>Consistência multi-VM

### <a name="what-is-multi-vm-consistency"></a>O que é consistência multi-VM?

A consistência multi-VM garante que o ponto de recuperação é consistente em todas as máquinas virtuais replicadas.

A Recuperação do Site fornece uma opção **de consistência Multi-VM,** que cria um grupo de replicação de todas as máquinas.

Quando falhares nas máquinas virtuais, elas terão partilhado pontos de recuperação consistentes e consistentes com aplicações.

Passe pelo tutorial para permitir a [consistência multi-VM](./azure-to-azure-tutorial-enable-replication.md#enable-replication-for-a-vm).

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Posso falhar com uma única máquina virtual dentro de um grupo de replicação de consistência multi-VM?

Ao selecionar a opção **de consistência Multi-VM,** afirma que a aplicação tem uma dependência de todas as máquinas virtuais dentro de um grupo. A falha da máquina virtual não é permitida.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Quantas máquinas virtuais posso replicar como parte de um grupo de replicação de consistência multi-VM?

Pode replicar 16 máquinas virtuais juntas num grupo de replicação.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Quando devo permitir a consistência multi-VM?

Como a consistência multi-VM é intensiva em CPU, permitindo que possa afetar o desempenho da carga de trabalho. Utilize consistência multi-VM apenas se as máquinas estiverem a executar a mesma carga de trabalho e necessitar de consistência em várias máquinas. Por exemplo, se tiver duas instâncias do SQL Server e dois servidores web numa aplicação, deverá ter consistência multi-VM apenas para as instâncias do SQL Server.

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>Pode adicionar um VM já replicado a um grupo de replicação?
Pode adicionar um VM a um novo grupo de replicação, permitindo a replicação. Também pode adicionar um VM a um grupo de replicação existente, permitindo a replicação. No entanto, não é possível adicionar um VM já replicado a um novo grupo de replicação ou a um grupo de replicação existente.
 
## <a name="failover"></a>Ativação pós-falha


### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Como é assegurada a capacidade na região-alvo dos VMs do Azure?

A equipa de recuperação do local e o plano da equipa de gestão de capacidades da Azure para capacidade de infraestrutura suficiente. Quando iniciar uma falha, as equipas também ajudam a garantir que as instâncias VM protegidas pela Recuperação do Local serão implantadas na região alvo.

### <a name="is-failover-automatic"></a>A ativação pós-falha é automática?

A ativação pós-falha não é automática. Pode iniciar falhas com um único clique no portal, ou pode utilizar o [PowerShell](azure-to-azure-powershell.md) para desencadear uma falha.

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>Posso manter um endereço IP público depois de uma falha?

Não é possível manter o endereço IP público da aplicação de produção depois de uma falha.

Quando você traz uma carga de trabalho como parte do processo de failover, você precisa atribuir um recurso IP público Azure à carga de trabalho. O recurso IP público Azure tem de estar disponível na região-alvo. Pode atribuir manualmente o recurso IP público Azure ou automatizá-lo com um plano de recuperação. Saiba como [configurar endereços IP públicos após o failover](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>Posso manter um endereço IP privado durante uma falha?

Sim, pode ficar com um endereço IP privado. Por predefinição, quando permite a recuperação de desastres para VMs Azure, a Recuperação do Site cria recursos-alvo baseados nas definições de recursos de origem. Para máquinas virtuais Azure configuradas com endereços IP estáticos, a Recuperação do Site tenta providenciar o mesmo endereço IP para o VM-alvo se não estiver a ser utilizado.
Saiba como [manter os endereços IP durante o failover](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>Depois de uma falha, por que razão o servidor é atribuído um novo endereço IP?

A Recuperação do Site tenta fornecer o endereço IP no momento da falha. Se outra máquina virtual estiver a tomar esse endereço, a Recuperação do Site define o próximo endereço IP disponível como alvo.

Saiba mais sobre [a criação de mapeamento de rede e endereço IP para redes virtuais.](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Quais são os pontos de recuperação **mais recentes (mais baixos de RPO)** de recuperação?

A opção **mais recente (mais baixa RPO)** processa primeiro todos os dados que foram enviados para a Recuperação do Site. Após o serviço processar os dados, cria um ponto de recuperação para cada VM antes de falhar para o VM. Esta opção fornece o objetivo de ponto de recuperação mais baixo (RPO). O VM criado após o failover tem todos os dados replicados para a Recuperação do Local a partir do momento em que a falha foi desencadeada.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Os últimos pontos de recuperação **(rpo mais baixos)** têm impacto no RTO de falha?

Sim. A Recuperação do Site processa todos os dados pendentes antes de falhar, pelo que esta opção tem um maior objetivo de tempo de recuperação (RTO) em comparação com outras opções.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>O que significa a última opção **processada** em pontos de recuperação?

A última opção **processada** falha em todos os VMs do plano até ao último ponto de recuperação que a Recuperação do Site processou. Para ver o último ponto de recuperação de um VM específico, consulte os **pontos de recuperação mais recentes** nas definições de VM. Esta opção fornece um RTO baixo, porque não é gasto tempo a processar dados não processados.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>O que acontece se a minha região primária sofrer uma paragem inesperada?

Pode desencadear uma falha após a paragem. A Recuperação do Local não precisa de conectividade da região primária para fazer o failover.

### <a name="what-is-an-rto-of-a-vm-failover"></a>O que é um RTO de um VM failover?

A Recuperação do Site tem um [RTO SLA de 2 horas.](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) No entanto, na maior parte do tempo, a Recuperação do Site falha sobre máquinas virtuais em minutos. Pode calcular o RTO indo para os trabalhos de failover, que mostram o tempo que demorou a trazer o VM. Para o plano de recuperação RTO, consulte a secção seguinte.

## <a name="recovery-plans"></a>Planos de recuperação

### <a name="what-is-a-recovery-plan"></a>O que é um plano de recuperação?

Um plano de recuperação no Local de Recuperação orquestra a recuperação falhada dos VMs. Ajuda a tornar a recuperação consistentemente precisa, repetível e automatizada. Um plano de recuperação aborda as seguintes necessidades:

- Definindo um grupo de máquinas virtuais que falham juntas
- Definindo as dependências entre máquinas virtuais para que a aplicação apareça com precisão
- Automatizar a recuperação juntamente com ações manuais personalizadas para realizar tarefas que não o failover de máquinas virtuais

Saiba mais [sobre a criação de planos de recuperação.](site-recovery-create-recovery-plans.md)

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Como é que a sequência é conseguida num plano de recuperação?

Num plano de recuperação, pode criar vários grupos para alcançar a sequência. Todos os grupos falham de uma vez. Máquinas virtuais que fazem parte do mesmo grupo falham juntas, seguidas por outro grupo. Para aprender a modelar uma aplicação utilizando um plano de recuperação, consulte [Planos de recuperação](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Como posso encontrar o RTO de um plano de recuperação?

Para verificar o RTO de um plano de recuperação, faça um teste de failover para o plano de recuperação e vá para **os trabalhos de Recuperação do Local**.
No exemplo seguinte, consulte o trabalho **SAPTestRecoveryPlan**. O trabalho demorou 8 minutos e 59 segundos a falhar em todas as máquinas virtuais e a fazer ações especificadas.

![Lista de trabalhos de recuperação de locais](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Posso adicionar livros de automação ao plano de recuperação?

Sim, pode integrar os runbooks da Azure Automation no seu plano de recuperação. Saiba mais sobre [a adição de livros de azure Automation](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Reproteção e recuo

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Falhei da região primária para uma região de recuperação de desastres. Os VMs numa região dr são protegidos automaticamente?

N.º Quando [se falha sobre](./azure-to-azure-tutorial-failover-failback.md) os VMs de uma região para outra, os VMs começam na região dr em um estado desprotegido. Para reprotecer os VMs para a região primária, é necessário [reprotecer](./azure-to-azure-how-to-reprotect.md) os VMs na região secundária.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>No momento da reprotecção, a Recuperação do Sítio replica dados completos da região secundária para a região primária?

Depende da situação. Se a região de origem VM existir, apenas as alterações entre o disco de origem e o disco-alvo são sincronizadas. A Recuperação do Site calcula os diferenciais comparando os discos e, em seguida, transfere os dados. Este processo geralmente leva algumas horas. Para obter mais informações sobre o que acontece durante a reproteção, consulte [Reprotect falhou sobre as instâncias VM de Azure para a região primária.](./azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection)

### <a name="how-much-time-does-it-take-to-fail-back"></a>Quanto tempo demora a reativação pós-falha?

Após a reproteção, o recuo leva cerca do mesmo tempo que leva para falhar da região primária para uma região secundária.

## <a name="capacity"></a><a name="capacity"></a>Capacidade

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Como é assegurada a capacidade na região-alvo dos VMs do Azure?

A equipa de recuperação do local e o plano da equipa de gestão de capacidades da Azure para capacidade de infraestrutura suficiente. Quando iniciar uma falha, as equipas também ajudam a garantir que as instâncias VM protegidas pela Recuperação do Local serão implantadas na região alvo.

### <a name="does-site-recovery-work-with-reserved-instances"></a>A Recuperação do Local funciona com instâncias reservadas?

Sim, você pode comprar [VMs Azure reservados](https://azure.microsoft.com/pricing/reserved-vm-instances/) na região de recuperação de desastres, e as operações de failover de recuperação de locais irão usá-los. Não é necessária nenhuma configuração adicional.

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
