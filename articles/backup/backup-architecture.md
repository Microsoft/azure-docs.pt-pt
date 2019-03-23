---
title: Arquitetura de cópia de segurança do Azure
description: Fornece uma descrição geral da arquitetura, componentes e processos utilizados pelo serviço de cópia de segurança do Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: 98ffe145103b4be04014627ed04d04dcf7542015
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368964"
---
# <a name="azure-backup-architecture"></a>Arquitetura de cópia de segurança do Azure

Pode utilizar o [serviço de cópia de segurança do Azure](backup-overview.md) para criar cópias de segurança para a plataforma de cloud do Microsoft Azure. Este artigo resume a arquitetura, componentes e processos de cópia de segurança do Azure. 

## <a name="what-does-azure-backup-do"></a>O que faz a cópia de segurança do Azure?

O Azure Backup cria cópias de segurança de dados, o estado da máquina e cargas de trabalho em execução em máquinas no local e instâncias de máquina virtual do Azure (VM). Existem vários cenários de cópia de segurança do Azure.

## <a name="how-does-azure-backup-work"></a>Como funciona a cópia de segurança do Azure?

Pode fazer backup de máquinas e dados utilizando uma série de métodos:

- **Cópia de segurança máquinas no local**:
    - Pode criar cópias de segurança máquinas do Windows no local diretamente no Azure utilizando o agente de cópia de segurança Microsoft Azure Recovery dos serviços Azure (MARS). As máquinas Linux não são suportadas.
    - Pode fazer backup de máquinas no local para um servidor de cópia de segurança (System Center Data Protection Manager (DPM) ou Microsoft Azure Backup Server (MABS)). Em seguida, pode fazer backup do servidor de cópia de segurança para um cofre dos serviços de recuperação do Azure.

- **Cópia de segurança de VMs do Azure**:
    - Pode fazer backup de VMs do Azure diretamente. O Azure Backup instala uma extensão de cópia de segurança para o agente de VM do Azure que está em execução na VM. Esta extensão cria cópias de segurança de toda a VM.
    - Pode criar cópias de segurança específicos ficheiros e pastas na VM do Azure ao executar o agente MARS.
    - Pode fazer backup de VMs do Azure para o MABS que está em execução no Azure e, em seguida, pode criar cópias de segurança o MABS para um cofre dos serviços de recuperação.

Saiba mais sobre [o que pode criar cópias de segurança](backup-overview.md) e sobre [cenários de cópia de segurança suportados](backup-support-matrix.md).

## <a name="where-is-data-backed-up"></a>Em que dados de cópia de segurança?

Cópia de segurança do Azure armazena dados de cópia de segurança num cofre dos serviços de recuperação. Um cofre é uma entidade de armazenamento online no Azure que é utilizada para armazenar dados, como cópias de segurança, pontos de recuperação e políticas de cópia de segurança.

Os cofres dos serviços de recuperação têm as seguintes funcionalidades:

- Cofres facilitam a organizar os dados de cópia de segurança, enquanto minimiza a sobrecarga de gerenciamento.
- Em cada subscrição do Azure, pode criar até 500 cofres.
- Pode monitorizar itens de cópia de segurança num cofre, incluindo as máquinas de VMs do Azure e no local.
- Pode gerir o acesso do cofre com o Azure [controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
- Especifique a forma como os dados no cofre são replicados para redundância:
    - **Armazenamento localmente redundante (LRS)**: Para proteger contra falhas num Data Center, pode usar LRS. LRS replica os dados para uma unidade de escala de armazenamento. [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
    - **Armazenamento georredundante (GRS)**: Para proteger contra interrupções de toda a região, pode usar o GRS. GRS replica os dados para uma região secundária. [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). 
    - Por predefinição, os cofres dos serviços de recuperação utilizam GRS. 

## <a name="backup-agents"></a>Agentes de cópia de segurança

Cópia de segurança do Azure fornece os agentes de cópia de segurança diferentes, dependendo do tipo de máquina é a cópia de segurança:

**Agente** | **Detalhes** 
--- | --- 
**Agente MARS** | <ul><li>É executada nas máquinas do Windows Server individuais no local para fazer backup de arquivos, pastas e o estado do sistema.</li> <li>É executada em VMs do Azure para fazer uma cópia de segurança de ficheiros, pastas e o estado do sistema.</li> <li>É executado em servidores DPM/MABS para criar cópias de segurança do disco de armazenamento local do DPM/MABS para o Azure.</li></ul> 
**Extensão VM do Azure** | É executada em VMs do Azure para fazer o backup deles até um cofre.

## <a name="backup-types"></a>Tipos de cópia de segurança

A tabela seguinte explica os diferentes tipos de cópias de segurança e quando são usadas:

**Tipo de cópia de segurança** | **Detalhes** | **Utilização**
--- | --- | ---
**Completa** | Uma cópia de segurança completa contém a origem de dados completa. Demora mais largura de banda de rede que as cópias de segurança diferenciais ou incrementais. | Utilizado para a cópia de segurança inicial.
**Differential** |  Uma cópia de segurança diferencial armazena os blocos que sofreram alterações desde a cópia de segurança completa inicial. Utiliza uma quantidade menor de rede e armazenamento e não manter cópias redundantes de dados não alterados.<br/><br/> Ineficiente porque os blocos de dados que são inalterados entre backups posteriores são transferidos e armazenados. | Não é utilizado pelo Azure Backup.
**Incremental** | Cópia de segurança incremental armazena apenas os blocos de dados que sofreram alterações desde a cópia de segurança anterior. Elevada eficiência de armazenamento e rede. <br/><br/> Com a cópia de segurança incremental, não é necessário para complementar com cópias de segurança completas. | Utilizado pelo DPM/MABS para cópias de segurança do disco e utilizado em todas as cópias de segurança para o Azure.

## <a name="sql-server-backup-types"></a>Tipos de cópia de segurança do SQL Server

A tabela seguinte explica os diferentes tipos de cópias de segurança utilizados para bases de dados do SQL Server e a frequência com que eles são usados:

**Tipo de cópia de segurança** | **Detalhes** | **Utilização**
--- | --- | ---
**Cópia de segurança completa** | Uma cópia de segurança completa da base de dados faz backup de todo o banco de dados. Contém todos os dados numa base de dados específica ou num conjunto de grupos de ficheiros ou ficheiros. Uma cópia de segurança completa também contém suficiente registos para recuperar os dados. | No máximo, pode acionar uma cópia de segurança completa por dia.<br/><br/> Pode optar por tornar totalmente a cópia de segurança num intervalo diário ou semanal.
**Cópia de segurança diferencial** | Uma cópia de segurança diferencial baseia-se a mais recente, anterior completo dados cópia de segurança.<br/><br/> Ele captura apenas os dados que são alterados desde a cópia de segurança completa. |  No máximo, pode acionar uma cópia de segurança diferencial por dia.<br/><br/> Não é possível configurar uma cópia de segurança completa e uma cópia de segurança diferencial no mesmo dia.
**Cópia de segurança de registo de transações** | Uma cópia de segurança do registo permite que o restauro de ponto no tempo até um segundo específico. | No máximo, pode configurar cópias de segurança do registo transacional a cada 15 minutos.

### <a name="comparison-of-backup-types"></a>Comparação dos tipos de cópia de segurança

Consumo de armazenamento, objetivo (RTO) de tempo de recuperação e consumo de rede varia em cada tipo de cópia de segurança. A imagem seguinte mostra uma comparação dos tipos de cópia de segurança:

- Origem de dados A é composta por 10 armazenamento blocos, A1 a A10, que são cópia de segurança mensal.
- Os blocos A2, A3, A4 e A9 são alterados no primeiro mês e o bloco A5 no mês seguinte.
- Para cópias de segurança diferenciais, no segundo mês, os blocos alterados A2, A3, A4 e A9 são uma cópia de segurança. No terceiro mês, é criada uma nova cópia de segurança destes mesmos blocos, juntamente com o bloco alterado A5. Continua a ser feita uma cópia de segurança dos blocos alterados até ser feita a próxima cópia de segurança completa.
- Para cópias de segurança incrementais, no segundo mês, os blocos A2, A3, A4 e A9 são marcados como alterados e transferidos. No terceiro mês, só o bloco A5 é marcado e transferido. 

![imagem que mostra a comparação dos métodos de cópia de segurança](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Recursos de backup

A tabela seguinte resume os recursos com suporte para os diferentes tipos de cópia de segurança:

**Funcionalidade** | **Máquinas do Windows Server no local (direct)** | **VMs do Azure** | **Máquinas ou aplicações com o DPM/MABS**
--- | --- | --- | ---
Criar cópias de segurança para o Cofre | ![Sim][green] | ![Sim][green] | ![Sim][green] 
Criar cópias de segurança para o disco do DPM/MABS, em seguida, para o Azure | | | ![Sim][green] 
Comprimir dados enviados para cópia de segurança | ![Sim][green] | Não é utilizada compressão durante a transferência de dados. Armazenamento é preencher um pouco, mas a restauração é mais rápida.  | ![Sim][green] 
Executar cópia de segurança incremental |![Sim][green] |![Sim][green] |![Sim][green] 
Criar cópias de segurança com eliminação de duplicados discos | | | ![Parcialmente][yellow]<br/><br/> Para o DPM/MABS servidores implementados apenas no local. 

![chave da tabela](./media/backup-architecture/table-key.png)

## <a name="architecture-direct-backup-of-azure-vms"></a>Arquitetura: Cópia de segurança direta de VMs do Azure

1. Quando ativa a cópia de segurança para uma VM do Azure, é executada uma cópia de segurança, de acordo com a agenda que especificar.
1. Durante a primeira cópia de segurança, uma extensão de cópia de segurança está instalada na VM, se a VM está em execução.
    - Para VMs do Windows, a extensão VMSnapshot está instalada.
    - Para VMs do Linux, a extensão de VMSnapshot Linux está instalada.
1. A extensão tira um instantâneo de nível de armazenamento. 
    - Para as VMs do Windows que estão a ser executado, cópia de segurança, as coordenadas com o Windows Volume de cópia sombra Service (VSS) para tirar um instantâneo consistente com a aplicação da VM. Por predefinição, a cópia de segurança permite cópias de segurança completas do VSS. Se a cópia de segurança não consegue tirar um instantâneo consistente com a aplicação, em seguida, é tirado um instantâneo consistente com ficheiros.
    - Para VMs do Linux, o Backup tira um instantâneo consistente com ficheiros. Para instantâneos consistentes com a aplicação, terá de personalizar os scripts de pré/pós.
    - Cópia de segurança está otimizada fazendo o backup de cada disco VM em paralelo. Para cada disco a cópia de segurança, o Azure Backup lê os blocos no disco e armazena apenas os dados alterados. 
1. Depois do instantâneo é tirado, os dados são transferidos para o cofre. 
    - Apenas blocos de dados alterados desde a última cópia de segurança são copiados.
    - Dados não estão encriptados. Cópia de segurança do Azure pode fazer uma cópia de segurança de VMs do Azure que era criptografado com o Azure Disk Encryption.
    - Dados de instantâneos podem não ser imediatamente copiados para o cofre. Em horas de ponta, a cópia de segurança poderá demorar algumas horas. Tempo total de cópia de segurança para uma VM será inferior a 24 horas para políticas de cópia de segurança diárias.
1. Depois dos dados são enviados para o cofre, o instantâneo é removido e é criado um ponto de recuperação.

VMs do Azure precisam de acesso à internet para comandos de controlo. Se estiver fazendo backup cargas de trabalho dentro da VM (por exemplo, cópias de segurança do SQL Server da base de dados), os dados de back-end têm também de acesso à internet. 

![Cópia de segurança de VMs do Azure](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>Arquitetura: Cópia de segurança direta de máquinas do Windows Server no local ou pastas ou ficheiros de VM do Azure

1. Para configurar o cenário, transfira e instale o agente de MARS na máquina. Em seguida, selecione o que fazer cópias de segurança, quando as cópias de segurança serão executada e o intervalo de tempo vai ser mantidos no Azure.
1. A cópia de segurança inicial é executada de acordo com as definições de cópia de segurança.
1. O agente de MARS utiliza o VSS para tirar um instantâneo de ponto no tempo dos volumes selecionados para cópia de segurança.
    - O agente de MARS utiliza apenas a operação de escrita do Windows sistema para capturar o instantâneo.
    - Uma vez que o agente não usa qualquer escritores VSS do aplicativo, ele não captura instantâneos consistentes com a aplicação.
1. Depois de criar o instantâneo com o VSS, o agente de MARS cria um disco rígido virtual (VHD) na pasta de cache que especificou quando configurou a cópia de segurança. O agente também armazena as somas de verificação para cada bloco de dados.
1. Executam cópias de segurança incrementais, de acordo com a agenda que especificar,, a menos que executar uma cópia de segurança ad-hoc.
1. Cópias de segurança incrementais, os ficheiros alterados são identificados e é criado um novo VHD. O VHD é compactado e criptografado, e, em seguida, são enviado para o cofre.
1. Após a conclusão da cópia de segurança incremental, o novo VHD é mesclado com o VHD que criou após a replicação inicial. Este VHD intercalado fornece o estado mais recente a ser utilizado para comparação para cópia de segurança em curso.

![Cópia de segurança de máquinas do Windows Server no local com o agente MARS](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>Arquitetura: Cópia de segurança para DPM/MABS

1. Instalar o agente de proteção do DPM ou MABS nas máquinas que pretende proteger. Em seguida, adicione as máquinas para um grupo de proteção do DPM.
    - Para proteger máquinas no local, o servidor DPM ou MABS tem de ser localizada no local.
    - Para proteger as VMs do Azure, o servidor do MABS tem de estar localizado no Azure, a ser executado como uma VM do Azure.
    - Com o DPM/MABS, pode proteger volumes de cópia de segurança, partilhas, ficheiros e pastas. Também pode proteger o estado do sistema de um computador (bare-metal) e que pode proteger aplicações específicas com as definições de cópia de segurança com suporte para a aplicação.
1. Quando configurar a proteção para uma máquina ou aplicação no DPM/MABS, selecione para criar cópias de segurança para o disco local do MABS/DPM para armazenamento de curta duração e para o Azure para proteção online. Também pode especificar quando a cópia de segurança para o armazenamento do DPM/MABS local deve ser executada e quando a cópia de segurança online para o Azure deve ser executada.
1. O disco da carga de trabalho protegida é uma cópia de segurança para os discos do MABS/DPM locais, de acordo com a agenda que especificou.
4. Os discos do DPM/MABS são copiados para o Cofre pelo agente de MARS que está em execução no servidor do DPM/MABS.

![Cópia de segurança de máquinas e cargas de trabalho protegidas pelo DPM ou MABS](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Armazenamento VM do Azure

VMs do Azure utilizam discos para armazenar o respetivo sistema operativo, aplicações e dados. Cada VM do Azure tem, pelo menos, dois discos: um disco para o sistema operativo e um disco temporário. VMs do Azure também podem ter discos de dados para dados de aplicativos. Os discos são armazenados como VHDs.

- Os VHDs são armazenados como blobs de páginas em contas de armazenamento standard ou premium no Azure:
    - **Armazenamento Standard:** Suporte para VMs que executam cargas de trabalho que não são sensíveis a latência de discos de fiável e económica. Armazenamento Standard pode utilizar discos de unidade padrão de estado sólido (SSD) ou discos de unidade de disco rígido (HDD) padrão.
    - **Armazenamento Premium:** Suporte de disco de alto desempenho. Utiliza discos SSD premium.
- Existem escalões de desempenho diferentes para discos:
    - **Disco standard de HDD:** Protegido por HDDs e utilizado para armazenamento económico.
    - **Disco SSD Standard:** Combina elementos de discos SSD premium e os discos HDD standard. Oferece desempenho mais consistente e de confiabilidade que HDD, mas ainda económica.
    - **Premium SSD disk:** Apoiado por SSDs e fornece elevado desempenho e baixa latência para VMs que executam cargas de trabalho de e/S intensivas.
- Discos podem ser geridos ou não geridos:
    - **Discos não geridos:** Tipo de discos utilizados por VMs tradicionais. Para estes discos, crie a sua própria conta de armazenamento e especificá-lo ao criar o disco. Em seguida, terá de descobrir como maximizar os recursos de armazenamento para as suas VMs.
    - **Discos geridos:** O Azure cria e gerencia as contas de armazenamento para. Especificar o escalão de tamanho e o desempenho de disco e Azure cria discos geridos por si. À medida que adiciona discos e dimensionar VMs, o Azure trata as contas de armazenamento.

Para obter mais informações sobre o armazenamento de disco e os tipos de disco disponível para VMs, veja estes artigos:

- [Managed disks do Azure para VMs do Windows](../virtual-machines/windows/managed-disks-overview.md)
- [Managed disks do Azure para VMs do Linux](../virtual-machines/linux/managed-disks-overview.md)
- [Tipos de disco disponível para VMs](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Criar cópias de segurança e restaurar VMs do Azure com o armazenamento premium 

Pode fazer backup de VMs do Azure com o armazenamento premium com o Azure Backup:

- Durante o processo de cópia de segurança de VMs com o armazenamento premium, o serviço de cópia de segurança cria uma localização de transição temporária, com o nome *AzureBackup -*, na conta de armazenamento. O tamanho da localização de transição é igual ao tamanho do instantâneo de ponto de recuperação.
- Certifique-se de que a conta de armazenamento premium tem espaço livre suficiente para acomodar a localização de transição temporária. [Saiba mais](../storage/common/storage-scalability-targets.md#premium-performance-storage-account-scale-limits). Não modifique a localização de transição.
- Depois de concluída a tarefa de cópia de segurança, a localização de transição é eliminada.
- O preço de armazenamento utilizado para a localização de transição é consistente com [preços de armazenamento premium](../virtual-machines/windows/disks-types.md#billing).

Ao restaurar VMs do Azure ao utilizar o armazenamento premium, pode restaurá-las para premium ou o armazenamento standard. Normalmente, seria restaurá-las para o armazenamento premium. Mas, se tiver apenas um subconjunto de ficheiros da VM, pode ser rentável restaurá-los para o armazenamento standard.

### <a name="back-up-and-restore-managed-disks"></a>Criar cópias de segurança e restaurar discos geridos

Pode fazer backup de VMs do Azure com discos geridos:

- Fazer uma cópia de segurança de VMs com discos geridos da mesma forma que faça qualquer outra VM do Azure. Pode criar cópias de segurança da VM diretamente a partir de definições da máquina virtual, ou pode ativar a cópia de segurança para as VMs no cofre dos serviços de recuperação.
- Pode criar cópias de segurança de VMs em discos geridos através de coleções de RestorePoint criadas sobre os discos geridos.
- Cópia de segurança do Azure também suporta a cópia de segurança de VMs com discos geridos que eram criptografados com o Azure Disk Encryption.

Quando restaurar VMs com discos geridos, pode restaurar para uma VM completa com discos geridos ou para uma conta de armazenamento:

- Durante o processo de restauro, o Azure trata os discos geridos. Se estiver a utilizar a opção de conta de armazenamento, que gere a conta de armazenamento que é criada durante o processo de restauração.
- Se restaurar uma VM gerida que é encriptada, certifique-se de chaves da VM e existem segredos no Cofre de chaves, antes de iniciar o processo de restauração.

## <a name="next-steps"></a>Passos Seguintes

- Reveja a matriz de suporte para [Conheça os recursos suportados e limitações para cenários de cópia de segurança](backup-support-matrix.md).
- Configure a cópia de segurança para um desses cenários:
    - [Cópia de segurança de VMs do Azure](backup-azure-arm-vms-prepare.md).
    - [Cópia de segurança diretamente a máquinas Windows](tutorial-backup-windows-server-to-azure.md), sem um servidor de cópia de segurança.
    - [Configurar o MABS](backup-azure-microsoft-azure-backup.md) para cópia de segurança para o Azure e, em seguida, de cópia de segurança de cargas de trabalho para o MABS.
    - [Configurar o DPM](backup-azure-dpm-introduction.md) para cópia de segurança para o Azure e, em seguida, de cópia de segurança de cargas de trabalho para o DPM.


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

