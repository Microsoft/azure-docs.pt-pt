---
title: Oracle Database em estratégias de backup de máquinas virtuais Azure
description: Opções para apoiar as Bases de Dados da Oracle num ambiente VM Azure Linux.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 8a1eb1c21663e0294cd384daa0ba644adf78007a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673211"
---
# <a name="oracle-database-in-azure-linux-vm-backup-strategies"></a>Oracle Database em estratégias de backup VM Azure Linux

As cópias de segurança da base de dados protegem a base de dados contra a perda de dados devido à falha do componente de armazenamento e à falha do centro de dados. Podem também ser um meio de recuperação do erro humano e uma forma de clonar uma base de dados para fins de desenvolvimento ou teste. 

Em Azure, uma vez que todo o armazenamento é altamente redundante e a perda de um ou mais discos não conduzirá a uma interrupção da base de dados, as cópias de segurança são mais frequentemente utilizadas para proteger contra erros humanos, para operações de clonagem ou preservação de dados para fins regulamentares. São também um meio de proteção contra falhas regionais onde uma tecnologia de recuperação de desastres como o DataGuard não está a ser utilizada. Neste caso, os backups devem ser armazenados em diferentes regiões do Azure utilizando replicações geo-redundantes, de modo a estarem disponíveis fora da região de base de dados primária.

## <a name="azure-storage"></a>Storage do Azure 

Os [serviços de Armazenamento Azure](../../../storage/common/storage-introduction.md) são a solução de armazenamento em nuvem da Microsoft para cenários modernos de armazenamento de dados. O Azure Storage oferece uma série de serviços que podem ser usados para montar armazenamento externo ao Azure Linux VM, adequado como suporte de reserva para bases de dados Oracle. Uma ferramenta de backup, como a Oracle RMAN, é necessária para iniciar uma operação de backup ou restaurar e copiar a cópia de segurança para/a partir do serviço de Armazenamento Azure.
 
Os serviços de Armazenamento Azure oferecem os seguintes benefícios:

-  Durável e de elevada disponibilidade. A redundância garante que os dados estão seguros em caso de falhas de hardware transitórias. Todo o armazenamento é por defeito triplo espelhado. Também pode optar por replicar dados em centros de dados ou regiões geográficas para proteção adicional contra catástrofes locais ou desastres naturais. Os dados replicados desta forma permanecem altamente disponíveis no caso de uma falha inesperada.

-  Está seguro. Todos os dados escritos numa conta de armazenamento Azure são encriptados pelo serviço. O Armazenamento do Azure oferece-lhe controlo detalhado sobre quem tem acesso aos seus dados.

-  Escalável. O Armazenamento do Azure foi criado para ser extremamente dimensionável para satisfazer as necessidades de armazenamento e desempenho de dados das aplicações atuais.

-  Gerido. A Azure lida com manutenção de hardware, atualizações e problemas críticos para si.

-  Acessível. Os dados no Armazenamento do Azure são acessíveis a partir de qualquer local no mundo através de HTTP ou HTTPS. A Microsoft fornece bibliotecas de clientes para armazenamento Azure em uma variedade de idiomas, incluindo .NET, Java, Node.js, Python, PHP, Ruby, Go, e outros, bem como uma API de REST madura. O Armazenamento do Azure suporta scripting no Azure PowerShell ou na CLI do Azure. E o portal do Azure e o Explorador de Armazenamento do Azure oferecem soluções visuais simples para trabalhar com os seus dados.

A plataforma Azure Storage inclui os seguintes serviços de dados que são adequados para usar como meios de segurança para a base de dados Oracle:

-  Blobs do Azure: um arquivo de objetos extremamente dimensionável para texto e dados binários. Também inclui suporte para análise de big data através da Data Lake Storage Gen2.

-  Ficheiros Azure: Partilhas de ficheiros geridas para implementações em nuvem ou no local.

-  Discos Azure: Volumes de armazenamento ao nível do bloco para VMs Azure.

### <a name="cross-regional-storage-mounting"></a>Montagem de armazenamento trans-regional

A capacidade de aceder ao armazenamento de backup em todas as regiões é um aspeto importante do BCDR e útil para a clonagem de bases de dados de backups em diferentes regiões geográficas. Armazenamento em nuvem Azure fornece cinco níveis de [redundância](../../../storage/common/storage-redundancy.md)
- Armazenamento LocalMente Redundante [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) onde os seus dados são replicados três vezes dentro de um único local físico na região primária.  
- Armazenamento Redundante zona [(ZRS)](../../../storage/common/storage-redundancy.md#zone-redundant-storage) onde os seus dados são protegidos pelo LRS na região primária, e replicado sincronizadamente em três zonas de disponibilidade (AZ) na região primária. Cada AZ também está protegido pelo LRS. 
- Geo-Redundant Armazenamento [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) onde os seus dados são protegidos pelo LRS na região primária, e replicados assíncronamente para uma região secundária onde também é protegido pelo LRS.
- O armazenamento de zonas-redundantes [geo-zona (GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) copia os seus dados sincronizadamente em três zonas de disponibilidade de Azure na região primária utilizando zRS. Em seguida, copia os dados de forma assíncrona para uma única localização física na região secundária. Em todos os locais, os dados são protegidos pelo LRS também. 
- Ler Access Geo-Redundant Storage [(RA-GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) e Ler Acesso Geo-Zona-Redundante Armazenamento [(RA-GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) permite o acesso apenas à leitura de dados replicados na região secundária em todos os momentos. 

#### <a name="blob-storage-and-file-storage"></a>Armazenamento de blob e armazenamento de ficheiros

Quando utilizar ficheiros Azure com protocolos SMB ou NFS 4.1 (Preview) para montar como armazenamento de backup, note que a Azure Files não suporta armazenamento geo-redundante de acesso de leitura (RA-GRS) e armazenamento de zona de acesso de leitura -zona redundante (RA-GZRS). 

Além disso, se o requisito de armazenamento de cópia de segurança for superior a 5 Ficheiros TiB Azure requer a [funcionalidade De Grandes Partilhas de Ficheiros](../../../storage/files/storage-files-planning.md) ativada que não suporta redundância GRS ou GZRS - apenas o LRS é suportado. 

A Azure Blob montado usando o protocolo NFS 3.0 (Preview) atualmente apenas suporta redundância LRS e ZRS.  

AZure Blob configurado com qualquer opção de redundância pode ser montado usando Blobfuse.

#### <a name="recovery-services-vault"></a>Cofre dos Serviços de Recuperação 

Um cofre dos Serviços de Recuperação é uma entidade de gestão que armazena pontos de recuperação criados ao longo do tempo e disponibiliza uma interface para realizar operações relacionadas com cópia de segurança. Estas incluem criar cópias de segurança a pedido, realizar restauros e criar políticas de cópia de segurança.
O Azure Backup manuseia automaticamente o armazenamento para o cofre. Precisa de especificar como esse armazenamento é replicado no momento da criação. Não pode ser mudado depois de os objetos estarem protegidos no cofre. Para redundância regional, escolha o cenário geo-redundante.

Se pretende restabelecer a uma região secundária, [a região emparelhada Azure](../../../best-practices-availability-paired-regions.md) permite a função [cross region Restore.](../../../backup/backup-create-rs-vault.md) Quando a restauração da região transversal está ativada, o armazenamento de backup é transferido de GRS para armazenamento geo-redundante de acesso de leitura (RA-GRS). 

### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure

O armazenamento Azure Blob é um serviço de armazenamento altamente rentável, durável e seguro baseado na nuvem utilizado para armazenar grandes quantidades de dados não estruturados e é um meio de comunicação ideal para usar para backups da Oracle Database. O armazenamento Azure Blob pode ser montado no Azure Linux VM's utilizando o protocolo NFS v3.0 ou Blobfuse (Linux FUSE).

#### <a name="azure-blob-blobfuse"></a>Azure Blob Blobfuse

[Blobfuse](../../../storage/blobs/storage-how-to-mount-container-linux.md) é um projeto de código aberto desenvolvido para fornecer um sistema de ficheiros virtual apoiado pelo armazenamento Azure Blob. Utiliza a biblioteca de código aberto libfuse para comunicar com o módulo de kernel Linux FUSE e implementa as operações do sistema de ficheiros utilizando as APIs de Blob REST de Armazenamento Azure. Blobfuse está atualmente disponível para distribuição Ubuntu e Centos/RedHat. Também está disponível para Kubernetes utilizando o [controlador CSI.](https://github.com/kubernetes-sigs/blob-csi-driver) 

Embora omnipresente em todas as regiões do Azure, e trabalhe com todos os tipos de conta de armazenamento, incluindo o V1/v2 e a Azure Data Lake Store Gen2, o desempenho oferecido pela Blobfuse tem demonstrado ser menos do que protocolos alternativos, como SMB ou NFS. Para adequar-se como suporte de cópia de segurança da base de dados, recomendamos a utilização de protocolos SMB ou [NFS](../../../storage/blobs/storage-how-to-mount-container-linux.md) para montar o armazenamento do Azure Blob. 

#### <a name="azure-blob-nfs-v30-preview"></a>Azure Blob NFS v3.0 (Pré-visualização)

O suporte azure para o sistema de ficheiros de rede (NFS) v3.0 está agora em pré-visualização. O suporte [NFS](../../../storage/blobs/network-file-system-protocol-support.md) permite que os clientes Windows e Linux montem um recipiente de armazenamento Blob num Azure VM. 

A pré-visualização pública NFS 3.0 suporta contas de armazenamento GPV2 com desempenho de nível padrão nas seguintes regiões: 
- Leste da Austrália
- Coreia do Sul Central
- Centro-Sul dos EUA. 

Para garantir a segurança da rede, a conta de armazenamento utilizada para a montagem de NFS deve ser contida num VNet. A segurança do Azure Ative Directory (AD) e as listas de controlo de acesso (ACLs) ainda não são suportadas em contas que tenham o suporte do protocolo NFS 3.0 ativado neles.

### <a name="azure-files"></a>Ficheiros do Azure

[O Azure Files](../../../storage/files/storage-files-introduction.md) é um sistema de ficheiros distribuídos totalmente gerido, baseado na nuvem, que pode ser montado em clientes windows, Linux ou macOS baseados na nuvem.

O Azure Files oferece ações de ficheiros cross-platform totalmente geridas na nuvem que são acessíveis através do protocolo do Bloco de Mensagens do Servidor (SMB) e do protocolo do Sistema de Ficheiros de Rede (NFS) (pré-visualização). A Azure Files não suporta atualmente o acesso a vários protocolos, pelo que uma partilha só pode ser uma partilha NFS ou uma participação SMB. Recomendamos determinar qual o protocolo que melhor se adequa às suas necessidades antes de criar ações de ficheiros Azure.

As ações do Azure File também podem ser protegidas através do cofre de serviços de backup da Azure, fornecendo uma camada adicional de proteção para as cópias de segurança Oracle RMAN.

#### <a name="azure-files-nfs-v41-preview"></a>Ficheiros Azure NFS v4.1 (Pré-visualização)

As ações de ficheiros Azure podem ser montadas nas distribuições do Linux utilizando o protocolo V4.1 do Sistema de Ficheiros de Rede (NFS). Enquanto na Pré-visualização existem uma série de limitações às funcionalidades suportadas, que são documentadas [aqui](../../../storage/files/storage-files-how-to-mount-nfs-shares.md). 

Enquanto na pré-visualização os ficheiros Azure NFS v4.1 também se restringem às [seguintes regiões:](../../../storage/files/storage-files-how-to-mount-nfs-shares.md)
- Leste dos EUA (LRS e ZRS)
- E.U.A. Leste 2
- E.U.A. Oeste 2
- Europa Ocidental
- Sudeste Asiático
- Sul do Reino Unido
- Leste da Austrália

#### <a name="azure-files-smb-30"></a>Ficheiros Azure SMB 3.0

As ações de ficheiros Azure podem ser montadas nas distribuições linux utilizando o cliente kernel SMB. O Protocolo Comum do Sistema de Ficheiros de Internet (CIFS), disponível nas distribuições do Linux, é um dialeto de SMB. Ao montar O SMB de Ficheiros Azure em VMs Linux, é montado como sistema de ficheiros tipo CIFS e o pacote CIFS deve ser instalado. 

A Azure Files SMB está geralmente disponível em todas as regiões do Azure, e apresenta as mesmas características de desempenho que os protocolos NFS v3.0 e v4.1, sendo atualmente o método recomendado para fornecer meios de armazenamento de backup a Azure Linux VMs.  

Existem duas versões suportadas de SMB disponíveis, SMB 2.1 e SMB 3.0, sendo esta última recomendada uma vez que suporta encriptação em trânsito. No entanto, diferentes versões de kernels Linux têm suporte diferente para SMB 2.1 e 3.0 e deve consultar a tabela [aqui](../../../storage/files/storage-how-to-use-files-linux.md) para garantir que a sua aplicação suporta SMB 3.0. 

Uma vez que o Azure Files foi concebido para ser um serviço de partilha de ficheiros multiutilizador, existem certas características que deve sintonizar para torná-lo mais adequado como suporte de armazenamento de backup. É aconselhável desligar e definir os IDs do utilizador e do grupo para ficheiros criados.

## <a name="azure-netapp-files"></a>Azure NetApp Files

O serviço [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-solution-architectures.md) é uma solução de armazenamento completa para a Oracle Databases em VMs Azure. Construído sobre um armazenamento de ficheiros de classe empresarial, de alto desempenho, medido, suporta qualquer tipo de carga de trabalho e está altamente disponível por padrão. Juntamente com o controlador Oracle Direct NFS (dNFS), o Azure NetApp Files fornece uma camada de armazenamento altamente otimizada para a Base de Dados oracle.

O Azure NetApp Files fornece cópias de instantâneos eficientes baseadas em armazenamento no sistema de armazenamento subjacente que utiliza um mecanismo de redirecionamento na escrita (RoW). Embora as cópias instantâneas sejam extremamente rápidas para tomar e restaurar, elas servem apenas como uma primeira linha de defesa, o que pode explicar a grande maioria das operações de restauro necessárias de qualquer determinada organização, que muitas vezes é a recuperação de um erro humano. No entanto, as cópias Snapshot não são uma cópia de segurança completa. Para cobrir todos os requisitos de backup e restauro, as réplicas de instantâneos externos e/ou outras cópias de backup devem ser criadas numa geografia remota para proteger contra interrupções regionais. Para saber mais sobre a utilização de Ficheiros NetApp para Bases de Dados oracle no Azure, leia este [relatório](https://www.netapp.com/pdf.html?item=/media/17105-tr4780pdf.pdf).

## <a name="azure-backup-service"></a>Serviço de backup Azure

[O Azure Backup](../../../backup/backup-overview.md) é um PaaS totalmente gerido que fornece soluções simples, seguras e económicas para fazer o backup dos seus dados e recuperá-lo da nuvem Microsoft Azure. O Azure Backup pode fazer backup e restaurar as bases de dados da Azure VM's, Azure Files, bem como as bases de dados SQL Server, Oracle, MySQL, PostreSQL e SAP HANA em VMs Azure. 

A Azure Backup fornece backups independentes e isolados para proteger contra a destruição acidental de dados originais. As cópias de segurança são armazenadas num [cofre dos Serviços](../../../backup/backup-azure-recovery-services-vault-overview.md) de Recuperação com gestão incorporada de pontos de recuperação. A configuração e a escalabilidade são simples, as cópias de segurança são otimizadas e pode facilmente restaurar conforme necessário. Utiliza a potência subjacente e a escala ilimitada da nuvem Azure para fornecer alta disponibilidade sem manutenção ou monitorização por cima. O Azure Backup não limita a quantidade de dados de entrada ou saída que transfere, nem cobra pelos dados que são transferidos, e os dados são protegidos em trânsito e em repouso. 

Para garantir a durabilidade, o Azure Backup oferece vários tipos de replicação para manter os seus dados de backup altamente disponíveis.

- O armazenamento localmente redundante [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) replica os seus dados três vezes (cria três cópias dos seus dados) numa unidade de escala de armazenamento num centro de dados.
- O armazenamento geo-redundante [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) é a opção de replicação padrão e recomendada. O GRS replica os dados para uma região secundária (a centenas de quilómetros da localização primária da origem de dados).

Um cofre criado com redundância GRS inclui a opção de configurar a funcionalidade [Cross Region Restore,](../../../backup/backup-create-rs-vault.md#set-storage-redundancy) que permite restaurar dados numa região secundária, azure emparelhada.

O serviço Azure Backup fornece uma [estrutura](../../../backup/backup-azure-linux-app-consistent.md) para alcançar a consistência da aplicação durante cópias de segurança de Windows e Linux VMs para várias aplicações como Oracle, MySQL, Mongo DB, SAP HANA e PostGreSQL, chamadas instantâneos consistentes de aplicação. Isto envolve invocar um pré-script (para quiesce as aplicações) antes de tirar uma foto dos discos e chamar o pós-script (comandos para descongelar as aplicações) após a conclusão do instantâneo, para devolver as aplicações ao modo normal. Enquanto os pré-scripts e pós-scripts da amostra são fornecidos no GitHub, a criação e manutenção destes scripts é da sua responsabilidade. No caso da Oracle, a base de dados deve estar em modo de arquivo para permitir cópias de segurança on-line, e a base de dados apropriada começa e termina os comandos de backup são executados nos pré e pós-scripts, que deve criar e manter. 

O Azure Backup está agora a fornecer uma [estrutura pré-script e pós-script melhorada](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts), atualmente em pré-visualização, onde o serviço Azure Backup fornecerá pré-scripts e pós-scripts embalados para aplicações selecionadas. Os utilizadores do Azure Backup apenas precisam de nomear a aplicação e, em seguida, a cópia de segurança Azure VM irá automaticamente invocar os pré-scripts e pós-scripts relevantes. Os pré-scripts e pós-scripts embalados serão mantidos pela equipa de Backup do Azure para que os utilizadores possam ter a certeza do suporte, propriedade e validade destes scripts. Atualmente, as aplicações apoiadas para o quadro reforçado são a Oracle e a MySQL, com mais tipos de aplicação esperados no futuro. O instantâneo será uma cópia completa do armazenamento e não um incremental ou copy on Write snapshot, por isso é um meio eficaz para restaurar a sua base de dados a partir de.

## <a name="next-steps"></a>Passos seguintes

- [Criar quickstart oracle Database](oracle-database-quick-create.md)
- [Fazer o back-up da Oracle Database para ficheiros Azure](oracle-database-backup-azure-storage.md)
- [Faça backup na Oracle Database utilizando o serviço de backup Azure](oracle-database-backup-azure-backup.md)


