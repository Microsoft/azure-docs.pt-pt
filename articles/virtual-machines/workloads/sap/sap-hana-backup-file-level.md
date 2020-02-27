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
ms.date: 07/05/2018
ms.author: hermannd
ms.openlocfilehash: d1044e4cbfd59cdf58af1a132a64e7143083905c
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616159"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>Backup SAP HANA Azure no nível de ficheiro

## <a name="introduction"></a>Introdução

Isto faz parte de uma série de artigos relacionados em três partes sobre cópia de segurança SAP HANA. [O guia de backup do SAP HANA em Máquinas Virtuais Azure](./sap-hana-backup-guide.md) fornece uma visão geral e informações sobre o início, e a cópia de [segurança SAP HANA com base em instantâneos](./sap-hana-backup-storage-snapshots.md) de armazenamento cobre a opção de backup baseada em instantâneos de armazenamento.

Diferentes tipos de VM em Azure permitem um número diferente de VHDs ligados. Os detalhes exatos estão documentados em [Tamanhos para máquinas virtuais Linux em Azure](../../linux/sizes.md). Para os testes referidos nesta documentação, usamos um GS5 Azure VM que permite 64 discos de dados anexados. Para sistemas SAP HANA maiores, um número significativo de discos pode já ser tomado para dados e ficheiros de registo, possivelmente em combinação com a tira de software para uma ótima entrada de IO do disco. Para obter mais detalhes sobre as configurações de discos sugeridos para as implementações do SAP HANA em VMs Azure, leia o artigo [SAP HANA no guia de operações do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations). As recomendações feitas incluem recomendações espaciais de disco para backups locais também.

Não existe nenhuma integração de backup SAP HANA disponível com o serviço de backup Azure neste momento. A forma padrão de gerir a cópia de segurança/restauro ao nível do ficheiro é com uma cópia de segurança baseada em ficheiros através do Estúdio SAP HANA ou através de declarações SAP HANA SQL. Consulte [o SAP HANA SQL e](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) o System Views Reference para mais informações.

![Esta figura mostra o diálogo do item do menu de reserva no Estúdio SAP HANA](media/sap-hana-backup-file-level/image022.png)

Esta figura mostra o diálogo do item do menu de reserva no Estúdio SAP HANA. Ao escolher o ficheiro tipo &quot;,&quot; tem de especificar um caminho no sistema de ficheiros onde o SAP HANA escreve os ficheiros de cópia de segurança. Restaurar funciona da mesma forma.

Embora esta escolha pareça simples e direta, existem algumas considerações. Como mencionado anteriormente, um VM Azure tem uma limitação de número de discos de dados que podem ser anexados. Pode não haver capacidade para armazenar ficheiros de backup SAP HANA nos sistemas de ficheiros do VM, dependendo do tamanho da base de dados e dos requisitos de produção de disco, o que pode envolver a desencriptação de software em vários discos de dados. Várias opções para mover estes ficheiros de backup, e gerir restrições de tamanho de ficheiro e desempenho ao manusear terabytes de dados, são fornecidas mais tarde neste artigo.

Outra opção, que oferece mais liberdade em relação à capacidade total, é o armazenamento de blob Azure. Embora uma única bolha também esteja restrita a 1 TB, a capacidade total de um único recipiente de bolha é atualmente de 500 TB. Além disso, dá aos clientes a opção de selecionar o chamado &quot;armazenamento de&quot;  fresco, que tem um custo-benefício. Ver [Armazenamento Azure Blob: Camadas](../../../storage/blobs/storage-blob-storage-tiers.md) de armazenamento quentes e frescos para mais detalhes sobre o armazenamento de bolhas frescas.

Para obter segurança adicional, utilize uma conta de armazenamento geo-replicada para armazenar as cópias de segurança SAP HANA. Consulte [a replicação do Armazenamento Azure](../../../storage/common/storage-redundancy.md) para obter detalhes sobre a replicação da conta de armazenamento.

Pode-se colocar VHDs dedicados para backups SAP HANA numa conta de armazenamento de reserva dedicada que é geo-replicada. Ou então poderia copiar os VHDs que mantêm as cópias de segurança SAP HANA para uma conta de armazenamento geo-replicada, ou para uma conta de armazenamento que está em uma região diferente.

## <a name="azure-backup-agent"></a>Agente de backup Azure

A cópia de segurança Azure oferece a opção não só de backup de VMs completos, mas também de ficheiros e diretórios através do agente de backup, que tem de ser instalado no OS de hóspedes. Mas este agente só é suportado no Windows (consulte [O Back up a Windows Server ou cliente para o Azure utilizando o modelo](../../../backup/backup-configure-vault.md)de implementação do Gestor de Recursos).

Uma salição é copiar primeiro ficheiros de backup SAP HANA para um Windows VM no Azure (por exemplo, através da partilha SAMBA) e depois utilizar o agente de reserva Azure a partir daí. Embora seja tecnicamente possível, acrescentaria complexidade e abrandaria um pouco o processo de backup ou restauro devido à cópia entre o Linux e o Windows VM. Não é aconselhável seguir esta abordagem.

## <a name="azure-blobxfer-utility-details"></a>Detalhes da utilidade da Blobxfer Azure

Para armazenar diretórios e ficheiros sobre o armazenamento do Azure, pode-se utilizar cli ou PowerShell, ou desenvolver uma ferramenta utilizando um dos [SDKs Azure](https://azure.microsoft.com/downloads/). Existe também um utilitário pronto a usar, o AzCopy, para copiar dados para o armazenamento do Azure. (ver Dados de transferência com o Utilitário de [Linha de Comando AzCopy).](../../../storage/common/storage-use-azcopy.md)

Portanto, blobxfer foi usado para copiar ficheiros de reserva SAP HANA. É de código aberto, usado por muitos clientes em ambientes de produção, e disponível no [GitHub.](https://github.com/Azure/blobxfer) Esta ferramenta permite copiar dados diretamente para o armazenamento de blob Azure ou para a partilha de ficheiros Azure. Também oferece uma gama de funcionalidades úteis, como o hash md5 ou o paralelismo automático ao copiar um diretório com vários ficheiros.

## <a name="sap-hana-backup-performance"></a>Desempenho da cópia de segurança do SAP HANA

![Esta imagem é da consola de backup SAP HANA no Estúdio SAP HANA](media/sap-hana-backup-file-level/image023.png)

Esta imagem é da consola de backup SAP HANA no Estúdio SAP HANA. Demorou cerca de 42 minutos a fazer a cópia de segurança dos 230 GB num único disco de armazenamento padrão Azure ligado ao VM HANA utilizando o sistema de ficheiros XFS.

![Esta imagem é de YaST no Teste SAP HANA VM](media/sap-hana-backup-file-level/image024.png)

Esta imagem é de YaST no VM de teste SAP HANA. Pode-se ver o disco único de 1-TB para a cópia de segurança SAP HANA, como mencionado anteriormente. Demorou cerca de 42 minutos a fazer o reforço de 230 GB. Além disso, foram anexados cinco discos de 200 GB e o software RAID md0 criado, com tiras em cima destes cinco discos de dados Azure.

![Repetir a mesma cópia de segurança no software RAID com a descamação em cinco discos de dados padrão de armazenamento do Azure anexados](media/sap-hana-backup-file-level/image025.png)

A repetição da mesma cópia de segurança no software RAID com a descamação em cinco discos de dados de armazenamento padrão Azure anexados fez com que o tempo de backup de 42 minutos para 10 minutos. Os discos estavam ligados sem cache para o VM. Portanto, é óbvio a importância da escrita do disco para o tempo de backup. Em seguida, pode-se mudar para o Armazenamento Azure Premium para acelerar ainda mais o processo para um desempenho ótimo. Em geral, o Armazenamento Premium Azure deve ser utilizado para sistemas de produção.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Copiar ficheiros de backup SAP HANA para armazenamento de blob Azure

Outra opção para armazenar rapidamente ficheiros de backup SAP HANA é o armazenamento de blob Azure. Um único recipiente de bolha tem um limite de 500 TB, o suficiente para alguns sistemas SAP HANA mais pequenos, utilizando M32ts, M32ls, M64ls e GS5 VM tipos de Azure, para manter cópias de segurança Suficientes SAP HANA. Os clientes têm a escolha entre &quot;&quot; quente e &quot;armazenamento de bolhas de&quot; frio (ver [Armazenamento Azure Blob: camadas de armazenamento quentes e frescos).](../../../storage/blobs/storage-blob-storage-tiers.md)

Com a ferramenta blobxfer, é fácil copiar os ficheiros de backup SAP HANA diretamente para o armazenamento de blob Azure.

![Aqui pode-se ver os ficheiros de uma cópia de segurança completa do ficheiro SAP HANA](media/sap-hana-backup-file-level/image026.png)

Aqui pode-se ver os ficheiros de uma cópia de segurança completa do ficheiro SAP HANA. Há quatro ficheiros e o maior tem cerca de 230 GB.

![Demorou cerca de 3000 segundos para copiar os 230 GB para um contentor de blob de conta de armazenamento padrão Azure](media/sap-hana-backup-file-level/image027.png)

Não utilizando haxixe md5 no teste inicial, demorou cerca de 3000 segundos a copiar os 230 GB para um recipiente de depósito padrão Azure.

![Nesta imagem, pode-se ver como fica no portal Azure](media/sap-hana-backup-file-level/image028.png)

Nesta imagem, pode-se ver como fica no portal Azure. Foi criado um contentor de bolhas chamado &quot;sap-hana-backups&quot; foi criado e inclui as quatro bolhas, que representam os ficheiros de backup SAP HANA. Um deles tem um tamanho de aproximadamente 230 GB.

A consola de backup HANA Studio permite restringir o tamanho máximo de ficheiros hana. No ambiente da amostra, melhorou o desempenho, tornando possível ter vários ficheiros de backup menores, em vez de um ficheiro grande de 230 GB.

![Definir o limite de tamanho do&#39;ficheiro de reserva no lado HANA não melhora o tempo de backup](media/sap-hana-backup-file-level/image029.png)

A definição do limite de tamanho&#39;do ficheiro de reserva no lado HANA não melhora o tempo de backup, porque os ficheiros são escritos sequencialmente como mostrado nesta figura. O limite de tamanho do ficheiro foi definido para 60 GB, pelo que a cópia de segurança criou quatro grandes ficheiros de dados em vez do ficheiro único de 230 GB. A utilização de múltiplos ficheiros de backup é uma necessidade para apoiar bases de dados HANA que alavancam a memória de VMs Azure maiores como M64s, M64ms, M128s e M128ms.

![Para testar o paralelismo da ferramenta blobxfer, o tamanho máximo do ficheiro para cópias de segurança HANA foi então definido para 15 GB](media/sap-hana-backup-file-level/image030.png)

Para testar o paralelismo da ferramenta blobxfer, o tamanho máximo do ficheiro para cópias de segurança HANA foi então definido para 15 GB, o que resultou em 19 ficheiros de backup. Esta configuração trouxe tempo para blobxfer copiar o armazenamento de 230 GB para o blob Azure de 3000 segundos para 875 segundos.

Este resultado deve-se ao limite de 60 MB/seg para a escrita de uma bolha Azure. O paralelismo através de várias bolhas resolve o estrangulamento, mas há uma desvantagem: o aumento do desempenho da ferramenta blobxfer para copiar todos estes ficheiros de backup HANA para o armazenamento de blob azure coloca a carga tanto no VM HANA como na rede. O funcionamento do sistema HANA torna-se impactado.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>Cópia blob de discos de dados dedicados do Azure no software de backup RAID

Ao contrário da cópia de segurança manual do disco de dados VM, nesta abordagem não se faz cópia de segurança de todos os discos de dados de um VM para salvar toda a instalação SAP, incluindo dados HANA, ficheiros de registo HANA e ficheiros config. Em vez disso, a ideia é ter software dedicado RAID com striping através de vários VHDs de dados Azure para armazenar uma cópia de segurança completa do ficheiro SAP HANA. Uma cópia apenas estes discos, que têm a cópia de segurança SAP HANA. Poderiam facilmente ser mantidos numa conta de armazenamento de reserva dedicada à HANA, ou anexados a uma empresa dedicada &quot;gestão de backup seletiva&quot; para posterior processamento.

![Todos os VHDs envolvidos foram copiados usando o comando **start-azurestorageblobcopy** PowerShell](media/sap-hana-backup-file-level/image031.png)

Após a cópia de segurança do software local RAID foi concluída, todos os VHDs envolvidos foram copiados usando o comando powerShell de **start-azurestorageblobcopy** (ver [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Uma vez que apenas afeta o sistema de ficheiros dedicado para manter os ficheiros de backup, não existem preocupações sobre os dados do SAP HANA ou a consistência do ficheiro de registo no disco. Um benefício deste comando é que funciona enquanto o VM permanece on-line. Para ter a certeza de que nenhum processo escreve para o conjunto de listras de reserva, certifique-se de desmontá-lo antes da cópia blob, e montá-lo novamente depois. Ou pode-se usar uma forma apropriada de congelar &quot;&quot; sistema de ficheiros. Por exemplo, através de xfs\_congelar para o sistema de ficheiros XFS.

![Esta imagem mostra a lista de bolhas no contentor vhds no portal Azure](media/sap-hana-backup-file-level/image032.png)

Esta imagem mostra a lista de bolhas no &quot;vhds&quot; contentor no portal Azure. A imagem mostra os cinco VHDs, que foram anexados ao VM do servidor SAP HANA para servir como raid de software para manter ficheiros de backup SAP HANA. Também mostra as cinco cópias, que foram tiradas através do comando de cópia blob.

![Para efeitos de teste, as cópias dos discos RAID do software de backup SAP HANA foram anexadas ao VM do servidor de aplicações](media/sap-hana-backup-file-level/image033.png)

Para efeitos de teste, as cópias dos discos RAID do software de backup SAP HANA foram anexadas ao VM do servidor de aplicações.

![O servidor de aplicações VM foi desligado para anexar as cópias do disco](media/sap-hana-backup-file-level/image034.png)

O vM do servidor de aplicações foi desligado para anexar as cópias do disco. Após o início do VM, os discos e o RAID foram descobertos corretamente (montados via UUID). Faltava apenas o ponto de montagem, que foi criado através do partidor YaST. Em seguida, as cópias de ficheiros de cópia sap HANA tornaram-se visíveis a nível de Os.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Copiar ficheiros de backup SAP HANA para a partilha da NFS

Para diminuir o impacto potencial no sistema SAP HANA a partir de uma perspetiva de desempenho ou de espaço em disco, pode considerar-se armazenar os ficheiros de backup SAP HANA numa parte NFS. Tecnicamente funciona, mas significa usar um segundo VM Azure como anfitrião da quota nFS. Não deve ser um pequeno tamanho VM, devido à largura de banda da rede VM. Faria sentido encerrar esta &quot;de apoio v&quot;m e apenas trazê-la para executar o backup SAP HANA. Escrever numa quota da NFS coloca carga na rede e impacta o sistema SAP HANA, mas apenas a gestão dos ficheiros de backup posteriormente no &quot;vM de reserva&quot; não influenciaria de todo o sistema SAP HANA.

![Uma parte da NFS de outro Azure VM foi montada no VM do servidor SAP HANA](media/sap-hana-backup-file-level/image035.png)

Para verificar o caso de utilização do NFS, foi montada uma parte NFS de outro Azure VM para o VM do servidor SAP HANA. Não foi aplicada uma afinação especial da NFS.

![Demorou 1 hora e 46 minutos para fazer o backup diretamente](media/sap-hana-backup-file-level/image036.png)

A parte da NFS foi um conjunto de riscas rápidas, como a do servidor SAP HANA. No entanto, demorou 1 hora e 46 minutos a fazer o backup diretamente na parte nFS em vez de 10 minutos, ao escrever para um conjunto de listras local.

![A alternativa não foi muito mais rápida a 1 hora e 43 minutos](media/sap-hana-backup-file-level/image037.png)

A alternativa de fazer um backup para um conjunto de riscas locais e copiar para a quota nFS no nível de OS (um simples comando **cp-avr)** não foi muito mais rápida. Demorou 1 hora e 43 minutos.

Funciona, mas o desempenho não foi bom para o teste de apoio de 230 GB. Seria ainda pior para multi terabytes.

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Copiar ficheiros de backup SAP HANA para Ficheiros Azure

É possível montar uma partilha de Ficheiros Azure dentro de um VM Azure Linux. O artigo Como utilizar o [armazenamento de Ficheiros Azure com o Linux](../../../storage/files/storage-how-to-use-files-linux.md) fornece detalhes sobre como fazê-lo. Tenha em mente que existe atualmente um limite de quota de 5 TB de uma parte de ficheiro Azure, e um limite de tamanho de ficheiro de 1 TB por ficheiro. Para mais informações, consulte a escalabilidade e os alvos de desempenho do [Azure Files.](../../../storage/files/storage-files-scale-targets.md)

Os testes demonstraram, no entanto,&#39;que a cópia de segurança SAP HANA não funciona atualmente diretamente com este tipo de montagem CIFS. Também está indicado na [Nota SAP 1820529](https://launchpad.support.sap.com/#/notes/1820529) que o CIFS não é recomendado.

![Esta figura mostra um erro no diálogo de backup no Estúdio SAP HANA](media/sap-hana-backup-file-level/image038.png)

Este valor mostra um erro no diálogo de backup no Estúdio SAP HANA, ao tentar fazer backup diretamente a uma partilha de ficheiros Azure montada no CIFS. Por isso, primeiro é preciso fazer uma cópia padrão do SAP HANA num sistema de ficheiros VM e, em seguida, copiar os ficheiros de reserva de lá para o serviço de ficheiros Azure.

![Este número mostra que demorou cerca de 929 segundos para copiar 19 ficheiros de backup SAP HANA](media/sap-hana-backup-file-level/image039.png)

Este valor mostra que demorou cerca de 929 segundos a copiar 19 ficheiros de backup SAP HANA com um tamanho total de cerca de 230 GB para a partilha de ficheiros Azure.

![A estrutura de diretório de origem do VM SAP HANA foi copiada para a partilha de ficheiros Azure](media/sap-hana-backup-file-level/image040.png)

Nesta imagem, pode-se ver que a estrutura de diretório de origem no VM SAP HANA foi copiada para a partilha de ficheiros Azure: um diretório (hana\_backup\_fsl\_15gb) e 19 ficheiros de backup individuais.

Armazenar ficheiros de backup SAP HANA em ficheiros Azure pode ser uma opção interessante no futuro quando as cópias de segurança do ficheiro SAP HANA o suportam diretamente. Ou quando se torna possível montar ficheiros Azure via NFS e o limite máximo de quota é consideravelmente superior a 5 TB.

## <a name="next-steps"></a>Passos seguintes
* [O guia de backup para SAP HANA em Máquinas Virtuais Azure](sap-hana-backup-guide.md) dá uma visão geral e informações sobre o início.
* A cópia de [segurança SAP HANA com base em instantâneos](sap-hana-backup-storage-snapshots.md) de armazenamento descreve a opção de backup baseada em instantâneos de armazenamento.
* Para aprender a estabelecer alta disponibilidade e plano para a recuperação de desastres do SAP HANA em Azure (grandes instâncias), consulte a alta disponibilidade do [SAP HANA (grandes instâncias) e a recuperação de desastres em Azure.](hana-overview-high-availability-disaster-recovery.md)
