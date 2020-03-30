---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f30518c3bfc9876cbddaf8295ff9e8b667a70200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74014538"
---
## <a name="overview"></a>Descrição geral
O Armazenamento Azure fornece a capacidade de tirar fotos de bolhas. As imagens captam o estado de bolha naquele momento. Neste artigo, descrevemos um cenário em que pode manter cópias de segurança de discos de máquinas virtuais usando instantâneos. Pode utilizar esta metodologia quando optar por não utilizar o Serviço de Backup e Recuperação do Azure e deseja criar uma estratégia de backup personalizada para os seus discos de máquinavirtual.

Os discos de máquinas virtuais Azure são armazenados como bolhas de página no Armazenamento Azure. Uma vez que estamos descrevendo uma estratégia de backup para discos de máquinas virtuais neste artigo, referimo-nos a instantâneos no contexto das bolhas de página. Para saber mais sobre instantâneos, consulte criar [um instantâneo de uma bolha.](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)

## <a name="what-is-a-snapshot"></a>O que é uma foto?
Um instantâneo blob é uma versão apenas de uma bolha que é capturada em um momento de tempo. Uma vez criado um instantâneo, pode ser lido, copiado ou eliminado, mas não modificado. As imagens fornecem uma maneira de apoiar uma bolha como aparece num momento no tempo. Até à versão REST 2015-04-05, tinha a capacidade de copiar fotos completas. Com a versão REST 2015-07-08 e acima, também pode copiar instantâneos incrementais.

## <a name="full-snapshot-copy"></a>Cópia instantânea completa
As imagens podem ser copiadas para outra conta de armazenamento como uma bolha para manter as cópias de segurança da bolha base. Também pode copiar um instantâneo sobre a sua bolha base, que é como restaurar a bolha para uma versão anterior. Quando um instantâneo é copiado de uma conta de armazenamento para outra, ocupa o mesmo espaço que a página base blob. Portanto, copiar instantâneos inteiros de uma conta de armazenamento para outra é lento e consome muito espaço na conta de armazenamento alvo.

> [!NOTE]
> Se copiar a bolha base para outro destino, as imagens da bolha não são copiadas juntamente com ela. Da mesma forma, se substituir uma bolha base com uma cópia, as imagens associadas à bolha base não são afetadas e permanecem intactas sob o nome de bolha base.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Discos de back up usando instantâneos
Como estratégia de backup para os seus discos de máquina virtual, pode tirar fotografias periódicas do disco ou da bolha de página e copiá-las para outra conta de armazenamento utilizando ferramentas como a operação [Copy Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) ou [AzCopy](../articles/storage/common/storage-use-azcopy.md). Pode copiar uma foto para uma página de destino blob com um nome diferente. A página de destino resultante blob é uma bolha de página reempreciável e não um instantâneo. Mais tarde neste artigo, descrevemos passos para tirar cópias de segurança de discos de máquinas virtuais usando instantâneos.

### <a name="restore-disks-using-snapshots"></a>Restaurar discos usando instantâneos
Quando chegar a altura de restaurar o disco para uma versão estável que foi previamente captada numa das imagens de backup, pode copiar um instantâneo sobre a bolha da página base. Após a promoção do instantâneo para a bolha da página base, o instantâneo permanece, mas a sua fonte é substituída com uma cópia que pode ser lida e escrita. Mais tarde neste artigo descrevemos passos para restaurar uma versão anterior do seu disco a partir do seu instantâneo.

### <a name="implementing-full-snapshot-copy"></a>Implementação de cópia instantânea completa
Pode implementar uma cópia instantânea completa fazendo o seguinte,

* Primeiro, tire uma foto da bolha base usando a operação [Snapshot Blob.](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob)
* Em seguida, copie o instantâneo para uma conta de armazenamento de alvo utilizando [copy blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob).
* Repita este processo para manter cópias de reserva da sua bolha base.

## <a name="incremental-snapshot-copy"></a>Cópia incremental de instantâneo
A nova funcionalidade no [API GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) fornece uma forma muito melhor de fazer o backup das imagens das suas páginas ou discos. A API devolve a lista de alterações entre a bolha base e os instantâneos, o que reduz a quantidade de espaço de armazenamento utilizado na conta de backup. A API suporta bolhas de página no Armazenamento Premium, bem como armazenamento padrão. Utilizando esta API, pode construir soluções de backup mais rápidas e eficientes para VMs Azure. Esta API estará disponível com a versão REST 2015-07-08 e superior.

Incremental Snapshot Copy permite-lhe copiar de uma conta de armazenamento para outra a diferença entre,

* Bolha base e seu Snapshot OR
* Qualquer dois instantâneos da bolha base

Desde que sejam satisfeitas as seguintes condições,

* A bolha foi criada em Janeiro-1-2016 ou mais tarde.
* A bolha não foi substituída com [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) ou [Copy Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) entre duas fotos.

**Nota:** Esta funcionalidade está disponível para Premium e Standard Azure Page Blobs.

Quando se tem uma estratégia de backup personalizada usando instantâneos, copiar as fotos de uma conta de armazenamento para outra pode ser lenta e pode consumir muito espaço de armazenamento. Em vez de copiar todo o instantâneo para uma conta de armazenamento de reserva, pode escrever a diferença entre instantâneos consecutivos para uma página de cópia de reserva. Desta forma, o tempo para copiar e o espaço para armazenar backups é substancialmente reduzido.

### <a name="implementing-incremental-snapshot-copy"></a>Implementação de cópia seleléris incremental de instantâneo
Pode implementar cópias de instantâneo incremental fazendo o seguinte,

* Tire uma foto da bolha base utilizando [o Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob).
* Copie o instantâneo para a conta de armazenamento de cópia de segurança do alvo na mesma ou em qualquer outra região do Azure utilizando [copy blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob). Esta é a página de reserva blob. Tire uma foto da página de reserva e guarde-a na conta de reserva.
* Tire outra foto da bolha base usando snapshot Blob.
* Obtenha a diferença entre o primeiro e o segundo instantâneos da bolha base utilizando [o GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges). Utilize o novo parâmetro **prevsnapshot,** para especificar o valor dateTime do instantâneo com o qual pretende obter a diferença. Quando este parâmetro está presente, a resposta REST inclui apenas as páginas que foram alteradas entre o instantâneo do alvo e o instantâneo anterior, incluindo páginas claras.
* Utilize o [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) para aplicar estas alterações na bolha da página de cópia de segurança.
* Por fim, tire uma foto da página de reserva blob e guarde-a na conta de armazenamento de reserva.

Na secção seguinte, descreveremos mais detalhadamente como pode manter cópias de backup de discos utilizando incremental Snapshot Copy

## <a name="scenario"></a>Cenário
Nesta secção, descrevemos um cenário que envolve uma estratégia de backup personalizada para discos de máquinas virtuais usando instantâneos.

Considere um DS-série Azure VM com um disco P30 de armazenamento premium anexado. O disco P30 chamado *mypremiumdisk* é armazenado numa conta de armazenamento premium chamada *mypremiumaccount*. Uma conta de armazenamento padrão chamada *mybackupstdaccount* é usada para armazenar a cópia de segurança do *mypremiumdisk*. Gostaríamos de manter uma foto do *mypremiumdisk* a cada 12 horas.

Para aprender sobre a criação de uma conta de armazenamento, consulte Criar uma conta de [armazenamento.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

Para aprender sobre o backup de VMs Azure, consulte os backups do [Plan Azure VM](../articles/backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Passos para manter as cópias de segurança de um disco utilizando instantâneos incrementais
Os seguintes passos descrevem como tirar fotos do *mypremiumdisk* e manter as cópias de segurança na *conta mybackupstd .* O backup é uma página padrão blob chamada *mybackupstdpageblob*. A bolha da página de backup reflete sempre o mesmo estado que o último instantâneo do *mypremiumdisk*.

1. Crie a bolha da página de backup para o seu disco de armazenamento premium, tirando uma foto do *mypremiumdisk* chamada *mypremiumdisk_ss1*.
2. Copie esta foto para mybackupstdaccount como uma página blob chamada *mybackupstdpageblob*.
3. Tire uma foto do *mybackupstdpageblob* chamado *mybackupstdpageblob_ss1*, usando [snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) e guarde-o na minha conta de *backupstd*.
4. Durante a janela de reserva, crie mais uma foto do *mypremiumdisk,* digamos *mypremiumdisk_ss2*, e guarde-a na *conta mypremium.*
5. Obtenha as alterações incrementais entre os dois instantâneos, *mypremiumdisk_ss2* e *mypremiumdisk_ss1*, utilizando [o GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) na *mypremiumdisk_ss2* com o parâmetro **pré-vsnapshot** definido para o carimbo de tempo de *mypremiumdisk_ss1*. Escreva estas alterações incrementais na página de backup blob *mybackupstdpageblob* na *minha conta de backupstd .* Se existirem intervalos eliminados nas alterações incrementais, devem ser eliminados da bolha da página de cópia de segurança. Utilize o [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) para escrever alterações incrementais na bolha da página de cópia de segurança.
6. Tire uma foto da página de reserva blob *mybackupstdpageblob*, chamada *mybackupstdpageblob_ss2*. Elimine o instantâneo anterior *mypremiumdisk_ss1* da conta de armazenamento premium.
7. Repita os passos 4-6 em cada janela de reserva. Desta forma, pode manter cópias de segurança do *mypremiumdisk* numa conta de armazenamento padrão.

![Disco de back up usando instantâneos incrementais](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Passos para restaurar um disco a partir de instantâneos
Os seguintes passos, descrevam como restaurar o disco premium, *mypremiumdisk* para um instantâneo anterior da conta de armazenamento de backup *mybackupstdaccount*.

1. Identifique o ponto no tempo a que pretende restaurar o disco premium. Digamos que é *instantâneo mybackupstdpageblob_ss2*, que está armazenado na conta de armazenamento de reserva *mybackupstdaccount*.
2. Na minha conta de backupstd, promova o instantâneo *mybackupstdpageblob_ss2* à medida que a nova página de base de backup blob *mybackupstdpageblobrestaurada*.
3. Tire uma foto desta página de reserva restaurada, chamada *mybackupstdpageblobrestored_ss1*.
4. Copie a página restaurada blob *mybackupstdpageblob restaurada* da *mybackupstdaccount* para *mypremiumaccount* como o novo disco *premium mypremiumdisk restaurado*.
5. Tire uma foto do *mypremiumdisk restaurado,* chamado *mypremiumdiskrestored_ss1* para fazer futuras cópias incrementais.
6. Aponte a série DS VM para o disco restaurado *mypremiumdisk restaurado* e retire o *antigo mypremiumdisk* do VM.
7. Inicie o processo de backup descrito na secção anterior para o disco restaurado *mypremiumdisk restaurado,* utilizando o *mybackupstdpageblob restaurado* como a bolha da página de reserva.

![Restaurar o disco a partir de instantâneos](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Passos Seguintes
Use os seguintes links para saber mais sobre a criação de instantâneos de uma bolha e planejando a sua infraestrutura de backup VM.

* [Criando um instantâneo de uma bolha](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Planeje a sua Infraestrutura de Backup VM](../articles/backup/backup-azure-vms-introduction.md)

