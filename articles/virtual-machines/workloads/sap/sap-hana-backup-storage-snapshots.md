---
title: Cópia de segurança do Azure do SAP HANA com base nos instantâneos de armazenamento | Documentos da Microsoft
description: Existem duas principais cópia de segurança possibilidades para o SAP HANA em máquinas virtuais do Azure, este artigo fala sobre a cópia de segurança do SAP HANA com base nos instantâneos de armazenamento
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
ms.openlocfilehash: 74f47344afff630a8633b340ea4ce21db28db7ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60936606"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>Cópia de segurança SAP HANA baseada em instantâneos de armazenamento

## <a name="introduction"></a>Introdução

Esta é a parte de uma série de três partes de artigos relacionados na cópia de segurança do SAP HANA. [Guia de segurança para SAP HANA em Azure Virtual Machines](sap-hana-backup-guide.md) fornece uma descrição geral e informações sobre como começar, e [SAP HANA Azure Backup no nível de arquivo](sap-hana-backup-file-level.md) abrange a opção de cópia de segurança baseados em ficheiros.

Quando utilizar uma funcionalidade de cópia de segurança de VM para um sistema de demonstração de tudo-em-um de instância única, uma deve optar por fazer uma cópia de segurança VM em vez de gerir cópias de segurança do HANA ao nível do SO. Uma alternativa é criar instantâneos de Blobs do Azure para criar cópias de discos virtuais individuais, que estão anexados a uma máquina virtual, e manter os ficheiros de dados do HANA. Mas um ponto crítico é a consistência de aplicação quando criar um instantâneo de cópia de segurança ou de disco da VM, enquanto o sistema está a funcionar e em execução. Ver _consistência de dados do SAP HANA ao criar instantâneos de armazenamento_ no artigo relacionado [guia de cópia de segurança para SAP HANA em Azure Virtual Machines](sap-hana-backup-guide.md). SAP HANA tem uma funcionalidade que suporta estes tipos de instantâneos de armazenamento.

## <a name="sap-hana-snapshots-as-central-part-of-application-consistent-backups"></a>Instantâneos de SAP HANA como uma parte central de cópias de segurança de aplicação

Há uma funcionalidade no SAP HANA que suporte um instantâneo de armazenamento. Há uma restrição para sistemas de contentor único. Cenários de brilhar SAP HANA MCS com mais do que um inquilino não suportam este tipo de instantâneo de base de dados do SAP HANA (consulte [criar um instantâneo de armazenamento (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

Ele funciona da seguinte forma:

- Preparar para um instantâneo de armazenamento, iniciando o instantâneo do SAP HANA
- Executar o instantâneo de armazenamento (por exemplo, o instantâneo de blob do Azure)
- Confirmar o instantâneo do SAP HANA

![Nesta captura de ecrã mostra que um instantâneo de dados do SAP HANA pode ser criado por meio de uma instrução SQL](media/sap-hana-backup-storage-snapshots/image011.png)

Nesta captura de ecrã mostra que um instantâneo de dados do SAP HANA pode ser criado por meio de uma instrução SQL.

![O instantâneo, em seguida, também é apresentado no catálogo de cópia de segurança no SAP HANA Studio](media/sap-hana-backup-storage-snapshots/image012.png)

O instantâneo, em seguida, também é apresentado no catálogo de cópia de segurança no SAP HANA Studio.

![No disco, o instantâneo aparece no diretório de dados SAP HANA](media/sap-hana-backup-storage-snapshots/image013.png)

No disco, o instantâneo aparece no diretório de dados SAP HANA.

Um tem para se certificar de que a consistência do sistema de ficheiros também é garantida antes de executar o instantâneo de armazenamento, enquanto o SAP HANA está no modo de preparação de instantâneo. Ver _consistência de dados do SAP HANA ao criar instantâneos de armazenamento_ no artigo relacionado [guia de cópia de segurança para SAP HANA em Azure Virtual Machines](sap-hana-backup-guide.md).

Depois de fazer o instantâneo de armazenamento, é fundamental para confirmar o instantâneo do SAP HANA. Há uma instrução de SQL correspondente para executar: INSTANTÂNEO de FECHAR de dados de cópia de segurança (veja [dados FECHAR INSTANTÂNEO instrução BACKUP (cópia de segurança e recuperação)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm)).

> [!IMPORTANT]
> Certifique-se o instantâneo do HANA. Devido a &quot;cópia ao gravar,&quot; SAP HANA podem exigir espaço em disco adicional no instantâneo-preparar modo, e não é possível iniciar novas cópias de segurança até que o instantâneo do SAP HANA foi confirmado.

## <a name="hana-vm-backup-via-azure-backup-service"></a>Cópia de segurança de VM do HANA através do serviço de cópia de segurança do Azure

O agente de cópia de segurança do serviço de cópia de segurança do Azure não está disponível para VMs do Linux. Além disso Linux não tem uma funcionalidade semelhante como o Windows tem com VSS.  Para utilizar o Azure backup no nível do diretório do ficheiro, um seria copiar ficheiros de cópia de segurança do SAP HANA para uma VM do Windows e, em seguida, utilize o agente de cópia de segurança. 

Caso contrário, apenas um backup completo de VM do Linux é possível por meio do serviço de cópia de segurança do Azure. Ver [descrição geral dos recursos do Azure Backup](../../../backup/backup-introduction-to-azure-backup.md) para obter mais informações.

O serviço de cópia de segurança do Azure oferece uma opção para criar cópias de segurança e restaurar uma VM. Podem encontrar mais informações sobre este serviço e como ele funciona no artigo [planear a sua infraestrutura de cópia de segurança de VM no Azure](../../../backup/backup-azure-vms-introduction.md).

Existem duas considerações importantes, de acordo com esse artigo:

_&quot;Para máquinas virtuais do Linux, apenas as cópias de segurança consistente com ficheiros são possíveis, uma vez que o Linux não tem uma plataforma equivalente para VSS.&quot;_

_&quot;Os aplicativos precisam implementar seus próprios &quot;correção&quot; mecanismo sobre os dados restaurados.&quot;_

Por conseguinte, um tem para se certificar de que o SAP HANA está num estado consistente no disco quando a cópia de segurança é iniciado. Ver _instantâneos do SAP HANA_ descrito anteriormente no documento. Mas há um problema em potencial ao SAP HANA permanece neste modo de preparação do instantâneo. Ver [criar um instantâneo de armazenamento (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) para obter mais informações.

Esse artigo Estados:

_&quot;É vivamente recomendado para confirmar ou abandonar um instantâneo de armazenamento logo que possível após ter sido criado. Embora o instantâneo de armazenamento está a ser preparado ou criado, os dados relevantes para o instantâneo são congelados. Enquanto os dados relevantes para o instantâneo permanecem congelados, podem ainda ser feitas alterações na base de dados. Essas alterações não fará com que os dados relevantes para o instantâneo congelados para serem alterados. Em vez disso, as alterações são escritas posições na área de dados que estão separadas a partir do instantâneo de armazenamento. As alterações também são escritas no registo. No entanto, quanto mais tempo os dados relevantes para o instantâneo são mantidos congelados, quanto mais o volume de dados pode crescer.&quot;_

Cópia de segurança do Azure se encarrega de consistência de sistema de ficheiros através de extensões de VM do Azure. Estas extensões não são autónomas disponíveis e funcionam apenas em combinação com o serviço de cópia de segurança do Azure. No entanto, ainda é um requisito para fornecer os scripts para criar e eliminar um instantâneo do SAP HANA para garantir a consistência de aplicação.

Cópia de segurança do Azure tem quatro fases principais:

- Executar preparar script - script tem de criar um instantâneo do SAP HANA
- Tirar instantâneo
- Executar o script posterior ao instantâneo - o script tem de eliminar o SAP HANA criada pelo script de preparação
- Transferência de dados para o Cofre

Para obter detalhes sobre onde pretende copiar estes scripts e detalhes sobre como o Azure Backup funciona exatamente, consulte os artigos seguintes:

- [Planear a sua infraestrutura de cópias de segurança de VMs no Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Cópias de segurança consistentes de VMs do Linux do Azure](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)



No momento, a Microsoft não publicou preparar scripts e scripts de pós-instantâneos para o SAP HANA. Como o integrador de cliente ou sistema seria necessário criar esses scripts e configurar o procedimento com base na documentação do mencionado acima.


## <a name="restore-from-application-consistent-backup-against-a-vm"></a>Restaurar a partir de cópia de segurança consistente do aplicativo em relação a uma VM
O processo de restauração de um aplicativo consistente cópia de segurança criada pela cópia de segurança do Azure está documentado no artigo [recuperar ficheiros a partir de cópia de segurança da máquina virtual do Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm). 

> [!IMPORTANT]
> No artigo [recuperar ficheiros a partir de cópia de segurança da máquina virtual do Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) é uma lista de exceções e os passos apresentados ao utilizar conjuntos de faixa de disco. Os discos repartidos são, provavelmente, a configuração de VM regular para SAP HANA. Por conseguinte, é essencial para ler o artigo e testar o processo de restauração para tais casos, tal como indicado no artigo. 



## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>Restaurar a chave de licença do HANA e a VM através do serviço de cópia de segurança do Azure

O serviço de cópia de segurança do Azure foi concebido para criar uma nova VM durante o restauro. Não há nenhum plano neste momento para fazer uma &quot;in-loco&quot; restauro de uma VM do Azure existente.

![Esta figura mostra a opção de restauro do serviço do Azure no portal do Azure](media/sap-hana-backup-storage-snapshots/image019.png)

Esta figura mostra a opção de restauro do serviço do Azure no portal do Azure. Um pode escolher entre criar uma VM durante o restauro ou ao restaurar os discos. Depois de restaurar os discos, ainda é necessário criar uma nova VM com base no mesmo. Sempre que uma nova VM é criada no Azure a exclusivo ID da VM for alterado (consulte [acessando e utilizar o ID exclusivo da VM do Azure](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![Esta figura mostra o ID exclusivo da VM do Azure antes e após o restauro através do serviço de cópia de segurança do Azure](media/sap-hana-backup-storage-snapshots/image020.png)

Esta figura mostra o ID exclusivo da VM do Azure antes e após o restauro através do serviço de cópia de segurança do Azure. A chave de hardware do SAP, o que é utilizada para SAP, licenciamento, está a utilizar este ID exclusivo de VM. Como conseqüência, uma nova licença do SAP tem de ser instalados depois de restaurar uma VM.

Uma nova funcionalidade de cópia de segurança do Azure foi apresentada no modo de pré-visualização durante a criação deste guia de cópia de segurança. Ele permite que um restauro ao nível do ficheiro com base no instantâneo VM que era utilizado para a VM cópia de segurança. Isso evita a necessidade de implementar uma nova VM e, portanto, o ID exclusivo da VM permanece o mesmo e nenhuma nova chave de licença do SAP HANA é necessária. Mais documentação sobre esta funcionalidade será fornecida depois foi totalmente testada.

Cópia de segurança do Azure, por fim, irá permitir cópia de segurança de discos virtuais do Azure individuais, além de arquivos e diretórios a partir de dentro da VM. Das principais vantagens do Azure Backup é o gerenciamento de todas as cópias de segurança, poupando o cliente de ter de fazê-lo. Se um restauro torna-se necessário, cópia de segurança do Azure irá selecionar a cópia de segurança correta a utilizar.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>Cópia de segurança do SAP HANA VM através de instantâneos de disco manual

Em vez de utilizar o serviço de cópia de segurança do Azure, um foi possível configurar uma solução de cópia de segurança individual através da criação de instantâneos de blob de VHDs do Azure manualmente através do PowerShell. Ver [utilizar instantâneos de blob com o PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) para obter uma descrição das etapas.

Ele oferece mais flexibilidade, mas não resolve os problemas explicados anteriormente neste documento:

- Ainda tem de confirmar que o SAP HANA está num estado consistente com a criação de um instantâneo do SAP HANA
- Não é possível substituir o disco do SO, mesmo que a VM é desalocada devido a um erro a indicar que existe uma concessão. Ele só funciona depois de eliminar a VM, o que poderia levar a um novo ID exclusivo do VM e a necessidade de instalar uma nova licença do SAP.

![É possível restaurar os discos de dados de uma VM do Azure](media/sap-hana-backup-storage-snapshots/image021.png)

É possível restaurar os discos de dados de uma VM do Azure, evitando o problema de obter um novo ID exclusivo do VM e, invalidada por conseguinte, a licença do SAP:

- Para o teste, dois discos de dados do Azure foram anexados a uma VM e RAID de software foi definida interessado na tecnologia 
- Foi confirmado que o SAP HANA estava num estado consistente pela funcionalidade de instantâneo do SAP HANA
- Congelar do sistema de arquivos (consulte _consistência de dados do SAP HANA ao criar instantâneos de armazenamento_ no artigo relacionado [guia de cópia de segurança para SAP HANA em Azure Virtual Machines](sap-hana-backup-guide.md))
- Instantâneos de blob foram tirados de ambos os discos de dados
- Libertar de sistema de ficheiros
- Confirmação de instantâneo do SAP HANA
- Para restaurar os discos de dados, a VM foi encerrada e os dois discos desligados
- Depois de desanexar discos, eles foram substituídos com os instantâneos de blob antigo
- Em seguida, os discos virtuais restaurados foram anexados novamente para a VM
- Depois de iniciar a VM, tudo sobre o software de RAID funcionou bem e foi redefinida com a hora do instantâneo de blob
- HANA foi definido de volta para o instantâneo do HANA

Caso tenha sido possível encerrar o SAP HANA antes dos instantâneos de blob, o procedimento seria menos complexo. Nesse caso, um pode ignorar o instantâneo do HANA e, se nada mais está acontecendo no sistema, também ignorar o congelamento de sistema de ficheiros. Complexidade adicional entra em cena quando é necessário fazer instantâneos, apesar de tudo o que está online. Ver _consistência de dados do SAP HANA ao criar instantâneos de armazenamento_ no artigo relacionado [guia de cópia de segurança para SAP HANA em Azure Virtual Machines](sap-hana-backup-guide.md).

## <a name="next-steps"></a>Passos Seguintes
* [Guia de segurança para SAP HANA em Azure Virtual Machines](sap-hana-backup-guide.md) fornece uma descrição geral e informações sobre como começar.
* [Cópia de segurança do SAP HANA com base no nível de ficheiro](sap-hana-backup-file-level.md) abrange a opção de cópia de segurança baseados em ficheiros.
* Para saber como estabelecer a elevada disponibilidade e o plano de recuperação após desastre do SAP HANA no Azure (instâncias grandes), veja [SAP HANA (instâncias grandes) elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md).
