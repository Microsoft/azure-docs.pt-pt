---
title: Início Rápido do Azure – Criar um blob no armazenamento de objetos com o Azure PowerShell | Microsoft Docs
description: Neste início rápido, irá utilizar o Azure PowerShell no armazenamento de objetos (Blobs). Em seguida, utilize o PowerShell para carregar um blob para o Armazenamento do Microsoft Azure, transferir um blob e listar os blobs num contentor.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: tamram
ms.openlocfilehash: 4ffa4319fa2691469899ff038eeedc7ef30ccebe
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565009"
---
# <a name="quickstart-upload-download-and-list-blobs-by-using-azure-powershell"></a>Início rápido: Carregar, baixar e listar BLOBs usando Azure PowerShell

Utilizar o módulo do Azure PowerShell para criar e gerir recursos do Azure. Pode criar ou gerir recursos do Azure a partir da linha de comandos do PowerShell ou em scripts. Este guia descreve como utilizar o PowerShell para transferir ficheiros entre o disco local e o armazenamento de Blobs do Azure.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para acessar o armazenamento do Azure, você precisará de uma assinatura do Azure. Se você ainda não tiver uma assinatura, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este guia de início rápido requer o módulo Azure PowerShell AZ versão 0,7 ou posterior. Executar `Get-InstalledModule -Name Az -AllVersions | select Name,Version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Criar um contentor

Os Blobs são sempre carregados para um contentor. Pode organizar grupos de blobs, tal como organiza os ficheiros em pastas no seu computador.

Defina o nome do contêiner e crie o contêiner usando [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer). Defina as permissões para `blob`, para permitir o acesso público dos ficheiros. O nome do contentor neste exemplo é *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
new-AzStoragecontainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contentor

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Os ficheiros VHD que suportam as VMs IaaS são blobs de páginas. Utilize blobs de acréscimo para registo, como quando quer escrever num ficheiro e continue a adicionar mais informações. A maioria dos ficheiros guardados no armazenamento de Blobs são blobs de blocos. 

Para carregar um ficheiro para um blob de blocos, obtenha uma referência de contentor e uma referência de blob de blocos nesse contentor. Quando tiver a referência de BLOB, você poderá carregar dados para ela usando [set-AzStorageBlobContent](/powershell/module/az.storage/set-AzStorageblobcontent). Esta operação cria o blob, caso este não exista, ou substitui-o se já existir.

Os exemplos seguintes carregam *Image001.jpg* e *Image002.png* da pasta *D:\\_TestImages* no disco local para o contentor que criou.

```powershell
# upload a file
set-AzStorageblobcontent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
set-AzStorageblobcontent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

Carregue os ficheiros que quiser antes de continuar.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Obtenha uma lista de BLOBs no contêiner usando [Get-AzStorageBlob](/powershell/module/az.storage/get-AzStorageblob). Este exemplo mostra apenas os nomes dos blobs carregados.

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>Transferir blobs

Transfira os blobs para o disco local. Para cada blob que você deseja baixar, defina o nome e chame [Get-AzStorageBlobContent](/powershell/module/az.storage/get-AzStorageblobcontent) para baixar o blob.

Este exemplo transfere os blobs para *D:\\_TestImages\Downloads* no disco local. 

```powershell
# download first blob
Get-AzStorageblobcontent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzStorageblobcontent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx
```

## <a name="data-transfer-with-azcopy"></a>Transferência de dados com o AzCopy

O utilitário [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) é outra opção para a transferência de dados de scripts de elevado desempenho para o Armazenamento do Azure. Utilize o AzCopy para transferir dados de e para o armazenamento de Blobs, Ficheiros e Tabelas.

Como exemplo rápido, eis o comando do AzCopy para carregar um ficheiro denominado *myfile.txt* para o contentor *mystoragecontainer* a partir de uma janela do PowerShell.

```powershell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>Limpar recursos

Remova todos os recursos que criou. A forma mais fácil de remover os recursos é eliminar o grupo de recursos. Remover o grupo de recursos também elimina todos os recursos incluídos no grupo. No exemplo seguinte, remover o grupo de recursos remove a conta de armazenamento e o próprio grupo de recursos.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, transferiu ficheiros entre um disco local e o armazenamento de Blobs do Azure. Para saber mais sobre como utilizar o armazenamento de Blobs com o PowerShell, avance para Como utilizar o Azure PowerShell com o Armazenamento do Azure.

> [!div class="nextstepaction"]
> [Using Azure PowerShell with Azure Storage (Utilizar o Azure PowerShell com o Armazenamento do Azure)](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Referência de cmdlets do Armazenamento do Microsoft Azure PowerShell

* [Cmdlets do Armazenamento do PowerShell](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Explorador de Armazenamento do Microsoft Azure

* O [Explorador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.
