---
title: Faça o upload de um VHD para Azure ou copie um disco através de regiões - Azure PowerShell
description: Saiba como carregar um VHD para um disco gerido aZure e copiar um disco gerido através das regiões, utilizando o Azure PowerShell, através do upload direto.
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: c766c78705a1c1e40a9385360d35ac06a3db3a5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99252243"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>Faça o upload de um VHD para Azure ou copie um disco gerido para outra região - Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Descarregue a versão mais recente [do AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Instale o módulo Azure PowerShell](/powershell/azure/install-Az-ps).
- Se pretender carregar um VHD a partir do local: Um VHD de tamanho fixo que [foi preparado para o Azure,](prepare-for-upload-vhd-image.md)armazenado localmente.
- Ou, um disco gerido em Azure, se pretender realizar uma ação de cópia.

## <a name="getting-started"></a>Introdução

Se preferir fazer o upload de discos através de um GUI, pode fazê-lo utilizando o Azure Storage Explorer. Para mais detalhes, consulte: [Use o Azure Storage Explorer para gerir discos geridos aZure](../disks-use-storage-explorer-managed-disks.md)

Para fazer o upload do seu VHD para Azure, terá de criar um disco gerido vazio que esteja configurado para este processo de upload. Antes de criar um, há algumas informações adicionais que deve saber sobre estes discos.

Este tipo de disco gerido tem dois estados únicos:

- ReadToUpload, o que significa que o disco está pronto para receber um upload mas não foi gerada nenhuma [assinatura de acesso seguro](../../storage/common/storage-sas-overview.md) (SAS).
- ActiveUpload, o que significa que o disco está pronto para receber um upload e o SAS foi gerado.

> [!NOTE]
> Enquanto em qualquer um destes estados, o disco gerido será faturado com [preços hdd padrão](https://azure.microsoft.com/pricing/details/managed-disks/), independentemente do tipo real de disco. Por exemplo, um P10 será cobrado como um S10. Isto será verdade até `revoke-access` ser chamado no disco gerido, que é necessário para anexar o disco a um VM.

## <a name="create-an-empty-managed-disk"></a>Criar um disco gerido vazio

Antes de criar um HDD padrão vazio para o upload, vai precisar do tamanho do ficheiro do VHD que pretende carregar, em bytes. O código de exemplo vai conseguir isso para si, mas, para fazê-lo por si mesmo, pode usar: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length` . Este valor é utilizado ao especificar o parâmetro **-UploadSizeInBytes.**

Agora, na sua concha local, crie um HDD padrão vazio para carregar especificando a definição **de Upload** no parâmetro **-CreateOption,** bem como o parâmetro **-UploadSizeInBytes** no cmdlet [New-AzDiskConfig.](/powershell/module/az.compute/new-azdiskconfig) Em seguida, ligue para [New-AzDisk](/powershell/module/az.compute/new-azdisk) para criar o disco.

Substituir `<yourdiskname>` `<yourresourcegroupname>` , `<yourregion>` e, em seguida, executar os seguintes comandos:

> [!TIP]
> Se estiver a criar um disco DE, adicione `-HyperVGeneration '<yourGeneration>'` a `New-AzDiskConfig` .

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -Disk $diskconfig
```

Se quiser carregar um SSD premium ou um SSD padrão, substitua **Standard_LRS** por **Premium_LRS** ou **StandardSSD_LRS**. Os discos ultra ainda não estão suportados.

Agora que criou um disco gerido vazio que está configurado para o processo de upload, pode enviar um VHD para o mesmo. Para fazer o upload de um VHD para o disco, necessitará de um SAS escrito, para que possa reencomiá-lo como o destino do seu upload.

Para gerar um SAS writable do seu disco gerido vazio, substitua `<yourdiskname>` `<yourresourcegroupname>` e, em seguida, use os seguintes comandos:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>Carregar um VHD

Agora que tem um SAS para o seu disco gerido vazio, pode usá-lo para definir o seu disco gerido como o destino para o seu comando de upload.

Utilize o AzCopy v10 para enviar o seu ficheiro VHD local para um disco gerido especificando o SAS URI gerado.

Este upload tem a mesma produção que o [HDD padrão](../disks-types.md#standard-hdd)equivalente . Por exemplo, se tiver um tamanho que equivale a S4, terá uma produção de até 60 MiB/s. Mas, se tiver um tamanho que equivale a S70, terá uma produção de até 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Depois de o upload estar completo, e já não precisar de escrever mais dados para o disco, revogue o SAS. A revogação do SAS alterará o estado do disco gerido e permite-lhe anexar o disco a um VM.

Substitua `<yourdiskname>` `<yourresourcegroupname>` e, em seguida, executar o seguinte comando:

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>Copiar um disco gerido

O upload direto também simplifica o processo de cópia de um disco gerido. Pode copiar dentro da mesma região ou copiar o seu disco gerido para outra região.

O seguinte script fará isso por si, o processo é semelhante aos passos descritos anteriormente, com algumas diferenças, uma vez que está a trabalhar com um disco existente.

> [!IMPORTANT]
> Tens de adicionar uma compensação de 512 quando estás a fornecer o tamanho do disco em bytes de um disco gerido a partir do Azure. Isto porque Azure omite o rodapé ao devolver o tamanho do disco. A cópia falhará se não fizer isto. O seguinte guião já faz isto por si.

Substitua o `<sourceResourceGroupHere>` , , , e `<sourceDiskNameHere>` `<targetDiskNameHere>` `<targetResourceGroupHere>` `<yourOSTypeHere>` `<yourTargetLocationHere>` (um exemplo de um valor de localização seria uswest2) pelos seus valores, em seguida, executar o seguinte script para copiar um disco gerido.

> [!TIP]
> Se estiver a criar um disco DE, adicione -HyperVGeneration <yourGeneration> ' para `New-AzDiskConfig` .

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>Passos seguintes

Agora que fez o upload de um VHD com sucesso para um disco gerido, pode ligar o disco a um VM e começar a usá-lo.

Para saber como anexar um disco de dados a um VM, consulte o nosso artigo sobre o assunto: [Anexar um disco de dados a um VM do Windows com o PowerShell](attach-disk-ps.md). Para utilizar o disco como disco DE, consulte [Criar um VM do Windows a partir de um disco especializado](create-vm-specialized.md#create-the-new-vm).
