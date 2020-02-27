---
title: Backup SAP HANA Azure com base em instantâneos de armazenamento / Microsoft Docs
description: Existem duas grandes possibilidades de backup para sap HANA em máquinas virtuais Azure, este artigo cobre backup SAP HANA com base em instantâneos de armazenamento
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
ms.openlocfilehash: c977bc7db5608e5718e98a26ed594e5ebf2be998
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617422"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>Backup SAP HANA com base em instantâneos de armazenamento

## <a name="introduction"></a>Introdução

Isto faz parte de uma série de artigos relacionados em três partes sobre cópia de segurança SAP HANA. [O guia de backup do SAP HANA em Máquinas Virtuais Azure](sap-hana-backup-guide.md) fornece uma visão geral e informações sobre o início, e [o SAP HANA Azure Backup no nível](sap-hana-backup-file-level.md) de ficheiro cobre a opção de backup baseada em ficheiros.

Ao utilizar uma funcionalidade de backup VM para um sistema de demonstração all-in-one de um único caso, deve considerar-se fazer uma cópia de segurança VM em vez de gerir cópias de segurança HANA ao nível de OS. Uma alternativa é pegar em imagens de blob Azure para criar cópias de discos virtuais individuais, que estão ligados a uma máquina virtual, e manter os ficheiros de dados HANA. Mas um ponto crítico é a consistência da aplicação ao criar uma cópia de segurança VM ou de um disco instantâneo enquanto o sistema está em funcionamento. Consulte a _consistência dos dados do SAP HANA ao tirar fotografias_ de armazenamento no artigo relacionado Guia de backup para [SAP HANA em Máquinas Virtuais Azure](sap-hana-backup-guide.md). A SAP HANA tem uma funcionalidade que suporta este tipo de instantâneos de armazenamento.

## <a name="sap-hana-snapshots-as-central-part-of-application-consistent-backups"></a>Snapshots SAP HANA como parte central de backups consistentes de aplicação

Existe uma funcionalidade no SAP HANA que suporta tirar uma foto de armazenamento. Existe uma restrição aos sistemas de contentores únicos. Os cenários de SAP HANA MCS com mais de um inquilino não suportam este tipo de instantâneo de base de dados SAP HANA (ver [Create a Storage Snapshot (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

Funciona da seguinte forma:

- Prepare-se para um instantâneo de armazenamento, aiciando o instantâneo SAP HANA
- Executar o instantâneo de armazenamento (instantâneo de blob Azure, por exemplo)
- Confirme o instantâneo do SAP HANA

![Esta imagem mostra que um instantâneo de dados SAP HANA pode ser criado através de uma declaração SQL](media/sap-hana-backup-storage-snapshots/image011.png)

Esta imagem mostra que um instantâneo de dados SAP HANA pode ser criado através de uma declaração SQL.

![O instantâneo também aparece no catálogo de backup no Estúdio SAP HANA](media/sap-hana-backup-storage-snapshots/image012.png)

O instantâneo também aparece no catálogo de backup no Estúdio SAP HANA.

![No disco, o instantâneo aparece no diretório de dados SAP HANA](media/sap-hana-backup-storage-snapshots/image013.png)

No disco, o instantâneo aparece no diretório de dados da SAP HANA.

É preciso garantir que a consistência do sistema de ficheiros também é garantida antes de executar o instantâneo de armazenamento enquanto o SAP HANA está no modo de preparação instantânea. Consulte a _consistência dos dados do SAP HANA ao tirar fotografias_ de armazenamento no artigo relacionado Guia de backup para [SAP HANA em Máquinas Virtuais Azure](sap-hana-backup-guide.md).

Uma vez feito o instantâneo de armazenamento, é fundamental confirmar o instantâneo SAP HANA. Existe uma declaração SQL correspondente para executar: BACKUP DATA CLOSE SNAPSHOT (ver [BACKUP DATA CLOSE SNAPSHOT Statement (Backup and Recovery)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm)).

> [!IMPORTANT]
> Confirme a foto da HANA. Devido ao &quot;Copy-on-Write,&quot; O SAP HANA pode necessitar de espaço adicional no disco no modo de preparação de instantâneos, e não é possível iniciar novas cópias até que o instantâneo SAP HANA seja confirmado.

## <a name="hana-vm-backup-via-azure-backup-service"></a>Backup HANA VM através do serviço de backup Azure

O agente de reserva do serviço De backup Azure não está disponível para VMs Linux. Além disso, o Linux não possui funcionalidades semelhantes à que o Windows tem com VSS.  Para utilizar a cópia de segurança do Azure no nível de ficheiro/diretório, uma cópia dos ficheiros de backup SAP HANA para um VM do Windows e, em seguida, utilizar o agente de reserva. 

Caso contrário, apenas é possível um backup Full Linux VM através do serviço de backup Azure. Consulte [a visão geral das funcionalidades em Azure Backup](../../../backup/backup-introduction-to-azure-backup.md) para saber mais.

O serviço de backup Azure oferece uma opção para fazer backup e restaurar um VM. Mais informações sobre este serviço e como funciona podem ser encontradas no artigo Planeie a sua infraestrutura de [backup VM em Azure](../../../backup/backup-azure-vms-introduction.md).

Há duas considerações importantes de acordo com o mesmo artigo:

_&quot;Para máquinas virtuais Linux, apenas cópias de segurança consistentes com ficheiros são possíveis, uma vez que o Linux não tem uma plataforma equivalente à VSS.&quot;_

_&quot;As Aplicações precisam de implementar o seu próprio mecanismo de&quot; de fixação &quot;nos dados restaurados.&quot;_

Portanto, temos de nos certificar de que o SAP HANA está num estado consistente no disco quando a cópia de segurança começa. Consulte _as imagens do SAP HANA descritas_ anteriormente no documento. Mas há um problema potencial quando o SAP HANA permanece neste modo de preparação instantânea. Consulte Criar um Snapshot de [Armazenamento (Estúdio SAP HANA)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) para obter mais informações.

O artigo diz:

_&quot;É fortemente recomendado confirmar ou abandonar um instantâneo de armazenamento o mais rapidamente possível após a sua criação. Enquanto o instantâneo de armazenamento está a ser preparado ou criado, os dados instantâneos relevantes são congelados. Enquanto os dados instantâneos relevantes permanecem congelados, ainda podem ser feitas alterações na base de dados. Tais alterações não farão com que os dados congelados relevantes para instantâneos sejam alterados. Em vez disso, as alterações são escritas para posições na área de dados separadas do instantâneo de armazenamento. As alterações também estão escritas no registo. No entanto, quanto mais tempo os dados instantâneos relevantes forem mantidos congelados, mais o volume de dados pode crescer.&quot;_

A Azure Backup cuida da consistência do sistema de ficheiros através das extensões VM Do Azure. Estas extensões não estão disponíveis autónomas, e funcionam apenas em combinação com o serviço de backup Azure. No entanto, ainda é um requisito fornecer scripts para criar e apagar um instantâneo SAP HANA para garantir a consistência da aplicação.

O Azure Backup tem quatro fases principais:

- Execute o roteiro de preparar - script precisa criar um instantâneo SAP HANA
- Tirar fotografias
- Execute script pós-instantâneo - script precisa de eliminar o SAP HANA criado pelo script de preparação
- Transferir dados para o cofre

Para mais detalhes sobre onde copiar estes scripts e detalhes sobre o funcionamento exato do Azure Backup, consulte os seguintes artigos:

- [Planear a sua infraestrutura de cópias de segurança de VMs no Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Backup consistente com aplicações de VMs Azure Linux](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)



Neste momento, a Microsoft não publicou scripts de preparação e scripts pós-instantâneos para o SAP HANA. Como cliente ou integrador do sistema, teria de criar esses scripts e configurar o procedimento com base na documentação acima referida.


## <a name="restore-from-application-consistent-backup-against-a-vm"></a>Restaurar a partir de backup consistente de aplicação contra um VM
O processo de restauro de uma cópia de segurança consistente de aplicação tomada por backup Azure está documentado no artigo [Recuperar ficheiros da cópia de segurança virtual da máquina azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm). 

> [!IMPORTANT]
> No artigo [Recuperar ficheiros da cópia de segurança virtual da máquina Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) é uma lista de exceções e passos listados ao utilizar conjuntos de riscas de disco. Os discos às riscas são provavelmente a configuração vm regular para SAP HANA. Por conseguinte, é essencial ler o artigo e testar o processo de restauro para casos listados no artigo. 



## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>Chave de licença HANA e VM restaurar através do serviço de backup Azure

O serviço de backup Azure foi concebido para criar um novo VM durante o restauro. Não há nenhum plano neste momento para fazer um &quot;no local&quot; restaurar um VM Azure existente.

![Este número mostra a opção de restauro do serviço Azure no portal Azure](media/sap-hana-backup-storage-snapshots/image019.png)

Este número mostra a opção de restauro do serviço Azure no portal Azure. Pode-se escolher entre criar um VM durante a restauração ou restaurar os discos. Depois de restaurar os discos, ainda é necessário criar um novo VM em cima dele. Sempre que um novo VM é criado no Azure, as mudanças únicas de ID VM (ver [Accessing and Using Azure VM Unique ID](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![Esta figura mostra o ID único Azure VM antes e depois do restauro através do serviço de backup Azure](media/sap-hana-backup-storage-snapshots/image020.png)

Este número mostra o ID único Azure VM antes e depois do restauro através do serviço de backup Azure. A chave de hardware SAP, que é usada para licenciamento SAP, está a utilizar este ID VM único. Como consequência, uma nova licença SAP tem de ser instalada após um restauro vm.

Uma nova funcionalidade de Backup Azure foi apresentada no modo de pré-visualização durante a criação deste guia de backup. Permite uma restauração do nível de ficheiro com base no instantâneo vm que foi tirado para a cópia de segurança VM. Isto evita a necessidade de implementar um novo VM, pelo que o ID VM único permanece o mesmo e não é necessária nova chave de licença SAP HANA. Mais documentação sobre esta funcionalidade será fornecida depois de totalmente testada.

O Azure Backup irá eventualmente permitir a cópia de segurança de discos virtuais Azure individuais, além de ficheiros e diretórios de dentro do VM. Uma grande vantagem da Azure Backup é a sua gestão de todos os backups, poupando o cliente de ter que fazê-lo. Se um restauro se tornar necessário, o Azure Backup selecionará a cópia de segurança correta para utilizar.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>Backup SAP HANA VM através de instantâneo manual de disco

Em vez de utilizar o serviço de backup Azure, pode-se configurar uma solução de backup individual criando imagens blob de VHDs Azure manualmente através do PowerShell. Consulte [A utilização de imagens blob com powerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) para uma descrição dos passos.

Proporciona mais flexibilidade, mas não resolve as questões explicadas anteriormente neste documento:

- Você ainda deve ter certeza de que SAP HANA está em um estado consistente criando um instantâneo SAP HANA
- O disco OS não pode ser substituído mesmo que o VM seja transferido devido a um erro que indique que existe um contrato de arrendamento. Só funciona depois de apagar o VM, o que levaria a um novo ID VM único e à necessidade de instalar uma nova licença SAP.

![É possível restaurar apenas os discos de dados de um VM Azure](media/sap-hana-backup-storage-snapshots/image021.png)

É possível restaurar apenas os discos de dados de um VM Azure, evitando o problema de obter um novo ID VM exclusivo e, portanto, invalidado a licença SAP:

- Para o teste, dois discos de dados Azure foram anexados a um VM e o software RAID foi definido em cima deles 
- Foi confirmado que SAP HANA estava em um estado consistente por SAP HANA instantâneo
- Congelamento do sistema de ficheiros (ver _consistência de dados do SAP HANA ao tirar fotografias_ de armazenamento no artigo relacionado Guia de backup para [SAP HANA em Máquinas Virtuais Azure)](sap-hana-backup-guide.md)
- Fotografias blob foram tiradas de ambos os discos de dados
- Sistema de ficheiros descongela
- Confirmação instantânea SAP HANA
- Para restaurar os discos de dados, o VM foi desligado e ambos os discos desconectados
- Depois de separar os discos, foram substituídos com as fotos de blob antigas
- Em seguida, os discos virtuais restaurados foram novamente ligados ao VM
- Depois de iniciar o VM, tudo no software RAID funcionou bem e foi recuado para o tempo de instantâneo blob
- HANA foi recuado para o instantâneo hana

Se fosse possível desligar o SAP HANA antes das imagens da bolha, o procedimento seria menos complexo. Nesse caso, pode-se saltar a foto do HANA e, se não se passa mais nada no sistema, também pode-se ignorar o congelamento do sistema de ficheiros. A complexidade adicionada surge na imagem quando é necessário fazer instantâneos enquanto tudo está online. Consulte a _consistência dos dados do SAP HANA ao tirar fotografias_ de armazenamento no artigo relacionado Guia de backup para [SAP HANA em Máquinas Virtuais Azure](sap-hana-backup-guide.md).

## <a name="next-steps"></a>Passos seguintes
* [O guia de backup para SAP HANA em Máquinas Virtuais Azure](sap-hana-backup-guide.md) dá uma visão geral e informações sobre o início.
* [A cópia de segurança SAP HANA com base no nível de ficheiro](sap-hana-backup-file-level.md) cobre a opção de cópia de segurança baseada em ficheiros.
* Para aprender a estabelecer alta disponibilidade e plano para a recuperação de desastres do SAP HANA em Azure (grandes instâncias), consulte a alta disponibilidade do [SAP HANA (grandes instâncias) e a recuperação de desastres em Azure.](hana-overview-high-availability-disaster-recovery.md)
