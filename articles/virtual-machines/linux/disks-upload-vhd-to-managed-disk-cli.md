---
title: Faça o upload de um VHD para Azure ou copie um disco através de regiões - Azure CLI
description: Saiba como carregar um VHD para um disco gerido aZure e copiar um disco gerido através das regiões, utilizando o CLI Azure, através do upload direto.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 473e87904742395eca6b7eeba0875cd93789104d
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978990"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Faça o upload de um VHD para Azure ou copie um disco gerido para outra região - Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Descarregue a versão mais recente [do AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Instale o Azure CLI](/cli/azure/install-azure-cli).
- Se pretender carregar um VHD a partir do local: Um VHD de tamanho fixo que [foi preparado para o Azure,](../windows/prepare-for-upload-vhd-image.md)armazenado localmente.
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

Antes de criar um HDD padrão vazio para o upload, vai precisar do tamanho do ficheiro do VHD que pretende carregar, em bytes. Para conseguir isso, pode usar um `wc -c <yourFileName>.vhd` `ls -al <yourFileName>.vhd` ou. Este valor é utilizado ao especificar o parâmetro **--upload-size-bytes.**

Crie um HDD padrão vazio para o upload especificando tanto o parâmetro **-- para carregar** e o parâmetro **--upload-size-bytes** em um [disco criar](/cli/azure/disk#az-disk-create) cmdlet:

`<yourdiskname>` `<yourresourcegroupname>` Substitua, por `<yourregion>` valores à sua escolha. O `--upload-size-bytes` parâmetro contém um valor de exemplo `34359738880` de, substitua-o por um valor apropriado para si.

> [!TIP]
> Se estiver a criar um disco DE, adicione -- hiper-v geração <yourGeneration> a `az disk create` .

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Se quiser carregar um SSD premium ou um SSD padrão, substitua **standard_lrs** por **premium_LRS** ou **standardssd_lrs**. Os discos ultra não são suportados por enquanto.

Agora que criou um disco gerido vazio que está configurado para o processo de upload, pode enviar um VHD para o mesmo. Para fazer o upload de um VHD para o disco, necessitará de um SAS escrito, para que possa reencomiá-lo como o destino do seu upload.

Para gerar um SAS writable do seu disco gerido vazio, substitua `<yourdiskname>` `<yourresourcegroupname>` e, em seguida, use o seguinte comando:

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

Agora que tem um SAS para o seu disco gerido vazio, pode usá-lo para definir o seu disco gerido como o destino para o seu comando de upload.

Utilize o AzCopy v10 para enviar o seu ficheiro VHD local para um disco gerido especificando o SAS URI gerado.

Este upload tem a mesma produção que o [HDD padrão](../disks-types.md#standard-hdd)equivalente . Por exemplo, se tiver um tamanho que equivale a S4, terá uma produção de até 60 MiB/s. Mas, se tiver um tamanho que equivale a S70, terá uma produção de até 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Depois de o upload estar completo, e já não precisar de escrever mais dados para o disco, revogue o SAS. A revogação do SAS alterará o estado do disco gerido e permite-lhe anexar o disco a um VM.

Substitua `<yourdiskname>` `<yourresourcegroupname>` e, em seguida, utilize o seguinte comando para tornar o disco utilizável:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>Copiar um disco gerido

O upload direto também simplifica o processo de cópia de um disco gerido. Pode copiar dentro da mesma região ou cross-region (para outra região).

O seguinte script fará isso por si, o processo é semelhante aos passos descritos anteriormente, com algumas diferenças uma vez que está a trabalhar com um disco existente.

> [!IMPORTANT]
> Tens de adicionar uma compensação de 512 quando estás a fornecer o tamanho do disco em bytes de um disco gerido a partir do Azure. Isto porque Azure omite o rodapé ao devolver o tamanho do disco. A cópia falhará se não fizer isto. O seguinte guião já faz isto por si.

Substitua o `<sourceResourceGroupHere>` , , , e `<sourceDiskNameHere>` `<targetDiskNameHere>` `<targetResourceGroupHere>` `<yourTargetLocationHere>` (um exemplo de um valor de localização seria uswest2) pelos seus valores, em seguida, executar o seguinte script para copiar um disco gerido.

> [!TIP]
> Se estiver a criar um disco DE, adicione -- hiper-v geração <yourGeneration> a `az disk create` .

```azurecli
sourceDiskName=<sourceDiskNameHere>
sourceRG=<sourceResourceGroupHere>
targetDiskName=<targetDiskNameHere>
targetRG=<targetResourceGroupHere>
targetLocation=<yourTargetLocationHere>

sourceDiskSizeBytes=$(az disk show -g $sourceRG -n $sourceDiskName --query '[diskSizeBytes]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocation --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI=$(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Passos seguintes

Agora que fez o upload de um VHD com sucesso para um disco gerido, pode anexar o disco como [disco de dados a um VM existente](add-disk.md) ou [anexar o disco VM a um disco VM como disco de OS,](upload-vhd.md#create-the-vm)para criar um novo VM.