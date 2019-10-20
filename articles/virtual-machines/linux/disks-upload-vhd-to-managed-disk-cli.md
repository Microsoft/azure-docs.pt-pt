---
title: Carregar um VHD no Azure usando o CLI do Azure
description: Saiba como carregar um VHD em um disco gerenciado do Azure e copiar um disco gerenciado entre regiões, usando o CLI do Azure.
services: virtual-machines-linux,storage
author: roygara
ms.author: rogarana
ms.date: 09/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: dfcf9ea61a1f0fb5fd2d3b613c2449480753b3a1
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595100"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Carregar um VHD no Azure usando o CLI do Azure

Este artigo explica como carregar um VHD do computador local para um disco gerenciado do Azure. Anteriormente, era necessário seguir um processo mais envolvido que incluía o preparo de seus dados em uma conta de armazenamento e o gerenciamento dessa conta de armazenamento. Agora, você não precisa mais gerenciar uma conta de armazenamento ou preparar dados nela para carregar um VHD. Em vez disso, você cria um disco gerenciado vazio e carrega um VHD diretamente nele. Isso simplifica o carregamento de VMs locais para o Azure e permite que você carregue um VHD até 32 TiB diretamente em um grande disco gerenciado.

Se você estiver fornecendo uma solução de backup para VMs IaaS no Azure, recomendamos o uso do carregamento direto para restaurar os backups do cliente para o Managed disks. Se você estiver carregando um VHD de um computador externo para o Azure, as velocidades dependerão da largura de banda local. Se você estiver usando uma VM do Azure, sua largura de banda será a mesma que HDDs padrão.

Atualmente, o carregamento direto tem suporte para discos gerenciados HDD padrão, SSD Standard e SSD Premium. Ainda não há suporte para o ultra SSDs.

## <a name="prerequisites"></a>Pré-requisitos

- Baixe a versão mais recente [do AzCopy V10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Instale o CLI do Azure](/cli/azure/install-azure-cli).
- Um arquivo VHD, armazenado localmente
- Se você pretende carregar um VHD de on-PEM: um VHD que foi [preparado para o Azure](../windows/prepare-for-upload-vhd-image.md), armazenado localmente.
- Ou, um disco gerenciado no Azure, se você pretende executar uma ação de cópia.

## <a name="create-an-empty-managed-disk"></a>Criar um disco gerenciado vazio

Para carregar o VHD no Azure, você precisará criar um disco gerenciado vazio que esteja configurado para esse processo de carregamento. Antes de criar uma, há algumas informações adicionais que você deve saber sobre esses discos.

Esse tipo de disco gerenciado tem dois Estados exclusivos:

- ReadToUpload, que significa que o disco está pronto para receber um upload, mas nenhuma [assinatura de acesso seguro](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) foi gerada.
- ActiveUpload, o que significa que o disco está pronto para receber um upload e a SAS foi gerada.

Em qualquer um desses Estados, o disco gerenciado será cobrado no preço de [HDD padrão](https://azure.microsoft.com/pricing/details/managed-disks/), independentemente do tipo real de disco. Por exemplo, um P10 será cobrado como um S10. Isso será verdadeiro até que `revoke-access` seja chamado no disco gerenciado, que é necessário para anexar o disco a uma VM.

Antes de criar um HDD padrão vazio para carregamento, você precisará ter o tamanho do arquivo do VHD que deseja carregar, em bytes. Para obtê-lo, você pode usar `wc -c <yourFileName>.vhd` ou `ls -al <yourFileName>.vhd`. Esse valor é usado ao especificar o parâmetro **--upload-size-bytes** .

Crie um HDD padrão vazio para carregamento, especificando o parâmetro **-– para-upload** e o parâmetro **--upload-size-bytes** em um cmdlet Create de [disco](/cli/azure/disk#az-disk-create) :

```bash
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Se você quiser carregar um SSD Premium ou um SSD padrão, substitua **standard_lrs** por **premium_LRS** ou **standardssd_lrs**. SSD Ultra ainda não tem suporte.

Agora você criou um disco gerenciado vazio que está configurado para o processo de carregamento. Para carregar um VHD no disco, você precisará de uma SAS gravável, para que você possa referenciá-lo como o destino do upload.

Para gerar uma SAS gravável de seu disco gerenciado vazio, use o seguinte comando:

```bash
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

Valor retornado de exemplo:

```
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>Carregar VHD

Agora que você tem uma SAS para seu disco gerenciado vazio, você pode usá-lo para definir o disco gerenciado como o destino para o comando de carregamento.

Use AzCopy V10 para carregar o arquivo VHD local em um disco gerenciado especificando o URI SAS gerado.

Esse carregamento tem a mesma taxa de transferência que o [HDD padrão](disks-types.md#standard-hdd)equivalente. Por exemplo, se você tiver um tamanho que é igual a S4, terá uma taxa de transferência de até 60 MiB/s. Mas, se você tiver um tamanho igual a S70, terá uma taxa de transferência de até 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Se sua SAS expirar durante o carregamento e você ainda não tiver chamado `revoke-access`, poderá obter uma nova SAS para continuar o carregamento usando `grant-access`, novamente.

Depois que o upload for concluído e você não precisar mais gravar mais dados no disco, revogue a SAS. A revogação da SAS alterará o estado do disco gerenciado e permitirá que você anexe o disco a uma VM.

```bash
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="copy-a-managed-disk"></a>Copiar um disco gerido

O carregamento direto também simplifica o processo de cópia de um disco gerenciado. Você pode copiar na mesma região ou em uma região cruzada (para outra região).

O script a seguir fará isso para você, o processo é semelhante às etapas descritas anteriormente, com algumas diferenças, já que você está trabalhando com um disco existente.

> [!IMPORTANT]
> Você precisa adicionar um deslocamento de 512 quando estiver fornecendo o tamanho do disco em bytes de um disco gerenciado do Azure. Isso ocorre porque o Azure omite o rodapé ao retornar o tamanho do disco. A cópia falhará se você não fizer isso. O script a seguir já faz isso para você.

Substitua o `<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>` e `<yourTargetLocationHere>` (um exemplo de um valor de local seria uswest2) com seus valores e, em seguida, execute o seguinte script para copiar um disco gerenciado.

```bash
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

Agora que você carregou com êxito um VHD em um disco gerenciado, você pode anexar o disco a uma VM e começar a usá-lo.

Para saber como anexar um disco a uma VM, consulte nosso artigo sobre o assunto: [Adicionar um disco a uma VM do Linux](add-disk.md).
