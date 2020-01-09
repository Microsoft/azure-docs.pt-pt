---
title: SAP HANA o backup do Azure no nível do arquivo | Microsoft Docs
description: Há duas grandes possibilidades de backup para SAP HANA em máquinas virtuais do Azure, este artigo aborda SAP HANA backup do Azure no nível do arquivo
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: e8ad1a9468c52fd120360827ed3a08f8f53ec3d1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426012"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA o backup do Azure no nível do arquivo

## <a name="introduction"></a>Introdução

Isso faz parte de uma série de três partes de artigos relacionados em SAP HANA backup. O [Guia de backup para SAP Hana em máquinas virtuais do Azure](./sap-hana-backup-guide.md) fornece uma visão geral e informações sobre como começar e [SAP Hana backup com base em instantâneos de armazenamento](./sap-hana-backup-storage-snapshots.md) aborda a opção de backup baseado em instantâneo de armazenamento.

Diferentes tipos de VM no Azure permitem um número diferente de VHDs anexados. Os detalhes exatos são documentados em [tamanhos de máquinas virtuais do Linux no Azure](../../linux/sizes.md). Para os testes mencionados nesta documentação, usamos uma VM do Azure GS5, que permite discos de dados anexados a 64. Para sistemas de SAP HANA maiores, um número significativo de discos já pode ser usado para arquivos de dados e de log, possivelmente em combinação com a distribuição de software para uma taxa de transferência de e/s de disco ideal. Para obter mais detalhes sobre as configurações de disco sugeridas para implantações SAP HANA em VMs do Azure, leia o artigo [SAP Hana no guia de operações do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations). As recomendações feitas também incluem recomendações de espaço em disco para backups locais.

Não há SAP HANA integração de backup disponível com o serviço de backup do Azure no momento. A maneira padrão de gerenciar backup/restauração no nível de arquivo é com um backup baseado em arquivo por meio do SAP HANA Studio ou por meio de instruções SAP HANA SQL. Consulte [SAP Hana referência de exibições do SQL e do sistema](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) para obter mais informações.

![Esta figura mostra a caixa de diálogo do item de menu backup no SAP HANA Studio](media/sap-hana-backup-file-level/image022.png)

Esta figura mostra a caixa de diálogo do item de menu backup no SAP HANA Studio. Ao escolher o tipo &quot;arquivo,&quot; um precisa especificar um caminho no sistema de arquivos em que SAP HANA grava os arquivos de backup. A restauração funciona da mesma maneira.

Embora essa escolha pareça simples e direta, há algumas considerações. Como mencionado anteriormente, uma VM do Azure tem uma limitação do número de discos de dados que podem ser anexados. Pode não haver capacidade para armazenar SAP HANA arquivos de backup nos sistemas de arquivos da VM, dependendo do tamanho dos requisitos de taxa de transferência do banco de dados e do disco, o que pode envolver a distribuição de software em vários discos. Várias opções para mover esses arquivos de backup e gerenciar restrições de tamanho de arquivo e desempenho ao lidar com terabytes de dados são fornecidas posteriormente neste artigo.

Outra opção, que oferece mais liberdade sobre a capacidade total, é o armazenamento de BLOBs do Azure. Embora um único blob também seja restrito a 1 TB, a capacidade total de um único contêiner de BLOBs é, no momento, 500 TB. Além disso, ele oferece aos clientes a opção de selecionar, chamado &quot;armazenamento de BLOBs&quot; frio, que tem um benefício de custo. Consulte [armazenamento de BLOBs do Azure: camadas de armazenamento quentes e frias](../../../storage/blobs/storage-blob-storage-tiers.md) para obter detalhes sobre o armazenamento de BLOBs frio.

Para obter segurança adicional, use uma conta de armazenamento replicado geograficamente para armazenar os backups de SAP HANA. Consulte [replicação de armazenamento do Azure](../../../storage/common/storage-redundancy.md) para obter detalhes sobre a replicação da conta de armazenamento.

É possível fazer VHDs dedicados para backups SAP HANA em uma conta de armazenamento de backup dedicada que é replicada geograficamente. Ou então, é possível copiar os VHDs que mantêm os backups de SAP HANA em uma conta de armazenamento replicada geograficamente ou em uma conta de armazenamento que esteja em uma região diferente.

## <a name="azure-backup-agent"></a>Agente de backup do Azure

O backup do Azure oferece a opção de não apenas fazer backup de VMs completas, mas também arquivos e diretórios por meio do agente de backup, que deve ser instalado no sistema operacional convidado. Mas esse agente só tem suporte no Windows (consulte [fazer backup de um Windows Server ou cliente no Azure usando o modelo de implantação do Gerenciador de recursos](../../../backup/backup-configure-vault.md)).

Uma solução alternativa é copiar primeiro SAP HANA arquivos de backup para uma VM do Windows no Azure (por exemplo, por meio de compartilhamento do SAMBA) e, em seguida, usar o agente de backup do Azure a partir daí. Embora seja tecnicamente possível, ela adicionaria complexidade e tornaria o processo de backup ou restauração mais lento devido à cópia entre o Linux e a VM do Windows. Não é recomendável seguir essa abordagem.

## <a name="azure-blobxfer-utility-details"></a>Detalhes do utilitário blobxfer do Azure

Para armazenar diretórios e arquivos no armazenamento do Azure, é possível usar a CLI ou o PowerShell ou desenvolver uma ferramenta usando um dos [SDKs do Azure](https://azure.microsoft.com/downloads/). Também há um utilitário pronto para uso, AzCopy, para copiar dados para o armazenamento do Azure. (consulte [transferir dados com o utilitário de linha de comando AzCopy](../../../storage/common/storage-use-azcopy.md)).

Portanto, blobxfer foi usado para copiar SAP HANA arquivos de backup. Ele é código-fonte aberto, usado por muitos clientes em ambientes de produção e disponível no [GitHub](https://github.com/Azure/blobxfer). Essa ferramenta permite copiar dados diretamente para o armazenamento de BLOBs do Azure ou para o compartilhamento de arquivos do Azure. Ele também oferece uma variedade de recursos úteis, como o hash MD5 ou o paralelismo automático ao copiar um diretório com vários arquivos.

## <a name="sap-hana-backup-performance"></a>Desempenho da cópia de segurança do SAP HANA

![Esta captura de tela é do console de backup SAP HANA no SAP HANA Studio](media/sap-hana-backup-file-level/image023.png)

Esta captura de tela é do console de backup SAP HANA no SAP HANA Studio. Levou cerca de 42 minutos para fazer o backup dos 230 GB em um único disco de armazenamento standard do Azure conectado à VM do HANA usando o sistema de arquivos XFS.

![Esta captura de tela é do YaST na VM de teste SAP HANA](media/sap-hana-backup-file-level/image024.png)

Esta captura de tela é do YaST na VM de teste SAP HANA. É possível ver o disco único de 1 TB para SAP HANA backup, conforme mencionado anteriormente. Levou cerca de 42 minutos para fazer backup de 230 GB. Além disso, discos de 5 200 GB foram anexados e o software RAID md0 criado, com a distribuição sobre esses cinco discos de dados do Azure.

![Repetindo o mesmo backup no RAID de software com distribuição em cinco discos de dados de armazenamento standard do Azure anexados](media/sap-hana-backup-file-level/image025.png)

Repetir o mesmo backup no RAID de software com a distribuição entre cinco discos de dados de armazenamento standard do Azure anexados trouxe o tempo de backup de 42 minutos para 10 minutos. Os discos foram anexados sem cache para a VM. Portanto, é óbvio como a taxa de transferência de gravação de disco importante é para o tempo de backup. Em seguida, é possível mudar para o armazenamento Premium do Azure para acelerar ainda mais o processo de desempenho ideal. Em geral, o armazenamento Premium do Azure deve ser usado para sistemas de produção.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Copiar SAP HANA arquivos de backup para o armazenamento de BLOBs do Azure

Outra opção para armazenar rapidamente SAP HANA arquivos de backup é o armazenamento de BLOBs do Azure. Um único contêiner de blob tem um limite de 500 TB, o suficiente para alguns sistemas de SAP HANA menores, usando os tipos de VM M32ts, M32ls, M64ls e GS5 do Azure, para manter backups de SAP HANA suficientes. Os clientes têm a opção de &quot;&quot; e &quot;o armazenamento de BLOBs Cold&quot; (consulte [armazenamento de BLOBs do Azure: camadas de armazenamento quentes e frias](../../../storage/blobs/storage-blob-storage-tiers.md)).

Com a ferramenta blobxfer, é fácil copiar os arquivos de backup SAP HANA diretamente para o armazenamento de BLOBs do Azure.

![Aqui, é possível ver os arquivos de um backup completo de arquivo de SAP HANA](media/sap-hana-backup-file-level/image026.png)

Aqui, é possível ver os arquivos de um backup de arquivo SAP HANA completo. Há quatro arquivos e o maior tem aproximadamente 230 GB.

![Levou aproximadamente 3000 segundos para copiar os 230 GB para um contêiner de BLOB da conta de armazenamento standard do Azure](media/sap-hana-backup-file-level/image027.png)

Não usar o hash MD5 no teste inicial, levou aproximadamente 3000 segundos para copiar os 230 GB para um contêiner de BLOB da conta de armazenamento standard do Azure.

![Nesta captura de tela, é possível ver a aparência na portal do Azure](media/sap-hana-backup-file-level/image028.png)

Nesta captura de tela, é possível ver a aparência na portal do Azure. Um contêiner de blob chamado &quot;SAP-Hana-backups&quot; foi criado e inclui os quatro BLOBs, que representam os SAP HANA arquivos de backup. Um deles tem um tamanho de aproximadamente 230 GB.

O console de backup do HANA Studio permite restringir o tamanho máximo do arquivo de arquivos de backup do HANA. No ambiente de exemplo, ele melhorou o desempenho, tornando possível ter vários arquivos de backup menores, em vez de um grande arquivo de 230 GB.

![Definir o limite de tamanho do arquivo de backup no lado&#39;do Hana não melhora o tempo de backup](media/sap-hana-backup-file-level/image029.png)

Definir o limite de tamanho do arquivo de backup no lado&#39;do Hana não melhora o tempo de backup, pois os arquivos são gravados sequencialmente, conforme mostrado nesta figura. O limite de tamanho do arquivo foi definido como 60 GB, portanto, o backup criou quatro arquivos de dados grandes em vez do arquivo único de 230 GB. O uso de vários arquivos de backup é uma necessidade de fazer backup de bancos de dados do HANA que aproveitam a memória de VMs do Azure maiores, como M64s, M64ms, M128s e M128ms.

![Para testar o paralelismo da ferramenta blobxfer, o tamanho máximo do arquivo para backups do HANA foi definido como 15 GB](media/sap-hana-backup-file-level/image030.png)

Para testar o paralelismo da ferramenta blobxfer, o tamanho máximo do arquivo para backups do HANA foi definido como 15 GB, o que resultou em 19 arquivos de backup. Essa configuração trouxe o tempo de blobxfer para copiar os 230 GB para o armazenamento de BLOBs do Azure de 3000 segundos até 875 segundos.

Esse resultado é devido ao limite de 60 MB/s para gravar um blob do Azure. O paralelismo por meio de vários BLOBs resolve o afunilamento, mas há uma desvantagem: aumentar o desempenho da ferramenta blobxfer para copiar todos esses arquivos de backup do HANA para o armazenamento de BLOBs do Azure coloca a carga na VM do HANA e na rede. A operação do sistema HANA é afetada.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>Cópia de blob de discos de dados dedicados do Azure no RAID de software de backup

Ao contrário do backup manual de disco de dados da VM, nessa abordagem, um não faz backup de todos os discos de dados em uma VM para salvar toda a instalação do SAP, incluindo dados do HANA, arquivos de log do HANA e arquivos de configuração. Em vez disso, a ideia é ter um RAID de software dedicado com a distribuição entre vários VHDs de dados do Azure para armazenar um backup de arquivo SAP HANA completo. Um só copia esses discos, que têm o SAP HANA backup. Eles poderiam ser facilmente mantidos em uma conta de armazenamento de backup do HANA dedicada ou anexados a uma VM de gerenciamento de backup &quot;dedicada&quot; para processamento adicional.

![Todos os VHDs envolvidos foram copiados usando o comando * * Start-azurestorageblobcopy * * PowerShell](media/sap-hana-backup-file-level/image031.png)

Após a conclusão do backup para o RAID de software local, todos os VHDs envolvidos foram copiados usando o comando **Start-azurestorageblobcopy** do PowerShell (consulte [Start-azurestorageblobcopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Como afeta apenas o sistema de arquivos dedicado para manter os arquivos de backup, não há nenhuma preocupação sobre a consistência de arquivos de SAP HANA ou de dados no disco. Um benefício desse comando é que ele funciona enquanto a VM permanece online. Para ter certeza de que nenhum processo é gravado no conjunto de distribuição de backup, não se esqueça de desmontá-lo antes da cópia de BLOB e montá-lo novamente depois. Ou uma maneira apropriada de &quot;congelar&quot; sistema de arquivos. Por exemplo, via XFS\_Freeze para o sistema de arquivos XFS.

![Esta captura de tela mostra a lista de BLOBs no contêiner de VHDs no portal do Azure](media/sap-hana-backup-file-level/image032.png)

Esta captura de tela mostra a lista de BLOBs no &quot;VHDs&quot; contêiner no portal do Azure. A captura de tela mostra os cinco VHDs, que foram anexados à VM do SAP HANA Server para servir como o RAID de software para manter SAP HANA arquivos de backup. Ele também mostra as cinco cópias, que foram realizadas por meio do comando de cópia de BLOB.

![Para fins de teste, as cópias dos discos RAID de software de backup SAP HANA foram anexadas à VM do servidor de aplicativos](media/sap-hana-backup-file-level/image033.png)

Para fins de teste, as cópias dos discos RAID de software de backup SAP HANA foram anexadas à VM do servidor de aplicativos.

![A VM do servidor de aplicativos foi desligada para anexar as cópias de disco](media/sap-hana-backup-file-level/image034.png)

A VM do servidor de aplicativos foi desligada para anexar as cópias de disco. Depois de iniciar a VM, os discos e o RAID foram descobertos corretamente (montados via UUID). Somente o ponto de montagem estava ausente, que foi criado por meio do particionador YaST. Posteriormente, o SAP HANA cópias de arquivos de backup se tornaram visíveis no nível do sistema operacional.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Copiar SAP HANA arquivos de backup para o compartilhamento NFS

Para diminuir o impacto potencial no sistema de SAP HANA de uma perspectiva de espaço em disco ou desempenho, é possível considerar armazenar os arquivos de backup SAP HANA em um compartilhamento NFS. Tecnicamente, ele funciona, mas significa usar uma segunda VM do Azure como o host do compartilhamento NFS. Ele não deve ser um tamanho de VM pequeno, devido à largura de banda da rede VM. Isso faria sentido então desligar esse &quot;&quot; VM de backup e colocá-lo apenas para executar o backup de SAP HANA. A gravação em um compartilhamento NFS coloca a carga na rede e afeta o sistema SAP HANA, mas simplesmente gerenciar os arquivos de backup posteriormente na VM de backup &quot;&quot; não influenciaria o sistema SAP HANA.

![Um compartilhamento NFS de outra VM do Azure foi montado na VM do servidor SAP HANA](media/sap-hana-backup-file-level/image035.png)

Para verificar o caso de uso do NFS, um compartilhamento NFS de outra VM do Azure foi montado na VM do servidor SAP HANA. Nenhum ajuste de NFS especial foi aplicado.

![Demorou 1 hora e 46 minutos para fazer o backup diretamente](media/sap-hana-backup-file-level/image036.png)

O compartilhamento NFS era um conjunto de distribuição rápido, como aquele no servidor de SAP HANA. No entanto, demorou 1 hora e 46 minutos para fazer o backup diretamente no compartilhamento NFS, em vez de 10 minutos, ao gravar em um conjunto de distribuição local.

![A alternativa não era muito mais rápida em 1 hora e 43 minutos](media/sap-hana-backup-file-level/image037.png)

A alternativa de fazer um backup em um conjunto de distribuição local e copiar para o compartilhamento NFS no nível do sistema operacional (um comando **CP-AVR** simples) não era muito mais rápida. Levou 1 hora e 43 minutos.

Funciona, mas o desempenho não era bom para o teste de backup de 230 GB. Pareceria ainda pior para vários terabytes.

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Copiar SAP HANA arquivos de backup para arquivos do Azure

É possível montar um compartilhamento de arquivos do Azure dentro de uma VM Linux do Azure. O artigo [como usar o armazenamento de arquivos do Azure com o Linux](../../../storage/files/storage-how-to-use-files-linux.md) fornece detalhes sobre como fazer isso. Tenha em mente que atualmente há um limite de cota de 5 TB de um compartilhamento de arquivos do Azure e um limite de tamanho de arquivo de 1 TB por arquivo. Para obter mais informações, consulte [escalabilidade e metas de desempenho dos arquivos do Azure](../../../storage/files/storage-files-scale-targets.md).

Os testes mostraram, no entanto, que&#39;SAP Hana backup atualmente não funciona diretamente com esse tipo de montagem CIFS. Ele também é indicado no [SAP Note 1820529](https://launchpad.support.sap.com/#/notes/1820529) que o CIFS não é recomendado.

![Esta figura mostra um erro na caixa de diálogo de backup no SAP HANA Studio](media/sap-hana-backup-file-level/image038.png)

Esta figura mostra um erro na caixa de diálogo de backup no SAP HANA Studio, ao tentar fazer backup diretamente em um compartilhamento de arquivos do Azure montado em CIFS. Portanto, é preciso fazer um backup SAP HANA padrão em um sistema de arquivos de VM primeiro e, em seguida, copiar os arquivos de backup desse local para o serviço de arquivos do Azure.

![Esta figura mostra que levou cerca de 929 segundos para copiar 19 SAP HANA arquivos de backup](media/sap-hana-backup-file-level/image039.png)

Esta figura mostra que levou cerca de 929 segundos para copiar 19 SAP HANA arquivos de backup com um tamanho total de aproximadamente 230 GB para o compartilhamento de arquivos do Azure.

![A estrutura do diretório de origem na VM SAP HANA foi copiada para o compartilhamento de arquivos do Azure](media/sap-hana-backup-file-level/image040.png)

Nesta captura de tela, é possível ver que a estrutura do diretório de origem na VM SAP HANA foi copiada para o compartilhamento de arquivos do Azure: um diretório (Hana\_backup\_FSL\_15 GB) e 19 arquivos de backup individuais.

Armazenar SAP HANA arquivos de backup em arquivos do Azure pode ser uma opção interessante no futuro quando SAP HANA backups de arquivos dão suporte diretamente a ele. Ou quando for possível montar arquivos do Azure via NFS e o limite máximo de cota é consideravelmente maior que 5 TB.

## <a name="next-steps"></a>Passos seguintes
* O [Guia de backup para SAP Hana em máquinas virtuais do Azure](sap-hana-backup-guide.md) fornece uma visão geral e informações sobre como começar.
* [SAP Hana backup com base em instantâneos de armazenamento](sap-hana-backup-storage-snapshots.md) descreve a opção de backup baseado em instantâneo de armazenamento.
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA no Azure (instâncias grandes), consulte [alta disponibilidade e recuperação de desastre do SAP Hana (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md).
