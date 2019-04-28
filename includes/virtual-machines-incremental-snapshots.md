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
ms.openlocfilehash: 06e6e491fa1e9a047527efb78149855b125771ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543813"
---
# <a name="back-up-azure-unmanaged-vm-disks-with-incremental-snapshots"></a>Criar cópias de segurança do Azure discos VM não geridos com instantâneos incrementais
## <a name="overview"></a>Descrição geral
O armazenamento do Azure fornece a capacidade para tirar instantâneos de blobs. Instantâneos de capturam o estado de blob nesse ponto no tempo. Neste artigo, descrevemos um cenário no qual pode manter cópias de segurança de discos de máquinas virtuais através de instantâneos. Pode usar essa metodologia quando optar por não utilizar o serviço de recuperação e cópia de segurança do Azure e pretende criar uma estratégia de cópia de segurança personalizada para os discos da máquina virtual.

Discos da máquina virtual do Azure são armazenados como blobs de páginas no armazenamento do Azure. Uma vez que estamos descrevendo uma estratégia de cópia de segurança para os discos da máquina virtual neste artigo, nos Referimos a instantâneos no contexto de blobs de páginas. Para obter mais informações sobre instantâneos, consulte [criar um instantâneo de um Blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

## <a name="what-is-a-snapshot"></a>O que é um instantâneo?
Um instantâneo de blob é uma versão só de leitura de um blob que é capturado num ponto no tempo. Assim que tiver sido criado um instantâneo, ele possa ser lido, copiado, ou eliminado, mas não modificado. Instantâneos fornecem uma forma de fazer cópias de segurança de um blob como ela aparece num momento. Até REST 2015-04-05 de versão, tinha a capacidade de copiar instantâneos completos. Com o resto versão 2015-07-08 e superior, também pode copiar instantâneos incrementais.

## <a name="full-snapshot-copy"></a>Cópia do instantâneo completo
Instantâneos podem ser copiados para outra conta de armazenamento como um blob para manter as cópias de segurança do base blob. Também pode copiar um instantâneo ao longo do seu blob de base, que é como restaurar o blob para uma versão anterior. Quando um instantâneo é copiado de uma conta de armazenamento para outro, ele ocupa o mesmo espaço de como o blob de página base. Por conseguinte, instantâneos de todos a copiar de uma conta de armazenamento para outro é lenta e consome espaço na conta de armazenamento de destino.

> [!NOTE]
> Se copiar o blob de base para outro destino, os instantâneos do blob não são copiados juntamente com ele. Da mesma forma, se substituir um blob de base com uma cópia, instantâneos associados com o blob de base não são afetados e mantenha-se intactos sob o nome de blob de base.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Criar cópias de segurança utilizando instantâneos de discos
Como uma estratégia de cópia de segurança para os discos da máquina virtual, pode tirar instantâneos periódicos do blob de página ou de disco e cópia-los para outro armazenamento de conta através de ferramentas, como [Blob de cópia](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) operação ou [AzCopy](../articles/storage/common/storage-use-azcopy.md). Pode copiar um instantâneo para um blob de página de destino com um nome diferente. O blob de página de destino resultante é um blob de página graváveis e não um instantâneo. Neste artigo, descrevemos de passos para efetuar cópias de segurança dos discos de máquina virtual através de instantâneos.

### <a name="restore-disks-using-snapshots"></a>Restaure discos com instantâneos
Quando chegou a hora para restaurar o disco para uma versão estável que anteriormente foram capturada em um dos instantâneos de cópia de segurança, pode copiar um instantâneo sobre o blob de página base. Depois do instantâneo é promovido para a página base de BLOBs, mantém-se o instantâneo, mas sua fonte será substituído com uma cópia que pode ser de leitura e escrita. Neste artigo, descrevemos passos para restaurar uma versão anterior do seu disco a partir do respetivo instantâneo.

### <a name="implementing-full-snapshot-copy"></a>Implementar a cópia do instantâneo completo
Pode implementar uma cópia do instantâneo completo ao fazer o seguinte,

* Em primeiro lugar, tire um instantâneo do base blob com o [Blob de instantâneo](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) operação.
* Em seguida, copiar o instantâneo para uma utilização de conta de armazenamento do destino [Blob de cópia](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob).
* Repita este processo para manter cópias de segurança dos seus BLOBs base.

## <a name="incremental-snapshot-copy"></a>Cópia do instantâneo incremental
O novo recurso no [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) API fornece uma forma muito melhor para fazer cópias de segurança de instantâneos de seus blobs de páginas ou discos. A API devolve a lista de alterações entre o blob de base e os instantâneos, que reduz a quantidade de espaço de armazenamento utilizado na conta de cópia de segurança. A API suporta blobs de páginas no armazenamento Premium, bem como o armazenamento Standard. Com essa API, pode criar soluções de cópia de segurança mais rápidas e eficientes para VMs do Azure. Esta API estarão disponíveis na versão REST 2015-07-08 e superior.

Cópia de instantâneo incremental permite-lhe copiar de uma conta de armazenamento, para outra, a diferença entre,

* Blob de base e o respetivo instantâneo ou um
* Qualquer dois instantâneos do base blob

Fornecidas que as seguintes condições são cumpridas,

* O blob foi criado em 1 de Janeiro 2016 ou posterior.
* O blob não foi substituído com [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) ou [Blob de cópia](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) entre os dois instantâneos.

**Nota**: Esta funcionalidade está disponível para Blobs de páginas do Azure Standard e Premium.

Quando tem uma estratégia de cópia de segurança personalizada, utilizando instantâneos de, copiar os instantâneos de uma conta de armazenamento para outro pode ser lento e pode consumir muito espaço de armazenamento. Em vez de copiar o instantâneo de todo a uma conta de armazenamento de cópia de segurança, pode escrever a diferença entre os instantâneos consecutivas para um blob de página de cópia de segurança. Dessa forma, o tempo de cópia e o espaço para armazenar cópias de segurança é substancialmente reduzido.

### <a name="implementing-incremental-snapshot-copy"></a>Implementar a cópia do instantâneo Incremental
Pode implementar a cópia do instantâneo incremental ao fazer o seguinte,

* Tirar um instantâneo da base blob usando [Blob de instantâneo](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob).
* Copiar o instantâneo para a conta de armazenamento de cópia de segurança de destino na mesma subscrição ou qualquer outra região do Azure, utilizando [Blob de cópia](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob). Este é o blob de página de cópia de segurança. Tirar um instantâneo de blob de página de cópia de segurança e armazene-o na conta de cópia de segurança.
* Tire instantâneo outro do blob base com o Blob de instantâneo.
* Obter a diferença entre os instantâneos de primeiros e segunda da base blob usando [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges). Utilize o parâmetro novo **prevsnapshot**, para especificar o valor de DateTime do instantâneo que pretende obter a diferença com. Quando este parâmetro está presente, a resposta REST inclui somente as páginas que foram alteradas entre o instantâneo de destino e o instantâneo anterior, incluindo páginas claras.
* Uso [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) para aplicar estas alterações para o blob de página de cópia de segurança.
* Por fim, tire um instantâneo de blob de página de cópia de segurança e armazene-o na conta de armazenamento de cópia de segurança.

Na próxima seção, descreveremos mais detalhadamente como pode manter cópias de segurança de discos utilizando a cópia de instantâneo Incremental

## <a name="scenario"></a>Cenário
Nesta seção, descrevemos um cenário que envolve uma estratégia de cópia de segurança personalizada para utilizar instantâneos de discos de máquinas virtuais.

Considere uma VM do Azure de série DS com um disco de P30 de armazenamento premium ligado. O disco de P30 chamado *mypremiumdisk* são armazenados numa conta de armazenamento premium chamada *mypremiumaccount*. Uma conta de armazenamento standard denominada *mybackupstdaccount* é utilizado para armazenar a cópia de segurança de *mypremiumdisk*. Gostaríamos de manter um instantâneo dos *mypremiumdisk* a cada 12 horas.

Para saber mais sobre como criar uma conta de armazenamento, veja [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

Para saber mais sobre o backup das VMs do Azure, veja [cópias de segurança de VM do Azure de planear](../articles/backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Passos para manter cópias de segurança de um disco com instantâneos incrementais
Os passos seguintes descrevem como criar instantâneos de *mypremiumdisk* e manter as cópias de segurança na *mybackupstdaccount*. A cópia de segurança é um blob de página padrão chamado *mybackupstdpageblob*. O blob de página de cópia de segurança sempre reflete o estado do mesmo como o último instantâneo do *mypremiumdisk*.

1. Criar um instantâneo de blob de página de cópia de segurança para o seu disco de armazenamento premium, *mypremiumdisk* chamado *mypremiumdisk_ss1*.
2. Copie este instantâneo para mybackupstdaccount como um blob de página chamado *mybackupstdpageblob*.
3. Tirar um instantâneo *mybackupstdpageblob* chamado *mybackupstdpageblob_ss1*, com [Blob de instantâneo](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) e armazená-la no *mybackupstdaccount*.
4. Durante a janela de cópia de segurança, criar outro instantâneo do *mypremiumdisk*, digamos *mypremiumdisk_ss2*e armazená-la no *mypremiumaccount*.
5. Obter as alterações incrementais entre os dois instantâneos *mypremiumdisk_ss2* e *mypremiumdisk_ss1*, com [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) no *mypremiumdisk_ ss2* com o **prevsnapshot** parâmetro definido como o carimbo de hora de *mypremiumdisk_ss1*. Estas alterações incrementais de escrita para o blob de página de cópia de segurança *mybackupstdpageblob* na *mybackupstdaccount*. Se existirem intervalos eliminados nas alterações incrementais, tem de ser limpo de blob de página de cópia de segurança. Uso [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) escrever as alterações incrementais para o blob de página de cópia de segurança.
6. Tirar um instantâneo de blob de página de cópia de segurança *mybackupstdpageblob*, denominado *mybackupstdpageblob_ss2*. Eliminar o instantâneo anterior *mypremiumdisk_ss1* da conta de armazenamento premium.
7. Repita os passos 4 a 6 cada janela de backup. Dessa forma, pode manter cópias de segurança de *mypremiumdisk* numa conta de armazenamento standard.

![Fazer cópias de segurança de disco com instantâneos incrementais](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Passos para restaurar um disco a partir de instantâneos
Os seguintes passos descrevem como restaurar o disco premium, *mypremiumdisk* para um instantâneo anterior da conta de armazenamento de cópia de segurança *mybackupstdaccount*.

1. Identifica o ponto no tempo que queira restaurar o disco premium. Vamos supor que ele é instantâneo *mybackupstdpageblob_ss2*, que é armazenado na conta de armazenamento de cópia de segurança *mybackupstdaccount*.
2. No mybackupstdaccount, promover o instantâneo *mybackupstdpageblob_ss2* como o blob de página de base de cópia de segurança nova *mybackupstdpageblobrestored*.
3. Tirar um instantâneo de blob esta página de cópia de segurança restaurada, chamado *mybackupstdpageblobrestored_ss1*.
4. Copiar o blob de página restaurada *mybackupstdpageblobrestored* partir *mybackupstdaccount* para *mypremiumaccount* como o novo disco premium  *mypremiumdiskrestored*.
5. Tirar um instantâneo *mypremiumdiskrestored*, denominado *mypremiumdiskrestored_ss1* para fazer cópias de segurança incrementais futuras.
6. Aponte a VM da série DS para o disco restaurado *mypremiumdiskrestored* e desanexar o antigo *mypremiumdisk* da VM.
7. Iniciar o processo de cópia de segurança descrito na secção anterior para o disco restaurado *mypremiumdiskrestored*, utilizando o *mybackupstdpageblobrestored* como o blob de páginas de cópia de segurança.

![Restaurar o disco a partir de instantâneos](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Próximos Passos
Utilize as seguintes ligações para saber mais sobre a criação de instantâneos de um blob e planeamento da sua infraestrutura de cópia de segurança de VM.

* [Criar um instantâneo de um Blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Planear a sua infraestrutura de cópia de segurança de VM](../articles/backup/backup-azure-vms-introduction.md)

