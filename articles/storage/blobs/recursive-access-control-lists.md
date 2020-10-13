---
title: Desconfiem de ACLs de forma recursiva para a Azure Data Lake Storage Gen2 Microsoft Docs
description: Pode adicionar, atualizar e remover as listas de controlo de acesso de forma recorrente para os itens infantis existentes de um diretório principal.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 10/07/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: cedb6d162829d63aaac1a36b35abee1faeae3f1b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843401"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Definir listas de controlo de acesso (ACLs) recursivamente para Azure Data Lake Storage Gen2

A herança ACL já está disponível para novos itens infantis que são criados sob um diretório de pais. Pode também agora adicionar, atualizar e remover os ACLs de forma recorrente para os itens infantis existentes de um diretório dos pais sem ter de fazer estas alterações individualmente para cada item infantil.

> [!NOTE]
> A capacidade de definir listas de acesso de forma recorrente está em visualização pública e está disponível em todas as regiões.  

[Bibliotecas](#libraries)  |  [Amostras](#code-samples)  |  [Melhores práticas](#best-practice-guidelines)  |  [Dar feedback](#provide-feedback)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento que tem espaço hierárquico de nome (HNS) ativado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.

- As permissões corretas para executar o processo ACL recursivo. A permissão correta inclui qualquer uma das seguintes: 

  - Um diretor de [segurança](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) Azure Ative (AD) provisionado que foi atribuído a função de Proprietário de [Dados blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) de armazenamento no âmbito do contentor-alvo, grupo de recursos-mãe ou subscrição.   

  - Possuindo o utilizador do contentor-alvo ou diretório ao qual planeia aplicar o processo ACL recursivo. Isto inclui todos os itens para crianças no contentor-alvo ou diretório. 

- Uma compreensão de como os ACLs são aplicados a diretórios e ficheiros. Consulte [o controlo de acesso no Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Consulte a secção de **projeto** deste artigo para ver as orientações de instalação para PowerShell, .NET SDK e Python SDK.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Instale as bibliotecas necessárias.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Certifique-se de que a estrutura .NET está instalada. Consulte [o Download .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
2. Verifique se a versão do PowerShell que foi instalada é `5.1` ou superior utilizando o seguinte comando.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Para atualizar a sua versão do PowerShell, consulte [a atualização do Windows PowerShell existente](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell)
    
3. Instale a versão mais recente do módulo PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

4. Feche e, em seguida, reabra a consola PowerShell.

5. Instale o módulo de pré-visualização **Az.Storage.**

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Para obter mais informações sobre como instalar módulos PowerShell, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="net"></a>[.NET](#tab/dotnet)

1. Abra uma janela de comando (Por exemplo: Windows PowerShell).

2. A partir do seu diretório de projeto, instale o pacote de pré-visualização Azure.Storage.Files.DataLake utilizando o `dotnet add package` comando.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.5.0-preview.1 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. Adicione estas declarações usando o topo do seu ficheiro de código.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="python"></a>[Python](#tab/python)

1. Descarregue a biblioteca de [clientes Azure Data Lake Storage para Python.](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)

2. Instale a biblioteca que descarregou utilizando [o pip](https://pypi.org/project/pip/).

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

Adicione estas declarações de importação ao topo do seu ficheiro de código.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

---

## <a name="connect-to-your-account"></a>Ligar à sua conta

Pode ligar-se utilizando o Azure Ative Directory (AD) ou utilizando uma chave de conta. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

A tabela seguinte mostra cada uma das funções suportadas e a sua capacidade de definição ACL.

|Função|Capacidade de definição de ACL|
|--|--|
|[Proprietário dos Dados do Armazenamento de Blobs](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Todos os diretórios e ficheiros na conta.|
|[Contribuinte de Dados do Armazenamento de Blobs](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Apenas diretórios e ficheiros pertencentes ao diretor de segurança.|

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Opção 2: Obter autorização utilizando a chave da conta de armazenamento

Com esta abordagem, o sistema não verifica permissões Azure RBAC ou ACL.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

### <a name="net"></a>[.NET](#tab/dotnet)

Para utilizar os snippets neste artigo, terá de criar uma instância [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) que represente a conta de armazenamento.

#### <a name="connect-by-using-azure-active-directory-ad"></a>Conecte-se utilizando o Azure Ative Directory (AD)

Pode utilizar a biblioteca de [clientes de identidade Azure para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) para autenticar a sua aplicação com Azure AD.

Depois de instalar a embalagem, adicione esta declaração utilizando a declaração no topo do seu ficheiro de código.

```csharp
using Azure.Identity;
```

Arranja uma identificação com um cliente, um segredo de cliente, e uma identificação de inquilino. Para isso, consulte [Adquirir um token da Azure AD para autorizar pedidos de uma aplicação do cliente.](../common/storage-auth-aad-app.md) Como parte desse processo, terás de atribuir um dos seguintes papéis de [controlo de acesso baseado em funções (Azure RBAC)](../../role-based-access-control/overview.md) ao teu diretor de segurança. 

|Função|Capacidade de definição de ACL|
|--|--|
|[Proprietário dos Dados do Armazenamento de Blobs](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Todos os diretórios e ficheiros na conta.|
|[Contribuinte de Dados do Armazenamento de Blobs](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Apenas diretórios e ficheiros pertencentes ao diretor de segurança.|

Este exemplo cria um exemplo [de DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) usando um ID de cliente, um segredo de cliente e uma identificação de inquilino.  

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

``` 

#### <a name="connect-by-using-an-account-key"></a>Conecte-se usando uma chave de conta

Esta abordagem é a forma mais fácil de ligar a uma conta. 

Este exemplo cria uma instância [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) utilizando uma chave de conta.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

> [!NOTE]
> Para mais exemplos, consulte a biblioteca do [cliente de identidade Azure para obter](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) documentação .NET.

### <a name="python"></a>[Python](#tab/python)

Para utilizar os snippets neste artigo, terá de criar uma instância **DataLakeServiceClient** que represente a conta de armazenamento. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Conecte-se utilizando o Azure Ative Directory (AD)

Pode utilizar a biblioteca de [clientes de identidade Azure para python](https://pypi.org/project/azure-identity/) para autenticar a sua aplicação com Azure AD.

Este exemplo cria um exemplo **de DataLakeServiceClient** usando um ID de cliente, um segredo de cliente e uma identificação de inquilino.  Para obter estes valores, consulte [Adquirir um token da Azure AD para autorizar pedidos de uma aplicação do cliente.](../common/storage-auth-aad-app.md) Como parte desse processo, terás de atribuir um dos seguintes papéis de [controlo de acesso baseado em funções (Azure RBAC)](../../role-based-access-control/overview.md) ao teu diretor de segurança. 

|Função|Capacidade de definição de ACL|
|--|--|
|[Proprietário dos Dados do Armazenamento de Blobs](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Todos os diretórios e ficheiros na conta.|
|[Contribuinte de Dados do Armazenamento de Blobs](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Apenas diretórios e ficheiros pertencentes ao diretor de segurança.|

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Para mais exemplos, consulte a biblioteca de [clientes de identidade Azure para](https://pypi.org/project/azure-identity/) documentação Python.

### <a name="connect-by-using-an-account-key"></a>Conecte-se usando uma chave de conta

Esta é a maneira mais fácil de ligar a uma conta. 

Este exemplo cria uma instância **DataLakeServiceClient** utilizando uma chave de conta.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Substitua o `storage_account_name` valor do espaço reservado pelo nome da sua conta de armazenamento.

- Substitua o `storage_account_key` valor do espaço reservado pela chave de acesso à sua conta de armazenamento.

---

## <a name="set-an-acl-recursively"></a>Desaprote um ACL de forma recursiva

Quando *configurar* um ACL, **substitui-se** toda a ACL, incluindo todas as suas entradas. Se pretender alterar o nível de permissão de um principal de segurança ou adicionar um novo princípio de segurança à ACL sem afetar outras entradas existentes, deverá *atualizar* o ACL em vez disso. Para atualizar um ACL em vez de substituí-lo, consulte a Atualização de uma secção [ACL recursivamente](#update-an-acl-recursively) deste artigo.   

Esta secção contém exemplos para definir um ACL 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Desaprote um ACL de forma recorrente utilizando o `Set-AzDataLakeGen2AclRecursive` cmdlet.

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Estas entradas dão ao utilizador que lê, escreve e executa permissões, dá ao grupo que só lê e executa permissões, e não dá acesso a todos os outros. A última entrada ACL neste exemplo dá a um utilizador específico com o objeto ID "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" ler e executar permissões.

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

### <a name="net"></a>[.NET](#tab/dotnet)

Desenre um ACL de forma recorrente, chamando o método **DataLakeDirectoryClient.SetAccessControlRecursiveAsync.** Passe este método uma [Lista](/dotnet/api/system.collections.generic.list-1) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Cada [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) define uma entrada ACL. 

Se pretender definir uma entrada ACL **predefinitiva,** pode definir a propriedade [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) para **ser verdadeira**. 

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Este método aceita um parâmetro booleano nomeado `isDefaultScope` que especifica se deve definir o ACL predefinido. Este parâmetro é utilizado no construtor do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). As entradas da ACL dão ao utilizador que leia, escreva e execute permissões, dá ao grupo que só lê e executa permissões, e não dá acesso a todos os outros. A última entrada ACL neste exemplo dá a um utilizador específico o ID do objeto ""xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" ler e executar permissões.

```cs
public async void SetACLRecursively(DataLakeServiceClient serviceClient, bool isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlList = 
        new List<PathAccessControlItem>() 
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Write | 
            RolePermissions.Execute, isDefaultScope),
                    
        new PathAccessControlItem(AccessControlType.Group, 
            RolePermissions.Read | 
            RolePermissions.Execute, isDefaultScope),
                    
        new PathAccessControlItem(AccessControlType.Other, 
            RolePermissions.None, isDefaultScope),

        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Execute, isDefaultScope,
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.SetAccessControlRecursiveAsync
        (accessControlList, null);
}

```

### <a name="python"></a>[Python](#tab/python)

Desaprote um ACL de forma recorrente, chamando o método **DataLakeDirectoryClient.set_access_control_recursive.**

Se pretender definir uma entrada ACL **predefinida,** adicione a cadeia `default:` ao início de cada cadeia de entrada ACL. 

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . 

Este método aceita um parâmetro booleano nomeado `is_default_scope` que especifica se deve definir o ACL predefinido. se esse parâmetro `True` for, a lista de entradas ACL é precedida com a cadeia `default:` . 

As entradas da ACL dão ao utilizador que leia, escreva e execute permissões, dá ao grupo que só lê e executa permissões, e não dá acesso a todos os outros. A última entrada ACL neste exemplo dá a um utilizador específico o ID do objeto ""xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" ler e executar permissões. Estas entradas dão ao utilizador que lê, escreve e executa permissões, dá ao grupo que só lê e executa permissões, e não dá acesso a todos os outros. A última entrada ACL neste exemplo dá a um utilizador específico o ID do objeto ""xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" ler e executar permissões.

```python
def set_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:4a9028cf-f779-4032-b09d-970ebe3db258:r--'   

        if is_default_scope:
           acl = 'default:user::rwx,default:group::rwx,default:other::rwx,default:user:4a9028cf-f779-4032-b09d-970ebe3db258:r--'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

---

## <a name="update-an-acl-recursively"></a>Atualize um ACL recursivamente

Quando *atualiza* um ACL, modifica o ACL em vez de substituir o ACL. Por exemplo, pode adicionar um novo princípio de segurança à ACL sem afetar outros princípios de segurança listados na ACL.  Para substituir o ACL em vez de o atualizar, consulte a secção [ACL recursivamente](#set-an-acl-recursively) deste artigo. 

Para atualizar um ACL, crie um novo objeto ACL com a entrada ACL que pretende atualizar e, em seguida, utilize esse objeto na atualização da operação ACL. Não obtenha o ACL existente, basta fornecer entradas ACL para serem atualizadas.

Esta secção contém exemplos para como atualizar um ACL.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Atualize um ACL recursivamente utilizando o **cmdlet Update-AzDataGen2Acsive.** 

Este exemplo atualiza uma entrada ACL com permissão de escrita. 

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Se pretender atualizar uma entrada ACL **predefinitiva,** utilize o parâmetro **-DefaultScope** quando executar o comando **Set-AzDataGen2ItemAclObject.** Por exemplo: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx -DefaultScope`.

### <a name="net"></a>[.NET](#tab/dotnet)

Atualize um ACL recursivamente chamando o método **DataLakeDirectoryClient.UpdateAccessControlRecursiveAsync.**  Passe este método uma [Lista](/dotnet/api/system.collections.generic.list-1) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Cada [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) define uma entrada ACL. 

Se pretender atualizar uma entrada ACL **predefinitiva,** pode definir a propriedade [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) para **ser verdadeira**. 

Este exemplo atualiza uma entrada ACL com permissão de escrita. Este método aceita um parâmetro booleano nomeado `isDefaultScope` que especifica se deve atualizar o ACL predefinido. Este parâmetro é utilizado no construtor do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

```cs
public async void UpdateACLsRecursively(DataLakeServiceClient serviceClient, bool isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
        GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlListUpdate = 
        new List<PathAccessControlItem>()
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read |
            RolePermissions.Write | 
            RolePermissions.Execute, isDefaultScope, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.UpdateAccessControlRecursiveAsync
        (accessControlListUpdate, null);

}
```

### <a name="python"></a>[Python](#tab/python)

Atualize um ACL recursivamente chamando o método **DataLakeDirectoryClient.update_access_control_recursive.** Se pretender atualizar uma entrada ACL **predefinida,** adicione a cadeia `default:` ao início de cada cadeia de entrada ACL. 

Este exemplo atualiza uma entrada ACL com permissão de escrita.

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Este método aceita um parâmetro booleano nomeado `is_default_scope` que especifica se deve atualizar o ACL predefinido. se esse parâmetro `True` for, a entrada ACL atualizada é precedida com a cadeia `default:` .  

```python
def update_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")

        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'   

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'

        directory_client.update_access_control_recursive(acl=acl)

        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e)
```

---

## <a name="remove-acl-entries-recursively"></a>Remover as entradas ACL recursivamente

Pode remover uma ou mais entradas ACL de forma recorrente. Para remover uma entrada ACL, crie um novo objeto ACL para a entrada ACL ser removida e, em seguida, utilize esse objeto na remoção da operação ACL. Não obtenha o ACL existente, basta fornecer as entradas ACL para serem removidas. 

Esta secção contém exemplos para como remover um ACL. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

### <a name="net"></a>[.NET](#tab/dotnet)

Remova as entradas ACL chamando o método **DataLakeDirectoryClient.RemoveAccessControlRecursiveAsync.** Passe este método uma [Lista](/dotnet/api/system.collections.generic.list-1) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Cada [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) define uma entrada ACL. 

Se pretender remover uma entrada ACL **predefinitiva,** pode definir a propriedade [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) para **ser verdadeira**. 

Este exemplo remove uma entrada ACL da ACL do diretório denominado `my-parent-directory` . Este método aceita um parâmetro booleano nomeado `isDefaultScope` que especifica se deve remover a entrada do ACL predefinido. Este parâmetro é utilizado no construtor do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

```cs
public async void RemoveACLsRecursively(DataLakeServiceClient serviceClient, isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<RemovePathAccessControlItem> accessControlListForRemoval = 
        new List<RemovePathAccessControlItem>()
        {
            new RemovePathAccessControlItem(AccessControlType.User, isDefaultScope,
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
        };

    await directoryClient.RemoveAccessControlRecursiveAsync
        (accessControlListForRemoval, null);

}
```

### <a name="python"></a>[Python](#tab/python)

Remova as entradas ACL chamando o método **DataLakeDirectoryClient.remove_access_control_recursive.** Se pretender remover uma entrada ACL **predefinida,** adicione a cadeia `default:` ao início da cadeia de entrada ACL. 

Este exemplo remove uma entrada ACL da ACL do diretório denominado `my-parent-directory` . Este método aceita um parâmetro booleano nomeado `is_default_scope` que especifica se deve remover a entrada do ACL predefinido. se esse parâmetro `True` for, a entrada ACL atualizada é precedida com a cadeia `default:` . 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:4a9028cf-f779-4032-b09d-970ebe3db258'

        if is_default_scope:
           acl = 'default:user:4a9028cf-f779-4032-b09d-970ebe3db258'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

---

## <a name="recover-from-failures"></a>Recuperar de falhas

Pode encontrar erros de tempo de execução ou permissão. Para erros de tempo de execução, reinicie o processo desde o início. Podem ocorrer erros de permissão se o diretor de segurança não tiver permissão suficiente para modificar a ACL de um diretório ou ficheiro que está na hierarquia do diretório a ser modificado. Aborde a questão da permissão e, em seguida, opte por retomar o processo a partir do ponto de falha, utilizando um token de continuação, ou reiniciar o processo desde o início. Não tens de usar o símbolo de continuação se preferires recomeçar desde o início. Pode re-aplicar as entradas ACL sem qualquer impacto negativo.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Devolva os resultados à variável. O tubo falhou as entradas para uma tabela formatada.

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

## <a name="net"></a>[.NET](#tab/dotnet)

Este exemplo devolve um sinal de continuação em caso de falha. A aplicação pode voltar a chamar este método de exemplo depois de o erro ter sido abordado, e passar no token de continuação. Se este método de exemplo for chamado pela primeira vez, a aplicação pode passar num valor para ``null`` o parâmetro simbólico de continuação. 

```cs
public async Task<string> ResumeAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient,
    List<PathAccessControlItem> accessControlList, 
    string continuationToken)
{
    try
    {
        var accessControlChangeResult =
            await directoryClient.SetAccessControlRecursiveAsync(
                accessControlList, continuationToken: continuationToken, null);

        if (accessControlChangeResult.Value.Counters.FailedChangesCount > 0)
        {
            continuationToken =
                accessControlChangeResult.Value.ContinuationToken;
        }

        return continuationToken;
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
        return continuationToken;
    }

}
```

### <a name="python"></a>[Python](#tab/python)

Este exemplo devolve um sinal de continuação em caso de falha. A aplicação pode voltar a chamar este método de exemplo depois de o erro ter sido abordado, e passar no token de continuação. Se este método de exemplo for chamado pela primeira vez, a aplicação pode passar num valor para ``None`` o parâmetro simbólico de continuação. 

```python
def resume_set_acl_recursive(continuation_token):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl, continuation=continuation_token)

        continuation_token = acl_change_result.continuation

        return continuation_token
        
    except Exception as e:
     print(e) 
     return continuation_token
```

---

## <a name="resources"></a>Recursos

Esta secção contém ligações a bibliotecas e amostras de código.

#### <a name="libraries"></a>Bibliotecas

- [PowerShell](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.powershellgallery.com%2Fpackages%2FAz.Storage%2F2.5.2-preview&data=02%7C01%7Cnormesta%40microsoft.com%7Ccdabce06132c42132b4008d849a2dfb1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637340311173215017&sdata=FWynO9UKTt7ESMCFgkWaL7J%2F%2BjODaRo5BD6G6yCx9os%3D&reserved=0)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)

#### <a name="code-samples"></a>Exemplos de código

- PowerShell: [Amostra de Readme](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D)  |  [Sample](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)

- NET: [Amostra de readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%252FSc2aDVW3De4A%252Fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0)  |  [Sample](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python: [Amostra de Readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0)  |  [Sample](https://recursiveaclpr.blob.core.windows.net/privatedrop/datalake_samples_access_control_async.py?sv=2019-02-02&st=2020-08-24T07%3A48%3A10Z&se=2021-08-25T07%3A48%3A00Z&sr=b&sp=r&sig=%2F1c540%2BpXYyNcuTmWPWHg2m9SyClXLIMw7ChLZGsyD0%3D)

## <a name="best-practice-guidelines"></a>Diretrizes de boas práticas

Esta secção fornece-lhe algumas diretrizes de boas práticas para definir ACLs de forma recursiva. 

#### <a name="handling-runtime-errors"></a>Manipulação de erros de tempo de execução

Um erro de tempo de execução pode ocorrer por muitas razões (Por exemplo: uma falha ou um problema de conectividade do cliente). Se encontrar um erro de tempo de execução, reinicie o processo ACL recursivo. Os ACLs podem ser reaplicados em itens sem causar um impacto negativo. 

#### <a name="handling-permission-errors-403"></a>Erros de permissão de manuseamento (403)

Se encontrar uma exceção ao controlo de acesso durante a execução de um processo ACL recursivo, o seu [diretor de segurança](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) AD pode não ter autorização suficiente para aplicar um ACL a um ou mais itens infantis na hierarquia do diretório. Quando ocorre um erro de permissão, o processo para e é fornecido um sinal de continuação. Corrija o problema da permissão e, em seguida, use o token de continuação para processar o conjunto de dados restante. Os diretórios e ficheiros que já foram processados com sucesso não terão de ser processados novamente. Também pode optar por reiniciar o processo ACL recursivo. Os ACLs podem ser reaplicados em itens sem causar um impacto negativo. 

#### <a name="credentials"></a>Credenciais 

Recomendamos que você forja um principal de segurança Azure AD que foi atribuído a função [de Proprietário de Dados de Armazenamento blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) no âmbito da conta de armazenamento alvo ou do recipiente. 

#### <a name="performance"></a>Desempenho 

Para reduzir a latência, recomendamos que execute o processo ACL recursivo numa Máquina Virtual Azure (VM) que está localizada na mesma região que a sua conta de armazenamento. 

#### <a name="acl-limits"></a>Limites da ACL

O número máximo de ACLs que pode aplicar a um diretório ou ficheiro é de 32 ACLs de acesso e 32 ACLs predefinidos. Para obter mais informações, consulte [o controlo de acesso no Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

<a id="provide-feedback"></a>

### <a name="provide-feedback-or-report-issues"></a>Fornecer problemas de feedback ou relatório

Pode fornecer o seu feedback ou reportar um problema em  [recursiveACLfeedback@microsoft.com](mailto:recursiveACLfeedback@microsoft.com) .

## <a name="see-also"></a>Consulte também

- [Access control in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) (Controlo de acesso no Azure Data Lake Storage Gen2)
- [Problemas conhecidos](data-lake-storage-known-issues.md)


