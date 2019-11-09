---
title: Arquitetura de backup do Azure
description: Fornece uma visão geral da arquitetura, dos componentes e dos processos usados pelo serviço de backup do Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: dacurwin
ms.openlocfilehash: e072923c2c8b1d8e5bb281a5bcff992b25289b4d
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888494"
---
# <a name="azure-backup-architecture-and-components"></a>Arquitetura e componentes de backup do Azure

Você pode usar o [serviço de backup do Azure](backup-overview.md) para fazer backup de dados para a plataforma de nuvem Microsoft Azure. Este artigo resume a arquitetura, os componentes e os processos do backup do Azure.

## <a name="what-does-azure-backup-do"></a>O que o backup do Azure faz?

O backup do Azure faz backup dos dados, do estado da máquina e das cargas de trabalho em execução em máquinas locais e em instâncias de VM (máquina virtual) do Azure. Há vários cenários de backup do Azure.

## <a name="how-does-azure-backup-work"></a>Como funciona o backup do Azure?

Você pode fazer backup de computadores e dados usando vários métodos:

- **Fazer backup de computadores locais**:
  - Você pode fazer backup de computadores Windows no local diretamente no Azure usando o agente de Serviços de Recuperação do Microsoft Azure de backup do Azure (MARS). Não há suporte para computadores Linux.
  - Você pode fazer backup de computadores locais em um servidor de backup – o System Center Data Protection Manager (DPM) ou o Backup do Microsoft Azure Server (MABS). Em seguida, você pode fazer backup do servidor de backup em um cofre dos serviços de recuperação no Azure.

- **Fazer backup de VMs do Azure**:
  - Você pode fazer backup de VMs do Azure diretamente. O backup do Azure instala uma extensão de backup para o agente de VM do Azure que está em execução na VM. Essa extensão faz o backup de toda a VM.
  - Você pode fazer backup de arquivos e pastas específicos na VM do Azure executando o agente MARS.
  - Você pode fazer backup de VMs do Azure para o MABS em execução no Azure e, em seguida, fazer backup do MABS em um cofre dos serviços de recuperação.

Saiba mais sobre [o que você pode fazer](backup-overview.md) backup e sobre [cenários de backup com suporte](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Onde é feito o backup dos dados?

O backup do Azure armazena dados de backup em um cofre dos serviços de recuperação. Um cofre é uma entidade de armazenamento online no Azure que é usada para armazenar dados, como cópias de backup, pontos de recuperação e políticas de backup.

Os cofres dos serviços de recuperação têm os seguintes recursos:

- Os cofres facilitam a organização dos dados de backup, minimizando a sobrecarga de gerenciamento.
- Em cada assinatura do Azure, você pode criar até 500 cofres.
- Você pode monitorar itens com backup em um cofre, incluindo VMs do Azure e computadores locais.
- Você pode gerenciar o acesso ao cofre com o [RBAC (controle de acesso baseado em função)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)do Azure.
- Você especifica como os dados no cofre são replicados para redundância:
  - **LRS (armazenamento com redundância local)** : para se proteger contra falhas em um datacenter, você pode usar o lRS. O LRS replica os dados para uma unidade de escala de armazenamento. [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
  - **Armazenamento com redundância geográfica (GRS)** : para proteger contra interrupções em toda a região, você pode usar o grs. O GRS Replica seus dados para uma região secundária. [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).
  - Por padrão, os cofres dos serviços de recuperação usam GRS.

## <a name="backup-agents"></a>Agentes de backup

O backup do Azure fornece diferentes agentes de backup, dependendo do tipo de computador cujo backup está sendo feito:

**Agente** | **Detalhes**
--- | ---
**Agente MARS** | <ul><li>É executado em máquinas locais individuais do Windows Server para fazer backup de arquivos, pastas e do estado do sistema.</li> <li>É executado em VMs do Azure para fazer backup de arquivos, pastas e do estado do sistema.</li> <li>É executado em servidores DPM/MABS para fazer backup do disco de armazenamento local do DPM/MABS no Azure.</li></ul>
**Extensão de VM do Azure** | É executado em VMs do Azure para fazer backup em um cofre.

## <a name="backup-types"></a>Tipos de backup

A tabela a seguir explica os diferentes tipos de backups e quando eles são usados:

**Tipo de backup** | **Detalhes** | **Utilização**
--- | --- | ---
**Completo** | Um backup completo contém a fonte de dados inteira. Consome mais largura de banda de rede do que backups diferenciais ou incrementais. | Usado para backup inicial.
**Diferencial** |  Um backup diferencial armazena os blocos que foram alterados desde o backup completo inicial. O usa uma quantidade menor de rede e armazenamento e não mantém cópias redundantes de dados inalterados.<br/><br/> Ineficiente porque os blocos de dados que são inalterados entre os backups posteriores são transferidos e armazenados. | Não usado pelo backup do Azure.
**Lucrativ** | Um backup incremental armazena apenas os blocos de dados que foram alterados desde o backup anterior. Alta eficiência de armazenamento e rede. <br/><br/> Com o backup incremental, não é necessário complementar com backups completos. | Usado pelo DPM/MABS para backups de disco e usado em todos os backups no Azure. Não é usado para SQL Server Backup.

## <a name="sql-server-backup-types"></a>SQL Server tipos de backup

A tabela a seguir explica os diferentes tipos de backups usados para bancos de dados SQL Server e com que frequência eles são usados:

**Tipo de backup** | **Detalhes** | **Utilização**
--- | --- | ---
**Backup completo** | Um backup de banco de dados completo faz backup de todo o banco de dados. Ele contém todos os dados em um banco de dado específico ou em um conjunto de grupos ou arquivos de arquivo. Um backup completo também contém logs suficientes para recuperar esses dados. | No máximo, você pode disparar um backup completo por dia.<br/><br/> Você pode optar por fazer um backup completo em um intervalo diário ou semanal.
**Backup diferencial** | Um backup diferencial é baseado no backup de dados completos mais recente e anterior.<br/><br/> Ele captura apenas os dados que são alterados desde o backup completo. |  No máximo, você pode disparar um backup diferencial por dia.<br/><br/> Você não pode configurar um backup completo e um backup diferencial no mesmo dia.
**Backup de log de transações** | Um backup de log permite a restauração pontual até um segundo específico. | No máximo, você pode configurar backups de log transacionais a cada 15 minutos.

### <a name="comparison-of-backup-types"></a>Comparação de tipos de backup

O consumo de armazenamento, o RTO (objetivo de tempo de recuperação) e o consumo de rede variam de acordo com cada tipo de backup. A imagem a seguir mostra uma comparação dos tipos de backup:

- A fonte de dados A é composta de 10 blocos de armazenamento, A1-A10, cujo backup é feito mensalmente.
- Os blocos A2, A3, A4 e A9 são alterados no primeiro mês e o bloco A5 no mês seguinte.
- Para backups diferenciais, no segundo mês, são feitos backup dos blocos de alteração a2, a3, A4 e A9. No terceiro mês, é criada uma nova cópia de segurança destes mesmos blocos, juntamente com o bloco alterado A5. Continua a ser feita uma cópia de segurança dos blocos alterados até ser feita a próxima cópia de segurança completa.
- Para backups incrementais, no segundo mês, os blocos a2, a3, A4 e A9 são marcados como alterados e transferidos. No terceiro mês, só o bloco A5 é marcado e transferido.

![Imagem mostrando comparações de métodos de backup](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Recursos de backup

A tabela a seguir resume os recursos com suporte para os diferentes tipos de backup:

**Funcionalidade** | **Máquinas locais do Windows Server (diretas)** | **VMs do Azure** | **Computadores ou aplicativos com o DPM/MABS**
--- | --- | --- | ---
Fazer backup no cofre | ![Sim][green] | ![Sim][green] | ![Sim][green]
Fazer backup no disco do DPM/MABS e, em seguida, para o Azure | | | ![Sim][green]
Compactar dados enviados para backup | ![Sim][green] | Nenhuma compactação é usada ao transferir dados. O armazenamento é ligeiramente simples, mas a restauração é mais rápida.  | ![Sim][green]
Executar backup incremental |![Sim][green] |![Sim][green] |![Sim][green]
Fazer backup de discos com eliminação de duplicação | | | ![Parcialmente][yellow]<br/><br/> Para servidores do DPM/MABS implantados somente localmente.

![Chave de tabela](./media/backup-architecture/table-key.png)

## <a name="architecture-direct-backup-of-azure-vms"></a>Arquitetura: backup direto de VMs do Azure

1. Quando você habilita o backup para uma VM do Azure, um backup é executado de acordo com o agendamento especificado.
1. Durante o primeiro backup, uma extensão de backup é instalada na VM se a VM estiver em execução.
    - Para VMs do Windows, a extensão VMSnapshot é instalada.
    - Para VMs do Linux, a extensão do Linux VMSnapshot é instalada.
1. A extensão usa um instantâneo de nível de armazenamento.
    - Para VMs do Windows em execução, o backup coordena com o Windows Serviço de Cópias de Sombra de Volume (VSS) para obter um instantâneo consistente com o aplicativo da VM. Por padrão, o backup faz backups completos do VSS. Se o backup não puder usar um instantâneo consistente com o aplicativo, ele usará um instantâneo consistente com o arquivo.
    - Para VMs do Linux, o backup usa um instantâneo consistente com arquivo. Para instantâneos consistentes com o aplicativo, você precisa personalizar manualmente os scripts Pre/post.
    - O backup é otimizado com o backup de cada disco de VM em paralelo. Para cada disco cujo backup está sendo feito, o backup do Azure lê os blocos no disco e armazena apenas os dados alterados.
1. Depois que o instantâneo é obtido, os dados são transferidos para o cofre.
    - Somente blocos de dados que foram alterados desde o último backup são copiados.
    - Os dados não estão criptografados. O backup do Azure pode fazer backup de VMs do Azure que foram criptografadas usando Azure Disk Encryption.
    - Os dados de instantâneo podem não ser copiados imediatamente para o cofre. Em horários de pico, o backup pode levar algumas horas. O tempo total de backup de uma VM será menor que 24 horas para políticas de backup diárias.
1. Depois que os dados são enviados para o cofre, um ponto de recuperação é criado. Por padrão, os instantâneos são retidos por dois dias antes de serem excluídos. Esse recurso permite a operação de restauração desses instantâneos, reduzindo assim os tempos de restauração. Ele reduz o tempo necessário para transformar e copiar dados de volta do cofre. Consulte [recurso de restauração instantânea do backup do Azure](https://docs.microsoft.com/azure/backup/backup-instant-restore-capability).

As VMs do Azure precisam de acesso à Internet para comandos de controle. Se você estiver fazendo backup de cargas de trabalho dentro da VM (por exemplo, SQL Server backups de banco de dados), o back-end também precisará de acesso à Internet.

![Backup de VMs do Azure](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Arquitetura: backup direto de máquinas locais do Windows Server ou arquivos ou pastas da VM do Azure

1. Para configurar o cenário, baixe e instale o agente MARS no computador. Em seguida, selecione o que fazer backup, quando os backups serão executados e por quanto tempo eles serão mantidos no Azure.
1. O backup inicial é executado de acordo com suas configurações de backup.
1. O agente MARS usa o VSS para fazer um instantâneo pontual dos volumes selecionados para backup.
    - O agente MARS usa apenas a operação de gravação do sistema Windows para capturar o instantâneo.
    - Como o agente não usa nenhum gravador VSS de aplicativo, ele não captura instantâneos consistentes com o aplicativo.
1. Depois de tirar o instantâneo com o VSS, o agente MARS cria um VHD (disco rígido virtual) na pasta de cache que você especificou quando configurou o backup. O agente também armazena somas de verificação para cada bloco de dados.
1. Os backups incrementais são executados de acordo com o agendamento que você especificar, a menos que você execute um backup ad hoc.
1. Em backups incrementais, os arquivos alterados são identificados e um novo VHD é criado. O VHD é compactado e criptografado e é enviado para o cofre.
1. Após a conclusão do backup incremental, o novo VHD é mesclado com o VHD criado após a replicação inicial. Esse VHD mesclado fornece o estado mais recente a ser usado para comparação para o backup em andamento.

![Backup de máquinas locais do Windows Server com o agente MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Arquitetura: fazer backup para o DPM/MABS

1. Instale o DPM ou o agente de proteção do MABS em computadores que você deseja proteger. Em seguida, você adiciona os computadores a um grupo de proteção do DPM.
    - Para proteger as máquinas locais, o servidor DPM ou MABS deve estar localizado localmente.
    - Para proteger as VMs do Azure, o servidor MABS deve estar localizado no Azure, em execução como uma VM do Azure.
    - Com o DPM/MABS, você pode proteger volumes de backup, compartilhamentos, arquivos e pastas. Você também pode proteger o estado do sistema (bare metal) de um computador e pode proteger aplicativos específicos com configurações de backup com reconhecimento de aplicativo.
1. Quando você configura a proteção para um computador ou aplicativo no DPM/MABS, você seleciona para fazer backup no disco local do MABS/DPM para armazenamento de curto prazo e para o Azure para proteção online. Você também especifica quando o backup para o armazenamento do DPM/MABS local deve ser executado e quando o backup online para o Azure deve ser executado.
1. O backup do disco da carga de trabalho protegida é feito nos discos MABS/DPM locais, de acordo com o agendamento especificado.
4. O backup dos discos do DPM/MABS é feito no cofre pelo agente MARS em execução no servidor DPM/MABS.

![Backup de máquinas e cargas de trabalho protegidas pelo DPM ou pelo MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Armazenamento de VM do Azure

As VMs do Azure usam discos para armazenar seu sistema operacional, aplicativos e dados. Cada VM do Azure tem pelo menos dois discos: um disco para o sistema operacional e um disco temporário. As VMs do Azure também podem ter discos de dados para dados de aplicativo. Os discos são armazenados como VHDs.

- Os VHDs são armazenados como BLOBs de páginas nas contas de armazenamento Standard ou Premium no Azure:
  - **Armazenamento Standard:** Suporte a disco confiável e de baixo custo para VMs que executam cargas de trabalho que não são sensíveis à latência. O armazenamento Standard pode usar discos SSD (unidade de estado sólido) padrão ou discos de unidade de disco rígido (HDD) padrão.
  - **Armazenamento Premium:** Suporte a disco de alto desempenho. Usa discos SSD Premium.
- Há diferentes níveis de desempenho para discos:
  - **HDD Standard disco:** Apoiado por HDDs e usado para armazenamento econômico.
  - **SSD Standard disco:** Combina elementos de discos SSD Premium e discos HDD padrão. Oferece desempenho e confiabilidade mais consistentes do que o HDD, mas ainda econômico.
  - **SSD Premium disco:** Apoiado pelo SSDs e fornece alto desempenho e baixa latência para VMs que executam cargas de trabalho intensivas de e/s.
- Os discos podem ser gerenciados ou não gerenciados:
  - **Discos não gerenciados:** Tipo tradicional de discos usados pelas VMs. Para esses discos, você cria sua própria conta de armazenamento e a especifica ao criar o disco. Em seguida, você precisa descobrir como maximizar os recursos de armazenamento para suas VMs.
  - **Discos gerenciados:** O Azure cria e gerencia as contas de armazenamento para você. Você especifica o tamanho do disco e o nível de desempenho, e o Azure cria discos gerenciados para você. À medida que você adiciona discos e dimensiona VMs, o Azure lida com as contas de armazenamento.

Para obter mais informações sobre o armazenamento em disco e os tipos de disco disponíveis para VMs, consulte estes artigos:

- [Azure Managed disks para VMs Windows](../virtual-machines/windows/managed-disks-overview.md)
- [Azure Managed disks para VMs Linux](../virtual-machines/linux/managed-disks-overview.md)
- [Tipos de disco disponíveis para VMs](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Fazer backup e restaurar VMs do Azure com o armazenamento Premium

Você pode fazer backup de VMs do Azure usando o armazenamento Premium com o backup do Azure:

- Durante o processo de backup de VMs com o armazenamento Premium, o serviço de backup cria um local temporário de preparo, chamado *AzureBackup-* , na conta de armazenamento. O tamanho do local de preparo é igual ao tamanho do instantâneo do ponto de recuperação.
- Certifique-se de que a conta de armazenamento Premium tenha espaço livre suficiente para acomodar o local temporário de preparo. [Saiba mais](../storage/common/storage-scalability-targets.md#premium-performance-storage-account-scale-limits). Não modifique o local de preparo.
- Após a conclusão do trabalho de backup, o local de preparo será excluído.
- O preço do armazenamento usado para o local de preparo é consistente com os [preços do armazenamento Premium](../virtual-machines/windows/disks-types.md#billing).

Ao restaurar as VMs do Azure usando o armazenamento Premium, você pode restaurá-las para o armazenamento Premium ou Standard. Normalmente, você os restauraria para o armazenamento Premium. Mas se você precisar apenas de um subconjunto de arquivos da VM, pode ser econômico restaurá-los para o armazenamento padrão.

### <a name="back-up-and-restore-managed-disks"></a>Fazer backup e restaurar discos gerenciados

Você pode fazer backup de VMs do Azure com discos gerenciados:

- Você faz backup de VMs com Managed disks da mesma maneira que faz com qualquer outra VM do Azure. Você pode fazer backup da VM diretamente das configurações da máquina virtual ou pode habilitar o backup para VMs no cofre dos serviços de recuperação.
- Pode criar cópias de segurança de VMs em discos geridos através de coleções de RestorePoint criadas sobre os discos geridos.
- O backup do Azure também dá suporte ao backup de VMs com discos gerenciados que foram criptografados usando Azure Disk Encryption.

Ao restaurar VMs com discos gerenciados, você pode restaurar para uma VM completa com discos gerenciados ou para uma conta de armazenamento:

- Durante o processo de restauração, o Azure lida com os discos gerenciados. Se você estiver usando a opção de conta de armazenamento, você gerenciará a conta de armazenamento que é criada durante o processo de restauração.
- Se você restaurar uma VM gerenciada criptografada, verifique se as chaves e os segredos da VM existem no cofre de chaves antes de iniciar o processo de restauração.

## <a name="next-steps"></a>Passos seguintes

- Examine a matriz de suporte para [saber mais sobre os recursos e limitações com suporte para cenários de backup](backup-support-matrix.md).
- Configure o backup para um destes cenários:
  - [Fazer backup de VMs do Azure](backup-azure-arm-vms-prepare.md).
  - [Faça backup de computadores Windows diretamente](tutorial-backup-windows-server-to-azure.md), sem um servidor de backup.
  - [Configure o mAbs](backup-azure-microsoft-azure-backup.md) para backup no Azure e, em seguida, faça backup das cargas de trabalho para mAbs.
  - [Configure o DPM](backup-azure-dpm-introduction.md) para backup no Azure e, em seguida, faça backup das cargas de trabalho no DPM.

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
