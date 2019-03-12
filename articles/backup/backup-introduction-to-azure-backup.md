---
title: O que é o Backup do Azure?
description: Utilize o Azure Backup para criar cópias de segurança e restaurar dados e cargas de trabalho de Servidores Windows, estações de trabalho do Windows, servidores do System Center DPM e máquinas virtuais do Azure.
services: backup
author: rayne-wiselman
manager: carmonm
keywords: cópia de segurança e restauro; serviços de recuperação; soluções de cópia de segurança
ms.service: backup
ms.topic: overview
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2888f291980b83b0c917e88f65d06ebaa6c8348e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544108"
---
# <a name="overview-of-the-features-in-azure-backup"></a>Descrição geral das funcionalidades do Azure Backup
O Azure Backup é o serviço baseado no Azure que pode utilizar para criar cópias de segurança (ou proteger) e restaurar os dados na nuvem Microsoft. O Azure Backup substitui a solução de cópia de segurança no local ou fora das instalações por uma solução baseada na nuvem que é fiável, segura e competitiva em termos de custos. O Azure Backup oferece vários componentes que são transferidos e implementados no computador ou servidor adequado, ou na nuvem. O componente ou o agente que implementar depende do que pretende proteger. Todos os componentes do Azure Backup (independentemente de estar a proteger dados no local ou na cloud) podem ser utilizados para criar cópias de segurança para um cofre dos Serviços de Recuperação do Azure. Veja a [tabela de componentes do Azure Backup](backup-introduction-to-azure-backup.md#which-azure-backup-components-should-i-use) (mais à frente neste artigo) para obter informações sobre os componentes a utilizar para proteger dados, aplicações ou cargas de trabalho específicos.

[Veja uma descrição geral do vídeo do Azure Backup](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## <a name="why-use-azure-backup"></a>Porquê utilizar o Backup do Azure?
As soluções de cópia de segurança tradicionais evoluíram para tratar a nuvem como um ponto final ou destino de armazenamento estático, semelhante a discos ou bandas. Embora esta abordagem seja simples, é limitada e não tira o máximo partido de uma plataforma na nuvem subjacente, o que se reflete numa solução dispendiosa e ineficaz. Outras soluções são dispendiosas porque paga pelo tipo de armazenamento errado ou por armazenamento de que não precisa. Outras soluções são frequentemente ineficazes porque não oferecem o tipo ou quantidade de armazenamento necessário ou porque as tarefas administrativas consomem demasiado tempo. Em contrapartida, o Azure Backup fornece estas principais vantagens:

**Gestão de armazenamento automática** - os ambientes híbridos necessitam frequentemente de armazenamento heterogéneo - alguns no local e outros na nuvem. Com o Azure Backup, não existe nenhum custo para a utilização de dispositivos de armazenamento no local. O Azure Backup atribui automaticamente e gere o armazenamento de cópia de segurança, e utiliza um modelo de pagamento enquanto utiliza. O pagamento enquanto utiliza significa que paga apenas pelo armazenamento que consumir. Para mais informações, veja o artigo [Preços do Azure](https://azure.microsoft.com/pricing/details/backup).

**Dimensionamento ilimitado** - o Azure Backup utiliza o poder subjacente e o dimensionamento ilimitado da nuvem do Azure para fornecer elevada disponibilidade - sem qualquer overhead de manutenção ou monitorização. Pode configurar alertas para fornecer informações sobre eventos, mas não precisa de se preocupar com a elevada disponibilidade dos seus dados na nuvem.

**Várias opções de armazenamento** - um aspeto de elevada disponibilidade é a replicação do armazenamento. O Azure Backup oferece dois tipos de replicação: [armazenamento localmente redundante](../storage/common/storage-redundancy-lrs.md) e [armazenamento georredundante](../storage/common/storage-redundancy-grs.md). Escolha a opção de armazenamento de cópia de segurança com base na necessidade:

* O armazenamento localmente redundante (LRS) replica os seus dados três vezes (cria três cópias dos dados) numa unidade de escala de armazenamento de um datacenter. Todas as cópias dos dados existem na mesma região. O LRS é uma opção de baixo custo para proteger os dados contra falhas de hardware locais.

* O armazenamento georredundante (GRS) é a predefinição e a opção de replicação recomendada. GRS replica os dados para uma região secundária que é [regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md) (centenas de quilómetros da localização primária da origem de dados). O GRS custa mais do que o LRS, mas o GRS proporciona um nível mais elevado de durabilidade aos seus dados, mesmo se ocorrer uma indisponibilidade regional.

**Transferência de dados ilimitada** - o Azure Backup não limita a quantidade de dados de entrada ou saída que transfere. O Azure Backup também não cobra por dados que sejam transferidos. No entanto, se utilizar o serviço Importar/Exportar do Azure para importar grandes quantidades de dados, existe um custo associado aos dados de entrada. Para mais informações sobre este custo, veja [Fluxo de trabalho de cópia de segurança offline no Azure Backup](backup-azure-backup-import-export.md). Os dados de saída são os dados transferidos a partir de um cofre dos Serviços de Recuperação durante uma operação de restauro.

**Encriptação de dados** - a encriptação de dados permite a transmissão e o armazenamento seguros dos seus dados na nuvem pública. A frase de acesso de encriptação é armazenada por si localmente, e nunca é transmitida ou armazenada no Azure. Se for necessário restaurar quaisquer dados, é o único a ter a frase de acesso ou a chave de encriptação.

**Cópia de segurança consistente com as aplicações** – Uma cópia de segurança consistente com as aplicações significa que um ponto de recuperação tem todos os dados necessários para restaurar a cópia de segurança. O Azure Backup fornece cópias de segurança consistentes com as aplicações, o que garante que não são necessárias correções adicionais para restaurar os dados. Restaurar dados consistentes com as aplicações reduz o tempo de restauro, permitindo-lhe voltar rapidamente a um estado de execução.

**Retenção a longo prazo** – Pode utilizar os cofres dos Serviços de Recuperação para a retenção de dados a curto e longo prazo. O Azure não limita o período de tempo durante o qual os dados podem permanecer num cofre dos Serviços de Recuperação. Pode manter os dados num cofre o tempo que pretender. O Azure Backup tem um limite de 9999 pontos de recuperação por instância protegida. Consulte a secção [Backup and retention (Cópia de segurança e retenção)](backup-introduction-to-azure-backup.md#backup-and-retention) neste artigo para obter uma explicação sobre como este limite pode afetar as suas necessidades de cópia de segurança.

## <a name="which-azure-backup-components-should-i-use"></a>Que componentes do Azure Backup devo utilizar?
Utilize a seguinte tabela para obter informações sobre o que pode proteger com cada componente do Azure Backup.

| Componente | Benefícios | Limites | O que está protegido? | Onde estão armazenadas as cópias de segurança? |
| --- | --- | --- | --- | --- |
| Agente do Backup do Azure (MARS) |<li>Os ficheiros e as pastas de cópia de segurança no SO Windows físico ou virtual (as VMs podem estar no local ou no Azure)<li>Nenhum servidor de cópia de segurança separado necessário. |<li>Criar cópias de segurança 3 vezes por dia <li>Sem deteção de aplicações; restauro apenas ao nível do ficheiro, pasta e volume, <li>  Sem suporte para Linux. |<li>Ficheiros, <li>Pastas, <li>Estado do Sistema |Cofre dos Serviços de Recuperação |
| System Center DPM |<li>Instantâneos de deteção de aplicações (VSS)<li>Total flexibilidade para quando efetuar cópias de segurança<li>Granularidade de recuperação (tudo)<li>Pode utilizar o cofre dos Serviços de Recuperação<li>Apoio técnico para Linux para VMs de Hyper-V e VMware <li>Criar cópias de segurança e restaurar VMs VMware com o DPM 2012 R2 |Não é possível fazer cópias de segurança da carga de trabalho do Oracle.|<li>Ficheiros, <li>Pastas,<li> Volumes, <li>VMs,<li> Aplicações,<li> Cargas de trabalho <li>Estado do Sistema |<li>Cofre dos Serviços de Recuperação,<li> Disco ligado localmente,<li>  Banda (apenas no local) |
| Servidor do Backup do Azure |<li>Instantâneos de deteção de aplicações (VSS)<li>Total flexibilidade para quando efetuar cópias de segurança<li>Granularidade de recuperação (tudo)<li>Pode utilizar o cofre dos Serviços de Recuperação<li>Apoio técnico para Linux para VMs de Hyper-V e VMware<li>Criar cópias de segurança e restaurar VMs VMware <li>Não necessita de uma licença do System Center |<li>Não é possível fazer cópias de segurança da carga de trabalho do Oracle.<li>Requer sempre a subscrição do Azure em direto<li>Sem suporte para cópia de segurança em fila |<li>Ficheiros, <li>Pastas,<li> Volumes, <li>VMs,<li> Aplicações,<li> Cargas de trabalho, <li>Estado do Sistema |<li>Cofre dos Serviços de Recuperação,<li> Disco ligado localmente |
| Cópia de segurança da VM do IaaS do Azure |<li>Instantâneos de deteção de aplicações (VSS)<li>Cópias de segurança nativas para o Windows/Linux<li>Não é necessária qualquer instalação do agente específico<li>Cópia de segurança ao nível dos recursos de infraestrutura sem ser necessária qualquer infraestrutura de cópia de segurança |<li>Criar cópia de segurança de VMs uma vez por dia <li>Restaurar VMs apenas ao nível do disco<li>Não é possível efetuar a cópia de segurança no local |<li>VMs, <li>Todos os discos (com o PowerShell) |<p>Cofre dos Serviços de Recuperação</p> |

## <a name="what-are-the-deployment-scenarios-for-each-component"></a>Quais são os cenários de implementação para cada componente?
| Componente | Pode ser implementado no Azure? | Pode ser implementado no local? | Armazenamento de destino suportado |
| --- | --- | --- | --- |
| Agente do Backup do Azure (MARS) |<p>**Sim**</p> <p>O agente do Backup do Azure pode ser implementado em qualquer VM do Windows Server que é executada no Azure.</p> |<p>**Sim**</p> <p>O agente de Cópia de Segurança pode ser implementado em qualquer máquina física ou VM do Windows Server.</p> |<p>Cofre dos Serviços de Recuperação</p> |
| System Center DPM |<p>**Sim**</p><p>Saiba mais sobre [como proteger cargas de trabalho no Azure utilizando o System Center DPM](backup-azure-dpm-introduction.md).</p> |<p>**Sim**</p> <p>Saiba mais sobre [como proteger cargas de trabalho e VMs no datacenter](https://technet.microsoft.com/system-center-docs/dpm/data-protection-manager).</p> |<p>Disco ligado localmente,</p> <p>Cofre dos Serviços de Recuperação,</p> <p>banda (apenas no local)</p> |
| Servidor do Backup do Azure |<p>**Sim**</p><p>Saiba mais sobre [como proteger cargas de trabalho no Azure utilizando o Servidor do Backup do Azure](backup-azure-microsoft-azure-backup.md).</p> |<p>**Sim**</p> <p>Saiba mais sobre [como proteger cargas de trabalho no Azure utilizando o Servidor do Backup do Azure](backup-azure-microsoft-azure-backup.md).</p> |<p>Disco ligado localmente,</p> <p>Cofre dos Serviços de Recuperação</p> |
| Cópia de segurança da VM do IaaS do Azure |<p>**Sim**</p><p>Parte dos recursos de infraestrutura do Azure</p><p>Especializada para a [cópia de segurança da infraestrutura do Azure como máquinas virtuais do serviço (IaaS)](backup-azure-vms-introduction.md).</p> |<p>**Não**</p> <p>Utilize o System Center DPM para efetuar a cópia de segurança das máquinas virtuais no datacenter.</p> |<p>Cofre dos Serviços de Recuperação</p> |

## <a name="which-applications-and-workloads-can-be-backed-up"></a>Podem ser efetuadas cópias de segurança a que aplicações e cargas de trabalho?
A tabela seguinte fornece uma matriz dos dados e cargas de trabalho que podem ser protegidos com o Azure Backup. A coluna de solução do Azure Backup tem ligações para a documentação de implementação dessa solução.

| Dados ou Carga de Trabalho | Ambiente de origem | Solução do Backup do Azure |
| --- | --- | --- |
| Ficheiros e pastas |Windows Server |<p>[Agente do Backup do Azure](backup-configure-vault.md),</p> <p>[O System Center DPM](backup-azure-dpm-introduction.md) (+ o agente do Backup do Azure),</p> <p>[Servidor do Backup do Azure](backup-azure-microsoft-azure-backup.md) (inclui o agente do Backup do Azure)</p> |
| Ficheiros e pastas |Computador Windows |<p>[Agente do Backup do Azure](backup-configure-vault.md),</p> <p>[O System Center DPM](backup-azure-dpm-introduction.md) (+ o agente do Backup do Azure),</p> <p>[Servidor do Backup do Azure](backup-azure-microsoft-azure-backup.md) (inclui o agente do Backup do Azure)</p> |
| Máquina virtual do Hyper-V (Windows) |Windows Server |<p>[O System Center DPM](backup-azure-backup-sql.md) (+ o agente do Backup do Azure),</p> <p>[Servidor do Backup do Azure](backup-azure-microsoft-azure-backup.md) (inclui o agente do Backup do Azure)</p> |
| Máquina virtual do Hyper-V (Linux) |Windows Server |<p>[O System Center DPM](backup-azure-backup-sql.md) (+ o agente do Backup do Azure),</p> <p>[Servidor do Backup do Azure](backup-azure-microsoft-azure-backup.md) (inclui o agente do Backup do Azure)</p> |
| Máquina virtual VMware |Windows Server |<p>[O System Center DPM](backup-azure-backup-sql.md) (+ o agente do Backup do Azure),</p> <p>[Servidor do Backup do Azure](backup-azure-microsoft-azure-backup.md) (inclui o agente do Backup do Azure)</p> |
| Microsoft SQL Server |Windows Server |<p>[O System Center DPM](backup-azure-backup-sql.md) (+ o agente do Backup do Azure),</p> <p>[Servidor do Backup do Azure](backup-azure-microsoft-azure-backup.md) (inclui o agente do Backup do Azure)</p> |
| Microsoft SharePoint |Windows Server |<p>[O System Center DPM](backup-azure-backup-sql.md) (+ o agente do Backup do Azure),</p> <p>[Servidor do Backup do Azure](backup-azure-microsoft-azure-backup.md) (inclui o agente do Backup do Azure)</p> |
| Microsoft Exchange |Windows Server |<p>[O System Center DPM](backup-azure-backup-sql.md) (+ o agente do Backup do Azure),</p> <p>[Servidor do Backup do Azure](backup-azure-microsoft-azure-backup.md) (inclui o agente do Backup do Azure)</p> |
| VMs do IaaS do Azure (Windows) |em execução no Azure |[Azure Backup (extensão da VM)](backup-azure-vms-introduction.md) |
| VMs do IaaS do Azure (Linux) |em execução no Azure |[Azure Backup (extensão da VM)](backup-azure-vms-introduction.md) |

## <a name="linux-support"></a>Apoio Técnico para Linux
A tabela seguinte mostra os componentes de cópia de segurança do Azure suportados para o Linux.  

**Componente** | **Linux (aprovado pelo Azure)**
--- | ---
Agente do Backup do Azure (MARS) | Nenhum (baseado em Windows agente apenas)
System Center DPM | Cópia de segurança consistente com ficheiros de VMs de Convidado Linux no Hyper-V e no VMWare<br/><br/> Restauro de VMs de Convidado do Linux do Hyper-V e do VMWare</br></br> Cópia de segurança consistente com ficheiros não está disponível para VMs do Azure
Servidor do Backup do Azure | Cópia de segurança consistente com ficheiros de VMs de Convidado Linux no Hyper-V e no VMWare<br/><br/> Restaurar a VM de Hyper-V e VMs de convidado de Linux do VMWare</br></br> Cópia de segurança consistente com ficheiros não está disponível para VMs do Azure
Cópia de segurança da VM do IaaS do Azure | Cópia de segurança consistente com a aplicação a utilizar o [framework do script prévio e o script posterior](backup-azure-linux-app-consistent.md)<br/><br/> [Recuperação ao nível do ficheiro](backup-azure-restore-files-from-vm.md)<br/><br/> [Criar uma VM a partir de um disco restaurado](backup-azure-arm-restore-vms.md#create-new-restore-disks)<br/><br/> [Criar uma VM a partir de um ponto de recuperação](backup-azure-arm-restore-vms.md#create-new-create-a-vm).

## <a name="using-premium-storage-vms-with-azure-backup"></a>Utilizar o premium storage VMs com o Azure Backup
O Azure Backup protege VMs do premium storage. Armazenamento premium do Azure é a unidade de estado sólido (SSD)-concebida para suportar cargas de trabalho de e/S intensivas. O Armazenamento Premium é apelativo para cargas de trabalho de máquina virtual (VM). Para obter mais informações sobre o armazenamento Premium e outros tipos de disco, consulte o artigo [selecionar um tipo de disco](../virtual-machines/windows/disks-types.md).

### <a name="back-up-premium-storage-vms"></a>Fazer cópias de segurança de VMs do premium storage
Enquanto o backup das VMs do Premium Storage, o serviço de cópia de segurança cria uma localização de transição temporária, com o nome "AzureBackup-", na conta de armazenamento premium. O tamanho da localização de transição é igual ao tamanho do instantâneo do ponto de recuperação. Certifique-se de que a conta de armazenamento premium tem espaço livre para acomodar a localização de transição temporária. Para obter mais informações, consulte o artigo sobre [metas de escalabilidade do armazenamento do Azure](../storage/common/storage-scalability-targets.md). Depois de a tarefa de cópia de segurança estar concluída, a localização de transição é eliminada. O preço do armazenamento utilizado para a localização de transição é consistente com todos os [Preços do Premium Storage](../virtual-machines/windows/disks-types.md).

> [!NOTE]
> Não modifique ou edite a localização de transição.
>
>

### <a name="restore-premium-storage-vms"></a>Restaurar VMs do premium storage
Pode restaurar VMs de Armazenamento Premium para o Armazenamento Premium ou para o Armazenamento Standard. Restaurar o ponto de recuperação de uma VM de Armazenamento Premium novamente para o Armazenamento Premium é o processo normal. No entanto, pode ser rentável restaurar o ponto de recuperação de uma VM de Armazenamento Premium para o Armazenamento Standard se necessitar de um subconjunto de ficheiros da VM.

## <a name="using-managed-disk-vms-with-azure-backup"></a>Utilizar VMs de disco gerido com o Azure Backup
O Azure Backup protege VMs de disco gerido. Os discos geridos libertam-no da gestão de contas de armazenamento de máquinas virtuais e simplificam bastante o aprovisionamento das VMs.

### <a name="back-up-managed-disk-vms"></a>Cópia de segurança de VMs de disco gerido
A criação de cópias de segurança de VMs em discos geridos não difere da criação de cópias de segurança de VMs do Resource Manager. No portal do Azure, pode configurar a tarefa de cópia de segurança diretamente na vista Máquina Virtual ou na vista do cofre dos Serviços de Recuperação. Pode criar cópias de segurança de VMs em discos geridos através de coleções de RestorePoint criadas sobre os discos geridos. A cópia de segurança do Azure também suporta a criação de cópia de segurança de VMs de discos geridos encriptados com o Azure Disk Encryption (ADE).

### <a name="restore-managed-disk-vms"></a>Restaurar VMs de disco gerido
O Azure Backup permite-lhe restaurar uma VM completa com discos geridos ou restaurar discos geridos para uma conta de armazenamento. O Azure faz a gestão dos discos geridos durante o processo de restauro. Quanto a si (o cliente), faz a gestão da conta de armazenamento criada como parte do processo de restauro. Ao restaurar VMs geridas encriptadas, as chaves e segredos da VM devem existir no cofre de chaves antes de começar a operação de restauro.

## <a name="what-are-the-features-of-each-backup-component"></a>Quais são as funcionalidades de cada componente do Backup?
As secções seguintes fornecem tabelas que resumem a disponibilidade ou o suporte para várias funcionalidades em cada componente do Azure Backup. Consulte as informações de cada tabela para suporte adicional ou detalhes.

### <a name="storage"></a>Armazenamento
| Funcionalidade | Agente do Backup do Azure | System Center DPM | Servidor do Backup do Azure | Cópia de segurança da VM do IaaS do Azure |
| --- | --- | --- | --- | --- |
| Cofre dos Serviços de Recuperação |![Sim][green] |![Sim][green] |![Sim][green] |![Sim][green] |
| Armazenamento em disco | |![Sim][green] |![Sim][green] | |
| Armazenamento em banda | |![Sim][green] | | |
| Compressão <br/>(no cofre dos Serviços de Recuperação) |![Sim][green] |![Sim][green] |![Sim][green] | |
| Cópia de segurança incremental |![Sim][green] |![Sim][green] |![Sim][green] |![Sim][green] |
| Eliminação de discos duplicados | |![Parcialmente][yellow] |![Parcialmente][yellow] | | |

![chave da tabela](./media/backup-introduction-to-azure-backup/table-key.png)

O cofre dos Serviços de Recuperação é o destino de armazenamento preferencial em todos os componentes. O System Center DPM e o Azure Backup Server oferecem também a opção de criar uma cópia de disco local. No entanto, apenas o System Center DPM disponibiliza a opção para escrever dados num dispositivo de armazenamento em banda.

#### <a name="compression"></a>Compressão
As cópias de segurança estão comprimidas para reduzir o espaço de armazenamento necessário. O único componente que não utiliza a compressão é a extensão da VM. A extensão de VM copia todos os dados de cópias de segurança da conta de armazenamento para o cofre dos Serviços de Recuperação na mesma região. Não é utilizada compressão durante a transferência dos dados. A transferência dos dados sem compressão aumenta ligeiramente o armazenamento utilizado. No entanto, armazenar os dados sem compressão permite restauros mais rápidos, caso precise desse ponto de recuperação.


#### <a name="disk-deduplication"></a>Eliminação de Discos Duplicados
Pode tirar partido da eliminação de duplicados ao implementar o System Center DPM ou o Azure Backup Server [numa máquina virtual Hyper-V](https://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx). O Windows Server efetua a eliminação de duplicados de dados (ao nível do anfitrião) em discos rígidos virtuais (VHDs) que estão ligados à máquina virtual como o armazenamento de cópia de segurança.

> [!NOTE]
> A eliminação de duplicados não está disponível no Azure para nenhum componente do Backup. Quando o System Center DPM e o Servidor do Backup são implementados no Azure, não é possível eliminar os discos de armazenamento ligados à VM duplicados.
>
>

### <a name="incremental-backup-explained"></a>A cópia de segurança incremental explicada
Todos os componentes do Azure Backup suportam a cópia de segurança incremental, independentemente do armazenamento de destino (disco, banda, cofre dos Serviços de Recuperação). A cópia de segurança incremental assegura que as cópias de segurança são eficientes ao nível do armazenamento e do tempo, transferindo apenas as alterações efetuadas desde a última cópia de segurança.

#### <a name="comparing-full-differential-and-incremental-backup"></a>Comparação entre cópias de segurança Completas, Diferenciais e Incrementais

O consumo de armazenamento, o objetivo de tempo de recuperação (RTO) e o consumo de rede variam em cada tipo de método de cópia de segurança. Para manter o custo total de propriedade (TCO) das cópias de segurança reduzido, tem de saber como escolher a melhor solução de cópias de segurança. A imagem seguinte compara as Cópias de Segurança Completa, Diferencial e Incremental. Na imagem, a origem de dados A é composta por dez blocos de armazenamento, A1 a A10, dos quais são criadas cópias de segurança mensalmente. Os blocos A2, A3, A4 e A9 são alterados no primeiro mês e o bloco A5 no mês seguinte.

![imagem que mostra a comparação dos métodos de cópia de segurança](./media/backup-introduction-to-azure-backup/backup-method-comparison.png)

Com a **Cópia de Segurança Completa**, cada cópia de segurança contém a origem de dados completa. A cópia de segurança completa consome uma grande quantidade de largura de banda de rede e de armazenamento sempre que é transferida uma cópia de segurança.

A **Cópia de Segurança Diferencial** armazena apenas os blocos que sofreram alterações desde a cópia de segurança completa inicial, o que resulta num consumo de rede e de armazenamento mais reduzido. As cópias de segurança diferenciais não retêm cópias redundantes de dados não alterados. No entanto, uma vez que os blocos de dados que se mantêm inalterados entre as cópias de segurança subsequentes são transferidos e armazenados, as cópias de segurança diferenciais são ineficazes. No segundo mês, é criada uma cópia de segurança dos blocos alterados A2, A3, A4 e A9. No terceiro mês, é criada uma nova cópia de segurança destes mesmos blocos, juntamente com o bloco alterado A5. Continua a ser feita uma cópia de segurança dos blocos alterados até ser feita a próxima cópia de segurança completa.

A **Cópia de Segurança Incremental** proporciona uma elevada eficiência em termos de armazenamento e de rede ao armazenar apenas os blocos de dados que sofreram alterações desde a cópia de segurança anterior. Com a cópia de segurança incremental, não é necessário fazer cópias de segurança regulares. No exemplo, depois de feita a cópia de segurança completa no primeiro mês, os blocos A2, A3, A4 e A9 são marcados como alterados e transferidos para o segundo mês. No terceiro mês, só o bloco A5 é marcado e transferido. Mover menos dados poupa recursos de armazenamento e de rede, o que reduz o TCO.

### <a name="security"></a>Segurança
| Funcionalidade | Agente do Backup do Azure | System Center DPM | Servidor do Backup do Azure | Cópia de segurança da VM do IaaS do Azure |
| --- | --- | --- | --- | --- |
| Segurança da rede<br/> (para o Azure) |![Sim][green] |![Sim][green] |![Sim][green] |![Sim][green] |
| Segurança de dados<br/> (no Azure) |![Sim][green] |![Sim][green] |![Sim][green] |![Sim][green] |

![chave da tabela](./media/backup-introduction-to-azure-backup/table-key.png)

#### <a name="network-security"></a>Segurança da rede
Todo o tráfego de cópia de segurança dos seus servidores para o cofre dos Serviços de Recuperação é encriptado com a norma AES (Advanced Encryption Standard) 256. Os dados de cópia de segurança são enviados através de uma ligação HTTPS segura. Os dados de cópia de segurança também são armazenados no cofre dos Serviços de Recuperação no formato encriptado. Apenas você, o cliente do Azure, tem a frase de acesso para desbloquear estes dados. A Microsoft não pode desencriptar os dados de cópia de segurança em momento algum.

> [!WARNING]
> Depois de estabelecer o cofre dos Serviços de Recuperação, é o único a ter acesso à chave de encriptação. A Microsoft nunca mantém uma cópia da sua chave de encriptação e não tem acesso à chave. Se a chave está no local incorreto, a Microsoft não pode recuperar os dados de cópia de segurança.
>
>

#### <a name="data-security"></a>Segurança de dados
A cópia de segurança das VMs do Azure necessita da configuração da encriptação *na* máquina virtual. O Azure Backup suporta o Azure Disk Encryption, que utiliza o BitLocker nas máquinas virtuais do Windows e **dm-crypt** nas máquinas virtuais do Linux. No back-end, o Azure Backup utiliza a [encriptação do Serviço de Armazenamento do Azure](../storage/common/storage-service-encryption.md), que protege os dados inativos.

### <a name="network"></a>Rede
| Funcionalidade | Agente do Backup do Azure | System Center DPM | Servidor do Backup do Azure | Cópia de segurança da VM do IaaS do Azure |
| --- | --- | --- | --- | --- |
| Compressão de rede <br/>(para o **servidor de cópia de segurança**) | |![Sim][green] |![Sim][green] | |
| Compressão de rede <br/>(no **cofre dos Serviços de Recuperação**) |![Sim][green] |![Sim][green] |![Sim][green] | |
| Protocolo de rede <br/>(para o **servidor de cópia de segurança**) | |TCP |TCP | |
| Protocolo de rede <br/>(no **cofre dos Serviços de Recuperação**) |HTTPS |HTTPS |HTTPS |HTTPS |

![chave da tabela](./media/backup-introduction-to-azure-backup/table-key-2.png)

A extensão da VM (na VM IaaS) lê os dados diretamente a partir da conta de armazenamento do Azure através da rede de armazenamento, pelo que não é necessário comprimir este tráfego.

Se estiver a utilizar um servidor System Center DPM ou Azure Backup Server como um servidor de cópia de segurança secundário, comprima os dados que passam do servidor primário para o servidor de cópia de segurança. Comprimir os dados antes da criação da cópia de segurança no DPM ou no Azure Backup Server poupa largura de banda.

#### <a name="network-throttling"></a>Limitação de rede
O agente do Azure Backup fornece limitação de rede, o que lhe permite controlar como a largura de banda de rede é utilizada durante a transferência de dados. A limitação pode ser útil se precisar de efetuar uma cópia de segurança dos dados durante as horas de trabalho, mas não pretende que o processo de cópia de segurança interfira com outro tráfego de Internet. A limitação para a transferência de dados aplica-se às atividades de cópia de segurança e de restauro.

## <a name="backup-and-retention"></a>Cópia de segurança e retenção

O Azure Backup tem um limite de 9999 pontos de recuperação, também conhecidos como cópias de segurança ou instantâneos de cópia de segurança, por *nstância protegida*. Uma instância protegida é um computador, servidor (físico ou virtual) ou carga de trabalho configurados para criar cópias de segurança para o Azure. Para obter mais informações, veja a secção [O que são as instâncias protegidas](backup-introduction-to-azure-backup.md#what-is-a-protected-instance). Uma instância está protegida depois de uma cópia de segurança de dados ter sido guardada. A cópia de segurança de dados é a proteção. Se a origem de dados fosse perdida ou tivesse sido danificada, a cópia de segurança poderia restaurar os dados de origem. A tabela seguinte mostra a frequência de cópia de segurança máxima de cada componente. A sua configuração de política de cópias de segurança determina quão rapidamente pode consumir os pontos de recuperação. Por exemplo, se criar um ponto de recuperação por dia, pode manter os pontos de recuperação durante 27 anos antes de os esgotar. Se criar um ponto de recuperação por mês, pode manter os pontos de recuperação durante 833 anos antes de os esgotar. O serviço do Backup não define um limite de tempo de expiração para um ponto de recuperação.

|  | Agente do Backup do Azure | System Center DPM | Servidor do Backup do Azure | Cópia de segurança da VM do IaaS do Azure |
| --- | --- | --- | --- | --- |
| Frequência de cópia de segurança<br/> (para o cofre dos Serviços de Recuperação) |Três cópias de segurança por dia |Duas cópias de segurança por dia |Duas cópias de segurança por dia |Uma cópia de segurança por dia |
| Frequência de cópia de segurança<br/> (para o disco) |Não aplicável |<li>A cada 15 minutos para o SQL Server <li>A cada hora para outras cargas de trabalho |<li>A cada 15 minutos para o SQL Server <li>A cada hora para outras cargas de trabalho</p> |Não aplicável |
| Opções de retenção |Diariamente, semanalmente, mensalmente, anualmente |Diariamente, semanalmente, mensalmente, anualmente |Diariamente, semanalmente, mensalmente, anualmente |Diariamente, semanalmente, mensalmente, anualmente |
| Número máximo de pontos de recuperação por instância protegida |9999|9999|9999|9999|
| Período de retenção máximo |Depende da frequência da cópia de segurança |Depende da frequência da cópia de segurança |Depende da frequência da cópia de segurança |Depende da frequência da cópia de segurança |
| Pontos de recuperação no disco local |Não aplicável |<li>64 para Servidores de Ficheiros<li>448 para Servidores de Aplicações |<li>64 para Servidores de Ficheiros<li>448 para Servidores de Aplicações |Não aplicável |
| Pontos de recuperação em banda |Não aplicável |Ilimitado |Não aplicável |Não aplicável |

## <a name="what-is-a-protected-instance"></a>O que é uma instância protegida
Uma instância protegida é uma referência genérica para um computador com o Windows, um servidor (físico ou virtual) ou uma base de dados SQL que tenham sido configurados para cópias de segurança para o Azure. Uma instância está protegida depois de configurar uma política de cópias de segurança para o computador, servidor ou base de dados e depois de criar uma cópia de segurança dos dados. As cópias subsequentes dos dados de cópia de segurança para essa instância protegida (chamados pontos de recuperação), aumentam a quantidade de armazenamento consumido. Pode criar até 9999 pontos de recuperação para uma instância protegida. Se eliminar um ponto de recuperação a partir do armazenamento, não conta em relação ao total de pontos de recuperação de 9999.
Alguns exemplos comuns de instâncias protegidas são máquinas virtuais, servidores de aplicações, bases de dados e computadores pessoais com o sistema operativo Windows. Por exemplo:

* Uma máquina virtual a executar recursos de infraestrutura hipervisores do Hyper-V ou do IaaS do Azure. Os sistemas operativos convidados para a máquina virtual podem ser Windows Server ou Linux.
* Um servidor de aplicações: O servidor de aplicações pode ser uma máquina física ou virtual a executar o Windows Server e cargas de trabalho com dados que precisam ser efetuada a cópia de segurança. Cargas de trabalho comuns são o Microsoft SQL Server, o Microsoft Exchange Server, o Microsoft SharePoint Server e a função do Servidor de Ficheiros no Windows Server. Para efetuar a cópia de segurança destas cargas de trabalho necessita do System Center Data Protection Manager (DPM) ou do Azure Backup Server.
* Um computador pessoal, portátil ou uma estação de trabalho com o sistema operativo Windows.


## <a name="what-is-a-recovery-services-vault"></a>O que é um cofre dos Serviços de Recuperação?
Um cofre dos Serviços de Recuperação é uma entidade de armazenamento online no Azure utilizada para armazenar dados, como cópias de segurança, pontos de recuperação e políticas de cópia de segurança. Pode utilizar cofres dos Serviços de Recuperação para armazenar dados de cópia de segurança para serviços do Azure, bem como servidores e estações de trabalho no local. Os cofres dos Serviços de Recuperação facilitam a organização dos dados de cópia de segurança ao minimizar os custos de gestão. Em cada subscrição do Azure, pode criar até 500 cofres dos Serviços de Recuperação por região do Azure. Quando ponderar o local onde pretende armazenar os seus dados, é de salientar que existem diferenças entre as regiões. Veja [Armazenamento georredundante](../storage/common/storage-redundancy-grs.md) para obter informações sobre emparelhamento de regiões e considerações de armazenamento adicionais.

Os cofres de cópias de segurança, baseados no Azure Service Manager, foram a primeira versão do cofre. Os cofres dos Serviços de Recuperação, que adicionam as funcionalidades do modelo do Azure Resource Manager, são a segunda versão do cofre. Veja o artigo [Descrição geral do cofre dos Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md) para obter uma descrição completa das diferenças das funcionalidades. Já não pode criar cofres de cópias de segurança e todos os cofres de cópias de segurança existentes foram atualizados para cofres dos Serviços de Recuperação. Pode utilizar o portal do Azure para gerir os cofres que foram atualizados para cofres dos Serviços de Recuperação.

## <a name="how-does-azure-backup-differ-from-azure-site-recovery"></a>Qual é a diferença entre o Backup do Azure e o Azure Site Recovery?
O Azure Backup e o Azure Site Recovery estão relacionados na medida em que ambos os serviços criam cópias de segurança dos dados e podem restaurá-los. No entanto, estes serviços servem objetivos diferentes ao oferecer continuidade empresarial e recuperação após desastre na sua empresa. Utilize o Azure Backup para proteger e restaurar dados a um nível mais granular. Por exemplo, se uma apresentação num computador portátil se danificasse, utilizaria o Azure Backup para restaurar a apresentação. Se quisesse replicar a configuração e os dados de uma VM através de outro datacenter, utilizaria o Azure Site Recovery.

O Azure Backup protege os dados no local e na nuvem. O Azure Site Recovery coordena a replicação da máquina virtual e do servidor físico, a ativação pós-falha e a reativação pós-falha. Ambos os serviços são importantes porque a sua solução de recuperação após desastre tem de manter os seus dados seguros e recuperáveis (Cópia de Segurança) *e* manter as cargas de trabalho disponíveis (Recuperação de Sites) quando ocorrerem falhas.

Os seguintes conceitos podem ajudá-lo a tomar decisões importantes acerca da cópia de segurança e da recuperação após desastre.

| Conceito | Detalhes | Cópia de segurança | Recuperação após desastre (DR) |
| --- | --- | --- | --- |
| Objetivo de ponto de recuperação (RPO) |A quantidade de perda de dados aceitável se uma recuperação tiver de ser efetuada. |As soluções de cópia de segurança têm uma grande variação no respetivo RPO aceitável. Normalmente, as cópias de segurança de máquinas virtuais têm um RPO de um dia, enquanto as cópias de segurança de bases de dados têm RPOs tão baixos como 15 minutos. |As soluções de recuperação após desastre têm RPOs baixas. A cópia da DR pode atrasar-se em alguns segundos ou minutos. |
| Objetivo de tempo de recuperação (RTO) |A quantidade de tempo que demora a concluir uma recuperação ou um restauro. |Devido ao RPO maior, a quantidade de dados que uma solução de cópia de segurança necessita para processar é, normalmente, muito mais elevada, o que resulta em RTOs maiores. Por exemplo, pode demorar dias para restaurar dados de bandas, consoante o tempo que demora a transportar a banda a partir de uma localização fora das instalações. |As soluções de recuperação após desastre têm RTOs menores porque são estão mais sincronizadas com a origem. Não é necessário processar tantas as alterações. |
| Retenção |O tempo durante o qual os dados precisam de ser armazenados |Para cenários que necessitem de uma recuperação operacional (danos em dados, eliminação de ficheiros inadvertida, falha de SO), os dados de cópia de segurança são, normalmente, mantidos durante 30 dias ou menos.<br>A partir de um ponto de vista da conformidade, os dados poderão ter de ser armazenados durante meses ou mesmo anos. Dados de cópia de segurança são, idealmente, adequados para o arquivo nestes casos. |A recuperação após desastre apenas precisa de dados da recuperação operacional, que demoram, normalmente, algumas horas ou um dia, no máximo. Devido à captura de dados detalhados utilizada em soluções de DR, não se recomenda a utilização de dados de DR para uma retenção de longo prazo. |

## <a name="next-steps"></a>Passos Seguintes
Utilize um dos seguintes tutoriais para obter instruções detalhadas, passo-a-passo, para proteger os dados no Windows Server ou proteger uma máquina virtual (VM) no Azure:

* [Fazer uma Cópia de Segurança de Ficheiros e Pastas](backup-try-azure-backup-in-10-mins.md)
* [Fazer uma Cópia de Segurança de Máquinas Virtuais do Azure](backup-azure-vms-first-look-arm.md)

Para obter detalhes sobre como proteger outras cargas de trabalho, veja um dos seguintes artigos:

* [Fazer uma cópia de segurança do seu Windows Server](backup-configure-vault.md)
* [Fazer uma cópia de segurança de cargas de trabalho de aplicações](backup-azure-microsoft-azure-backup.md)
* [Cópia de segurança das VMs IaaS do Azure](backup-azure-arm-vms-prepare.md)

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png
