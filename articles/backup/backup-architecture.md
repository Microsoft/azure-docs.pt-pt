---
title: Visão geral da arquitetura
description: Fornece uma visão geral da arquitetura, componentes e processos utilizados pelo serviço de backup Azure.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: b093c6702bb26fe537622727fe1b623141bf4160
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273621"
---
# <a name="azure-backup-architecture-and-components"></a>Arquitetura e componentes de backup Azure

Pode utilizar o [serviço de backup Azure](backup-overview.md) para fazer backup de dados para a plataforma cloud do Microsoft Azure. Este artigo resume a arquitetura, componentes e processos azure Backup.

## <a name="what-does-azure-backup-do"></a>O que faz o Azure Backup?

O Azure Backup apoia os dados, o estado da máquina e as cargas de trabalho que estão a funcionar nas máquinas no local e nas instâncias da máquina virtual Azure (VM). Há uma série de cenários de Backup Azure.

## <a name="how-does-azure-backup-work"></a>Como funciona o Azure Backup?

Pode fazer o backup de máquinas e dados utilizando uma série de métodos:

- **Máquinas de back-up no local:**
  - Pode fazer o backup das máquinas Windows diretamente para o Azure utilizando o agente Azure Backup Services (MARS). As máquinas linux não são suportadas.
  - Pode fazer o backup das máquinas no local para um servidor de backup - ou System Center Data Protection Manager (DPM) ou Microsoft Azure Backup Server (MABS). Em seguida, pode fazer o backup do servidor de reserva para um cofre dos Serviços de Recuperação em Azure.

- **Back up VMs Azure:**
  - Pode sacar os VMs Azure diretamente. A Azure Backup instala uma extensão de reserva ao agente Azure VM que está a funcionar no VM. Esta extensão apoia todo o VM.
  - Pode fazer o backup de ficheiros e pastas específicos no Azure VM executando o agente MARS.
  - Podes fazer o back-up Azure VMs para os MABS que estão a funcionar em Azure, e depois podes apoiar o MABS até um cofre dos Serviços de Recuperação.

Saiba mais sobre [o que pode fazer backup](backup-overview.md) e sobre [cenários de backup suportados.](backup-support-matrix.md)

## <a name="where-is-data-backed-up"></a>Onde é que os dados são apoiados?

As lojas Azure Backup confirmam os dados num cofre dos Serviços de Recuperação. Um cofre é uma entidade de armazenamento on-line em Azure que é usada para deter dados, tais como cópias de backup, pontos de recuperação e políticas de backup.

Os cofres dos Serviços de Recuperação têm as seguintes funcionalidades:

- Os cofres facilitam a organização dos seus dados de backup, minimizando as despesas de gestão.
- Em cada subscrição do Azure, pode criar até 500 cofres.
- Você pode monitorizar itens de reserva em um cofre, incluindo VMs Azure e máquinas no local.
- Você pode gerir o acesso ao cofre com o controlo de acesso baseado em funções Azure [(RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
- Especifica como os dados no cofre são replicados para redundância:
  - **Armazenamento redundante localmente (LRS)** : Para proteger contra falhas num datacenter, pode utilizar LRS. O LRS replica dados para uma unidade de escala de armazenamento. [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
  - **Armazenamento geo-redundante (GRS)** : Para proteger contra interrupções em toda a região, pode utilizar GRS. GrS replica os seus dados para uma região secundária. [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).
  - Por padrão, os cofres dos Serviços de Recuperação utilizam GRS.

## <a name="backup-agents"></a>Agentes de reserva

A Azure Backup fornece diferentes agentes de backup, dependendo do tipo de máquina que está a ser apoiada:

**Agente** | **Detalhes**
--- | ---
**Agente MARS** | <ul><li>Funciona em máquinas individuais no local do Windows Server para fazer o back-up de ficheiros, pastas e o estado do sistema.</li> <li>Funciona em VMs Azure para fazer o back-up de ficheiros, pastas e estado do sistema.</li> <li>Funciona em servidores DPM/MABS para fazer o backup do disco de armazenamento local DPM/MABS para o Azure.</li></ul>
**Extensão Azure VM** | Corre em VMs Azure para os apoiar num cofre.

## <a name="backup-types"></a>Tipos de backup

A tabela a seguir explica os diferentes tipos de backups e quando são usados:

**Tipo de cópia de segurança** | **Detalhes** | **Utilização**
--- | --- | ---
**Cheio** | Uma cópia de segurança completa contém toda a fonte de dados. Requer mais largura de banda de rede do que backups diferenciais ou incrementais. | Usado para cópia de segurança inicial.
**Diferencial** |  Um backup diferencial armazena os blocos que mudaram desde a cópia de segurança inicial. Usa uma menor quantidade de rede e armazenamento, e não guarda cópias redundantes de dados inalterados.<br/><br/> Ineficiente porque os blocos de dados que são inalterados entre cópias de segurança posteriores são transferidos e armazenados. | Não usado pela Azure Backup.
**Incremental** | Uma cópia de segurança incremental armazena apenas os blocos de dados que mudaram desde a cópia de segurança anterior. Alta eficiência de armazenamento e rede. <br/><br/> Com reforçoincremental, não há necessidade de complementar com cópias de segurança completas. | Usado por DPM/MABS para backups de disco, e usado em todas as cópias de segurança para Azure. Não utilizado para cópia de segurança do SQL Server.

## <a name="sql-server-backup-types"></a>Tipos de backup do Servidor SQL

A tabela que se segue explica os diferentes tipos de backups utilizados para as bases de dados do SQL Server e a frequência com que são utilizadas:

**Tipo de cópia de segurança** | **Detalhes** | **Utilização**
--- | --- | ---
**Backup completo** | Uma base de dados completa reserva toda a base de dados. Contém todos os dados numa base de dados específica ou num conjunto de ficheiros ou ficheiros. Uma cópia de segurança completa também contém registos suficientes para recuperar esses dados. | No máximo, podes ativar um reforço completo por dia.<br/><br/> Pode optar por fazer um backup completo num intervalo diário ou semanal.
**Backup diferencial** | Uma cópia de segurança diferencial baseia-se na cópia de segurança de dados completos mais recentes e anteriores.<br/><br/> Captura apenas os dados que mudaram desde a cópia de segurança total. |  No máximo, pode desencadear uma cópia de segurança diferencial por dia.<br/><br/> Não se pode configurar uma cópia de segurança completa e uma cópia de segurança diferencial no mesmo dia.
**Cópia de segurança do registo de transações** | Uma cópia de segurança permite a restauração pontual até um segundo específico. | No máximo, pode configurar cópias de registo transacional a cada 15 minutos.

### <a name="comparison-of-backup-types"></a>Comparação de tipos de backup

O consumo de armazenamento, o objetivo do tempo de recuperação (RTO) e o consumo de rede variam para cada tipo de backup. A imagem seguinte mostra uma comparação dos tipos de backup:

- Fonte de dados A é composta por 10 blocos de armazenamento, A1-A10, que são apoiados mensalmente.
- Os blocos A2, A3, A4 e A9 são alterados no primeiro mês e o bloco A5 no mês seguinte.
- Para backups diferenciais, no segundo mês, os blocos A2, A3, A4 e A9 estão apoiados. No terceiro mês, é criada uma nova cópia de segurança destes mesmos blocos, juntamente com o bloco alterado A5. Continua a ser feita uma cópia de segurança dos blocos alterados até ser feita a próxima cópia de segurança completa.
- Para backups incrementais, no segundo mês, os blocos A2, A3, A4 e A9 estão marcados como alterados e transferidos. No terceiro mês, só o bloco A5 é marcado e transferido.

![Imagem mostrando comparações de métodos de backup](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Funcionalidades de backup

A tabela seguinte resume as características suportadas para os diferentes tipos de backup:

**Funcionalidade** | **Backup direto de Ficheiros e Pastas (utilizando o agente MARS)** | **Azure VM Backup** | **Máquinas ou aplicativos com DPM/MABS**
--- | --- | --- | ---
Volte para o cofre. | ![Sim][green] | ![Sim][green] | ![Sim][green]
Volte para o disco DPM/MABS, depois para Azure | | | ![Sim][green]
Comprimir os dados enviados para cópia de segurança | ![Sim][green] | Não é utilizada compressão na transferência de dados. O armazenamento está ligeiramente insuflado, mas a restauração é mais rápida.  | ![Sim][green]
Executar backup incremental |![Sim][green] |![Sim][green] |![Sim][green]
Discos de back-up desduplicados | | | ![Parcialmente][yellow]<br/><br/> Para servidores DPM/MABS implantados apenas no local.

![Chave de mesa](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>Essenciais de política de backup

- Uma política de reserva é criada por cofre.
- Uma política de backup pode ser criada para o backup de seguintes cargas de trabalho
  - VM do Azure
  - SQL em Azure VM
  - Partilha de Ficheiros do Azure
- Uma política pode ser atribuída a muitos recursos. Uma política de backup Azure VM pode ser usada para proteger muitos VMs Azure.
- Uma política consiste em dois componentes
  - Horário: Quando levar o backup
  - Retenção: Durante quanto tempo cada cópia de segurança deve ser retida.
- A programação pode ser definida como "diária" ou "semanal" com um determinado ponto de tempo.
- A retenção pode ser definida para pontos de backup "diários", "semanais", "mensais", "anuais".
- "Semanalmente" refere-se a um backup num determinado dia da semana, "mensalmente" significa um backup num determinado dia do mês e "anualmente" refere-se a um backup num determinado dia do ano.
- A retenção para pontos de backup "mensais", "anuais" é referida como "LongTermRetention".
- Quando um cofre é criado, é também criada uma política para backups De VM Azure chamada "DefaultPolicy" e pode ser usada para apoiar Os VMs Azure.

## <a name="architecture-built-in-azure-vm-backup"></a>Arquitetura: Backup Azure VM incorporado

1. Quando ativa o backup de um VM Azure, uma cópia de segurança corre de acordo com o horário que especifica.
1. Durante a primeira cópia de segurança, é instalada uma extensão de reserva no VM se o VM estiver em funcionamento.
    - Para VMs windows, a extensão VMSnapshot está instalada.
    - Para Os VMs Linux, a extensão VMSnapshot Linux está instalada.
1. A extensão tem uma imagem de nível de armazenamento.
    - Para os VMs do Windows que estão em execução, as coordenadas de backup com o Windows Volume Shadow Copy Service (VSS) para tirar uma imagem consistente com aplicações do VM. Por padrão, a Backup recebe cópias de segurança VSS completas. Se o Backup não conseguir tirar uma fotografia consistente com a aplicação, então é preciso uma fotografia consistente com ficheiros.
    - Para os VMs Linux, o Backup tem uma imagem consistente com ficheiros. Para imagens consistentes com aplicativos, é necessário personalizar manualmente scripts pré/post.
    - A cópia de segurança é otimizada através do backup de cada disco VM em paralelo. Para cada disco que está a ser apoiado, o Azure Backup lê os blocos no disco e armazena apenas os dados alterados.
1. Depois da foto ser tirada, os dados são transferidos para o cofre.
    - Apenas blocos de dados que mudaram desde a última cópia de cópia.
    - Os dados não estão encriptados. O Azure Backup pode fazer backup de VMs Azure que foram encriptados utilizando encriptação de disco azure.
    - Os dados instantâneos podem não ser imediatamente copiados para o cofre. Nas horas de pico, o reforço pode levar algumas horas. O tempo total de backup para um VM será inferior a 24 horas para as políticas diárias de backup.
1. Depois que os dados são enviados para o cofre, um ponto de recuperação é criado. Por predefinição, as imagens são mantidas durante dois dias antes de serem eliminadas. Esta função permite restaurar o funcionamento a partir destes instantâneos, reduzindo assim os tempos de restauro. Reduz o tempo necessário para transformar e copiar dados do cofre. Consulte a capacidade de restauro instantâneo de [backup azure](https://docs.microsoft.com/azure/backup/backup-instant-restore-capability).

Não precisa de permitir explicitamente a conectividade à Internet para fazer o back-up dos seus VMs Azure.

![Backup de VMs Azure](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Arquitetura: Backup direto de máquinas windows server no local ou ficheiros ou pastas Azure VM

1. Para configurar o cenário, faça o download e instale o agente MARS na máquina. Em seguida, selecione o que fazer, quando os backups vão correr, e quanto tempo serão mantidos em Azure.
1. A cópia de segurança inicial é de acordo com as definições de backup.
1. O agente MARS utiliza o VSS para tirar uma foto ponto-a-tempo dos volumes selecionados para cópia de segurança.
    - O agente MARS utiliza apenas a operação de escrita do sistema Windows para capturar o instantâneo.
    - Como o agente não usa nenhuma aplicação de escritores VSS, não captura imagens consistentes com aplicações.
1. Depois de tirar a fotografia com VSS, o agente MARS cria um disco rígido virtual (VHD) na pasta de cache que especificou quando configuraa a cópia de segurança. O agente também armazena cheques para cada bloco de dados.
1. As cópias de segurança incrementais são executadas de acordo com o horário que especifica, a menos que faça uma cópia de segurança a pedido.
1. Nas cópias de segurança incrementais, os ficheiros alterados são identificados e é criado um novo VHD. O VHD é comprimido e encriptado, e depois é enviado para o cofre.
1. Após os acabamentos de backup incremental, o novo VHD é fundido com o VHD criado após a replicação inicial. Este VHD fundido fornece o mais recente estado a ser usado para comparação para backup contínuo.

![Backup de máquinas de Servidor estonteante no local com agente MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Arquitetura: Back up to DPM/MABS

1. Instale o agente de proteção DPM ou MABS nas máquinas que pretende proteger. Em seguida, adicione as máquinas a um grupo de proteção DPM.
    - Para proteger as máquinas no local, o servidor DPM ou MABS deve estar localizado no local.
    - Para proteger os VMs Azure, o servidor MABS deve estar localizado em Azure, funcionando como um VM Azure.
    - Com DPM/MABS, pode proteger volumes de cópiade cópia, partilhas, ficheiros e pastas. Também pode proteger o estado do sistema de uma máquina (metal nu), e pode proteger aplicações específicas com configurações de backup conscientes da aplicação.
1. Quando configura risse proteção para uma máquina ou aplicação em DPM/MABS, selecione-se para fazer o back-up do disco local MABS/DPM para armazenamento a curto prazo e para o Azure para proteção online. Especifica também quando deve ser executada a cópia de segurança para o armazenamento local DPM/MABS e quando deve ser executada a cópia de segurança online para o Azure.
1. O disco da carga de trabalho protegida é apoiado nos discos MABS/DPM locais, de acordo com o horário especificado.
1. Os discos DPM/MABS são apoiados até ao cofre pelo agente MARS que está a funcionar no servidor DPM/MABS.

![Backup de máquinas e cargas de trabalho protegidas por DPM ou MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Armazenamento Azure VM

Os VMs Azure usam discos para armazenar o seu sistema operativo, aplicações e dados. Cada VM Azure tem pelo menos dois discos: um disco para o sistema operativo e um disco temporário. Os VMs Azure também podem ter discos de dados para dados de aplicações. Os discos são armazenados como VHDs.

- Os VHDs são armazenados como bolhas de página em contas de armazenamento padrão ou premium em Azure:
  - **Armazenamento padrão:** Suporte fiável e de baixo custo para vMs executando cargas de trabalho que não são sensíveis à latência. O armazenamento padrão pode utilizar discos de unidade de estado sólido padrão (SSD) ou discos de disco rígido padrão (HDD).
  - **Armazenamento premium:** Suporte de disco de alto desempenho. Usa discos SSD premium.
- Existem diferentes níveis de desempenho para discos:
  - **Disco HDD padrão:** Apoiado por HDDs, e usado para armazenamento rentável.
  - **Disco SSD padrão:** Combina elementos de discos SSD premium e discos HDD padrão. Oferece um desempenho e fiabilidade mais consistentes do que o HDD, mas ainda assim rentável.
  - **Disco SSD premium:** Apoiado por SSDs, e fornece alta performance e baixa latência para VMs que estão executando cargas de trabalho intensivas em I/O.
- Os discos podem ser geridos ou não geridos:
  - **Discos não geridos:** Tipo tradicional de discos usados por VMs. Para estes discos, cria a sua própria conta de armazenamento e especifica-a quando cria o disco. Em seguida, você precisa descobrir como maximizar os recursos de armazenamento para os seus VMs.
  - **Discos geridos:** O Azure cria e gere as contas de armazenamento para si. Especifica o tamanho do disco e o nível de desempenho, e o Azure cria discos geridos para si. À medida que adiciona discos e VMs de escala, o Azure trata das contas de armazenamento.

Para obter mais informações sobre o armazenamento de discos e os tipos de discos disponíveis para VMs, consulte estes artigos:

- [Azure geriu discos para VMs do Windows](../virtual-machines/windows/managed-disks-overview.md)
- [Azure geriu discos para VMs Linux](../virtual-machines/linux/managed-disks-overview.md)
- [Tipos de disco disponíveis para VMs](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Back up e restaurar VMs Azure com armazenamento premium

Pode fazer backup de VMs Azure utilizando armazenamento premium com Backup Azure:

- Durante o processo de backup de VMs com armazenamento premium, o serviço de backup cria uma localização temporária de encenação, chamada *AzureBackup-* na conta de armazenamento. O tamanho do local de paragem é igual ao tamanho do instantâneo do ponto de recuperação.
- Certifique-se de que a conta de armazenamento premium dispõe de espaço gratuito adequado para acomodar a localização temporária. Para obter mais informações, consulte os alvos de Escalabilidade para contas de [armazenamento de blob](../storage/blobs/scalability-targets-premium-page-blobs.md)de página premium . Não modifique o local da encenação.
- Após o trabalho de reserva terminar, o local de preparação é apagado.
- O preço de armazenamento utilizado para a localização da encenação é consistente com os preços de [armazenamento premium.](../virtual-machines/windows/disks-types.md#billing)

Quando restaurar os VMs Azure utilizando armazenamento premium, pode restaurá-los para armazenamento premium ou standard. Normalmente, você os devolveria ao armazenamento premium. Mas se precisar apenas de um subconjunto de ficheiros do VM, pode ser rentável restaurá-los para o armazenamento padrão.

### <a name="back-up-and-restore-managed-disks"></a>Apoiar e restaurar discos geridos

Pode fazer o back up VMs Azure com discos geridos:

- Você apoia VMs com discos geridos da mesma forma que faz qualquer outro VM Azure. Pode fazer o backup do VM diretamente a partir das definições da máquina virtual, ou pode ativar cópias de segurança para VMs no cofre dos Serviços de Recuperação.
- Pode criar cópias de segurança de VMs em discos geridos através de coleções de RestorePoint criadas sobre os discos geridos.
- O Azure Backup também suporta o backup de VMs com discos geridos que foram encriptados utilizando encriptação de disco azure.

Quando restaurar Os VMs com discos geridos, pode restaurar um VM completo com discos geridos ou para uma conta de armazenamento:

- Durante o processo de restauro, o Azure trata dos discos geridos. Se estiver a usar a opção de conta de armazenamento, gere a conta de armazenamento criada durante o processo de restauro.
- Se restaurar um VM gerido encriptado, certifique-se de que as chaves e segredos do VM existem no cofre da chave antes de iniciar o processo de restauro.

## <a name="next-steps"></a>Passos Seguintes

- Reveja a matriz de suporte para [aprender sobre funcionalidades suportadas e limitações para cenários](backup-support-matrix.md)de backup .
- Configurar cópias de segurança para um destes cenários:
  - [Back up VMs Azure](backup-azure-arm-vms-prepare.md).
  - [Faça o backup das máquinas do Windows diretamente,](tutorial-backup-windows-server-to-azure.md)sem um servidor de reserva.
  - [Configurar o MABS](backup-azure-microsoft-azure-backup.md) para obter cópias de segurança para o Azure e, em seguida, fazer backup das cargas de trabalho para o MABS.
  - [Instale dPM](backup-azure-dpm-introduction.md) para reforços para Azure, e, em seguida, faça backup de cargas para DPM.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
