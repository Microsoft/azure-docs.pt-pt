---
title: Sap HANA Azure Backup no nível de ficheiro | Microsoft Docs
description: Existem duas grandes possibilidades de backup para SAP HANA em máquinas virtuais Azure, este artigo abrange SAP HANA Azure Backup no nível de ficheiro
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 0ff226a156721382a289af0f2a8a0f898a57bab9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101667985"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA Azure Backup no nível de ficheiro

## <a name="introduction"></a>Introdução

Este artigo é um artigo relacionado com [o guia de backup da SAP HANA em Azure Virtual Machines,](./sap-hana-backup-guide.md)que fornece uma visão geral e informações sobre como começar e mais detalhes sobre o serviço de backup Azure e instantâneos de armazenamento. 

Diferentes tipos de VM em Azure permitem um número diferente de VHDs ligados. Os detalhes exatos estão documentados em [Tamanhos para máquinas virtuais Linux em Azure](../../sizes.md). Para os testes referidos nesta documentação utilizamos um GS5 Azure VM, que permite 64 discos de dados anexados. Para sistemas SAP HANA maiores, um número significativo de discos pode já ser levado para ficheiros de dados e registos, possivelmente em combinação com a tira de software para uma produção ótima de IO do disco. Para obter mais detalhes sobre as configurações sugeridas do disco para implementações SAP HANA em VMs Azure, leia o artigo [Configurações de armazenamento de máquinas virtuais SAP HANA Azure](./hana-vm-operations-storage.md). As recomendações feitas incluem recomendações de espaço em disco para cópias de segurança locais também.

A forma padrão de gerir backup/restauro ao nível do ficheiro é com uma cópia de segurança baseada em ficheiros através do SAP HANA Studio ou através de declarações SAP HANA SQL. Para mais informações, leia o artigo [SAP HANA SQL e System Views Reference](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.05/en-US/3859e48180bb4cf8a207e15cf25a7e57.html).

![Este número mostra o diálogo do item do menu de reserva no ESTÚDIO SAP HANA](media/sap-hana-backup-file-level/backup-menue-dialog.png)

Este número mostra o diálogo do item do menu de reserva no Estúdio SAP HANA. Ao escolher o ficheiro do &quot; tipo, &quot; é necessário especificar um caminho no sistema de ficheiros onde o SAP HANA escreve os ficheiros de backup. Restaurar funciona da mesma forma.

Embora esta escolha pareça simples e simples, existem algumas considerações. Um Azure VM tem uma limitação do número de discos de dados que podem ser anexados. Pode não haver capacidade para armazenar ficheiros de backup SAP HANA nos sistemas de ficheiros do VM, dependendo do tamanho da base de dados e dos requisitos de produção de discos, o que pode envolver a descoser de vários discos de dados. Várias opções para mover estes ficheiros de backup, e gerir as restrições de tamanho de ficheiro e desempenho ao lidar com terabytes de dados, são fornecidas mais tarde neste artigo.

Outra opção, que oferece mais liberdade em relação à capacidade total, é o armazenamento de blob Azure. Enquanto uma única bolha também é restrita a 1 TB, a capacidade total de um único recipiente blob é atualmente de 500 TB. Além disso, dá aos clientes a opção de selecionar o chamado &quot; armazenamento de &quot; bolhas frescas, que tem um benefício de custo. Consulte [o armazenamento Azure Blob: níveis de acesso quentes, frescos e arquivados](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal) para obter detalhes sobre o armazenamento de bolhas frescas.

Para maior segurança, utilize uma conta de armazenamento geo-replicada para armazenar as cópias de segurança SAP HANA. Consulte [a redundância do Azure Storage](../../../storage/common/storage-redundancy.md) para obter detalhes sobre a redundância de armazenamento e replicação do armazenamento.

Pode-se colocar VHDs dedicados para backups SAP HANA numa conta de armazenamento de backup dedicada que é geo-replicada. Ou então pode-se copiar os VHDs que mantêm as cópias de segurança SAP HANA numa conta de armazenamento geo-replicada, ou para uma conta de armazenamento que esteja numa região diferente.

## <a name="azure-blobxfer-utility-details"></a>Detalhes de utilidade de blobxfer Azure

Para armazenar diretórios e ficheiros no armazenamento Azure, pode-se utilizar CLI ou PowerShell, ou desenvolver uma ferramenta utilizando um dos [SDKs Azure](https://azure.microsoft.com/downloads/). Existe também um utilitário pronto a usar, o AzCopy, para copiar dados para o armazenamento Azure. (ver [dados de transferência com o Utilitário AzCopy Command-Line).](../../../storage/common/storage-use-azcopy-v10.md)

Portanto, o blobxfer foi usado para copiar ficheiros de backup SAP HANA. É de código aberto, utilizado por muitos clientes em ambientes de produção, e disponível no [GitHub.](https://github.com/Azure/blobxfer) Esta ferramenta permite copiar dados diretamente para o armazenamento de blob Azure ou para a partilha de ficheiros Azure. Também oferece uma gama de funcionalidades úteis, como o haxixe md5, ou paralelismo automático ao copiar um diretório com vários ficheiros.

## <a name="sap-hana-backup-performance"></a>Desempenho da cópia de segurança do SAP HANA
Neste capítulo, são discutidas considerações de desempenho. Os números alcançados podem não representar o estado mais recente, uma vez que existe um desenvolvimento estável para obter uma melhor produção para o armazenamento de Azure. Como resultado, deve realizar testes individuais para a sua configuração e região de Azure.

![Esta imagem é da consola de backup SAP HANA no ESTÚDIO SAP HANA](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

Esta imagem mostra a consola de backup SAP HANA do SAP HANA Studio. Demorou cerca de 42 minutos para realizar uma cópia de segurança de 230 GB num único disco de armazenamento Standard HDD Azure ligado ao HANA VM utilizando o sistema de ficheiros XFS no único disco.

![Esta imagem é de YaST no teste SAP HANA VM](media/sap-hana-backup-file-level/one-backup-disk.png)

Esta imagem é de YaST no teste SAP HANA VM. Pode ver o disco único de 1-TB para a cópia de segurança SAP HANA. Demorou cerca de 42 minutos para fazer backup de 230 GB. Além disso, foram anexados cinco discos de 200 GB e criados raid md0 de software, com strip em cima destes cinco discos de dados Azure.

![Repetindo a mesma cópia de segurança no software RAID com striping em cinco discos de dados de armazenamento padrão Azure anexados](media/sap-hana-backup-file-level/five-backup-disks.png)

Repetir a mesma cópia de segurança no software RAID com a despida através de cinco discos de dados de armazenamento padrão Azure ligados elevou o tempo de reserva de 42 minutos para 10 minutos. Os discos foram ligados sem cache no VM. Este exercício demonstra a importância da produção de disco para alcançar um bom tempo de backup. Pode mudar para o armazenamento SSD Standard Azure ou para o Azure Premium Storage para acelerar ainda mais o processo para um desempenho ideal. Em geral, o armazenamento hdd padrão Azure não é recomendado e foi usado apenas para fins de demonstração. Recomendação é utilizar um mínimo de armazenamento SSD Standard Azure ou armazenamento Azure Premium para sistemas de produção.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Copie os ficheiros de backup DA SAP HANA para o armazenamento de blob Azure
Os números de desempenho, os números de duração de backup e os números de duração da cópia mencionados podem não representar o estado mais recente da tecnologia Azure. A Microsoft está a melhorar continuamente o armazenamento do Azure para oferecer mais produção e latências mais baixas. Portanto, os números são apenas para fins de demonstração. Você precisa testar para sua necessidade individual na região Azure da sua escolha para ser capaz de julgar com método é o melhor para você.

Outra opção para armazenar rapidamente ficheiros de backup SAP HANA é o armazenamento de blob Azure. Um único recipiente blob tem um limite de cerca de 500 TB, o suficiente para sistemas SAP HANA, utilizando M32ts, M32ls, M64ls e GS5 VM de Azure, para manter backups SAP HANA suficientes. Os clientes têm a escolha entre &quot; o armazenamento de &quot; bolhas quentes e &quot; &quot; frias (ver [armazenamento Azure Blob: quente, fresco e nível de acesso ao arquivo).](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)

Com a ferramenta blobxfer, é fácil copiar os ficheiros de backup SAP HANA diretamente para o armazenamento de blob Azure.

![Aqui pode-se ver os ficheiros de uma cópia de segurança completa do ficheiro SAP HANA](media/sap-hana-backup-file-level/list-of-backups.png)

Pode ver os ficheiros de uma cópia de segurança completa do ficheiro SAP HANA. Dos quatro ficheiros, o maior tem aproximadamente 230 GB de tamanho.

![Demorou cerca de 3000 segundos a copiar os 230 GB para um recipiente de blob de conta de armazenamento padrão Azure](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

Não utilizar o haxixe md5 no teste inicial, demorou cerca de 3000 segundos a copiar os 230 GB para um recipiente de bolhas de conta de armazenamento padrão Azure.

A consola de backup HANA Studio permite restringir o tamanho máximo de ficheiros de ficheiros HANA. No ambiente da amostra, melhorou o desempenho com vários ficheiros de backup menores, em vez de um grande ficheiro de 230 GB.

Definir o limite de tamanho do ficheiro de cópia de segurança no lado HANA não&#39;melhorar o tempo de cópia de segurança, porque os ficheiros são escritos sequencialmente. O limite de tamanho do ficheiro foi definido para 60 GB, pelo que a cópia de segurança criou quatro grandes ficheiros de dados em vez do ficheiro único de 230 GB. A utilização de vários ficheiros de backup pode tornar-se uma necessidade de fazer backup das bases de dados HANA se os seus alvos de backup tiverem limitações nos tamanhos dos ficheiros de tamanhos de bolhas.

![Para testar o paralelismo da ferramenta blobxfer, o tamanho máximo do ficheiro para cópias de segurança HANA foi então definido para 15 GB](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

Para testar o paralelismo da ferramenta blobxfer, o tamanho máximo do ficheiro para cópias de segurança HANA foi então definido para 15 GB, o que resultou em 19 ficheiros de backup. Esta configuração trouxe o tempo para o blobxfer copiar o armazenamento de 230 GB para a azure blob de 3000 segundos para 875 segundos.

Enquanto explora cópias de backups realizadas contra discos locais para outros locais, como o armazenamento de blob Azure, tenha em mente que a largura de banda utilizada por um eventual processo de cópia paralela é contabilização contra a rede ou quota de armazenamento do seu tipo VM individual. Como resultado, é necessário equilibrar a duração da cópia com a rede e a largura de banda de armazenamento que a carga de trabalho normal em funcionamento no VM é necessária. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Copiar ficheiros de backup SAP HANA para a partilha da NFS

O Microsoft Azure oferece ações nativas da NFS através [dos Ficheiros Azure NetApp](https://azure.microsoft.com/services/netapp/). Pode criar diferentes volumes de dezenas de TBs na capacidade de armazenar e gerir backups. Também pode snapshot esses volumes com base na tecnologia da NetApp. O Azure NetApp Files (ANF) é oferecido em três níveis de serviço diferentes que dão diferentes níveis de armazenamento. Para mais detalhes, leia os níveis de serviço do artigo [Para ficheiros Azure NetApp](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Pode criar e montar um volume NFS a partir da ANF, conforme descrito no artigo [Quickstart: Configurar ficheiros Azure NetApp e criar um volume NFS](../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal).

Além de utilizar volumes nativos de NFS de Azure através da ANF, existem várias possibilidades de criar implementações próprias que fornecem ações NFS em Azure. Todos têm a desvantagem de que precisa para implementar e gerir essas soluções. Algumas dessas possibilidades estão documentadas nestes artigos:

- [Alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md)
- [GlusterFS nas VMs do Azure no Red Hat Enterprise Linux para o SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md)

As ações NFS criadas por meios acima descritos podem ser usadas para executar diretamente backups HANA contra ou para copiar cópias de backups que foram realizadas contra discos locais para essas ações NFS.

> [!NOTE]
> SAP HANA suporta NFS v3 e NFS v4.x. Qualquer outro formato como O SMB com sistema de ficheiros CIFS não é suportado para escrever backups HANA contra. Consulte também [a nota de suporte da SAP #1820529](https://launchpad.support.sap.com/#/notes/1820529)

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Copie ficheiros de backup DA SAP HANA para ficheiros Azure

É possível montar uma partilha de Ficheiros Azure dentro de um VM Azure Linux. O artigo [Como utilizar o armazenamento de ficheiros Azure com o Linux](../../../storage/files/storage-how-to-use-files-linux.md) fornece detalhes sobre como executar a configuração. Para limitações em ficheiros Azure Ou Azure premium, leia o artigo [Azure Files escalaability and performance targets](../../../storage/files/storage-files-scale-targets.md).

> [!NOTE]
> O SMB com o sistema de ficheiros CIFS não é suportado pela SAP HANA para escrever cópias de segurança HANA contra. Consulte também [a nota de suporte da SAP #1820529](https://launchpad.support.sap.com/#/notes/1820529). Como resultado, só pode usar esta solução como destino final de uma cópia de segurança da base de dados HANA que foi conduzida diretamente contra discos anexados locais
> 

Num teste realizado contra os Ficheiros Azure, não com os Ficheiros Azure Premium, demorou cerca de 929 segundos a copiar 19 ficheiros de backup com um volume global de 230 GB. Esperamos que o tempo que o tempo de utilização dos Ficheiros Azure Premium seja muito melhor. No entanto, tem de ter em mente que precisa de equilibrar os interesses de uma cópia rápida com os requisitos que a sua carga de trabalho tem na largura de banda da rede. Uma vez que cada tipo Azure VM aplica quota de largura de banda de rede, você precisa permanecer dentro do intervalo dessa quota com a sua carga de trabalho mais a cópia dos ficheiros de backup.

![Este número mostra que demorou cerca de 929 segundos a copiar 19 ficheiros de backup SAP HANA](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


Armazenar ficheiros de backup SAP HANA em ficheiros Azure pode ser uma opção interessante. Especialmente com a latência melhorada e produção de Ficheiros Azure Premium.

## <a name="next-steps"></a>Passos seguintes
* [O guia de backup para SAP HANA em Azure Virtual Machines](sap-hana-backup-guide.md) dá uma visão geral e informações sobre o início.
* Para aprender a estabelecer uma elevada disponibilidade e plano para a recuperação de desastres da SAP HANA em Azure (grandes instâncias), ver [SAP HANA (grandes instâncias) alta disponibilidade e recuperação de desastres em Azure](hana-overview-high-availability-disaster-recovery.md).
