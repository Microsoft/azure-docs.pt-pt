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
ms.openlocfilehash: a662598efdca05769c7da9fbeecdf692dccdacb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027570"
---
## <a name="overview"></a>Descrição Geral
O Azure Storage fornece a capacidade de tirar fotos de bolhas. Os instantâneos captam o estado da bolha nessa altura. Neste artigo, descrevemos um cenário em que é possível manter cópias de segurança de discos de máquinas virtuais utilizando instantâneos. Pode utilizar esta metodologia quando optar por não utilizar o Serviço de Backup e Recuperação do Azure e pretende criar uma estratégia de backup personalizada para os seus discos de máquinas virtuais. Para máquinas virtuais que executam cargas de trabalhos ou cargas de trabalho críticas de missão, é recomendado usar [o Azure Backup](../articles/backup/backup-azure-vms-introduction.md) como parte da estratégia de backup.  

Os discos de máquina virtual Azure são armazenados como bolhas de página no Azure Storage. Uma vez que estamos descrevendo uma estratégia de backup para discos de máquinas virtuais neste artigo, referimo-nos a instantâneos no contexto das bolhas de página. Para saber mais sobre instantâneos, consulte a [Criação de um Instantâneo de uma Bolha.](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)

## <a name="what-is-a-snapshot"></a>O que é uma foto?
Um instantâneo blob é uma versão apenas de leitura de uma bolha que é capturada em um ponto do tempo. Uma vez criado um instantâneo, pode ser lido, copiado ou eliminado, mas não modificado. As imagens fornecem uma forma de fazer recuar uma bolha como aparece num momento do tempo. Até à versão REST 2015-04-05, teve a capacidade de copiar fotografias completas. Com a versão REST 2015-07-08 e acima, também pode copiar instantâneos incrementais.

## <a name="full-snapshot-copy"></a>Cópia instantânea completa
As imagens podem ser copiadas para outra conta de armazenamento como uma bolha para manter as cópias de segurança da bolha de base. Também pode copiar uma imagem sobre a sua bolha de base, que é como restaurar a bolha para uma versão anterior. Quando um instantâneo é copiado de uma conta de armazenamento para outra, ocupa o mesmo espaço que a bolha da página base. Portanto, copiar instantâneos inteiros de uma conta de armazenamento para outra é lento e consome muito espaço na conta de armazenamento alvo.

> [!NOTE]
> Se copiar a bolha de base para outro destino, as imagens da bolha não são copiadas juntamente com ela. Da mesma forma, se substituir uma bolha de base com uma cópia, as imagens associadas à bolha de base não são afetadas e permanecem intactas sob o nome da bolha base.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Fazer back-up discos usando instantâneos
Como estratégia de backup para os seus discos de máquinas virtuais, pode tirar fotos periódicas do disco ou da bolha de página e copiá-las para outra conta de armazenamento utilizando ferramentas como a operação [Copy Blob](/rest/api/storageservices/Copy-Blob) ou [AzCopy](../articles/storage/common/storage-use-azcopy-v10.md). Pode copiar uma imagem instantânea para uma mancha de página de destino com um nome diferente. A bolha da página de destino resultante é uma bolha de página escrita e não uma foto instantânea. Mais tarde neste artigo, descrevemos passos para fazer cópias de segurança de discos de máquinas virtuais usando instantâneos.

### <a name="restore-disks-using-snapshots"></a>Restaurar discos usando instantâneos
Quando for a hora de restaurar o seu disco para uma versão estável que foi previamente capturada numa das imagens de backup, pode copiar uma imagem sobre a bolha da página base. Depois do instantâneo ser promovido à página base blob, o instantâneo permanece, mas a sua fonte é substituída com uma cópia que pode ser lida e escrita. Mais tarde neste artigo descrevemos passos para restaurar uma versão anterior do seu disco a partir da sua imagem.

### <a name="implementing-full-snapshot-copy"></a>Implementação de cópia snapshot completa
Pode implementar uma cópia instantânea completa fazendo o seguinte,

* Primeiro, tire uma foto da bolha de base utilizando a operação [Snapshot Blob.](/rest/api/storageservices/Snapshot-Blob)
* Em seguida, copie o instantâneo para uma conta de armazenamento alvo utilizando [copy Blob](/rest/api/storageservices/Copy-Blob).
* Repita este processo para manter cópias de reserva da sua bolha de base.

## <a name="incremental-snapshot-copy"></a>Cópia instantânea incremental
A nova funcionalidade na API [GetPageRanges](/rest/api/storageservices/Get-Page-Ranges) fornece uma forma muito melhor de fazer o back-up das imagens das suas páginas blobs ou discos. A API devolve a lista de alterações entre a bolha de base e as imagens, o que reduz a quantidade de espaço de armazenamento utilizado na conta de backup. A API suporta bolhas de página no Armazenamento Premium, bem como o Armazenamento Padrão. Utilizando esta API, pode construir soluções de backup mais rápidas e eficientes para os VMs Azure. Esta API estará disponível com a versão REST 2015-07-08 e superior.

Cópia Snapshot incremental permite-lhe copiar de uma conta de armazenamento para outra a diferença entre,

* Bolha de base e seu Snapshot OR
* Qualquer dois instantâneos da bolha da base

Desde que sejam satisfeitas as seguintes condições,

* A bolha foi criada em Jan-1-2016 ou mais tarde.
* A bolha não foi substituída com [PutPage](/rest/api/storageservices/Put-Page) ou [Copy Blob](/rest/api/storageservices/Copy-Blob) entre duas imagens.

>[!NOTE]
>Esta funcionalidade está disponível para Premium e Standard Azure Page Blobs.

Quando você tem uma estratégia de backup personalizada usando instantâneos, copiar os instantâneos de uma conta de armazenamento para outra pode ser lento e pode consumir muito espaço de armazenamento. Em vez de copiar todo o instantâneo para uma conta de armazenamento de reserva, pode escrever a diferença entre instantâneos consecutivos para uma bolha de página de reserva. Desta forma, o tempo de copiar e o espaço para armazenar backups é substancialmente reduzido.

### <a name="implementing-incremental-snapshot-copy"></a>Implementação de cópia snapshot incremental
Pode implementar uma cópia instantânea incremental fazendo o seguinte,

* Tire uma foto da bolha de base usando [snapshot Blob](/rest/api/storageservices/Snapshot-Blob).
* Copie o instantâneo para a conta de armazenamento de backup do alvo na mesma ou em qualquer outra região de Azure utilizando [a Copy Blob](/rest/api/storageservices/Copy-Blob). Esta é a bolha da página de reserva. Tire uma foto da bolha da página de reserva e guarde-a na conta de reserva.
* Tire outra foto da bolha de base usando snapshot Blob.
* Obtenha a diferença entre a primeira e a segunda imagens da bolha de base utilizando [GetPageRanges](/rest/api/storageservices/Get-Page-Ranges). Utilize o novo **prevsnapshot do** parâmetro , para especificar o valor dataTime do instantâneo com o qual pretende obter a diferença. Quando este parâmetro está presente, a resposta REST inclui apenas as páginas que foram alteradas entre o instantâneo do alvo e o instantâneo anterior, incluindo páginas claras.
* Utilize [o PutPage](/rest/api/storageservices/Put-Page) para aplicar estas alterações na bolha da página de reserva.
* Por fim, tire uma foto da bolha da página de reserva e guarde-a na conta de armazenamento de cópias de segurança.

Na secção seguinte, descreveremos com mais pormenor como pode manter cópias de segurança dos discos utilizando cópia snapshot incremental

## <a name="scenario"></a>Scenario
Nesta secção, descrevemos um cenário que envolve uma estratégia de backup personalizada para discos de máquinas virtuais usando instantâneos.

Considere um Azure VM da série DS com um disco P30 de armazenamento premium ligado. O disco P30 chamado *mypremiumdisk* está guardado numa conta de armazenamento premium chamada *mypremiumaccount*. Uma conta de armazenamento padrão chamada *mybackupstdaccount* é usada para armazenar a cópia de segurança do *mypremiumdisk*. Gostaríamos de ter uma foto do *mypremiumdisk* a cada 12 horas.

Para saber mais sobre a criação de uma conta de armazenamento, consulte [Criar uma conta de armazenamento.](../articles/storage/common/storage-account-create.md)

Para saber mais sobre o backup dos VMs Azure, consulte as [cópias de segurança Plan Azure VM](../articles/backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Passos para manter cópias de segurança de um disco usando instantâneos incrementais
Os seguintes passos descrevem como tirar fotos do *mypremiumdisk* e manter os backups na *contagem de mybackupstdac.* A cópia de segurança é uma bolha de página padrão chamada *mybackupstdpageblob*. A bolha da página de reserva reflete sempre o mesmo estado que a última foto do *mypremiumdisk*.

1. Crie a bolha de página de reserva para o seu disco de armazenamento premium, tirando uma foto do *mypremiumdisk* chamada *mypremiumdisk_ss1*.
2. Copie esta foto para o mybackupstdaccount como uma bolha de página chamada *mybackupstdpageblob*.
3. Tire uma foto do *mybackupstdpageblob* chamado *mybackupstdpageblob_ss1,* usando [snapshot Blob](/rest/api/storageservices/Snapshot-Blob) e guarde-o no *mybackupstdaccount*.
4. Durante a janela de reserva, crie outra imagem do *mypremiumdisk*, digamos *mypremiumdisk_ss2*, e guarde-a na *conta mypremiumaccount*.
5. Obtenha as alterações incrementais entre os dois instantâneos, *mypremiumdisk_ss2* e *mypremiumdisk_ss1*, utilizando [o GetPageRanges](/rest/api/storageservices/Get-Page-Ranges) no *mypremiumdisk_ss2* com o parâmetro **pré-navegação** definido para o tempotando de *mypremiumdisk_ss1*. Escreva estas alterações incrementais na página de backup blob *mybackupstdpageblob* no *mybackupstdaccount*. Se houver intervalos eliminados nas alterações incrementais, devem ser limpos a partir da bolha da página de reserva. Utilize [o PutPage](/rest/api/storageservices/Put-Page) para escrever alterações incrementais na bolha da página de reserva.
6. Tire uma foto da página de backup blob *mybackupstdpageblob*, chamada *mybackupstdpageblob_ss2*. Elimine o instantâneo anterior *mypremiumdisk_ss1* da conta de armazenamento premium.
7. Repita os passos 4-6 em todas as janelas de reserva. Desta forma, pode manter cópias de segurança do *mypremiumdisk* numa conta de armazenamento padrão.

![Fazer o disco de re-up usando instantâneos incrementais](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Passos para restaurar um disco a partir de instantâneos
Os seguintes passos, descreva como restaurar o disco premium, *mypremiumdisk* para uma foto anterior da conta de armazenamento de backup *mybackupstdaccount*.

1. Identifique o ponto no tempo a que deseja restaurar o disco premium. Digamos que é instantâneo *mybackupstdpageblob_ss2,* que está guardado na conta de armazenamento de backup *mybackupstdaccount*.
2. Na contagem de mybackupstdaccount, promova o instantâneo *mybackupstdpageblob_ss2* como a nova página de base de backup blob *mybackupstdpageblobrestored*.
3. Tire uma foto desta bolha de página de reserva restaurada, chamada *mybackupstdpageblobrestored_ss1*.
4. Copie a página restaurada blob *mybackupstdpageblobrestored* from *mybackupstdaccount* to *mypremiumaccount* as the new premium disk *mypremiumdiskrestored*.
5. Tire uma foto do *mypremiumdiskrestored,* chamado *mypremiumdiskrestored_ss1* para fazer futuras cópias de segurança incrementais.
6. Aponte a série DS VM para o disco restaurado *mypremiumdiskrestored* e despremiumdisk do VM. 
7. Inicie o processo de Cópia de Segurança descrito na secção anterior para o disco restaurado *mypremiumdiskrestored*, utilizando o *mybackupstdpageblobrestored* como a bolha da página de reserva.

![Restaurar o disco a partir de instantâneos](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Passos Seguintes
Use os seguintes links para saber mais sobre a criação de instantâneos de uma bolha e planejar a sua infraestrutura de backup VM.

* [Criando um instantâneo de uma bolha](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Planeie a sua infraestrutura de backup VM](../articles/backup/backup-azure-vms-introduction.md)