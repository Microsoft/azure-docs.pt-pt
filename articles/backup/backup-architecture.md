---
title: Arquitetura de cópia de segurança do Azure
description: Fornece uma descrição geral da arquitetura, componentes e processos utilizados pelo serviço de cópia de segurança do Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: raynew
ms.openlocfilehash: d3e6a17ba9d0712d921d8e0a1d0bcbcd68ce5cfb
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360327"
---
# <a name="azure-backup-architecture"></a>Arquitetura de cópia de segurança do Azure

Pode utilizar o [serviço de cópia de segurança do Azure](backup-overview.md) faça backup dos dados para a cloud do Microsoft Azure. Este artigo resume a arquitetura, componentes e processos de cópia de segurança do Azure. 


## <a name="what-does-azure-backup-do"></a>O que faz a cópia de segurança do Azure?

O Azure Backup cria uma cópia de segurança de dados, o estado da máquina e cargas de trabalho em execução em máquinas no local e VMs do Azure. Existem vários cenários de cópia de segurança do Azure.

- **Cópia de segurança máquinas no local**:
    - Pode fazer backup de máquinas no local diretamente no Azure utilizando o Azure Backup.
    - Pode proteger as máquinas no local com o System Center Data Protection Manager (DPM) ou o servidor de cópia de segurança do Azure (MABS) da Microsoft e, em seguida, por sua vez criar cópias de segurança dos dados protegidos no DPM/MABS para o Azure utilizando o Azure Backup.
- **Cópia de segurança de VMs do Azure**:
    - Pode fazer uma cópia de segurança de VMs do Azure diretamente com o Azure Backup.
    - Pode proteger VMs do Azure com o DPM ou MABS em execução no Azure e, em seguida, por sua vez criar cópias de segurança dos dados protegidos em dados DPM/MABS com o Azure Backup.

Saiba mais sobre [o que pode criar cópias de segurança](backup-overview.md), e [cenários de cópia de segurança suportados](backup-support-matrix.md).


## <a name="where-is-data-backed-up"></a>Em que dados de cópia de segurança?

Arquivos de cópia de segurança do Azure backup de dados num cofre dos serviços de recuperação. Um cofre é uma entidade de armazenamento online no Azure, o que é utilizado para armazenar dados, como cópias de segurança, pontos de recuperação e políticas de cópia de segurança.

- Os cofres dos Serviços de Recuperação facilitam a organização dos dados de cópia de segurança ao minimizar os custos de gestão.
- Em cada subscrição do Azure, pode criar até 500 cofres dos serviços de recuperação. 
- Pode monitorizar itens de cópia de segurança num cofre, incluindo as máquinas de VMs do Azure e no local.
- Pode gerir o acesso do cofre com o Azure [controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
- Especifique a forma como os dados no cofre são replicados para redundância:
    - **LRS**: Pode utilizar o armazenamento localmente redundante (LRS) para protege contra falhas num Data Center. LRS replica os dados para uma unidade de escala de armazenamento. [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs).
    - **GRS**: Pode utilizar o armazenamento georredundante (GRS): Protege contra falhas de toda a região. Replica os dados para uma região secundária. [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs). 
    - Por predefinição, os cofres dos serviços de recuperação para cópia de segurança utilizam GRS. 



## <a name="how-does-azure-backup-work"></a>Como funciona a cópia de segurança do Azure?

Execuções de cópia de segurança do Azure com base num padrão de cópias de segurança ou política de cópia de segurança personalizada. A forma em que o Azure Backup cria uma cópia de segurança depende do cenário.

**Cenário** | **Detalhes** 
--- | ---
**Diretamente fazer cópias de segurança de máquinas no local** | Para fazer diretamente a cópias de segurança de máquinas no local, o Azure Backup utiliza o agente dos serviços de recuperação do Azure (MARS) da Microsoft. O agente está instalado em cada máquina que pretende criar cópias de segurança. <br/><br/> Este tipo de cópia de segurança não está disponível para máquinas do Linux no local. 
**Diretamente fazer cópias de segurança de VMs do Azure** | Para fazer diretamente a cópias de segurança de VMs do Azure, uma extensão de VM do Azure está instalada na VM de cópia de segurança é executada na primeira vez para a VM. 
**Fazer uma cópia de segurança de máquinas e aplicações protegidas pelo DPM ou MABS** | Neste cenário, a aplicação/máquina é primeiro uma cópia de segurança para o DPM ou MABS armazenamento local. Em seguida, os dados no DPM/MABS é uma cópia de segurança para o Cofre pelo Azure Backup. As máquinas no local podem ser protegidas pelo DPM/MABS em execução no local. VMs do Azure podem ser protegidas pelo DPM/MABS em execução no Azure.

[Obtenha uma descrição geral](backup-overview.md)e veja [o que é suportado](backup-support-matrix.md) para cada cenário.


### <a name="backup-agents"></a>Agentes de cópia de segurança

O Azure Backup fornece agentes diferentes, dependendo do tipo de cópia de segurança.

**Agente** | **Detalhes** 
--- | --- 
**Agente dos serviços de recuperação do Azure (MARS) da Microsoft** | Este agente é executado em servidores de Windows de individual no local para fazer backup de arquivos, pastas e estado do sistema<br/><br/> Este agente é executado em servidores DPM/MABS para criar cópias de segurança do disco de armazenamento local do DPM/MABS. As máquinas e aplicações são uma cópia de segurança localmente para este disco do DPM/MABS.
**Extensão VM do Azure** | Para fazer uma cópia de segurança de VMs do Azure, uma extensão de cópia de segurança é adicionada para o agente de VM do Azure em execução nas VMs. 


## <a name="backup-types"></a>Tipos de cópia de segurança

**Tipo de cópia de segurança** | **Detalhes** | **Utilização**
--- | --- | ---
**Completa** | Uma cópia de segurança contém a origem de dados completa.<br/><br/> Cópia de segurança completa demora mais largura de banda de rede. | Utilizado para a cópia de segurança inicial.
**Differential** |  Armazena os blocos que sofreram alterações desde a cópia de segurança completa inicial. Utiliza uma quantidade menor de rede e armazenamento e não retêm cópias redundantes de dados inalterados.<br/><br/> Ineficiente porque os blocos de dados inalterados entre cópias de segurança subsequentes são transferidos e armazenados. | Não é utilizado pelo Azure Backup.
**Incremental** | Elevada eficiência de armazenamento e rede. Armazena apenas os blocos de dados que foram alterados desde a cópia de segurança anterior. <br/><br/> Não é necessário para com a cópia de segurança incremental, não é necessário para complementar com cópias de segurança completas. | Utilizado pelo DPM/MABS para cópias de segurança do disco e utilizado em todas as cópias de segurança para o Azure.

### <a name="comparison"></a>Comparação

Consumo de armazenamento, objetivo (RTO) de tempo de recuperação e consumo de rede varia em cada tipo de cópia de segurança. A imagem seguinte mostra uma comparação dos tipos de cópia de segurança.
- Origem de dados A é composta por armazenamento 10 bloqueia A1 a A10, que são cópia de segurança mensal.
- Os blocos A2, A3, A4 e A9 são alterados no primeiro mês e o bloco A5 no mês seguinte.
- Para cópias de segurança diferenciais, no segundo mês, os blocos alterados A2, A3, A4 e A9 são uma cópia de segurança. No terceiro mês, é criada uma nova cópia de segurança destes mesmos blocos, juntamente com o bloco alterado A5. Continua a ser feita uma cópia de segurança dos blocos alterados até ser feita a próxima cópia de segurança completa.
- Para cópias de segurança incrementais, depois de tomar a cópia de segurança completa no primeiro mês, os blocos A2, A3, A4 e A9 são marcados como alterados e transferidos para o mês. No terceiro mês, só o bloco A5 é marcado e transferido. 

![imagem que mostra a comparação dos métodos de cópia de segurança](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Recursos de backup

A tabela seguinte resume as funcionalidades para diferentes tipos de cópia de segurança.

**Funcionalidade** | **Máquinas do Windows (direct) no local** | **VMs do Azure** | **Máquinas/aplicações com o DPM/MABS**
--- | --- | --- | ---
Criar cópias de segurança para o Cofre | ![Sim][green] | ![Sim][green] | ![Sim][green] 
Cópia de segurança em disco do DPM/MABS, em seguida, Azure | | | ![Sim][green] 
Comprimir dados enviados para cópia de segurança | ![Sim][green] | Não é utilizada compressão durante a transferência de dados. Armazenamento é preencher um pouco, mas a restauração é mais rápida.  | ![Sim][green] 
Executar cópia de segurança incremental |![Sim][green] |![Sim][green] |![Sim][green] 
Criar cópias de segurança com eliminação de duplicados discos | | | ![Parcialmente][yellow]<br/><br/> Para o DPM/MABS servidores implementados apenas no local. 

![chave da tabela](./media/backup-architecture/table-key.png)


## <a name="architecture-direct-backup-of-on-premises-windows-machines"></a>Arquitetura: Cópia de segurança direta de máquinas do Windows no local

1. Para configurar o cenário, transferir e instalar o agente dos serviços de recuperação do Azure (MARS) da Microsoft na máquina, selecione o que fazer cópias de segurança, quando as cópias de segurança serão executada e o intervalo de tempo vai ser mantidos no Azure.
2. A cópia de segurança inicial é executada de acordo com as definições de cópia de segurança.
3. O agente de MARS utiliza o serviço de cópia de sombra de volumes (VSS) do Windows para tirar um instantâneo de ponto no tempo dos volumes selecionados para cópia de segurança.
    - O agente de MARS utiliza apenas escrever de sistema do Windows para capturar o instantâneo.
    - O agente não usa qualquer escritores VSS do aplicativo e, portanto, não captura instantâneos consistentes com a aplicação.
3. Depois de criar o instantâneo com o VSS, o agente de MARS cria um VHD na pasta de cache que especificou quando configurou a cópia de segurança, e armazena as somas de verificação para cada blocos de dados. 
4. Cópias de segurança incrementais executam em conformidade com o agendamento que especificar, a menos que executar uma cópia de segurança ad-hoc.
5. Cópias de segurança incrementais, os ficheiros alterados são identificados e é criado um novo VHD. Ele tem comprimidos e encriptados e enviadas para o cofre.
6. Após a conclusão da cópia de segurança incremental, o novo VHD é mesclado com o VHD criado após a replicação inicial, fornecendo o estado mais recente a ser utilizado para comparação para cópia de segurança em curso. 

![Cópia de segurança do servidor do Windows no local com o agente MARS](./media/backup-architecture/architecture-on-premises-mars.png)


## <a name="architecture-direct-backup-of-azure-vms"></a>Arquitetura: Cópia de segurança direta de VMs do Azure

1. Quando ativa a cópia de segurança para uma VM do Azure, é executada uma cópia de segurança em conformidade com o agendamento que especificar.
2. Durante a primeira cópia de segurança, uma extensão de cópia de segurança é instalada na VM, se estiver em execução.
    - Para VMs do Windows a VMSnapshot extensão é instalada.
    - Para VMs do Linux, a extensão de VMSnapshot Linux está instalada.
3. A extensão tira um instantâneo de nível de armazenamento. 
    - Para as VMs do Windows que estão a ser executado, cópia de segurança coordena com VSS para tirar um instantâneo consistente com a aplicação da VM. Por predefinição a cópia de segurança permite cópias de segurança completas do VSS. Se a cópia de segurança não consegue tirar um instantâneo consistente com a aplicação, em seguida, é tirado um instantâneo consistente com ficheiros.
    - Para a cópia de segurança do Linux VMs usa uma cópia de segurança consistente com ficheiros. Para instantâneos consistentes com a aplicação terá de personalizar os scripts de pré/pós.
    - Cópia de segurança está otimizada fazendo o backup de cada disco VM em paralelo. Para cada disco a cópia de segurança, o Azure Backup lê os blocos no disco e armazena apenas os dados alterados. 
4. Depois do instantâneo é tirado, os dados são transferidos para o cofre. 
    - Apenas blocos de dados que foram alterados desde a última cópia de segurança são copiados.
    - Dados não estão encriptados. Cópia de segurança do Azure pode fazer uma cópia de segurança de VMs do Azure que são encriptados com a encriptação de disco do Azure (ADE).
    - Dados de instantâneos podem não ser imediatamente copiados para o cofre. Poderá demorar algumas horas em períodos de pico. Tempo total de cópia de segurança para uma VM será menor que 24 horas para políticas de cópia de segurança diárias.
5. Depois de dados foram enviados para o cofre, o instantâneo é removido e é criado um ponto de recuperação.


![Cópia de segurança de VMs do Azure](./media/backup-architecture/architecture-azure-vm.png)


## <a name="architecture-back-up-to-dpmmabs"></a>Arquitetura: Cópia de segurança para DPM/MABS

1. Instalar o agente de proteção do DPM ou MABS nas máquinas que pretende proteger e adicionar as máquinas para um grupo de proteção do DPM.
    - Para proteger máquinas no local, o servidor DPM ou MABS tem de ser localizada no local.
    - Para proteger as VMs do Azure, o servidor DPM ou MABS tem de estar localizado no Azure, a ser executado como uma VM do Azure.
    - Com o DPM/MABS pode proteger volumes, partilhas, ficheiros e pasta de cópia de segurança. Pode proteger máquinas do sistema Estado/bare-metal e proteger aplicações específicas com as definições de cópia de segurança com suporte para a aplicação.
2. Quando configurar a proteção para uma máquina ou aplicação no DPM, selecione para criar cópias de segurança para o disco local do DPM para armazenamento de curta duração e para o Azure (proteção online). Também especificar quando a cópia de segurança para o armazenamento do DPM/MABS local deve ser executada e, quando a cópia de segurança online para o Azure deve ser executada.
3. O disco da carga de trabalho protegida é uma cópia de segurança para os discos do DPM locais e para o Azure, de acordo com a agenda que especificou.
4. A cópia de segurança online para o Cofre é processada pelo agente de MARS em execução no servidor do DPM/MABS.

![Cópia de segurança de máquinas/cargas de trabalho protegidas pelo DPM ou MABS](./media/backup-architecture/architecture-dpm-mabs.png)







## <a name="azure-vm-storage"></a>Armazenamento VM do Azure

- VMs do Azure utilizam discos para armazenar o respetivo sistema operativo, aplicações e dados.
- As VMs do Azure têm, pelo menos, dois discos. Uma para o sistema operativo e um disco temporário. Eles também podem ter discos de dados para dados de aplicativos. Os discos são armazenados como VHDs.
- VHD são armazenadas como blobs de páginas em contas de armazenamento standard ou premium no Azure.
    - Armazenamento Standard: Suporte para VMs que executam cargas de trabalho que não são sensíveis a latência de discos de fiável e económica. Armazenamento Standard pode utilizar os discos standard SSD ou discos SSD standard).
    - Armazenamento Premium: Suporte de disco de alto desempenho. Utiliza discos SSD premium.
- Existem escalões de desempenho diferentes para discos:
    - Disco standard de HDD: Protegido por HDDs e utilizado para armazenamento económico.
    - Disco SSD Standard: Combina o elemento de discos SSD premium e standard HDD discos, oferecendo um desempenho mais consistente e a confiabilidade de HDD, mas ainda económica.
    - Premium SSD disk: Sustentada pelo SSD, fornecendo alto desempenho e baixa latência para VMs com cargas de trabalho intensivas de e/s.
- Discos podem ser geridos ou não geridos:
    - Discos não geridos: Tradicionais tipos de discos utilizados por VMs. Para estes discos, crie a sua própria conta de armazenamento e especificá-lo ao criar o disco. Precisa descobrir como maximizar os recursos de armazenamento para as suas VMs.
    - Discos geridos: O Azure trata criar e gerir contas de armazenamento para si. Especificar o escalão de tamanho e o desempenho de disco e o Azure cria, gerir discos por si. À medida que adiciona discos e dimensionar VMs, o Azure trata armazenamento.

Leia mais:

- Saiba mais sobre o armazenamento em disco para [Windows](../virtual-machines/windows/about-disks-and-vhds.md) e [Linux](../virtual-machines/linux/about-disks-and-vhds.md) VMs.
- Saiba mais sobre [padrão](../virtual-machines/windows/standard-storage.md) e [premium](../virtual-machines/windows/premium-storage.md) armazenamento.


### <a name="backing-up-and-restoring-azure-vms-with-premium-storage"></a>Fazer backup e restaurar VMs do Azure com o armazenamento premium 

Pode fazer backup de VMs do Azure com o armazenamento premium com o Azure Backup:

- Ao backup das VMs com o armazenamento premium, o serviço de cópia de segurança cria uma localização de transição temporária, com o nome "AzureBackup-", na conta de armazenamento. O tamanho da localização de transição é igual ao tamanho do instantâneo do ponto de recuperação.
- Certifique-se de que a conta de armazenamento premium tem espaço livre suficiente para acomodar a localização de transição temporária. [Saiba mais](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets). Não modifique a localização de transição.
- Depois de concluída a tarefa de cópia de segurança, a localização de transição é eliminada.
- O preço de armazenamento utilizado para a localização de transição é consistente com [preços de armazenamento premium](../virtual-machines/windows/premium-storage.md#pricing-and-billing).

Ao restaurar VMs do Azure com o armazenamento premium, pode restaurá-las para premium ou o armazenamento standard. Normalmente, seria restaurar para premium, mas pode ser rentável restaurar padrão, se tiver apenas um subconjunto de ficheiros da VM.

### <a name="backing-up-and-restoring-managed-disks"></a>Fazer backup e restaurar discos geridos

Pode fazer backup de VMs do Azure com discos geridos.
- Fazer uma cópia de segurança de VMs com discos geridos da mesma forma que faça qualquer outra VM do Azure. Pode criar cópias de segurança da VM diretamente a partir de definições da máquina virtual, ou pode ativar a cópia de segurança para as VMs no cofre dos serviços de recuperação.
- Pode criar cópias de segurança de VMs em discos geridos através de coleções de RestorePoint criadas sobre os discos geridos.
- Cópia de segurança do Azure também suporta o backup de disco gerido de VMs encriptadas com o Azure Disk encryption (ADE).

Quando restaurar VMs com discos geridos, pode restaurar para uma VM completa com discos geridos, ou para uma conta de armazenamento.
- O Azure trata os discos geridos durante o processo de restauro e, com a opção de conta de armazenamento, que gere a conta de armazenamento que é criada durante o restauro.
- Se restaurar uma VM gerida que é criptografada, os segredos e chaves VM deverá ser encerrada no Cofre de chaves antes de iniciar o processo de restauração.




## <a name="next-steps"></a>Passos Seguintes

- [Revisão](backup-support-matrix.md) a matriz de suporte para saber mais sobre as funcionalidades suportadas e limitações para cenários de cópia de segurança.
- Configure a cópia de segurança para um dos cenários:
    - [Fazer cópia de segurança de VMs do Azure](backup-azure-arm-vms-prepare.md)
    - [Cópia de segurança diretamente a máquinas Windows](tutorial-backup-windows-server-to-azure.md), sem um servidor de cópia de segurança.
    - [Configurar o MABS](backup-azure-microsoft-azure-backup.md) para cópia de segurança para o Azure e, em seguida, de cópia de segurança de cargas de trabalho para o MABS.
    - [Configurar o DPM](backup-azure-dpm-introduction.md) para cópia de segurança para o Azure e, em seguida, de cópia de segurança de cargas de trabalho para o DPM.


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

