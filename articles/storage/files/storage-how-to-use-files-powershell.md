---
title: Início rápido para gerir partilhas de ficheiros do Azure com o Azure PowerShell
description: Utilize este início rápido para saber como gerir partilhas de ficheiros do Azure com o Azure PowerShell.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 802ad497f95a43665665d7e7dbd06c9081eba74a
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699509"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Início rápido: Criar e gerenciar um compartilhamento de arquivos do Azure com o Azure PowerShell 
Este guia orienta-o pelas noções básicas da utilização de [partilhas de ficheiros do Azure](storage-files-introduction.md) com o PowerShell. As partilhas de ficheiros do Azure são como outras partilhas de ficheiros, mas armazenadas na cloud e apoiadas pela plataforma do Azure. As partilhas de ficheiros do Azure suportam o protocolo SMB padrão do setor e permite a partilha de ficheiros entre várias máquinas, aplicações e instâncias. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você quiser instalar e usar o PowerShell localmente, este guia exigirá o módulo Azure PowerShell AZ versão 0,7 ou posterior. Para saber qual é a versão do módulo do Azure PowerShell que está a executar, execute `Get-Module -ListAvailable Az`. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar o PowerShell localmente, também terá de executar `Login-AzAccount` para iniciar sessão na sua conta do Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Se você ainda não tiver um grupo de recursos do Azure, poderá criar um novo com o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . 

O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na região E.U.A Leste:

```azurepowershell-interactive
New-AzResourceGroup `
    -Name myResourceGroup `
    -Location EastUS
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Uma conta de armazenamento é um conjunto partilhado de armazenamento que pode utilizar para implementar partilhas de ficheiros do Azure ou outros recursos de armazenamento, como blobs ou filas. Uma conta de armazenamento pode conter um número ilimitado de partilhas e uma partilha pode armazenar um número ilimitado de ficheiros, até aos limites de capacidade da conta de armazenamento.

Este exemplo cria uma conta de armazenamento usando o cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) . A conta de armazenamento é denominada *mystorageaccount\<número aleatório >* e uma referência a essa conta de armazenamento é armazenada na variável **$storageAcct**. Os nomes de contas de armazenamento têm de ser exclusivos, por isso, utilize `Get-Random` para anexar um número ao nome e torná-lo exclusivo. 

```azurepowershell-interactive 
$storageAcct = New-AzStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure
Agora, pode criar a sua primeira partilha de ficheiros do Azure. Você pode criar um compartilhamento de arquivos usando o cmdlet [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) . Este exemplo cria uma partilha com o nome `myshare`.

```azurepowershell-interactive
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

Os nomes das partilhas só podem ter letras minúsculas, números e hífenes, mas não podem começar com um hífen. Para obter detalhes completos sobre a nomenclatura de partilhas de ficheiros e ficheiros, veja [Naming and Referencing Shares, Directories, Files, and Metadata (Nomenclatura e Referência de Partilhas, Diretórios, Ficheiros e Metadados)](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Utilizar a partilha de ficheiros do Azure
O serviço Ficheiros do Azure fornece dois métodos para utilizar ficheiros e pastas na sua partilha de ficheiros do Azure : o [protocolo SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) padrão do setor e o [protocolo REST de Ficheiros](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Para montar uma partilha de ficheiros com SMB, veja o documento seguinte com base no seu SO:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Utilizar uma partilha de ficheiros do Azure com o protocolo REST de Ficheiros 
É possível trabalhar diretamente com o protocolo REST de arquivo diretamente (ou seja, programando chamadas HTTP REST por conta própria), mas a maneira mais comum de usar o protocolo REST de arquivo é usar o módulo Azure PowerShell, o [CLI do Azure](storage-how-to-use-files-cli.md)ou um SDK de armazenamento do Azure, todos os quais forneça um excelente wrapper em relação ao protocolo REST de arquivo na linguagem de script/programação de sua escolha.  

Na maioria dos casos, irá utilizar a partilha de ficheiros do Azure através do protocolo SMB, pois permite-lhe utilizar as aplicações e ferramentas existentes, mas existem vários motivos pelos quais é vantajoso utilizar a API REST de Ficheiros em vez do SMB, tais como:

- Se estiver a navegar na partilha de ficheiros a partir do PowerShell Cloud Shell (que não pode montar partilhas de ficheiros através de SMB).
- Se precisar de executar um script ou aplicação a partir de um cliente que não pode montar partilhas SMB, como clientes no local que não têm a porta 445 desbloqueada.
- Se estiver a tirar partido de recursos sem servidor, como o serviço [Funções do Azure](../../azure-functions/functions-overview.md). 

Os exemplos a seguir mostram como usar o módulo Azure PowerShell para manipular o compartilhamento de arquivos do Azure com o protocolo REST de arquivo. 

#### <a name="create-directory"></a>Criar diretório
Para criar um novo diretório chamado mydirectory na raiz do seu compartilhamento de arquivos do Azure, use o cmdlet [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory) .

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Carregar um ficheiro
Para demonstrar como carregar um arquivo usando o cmdlet [set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) , primeiro precisamos criar um arquivo dentro da unidade de rascunho do Cloud shell do PowerShell para carregar. 

Este exemplo coloca a data e hora atuais num novo ficheiro no disco e, em seguida, carrega o ficheiro para a partilha de ficheiros.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Se estiver a executar o PowerShell localmente, deve substituir `C:\Users\ContainerAdministrator\CloudDrive\` por um caminho existente no seu computador.

Depois de carregar o arquivo, você pode usar o cmdlet [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) para verificar se o arquivo foi carregado no compartilhamento de arquivos do Azure. 

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

#### <a name="download-a-file"></a>Transferir um ficheiro
Você pode usar o cmdlet [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) para baixar uma cópia do arquivo que você acabou de carregar na unidade de rascunho do seu Cloud Shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
     -Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

Depois de transferir o ficheiro, pode utilizar `Get-ChildItem` para ver se o ficheiro foi transferido para a unidade do PowerShell Cloud Shell.

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

#### <a name="copy-files"></a>Copiar ficheiros
Uma tarefa comum é copiar ficheiros de uma partilha de ficheiros para outra ou de/para um contentor de armazenamento de Blobs do Azure. Para demonstrar essa funcionalidade, você pode criar um novo compartilhamento e copiar o arquivo que você acabou de carregar para esse novo compartilhamento usando o cmdlet [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy) . 

```azurepowershell-interactive
New-AzStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Agora, se listar os ficheiros na nova partilha, deverá ver o ficheiro copiado.

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

Embora o `Start-AzStorageFileCopy` cmdlet seja conveniente para movimentações de arquivos ad hoc entre compartilhamentos de arquivos do Azure e contêineres de armazenamento de BLOBs do Azure, recomendamos AzCopy para movimentações maiores (em termos de número ou tamanho de arquivos que estão sendo movidos). Saiba mais sobre o [AzCopy para Windows](../common/storage-use-azcopy.md) e [AzCopy para Linux](../common/storage-use-azcopy-linux.md). O AzCopy tem de ser instalado localmente; não está disponível no Cloud Shell. 

## <a name="create-and-manage-share-snapshots"></a>Criar e gerir instantâneos de partilha
Uma tarefa útil adicional que pode fazer com uma partilha de ficheiros do Azure é criar instantâneos de partilha. Um instantâneo preserva um ponto no tempo para uma partilha de ficheiros do Azure. Os instantâneos de partilha são semelhantes às tecnologias de sistema operativo com as quais pode já estar familiarizado, tais como:
- [Serviço de Cópia Sombra de Volumes (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) para sistemas de ficheiros Windows, como NTFS e ReFS
- Instantâneos do [Gestor de Volumes Lógicos (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) para sistemas Linux
- Instantâneos do [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) para macOS. 
 Você pode criar um instantâneo de compartilhamento para um compartilhamento usando o `Snapshot` método no objeto do PowerShell para um compartilhamento de arquivos, que é recuperado com o cmdlet [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare) . 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name "myshare"
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>Procurar instantâneos de partilha
Pode procurar o conteúdo do instantâneo de partilha ao transmitir a referência do instantâneo (`$snapshot`) ao parâmetro `-Share` do cmdlet `Get-AzStorageFile`.

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Listar instantâneos de partilha
Pode ver a lista de instantâneos que criou para a sua partilha com o seguinte comando.

```azurepowershell-interactive
Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Restaurar a partir de um instantâneo de partilha
Pode restaurar um ficheiro com o comando `Start-AzStorageFileCopy` utilizado anteriormente. Para efeitos deste início rápido, vamos eliminar primeiro o ficheiro `SampleUpload.txt` carregado anteriormente para podermos restaurá-lo a partir do instantâneo.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"
 # Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Eliminar um instantâneo de partilha
Você pode excluir um instantâneo de compartilhamento usando o cmdlet [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare) , com a variável que contém `$snapshot` a referência ao `-Share` parâmetro.

```azurepowershell-interactive
Remove-AzStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>Limpar recursos
Quando terminar, você poderá usar o cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos relacionados. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Em alternativa, pode remover recursos um a um:

- Para remover as partilhas de ficheiros do Azure criadas para este início rápido.

    ```azurepowershell-interactive
    Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
        Remove-AzStorageShare -Context $storageAcct.Context -Name $_.Name
    }
    ```

- Para remover a própria conta de armazenamento (isto removerá implicitamente as partilhas de ficheiros do Azure, bem como quaisquer outros recursos de armazenamento que possa ter criado, como um contentor de armazenamento de Blobs do Azure).

    ```azurepowershell-interactive
    Remove-AzStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [O que são os Ficheiros do Azure?](storage-files-introduction.md)
