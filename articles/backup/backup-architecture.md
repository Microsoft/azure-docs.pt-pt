---
title: Descrição geral da arquitetura
description: Fornece uma visão geral da arquitetura, componentes e processos utilizados pelo serviço Azure Backup.
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: 1e5a61bd4e3287c1100ff1f54fda797c1add438b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466416"
---
# <a name="azure-backup-architecture-and-components"></a>Arquitetura e componentes Azure Backup

Pode utilizar o [serviço Azure Backup](backup-overview.md) para fazer cópia de segurança de dados na plataforma cloud do Microsoft Azure. Este artigo resume a arquitetura, componentes e processos da Azure Backup.

## <a name="what-does-azure-backup-do"></a>O que faz o Azure Backup?

O Azure Backup confirma os dados, o estado da máquina e as cargas de trabalho em funcionamento em máquinas no local e em casos de máquina virtual Azure (VM). Há uma série de cenários de backup do Azure.

## <a name="how-does-azure-backup-work"></a>Como funciona o Azure Backup?

Pode fazer o back up machines e os dados utilizando uma série de métodos:

- **Máquinas de apoio no local:**
  - Pode fazer backup nas máquinas do Windows diretamente para o Azure, utilizando o agente Azure Backup Microsoft Azure Recovery Services (MARS). As máquinas Linux não são suportadas.
  - Pode fazer backup de máquinas no local para um servidor de backup - ou System Center Data Protection Manager (DPM) ou Microsoft Azure Backup Server (MABS). Em seguida, pode fazer backup no servidor de reserva para um cofre dos Serviços de Recuperação em Azure.

- **Apoio Azure VMs:**
  - Pode apoiar os VMs Azure diretamente. O Azure Backup instala uma extensão de backup para o agente Azure VM que está em execução no VM. Esta extensão confirma todo o VM.
  - Pode fazer o back-up de ficheiros e pastas específicos no Azure VM executando o agente MARS.
  - Podes fazer o reforço dos VMs do Azure para o MABS que está a funcionar em Azure, e depois podes fazer o apoio do MABS a um cofre dos Serviços de Recuperação.

Saiba mais sobre [o que pode apoiar](backup-overview.md) e sobre [cenários de backup apoiados](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Onde estão os dados?

A Azure Backup armazena dados de backup em cofres - cofres dos Serviços de Recuperação e cofres de reserva. Um cofre é uma entidade de armazenamento on-line em Azure que é usada para conter dados, tais como cópias de backup, pontos de recuperação e políticas de backup.

Os cofres têm as seguintes características:

- Os cofres facilitam a organização dos seus dados de backup, minimizando a sobrecarga de gestão.
- Pode monitorizar itens de apoio num cofre, incluindo VMs Azure e máquinas no local.
- Você pode gerir o acesso ao cofre com [o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md).
- Especifica como os dados no cofre são replicados para redundância:
  - **Armazenamento localmente redundante (LRS)**: Para proteger contra falhas num datacenter, pode utilizar LRS. O LRS replica dados numa unidade de escala de armazenamento. [Saiba mais](../storage/common/storage-redundancy.md#locally-redundant-storage).
  - **Armazenamento geo-redundante (GRS)**: Para proteger contra interrupções em toda a região, pode utilizar GRS. GRS replica os seus dados para uma região secundária. [Saiba mais](../storage/common/storage-redundancy.md#geo-redundant-storage).
  - **Armazenamento redundante de zona (ZRS)**: replica os seus dados em [zonas de disponibilidade,](../availability-zones/az-overview.md#availability-zones)garantindo residência de dados e resiliência na mesma região. [Saiba mais](../storage/common/storage-redundancy.md#zone-redundant-storage)
  - Por predefinição, os cofres dos Serviços de Recuperação utilizam GRS.

Os cofres dos Serviços de Recuperação têm as seguintes características adicionais:

- Em cada subscrição do Azure, pode criar até 500 cofres.

## <a name="backup-agents"></a>Agentes do Azure Backup

A Azure Backup fornece diferentes agentes de backup, dependendo do tipo de máquina que está a ser apoiada:

**Agente** | **Detalhes**
--- | ---
**Agente MARS** | <ul><li>Funciona em máquinas do Windows Server individuais para fazer o back-up de ficheiros, pastas e o estado do sistema.</li> <li>Funciona em VMs Azure para fazer o back up ficheiros, pastas e o estado do sistema.</li> <li>Executa nos servidores DPM/MABS para fazer o back up do disco de armazenamento local DPM/MABS para Azure.</li></ul>
**Extensão Azure VM** | Corre em VMs Azure para os apoiar até um cofre.

## <a name="backup-types"></a>Tipos de cópia de segurança

A tabela seguinte explica os diferentes tipos de backups e quando são usados:

**Tipo de backup** | **Detalhes** | **Utilização**
--- | --- | ---
**Completa** | Uma cópia de segurança completa contém toda a fonte de dados. É preciso mais largura de banda de rede do que backups diferenciais ou incrementais. | Usado para reforço inicial.
**Diferencial** |  Uma cópia de segurança diferencial armazena os blocos que mudaram desde a cópia de segurança inicial. Usa uma menor quantidade de rede e armazenamento, e não mantém cópias redundantes de dados inalterados.<br/><br/> Ineficiente porque os blocos de dados que são inalterados entre cópias de segurança posteriores são transferidos e armazenados. | Não usado pela Azure Backup.
**Incremental** | Uma cópia de segurança incremental armazena apenas os blocos de dados que mudaram desde a cópia de segurança anterior. Alta eficiência de armazenamento e rede. <br/><br/> Com cópias de segurança incrementais, não há necessidade de complementar com cópias de segurança completas. | Usado por DPM/MABS para backups de discos e usado em todas as cópias de segurança para Azure. Não é utilizado para a cópia de segurança do SQL Server.

## <a name="sql-server-backup-types"></a>Tipos de backup do SQL Server

A tabela seguinte explica os diferentes tipos de cópias de segurança utilizadas para as bases de dados do SQL Server e a frequência com que são utilizadas:

**Tipo de backup** | **Detalhes** | **Utilização**
--- | --- | ---
**Cópia de segurança completa** | Uma cópia de segurança da base de dados completa confirma toda a base de dados. Contém todos os dados numa base de dados específica ou num conjunto de grupos de ficheiros ou ficheiros. Uma cópia de segurança completa também contém registos suficientes para recuperar esses dados. | No máximo, pode acionar uma cópia de segurança completa por dia.<br/><br/> Pode optar por fazer uma cópia de segurança completa num intervalo diário ou semanal.
**Cópia de segurança diferencial** | Uma cópia de segurança diferencial baseia-se na cópia de segurança completa mais recente.<br/><br/> Captura apenas os dados que mudaram desde a cópia de segurança completa. |  No máximo, pode acionar uma cópia de segurança diferencial por dia.<br/><br/> Não pode configurar um backup completo e uma cópia de segurança diferencial no mesmo dia.
**Backup de registo de transações** | Uma cópia de segurança de registo permite a restauração pontual até um segundo específico. | No máximo, pode configurar cópias de segurança de registos transacionais a cada 15 minutos.

### <a name="comparison-of-backup-types"></a>Comparação de tipos de backup

O consumo de armazenamento, o objetivo do tempo de recuperação (RTO) e o consumo de rede variam para cada tipo de backup. A imagem a seguir mostra uma comparação dos tipos de backup:

- A fonte de dados A é composta por 10 blocos de armazenamento, A1-A10, que são apoiados mensalmente.
- Os blocos A2, A3, A4 e A9 são alterados no primeiro mês e o bloco A5 no mês seguinte.
- Para cópias de segurança diferenciais, no segundo mês os blocos A2, A3, A4 e A9 estão apoiados. No terceiro mês, é criada uma nova cópia de segurança destes mesmos blocos, juntamente com o bloco alterado A5. Continua a ser feita uma cópia de segurança dos blocos alterados até ser feita a próxima cópia de segurança completa.
- Para backups incrementais, no segundo mês os blocos A2, A3, A4 e A9 são marcados como alterados e transferidos. No terceiro mês, só o bloco A5 é marcado e transferido.

![Imagem mostrando comparações de métodos de backup](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Funcionalidades de backup

A tabela a seguir resume as funcionalidades suportadas para os diferentes tipos de cópias de segurança:

**Funcionalidade** | **Cópia de segurança direta de ficheiros e pastas (utilizando o agente MARS)** | **Azure VM Backup** | **Máquinas ou aplicativos com DPM/MABS**
--- | --- | --- | ---
De volta ao cofre | ![Yes][green] | ![Yes][green] | ![Yes][green]
Voltar para o disco DPM/MABS e depois para Azure | | | ![Yes][green]
Dados de comprimir enviados para cópia de segurança | ![Yes][green] | Não é utilizada qualquer compressão na transferência de dados. O armazenamento é ligeiramente insuflado, mas a restauração é mais rápida.  | ![Yes][green]
Executar backup incremental |![Yes][green] |![Yes][green] |![Yes][green]
Fazer backup discos deduplicados | | | ![Parcialmente][yellow]<br/><br/> Para servidores DPM/MABS implantados apenas no local.

![Chave de mesa](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>Essencial da política de backup

- Uma política de reserva é criada por cofre.
- Pode ser criada uma política de backup para a cópia de segurança das seguintes cargas de trabalho: Azure VMs, SQL em VMs Azure, SAP HANA em VMs Azure e ações de ficheiros Azure. A política de ficheiros e cópias de segurança de pastas utilizando o agente MARS é especificada na consola MARS.
  - Partilha de Ficheiros do Azure
- Uma política pode ser atribuída a muitos recursos. Uma política de backup Azure VM pode ser usada para proteger muitos VMs Azure.
- Uma política consiste em dois componentes
  - Horário: Quando tomar a cópia de segurança
  - Retenção: Por quanto tempo cada cópia de segurança deve ser mantida.
- A programação pode ser definida como "diária" ou "semanal" com um ponto de tempo específico.
- A retenção pode ser definida para pontos de backup "diários", "semanais", "mensais", "anualmente".
  - "semanalmente" refere-se a um backup em um certo dia da semana
  - "Mensalmente" refere-se a um backup num determinado dia do mês
  - "Anualmente" refere-se a um backup em um determinado dia do ano
- A retenção para pontos de backup "mensais", "anualmente" é referida como Retenção a Longo Prazo (LTR)
- Quando um cofre é criado, um "DefaultPolicy" também é criado e pode ser usado para apoiar recursos.
- Quaisquer alterações introduzidas no período de retenção de uma política de backup serão aplicadas retroativamente a todos os pontos de recuperação mais antigos, para além dos novos.

### <a name="impact-of-policy-change-on-recovery-points"></a>Impacto da mudança de política nos pontos de recuperação

- **A duração da retenção é aumentada/diminuída:** Quando a duração da retenção é alterada, a nova duração de retenção também é aplicada aos pontos de recuperação existentes. Como resultado, alguns dos pontos de recuperação serão limpos. Se o período de retenção for aumentado, os pontos de recuperação existentes também terão uma retenção acrescida.
- **Mudada de dia para semana:** Quando as cópias de segurança programadas são alteradas de dia para semana, os pontos de recuperação diários existentes são limpos.
- **Mudada de semanal para diária:** Os backups semanais existentes serão mantidos com base no número de dias restantes de acordo com a atual política de retenção.

### <a name="additional-reference"></a>Referência adicional

- Máquina Azure VM: Como [criar](./backup-azure-vms-first-look-arm.md#back-up-from-azure-vm-settings) e [modificar](./backup-azure-manage-vms.md#manage-backup-policy-for-a-vm) a política.
- Base de dados do SQL Server na máquina Azure VM: Como [criar](./backup-sql-server-database-azure-vms.md#create-a-backup-policy) e [modificar](./manage-monitor-sql-database-backup.md#modify-policy) a política.
- Partilha de ficheiros Azure: Como [criar](./backup-afs.md) e [modificar](./manage-afs-backup.md#modify-policy) a política.
- SAP HANA: Como [criar](./backup-azure-sap-hana-database.md#create-a-backup-policy) e [modificar](./sap-hana-db-manage.md#change-policy) a política.
- Como [criar](./backup-windows-with-mars-agent.md#create-a-backup-policy) e [modificar](./backup-azure-manage-mars.md#modify-a-backup-policy) a política.
- [Existem limitações no agendamento de backup com base no tipo de carga de trabalho?](./backup-azure-backup-faq.md#are-there-limits-on-backup-scheduling)
- [O que acontecerá aos pontos de recuperação existentes se alterar a política de retenção?](./backup-azure-backup-faq.md#what-happens-when-i-change-my-backup-policy)

## <a name="architecture-built-in-azure-vm-backup"></a>Arquitetura: Backup Azure VM incorporado

1. Quando ativa a cópia de segurança para um Azure VM, uma cópia de segurança é executada de acordo com o horário especificado.
1. Durante a primeira cópia de segurança, é instalada uma extensão de backup no VM se o VM estiver em funcionamento.
    - Para VMs do Windows, é instalada a extensão VMSnapshot.
    - Para os VMs Linux, a extensão VMSnapshot Linux está instalada.
1. A extensão requer uma imagem de nível de armazenamento.
    - Para os VMs do Windows que estão em execução, o Backup coordena com o Windows Volume Shadow Copy Service (VSS) para tirar uma imagem consistente da aplicação do VM. Por predefinição, o Backup recebe cópias de segurança VSS completas. Se o Backup não conseguir tirar uma imagem consistente da aplicação, então é necessário um instantâneo consistente com ficheiros.
    - Para os VMs Linux, o Backup tira uma imagem consistente com ficheiros. Para instantâneos consistentes com aplicações, é necessário personalizar manualmente scripts pré/post.
    - A cópia de segurança é otimizada através da cópia de segurança de cada disco VM em paralelo. Para cada disco que está a ser apoiado, o Azure Backup lê os blocos no disco e armazena apenas os dados alterados.
1. Depois da foto ser tirada, os dados são transferidos para o cofre.
    - Apenas blocos de dados que mudaram desde a última cópia de segurança são copiados.
    - Os dados não estão encriptados. O Azure Backup pode fazer backup de VMs Azure que foram encriptados usando encriptação de disco Azure.
    - Os dados do instantâneo podem não ser copiados para o cofre imediatamente. Em horas de pico, o reforço pode demorar algumas horas. O tempo total de backup de uma VM será inferior a 24 horas para políticas de cópias de segurança diárias.
1. Depois que os dados são enviados para o cofre, um ponto de recuperação é criado. Por predefinição, as imagens são mantidas durante dois dias antes de serem eliminadas. Esta funcionalidade permite restaurar o funcionamento destes instantâneos, reduzindo assim os tempos de restauro. Reduz o tempo necessário para transformar e copiar dados do cofre. Consulte [a capacidade de restauro instantâneo de backup Azure](./backup-instant-restore-capability.md).

Não precisa de permitir explicitamente que a conectividade da Internet faça o back up dos seus VMs Azure.

![Backup de Azure VMs](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Arquitetura: Cópia de segurança direta das máquinas do Windows Server ou dos ficheiros ou pastas Azure VM

1. Para configurar o cenário, descarregue e instale o agente MARS na máquina. Em seguida, selecione o que fazer, quando os backups vão funcionar, e quanto tempo serão mantidos em Azure.
1. A cópia de segurança inicial é de acordo com as definições de backup.
1. O agente MARS utiliza o VSS para tirar uma imagem pontual dos volumes selecionados para cópia de segurança.
    - O agente MARS utiliza apenas a operação de escrita do sistema Windows para capturar o instantâneo.
    - Como o agente não usa nenhuma aplicação de escritores VSS, não captura imagens consistentes com aplicações.
1. Depois de tirar a fotografia com VSS, o agente MARS cria um disco rígido virtual (VHD) na pasta de cache especificada quando configura a cópia de segurança. O agente também armazena as datas de verificação para cada bloco de dados. Estes são mais tarde utilizados para detetar blocos alterados para cópias de segurança incrementais subsequentes.
1. As cópias de segurança incrementais são executadas de acordo com o horário especificado, a menos que faça uma cópia de segurança a pedido.
1. Em cópias de segurança incrementais, são identificados ficheiros alterados e criado um novo VHD. O VHD é comprimido e encriptado, e depois é enviado para o cofre.
1. Após os acabamentos incrementais de backup, o novo VHD é fundido com o VHD criado após a replicação inicial. Este VHD fundido fornece o estado mais recente a ser usado para comparação para cópias de segurança em curso.

![Cópia de segurança das máquinas do Windows Server com agente MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Arquitetura: De volta ao DPM/MABS

1. Instale o agente de proteção DPM ou MABS nas máquinas que pretende proteger. Em seguida, adicione as máquinas a um grupo de proteção DPM.
    - Para proteger as máquinas no local, o servidor DPM ou MABS deve estar localizado no local.
    - Para proteger os VMs Azure, o servidor MABS deve estar localizado em Azure, funcionando como um VM Azure.
    - Com o DPM/MABS, pode proteger volumes de backup, partilhas, ficheiros e pastas. Também pode proteger o estado do sistema de uma máquina (metal nu), e pode proteger aplicações específicas com definições de backup conscientes da aplicação.
1. Quando configurar proteção para uma máquina ou app em DPM/MABS, selecione para fazer o back up até ao disco local MABS/DPM para armazenamento de curto prazo e para Azure para proteção on-line. Também especifica quando a cópia de segurança para o armazenamento local de DPM/MABS deve ser executada e quando a cópia de segurança on-line para Azure deve ser executada.
1. O disco da carga de trabalho protegida é apoiado até aos discos MABS/DPM locais, de acordo com o horário especificado.
1. Os discos DPM/MABS são apoiados até ao cofre pelo agente MARS que está a funcionar no servidor DPM/MABS.

![Cópia de segurança das máquinas e cargas de trabalho protegidas por DPM ou MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Armazenamento Azure VM

Os VMs Azure usam discos para armazenar o seu sistema operativo, aplicações e dados. Cada VM Azure tem pelo menos dois discos: um disco para o sistema operativo e um disco temporário. Os VMs Azure também podem ter discos de dados para dados de aplicações. Os discos são armazenados como VHDs.

- Os VHDs são armazenados como bolhas de página em contas de armazenamento standard ou premium em Azure:
  - **Armazenamento padrão:** Suporte fiável e de baixo custo para VMs que executam cargas de trabalho que não são sensíveis à latência. O armazenamento padrão pode utilizar discos de unidade de estado sólido padrão (SSD) ou discos de disco rígido padrão (HDD).
  - **Armazenamento premium:** Suporte de disco de alto desempenho. Usa discos SSD premium.
- Existem diferentes níveis de desempenho para discos:
  - **Disco HDD padrão:** Apoiado por HDDs, e usado para armazenamento rentável.
  - **Disco SSD padrão:** Combina elementos de discos SSD premium e discos HDD padrão. Oferece um desempenho e fiabilidade mais consistentes do que o HDD, mas ainda assim rentável.
  - **Disco SSD premium:** Apoiado por SSDs, e fornece alto desempenho e baixa latência para VMs que estão executando cargas de trabalho intensivos de I/O.
- Os discos podem ser geridos ou não geridos:
  - **Discos não geridos:** Tipo tradicional de discos usados por VMs. Para estes discos, cria a sua própria conta de armazenamento e especifica-a quando cria o disco. Em seguida, você precisa descobrir como maximizar os recursos de armazenamento para os seus VMs.
  - **Discos geridos:** Azure cria e gere as contas de armazenamento para si. Especifica o tamanho e o nível de desempenho do disco e o Azure cria discos geridos para si. Ao adicionar discos e VMs de escala, o Azure trata das contas de armazenamento.

Para obter mais informações sobre o armazenamento de discos e os tipos de discos disponíveis para VMs, consulte estes artigos:

- [Azure geriu discos para Os VMs Linux](../virtual-machines/managed-disks-overview.md)
- [Tipos de disco disponíveis para VMs](../virtual-machines/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Recue e restaure os VMs Azure com armazenamento premium

Pode apoiar os VMs Azure utilizando armazenamento premium com Azure Backup:

- Durante o processo de backup de VMs com armazenamento premium, o serviço Backup cria uma localização de preparação temporária, chamada *AzureBackup-* na conta de armazenamento. O tamanho do local de paragem é igual ao tamanho do instantâneo do ponto de recuperação.
- Certifique-se de que a conta de armazenamento premium dispõe de espaço livre adequado para acomodar o local de paragem temporária. Para obter mais informações, consulte [os alvos de Escalaability para contas de armazenamento de blob de página premium](../storage/blobs/scalability-targets-premium-page-blobs.md). Não modifique o local de paragem.
- Após o fim do trabalho de reserva, o local de paragem é apagado.
- O preço de armazenamento utilizado para a localização de localização é consistente com [o preço de armazenamento premium.](../virtual-machines/disks-types.md#billing)

Quando restaurar os VMs Azure utilizando um armazenamento premium, pode restaurá-los para armazenamento premium ou padrão. Normalmente, restabelecê-los-ias para armazenamento premium. Mas se precisar apenas de um subconjunto de ficheiros do VM, pode ser rentável restaurá-los para o armazenamento padrão.

### <a name="back-up-and-restore-managed-disks"></a>Faça recuar e restaurar discos geridos

Pode fazer o back up Azure VMs com discos geridos:

- Faz o back up VMs com discos geridos da mesma forma que faz qualquer outro VM Azure. Pode fazer backup do VM diretamente a partir das definições da máquina virtual, ou pode ativar a cópia de segurança para VMs no cofre dos Serviços de Recuperação.
- Pode criar cópias de segurança de VMs em discos geridos através de coleções de RestorePoint criadas sobre os discos geridos.
- O Azure Backup também suporta o backup de VMs com discos geridos que foram encriptados usando a Encriptação do Disco Azure.

Quando restaurar VMs com discos geridos, pode restaurar para um VM completo com discos geridos ou para uma conta de armazenamento:

- Durante o processo de restauro, o Azure trata dos discos geridos. Se estiver a utilizar a opção de conta de armazenamento, gere a conta de armazenamento criada durante o processo de restauro.
- Se restaurar um VM gerido que esteja encriptado, certifique-se de que as chaves e segredos do VM existem no cofre antes de iniciar o processo de restauro.

## <a name="next-steps"></a>Passos seguintes

- Reveja a matriz de suporte para [saber sobre funcionalidades e limitações suportadas para cenários de backup](backup-support-matrix.md).
- Configurar backup para um destes cenários:
  - [Apoiar os VMs do Azure.](backup-azure-arm-vms-prepare.md)
  - [Faça o backup das máquinas do Windows diretamente,](tutorial-backup-windows-server-to-azure.md)sem um servidor de reserva.
  - [Crie mABS](backup-azure-microsoft-azure-backup.md) para apoio ao Azure e, em seguida, faça o backup das cargas de trabalho para o MABS.
  - [Confiem dPM](backup-azure-dpm-introduction.md) para apoio ao Azure e, em seguida, rememtra as cargas de trabalho para dPM.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
