---
title: Armazenamento de Lagos Azure Gen2 PowerShell para ficheiros e ACLs (pré-visualização)
description: Utilize os cmdlets PowerShell para gerir diretórios e listas de controlo de acesso de ficheiros e diretórios (ACL) em contas de armazenamento que tenham espaço hierárquico (HNS) habilitado.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: a5ad4cbd11d6468413a43576e2156ee807067aa8
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153354"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Use powerShell para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2 (pré-visualização)

Este artigo mostra-lhe como usar o PowerShell para criar e gerir diretórios, ficheiros e permissões em contas de armazenamento que têm espaço hierárquico (HNS) habilitado. 

> [!IMPORTANT]
> O módulo PowerShell que está em destaque neste artigo está atualmente em pré-visualização pública.

[Gen1 para mapeamento Gen2](#gen1-gen2-map) | [Dar feedback](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição do Azure. Veja [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem espaço de nome hierárquico (HNS) ativado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.
> * .NET O quadro é de 4.7.2 ou superior instalado. Ver [Baixar .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * A versão PowerShell `5.1` ou superior.

## <a name="install-powershell-modules"></a>Instalar módulos PowerShell

1. Verifique se a versão da PowerShell que foi instalada é `5.1` ou superior utilizando o seguinte comando. 

    ```powershell
    echo $PSVersionTable.PSVersion.ToString() 
    ```
    
    Para atualizar a sua versão do PowerShell, consulte a atualização do [Windows PowerShell existente](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. Instale o mais recente módulo **PowershellGet.** Em seguida, feche e reabra a consola Powershell.

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

3.  Instale o módulo de pré-visualização **Az.Storage.**

    ```powershell
    install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.9.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

    Para obter mais informações sobre como instalar módulos PowerShell, consulte [Instalar o módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="connect-to-the-account"></a>Ligar à conta

Abra uma janela de comando Do Windows PowerShell e, em seguida, inscreva-se na sua subscrição Azure com o comando `Connect-AzAccount` e siga as instruções no ecrã.

```powershell
Connect-AzAccount
```

Se a sua identidade estiver associada a mais de uma subscrição, então delineie a sua subscrição ativa para a subscrição da conta de armazenamento que pretende criar e gerir diretórios. Neste exemplo, substitua o valor `<subscription-id>` espaço reservado pela identificação da sua subscrição.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Em seguida, escolha como pretende que os seus comandos obtenham autorização para a conta de armazenamento. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Opção 1: Obter autorização utilizando o Diretório Ativo Azure (AD)

Com esta abordagem, o sistema garante que a sua conta de utilizador possui as atribuições adequadas de controlo de acesso (RBAC) e permissões ACL. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Opção 2: Obter autorização utilizando a chave da conta de armazenamento

Com esta abordagem, o sistema não verifica as permissões RBAC ou ACL.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-file-system"></a>Criar um sistema de ficheiros

Um sistema de ficheiros funciona como um recipiente para os seus ficheiros. Pode criar um utilizando o `New-AzDatalakeGen2FileSystem` cmdlet. 

Este exemplo cria um sistema de ficheiros denominado `my-file-system`.

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório utilizando o `New-AzDataLakeGen2Item` cmdlet. 

Este exemplo adiciona um diretório chamado `my-directory` a um sistema de ficheiros.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

Este exemplo adiciona o mesmo diretório, mas também define as permissões, umask, valores de propriedade e valores de metadados. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Mostrar propriedades de diretório

Este exemplo obtém um diretório utilizando o `Get-AzDataLakeGen2Item` cmdlet, e depois imprime valores de propriedade para a consola.

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

## <a name="rename-or-move-a-directory"></a>Mudar o nome ou mover um diretório

Mude o nome ou mova um diretório utilizando o `Move-AzDataLakeGen2Item` cmdlet.

Este exemplo renomea um diretório do nome `my-directory` para o nome `my-new-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

Este exemplo move um diretório chamado `my-directory` para um subdiretório de `my-directory-2` chamado `my-subdirectory`. Este exemplo aplica-se também a um umask ao subdiretório.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2 -Umask --------x -PathRenameMode Posix
```

## <a name="delete-a-directory"></a>Eliminar um diretório

Elimine um diretório utilizando o `Remove-AzDataLakeGen2Item` cmdlet.

Este exemplo elimina um diretório chamado `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

Pode utilizar o parâmetro `-Force` para remover o ficheiro sem um aviso.

## <a name="download-from-a-directory"></a>Baixar de um diretório

Faça o download de um ficheiro de um diretório utilizando o `Get-AzDataLakeGen2ItemContent` cmdlet.

Este exemplo descarrega um ficheiro chamado `upload.txt` de um diretório chamado `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Enumerar o conteúdo de um diretório utilizando o `Get-AzDataLakeGen2ChildItem` cmdlet.

Este exemplo lista o conteúdo de um diretório chamado `my-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname
```

Este exemplo não devolve valores para as propriedades `ACL`, `Permissions`, `Group`e `Owner`. Para obter esses valores, utilize o parâmetro `-FetchPermission`. 

O exemplo seguinte lista o conteúdo do mesmo diretório, mas também utiliza o parâmetro `-FetchPermission` para devolver valores para as propriedades `ACL`, `Permissions`, `Group`e `Owner`. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchPermission
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

Para listar o conteúdo de um sistema de ficheiros, omita o `-Path` parâmetro do comando.

## <a name="upload-a-file-to-a-directory"></a>Faça upload de um ficheiro para um diretório

Faça o upload de um ficheiro para um diretório utilizando o `New-AzDataLakeGen2Item` cmdlet.

Este exemplo envia um ficheiro chamado `upload.txt` a um diretório chamado `my-directory`. 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

Este exemplo carrega o mesmo ficheiro, mas depois define as permissões, umask, valores de propriedade e valores de metadados do ficheiro de destino. Este exemplo também imprime estes valores para a consola.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.File.Metadata
$file1.File.Properties
```

## <a name="show-file-properties"></a>Mostrar propriedades de ficheiros

Este exemplo recebe um ficheiro utilizando o `Get-AzDataLakeGen2Item` cmdlet, e depois imprime valores de propriedade para a consola.

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

Elimine um ficheiro utilizando o `Remove-AzDataLakeGen2Item` cmdlet.

Este exemplo elimina um ficheiro chamado `upload.txt`. 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

Pode utilizar o parâmetro `-Force` para remover o ficheiro sem um aviso.

## <a name="manage-access-permissions"></a>Gerir permissões de acesso

Pode obter, definir e atualizar permissões de acesso de diretórios e ficheiros.

> [!NOTE]
> Se estiver a utilizar o Azure Ative Directory (Azure AD) para autorizar comandos, certifique-se de que o seu diretor de segurança foi atribuído ao papel de [Proprietário de Dados de Armazenamento Blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de [acesso em Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-directory-and-file-permissions"></a>Obtenha permissões de diretório e arquivo

Obtenha o ACL de um diretório ou ficheiro utilizando o `Get-AzDataLakeGen2Item`cmdlet.

Este exemplo obtém o ACL de um **diretório**, e depois imprime o ACL para a consola.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Este exemplo obtém o ACL de um **ficheiro** e depois imprime o ACL para a consola.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

A imagem seguinte mostra a saída depois de obter o ACL de um diretório.

![Obtenha saída ACL](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

Neste exemplo, o utilizador próprio leu, escreve e executa permissões. O grupo próprio só leu e executou permissões. Para obter mais informações sobre as listas de controlo de acesso, consulte [o controlo de acesso em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Definir diretórios e permissões de ficheiros

Utilize o `New-AzDataLakeGen2ItemAclObject` cmdlet para criar um ACL para o utilizador próprio, grupo próprio ou outros utilizadores. Em seguida, use o `Update-AzDataLakeGen2Item` cmdlet para cometer o ACL.

Este exemplo define o ACL num **diretório** para o utilizador próprio, grupo de possuir ou outros utilizadores, e depois imprime o ACL para a consola.

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
Este exemplo define o ACL num **ficheiro** para o utilizador próprio, grupo próprio ou outros utilizadores, e depois imprime o ACL para a consola.

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

A imagem seguinte mostra a saída após a definição do ACL de um ficheiro.

![Obtenha saída ACL](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

Neste exemplo, o utilizador próprio e o grupo de possuir apenas leram e escreveram permissões. Todos os outros utilizadores têm permissões de escrita e execução. Para obter mais informações sobre as listas de controlo de acesso, consulte [o controlo de acesso em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Atualizar diretórios e permissões de ficheiros

Utilize o `Get-AzDataLakeGen2Item` cmdlet para obter o ACL de um diretório ou ficheiro. Em seguida, use o `New-AzDataLakeGen2ItemAclObject` cmdlet para criar uma nova entrada ACL. Utilize o `Update-AzDataLakeGen2Item` cmdlet para aplicar o novo ACL.

Este exemplo dá a um utilizador a permissão de escrita e execução num diretório.

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

Este exemplo dá a um utilizador a permissão de escrita e execução num ficheiro.

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

### <a name="set-permissions-on-all-items-in-a-file-system"></a>Definir permissões em todos os itens de um sistema de ficheiros

Pode utilizar o `Get-AzDataLakeGen2Item` e o parâmetro `-Recurse` juntamente com o `Update-AzDataLakeGen2Item` cmdlet para configurar o ACL de todos os diretórios e ficheiros num sistema de ficheiros. 

```powershell
$filesystemName = "my-file-system"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse -FetchPermission | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Gen1 para Gen2 Mapping

A tabela seguinte mostra como os cmdlets utilizados para o mapa Gen1 de Armazenamento de Data Lake para os cmdlets para Data Lake Storage Gen2.

|Data Lake Storage Gen1 cmdlet| Data Lake Storage Gen2 cmdlet| Notas |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Por predefinição, o Get-AzDataLakeGen2ChildItem cmdlet apenas lista os itens infantis de primeiro nível. O parâmetro -Recurse lista itens infantis recursivamente. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Os itens de saída do Get-AzDataLakeGen2Item cmdlet têm estas propriedades: Acl, Proprietário, Grupo, Permissão.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|O Get-AzDataLakeGen2FileContent cmdlet descarregue o conteúdo do ficheiro para o ficheiro local.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|Novo AzDataLakeGen2Item|Este cmdlet envia o conteúdo do novo ficheiro a partir de um ficheiro local.|
|Remover-AzDataLakeStoreItem|Remover-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|O AzDataLakeGen2Item cmdlet atualiza apenas um item e não recursivamente. Se pretender atualizar de forma recursiva, lista os itens utilizando o cmdlet Get-AzDataLakeStoreChildItem, em seguida, o gasoduto para o AzDataLakeGen2Item cmdlet.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|O Cmdlet Get-AzDataLakeGen2Item reportará um erro se o item não existir.|



## <a name="see-also"></a>Veja também

* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Utilização da Azure PowerShell com armazenamento Azure.](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Armazenamento PowerShell cmdlets](/powershell/module/az.storage).

