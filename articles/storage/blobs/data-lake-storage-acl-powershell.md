---
title: Use powerShell para gerir ACLs em Azure Data Lake Storage Gen2
description: Utilize cmdlets PowerShell para gerir listas de controlo de acesso (ACL) em contas de armazenamento que tenham espaço hierárquico (HNS) ativado.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dd522355d30564d84fec15bdc57c7397c1e6cfe4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104702546"
---
# <a name="use-powershell-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Use powerShell para gerir ACLs em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar o PowerShell para obter, definir e atualizar as listas de controlo de acesso de diretórios e ficheiros. 

A herança ACL já está disponível para novos itens infantis que são criados sob um diretório de pais. Mas também pode adicionar, atualizar e remover ACLs de forma recorrente nos itens infantis existentes de um diretório parental sem ter que fazer estas alterações individualmente para cada item de criança. 

[Referência](/powershell/module/Az.Storage/)  |  Amostras recursivas [da ACL](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)  |  [Dar feedback](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento que tem espaço hierárquico de nome (HNS) ativado. Siga [estas](create-data-lake-storage-account.md) instruções para criar uma.

- Versão Azure CLI `2.6.0` ou superior.

- Uma das seguintes permissões de segurança:

  - Um diretor de [segurança](../../role-based-access-control/overview.md#security-principal) Azure Ative (AD) provisionado que foi atribuído a função de Proprietário de [Dados blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) de armazenamento no âmbito do contentor-alvo, grupo de recursos-mãe ou subscrição.  

  - Utilizador próprio do contentor-alvo ou diretório ao qual planeia aplicar as definições ACL. Para definir os ACLs de forma recorrente, isto inclui todos os itens para crianças no contentor-alvo ou diretório.
  
  - Chave da conta de armazenamento.

## <a name="install-the-powershell-module"></a>Instalar o módulo do PowerShell

1. Verifique se a versão do PowerShell que foi instalada é `5.1` ou superior utilizando o seguinte comando.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```

   Para atualizar a sua versão do PowerShell, consulte [a atualização do Windows PowerShell existente](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)

2. Instale o módulo **Az.Storage.**

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Para obter mais informações sobre como instalar módulos PowerShell, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="connect-to-the-account"></a>Ligar à conta

Escolha como pretende que os seus comandos obtenham autorização para a conta de armazenamento. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Opção 1: Obter autorização através da utilização do Diretório Ativo Azure (AD)

> [!NOTE]
> Se estiver a utilizar o Azure Ative Directory (Azure AD) para autorizar o acesso, certifique-se de que o seu principal de segurança foi atribuído à [função de Proprietário de Dados blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua alteração, consulte o modelo de controlo de  [acesso na Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

Com esta abordagem, o sistema garante que a sua conta de utilizador tem as atribuições adequadas de controlo de acesso baseado em funções (Azure RBAC) e permissões ACL.

1. Abra uma janela de comando Do Windows PowerShell e, em seguida, inscreva-se na subscrição do Azure com o `Connect-AzAccount` comando e siga as instruções no ecrã.

   ```powershell
   Connect-AzAccount
   ```

2. Se a sua identidade estiver associada a mais de uma subscrição, então desa estacie a sua subscrição ativa para a subscrição da conta de armazenamento que pretende criar e gerir diretórios. Neste exemplo, substitua o `<subscription-id>` valor do espaço reservado pelo ID da sua subscrição.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ``` 

3. Obtenha o contexto da conta de armazenamento.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
   ```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Opção 2: Obter autorização utilizando a chave da conta de armazenamento

Com esta abordagem, o sistema não verifica permissões Azure RBAC ou ACL. Obtenha o contexto da conta de armazenamento usando uma chave de conta.

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
```

## <a name="get-acls"></a>Obter ACLs

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

## <a name="set-acls"></a>Definir ACLs

Quando *configurar* um ACL, **substitui-se** toda a ACL, incluindo todas as suas entradas. Se pretender alterar o nível de permissão de um principal de segurança ou adicionar um novo princípio de segurança à ACL sem afetar outras entradas existentes, deverá *atualizar* o ACL em vez disso. Para atualizar um ACL em vez de substituí-lo, consulte a secção [ACLs update](#update-acls) deste artigo.  

Se optar por *definir* a ACL, deve adicionar uma entrada para o utilizador próprio, uma entrada para o grupo de propriedade e uma entrada para todos os outros utilizadores. Para saber mais sobre o utilizador próprio, o grupo de propriedade, e todos os outros utilizadores, consulte [Utilizadores e identidades.](data-lake-storage-access-control.md#users-and-identities)

Esta secção mostra-lhe como:

- Definir um ACL
- Definir ACLs recursivamente

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

> [!NOTE]
> Se pretender definir uma entrada ACL **predefinitiva,** utilize o parâmetro **-DefaultScope** quando executar o comando **Set-AzDataGen2ItemAclObject.** Por exemplo: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`. 

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

> [!NOTE]
> Para um conjunto da ACL de um grupo ou utilizador específico, utilize os respetivos IDs de objeto. Por exemplo, `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ou `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

A imagem a seguir mostra a saída após a definição do ACL de um ficheiro.

![Obtenha a saída ACL para arquivo](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

Neste exemplo, o próprio utilizador e o grupo de proprietária apenas leram e escreveram permissões. Todos os outros utilizadores têm permissões de escrita e execução. Para obter mais informações sobre as listas de controlo de acesso, consulte [o controlo de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-acls-recursively"></a>Definir ACLs recursivamente

Desaprova os ACLs de forma recorrente utilizando o **cmdlet Set-AzDataGen2Acsive.**

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Estas entradas dão ao utilizador que lê, escreve e executa permissões, dá ao grupo que só lê e executa permissões, e não dá acesso a todos os outros. A última entrada ACL neste exemplo dá a um utilizador específico com o objeto ID "xxxxxxx-xxxx-xxxx-xxxx-xxxx" ler e executar permissões.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Se pretender definir uma entrada ACL **predefinitiva,** utilize o parâmetro **-DefaultScope** quando executar o comando **Set-AzDataGen2ItemAclObject.** Por exemplo: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Para ver um exemplo que define os ACLs de forma recursiva em lotes especificando um tamanho de lote, consulte o artigo de referência [set-AzDataLakeGen2AclRecursive.](/powershell/module/az.storage/set-azdatalakegen2aclrecursive)

## <a name="update-acls"></a>Atualizar ACLs

Quando *atualiza* um ACL, modifica o ACL em vez de substituir o ACL. Por exemplo, pode adicionar um novo princípio de segurança à ACL sem afetar outros princípios de segurança listados na ACL.  Para substituir o ACL em vez de atualizá-lo, consulte a secção [De Definição de ACLs](#set-acls) deste artigo.

Para atualizar um ACL, crie um novo objeto ACL com a entrada ACL que pretende atualizar e, em seguida, utilize esse objeto na atualização da operação ACL. Não obtenha o ACL existente, basta fornecer entradas ACL para serem atualizadas.

Esta secção mostra-lhe como:

- Atualizar um ACL
- Atualizar ACLs recursivamente

### <a name="update-an-acl"></a>Atualizar um ACL

Primeiro, pegue a ACL. Em seguida, utilize o `set-AzDataLakeGen2ItemAclObject` cmdlet para adicionar ou atualizar uma entrada ACL. Utilize o `Update-AzDataLakeGen2Item` cmdlet para comprometer a ACL.

Este exemplo cria ou atualiza o ACL num **diretório** para um utilizador.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

> [!NOTE]
> Se pretender atualizar uma entrada ACL **predefinitiva,** utilize o parâmetro **-DefaultScope** quando executar o comando **Set-AzDataGen2ItemAclObject.** Por exemplo: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -DefaultScope`.

### <a name="update-acls-recursively"></a>Atualizar ACLs recursivamente

Atualize os ACLs de forma recorrente utilizando o **cmdlet Update-AzDataGen2Acsive.**

Este exemplo atualiza uma entrada ACL com permissão de escrita.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Para um conjunto da ACL de um grupo ou utilizador específico, utilize os respetivos IDs de objeto. Por exemplo, `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ou `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Para ver um exemplo que atualiza os ACLs de forma recursiva em lotes especificando um tamanho de lote, consulte o artigo de referência [Update-AzDataLakeGen2Acsive.](/powershell/module/az.storage/update-azdatalakegen2aclrecursive)

## <a name="remove-acl-entries"></a>Remover entradas ACL

Esta secção mostra-lhe como:

- Remover uma entrada ACL
- Remover as entradas ACL recursivamente

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

### <a name="remove-acl-entries-recursively"></a>Remover as entradas ACL recursivamente

Pode remover uma ou mais entradas ACL de forma recorrente. Para remover uma entrada ACL, crie um novo objeto ACL para a entrada ACL ser removida e, em seguida, utilize esse objeto na remoção da operação ACL. Não obtenha o ACL existente, basta fornecer as entradas ACL para serem removidas.

Remova as entradas ACL utilizando o **cmdlet Remove-AzDataLakeGen2Acsive.**

Este exemplo remove uma entrada ACL do diretório de raiz do recipiente.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> Se pretender remover uma entrada ACL **predefinitiva,** utilize o parâmetro **-DefaultScope** quando executar o comando **Set-AzDataGen2ItemAclObject.** Por exemplo: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

Para ver um exemplo que remove os ACLs de forma recorrente em lotes especificando um tamanho do lote, consulte o artigo de referência [Remove-AzDataLakeGen2Acsive.](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive)

## <a name="recover-from-failures"></a>Recuperar de falhas

Pode encontrar erros de tempo de execução ou permissão ao modificar os ACLs de forma recorrente. 

Para erros de tempo de execução, reinicie o processo desde o início. Podem ocorrer erros de permissão se o diretor de segurança não tiver permissão suficiente para modificar a ACL de um diretório ou ficheiro que está na hierarquia do diretório a ser modificado. Aborde a questão da permissão e, em seguida, opte por retomar o processo a partir do ponto de falha, utilizando um token de continuação, ou reiniciar o processo desde o início. Não tens de usar o símbolo de continuação se preferires recomeçar desde o início. Pode re-aplicar as entradas ACL sem qualquer impacto negativo.

Este exemplo devolve os resultados à variável e, em seguida, os tubos falharam as entradas para uma tabela formatada.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

Com base na saída da tabela, pode corrigir quaisquer erros de permissão e, em seguida, retomar a execução utilizando o token de continuação.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

Para ver um exemplo que define os ACLs de forma recursiva em lotes especificando um tamanho de lote, consulte o artigo de referência [set-AzDataLakeGen2AclRecursive.](/powershell/module/az.storage/set-azdatalakegen2aclrecursive)

Se quiser que o processo seja concluído ininterruptamente por erros de permissão, pode especificar isso.

Este exemplo utiliza o `ContinueOnFailure` parâmetro de modo a que a execução continue mesmo que a operação encontre um erro de permissão. 

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

Para ver um exemplo que define os ACLs de forma recursiva em lotes especificando um tamanho de lote, consulte o artigo de referência [set-AzDataLakeGen2AclRecursive.](/powershell/module/az.storage/set-azdatalakegen2aclrecursive)

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Ver também

- [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Cmdlets do Armazenamento do PowerShell](/powershell/module/az.storage)
- [Modelo de controlo de acesso em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Listas de controlo de acesso (ACLs) em Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
