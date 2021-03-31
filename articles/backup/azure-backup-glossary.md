---
title: Glossário de backup Azure
description: Este artigo define termos úteis para uso com Azure Backup.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: fb46415c8bdb463556d57004e37d741c1b9a9b57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102502030"
---
# <a name="azure-backup-glossary"></a>Glossário de backup Azure

Este glossário de termos pode ser útil quando se utiliza a Cópia de Segurança Azure.

>[!NOTE]
>
> - Os termos que são marcados com o prefixo "(termo específico da carga de trabalho)" referem-se aos termos que só são relevantes no contexto de um subconjunto específico de cargas de trabalho que o Azure Backup suporta.
> - Para termos que são normalmente utilizados na documentação de Backup Azure, mas que se referem a outros serviços Azure, é fornecida uma ligação direta à documentação do serviço Azure relevante.

## <a name="afs-azure-file-shares"></a>AFS (Azure File shares)

Consulte a documentação do [Azure Files](../storage/files/storage-files-introduction.md).

## <a name="alternate-location-recovery"></a>Recuperação de localização alternativa

Uma recuperação feita do ponto de recuperação para um local diferente do local original onde os backups foram levados. Ao utilizar a cópia de segurança Azure VM, isto significaria restaurar o VM num servidor diferente do servidor original onde as cópias de segurança foram recolhidas. Ao utilizar a cópia de segurança da partilha do Ficheiro Azure, isto significaria restaurar os dados para uma partilha de ficheiros diferente da partilha de ficheiros com cópia de segurança.

## <a name="application-consistent-backup"></a>Backup consistente da aplicação

(Termo específico para a carga de trabalho)

As cópias de segurança consistentes da aplicação captam o conteúdo da memória e as operações de I/O pendentes. Os instantâneos consistentes com aplicações utilizam um escritor [VSS](#vss-windows-volume-shadow-copy-service) VSS (ou scripts pré ou post para Linux) para garantir a consistência dos dados da aplicação antes de ocorrer uma cópia de segurança. [Saiba mais](backup-azure-vms-introduction.md).

## <a name="azure-resource-manager-arm-templates"></a>Modelos do Azure Resource Manager (ARM)

Consulte a [documentação dos modelos ARM](../azure-resource-manager/templates/overview.md).

## <a name="autoprotection-for-databases"></a>Autoproteção (para bases de dados)

(Termo específico para a carga de trabalho)

A autoproteção é uma capacidade que permite proteger automaticamente todas as bases de dados numa instância autónoma do SQL Server ou num grupo de disponibilidade SQL Server Always On. Não só permite cópias de segurança para as bases de dados existentes, como também protege todas as bases de dados que poderá adicionar no futuro.

## <a name="availability-storage-replication-types"></a>Disponibilidade (tipos de replicação de armazenamento)

O Azure Backup oferece três tipos de replicação para manter o seu armazenamento e dados altamente disponíveis:

### <a name="lrs"></a>LRS

[O armazenamento localmente redundante (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) replica os seus dados de backup três vezes (cria três cópias dos seus dados de backup) numa unidade de escala de armazenamento num datacenter. Todas as cópias dos dados de reserva existem na mesma região. O LRS é uma opção de baixo custo para proteger os seus dados de backup de falhas de hardware locais.

### <a name="grs"></a>GRS

[O armazenamento geo-redundante (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) é a opção de replicação padrão e recomendada. O GRS replica os seus dados de reserva para uma região secundária, a centenas de quilómetros da localização primária dos dados de origem. GrS custa mais do que LRS, mas GRS fornece um nível mais alto de durabilidade para os seus dados de backup, mesmo que haja uma paragem regional.

>[!NOTE]
>Para os cofres GRS que tenham a função de restauro transversal ativada, o armazenamento de backup é atualizado de GRS para RA-GRS (Read-Access Geo-Redundant Storage).

### <a name="zrs"></a>ZRS

[O armazenamento redundante de zona (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) replica os seus dados de backup em [zonas de disponibilidade,](../availability-zones/az-overview.md#availability-zones)garantindo residência de dados de backup e resiliência na mesma região. Assim, as suas cargas de trabalho críticas que requerem [residência de dados](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) podem ser apoiadas em ZRS.

## <a name="azure-command-line-interface-cli"></a>Interface de Linha de Comandos do Azure (CLI)

Consulte a [documentação do Azure CLI](/cli/azure/what-is-azure-cli).

## <a name="azure-policy"></a>Azure Policy

Consulte a [documentação da Política Azure](../governance/policy/overview.md).

## <a name="azure-powershell"></a>Azure PowerShell

Consulte a [documentação da Azure PowerShell](/powershell/azure/).

## <a name="azure-resource-manager-arm"></a>Gestor de Recursos Azure (ARM)

Consulte a [documentação do Gestor de Recursos Azure](../azure-resource-manager/management/overview.md).

## <a name="azure-disk-encryption-ade"></a>ADE (Azure Disk Encryption)

Consulte a documentação de [encriptação do disco Azure](../security/fundamentals/azure-disk-encryption-vms-vmss.md).

## <a name="backend-storage--cloud-storage--backup-storage"></a>Armazenamento de backend / Armazenamento em nuvem / armazenamento de backup

O armazenamento real usado por uma instância de reserva. Inclui o tamanho de todos os pontos de retenção que existem para a instância de backup (conforme definido pela política de backup e retenção).

## <a name="bare-metal-backup"></a>Backup de metal nu

Cópia de segurança dos ficheiros do sistema operativo e de todos os dados relativos a volumes críticos, com exceção dos dados do utilizador. Por definição, uma cópia de segurança de metal nu inclui uma cópia de segurança do estado do sistema. Fornece proteção quando um computador não arranca e tem que recuperar tudo. [Saiba mais](backup-mabs-system-state-and-bmr.md).

## <a name="backup-extensions--vm-extensions"></a>Extensões de backup / extensões VM

(Específico do tipo de carga de trabalho Azure VM)

As extensões de máquina virtual (VM) do Azure são pequenas aplicações que proporcionam tarefas de automação e configuração pós-implementação nas VMs do Azure. O Azure Backup apoia os VMs Azure instalando uma extensão ao agente Azure VM em funcionamento na máquina. O Azure Backup gere automaticamente estas extensões e os utilizadores não precisam de atualizar manualmente estas extensões.

## <a name="backup-instance--backup-item"></a>Instância de backup / Item de backup

Uma fonte de dados apoiada até um cofre com uma determinada política de backup e retenção constitui uma instância de backup ou um item de backup.

## <a name="backup-rule--backup-policy"></a>Regra de backup / Política de backup

Uma regra de backup é uma regra definida pelo utilizador que especifica quando e quantas vezes as cópias de segurança devem ser realizadas numa fonte de dados. Para alguns tipos de carga de trabalho, a política de backup também fornece uma forma de especificar o método instantâneo a aplicar na fonte de dados (completo, incremental, diferencial). As políticas de backup são muitas vezes criadas como uma combinação de regras de backup e regras de retenção.

## <a name="backup-vault"></a>Cofre de cópias de segurança

Um recurso Azure Resource Manager do tipo *Microsoft.DataProtection/BackupVaults*. Atualmente, os cofres de backup são usados para fazer backup de bases de dados Azure para o Servidor PostgreSQL. [Saiba mais sobre cofres de reserva.](backup-azure-recovery-services-vault-overview.md)

## <a name="bcdr-business-continuity-and-disaster-recovery"></a>BCDR (Continuidade de Negócios e Recuperação de Desastres)

O BCDR envolve um conjunto de processos que uma organização deve adotar para garantir que as aplicações e cargas de trabalho estão a funcionar durante o serviço planeado e não planeado ou paragens do Azure, com perturbações mínimas do negócio. [Saiba mais](https://azure.microsoft.com/solutions/backup-and-disaster-recovery/#overview) sobre os vários serviços que a Azure oferece para ajudá-lo a criar uma estratégia de BCDR sonora.

## <a name="churn"></a>Churn

A percentagem de variação nos dados sendo apoiada entre dois backups consecutivos. Isto pode ser devido à adição de novos dados, ou à modificação ou eliminação dos dados existentes.

## <a name="crash-consistent-backup"></a>Backup consistente com acidentes

(Termo específico para a carga de trabalho)

Normalmente, ocorrem instantâneos consistentes em acidentes se um VM Azure desligar no momento da cópia de segurança. Apenas os dados que já existem no disco no momento da cópia de segurança são capturados e apoiados. [Saiba mais](backup-azure-vms-introduction.md#snapshot-consistency).

## <a name="cross-region-restore-crr"></a>Restauro cross-region (CRR)

Como uma das [opções](backup-azure-arm-restore-vms.md#restore-options)de restauro, o Cross Region Restore (CRR) permite restaurar itens de backup numa região secundária, que é uma [região emparelhada Azure.](../best-practices-availability-paired-regions.md#what-are-paired-regions)

## <a name="data-box"></a>Caixa de dados

Consulte a [documentação da caixa de dados.](../databox/data-box-overview.md)

## <a name="datasource"></a>Origem de dados

Um recurso (recurso Azure, recursos de procuração ou recurso no local) que é um candidato para ser apoiado. Por exemplo, uma Azure VM ou uma partilha de FicheiroS Azure.

## <a name="dpm-data-protection-manager"></a>DPM (Gestor de Proteção de Dados)

(Termo específico para a carga de trabalho)

Consulte a documentação do [DPM.](/system-center/dpm/dpm-overview)

## <a name="expressroute"></a>ExpressRoute

Consulte a [documentação ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="file-system-consistent-backup"></a>Backup consistente do sistema de ficheiros

(Termo específico para a carga de trabalho)

As cópias de segurança consistentes do sistema de ficheiros proporcionam consistência, tirando uma imagem instantânea de todos os ficheiros ao mesmo tempo. [Saiba mais](backup-azure-vms-introduction.md#snapshot-consistency).

## <a name="frontend-storage--source-size"></a>Armazenamento frontend / Tamanho da fonte

O tamanho dos dados a ser apoiado para uma fonte de dados. O tamanho frontal de uma fonte de dados determina a contagem [de Instâncias Protegidas.](#protected-instance)

## <a name="full-backup"></a>Cópia de segurança completa

Em cópias de segurança completas, uma cópia de toda a fonte de dados é armazenada para cada cópia de segurança.

## <a name="gfs-backup-policy"></a>Política de backup GFS

Uma política de backup GFS (Avô-pai-filho) é uma política que lhe permite definir horários de backup semanais, mensais e anualmente, além do horário diário de backup. Os backups semanais são conhecidos como "filhos", os backups mensais são conhecidos como "pais" e os backups anual são conhecidos como "avós". Cada um destes conjuntos de cópias de backup pode ser configurado para ser conservado por diferentes durações de tempo, permitindo uma maior personalização das escolhas de retenção para cópias de backup. As políticas GFS são úteis para reter cópias de segurança por um longo período de tempo de uma forma mais eficiente em termos de armazenamento.

## <a name="iaas-vms--azure-vms"></a>IaaS VMs / Azure VMs

Consulte a [documentação do Azure VM](../virtual-machines/index.yml).

## <a name="incremental-backup"></a>Cópia de segurança incremental

As cópias de segurança incrementais armazenam apenas os blocos que mudaram desde a cópia de segurança anterior.

## <a name="instant-restore"></a>Restauro instantâneo

(Termo específico da carga de trabalho) A restauração instantânea envolve restaurar uma máquina diretamente a partir do seu instantâneo de reserva e não da cópia do instantâneo no cofre. As restaurações instantâneas são mais rápidas do que as restaurações de um cofre. O número de pontos de restauro instantâneos disponíveis depende da duração de retenção configurada para instantâneos. Atualmente aplicável apenas para backup Azure VM.

## <a name="iops"></a>IOPS

Operações de entrada/saída por segundo.

## <a name="item-level-restore"></a>Restauro do nível do item

(Termo específico para a carga de trabalho)

Restaurar ficheiros ou pastas individuais dentro da máquina a partir do ponto de recuperação.

## <a name="job"></a>Tarefa

Uma tarefa relacionada com cópias de segurança que é criada por um utilizador ou pelo serviço de Backup Azure. Os postos de trabalho podem ser programados ou a pedido (ad-hoc). Existem diferentes tipos de empregos - apoio, restauro, proteção de configuração, e assim por diante. [Saiba mais sobre empregos.](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault)

## <a name="mabs--azure-backup-server"></a>Servidor de backup MABS / Azure

(Termo específico para a carga de trabalho)

Com o Azure Backup Server, pode proteger as cargas de trabalho da aplicação tais como Hiper-V VMs, Microsoft SQL Server, SharePoint Server, Microsoft Exchange e clientes Windows a partir de uma única consola. Herda grande parte da funcionalidade de backup de carga de trabalho da DPM, mas com algumas diferenças. [Saiba mais](backup-azure-microsoft-azure-backup.md)

## <a name="managed-disks"></a>Managed disks

Consulte a documentação dos [discos geridos.](../virtual-machines/managed-disks-overview.md)

## <a name="mars-agent"></a>Agente MARS

(Termo específico para a carga de trabalho)

Também conhecido como agente de backup ou **agente de serviços de recuperação,** o agente MARS é utilizado pela Azure Backup para fazer cópias de segurança de máquinas de instalações e VMs Azure para um cofre de serviços de recuperação de backup em Azure.  [Saiba mais](backup-support-matrix-mars-agent.md).

## <a name="nsg-network-security-group"></a>NSG (Grupo de Segurança da Rede)

Consulte a documentação do [NSG.](../virtual-network/network-security-groups-overview.md)

## <a name="offline-seeding"></a>Sementeira offline

A sementeira offline refere-se ao processo de transferência da cópia de segurança inicial (completa) offline, sem a utilização de largura de banda de rede. Fornece um mecanismo para copiar dados de backup em dispositivos de armazenamento físico, que são depois enviados para um datacenter Azure próximo e enviados para um cofre dos Serviços de Recuperação. [Saiba mais](offline-backup-overview.md).

## <a name="on-demand-backup--ad-hoc-backup"></a>Backup a pedido / backup ad hoc

Um trabalho de backup que é desencadeado por um utilizador numa base única de necessidade, e não baseado no horário de backup (política) que foi configurado para o recurso.

## <a name="original-location-recovery-olr"></a>Recuperação original da localização (OLR)

Uma recuperação feita do ponto de restauro para o local de origem de onde foram tomadas as cópias de segurança, substituindo-a pelo estado armazenado no ponto de recuperação. Ao utilizar a cópia de segurança Azure VM, isto significaria restaurar o VM para o servidor original onde as cópias de segurança foram tomadas. Ao utilizar a cópia de segurança da partilha do Ficheiro Azure, isto significaria restaurar os dados para a partilha de ficheiros com cópia de segurança.

## <a name="passphrase"></a>Frase-passe

(Termo específico para a carga de trabalho)

Uma palavra-passe é usada para encriptar e desencriptar dados enquanto faz o backup ou restauração do seu local ou máquina local usando o agente MARS de ou para Azure.

## <a name="private-endpoint"></a>Ponto final privado

Consulte a [documentação private endpoint](../private-link/private-endpoint-overview.md).

## <a name="protected-instance"></a>Instância protegida

Um caso protegido refere-se ao servidor informático, físico ou virtual que utiliza para configurar a cópia de segurança do Azure.  Do **ponto de vista da faturação,** o Conde de Instância Protegida para uma máquina é uma função do seu tamanho dianteiro. Assim, uma única instância de backup (como um VM apoiado até Azure) pode corresponder a múltiplas instâncias protegidas, dependendo do seu tamanho frontal. [Saiba mais](https://azure.microsoft.com/pricing/details/backup/).

## <a name="rbac-role-based-access-control"></a>RBAC (Controlo de acesso baseado em funções)

Consulte a documentação do [RBAC.](../role-based-access-control/overview.md)

## <a name="recovery-point-restore-point-retention-point--point-in-time-pit"></a>Ponto de recuperação/ Ponto de restauro/ Ponto de retenção / Ponto no tempo (PIT)

Uma cópia dos dados originais que está a ser cópia. Um ponto de retenção está associado a uma marca de tempo para que possa usá-lo para restaurar um item num determinado ponto do tempo.

## <a name="recovery-services-vault"></a>Cofre dos Serviços de Recuperação

Um recurso Azure Resource Manager do tipo *Microsoft.RecoveryServices/vaults*. Atualmente, os cofres dos Serviços de Recuperação são utilizados para apoiar as seguintes cargas de trabalho: VMs Azure, SQL em VMs Azure, SAP HANA em VMs Azure e ações do Azure File. Também é usado para fazer backup de cargas de trabalho no local usando MARS, Azure Backup Server (MABS) e System Center DPM. [Saiba mais sobre cofres dos Serviços de Recuperação.](backup-azure-recovery-services-vault-overview.md)

## <a name="resource-group"></a>Grupo de recursos

Consulte a documentação do [Gestor de Recursos Azure](../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

## <a name="rest-api"></a>API REST

Consulte a documentação da [Azure REST API](/rest/api/azure/).

## <a name="retention-rule"></a>Regra de retenção

Uma regra definida pelo utilizador que especifica quanto tempo de cópia de segurança deve ser mantida.

## <a name="rpo-recovery-point-objective"></a>RPO (Objetivo do Ponto de Recuperação)

A RPO indica a perda máxima de dados que é possível num cenário de perda de dados. Isto é determinado pela frequência de reserva.

## <a name="rto-recovery-time-objective"></a>RTO (Objetivo do Tempo de Recuperação)

O RTO indica o tempo máximo possível em que os dados podem ser restaurados até ao último ponto de tempo disponível após um cenário de perda de dados.

## <a name="scheduled-backup"></a>Backup agendado

Um trabalho de backup que é automaticamente desencadeado pela política de backup configurada para o item dado.

## <a name="secondary-region--paired-region"></a>Região secundária / Região emparelhada

Um par regional é composto por duas regiões dentro da mesma geografia. Uma é a região primária, e a outra é a região secundária. As regiões emparelhadas são utilizadas por alguns serviços Azure (incluindo a Azure Backup com configurações GRS) para garantir a continuidade do negócio e proteger contra a perda de dados. [Saiba mais](../best-practices-availability-paired-regions.md).

## <a name="soft-delete"></a>Eliminação recuperável

A eliminação suave é uma funcionalidade que ajuda a proteger contra a eliminação acidental de dados de backup. Com a eliminação suave, mesmo que um ator malicioso elimine uma cópia de segurança (ou os dados de cópia de segurança sejam acidentalmente eliminados), os dados de cópia de segurança são retidos por um período adicional de tempo, permitindo a recuperação desse item de backup sem perda de dados. [Saiba mais](backup-azure-security-feature-cloud.md).

## <a name="snapshot"></a>Instantâneo

Um instantâneo é uma cópia completa e só de leitura de um disco rígido virtual (VHD) ou uma partilha de Ficheiro Azure. Saiba mais sobre [fotos de discos](../virtual-machines/windows/snapshot-copy-managed-disk.md) e fotos de [ficheiros.](../storage/files/storage-snapshots-files.md)

## <a name="storage-account"></a>Conta de armazenamento

Consulte a documentação da [conta de Armazenamento.](../storage/common/storage-account-overview.md)

## <a name="subscription"></a>Subscrição

Uma subscrição do Azure é um contentor lógico utilizado para aprovisionar recursos no Azure. Contém os detalhes de todos os seus recursos, como máquinas virtuais (VMs), bases de dados e muito mais.

## <a name="system-state-backup"></a>Backup do Estado do Sistema

(Termo específico para a carga de trabalho)

Confirma os ficheiros do sistema operativo. Esta cópia de segurança permite-lhe recuperar quando um computador começa, mas os ficheiros do sistema e o registo perdem-se. [Saiba mais](backup-mabs-system-state-and-bmr.md).

## <a name="tenant"></a>Inquilino

Um inquilino é uma representação de uma organização. É uma instância dedicada do Azure AD que uma organização ou programador de aplicações recebe ao criar uma relação com a Microsoft, tal como inscrever-se no Azure, no Microsoft Intune ou no Microsoft 365.

## <a name="tier"></a>Escalão de serviço

Atualmente, a Azure Backup suporta os seguintes níveis de armazenamento de backup:

### <a name="snapshot-tier"></a>Nível instantâneo

(Termo específico da carga de trabalho) Na primeira fase da cópia de segurança VM, a imagem tirada é armazenada juntamente com o disco. Esta forma de armazenamento é referida como camada instantânea. As restaurações de nível snapshot são mais rápidas (do que restaurar a partir de um cofre) porque eliminam o tempo de espera para as imagens serem copiadas do cofre antes de desencadear a operação de restauro.

### <a name="vault-standard-tier"></a>Vault-Standard nível

Os dados de backup de todas as cargas de trabalho suportadas pelo Azure Backup são armazenados em cofres que possuem armazenamento de backup, um conjunto de contas de armazenamento de escala automática gerido pela Azure Backup. O nível Vault-Standard é um nível de armazenamento on-line que permite armazenar uma cópia isolada de dados de backup num inquilino gerido pela Microsoft, criando assim uma camada adicional de proteção. Para cargas de trabalho onde o nível instantâneo é suportado, há uma cópia dos dados de backup tanto no nível de instantâneo como no nível padrão do cofre. O nível padrão do cofre garante que os dados de backup estão disponíveis mesmo que a fonte de dados que está a ser apoiada seja eliminada ou comprometida.

## <a name="unmanaged-disk"></a>Disco não gerido

Consulte a documentação dos [discos não geridos.](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks)

## <a name="vault"></a>Cofre

Uma entidade de armazenamento em Azure que abriga dados de backup. É também uma unidade de RBAC e faturação. Atualmente existem dois tipos de cofres - cofre dos Serviços de Recuperação e cofre de reserva.

## <a name="vault-credentials"></a>Credenciais de cofre

O ficheiro de credenciais do cofre é um certificado gerado pelo portal para cada cofre. Isto é usado enquanto regista um servidor no local para o cofre. [Saiba mais](backup-azure-dpm-introduction.md).

## <a name="vnet-virtual-network"></a>VNET (Rede Virtual)

Consulte a [documentação VNET.](../virtual-network/virtual-networks-overview.md)

## <a name="vss-windows-volume-shadow-copy-service"></a>VSS (Serviço de Cópia Sombra de Volume do Windows)

Consulte a [documentação VSS](/windows-server/storage/file-server/volume-shadow-copy-service).

## <a name="next-steps"></a>Passos seguintes

- [Visão geral do Backup Azure](backup-overview.md)
- [Arquitetura e componentes Azure Backup](backup-architecture.md)