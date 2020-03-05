---
title: SAP HANA Azure Backup no nível de ficheiros Microsoft Docs
description: Existem duas grandes possibilidades de backup para sAP HANA em máquinas virtuais Azure, este artigo cobre SAP HANA Azure Backup no nível de ficheiro
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 93b67936166eb73db5e9a15db42c2c6135794108
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271387"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>Backup SAP HANA Azure no nível de ficheiro

## <a name="introduction"></a>Introdução

Este artigo é um artigo relacionado com o [guia de backup para o SAP HANA em Máquinas Virtuais Azure,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)que fornece uma visão geral e informações sobre o início e mais detalhes sobre o serviço de backup Azure e fotos de armazenamento. 

Diferentes tipos de VM em Azure permitem um número diferente de VHDs ligados. Os detalhes exatos estão documentados em [Tamanhos para máquinas virtuais Linux em Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes). Para os testes referidos nesta documentação usamos um GS5 Azure VM, que permite 64 discos de dados anexados. Para sistemas SAP HANA maiores, um número significativo de discos pode já ser tomado para dados e ficheiros de registo, possivelmente em combinação com a tira de software para uma ótima entrada de IO do disco. Para obter mais detalhes sobre as configurações de disco sugeridas para as implementações do SAP HANA em VMs Azure, leia o artigo Configurações de [armazenamento de máquinas virtuais SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). As recomendações feitas incluem recomendações espaciais de disco para backups locais também.

A forma padrão de gerir a cópia de segurança/restauro ao nível do ficheiro é com uma cópia de segurança baseada em ficheiros através do Estúdio SAP HANA ou através de declarações SAP HANA SQL. Para mais informações, leia o artigo [SAP HANA SQL e System Views Reference](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf).

![Esta figura mostra o diálogo do item do menu de reserva no Estúdio SAP HANA](media/sap-hana-backup-file-level/backup-menue-dialog.png)

Esta figura mostra o diálogo do item do menu de reserva no Estúdio SAP HANA. Ao escolher o ficheiro tipo &quot;,&quot; tem de especificar um caminho no sistema de ficheiros onde o SAP HANA escreve os ficheiros de cópia de segurança. Restaurar funciona da mesma forma.

Embora esta escolha pareça simples e direta, existem algumas considerações. Um VM Azure tem uma limitação de número de discos de dados que podem ser anexados. Pode não haver capacidade para armazenar ficheiros de backup SAP HANA nos sistemas de ficheiros do VM, dependendo do tamanho da base de dados e dos requisitos de produção de disco, o que pode envolver a desencriptação de software em vários discos de dados. Várias opções para mover estes ficheiros de backup, e gerir restrições de tamanho de ficheiro e desempenho ao manusear terabytes de dados, são fornecidas mais tarde neste artigo.

Outra opção, que oferece mais liberdade em relação à capacidade total, é o armazenamento de blob Azure. Embora uma única bolha também esteja restrita a 1 TB, a capacidade total de um único recipiente de bolha é atualmente de 500 TB. Além disso, dá aos clientes a opção de selecionar o chamado &quot;armazenamento de&quot;  fresco, que tem um custo-benefício. Consulte [o armazenamento Azure Blob: hot, cool e archive access tiers](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal) para obter detalhes sobre o armazenamento de blob fresco.

Para obter segurança adicional, utilize uma conta de armazenamento geo-replicada para armazenar as cópias de segurança SAP HANA. Consulte o despedimento do [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) para obter detalhes sobre a redundância de armazenamento e a replicação de armazenamento.

Pode-se colocar VHDs dedicados para backups SAP HANA numa conta de armazenamento de reserva dedicada que é geo-replicada. Ou então poderia copiar os VHDs que mantêm as cópias de segurança SAP HANA para uma conta de armazenamento geo-replicada, ou para uma conta de armazenamento que está em uma região diferente.

## <a name="azure-blobxfer-utility-details"></a>Detalhes da utilidade da Blobxfer Azure

Para armazenar diretórios e ficheiros sobre o armazenamento do Azure, pode-se utilizar cli ou PowerShell, ou desenvolver uma ferramenta utilizando um dos [SDKs Azure](https://azure.microsoft.com/downloads/). Existe também um utilitário pronto a usar, o AzCopy, para copiar dados para o armazenamento do Azure. (ver Dados de transferência com o Utilitário de [Linha de Comando AzCopy).](../../../storage/common/storage-use-azcopy.md)

Portanto, blobxfer foi usado para copiar ficheiros de reserva SAP HANA. É de código aberto, usado por muitos clientes em ambientes de produção, e disponível no [GitHub.](https://github.com/Azure/blobxfer) Esta ferramenta permite copiar dados diretamente para o armazenamento de blob Azure ou para a partilha de ficheiros Azure. Também oferece uma gama de funcionalidades úteis, como o hash md5, ou paralelismo automático ao copiar um diretório com vários ficheiros.

## <a name="sap-hana-backup-performance"></a>Desempenho da cópia de segurança do SAP HANA
Neste capítulo, são discutidas considerações de desempenho. Os números alcançados podem não representar o estado mais recente, uma vez que existe um desenvolvimento constante para obter um melhor rendimento para o armazenamento azure. Como resultado, deve realizar testes individuais para a sua configuração e região de Azure.

![Esta imagem é da consola de backup SAP HANA no Estúdio SAP HANA](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

Esta imagem mostra a consola de reserva SAP HANA do Estúdio SAP HANA. Demorou cerca de 42 minutos a executar uma cópia de segurança de 230 GB num único disco de armazenamento Azure Standard HDD ligado ao VM HANA utilizando o sistema de ficheiros XFS num disco.

![Esta imagem é de YaST no Teste SAP HANA VM](media/sap-hana-backup-file-level/one-backup-disk.png)

Esta imagem é de YaST no VM de teste SAP HANA. Pode ver o disco único de 1 TB para a cópia de segurança SAP HANA. Demorou cerca de 42 minutos a fazer o reforço de 230 GB. Além disso, foram anexados cinco discos de 200 GB e o software RAID md0 criado, com tiras em cima destes cinco discos de dados Azure.

![Repetir a mesma cópia de segurança no software RAID com a descamação em cinco discos de dados padrão de armazenamento do Azure anexados](media/sap-hana-backup-file-level/five-backup-disks.png)

A repetição da mesma cópia de segurança no software RAID com a descamação em cinco discos de dados de armazenamento padrão Azure anexados fez com que o tempo de backup de 42 minutos para 10 minutos. Os discos estavam ligados sem cache para o VM. Este exercício demonstra a importância da escrita do disco para alcançar um bom tempo de backup. Pode mudar para o armazenamento SSD Padrão Azure ou para acelerar ainda mais o processo para um desempenho ótimo. Em geral, o armazenamento hdd padrão Azure não é recomendado e foi usado apenas para fins de demonstração. Recomendação é utilizar um mínimo de armazenamento SSD Padrão Azure ou Armazenamento Premium Azure para sistemas de produção.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Copiar ficheiros de backup SAP HANA para armazenamento de blob Azure
Os números de desempenho, os números de duração da cópia e os números de duração da cópia mencionados podem não representar o estado mais recente da tecnologia Azure. A Microsoft está a melhorar continuamente o armazenamento do Azure para fornecer mais entrada e mais llácências mais baixas. Portanto, os números são apenas para fins de demonstração. Você precisa testar para a sua necessidade individual na região azure a sua escolha para ser capaz de julgar com método é o melhor para você.

Outra opção para armazenar rapidamente ficheiros de backup SAP HANA é o armazenamento de blob Azure. Um único recipiente de bolha tem um limite de cerca de 500 TB, o suficiente para sistemas SAP HANA, utilizando M32ts, M32ls, M64ls e GS5 VM tipos de Azure, para manter cópias de segurança Suficientes SAP HANA. Os clientes têm a escolha entre &quot;&quot; quente e &quot;armazenamento de bolhas de&quot; frio (ver [armazenamento Azure Blob: hot, cool e archive access tiers).](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)

Com a ferramenta blobxfer, é fácil copiar os ficheiros de backup SAP HANA diretamente para o armazenamento de blob Azure.

![Aqui pode-se ver os ficheiros de uma cópia de segurança completa do ficheiro SAP HANA](media/sap-hana-backup-file-level/list-of-backups.png)

Pode ver os ficheiros de uma cópia de segurança completa do ficheiro SAP HANA. Dos quatro ficheiros, o maior tem cerca de 230 GB de tamanho.

![Demorou cerca de 3000 segundos para copiar os 230 GB para um contentor de blob de conta de armazenamento padrão Azure](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

Não utilizando haxixe md5 no teste inicial, demorou cerca de 3000 segundos a copiar os 230 GB para um recipiente de depósito padrão Azure.

A consola de backup HANA Studio permite restringir o tamanho máximo de ficheiros hana. No ambiente da amostra, melhorou o desempenho ao ter vários ficheiros de backup menores, em vez de um ficheiro grande de 230 GB.

A definição do limite de tamanho&#39;do ficheiro de reserva no lado HANA não melhora o tempo de backup, porque os ficheiros são escritos sequencialmente. O limite de tamanho do ficheiro foi definido para 60 GB, pelo que a cópia de segurança criou quatro grandes ficheiros de dados em vez do ficheiro único de 230 GB. A utilização de vários ficheiros de backup pode tornar-se uma necessidade para fazer backup nas bases de dados da HANA se os seus alvos de backup tiverem limitações nos tamanhos dos ficheiros dos tamanhos de blob.

![Para testar o paralelismo da ferramenta blobxfer, o tamanho máximo do ficheiro para cópias de segurança HANA foi então definido para 15 GB](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

Para testar o paralelismo da ferramenta blobxfer, o tamanho máximo do ficheiro para cópias de segurança HANA foi então definido para 15 GB, o que resultou em 19 ficheiros de backup. Esta configuração trouxe tempo para blobxfer copiar o armazenamento de 230 GB para o blob Azure de 3000 segundos para 875 segundos.

Enquanto explora cópias de cópias efetuadas contra discos locais para outros locais, como o armazenamento de blob Azure, tenha em mente que a largura de banda utilizada por um eventual processo de cópia paralela está a ser contabilizada com a rede ou quota de armazenamento do seu tipo VM individual. Como resultado, é necessário equilibrar a duração da cópia com a largura de banda de rede e armazenamento que a carga de trabalho normal em execução no VM está a exigir. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Copiar ficheiros de backup SAP HANA para a partilha da NFS

O Microsoft Azure oferece ações nFS nativas através [de Ficheiros Azure NetApp.](https://azure.microsoft.com/services/netapp/) Pode criar diferentes volumes de dezenas de TBs na capacidade de armazenar e gerir backups. Também pode tirar esses volumes com base na tecnologia do NetApp. O Azure NetApp Files (ANF) é oferecido em três diferentes níveis de serviço que dão diferentes resultados de armazenamento. Para mais detalhes, leia os níveis de serviço do artigo [para ficheiros Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Pode criar e montar um volume NFS a partir da ANF, conforme descrito no artigo [Quickstart: Configurar ficheiros Azure NetApp e criar um volume NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes?tabs=azure-portal).

Além de utilizar volumes nativos de NFS de Azure através da ANF, existem várias possibilidades de criar implementações próprias que fornecem ações da NFS no Azure. Todos têm a desvantagem que precisam para implementar e gerir essas soluções por si mesmos. Algumas dessas possibilidades estão documentadas nestes artigos:

- [Alta disponibilidade para NFS em VMs Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- [GlusterFS nas VMs do Azure no Red Hat Enterprise Linux para o SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)

As ações da NFS criadas por meios acima descritos podem ser utilizadas para executar diretamente cópias de segurança da HANA contra ou para copiar cópias de cópias de cópia seletivas que foram realizadas contra discos locais para essas ações da NFS.

> [!NOTE]
> Suporte SAP HANA NFS v3 e NFS v4.x. Qualquer outro formato como o SMB com sistema de ficheiros CIFS não é suportado para escrever backups HANA contra. Consulte também a [nota de suporte SAP #1820529](https://launchpad.support.sap.com/#/notes/1820529)

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Copiar ficheiros de backup SAP HANA para Ficheiros Azure

É possível montar uma partilha de Ficheiros Azure dentro de um VM Azure Linux. O artigo Como utilizar o [armazenamento do Ficheiro Azure com](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux) o Linux fornece detalhes sobre como executar a configuração. Para limitações em Ficheiros Azure ou ficheiros premium Azure, leia o artigo [Azure Files e alvos](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets)de desempenho .

> [!NOTE]
> O Sistema de Ficheiros SMB com CIFS não é suportado pela SAP HANA para escrever backups HANA contra. Consulte também a nota de [suporte SAP #1820529](https://launchpad.support.sap.com/#/notes/1820529). Como resultado, você só pode usar esta solução como destino final de uma cópia de segurança da base de dados HANA que foi conduzida diretamente contra discos ligados locais
> 

Num teste realizado contra ficheiros Azure, não o Azure Premium Files demorou cerca de 929 segundos a copiar 19 ficheiros de backup com um volume global de 230 GB. Esperamos que o tempo que a utilização dos Ficheiros Premium Azure seja muito melhor. No entanto, tem de ter em mente que precisa de equilibrar os interesses de uma cópia rápida com os requisitos que a sua carga de trabalho tem na largura de banda da rede. Uma vez que cada tipo De VM Azure aplica quota de largura de banda da rede, você precisa ficar dentro do alcance dessa quota com a sua carga de trabalho mais a cópia dos ficheiros de backup.

![Este número mostra que demorou cerca de 929 segundos para copiar 19 ficheiros de backup SAP HANA](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


Armazenar ficheiros de backup SAP HANA em ficheiros Azure pode ser uma opção interessante. Especialmente com a latência melhorada e a entrada de Ficheiros Premium Azure.

## <a name="next-steps"></a>Passos seguintes
* [O guia de backup para SAP HANA em Máquinas Virtuais Azure](sap-hana-backup-guide.md) dá uma visão geral e informações sobre o início.
* Para aprender a estabelecer alta disponibilidade e plano para a recuperação de desastres do SAP HANA em Azure (grandes instâncias), consulte a alta disponibilidade do [SAP HANA (grandes instâncias) e a recuperação de desastres em Azure.](hana-overview-high-availability-disaster-recovery.md)
