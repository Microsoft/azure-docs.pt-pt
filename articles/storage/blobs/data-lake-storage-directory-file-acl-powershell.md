---
title: Azure Data Lake Storage Gen2 PowerShell para ficheiros & ACLs
description: Utilize cmdlets PowerShell para gerir diretórios e listas de controlo de acesso a ficheiros e diretórios (ACL) em contas de armazenamento que tenham espaço hierárquico (HNS) ativado.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 08/26/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8083d42d9ce79bcf31e3875f2ff5d5f06970a7ff
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131518"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Use o PowerShell para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar o PowerShell para criar e gerir diretórios, ficheiros e permissões em contas de armazenamento que têm espaço hierárquico (HNS) ativado. 

[Referência](https://docs.microsoft.com/powershell/module/Az.Storage/)  |  Mapeamento da [Gen1 para a Gen2](#gen1-gen2-map)  |  [Dar feedback](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem espaço hierárquico de nome (HNS) ativado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.
> * .NET O quadro é 4.7.2 ou superior instalado. Consulte [o Download .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * Versão PowerShell `5.1` ou superior.

## <a name="install-the-powershell-module"></a>Instalar o módulo do PowerShell

1. Verifique se a versão do PowerShell que foi instalada é `5.1` ou superior utilizando o seguinte comando.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Para atualizar a sua versão do PowerShell, consulte [a atualização do Windows PowerShell existente](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)
    
2. Instale o módulo **Az.Storage.**

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Para obter mais informações sobre como instalar módulos PowerShell, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

## <a name="connect-to-the-account"></a>Ligar à conta

Abra uma janela de comando Do Windows PowerShell e, em seguida, inscreva-se na subscrição do Azure com o `Connect-AzAccount` comando e siga as instruções no ecrã.

```powershell
Connect-AzAccount
```

Se a sua identidade estiver associada a mais de uma subscrição, então desa estacie a sua subscrição ativa para a subscrição da conta de armazenamento que pretende criar e gerir diretórios. Neste exemplo, substitua o `<subscription-id>` valor do espaço reservado pelo ID da sua subscrição.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Em seguida, escolha como pretende que os seus comandos obtenham autorização para a conta de armazenamento. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Opção 1: Obter autorização através da utilização do Diretório Ativo Azure (AD)

Com esta abordagem, o sistema garante que a sua conta de utilizador tem as atribuições adequadas de controlo de acesso baseado em funções (Azure RBAC) e permissões ACL. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Opção 2: Obter autorização utilizando a chave da conta de armazenamento

Com esta abordagem, o sistema não verifica permissões Azure RBAC ou ACL.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-container"></a>Criar um contentor

Um contentor funciona como um sistema de ficheiros para os seus ficheiros. Pode criar um utilizando o `New-AzStorageContainer` cmdlet. 

Este exemplo cria um recipiente chamado `my-file-system` .

```powershell
$filesystemName = "my-file-system"
New-AzStorageContainer -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório utilizando o `New-AzDataLakeGen2Item` cmdlet. 

Este exemplo adiciona um diretório nomeado `my-directory` a um contentor.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

Este exemplo adiciona o mesmo diretório, mas também define as permissões, umask, valores de propriedade e metadados. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Mostrar propriedades de diretório

Este exemplo obtém um diretório usando o `Get-AzDataLakeGen2Item` cmdlet e, em seguida, imprime valores de propriedade para a consola.

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
> [!NOTE]
> Para obter o diretório de raiz do recipiente, omita o `-Path` parâmetro.

## <a name="rename-or-move-a-directory"></a>Mudar de nome ou mover um diretório

Mude o nome ou mova um diretório utilizando o `Move-AzDataLakeGen2Item` cmdlet.

Este exemplo renomea um diretório do nome `my-directory` para o nome `my-new-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Utilize o `-Force` parâmetro se quiser substituir sem indicações.

Este exemplo move um diretório nomeado `my-directory` para uma subdiretória de `my-directory-2` nome `my-subdirectory` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Eliminar um diretório

Elimine um diretório utilizando o `Remove-AzDataLakeGen2Item` cmdlet.

Este exemplo elimina um diretório chamado `my-directory` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

Pode utilizar o `-Force` parâmetro para remover o ficheiro sem uma solicitação.

## <a name="download-from-a-directory"></a>Descarregue a partir de um diretório

Faça o download de um ficheiro de um diretório utilizando o `Get-AzDataLakeGen2ItemContent` cmdlet.

Este exemplo descarrega um ficheiro chamado `upload.txt` de um diretório chamado `my-directory` . 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Listar o conteúdo de um diretório utilizando o `Get-AzDataLakeGen2ChildItem` cmdlet. Pode utilizar o parâmetro opcional `-OutputUserPrincipalName` para obter o nome (em vez do ID do objeto) dos utilizadores.

Este exemplo lista o conteúdo de um diretório denominado `my-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

O exemplo a seguir enumera o `ACL` , e as propriedades de cada item no `Permissions` `Group` `Owner` diretório. O `-FetchProperty` parâmetro é necessário para obter valores para o `ACL` imóvel. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

> [!NOTE]
> Para listar o conteúdo do diretório de raiz do recipiente, omita o `-Path` parâmetro.

## <a name="upload-a-file-to-a-directory"></a>Faça o upload de um ficheiro para um diretório

Faça o upload de um ficheiro para um diretório utilizando o `New-AzDataLakeGen2Item` cmdlet.

Este exemplo envia um ficheiro nomeado `upload.txt` para um diretório chamado `my-directory` . 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

Este exemplo carrega o mesmo ficheiro, mas depois define as permissões, umask, valores de propriedade e valores de metadados do ficheiro de destino. Este exemplo também imprime estes valores à consola.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

> [!NOTE]
> Para enviar um ficheiro para o diretório de raiz do recipiente, omita o `-Path` parâmetro.

## <a name="show-file-properties"></a>Mostrar propriedades de arquivo

Este exemplo obtém um ficheiro utilizando o `Get-AzDataLakeGen2Item` cmdlet e, em seguida, imprime valores de propriedade para a consola.

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

Elimine um ficheiro utilizando o `Remove-AzDataLakeGen2Item` cmdlet.

Este exemplo elimina um ficheiro chamado `upload.txt` . 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

Pode utilizar o `-Force` parâmetro para remover o ficheiro sem uma solicitação.

## <a name="manage-access-control-lists-acls"></a>Gerir listas de controlo de acesso (ACLs)

Pode obter, definir e atualizar permissões de acesso de diretórios e ficheiros.

> [!NOTE]
> Se estiver a utilizar o Azure Ative Directory (Azure AD) para autorizar comandos, certifique-se de que o seu diretor de segurança foi atribuído à [função de Proprietário de Dados blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de  [acesso na Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-an-acl"></a>Obtenha um ACL

Obtenha o ACL de um diretório ou ficheiro utilizando o `Get-AzDataLakeGen2Item` cmdlet.

Este exemplo obtém o ACL do diretório de raiz de um **recipiente** e, em seguida, imprime o ACL na consola.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Este exemplo obtém o ACL de um **diretório**, e depois imprime o ACL à consola.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Este exemplo obtém o ACL de um **ficheiro** e, em seguida, imprime o ACL à consola.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

A imagem a seguir mostra a saída depois de obter o ACL de um diretório.

![Obtenha a saída ACL para o diretório](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

Neste exemplo, o próprio utilizador leu, escreveu e executou permissões. O grupo de donos só leu e executou permissões. Para obter mais informações sobre as listas de controlo de acesso, consulte [o controlo de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>Definir um ACL

Utilize o `set-AzDataLakeGen2ItemAclObject` cmdlet para criar um ACL para o utilizador, grupo próprio ou outros utilizadores. Em seguida, use o `Update-AzDataLakeGen2Item` cmdlet para cometer a ACL.

Este exemplo coloca o ACL no diretório de raiz de um **recipiente** para o utilizador próprio, grupo próprio ou outros utilizadores, e depois imprime o ACL à consola.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Este exemplo define o ACL num **diretório** para o utilizador próprio, grupo próprio ou outros utilizadores, e depois imprime o ACL à consola.

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
Este exemplo define o ACL num **ficheiro** para o utilizador próprio, grupo próprio ou outros utilizadores, e depois imprime o ACL à consola.

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

A imagem a seguir mostra a saída após a definição do ACL de um ficheiro.

![Obtenha a saída ACL para arquivo](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

Neste exemplo, o próprio utilizador e o grupo de proprietária apenas leram e escreveram permissões. Todos os outros utilizadores têm permissões de escrita e execução. Para obter mais informações sobre as listas de controlo de acesso, consulte [o controlo de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="add-or-update-an-acl-entry"></a>Adicionar ou atualizar uma entrada ACL

Primeiro, pegue a ACL. Em seguida, utilize o `set-AzDataLakeGen2ItemAclObject` cmdlet para adicionar ou atualizar uma entrada ACL. Utilize o `Update-AzDataLakeGen2Item` cmdlet para comprometer a ACL.

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

### <a name="set-an-acl-recursively-preview"></a>Desaprote um ACL de forma recursiva (pré-visualização)

Pode adicionar, atualizar e remover ACLs de forma recorrente nos itens infantis existentes de um diretório de pais sem ter de escamar estas alterações individualmente para cada item infantil. Para obter mais informações, consulte [as listas de controlo de acesso (ACLs) de forma recorrente para Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

<a id="gen1-gen2-map"></a>

## <a name="gen1-to-gen2-mapping"></a>Gen1 para Gen2 Mapping

A tabela seguinte mostra como os cmdlets usados para o mapa de Data Lake Storage Gen1 para os cmdlets para data lake storage gen2.

|Cmdlet Gen1 de armazenamento de dados| Cmdlet Gen2 de armazenamento do lago de dados| Notas |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Por predefinição, o Get-AzDataLakeGen2ChildItem cmdlet apenas lista os itens infantis de primeiro nível. O parâmetro -Recurse lista itens infantis recursivamente. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Os itens de saída do Get-AzDataLakeGen2Item cmdlet têm estas propriedades: Acl, Owner, Group, Permission.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|O Get-AzDataLakeGen2FileContent cmdlet transferir conteúdo de ficheiro para o ficheiro local.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Este cmdlet envia o novo conteúdo do ficheiro a partir de um ficheiro local.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|O Update-AzDataLakeGen2Item cmdlet atualiza apenas um único item, e não recursivamente. Se pretender atualizar novamente, liste os itens utilizando o Get-AzDataLakeStoreChildItem cmdlet e, em seguida, conduza para o Update-AzDataLakeGen2Item cmdlet.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|O Get-AzDataLakeGen2Item cmdlet reportará um erro se o item não existir.|

## <a name="see-also"></a>Consulte também

* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Cmdlets do Armazenamento do PowerShell](/powershell/module/az.storage)
