---
title: Cópia de segurança do SAP HANA do Azure no nível de arquivo | Documentos da Microsoft
description: Existem duas principais cópia de segurança possibilidades para o SAP HANA em máquinas virtuais do Azure, este artigo aborda o SAP HANA Azure Backup no nível de ficheiro
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: fc35077e00bc6322a815a52ca6ab3571a4e06d3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60937804"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>Cópia de segurança do SAP HANA do Azure no nível de ficheiro

## <a name="introduction"></a>Introdução

Esta é a parte de uma série de três partes de artigos relacionados na cópia de segurança do SAP HANA. [Guia de segurança para SAP HANA em Azure Virtual Machines](./sap-hana-backup-guide.md) fornece uma descrição geral e informações sobre como começar, e [cópia de segurança do SAP HANA com base nos instantâneos de armazenamento](./sap-hana-backup-storage-snapshots.md) abrange a opção de cópia de segurança do baseadas em instantâneos de armazenamento.

Diferentes tipos VM no Azure permitem que um número diferente de VHDs ligados. Os detalhes exatos estão documentados em [tamanhos de máquinas de virtuais do Linux no Azure](../../linux/sizes.md). Para os testes referidos nesta documentação, nós usamos uma VM do Azure GS5 que permite a 64 discos de dados anexados. Para sistemas de SAP HANA maiores, um número significativo de discos já poderá não estar disponível para os ficheiros de registo e dados, possivelmente em combinação com repartição de software para o débito de e/s de disco ideal. Para obter mais detalhes sobre as configurações de disco sugeridos para implementações de SAP HANA em VMs do Azure, leia o artigo [SAP HANA no guia de operações do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations). As recomendações feitas são incluindo recomendações de espaço em disco para também uma cópias de segurança locais.

Não existe nenhuma integração de cópia de segurança do SAP HANA disponível com o serviço de cópia de segurança do Azure neste momento. É a forma padrão para gerir a cópia de segurança/restauro ao nível do ficheiro com um backup baseado em ficheiros através do SAP HANA Studio ou instruções SQL do SAP HANA. Ver [referência de vistas de sistema e SAP HANA SQL](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) para obter mais informações.

![Esta figura mostra a caixa de diálogo do item de menu de cópia de segurança no SAP HANA Studio](media/sap-hana-backup-file-level/image022.png)

Esta figura mostra a caixa de diálogo do item de menu de cópia de segurança no SAP HANA Studio. Ao escolher o tipo &quot;arquivo,&quot; é necessário especificar um caminho no sistema de arquivos em que o SAP HANA escreve os ficheiros de cópia de segurança. Restauro funciona da mesma forma.

Embora essa opção pode parecer simples e direta, existem algumas considerações. Como mencionado anteriormente, uma VM do Azure tem uma limitação do número de discos de dados que podem ser anexados. Talvez não haja capacidade para armazenar ficheiros de cópia de segurança do SAP HANA em sistemas de ficheiros da VM, dependendo do tamanho dos requisitos de débito da base de dados e de disco, que pode envolver a repartição de software em vários discos de dados. Várias opções para mover estes ficheiros de cópia de segurança e gerir as restrições de tamanho de ficheiro e o desempenho ao processar terabytes de dados, são fornecidas neste artigo.

Outra opção, que oferece mais liberdade em relação à capacidade total, é o armazenamento de Blobs do Azure. Embora um único blob também está limitado a 1 TB, a capacidade total de um contentor de blob único está, atualmente, 500 TB. Além disso, ele oferece aos clientes a opção para selecionar chamados &quot;esporádico&quot; armazenamento, que tem um custo benefício de Blobs. Consulte [armazenamento de Blobs do Azure: Acesso frequente e esporádico camadas de armazenamento](../../../storage/blobs/storage-blob-storage-tiers.md) para obter detalhes sobre o armazenamento de BLOBs de acesso esporádico.

Para segurança adicional, utilize uma conta de armazenamento georreplicado para armazenar as cópias de segurança do SAP HANA. Ver [replicação de armazenamento do Azure](../../../storage/common/storage-redundancy.md) para obter detalhes sobre a replicação de conta de armazenamento.

Um foi possível colocar VHDs dedicados para cópias de segurança do SAP HANA numa conta de armazenamento de cópia de segurança dedicado que é georreplicado. Caso contrário, um foi possível copiar os VHDs que manter as cópias de segurança do SAP HANA para uma conta de armazenamento georreplicado ou para uma conta de armazenamento que está numa região diferente.

## <a name="azure-backup-agent"></a>Agente de cópia de segurança do Azure

Cópia de segurança do Azure oferece a opção de não apenas de cópia de segurança completas VMs, mas também ficheiros e diretórios por meio do agente de cópia de segurança, que tem de ser instalado no SO convidado. Mas este agente só é suportado no Windows (consulte [cópia de segurança um servidor ou cliente Windows para o Azure com o modelo de implementação do Resource Manager](../../../backup/backup-configure-vault.md)).

Uma solução alternativa é primeiro de copiar ficheiros de cópia de segurança do SAP HANA a uma VM do Windows no Azure (por exemplo, por meio de partilha do SAMBA) e, em seguida, utilize o agente de cópia de segurança do Azure a partir daí. Embora seja tecnicamente possível, ele seria adicionar complexidade e abrandar a cópia de segurança ou restaurar processo um pouco devido a cópia entre a Linux e a VM do Windows. Não é recomendado para seguir esta abordagem.

## <a name="azure-blobxfer-utility-details"></a>Detalhes de utilitário blobxfer do Azure

Para armazenar os diretórios e ficheiros no armazenamento do Azure, um pode utilizar a CLI ou o PowerShell ou desenvolva uma ferramenta através de um da [SDKs do Azure](https://azure.microsoft.com/downloads/). Também existe um utilitário de prontos a utilizar, o AzCopy, para copiar dados para o armazenamento do Azure, mas é apenas Windows (consulte [transferir dados com o utilitário de linha de comandos do AzCopy](../../../storage/common/storage-use-azcopy.md)).

Por conseguinte, blobxfer foi utilizado para copiar ficheiros de cópia de segurança do SAP HANA. É aberto, utilizado por muitos clientes em ambientes de produção e está disponível no [GitHub](https://github.com/Azure/blobxfer). Esta ferramenta permite que alguém copiar dados diretamente para o armazenamento de Blobs do Azure ou a partilha de ficheiros do Azure. Ele também oferece uma variedade de recursos úteis, como o md5 hash ou o paralelismo automática ao copiar um diretório com vários ficheiros.

## <a name="sap-hana-backup-performance"></a>Desempenho de cópia de segurança do SAP HANA

![Nesta captura de ecrã é da consola de cópia de segurança do SAP HANA no SAP HANA Studio](media/sap-hana-backup-file-level/image023.png)

É nesta captura de ecrã da consola de cópia de segurança do SAP HANA no SAP HANA Studio. Demorou cerca de 42 minutos para fazer a cópia de segurança do 230 GB num disco de armazenamento standard do Azure ligado à VM do HANA com o sistema de ficheiros XFS.

![Nesta captura de ecrã é do YaST na VM de teste SAP HANA](media/sap-hana-backup-file-level/image024.png)

É nesta captura de ecrã do YaST na VM de teste SAP HANA. Um pode ver o disco único de 1 TB para cópia de segurança do SAP HANA, como mencionado anteriormente. Demorou cerca de 42 minutos a cópia de segurança GB 230. Além disso, cinco discos de 200 GB foram anexados e md0 RAID de software criado, com a repartição sobre esses cinco discos de dados do Azure.

![Repetir a mesma cópia de segurança no software de RAID com repartição em cinco discos de dados de armazenamento standard do Azure anexados](media/sap-hana-backup-file-level/image025.png)

Repetir a mesma cópia de segurança no software de RAID com repartição em cinco armazenamento standard do Azure discos de dados anexados colocado a hora da cópia de 42 minutos até 10 minutos. Os discos foram anexados sem colocação em cache para a VM. Portanto, é óbvio qual a importância débito de escrita de disco destina-se a hora da cópia. Um foi, em seguida, mude para o armazenamento Premium do Azure para acelerar ainda mais o processo para um desempenho ideal. Em geral, o armazenamento Premium do Azure deve ser utilizado para sistemas de produção.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Copiar ficheiros de cópia de segurança do SAP HANA para o armazenamento de Blobs do Azure

Outra opção para armazenar rapidamente os ficheiros de cópia de segurança do SAP HANA é o armazenamento de Blobs do Azure. Um contentor de BLOBs único tem um limite de 500 TB, suficiente para alguns sistemas SAP HANA mais pequenos, usando tipos M32ts, M32ls, M64ls e GS5 VM do Azure, para manter suficientes cópias de segurança do SAP HANA. Os clientes têm a opção entre &quot;frequente&quot; e &quot;frio&quot; armazenamento de BLOBs (consulte [armazenamento de Blobs do Azure: Acesso frequente e esporádico camadas de armazenamento](../../../storage/blobs/storage-blob-storage-tiers.md)).

Com a ferramenta de blobxfer, é fácil copiar os ficheiros de cópia de segurança do SAP HANA diretamente para o armazenamento de Blobs do Azure.

![Aqui, um pode ver os ficheiros de uma cópia de segurança de ficheiros completa do SAP HANA](media/sap-hana-backup-file-level/image026.png)

Aqui, um pode ver os ficheiros de uma cópia de segurança de ficheiros completa do SAP HANA. Existem quatro arquivos e a maior tem aproximadamente 230 GB.

![Demorou cerca de 3000 segundos para copiar os 230 GB para um contentor de BLOBs da conta de armazenamento standard do Azure](media/sap-hana-backup-file-level/image027.png)

Não utilizar o md5 hash no teste inicial, demorou cerca de 3000 segundos para copiar os 230 GB para um contentor de BLOBs da conta de armazenamento standard do Azure.

![Nesta captura de ecrã, um pode ver como ele se parece no portal do Azure](media/sap-hana-backup-file-level/image028.png)

Nesta captura de ecrã, um pode ver o aspeto que no portal do Azure. Um contentor de BLOBs denominado &quot;sap-hana-cópias de segurança&quot; foi criado e inclui os blobs de quatro, que representam os ficheiros de cópia de segurança do SAP HANA. Um deles tem um tamanho de aproximadamente 230 GB.

A consola de cópia de segurança do HANA Studio permite que alguém restringir o tamanho de ficheiro máximo dos ficheiros de cópia de segurança do HANA. No ambiente de exemplo, ele melhorada desempenho, tornando possível ter vários ficheiros de cópia de segurança mais pequenos, em vez de um arquivo grande de 230 GB.

![Definir o limite de tamanho de ficheiro de cópia de segurança a não do lado do HANA&#39;t melhorar o tempo de cópia de segurança](media/sap-hana-backup-file-level/image029.png)

Definir o limite de tamanho de ficheiro de cópia de segurança a não do lado do HANA&#39;t melhorar o tempo de cópia de segurança, porque os ficheiros são escritos em seqüência, como mostrado na figura. O limite de tamanho de ficheiro foi definido para 60 GB, pelo que a cópia de segurança criado quatro arquivos de dados grandes em vez do ficheiro de único 230 GB. Utilizar vários ficheiros de cópia de segurança é uma necessidade para fazer backup de bancos de dados do HANA que tiram partido da memória das maiores VMs do Azure, como M64s, M64ms, M128s e M128ms.

![Para testar o paralelismo da ferramenta blobxfer, o tamanho máximo de cópias de segurança do HANA, em seguida, foi definido para 15 GB](media/sap-hana-backup-file-level/image030.png)

Para testar o paralelismo da ferramenta blobxfer, o tamanho máximo de cópias de segurança do HANA, em seguida, foi definido para 15 GB, o que resultou em 19 ficheiros de cópia de segurança. Esta configuração trouxe o tempo de blobxfer copiar os 230 GB para o armazenamento de Blobs do Azure de 3000 segundos até 875 segundos.

O resultado é devido ao limite de 60 MB/seg de escrita de um blob do Azure. Paralelismo por meio de vários blobs resolve o afunilamento, mas há uma desvantagem: aumentar o desempenho da ferramenta blobxfer para copiar todos os estes HANA arquivos de backup para o armazenamento de Blobs do Azure coloca a carga sobre a VM de HANA e da rede. Operação de sistema HANA torna-se a ser afetada.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>Blob de cópia de discos de dados do Azure dedicada no RAID de software de cópia de segurança

Ao contrário do backup em disco de dados da VM manual, esta abordagem uma não cópia de segurança de todos os discos de dados numa VM para guardar a instalação inteira do SAP, incluindo os dados do HANA, HANA regista ficheiros e ficheiros de configuração. Em vez disso, a idéia é ter dedicado RAID de software de com repartição em todos os dados do Azure vários VHDs para armazenar uma cópia de segurança de ficheiros completa do SAP HANA. Um copia apenas estes discos, que têm a cópia de segurança do SAP HANA. Eles poderiam facilmente ser mantidos numa conta de armazenamento de cópia de segurança do HANA dedicada, ou ligados a um dedicado &quot;VM de gestão de cópia de segurança&quot; para processamento adicional.

![Todos os VHDs envolvidos foram copiados com a * * início-azurestorageblobcopy * * comando do PowerShell](media/sap-hana-backup-file-level/image031.png)

Depois da cópia de segurança para o local RAID de software foi concluída, todos os VHDs envolvidos foram copiados com o **start-azurestorageblobcopy** comando do PowerShell (consulte [AzureStorageBlobCopy início](/powershell/module/azure.storage/start-azurestorageblobcopy)). Como afeta apenas o sistema de ficheiros dedicado para manter os ficheiros de cópia de segurança, não há nenhum preocupações em matéria de consistência de ficheiros de registo ou de dados SAP HANA no disco. Uma vantagem deste comando é que ele funciona, enquanto a VM permanece online. Para ter certeza de que nenhum processo escreve para o conjunto de cópia de segurança do stripe, certifique-se de que desmontá-la antes da cópia de BLOBs e montá-la novamente posteriormente. Ou seria possível usar uma forma adequada para &quot;congelar&quot; o sistema de ficheiros. Por exemplo, via xfs\_congelar para o sistema de ficheiros XFS.

![Nesta captura de ecrã mostra a lista de blobs no contentor de vhds no portal do Azure](media/sap-hana-backup-file-level/image032.png)

Nesta captura de ecrã mostra a lista de blobs no &quot;vhds&quot; contentor no portal do Azure. A captura de ecrã mostra os cinco VHDs, que foram anexados ao servidor SAP HANA VM para servir como o software de RAID para manter os ficheiros de cópia de segurança do SAP HANA. Ela também mostra as cinco cópias, que são obtidas por meio do comando de cópia de Blobs.

![Para fins de teste, as cópias de discos RAID de software de cópia de segurança do SAP HANA foram anexadas à VM do servidor de aplicação](media/sap-hana-backup-file-level/image033.png)

Para fins de teste, as cópias de discos RAID de software de cópia de segurança do SAP HANA foram anexadas à VM do servidor de aplicação.

![Copia o VM foi encerrada para anexar o disco do servidor de aplicações](media/sap-hana-backup-file-level/image034.png)

A VM do servidor de aplicações foi encerrado anexar o disco copia. Depois de iniciar a VM, os discos e o RAID foram detetadas corretamente (montado através do UUID). Apenas o ponto de montagem estava em falta, que foi criado através do YaST particionador. Depois das cópias de ficheiro de cópia de segurança do SAP HANA tornou-se visível no nível do SO.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Copiar ficheiros de cópia de segurança do SAP HANA para partilhar NFS

Para reduzir o impacto potencial no sistema de SAP HANA a partir de um desempenho ou a perspectiva de espaço em disco, um pode optar por armazenar os ficheiros de cópia de segurança do SAP HANA numa partilha NFS. Tecnicamente funciona, mas significa usando uma segunda VM do Azure como o anfitrião da partilha NFS. Não deve ser um tamanho de VM pequeno, devido a largura de banda de rede da VM. Pode fazer sentido, em seguida, para encerrar isso &quot;cópia de segurança de VM&quot; e colocá-lo apenas para a cópia de segurança do SAP HANA em execução. Escrever no NFS partilha coloca a carga na rede e afeta o sistema de SAP HANA, mas simplesmente posteriormente a gerir os ficheiros de cópia de segurança no &quot;cópia de segurança de VM&quot; seria influencia o sistema de SAP HANA.

![Uma partilha NFS a partir de outra VM do Azure estiver montada para a VM do servidor do SAP HANA](media/sap-hana-backup-file-level/image035.png)

Para verificar que o NFS utilizar as maiúsculas e minúsculas, uma partilha NFS a partir de outra VM do Azure estiver montada para a VM do servidor do SAP HANA. Não havia nenhum especial NFS otimização aplicado.

![Demorou 1 hora e 46 minutos para fazer a cópia de segurança diretamente](media/sap-hana-backup-file-level/image036.png)

A partilha NFS foi um conjunto de stripe rápida, como no servidor do SAP HANA. No entanto, demorou 1 hora e 46 minutos para fazer a cópia de segurança diretamente na que partilha o NFS em vez de 10 minutos, quando o gravar num local stripe definido.

![A alternativa não era muito mais rápida, 1 hora e 43 minutos](media/sap-hana-backup-file-level/image037.png)

A alternativa de fazer uma cópia de segurança para um conjunto de local stripe e copiar para a partilha NFS no nível do SO (um simples **cp - avr** comandos) não era muito mais rápida. Demorou 1 hora e 43 minutos.

Para que ele funciona, mas o desempenho não era bom para o teste de cópia de segurança de 230 GB. Ele teria uma aparência ainda pior para vários terabytes.

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Copiar ficheiros de cópia de segurança do SAP HANA para ficheiros do Azure

É possível montar uma partilha de ficheiros do Azure dentro de uma VM do Linux do Azure. O artigo [como utilizar o armazenamento de ficheiros do Azure com o Linux](../../../storage/files/storage-how-to-use-files-linux.md) fornece detalhes sobre como fazê-lo. Tenha em atenção que não existe atualmente um limite de quota de 5 TB de uma partilha de ficheiros do Azure e um limite de tamanho de ficheiro de 1 TB por ficheiro. Ver [metas de desempenho e escalabilidade do armazenamento do Azure](../../../storage/common/storage-scalability-targets.md) para obter informações sobre limites de armazenamento.

Testes mostraram que, no entanto, que não de cópia de segurança do SAP HANA&#39;t atualmente funcionam diretamente com esse tipo de montagem do CIFS. Ele também é indicado na [1820529 de nota SAP](https://launchpad.support.sap.com/#/notes/1820529) que CIFS não é recomendado.

![Esta figura mostra um erro na caixa de diálogo cópia de segurança no SAP HANA Studio](media/sap-hana-backup-file-level/image038.png)

Esta figura mostra um erro na caixa de diálogo cópia de segurança no SAP HANA Studio, ao tentar criar cópias de segurança diretamente para uma partilha de ficheiros do Azure montada do CIFS. Portanto, é necessário fazer uma cópia de segurança padrão do SAP HANA num sistema de ficheiros VM pela primeira vez e, em seguida, copie os ficheiros de cópia de segurança a partir daí, ao serviço ficheiros do Azure.

![Esta figura mostra que demorou cerca de 929 segundos para copiar a 19 de ficheiros de cópia de segurança do SAP HANA](media/sap-hana-backup-file-level/image039.png)

Esta figura mostra que demorou cerca de 929 segundos para copiar a 19 de ficheiros de cópia de segurança de SAP HANA com um tamanho total de aproximadamente 230 GB para a partilha de ficheiros do Azure.

![A estrutura do diretório de origem na SAP HANA VM foi copiada para a partilha de ficheiros do Azure](media/sap-hana-backup-file-level/image040.png)

Nesta captura de ecrã, um pode ver que a estrutura do diretório de origem na SAP HANA VM foram copiada para a partilha de ficheiros do Azure: um diretório (hana\_cópia de segurança\_fsl\_15 gb) e 19 ficheiros de cópia de segurança individuais.

Armazenar ficheiros de cópia de segurança de SAP HANA em ficheiros do Azure pode ser uma opção interessante no futuro quando cópias de segurança de ficheiros de SAP HANA suportá-la diretamente. Ou quando ele possibilita o serviço ficheiros do Azure de montagem por meio de NFS e o limite de quota máxima é consideravelmente maior do que 5 TB.

## <a name="next-steps"></a>Passos Seguintes
* [Guia de segurança para SAP HANA em Azure Virtual Machines](sap-hana-backup-guide.md) fornece uma descrição geral e informações sobre como começar.
* [Cópia de segurança do SAP HANA com base nos instantâneos de armazenamento](sap-hana-backup-storage-snapshots.md) descreve a opção de cópia de segurança do baseadas em instantâneos de armazenamento.
* Para saber como estabelecer a elevada disponibilidade e o plano de recuperação após desastre do SAP HANA no Azure (instâncias grandes), veja [SAP HANA (instâncias grandes) elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md).
