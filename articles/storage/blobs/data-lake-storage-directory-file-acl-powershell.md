---
title: Azure Data Lake Storage Gen2 PowerShell para arquivos & ACLs (versão prévia)
description: Use os cmdlets do PowerShell para gerenciar diretórios e listas de controle de acesso (ACL) de arquivos e diretórios em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: be5a1dce89219957f98c585d8e531c369e2f23c4
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75690421"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Usar o PowerShell para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2 (versão prévia)

Este artigo mostra como usar o PowerShell para criar e gerenciar diretórios, arquivos e permissões em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado. 

> [!IMPORTANT]
> O módulo do PowerShell que é apresentado neste artigo está atualmente em visualização pública.

[Gen1 para mapeamento de Gen2](#gen1-gen2-map) | [fornecer comentários](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem o namespace hierárquico (HNS) habilitado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.
> * O .NET Framework é 4.7.2 ou superior instalado. Consulte [baixar .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * Versão do PowerShell `5.1` ou superior.

## <a name="install-powershell-modules"></a>Instalar módulos do PowerShell

1. Verifique se a versão do PowerShell instalada é `5.1` ou superior usando o comando a seguir. 

    ```powershell
    echo $PSVersionTable.PSVersion.ToString() 
    ```
    
    Para atualizar sua versão do PowerShell, consulte [atualizando o Windows PowerShell existente](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. Instale o módulo **PowershellGet** mais recente. Em seguida, feche e reabra o console do PowerShell.

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

3.  Instale o módulo de visualização **AZ. Storage** .

    ```powershell
    install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.9.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

    Para obter mais informações sobre como instalar módulos do PowerShell, consulte [instalar o Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="connect-to-the-account"></a>Conectar-se à conta

Abra uma janela de comando do Windows PowerShell e entre em sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela.

```powershell
Connect-AzAccount
```

Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para a assinatura da conta de armazenamento na qual você deseja criar e gerenciar diretórios. Neste exemplo, substitua o valor do espaço reservado `<subscription-id>` pela ID da sua assinatura.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Em seguida, escolha como você deseja que seus comandos obtenham autorização para a conta de armazenamento. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Opção 1: obter autorização usando o Azure Active Directory (AD)

Com essa abordagem, o sistema garante que sua conta de usuário tenha as atribuições de RBAC (controle de acesso baseado em função) apropriadas e permissões de ACL. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Opção 2: obter autorização usando a chave da conta de armazenamento

Com essa abordagem, o sistema não verifica as permissões de RBAC ou ACL de um recurso.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-file-system"></a>Criar um sistema de arquivos

Um sistema de arquivos atua como um contêiner para seus arquivos. Você pode criar um usando o cmdlet `New-AzDatalakeGen2FileSystem`. 

Este exemplo cria um sistema de arquivos chamado `my-file-system`.

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório usando o cmdlet `New-AzDataLakeGen2Item`. 

Este exemplo adiciona um diretório chamado `my-directory` a um sistema de arquivos.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

Este exemplo adiciona o mesmo diretório, mas também define as permissões, umask, valores de propriedade e valores de metadados. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Mostrar propriedades do diretório

Este exemplo obtém um diretório usando o cmdlet `Get-AzDataLakeGen2Item` e, em seguida, imprime os valores de propriedade no console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Directory.PathProperties.Group
$dir.Directory.PathProperties.Owner
$dir.Directory.Metadata
$dir.Directory.Properties
```

## <a name="rename-or-move-a-directory"></a>Renomear ou mover um diretório

Renomeie ou mova um diretório usando o cmdlet `Move-AzDataLakeGen2Item`.

Este exemplo renomeia um diretório do nome `my-directory` para o nome `my-new-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

Este exemplo move um diretório chamado `my-directory` para um subdiretório de `my-directory-2` chamado `my-subdirectory`. Este exemplo também aplica um umask ao subdiretório.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2 -Umask --------x -PathRenameMode Posix
```

## <a name="delete-a-directory"></a>Eliminar um diretório

Exclua um diretório usando o cmdlet `Remove-AzDataLakeGen2Item`.

Este exemplo exclui um diretório chamado `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

Você pode usar o parâmetro `-Force` para remover o arquivo sem um prompt.

## <a name="download-from-a-directory"></a>Baixar de um diretório

Baixe um arquivo de um diretório usando o cmdlet `Get-AzDataLakeGen2ItemContent`.

Este exemplo baixa um arquivo chamado `upload.txt` de um diretório chamado `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Liste o conteúdo de um diretório usando o cmdlet `Get-AzDataLakeGen2ChildItem`.

Este exemplo lista o conteúdo de um diretório chamado `my-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname
```

Este exemplo não retorna valores para as propriedades `ACL`, `Permissions`, `Group`e `Owner`. Para obter esses valores, use o parâmetro `-FetchPermission`. 

O exemplo a seguir lista o conteúdo do mesmo diretório, mas também usa o parâmetro `-FetchPermission` para retornar valores para as propriedades `ACL`, `Permissions`, `Group`e `Owner`. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchPermission
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

Para listar o conteúdo de um sistema de arquivos, omita o parâmetro `-Path` do comando.

## <a name="upload-a-file-to-a-directory"></a>Carregar um arquivo em um diretório

Carregue um arquivo em um diretório usando o cmdlet `New-AzDataLakeGen2Item`.

Este exemplo carrega um arquivo chamado `upload.txt` em um diretório chamado `my-directory`. 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

Este exemplo carrega o mesmo arquivo, mas, em seguida, define as permissões, umask, valores de propriedade e valores de metadados do arquivo de destino. Este exemplo também imprime esses valores no console.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.File.Metadata
$file1.File.Properties
```

## <a name="show-file-properties"></a>Mostrar propriedades do arquivo

Este exemplo obtém um arquivo usando o cmdlet `Get-AzDataLakeGen2Item` e, em seguida, imprime os valores de propriedade no console.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.File.PathProperties.Group
$file.File.PathProperties.Owner
$file.File.Metadata
$file.File.Properties
```

## <a name="delete-a-file"></a>Eliminar um ficheiro

Exclua um arquivo usando o cmdlet `Remove-AzDataLakeGen2Item`.

Este exemplo exclui um arquivo chamado `upload.txt`. 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

Você pode usar o parâmetro `-Force` para remover o arquivo sem um prompt.

## <a name="manage-access-permissions"></a>Gerenciar permissões de acesso

Você pode obter, definir e atualizar as permissões de acesso de diretórios e arquivos.

### <a name="get-directory-and-file-permissions"></a>Obter permissões de diretório e arquivo

Obtenha a ACL de um diretório ou arquivo usando o cmdlet `Get-AzDataLakeGen2Item`.

Este exemplo obtém a ACL de um **diretório**e, em seguida, imprime a ACL no console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Este exemplo obtém a ACL de um **arquivo** e, em seguida, imprime a ACL no console.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

A imagem a seguir mostra a saída depois de obter a ACL de um diretório.

![Obter saída de ACL](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

Neste exemplo, o usuário proprietário tem permissões de leitura, gravação e execução. O grupo proprietário tem apenas permissões de leitura e execução. Para obter mais informações sobre listas de controle de acesso, consulte [Access Control in Azure data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Definir permissões de diretório e arquivo

Use o cmdlet `New-AzDataLakeGen2ItemAclObject` para criar uma ACL para o usuário proprietário, o grupo proprietário ou outros usuários. Em seguida, use o cmdlet `Update-AzDataLakeGen2Item` para confirmar a ACL.

Este exemplo define a ACL em um **diretório** para o usuário proprietário, o grupo proprietário ou outros usuários e, em seguida, imprime a ACL no console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
Este exemplo define a ACL em um **arquivo** para o usuário proprietário, o grupo proprietário ou outros usuários e, em seguida, imprime a ACL no console.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

A imagem a seguir mostra a saída depois de definir a ACL de um arquivo.

![Obter saída de ACL](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

Neste exemplo, o usuário proprietário e o grupo proprietário têm apenas permissões de leitura e gravação. Todos os outros usuários têm permissões de gravação e execução. Para obter mais informações sobre listas de controle de acesso, consulte [Access Control in Azure data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Atualizar permissões de diretório e arquivo

Use o cmdlet `Get-AzDataLakeGen2Item` para obter a ACL de um diretório ou arquivo. Em seguida, use o cmdlet `New-AzDataLakeGen2ItemAclObject` para criar uma nova entrada de ACL. Use o cmdlet `Update-AzDataLakeGen2Item` para aplicar a nova ACL.

Este exemplo fornece permissão de gravação e execução de um usuário em um diretório.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Get the directory ACL
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

# To avoid duplicate ACL, remove the ACL entries that will be added later.
foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "group" -and $a.DefaultScope -eq $true-and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}

# Add ACL Entries
$aclnew = New-AzDataLakeGen2ItemAclObject -AccessControlType group -EntityId $id -Permission "-wx" -DefaultScope -InputObject $aclnew

# Update ACL on server
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew  

```

Este exemplo fornece permissão de gravação e execução de um usuário em um arquivo.

```powershell
$filesystemName = "my-file-system"
$fileName = "my-directory/upload.txt"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Get the file ACL
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName).ACL

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

# To avoid duplicate ACL, remove the ACL entries that will be added later.
foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "group" -and $a.DefaultScope -eq $true-and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}

# Add ACL Entries
$aclnew = New-AzDataLakeGen2ItemAclObject -AccessControlType group -EntityId $id -Permission "-wx" -DefaultScope -InputObject $aclnew

# Update ACL on server
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName -Acl $aclnew 

```

### <a name="set-permissions-on-all-items-in-a-file-system"></a>Definir permissões em todos os itens em um sistema de arquivos

Você pode usar o `Get-AzDataLakeGen2Item` e o parâmetro `-Recurse` junto com o cmdlet `Update-AzDataLakeGen2Item` para recursivamente definir a ACL de todos os diretórios e arquivos em um sistema de arquivos. 

```powershell
$filesystemName = "my-file-system"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Mapeamento de Gen1 para Gen2

A tabela a seguir mostra como os cmdlets usados para Data Lake Storage Gen1 são mapeados para os cmdlets para Data Lake Storage Gen2.

|Data Lake Storage Gen1 cmdlet| Data Lake Storage Gen2 cmdlet| Notas |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Por padrão, o cmdlet Get-AzDataLakeGen2ChildItem lista apenas os itens filho de primeiro nível. O parâmetro-Recurse lista os itens filhos recursivamente. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Os itens de saída do cmdlet Get-AzDataLakeGen2Item têm estas propriedades: ACL, proprietário, grupo, permissão.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|O cmdlet Get-AzDataLakeGen2FileContent baixa o conteúdo do arquivo para o arquivo local.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Esse cmdlet carrega o novo conteúdo do arquivo de um arquivo local.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|O cmdlet Update-AzDataLakeGen2Item atualiza apenas um único item e não recursivamente. Se desejar atualizar recursivamente, liste os itens usando o cmdlet Get-AzDataLakeStoreChildItem e, em seguida, pipeline para o cmdlet Update-AzDataLakeGen2Item.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|O cmdlet Get-AzDataLakeGen2Item relatará um erro se o item não existir.|



## <a name="see-also"></a>Ver também

* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Usando Azure PowerShell com o armazenamento do Azure](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
* [Cmdlets do PowerShell de armazenamento](/powershell/module/az.storage).

