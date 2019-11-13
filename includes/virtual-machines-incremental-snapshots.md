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
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014538"
---
## <a name="overview"></a>Descrição geral
O armazenamento do Azure fornece a capacidade de tirar instantâneos de BLOBs. Os instantâneos capturam o estado do blob nesse momento. Neste artigo, descrevemos um cenário no qual você pode manter backups de discos de máquina virtual usando instantâneos. Você pode usar essa metodologia quando optar por não usar o serviço de backup e recuperação do Azure e desejar criar uma estratégia de backup personalizada para seus discos de máquina virtual.

Os discos de máquina virtual do Azure são armazenados como BLOBs de páginas no armazenamento do Azure. Como estamos descrevendo uma estratégia de backup para discos de máquina virtual neste artigo, nos referimos aos instantâneos no contexto de blobs de páginas. Para saber mais sobre instantâneos, consulte [criando um instantâneo de um blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

## <a name="what-is-a-snapshot"></a>O que é um instantâneo?
Um instantâneo de blob é uma versão somente leitura de um blob que é capturado em um ponto no tempo. Depois que um instantâneo tiver sido criado, ele poderá ser lido, copiado ou excluído, mas não modificado. Os instantâneos fornecem uma maneira de fazer backup de um blob conforme ele é exibido em um momento no tempo. Até a versão REST 2015-04-05, você tinha a capacidade de copiar instantâneos completos. Com a versão REST 2015-07-08 e superior, você também pode copiar instantâneos incrementais.

## <a name="full-snapshot-copy"></a>Cópia de instantâneo completo
Os instantâneos podem ser copiados para outra conta de armazenamento como um blob para manter os backups do blob de base. Você também pode copiar um instantâneo em seu blob de base, que é como restaurar o blob para uma versão anterior. Quando um instantâneo é copiado de uma conta de armazenamento para outra, ele ocupa o mesmo espaço que o blob da página de base. Portanto, copiar instantâneos inteiros de uma conta de armazenamento para outra é lento e consome muito espaço na conta de armazenamento de destino.

> [!NOTE]
> Se você copiar o blob de base para outro destino, os instantâneos do BLOB não serão copiados junto com ele. Da mesma forma, se você substituir um blob de base por uma cópia, os instantâneos associados ao blob de base não serão afetados e permanecerão intactos no nome do blob de base.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Fazer backup de discos usando instantâneos
Como uma estratégia de backup para seus discos de máquina virtual, você pode fazer instantâneos periódicos do disco ou BLOB de página e copiá-los para outra conta de armazenamento usando ferramentas como [copiar blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) operação ou [AzCopy](../articles/storage/common/storage-use-azcopy.md). Você pode copiar um instantâneo para um blob de páginas de destino com um nome diferente. O blob de páginas de destino resultante é um blob de páginas gravável e não um instantâneo. Posteriormente neste artigo, descreveremos as etapas para fazer backups de discos de máquina virtual usando instantâneos.

### <a name="restore-disks-using-snapshots"></a>Restaurar discos usando instantâneos
Quando for o momento de restaurar o disco para uma versão estável que foi capturada anteriormente em um dos instantâneos de backup, você poderá copiar um instantâneo sobre o blob de página de base. Depois que o instantâneo é promovido para o blob de página de base, o instantâneo permanece, mas sua origem é substituída por uma cópia que pode ser lida e gravada. Posteriormente neste artigo, descreveremos as etapas para restaurar uma versão anterior do seu disco a partir de seu instantâneo.

### <a name="implementing-full-snapshot-copy"></a>Implementando cópia de instantâneo completo
Você pode implementar uma cópia de instantâneo completa fazendo o seguinte:

* Primeiro, tire um instantâneo do blob de base usando a operação de [blob de instantâneo](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) .
* Em seguida, copie o instantâneo para uma conta de armazenamento de destino usando [copiar blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob).
* Repita esse processo para manter cópias de backup de seu blob de base.

## <a name="incremental-snapshot-copy"></a>Cópia de instantâneo incremental
O novo recurso na API do [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) fornece uma maneira muito melhor de fazer backup dos instantâneos dos seus discos ou BLOBs de página. A API retorna a lista de alterações entre o blob de base e os instantâneos, o que reduz a quantidade de espaço de armazenamento usado na conta de backup. A API dá suporte a blobs de página no armazenamento Premium, bem como armazenamento padrão. Usando essa API, você pode criar soluções de backup mais rápidas e mais eficientes para VMs do Azure. Essa API estará disponível com a versão REST 2015-07-08 e superior.

A cópia de instantâneo incremental permite copiar de uma conta de armazenamento para outra a diferença entre,

* Blob de base e seu instantâneo ou
* Quaisquer dois instantâneos do blob de base

Desde que as seguintes condições sejam atendidas,

* O blob foi criado em Jan-1-2016 ou posterior.
* O BLOB não foi substituído por [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) ou [copia o blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) entre dois instantâneos.

**Observação**: esse recurso está disponível para os BLOBs Premium e Standard de páginas do Azure.

Quando você tem uma estratégia de backup personalizada usando instantâneos, a cópia dos instantâneos de uma conta de armazenamento para outra pode ser lenta e pode consumir muito espaço de armazenamento. Em vez de copiar o instantâneo inteiro para uma conta de armazenamento de backup, você pode gravar a diferença entre instantâneos consecutivos em um blob de páginas de backup. Dessa forma, o tempo de cópia e o espaço para armazenar backups são substancialmente reduzidos.

### <a name="implementing-incremental-snapshot-copy"></a>Implementando cópia de instantâneo incremental
Você pode implementar a cópia de instantâneo incremental fazendo o seguinte:

* Tire um instantâneo do blob de base usando o [blob de instantâneo](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob).
* Copie o instantâneo para a conta de armazenamento de backup de destino no mesmo ou em qualquer outra região do Azure usando [copiar blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob). Este é o blob de páginas de backup. Tire um instantâneo do blob de páginas de backup e armazene-o na conta de backup.
* Tire outro instantâneo do blob de base usando o blob de instantâneo.
* Obtenha a diferença entre o primeiro e o segundo instantâneos do blob de base usando [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges). Use o novo parâmetro **prevsnapshot**para especificar o valor de data e hora do instantâneo com o qual você deseja obter a diferença. Quando esse parâmetro estiver presente, a resposta REST incluirá apenas as páginas que foram alteradas entre o instantâneo de destino e o instantâneo anterior, incluindo páginas claras.
* Use [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) para aplicar essas alterações ao blob de páginas de backup.
* Por fim, tire um instantâneo do blob de páginas de backup e armazene-o na conta de armazenamento de backup.

Na próxima seção, descreveremos em mais detalhes como você pode manter os backups de discos usando a cópia de instantâneo incremental

## <a name="scenario"></a>Cenário
Nesta seção, descrevemos um cenário que envolve uma estratégia de backup personalizada para discos de máquina virtual usando instantâneos.

Considere uma VM do Azure da série DS com um disco p30 de armazenamento Premium anexado. O disco p30 chamado *mypremiumdisk* é armazenado em uma conta de armazenamento Premium chamada *mypremiumaccount*. Uma conta de armazenamento padrão chamada *mybackupstdaccount* é usada para armazenar o backup de *mypremiumdisk*. Gostaríamos de manter um instantâneo de *mypremiumdisk* a cada 12 horas.

Para saber mais sobre como criar uma conta de armazenamento, confira [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

Para saber mais sobre como fazer backup de VMs do Azure, consulte [planejar backups de VM do Azure](../articles/backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Etapas para manter os backups de um disco usando instantâneos incrementais
As etapas a seguir descrevem como tirar instantâneos de *mypremiumdisk* e manter os backups em *mybackupstdaccount*. O backup é um blob de páginas padrão chamado *mybackupstdpageblob*. O blob de páginas de backup sempre reflete o mesmo estado que o último instantâneo de *mypremiumdisk*.

1. Crie o blob de páginas de backup para o disco de armazenamento Premium, tirando um instantâneo de *mypremiumdisk* chamado *mypremiumdisk_ss1*.
2. Copie esse instantâneo para mybackupstdaccount como um blob de páginas chamado *mybackupstdpageblob*.
3. Tire um instantâneo de *mybackupstdpageblob* chamado *mybackupstdpageblob_ss1*, usando o [blob de instantâneo](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) e armazená-lo em *mybackupstdaccount*.
4. Durante a janela de backup, crie outro instantâneo de *mypremiumdisk*, digamos *mypremiumdisk_ss2*e armazene-o em *mypremiumaccount*.
5. Obtenha as alterações incrementais entre os dois instantâneos, *mypremiumdisk_ss2* e *Mypremiumdisk_ss1*, [usando GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) em *mypremiumdisk_ss2* com o parâmetro **prevsnapshot** definido como o carimbo de data/hora de *mypremiumdisk_ss1*. Grave essas alterações incrementais no blob de páginas de backup *mybackupstdpageblob* em *mybackupstdaccount*. Se houver intervalos excluídos nas alterações incrementais, eles deverão ser apagados do blob de páginas de backup. Use [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) para gravar alterações incrementais no blob de páginas de backup.
6. Tire um instantâneo do blob de páginas de backup *mybackupstdpageblob*, chamado *mybackupstdpageblob_ss2*. Exclua o instantâneo anterior *mypremiumdisk_ss1* da conta de armazenamento Premium.
7. Repita as etapas 4-6 a cada janela de backup. Dessa forma, você pode manter backups do *mypremiumdisk* em uma conta de armazenamento padrão.

![Fazer backup de disco usando instantâneos incrementais](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Etapas para restaurar um disco a partir de instantâneos
As etapas a seguir descrevem como restaurar o disco Premium, *mypremiumdisk* para um instantâneo anterior da conta de armazenamento de backup *mybackupstdaccount*.

1. Identifique o ponto no tempo no qual você deseja restaurar o disco Premium. Digamos que seja um instantâneo *mybackupstdpageblob_ss2*, que é armazenado na conta de armazenamento de backup *mybackupstdaccount*.
2. Em mybackupstdaccount, promova o instantâneo *mybackupstdpageblob_ss2* como o novo *mybackupstdpageblobrestored*de blob de página de base de backup.
3. Faça um instantâneo desse blob de páginas de backup restaurado, chamado *mybackupstdpageblobrestored_ss1*.
4. Copie o blob de página restaurado *mybackupstdpageblobrestored* de *mybackupstdaccount* para *mypremiumaccount* como o novo disco Premium *mypremiumdiskrestored*.
5. Tire um instantâneo do *mypremiumdiskrestored*, chamado *mypremiumdiskrestored_ss1* para fazer backups incrementais futuros.
6. Aponte a VM da série DS para o disco restaurado *mypremiumdiskrestored* e desanexe o antigo *mypremiumdisk* da VM.
7. Inicie o processo de backup descrito na seção anterior para o disco restaurado *mypremiumdiskrestored*, usando o *mybackupstdpageblobrestored* como o blob de páginas de backup.

![Restaurar disco de instantâneos](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Passos Seguintes
Use os links a seguir para saber mais sobre como criar instantâneos de um blob e planejar sua infraestrutura de backup de VM.

* [Criando um instantâneo de um blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Planejar sua infraestrutura de backup de VM](../articles/backup/backup-azure-vms-introduction.md)

