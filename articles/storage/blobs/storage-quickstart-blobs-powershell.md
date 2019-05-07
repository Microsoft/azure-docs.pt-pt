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
ms.openlocfilehash: c0a5f7271628e11dbc8fa8b18b21358923f567cc
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149407"
---
# <a name="quickstart-upload-download-and-list-blobs-by-using-azure-powershell"></a>Início rápido: Carregar, transferir e listar os blobs com o Azure PowerShell

Utilizar o módulo do Azure PowerShell para criar e gerir recursos do Azure. Pode criar ou gerir recursos do Azure a partir da linha de comandos do PowerShell ou em scripts. Este guia descreve como utilizar o PowerShell para transferir ficheiros entre o disco local e o armazenamento de Blobs do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para aceder ao armazenamento do Azure, terá uma subscrição do Azure. Se ainda não tiver uma subscrição, em seguida, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este início rápido requer o módulo Azure PowerShell Az versão 0,7 ou posterior. Executar `Get-InstalledModule -Name Az -AllVersions | select Name,Version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Criar um contentor

Os Blobs são sempre carregados para um contentor. Pode organizar grupos de blobs, tal como organiza os ficheiros em pastas no seu computador.

Defina o nome do contentor, em seguida, criar o contentor utilizando [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer). Defina as permissões para `blob`, para permitir o acesso público dos ficheiros. O nome do contentor neste exemplo é *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
new-AzStoragecontainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contentor

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Os ficheiros VHD que suportam as VMs IaaS são blobs de páginas. Utilize blobs de acréscimo para registo, como quando quer escrever num ficheiro e continue a adicionar mais informações. A maioria dos ficheiros guardados no armazenamento de Blobs são blobs de blocos. 

Para carregar um ficheiro para um blob de blocos, obtenha uma referência de contentor e uma referência de blob de blocos nesse contentor. Assim que tiver a referência de blob, pode carregar dados para a mesma, utilizando [Set-AzStorageBlobContent](/powershell/module/az.storage/set-AzStorageblobcontent). Esta operação cria o blob, caso este não exista, ou substitui-o se já existir.

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

Obter uma lista de blobs no contentor usando [Get-AzStorageBlob](/powershell/module/az.storage/get-AzStorageblob). Este exemplo mostra apenas os nomes dos blobs carregados.

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>Transferir blobs

Transfira os blobs para o disco local. Para cada blob que deseja baixar, defina o nome e a chamada [Get-AzStorageBlobContent](/powershell/module/az.storage/get-AzStorageblobcontent) para transferir o blob.

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

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, transferiu ficheiros entre um disco local e o armazenamento de Blobs do Azure. Para saber mais sobre como utilizar o armazenamento de Blobs com o PowerShell, avance para Como utilizar o Azure PowerShell com o Armazenamento do Azure.

> [!div class="nextstepaction"]
> [Using Azure PowerShell with Azure Storage (Utilizar o Azure PowerShell com o Armazenamento do Azure)](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Referência de cmdlets do Armazenamento do Microsoft Azure PowerShell

* [Cmdlets do Armazenamento do PowerShell](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Explorador de Armazenamento do Microsoft Azure

* O [Explorador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.
