---
title: SAP HANA o backup do Azure com base em instantâneos de armazenamento | Microsoft Docs
description: Há duas grandes possibilidades de backup para SAP HANA em máquinas virtuais do Azure, este artigo aborda SAP HANA backup com base em instantâneos de armazenamento
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
ms.openlocfilehash: 8bcfdefa2ea9de12ca6029839a41c91111a5c61c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078603"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>Cópia de segurança SAP HANA baseada em instantâneos de armazenamento

## <a name="introduction"></a>Introdução

Isso faz parte de uma série de três partes de artigos relacionados em SAP HANA backup. O [Guia de backup para SAP Hana em máquinas virtuais do Azure](sap-hana-backup-guide.md) fornece uma visão geral e informações sobre como começar, e [SAP Hana backup do Azure em nível de arquivo](sap-hana-backup-file-level.md) abrange a opção de backup baseado em arquivo.

Ao usar um recurso de backup de VM para um sistema de demonstração All-in-One de instância única, é necessário considerar fazer um backup de VM em vez de gerenciar os backups do HANA no nível do sistema operacional. Uma alternativa é pegar instantâneos de blob do Azure para criar cópias de discos virtuais individuais, que são anexados a uma máquina virtual, e manter os arquivos de dados do HANA. Mas um ponto crítico é a consistência do aplicativo ao criar um instantâneo de backup ou disco da VM enquanto o sistema está em execução. Consulte _SAP Hana consistência de dados ao_ obter instantâneos de armazenamento no artigo relacionado [Guia de backup para SAP Hana em máquinas virtuais do Azure](sap-hana-backup-guide.md). SAP HANA tem um recurso que dá suporte a esses tipos de instantâneos de armazenamento.

## <a name="sap-hana-snapshots-as-central-part-of-application-consistent-backups"></a>SAP HANA instantâneos como parte central dos backups consistentes do aplicativo

Há uma funcionalidade no SAP HANA que dá suporte ao fazer um instantâneo de armazenamento. Há uma restrição para sistemas de contêiner único. Os cenários que são SAP HANA MCS com mais de um locatário não dão suporte a esse tipo de instantâneo de banco de dados SAP HANA (consulte [criar um instantâneo de armazenamento (SAP Hana Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

Ele funciona da seguinte maneira:

- Preparar-se para um instantâneo de armazenamento iniciando o instantâneo de SAP HANA
- Executar o instantâneo de armazenamento (instantâneo de blob do Azure, por exemplo)
- Confirmar o instantâneo de SAP HANA

![Esta captura de tela mostra que um instantâneo de dados de SAP HANA pode ser criado por meio de uma instrução SQL](media/sap-hana-backup-storage-snapshots/image011.png)

Esta captura de tela mostra que um instantâneo de dados de SAP HANA pode ser criado por meio de uma instrução SQL.

![O instantâneo, em seguida, também aparece no catálogo de backup no SAP HANA Studio](media/sap-hana-backup-storage-snapshots/image012.png)

O instantâneo, em seguida, também aparece no catálogo de backup no SAP HANA Studio.

![No disco, o instantâneo aparece no diretório de dados do SAP HANA](media/sap-hana-backup-storage-snapshots/image013.png)

No disco, o instantâneo aparece no SAP HANA diretório de dados.

Uma delas deve garantir que a consistência do sistema de arquivos também seja garantida antes de executar o instantâneo de armazenamento enquanto SAP HANA está no modo de preparação do instantâneo. Consulte _SAP Hana consistência de dados ao_ obter instantâneos de armazenamento no artigo relacionado [Guia de backup para SAP Hana em máquinas virtuais do Azure](sap-hana-backup-guide.md).

Quando o instantâneo de armazenamento é concluído, é essencial confirmar o instantâneo de SAP HANA. Há uma instrução SQL correspondente a ser executada: BACKUP DATA CLOSE SNAPSHOT (consulte [instrução backup data Close snapshot (backup e recuperação)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm)).

> [!IMPORTANT]
> Confirme o instantâneo do HANA. Devido a &quot;cópia em gravação,&quot; SAP Hana pode exigir espaço em disco adicional no modo de preparação de instantâneo e não é possível iniciar novos backups até que o instantâneo de SAP Hana seja confirmado.

## <a name="hana-vm-backup-via-azure-backup-service"></a>Backup de VM do HANA por meio do serviço de backup do Azure

O agente de backup do serviço de backup do Azure não está disponível para VMs do Linux. Além disso, o Linux não tem funcionalidade semelhante à do Windows com o VSS.  Para fazer uso do backup do Azure no nível de arquivo/diretório, é possível copiar SAP HANA arquivos de backup para uma VM do Windows e, em seguida, usar o agente de backup. 

Caso contrário, apenas um backup completo da VM do Linux é possível por meio do serviço de backup do Azure. Consulte [visão geral dos recursos no backup do Azure](../../../backup/backup-introduction-to-azure-backup.md) para saber mais.

O serviço de backup do Azure oferece uma opção para fazer backup e restaurar uma VM. Mais informações sobre esse serviço e como ele funciona podem ser encontradas no artigo [planejar sua infraestrutura de backup de VM no Azure](../../../backup/backup-azure-vms-introduction.md).

Há duas considerações importantes de acordo com esse artigo:

_&quot;Para máquinas virtuais do Linux, somente backups consistentes com arquivos são possíveis, pois o Linux não tem uma plataforma equivalente ao VSS.&quot;_

_&quot;Os aplicativos precisam implementar seu próprio &quot;&quot; mecanismo de correção nos dados restaurados.&quot;_

Portanto, é possível ter certeza de que SAP HANA está em um estado consistente no disco quando o backup é iniciado. Consulte _SAP Hana instantâneos_ descritos anteriormente no documento. Mas há um problema em potencial quando SAP HANA permanece nesse modo de preparação de instantâneo. Consulte [criar um instantâneo de armazenamento (SAP Hana Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) para obter mais informações.

Esse artigo diz:

_&quot;É altamente recomendável confirmar ou abandonar um instantâneo de armazenamento assim que possível depois que ele tiver sido criado. Enquanto o instantâneo de armazenamento está sendo preparado ou criado, os dados relevantes do instantâneo são congelados. Embora os dados relevantes do instantâneo permaneçam congelados, as alterações ainda podem ser feitas no banco de dado. Essas alterações não farão com que os dados congelados do instantâneo sejam alterados. Em vez disso, as alterações são gravadas em posições na área de dados que são separadas do instantâneo de armazenamento. As alterações também são gravadas no log. No entanto, quanto mais tempo os dados relevantes de instantâneo são mantidos congelados, mais o volume de dados pode crescer.&quot;_

O backup do Azure cuida da consistência do sistema de arquivos por meio das extensões de VM do Azure. Essas extensões não estão disponíveis no autônomo e só funcionam em combinação com o serviço de backup do Azure. No entanto, ainda é um requisito para fornecer scripts para criar e excluir um instantâneo de SAP HANA para garantir a consistência do aplicativo.

O backup do Azure tem quatro fases principais:

- Executar script de preparação-o script precisa criar um instantâneo de SAP HANA
- Tirar instantâneo
- Executar script de pós-instantâneo-o script precisa excluir o SAP HANA criado pelo script de preparação
- Transferir dados para o cofre

Para obter detalhes sobre onde copiar esses scripts e detalhes sobre como o backup do Azure funciona exatamente, verifique os seguintes artigos:

- [Planear a sua infraestrutura de cópias de segurança de VMs no Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Backup consistente com o aplicativo de VMs Linux do Azure](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)



Neste momento, a Microsoft não publicou scripts de preparação e de pós-instantâneo para SAP HANA. Você, como cliente ou integrador de sistema, precisaria criar esses scripts e configurar o procedimento com base na documentação mencionada acima.


## <a name="restore-from-application-consistent-backup-against-a-vm"></a>Restaurar do backup consistente do aplicativo em uma VM
O processo de restauração de um backup consistente de aplicativo feito pelo backup do Azure é documentado no artigo [recuperar arquivos do backup de máquina virtual do Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm). 

> [!IMPORTANT]
> No artigo [recuperar arquivos do backup de máquina virtual do Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) é uma lista de exceções e etapas listadas ao usar conjuntos de distribuição de disco. Discos distribuídos são provavelmente a configuração de VM regular para SAP HANA. Portanto, é essencial ler o artigo e testar o processo de restauração para tais casos, conforme listado no artigo. 



## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>Chave de licença do HANA e restauração de VM por meio do serviço de backup do Azure

O serviço de backup do Azure foi projetado para criar uma nova VM durante a restauração. Agora não há um plano para fazer uma &quot;restauração in-loco&quot; de uma VM do Azure existente.

![Esta figura mostra a opção de restauração do serviço do Azure no portal do Azure](media/sap-hana-backup-storage-snapshots/image019.png)

Esta figura mostra a opção de restauração do serviço do Azure no portal do Azure. É possível escolher entre a criação de uma VM durante a restauração ou a restauração dos discos. Depois de restaurar os discos, ainda é necessário criar uma nova VM sobre ele. Sempre que uma nova VM é criada no Azure, a ID exclusiva da VM é alterada (consulte Acessando [e usando a ID exclusiva da VM do Azure](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![Esta figura mostra a ID exclusiva da VM do Azure antes e depois da restauração por meio do serviço de backup do Azure](media/sap-hana-backup-storage-snapshots/image020.png)

Esta figura mostra a ID exclusiva da VM do Azure antes e depois da restauração por meio do serviço de backup do Azure. A chave de hardware do SAP, que é usada para licenciamento SAP, está usando essa ID de VM exclusiva. Como consequência, uma nova licença SAP deve ser instalada após uma restauração de VM.

Um novo recurso de backup do Azure foi apresentado no modo de visualização durante a criação deste guia de backup. Ele permite uma restauração no nível do arquivo com base no instantâneo da VM que foi obtido para o backup da VM. Isso evita a necessidade de implantar uma nova VM e, portanto, a ID de VM exclusiva permanece a mesma e nenhuma nova chave de licença de SAP HANA é necessária. Mais documentação sobre esse recurso será fornecida depois que ele for totalmente testado.

O backup do Azure eventualmente permitirá o backup de discos virtuais individuais do Azure, além de arquivos e diretórios de dentro da VM. Uma grande vantagem do backup do Azure é o gerenciamento de todos os backups, evitando que o cliente precise fazê-lo. Se uma restauração for necessária, o backup do Azure selecionará o backup correto a ser usado.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>Backup de VM SAP HANA por meio de instantâneo de disco manual

Em vez de usar o serviço de backup do Azure, é possível configurar uma solução de backup individual criando instantâneos de blob de VHDs do Azure manualmente por meio do PowerShell. Consulte [usando instantâneos de blob com o PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) para obter uma descrição das etapas.

Ele fornece mais flexibilidade, mas não resolve os problemas explicados anteriormente neste documento:

- Você ainda deve verificar se SAP HANA está em um estado consistente criando um instantâneo de SAP HANA
- O disco do sistema operacional não pode ser substituído mesmo que a VM seja desalocada devido a um erro informando que existe uma concessão. Ele só funciona depois de excluir a VM, o que levaria a uma nova ID de VM exclusiva e a necessidade de instalar uma nova licença SAP.

![É possível restaurar apenas os discos de dados de uma VM do Azure](media/sap-hana-backup-storage-snapshots/image021.png)

É possível restaurar apenas os discos de dados de uma VM do Azure, evitando o problema de obter uma nova ID de VM exclusiva e, portanto, a licença SAP invalidada:

- Para o teste, dois discos de dados do Azure foram anexados a uma VM e RAID de software foi definido em cima deles 
- Foi confirmado que SAP HANA estava em um estado consistente por SAP HANA recurso de instantâneo
- Congelamento do sistema de arquivos (consulte _SAP Hana consistência de dados ao_ obter instantâneos de armazenamento no artigo relacionado [Guia de backup para SAP Hana em máquinas virtuais do Azure](sap-hana-backup-guide.md))
- Instantâneos de blob foram tirados de ambos os discos de dados
- Descongelar sistema de arquivos
- Confirmação de instantâneo de SAP HANA
- Para restaurar os discos de dados, a VM foi desligada e ambos os discos desanexados
- Depois de desanexar os discos, eles foram substituídos pelos instantâneos de blob anteriores
- Em seguida, os discos virtuais restaurados foram anexados novamente à VM
- Depois de iniciar a VM, tudo no software RAID funcionou bem e foi definido de volta para o tempo de instantâneo de BLOB
- O HANA foi definido de volta para o instantâneo do HANA

Se fosse possível desligar SAP HANA antes dos instantâneos de BLOB, o procedimento seria menos complexo. Nesse caso, é possível ignorar o instantâneo do HANA e, se nada mais estiver acontecendo no sistema, também ignorar o congelamento do sistema de arquivos. A complexidade adicional entra em cena quando é necessário fazer instantâneos enquanto tudo está online. Consulte _SAP Hana consistência de dados ao_ obter instantâneos de armazenamento no artigo relacionado [Guia de backup para SAP Hana em máquinas virtuais do Azure](sap-hana-backup-guide.md).

## <a name="next-steps"></a>Passos Seguintes
* O [Guia de backup para SAP Hana em máquinas virtuais do Azure](sap-hana-backup-guide.md) fornece uma visão geral e informações sobre como começar.
* [SAP Hana backup com base no nível de arquivo](sap-hana-backup-file-level.md) abrange a opção de backup baseado em arquivo.
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA no Azure (instâncias grandes), consulte [alta disponibilidade e recuperação de desastre do SAP Hana (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md).
