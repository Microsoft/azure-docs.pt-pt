---
title: Armazenamento de Lagos Azure Gen2 PowerShell para ficheiros & ACLs
description: Utilize os cmdlets PowerShell para gerir diretórios e listas de controlo de acesso de ficheiros e diretórios (ACL) em contas de armazenamento que tenham espaço hierárquico (HNS) habilitado.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: c859176857f64559b9a2994c9cfc2d4ec5f61e57
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691084"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Use powerShell para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar o PowerShell para criar e gerir diretórios, ficheiros e permissões em contas de armazenamento que têm espaço hierárquico (HNS) habilitado. 

[Gen1 para mapeamento](#gen1-gen2-map) | Gen2[Dar feedback](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem espaço de nome hierárquico (HNS) ativado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.
> * .NET O quadro é de 4.7.2 ou superior instalado. Ver [Baixar .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * Versão PowerShell `5.1` ou superior.

## <a name="install-the-powershell-module"></a>Instale o módulo PowerShell

1. Verifique se a versão da PowerShell `5.1` que foi instalada é ou superior utilizando o seguinte comando.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Para atualizar a sua versão do PowerShell, consulte a atualização do [Windows PowerShell existente](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. Instale módulo **de armazenamento Az.Storage.**

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Para obter mais informações sobre como instalar módulos PowerShell, consulte [Instalar o módulo PowerShell Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="connect-to-the-account"></a>Ligar à conta

Abra uma janela de comando Do Windows PowerShell e, `Connect-AzAccount` em seguida, inscreva-se na sua subscrição Azure com o comando e siga as instruções no ecrã.

```powershell
Connect-AzAccount
```

Se a sua identidade estiver associada a mais de uma subscrição, então delineie a sua subscrição ativa para a subscrição da conta de armazenamento que pretende criar e gerir diretórios. Neste exemplo, substitua o valor do `<subscription-id>` espaço reservado pela identificação da sua subscrição.

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

Um sistema de ficheiros funciona como um recipiente para os seus ficheiros. Pode criar um utilizando `New-AzDatalakeGen2FileSystem` o cmdlet. 

Este exemplo cria um `my-file-system`sistema de ficheiros chamado .

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de `New-AzDataLakeGen2Item` diretório utilizando o cmdlet. 

Este exemplo adiciona um `my-directory` diretório nomeado para um sistema de ficheiros.

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

Este exemplo obtém um `Get-AzDataLakeGen2Item` diretório usando o cmdlet, e depois imprime valores de propriedade para a consola.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```

## <a name="rename-or-move-a-directory"></a>Mudar o nome ou mover um diretório

Mude o nome ou mova `Move-AzDataLakeGen2Item` um diretório utilizando o cmdlet.

Este exemplo renomeia um `my-directory` diretório `my-new-directory`do nome para o nome .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Utilize `-Force` o parâmetro se quiser substituir sem solicitações.

Este exemplo move um `my-directory` diretório nomeado `my-directory-2` para `my-subdirectory`um subdiretório de nome . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Eliminar um diretório

Elimine um diretório `Remove-AzDataLakeGen2Item` utilizando o cmdlet.

Este exemplo elimina um `my-directory`diretório chamado . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

Pode utilizar `-Force` o parâmetro para remover o ficheiro sem um aviso.

## <a name="download-from-a-directory"></a>Baixar de um diretório

Faça o download de um `Get-AzDataLakeGen2ItemContent` ficheiro de um diretório utilizando o cmdlet.

Este exemplo descarrega `upload.txt` um ficheiro chamado `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Enumerar o conteúdo de um `Get-AzDataLakeGen2ChildItem` diretório utilizando o cmdlet. Pode utilizar o parâmetro `-OutputUserPrincipalName` opcional para obter o nome (em vez do ID do objeto) dos utilizadores.

Este exemplo lista o conteúdo `my-directory`de um diretório chamado .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

O exemplo seguinte `ACL` `Permissions`enumera `Group`as `Owner` propriedades de cada item no diretório. O `-FetchProperty` parâmetro é necessário para obter `ACL` valores para a propriedade. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

Para listar o conteúdo de um `-Path` sistema de ficheiros, omita o parâmetro do comando.

## <a name="upload-a-file-to-a-directory"></a>Faça upload de um ficheiro para um diretório

Faça o upload de um `New-AzDataLakeGen2Item` ficheiro para um diretório utilizando o cmdlet.

Este exemplo envia um `upload.txt` ficheiro nomeado `my-directory`para um diretório chamado . 

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
$file1.Properties
$file1.Properties.Metadata

```

## <a name="show-file-properties"></a>Mostrar propriedades de ficheiros

Este exemplo recebe um `Get-AzDataLakeGen2Item` ficheiro utilizando o cmdlet e, em seguida, imprime valores de propriedade para a consola.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>Eliminar um ficheiro

Elimine um ficheiro `Remove-AzDataLakeGen2Item` utilizando o cmdlet.

Este exemplo elimina um `upload.txt`ficheiro denominado . 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

Pode utilizar `-Force` o parâmetro para remover o ficheiro sem um aviso.

## <a name="manage-access-permissions"></a>Gerir permissões de acesso

Pode obter, definir e atualizar permissões de acesso de sistemas de ficheiros, diretórios e ficheiros. Estas permissões são capturadas nas listas de controlo de acesso (ACLs).

> [!NOTE]
> Se estiver a utilizar o Azure Ative Directory (Azure AD) para autorizar comandos, certifique-se de que o seu diretor de segurança foi atribuído ao papel de [Proprietário de Dados de Armazenamento Blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de [acesso em Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-an-acl"></a>Obter um ACL

Obtenha o ACL de um diretório `Get-AzDataLakeGen2Item`ou ficheiro utilizando o cmdlet.

Este exemplo obtém o ACL de um sistema de **ficheiros** e, em seguida, imprime o ACL para a consola.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

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

### <a name="set-an-acl"></a>Definir um ACL

Utilize `set-AzDataLakeGen2ItemAclObject` o cmdlet para criar um ACL para o utilizador próprio, grupo de possuir ou outros utilizadores. Em seguida, `Update-AzDataLakeGen2Item` use o cmdlet para comprometer o ACL.

Este exemplo define o ACL num sistema de **ficheiros** para o utilizador, grupo próprio ou outros utilizadores, e depois imprime o ACL para a consola.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Este exemplo define o ACL num **diretório** para o utilizador próprio, grupo de possuir ou outros utilizadores, e depois imprime o ACL para a consola.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
Este exemplo define o ACL num **ficheiro** para o utilizador próprio, grupo próprio ou outros utilizadores, e depois imprime o ACL para a consola.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

A imagem seguinte mostra a saída após a definição do ACL de um ficheiro.

![Obtenha saída ACL](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

Neste exemplo, o utilizador próprio e o grupo de possuir apenas leram e escreveram permissões. Todos os outros utilizadores têm permissões de escrita e execução. Para obter mais informações sobre as listas de controlo de acesso, consulte [o controlo de acesso em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).


### <a name="set-acls-on-all-items-in-a-file-system"></a>Desloque os ACLs em todos os itens de um sistema de ficheiros

Pode utilizar `Get-AzDataLakeGen2Item` o `-Recurse` e o parâmetro `Update-AzDataLakeGen2Item` juntamente com o cmdlet para configurar o ACL para diretórios e ficheiros num sistema de ficheiros. 

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl

$Token = $Null
do
{
     $items = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse -ContinuationToken $Token    
     if($items.Length -le 0) { Break;}
     $items | Update-AzDataLakeGen2Item -Acl $acl
     $Token = $items[$items.Count -1].ContinuationToken;
}
While ($Token -ne $Null) 
```

### <a name="add-or-update-an-acl-entry"></a>Adicione ou atualize uma entrada ACL

Primeiro, pegue a ACL. Em seguida, `set-AzDataLakeGen2ItemAclObject` utilize o cmdlet para adicionar ou atualizar uma entrada ACL. Utilize `Update-AzDataLakeGen2Item` o cmdlet para comprometer o ACL.

Este exemplo cria ou atualiza o ACL num **diretório** para um utilizador.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

### <a name="remove-an-acl-entry"></a>Remover uma entrada ACL

Este exemplo remove uma entrada de um ACL existente.

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
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

## <a name="see-also"></a>Consulte também

* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Cmdlets do Armazenamento do PowerShell](/powershell/module/az.storage)
