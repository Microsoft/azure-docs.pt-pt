---
title: Quickstart - Crie uma bolha com powerShell
titleSuffix: Azure Storage
description: Neste início rápido, irá utilizar o Azure PowerShell no armazenamento de objetos (Blobs). Em seguida, utilize o PowerShell para carregar um blob para o Armazenamento do Microsoft Azure, transferir um blob e listar os blobs num contentor.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 03/31/2020
ms.author: tamram
ms.openlocfilehash: 3b005bc359b3c1b0cafe663b7ce2b599b10973a1
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474011"
---
# <a name="quickstart-upload-download-and-list-blobs-with-powershell"></a>Quickstart: Upload, download e lista de blobs com PowerShell

Utilizar o módulo do Azure PowerShell para criar e gerir recursos do Azure. Pode criar ou gerir recursos do Azure a partir da linha de comandos do PowerShell ou em scripts. Este guia descreve como utilizar o PowerShell para transferir ficheiros entre o disco local e o armazenamento de Blobs do Azure.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para aceder ao Armazenamento Azure, necessitará de uma subscrição Azure. Se ainda não tem uma subscrição, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este quickstart requer o módulo Azure PowerShell Az versão 0.7 ou posterior. Executar `Get-InstalledModule -Name Az -AllVersions | select Name,Version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-az-ps)(Instalar o módulo do Azure PowerShell).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Criar um contentor

Os Blobs são sempre carregados para um contentor. Pode organizar grupos de blobs, tal como organiza os ficheiros em pastas no seu computador.

Delineie o nome do recipiente e, em seguida, crie o recipiente utilizando [o New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Defina as permissões para `blob`, para permitir o acesso público dos ficheiros. O nome do contentor neste exemplo é *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contentor

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Os ficheiros VHD que suportam as VMs IaaS são blobs de páginas. Utilize blobs de acréscimo para registo, como quando quer escrever num ficheiro e continue a adicionar mais informações. A maioria dos ficheiros guardados no armazenamento de Blobs são blobs de blocos. 

Para carregar um ficheiro para um blob de blocos, obtenha uma referência de contentor e uma referência de blob de blocos nesse contentor. Uma vez que tenha a referência blob, pode fazer o upload de dados através do [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent). Esta operação cria o blob, caso este não exista, ou substitui-o se já existir.

Os exemplos seguintes carregam *Image001.jpg* e *Image002.png* da pasta *D:\\_TestImages* no disco local para o contentor que criou.

```powershell
# upload a file
Set-AzStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

Carregue os ficheiros que quiser antes de continuar.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Obtenha uma lista de bolhas no recipiente utilizando [get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob). Este exemplo mostra apenas os nomes dos blobs carregados.

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>Transferir blobs

Transfira os blobs para o disco local. Para cada bolha que pretende descarregar, delineie o nome e ligue para [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent) para descarregar a bolha.

Este exemplo transfere os blobs para *D:\\_TestImages\Downloads* no disco local. 

```powershell
# download first blob
Get-AzStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx
```

## <a name="data-transfer-with-azcopy"></a>Transferência de dados com o AzCopy

O utilitário de linha de comando AzCopy oferece transferência de dados de alto desempenho e scriptable para o Armazenamento Azure. Pode utilizar o AzCopy para transferir dados de e para o armazenamento blob e ficheiros Azure. Para mais informações sobre o AzCopy v10, a versão mais recente do AzCopy, consulte [Get started with AzCopy](../common/storage-use-azcopy-v10.md). Para aprender sobre a utilização do AzCopy v10 com armazenamento Blob, consulte [os dados da Transferência com armazenamento AzCopy e Blob](../common/storage-use-azcopy-blobs.md).

O exemplo que se segue utiliza o AzCopy para fazer o upload de um ficheiro local para uma bolha. Lembre-se de substituir os valores da amostra por valores próprios:

```powershell
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
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
