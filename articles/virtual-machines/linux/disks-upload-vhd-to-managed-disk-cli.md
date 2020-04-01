---
title: Faça upload de um VHD para Azure ou copie um disco em todas as regiões - Azure CLI
description: Aprenda a carregar um VHD para um disco gerido pelo Azure e copie um disco gerido em todas as regiões, utilizando o Azure CLI, através do upload direto.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 6813206aebe67e4e6d2afd0d9c78d03f0c20c952
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420946"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Faça upload de um VHD para Azure ou copie um disco gerido para outra região - Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Descarregue a versão mais recente [do AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Instale o Azure CLI](/cli/azure/install-azure-cli).
- Se pretender fazer o upload de um VHD a partir do local: Um VHD de tamanho fixo que [foi preparado para o Azure,](../windows/prepare-for-upload-vhd-image.md)armazenado localmente.
- Ou, um disco gerido em Azure, se pretender realizar uma ação de cópia.

## <a name="getting-started"></a>Introdução

Se preferir fazer o upload de discos através de um GUI, pode fazê-lo usando o Azure Storage Explorer. Para mais detalhes consulte: [Use Azure Storage Explorer para gerir discos geridos pelo Azure](disks-use-storage-explorer-managed-disks.md)

Para fazer o upload do seu VHD para o Azure, terá de criar um disco gerido vazio que esteja configurado para este processo de upload. Antes de criar um, há algumas informações adicionais que deve saber sobre estes discos.

Este tipo de disco gerido tem dois estados únicos:

- ReadToUpload, o que significa que o disco está pronto para receber um upload mas não foi gerada nenhuma assinatura de [acesso segura](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS).
- ActiveUpload, o que significa que o disco está pronto para receber um upload e o SAS foi gerado.

> [!NOTE]
> Enquanto em qualquer um destes estados, o disco gerido será faturado com [preços HDD padrão,](https://azure.microsoft.com/pricing/details/managed-disks/)independentemente do tipo real de disco. Por exemplo, um P10 será cobrado como um S10. Isto será verdade `revoke-access` até que seja chamado no disco gerido, que é necessário para ligar o disco a um VM.

## <a name="create-an-empty-managed-disk"></a>Criar um disco gerido vazio

Antes de poder criar um HDD padrão vazio para o upload, vai precisar do tamanho do ficheiro do VHD que pretende carregar, em bytes. Para conseguir isso, pode `wc -c <yourFileName>.vhd` `ls -al <yourFileName>.vhd`usar qualquer um ou. Este valor é utilizado ao especificar o parâmetro **--upload-size-bytes.**

Crie um HDD padrão vazio para o upload especificando tanto o parâmetro **---para upload** como o parâmetro **--upload-size-bytes** em um [disco criar](/cli/azure/disk#az-disk-create) cmdlet:

`<yourdiskname>`Substitua, `<yourregion>` `<yourresourcegroupname>`com valores à sua escolha. O `--upload-size-bytes` parâmetro contém um `34359738880`valor exemplo de, substitua-o por um valor apropriado para si.

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Se quiser fazer o upload de um SSD premium ou de um SSD padrão, substitua **standard_lrs** por **premium_LRS** ou **standardssd_lrs**. Os discos ultra não são suportados por enquanto.

Agora que criou um disco gerido vazio que está configurado para o processo de upload, pode enviar-lhe um VHD. Para fazer o upload de um VHD para o disco, você precisará de um SAS rectilável, para que possa referenciar como o destino para o seu upload.

Para gerar um SAS de supreensível `<yourdiskname>` `<yourresourcegroupname>`do seu disco gerido vazio, substitua e , em seguida, use o seguinte comando:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

Valor devolvido da amostra:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>Carregar um VHD

Agora que tem um SAS para o seu disco gerido vazio, pode usá-lo para definir o disco gerido como destino para o seu comando de upload.

Utilize o AzCopy v10 para fazer o upload do ficheiro VHD local para um disco gerido, especificando o SAS URI que gerou.

Este upload tem a mesma entrada que o [HDD padrão](disks-types.md#standard-hdd)equivalente . Por exemplo, se tiver um tamanho que equivale a S4, terá uma potência de até 60 MiB/s. Mas, se tiver um tamanho que equivale a S70, terá uma potência de até 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Depois de o upload estar completo e já não precisar de escrever mais dados para o disco, revogar o SAS. A revogação do SAS alterará o estado do disco gerido e permitirá que prenda o disco a um VM.

`<yourdiskname>`Substitua `<yourresourcegroupname>`e, em seguida, utilize o seguinte comando para tornar o disco utilizável:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>Copiar um disco gerido

O upload direto também simplifica o processo de cópia de um disco gerido. Pode copiar dentro da mesma região ou região transversal (para outra região).

O guião seguinte fará isto por si, o processo é semelhante aos passos descritos anteriormente, com algumas diferenças já que está a trabalhar com um disco existente.

> [!IMPORTANT]
> Tem de adicionar uma compensação de 512 quando estiver a fornecer o tamanho do disco em bytes de um disco gerido a partir do Azure. Isto porque Azure omite o rodapé ao devolver o tamanho do disco. A cópia falhará se não fizer isto. O seguinte guião já faz isto por ti.

Substitua `<sourceResourceGroupHere>` `<sourceDiskNameHere>`o `<targetDiskNameHere>` `<targetResourceGroupHere>`, `<yourTargetLocationHere>` , , e (um exemplo de um valor de localização seria uswest2) com os seus valores, em seguida, executar o seguinte script para copiar um disco gerido.

```azurecli
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[uniqueId]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Passos seguintes

Agora que carregou com sucesso um VHD para um disco gerido, pode anexar o disco como um disco de [dados a um VM existente](add-disk.md) ou fixar o disco a um [VM como um disco OS,](upload-vhd.md#create-the-vm)para criar um novo VM. 

