---
title: Desconfiem de ACLs de forma recursiva para a Azure Data Lake Storage Gen2 Microsoft Docs
description: Pode adicionar, atualizar e remover as listas de controlo de acesso de forma recorrente para os itens infantis existentes de um diretório principal.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/17/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 2544711054678ec1bb5c43d40e4497eec9af2941
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96600341"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Definir listas de controlo de acesso (ACLs) recursivamente para Azure Data Lake Storage Gen2

A herança ACL já está disponível para novos itens infantis que são criados sob um diretório de pais. Pode também agora adicionar, atualizar e remover os ACLs de forma recorrente para os itens infantis existentes de um diretório dos pais sem ter de fazer estas alterações individualmente para cada item infantil.

[Bibliotecas](#libraries)  |  [Amostras](#code-samples)  |  [Melhores práticas](#best-practice-guidelines)

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento que tem espaço hierárquico de nome (HNS) ativado. Siga [estas](create-data-lake-storage-account.md) instruções para criar uma.

- As permissões corretas para executar o processo ACL recursivo. A permissão correta inclui qualquer uma das seguintes: 

  - Um diretor de [segurança](../../role-based-access-control/overview.md#security-principal) Azure Ative (AD) provisionado que foi atribuído a função de Proprietário de [Dados blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) de armazenamento no âmbito do contentor-alvo, grupo de recursos-mãe ou subscrição.   

  - Possuindo o utilizador do contentor-alvo ou diretório ao qual planeia aplicar o processo ACL recursivo. Isto inclui todos os itens para crianças no contentor-alvo ou diretório. 

- Uma compreensão de como os ACLs são aplicados a diretórios e ficheiros. Consulte [o controlo de acesso no Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md). 

Consulte a secção de **projeto** deste artigo para ver as orientações de instalação para PowerShell, .NET SDK e Python SDK.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Instale as bibliotecas necessárias.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Verifique se a versão do PowerShell que foi instalada é `5.1` ou superior utilizando o seguinte comando.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Para atualizar a sua versão do PowerShell, consulte [a atualização do Windows PowerShell existente](/powershell/scripting/install/installing-windows-powershell)
    
2. Instale o módulo **Az.Storage.**

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Para obter mais informações sobre como instalar módulos PowerShell, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Abra o [Azure Cloud Shell](../../cloud-shell/overview.md), ou se [instalou](/cli/azure/install-azure-cli) o Azure CLI localmente, abra uma aplicação de consola de comando como o Windows PowerShell.

2. Verifique se a versão do Azure CLI que foi instalada é `2.14.0` ou superior utilizando o seguinte comando.

   ```azurecli
    az --version
   ```
   Se a sua versão do Azure CLI for inferior `2.14.0` a , então instale uma versão posterior. Consulte [a Instalação do Azure CLI](/cli/azure/install-azure-cli).

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

### <a name="java"></a>[Java](#tab/java)

Para começar, abra [esta página](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) e encontre a versão mais recente da biblioteca Java. Em seguida, abra o ficheiro *pom.xml* no seu editor de texto. Adicione um elemento de dependência que faz referência a essa versão.

Se planeia autenticar a sua aplicação de cliente utilizando o Azure Ative Directory (AD), adicione uma dependência à Biblioteca de Clientes Secretos Azure. Consulte [a adição do pacote secret Client Library ao seu projeto.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project)

Em seguida, adicione estas declarações de importação ao seu ficheiro de código.

```java
import com.azure.identity.ClientSecretCredential;
import com.azure.identity.ClientSecretCredentialBuilder;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.Response;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

### <a name="python"></a>[Python](#tab/python)

1. Descarregue a biblioteca de [clientes Azure Data Lake Storage para Python.](https://recursiveaclpr.blob.core.windows.net/privatedrop/azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl?sv=2019-02-02&st=2020-08-24T07%3A47%3A01Z&se=2021-08-25T07%3A47%3A00Z&sr=b&sp=r&sig=H1XYw4FTLJse%2BYQ%2BfamVL21UPVIKRnnh2mfudA%2BfI0I%3D)

2. Instale a biblioteca que descarregou utilizando [o pip](https://pypi.org/project/pip/).

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

3. Adicione estas declarações de importação ao topo do seu ficheiro de código.

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

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Se estiver a utilizar o Azure CLI localmente, execute o comando de login.

   ```azurecli
   az login
   ```

   Se o CLI conseguir abrir o seu navegador predefinido, fá-lo-á e carregará uma página de inscrição do Azure.

   Caso contrário, abra uma página do navegador [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e introduza o código de autorização exibido no seu terminal. Em seguida, inscreva-se com as credenciais da sua conta no navegador.

   Para saber mais sobre diferentes métodos de autenticação, consulte [Autorizar o acesso aos dados de blob ou fila com o Azure CLI](./authorize-data-operations-cli.md).

2. Se a sua identidade estiver associada a mais de uma subscrição, então desa estalem a sua subscrição ativa para a subscrição da conta de armazenamento que irá hospedar o seu website estático.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Substitua o `<subscription-id>` valor do espaço reservado pelo ID da sua subscrição.

> [!NOTE]
> O exemplo apresentado neste artigo mostra a autorização do Azure Ative Directory (AD). Para saber mais sobre os métodos de autorização, consulte [Autorizar o acesso aos dados de blob ou fila com o Azure CLI](./authorize-data-operations-cli.md).

### <a name="net"></a>[.NET](#tab/dotnet)

Para utilizar os snippets neste artigo, terá de criar uma instância [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) que represente a conta de armazenamento.

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

Este exemplo cria um exemplo [de DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) usando um ID de cliente, um segredo de cliente e uma identificação de inquilino.  

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

Este exemplo cria uma instância [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) utilizando uma chave de conta.

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

### <a name="java"></a>[Java](#tab/java)

Para utilizar os snippets neste artigo, terá de criar uma instância **DataLakeServiceClient** que represente a conta de armazenamento. 

#### <a name="connect-by-using-an-account-key"></a>Conecte-se usando uma chave de conta

Esta é a maneira mais fácil de ligar a uma conta. 

Este exemplo cria uma instância **DataLakeServiceClient** utilizando uma chave de conta.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      
```

#### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Conecte-se utilizando o Azure Ative Directory (Azure AD)

Pode utilizar a biblioteca de [clientes de identidade Azure para a Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) para autenticar a sua aplicação com Azure AD.

Este exemplo cria um exemplo **de DataLakeServiceClient** usando um ID de cliente, um segredo de cliente e uma identificação de inquilino.  Para obter estes valores, consulte [Adquirir um token da Azure AD para autorizar pedidos de uma aplicação do cliente.](../common/storage-auth-aad-app.md)

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> Para mais exemplos, consulte a biblioteca de [clientes de identidade Azure para](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) documentação java.

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

Se optar por *definir* a ACL, deve adicionar uma entrada para o utilizador próprio, uma entrada para o grupo de propriedade e uma entrada para todos os outros utilizadores. Para saber mais sobre o utilizador próprio, o grupo de propriedade, e todos os outros utilizadores, consulte [Utilizadores e identidades.](data-lake-storage-access-control.md#users-and-identities) 

Esta secção contém exemplos para definir um ACL.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Desaprote um ACL de forma recorrente utilizando o **cmdlet Set-AzDataGen2Acsive.**

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

Para ver um exemplo que define os ACLs de forma recursiva em lotes especificando um tamanho de lote, consulte o artigo de referência [set-AzDataLakeGen2AclRecursive.](/powershell/module/az.storage/set-azdatalakegen2aclrecursive)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Desaprote um ACL de forma recorrente utilizando o comando [de acesso az fs de acesso ao set-recursive.](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive)

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Estas entradas dão ao utilizador que lê, escreve e executa permissões, dá ao grupo que só lê e executa permissões, e não dá acesso a todos os outros. A última entrada ACL neste exemplo dá a um utilizador específico com o objeto ID "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" ler e executar permissões.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Se pretender definir uma entrada ACL **predefinida,** adicione o prefixo `default:` a cada entrada. Por exemplo, `default:user::rwx` ou `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`. 

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

Para ver um exemplo que define os ACLs de forma recursiva em lotes especificando um tamanho do lote, consulte a [amostra](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET .

### <a name="java"></a>[Java](#tab/java)

Desenre um ACL de forma recorrente, chamando o método **DataLakeDirectoryClient.setAccessControlRecursive.** Passe este método uma [Lista](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) de [objetos PathAccessControlEntry.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) Cada [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) define uma entrada ACL. 

Se pretender definir uma entrada ACL **predefinida,** pode ligar para o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passar num valor **de verdade**. 

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Este método aceita um parâmetro booleano nomeado `isDefaultScope` que especifica se deve definir o ACL predefinido. Este parâmetro é utilizado em cada chamada para o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). As entradas da ACL dão ao utilizador que leia, escreva e execute permissões, dá ao grupo que só lê e executa permissões, e não dá acesso a todos os outros. A última entrada ACL neste exemplo dá a um utilizador específico com o objeto ID "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" ler e executar permissões.

```java
static public void SetACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){
    
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathAccessControlEntry> pathAccessControlEntries = 
        new ArrayList<PathAccessControlEntry>();

    // Create owner entry.
    PathAccessControlEntry ownerEntry = new PathAccessControlEntry();

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    ownerEntry.setDefaultScope(isDefaultScope);
    ownerEntry.setAccessControlType(AccessControlType.USER);
    ownerEntry.setPermissions(ownerPermission);

    pathAccessControlEntries.add(ownerEntry);

    // Create group entry.
    PathAccessControlEntry groupEntry = new PathAccessControlEntry();

    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(false);

    groupEntry.setDefaultScope(isDefaultScope);
    groupEntry.setAccessControlType(AccessControlType.GROUP);
    groupEntry.setPermissions(groupPermission);

    pathAccessControlEntries.add(groupEntry);

    // Create other entry.
    PathAccessControlEntry otherEntry = new PathAccessControlEntry();

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setExecutePermission(false).setReadPermission(false).setWritePermission(false);

    otherEntry.setDefaultScope(isDefaultScope);
    otherEntry.setAccessControlType(AccessControlType.OTHER);
    otherEntry.setPermissions(otherPermission);

    pathAccessControlEntries.add(otherEntry);

    // Create named user entry.
    PathAccessControlEntry userEntry = new PathAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(false);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx");
    userEntry.setPermissions(userPermission);    
    
    pathAccessControlEntries.add(userEntry);
    
    directoryClient.setAccessControlRecursive(pathAccessControlEntries);        

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
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'   

        if is_default_scope:
           acl = 'default:user::rwx,default:group::rwx,default:other::rwx,default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

Para ver um exemplo que processa os ACLs de forma recursiva em lotes especificando um tamanho do lote, consulte a [amostra](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de pitão .

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

Para ver um exemplo que atualiza os ACLs de forma recursiva em lotes especificando um tamanho de lote, consulte o artigo de referência [Update-AzDataLakeGen2Acsive.](/powershell/module/az.storage/update-azdatalakegen2aclrecursive)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Atualize um ACL de forma recorrente utilizando o comando [de actualização-recursivo de acesso a az.](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) 

Este exemplo atualiza uma entrada ACL com permissão de escrita. 

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Se pretender atualizar uma entrada ACL **predefinida,** adicione o prefixo `default:` a cada entrada. Por exemplo, `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

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

Para ver um exemplo que atualiza os ACLs de forma recursiva em lotes especificando um tamanho do lote, consulte a [amostra](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET .

### <a name="java"></a>[Java](#tab/java)

Atualize um ACL recursivamente chamando o método **DataLakeDirectoryClient.updateAccessControlRecursive.**  Passe este método uma [Lista](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) de [objetos PathAccessControlEntry.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) Cada [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) define uma entrada ACL. 

Se pretender atualizar uma entrada ACL **predefinida,** pode então o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passar num valor **de verdade**. 

Este exemplo atualiza uma entrada ACL com permissão de escrita. Este método aceita um parâmetro booleano nomeado `isDefaultScope` que especifica se deve atualizar o ACL predefinido. Este parâmetro é utilizado na chamada para o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). 

```java
static public void UpdateACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){

    DataLakeDirectoryClient directoryClient =
    fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathAccessControlEntry> pathAccessControlEntries = 
        new ArrayList<PathAccessControlEntry>();

    // Create named user entry.
    PathAccessControlEntry userEntry = new PathAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx");
    userEntry.setPermissions(userPermission);    
    
    pathAccessControlEntries.add(userEntry);
    
    directoryClient.updateAccessControlRecursive(pathAccessControlEntries);          
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

Para ver um exemplo que processa os ACLs de forma recursiva em lotes especificando um tamanho do lote, consulte a [amostra](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de pitão .

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

Para ver um exemplo que remove os ACLs de forma recorrente em lotes especificando um tamanho do lote, consulte o artigo de referência [Remove-AzDataLakeGen2Acsive.](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Remova as entradas ACL utilizando o [comando de remoção recursivo de armazenamento az fs.](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) 

Este exemplo remove uma entrada ACL do diretório de raiz do recipiente.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Se pretender remover uma entrada ACL **predefinida,** adicione o prefixo `default:` a cada entrada. Por exemplo, `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

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

Para ver um exemplo que remove os ACLs de forma recorrente em lotes especificando um tamanho do lote, consulte a [amostra](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET .

### <a name="java"></a>[Java](#tab/java)

Remova as entradas ACL chamando o método **DataLakeDirectoryClient.removeAccessControlRecursive.** Passe este método uma [Lista](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) de [objetos PathAccessControlEntry.](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) Cada [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) define uma entrada ACL. 

Se pretender remover uma entrada ACL **predefinida,** pode então o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passar num valor **de verdade**.  

Este exemplo remove uma entrada ACL da ACL do diretório denominado `my-parent-directory` . Este método aceita um parâmetro booleano nomeado `isDefaultScope` que especifica se deve remover a entrada do ACL predefinido. Este parâmetro é utilizado na chamada para o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html).


```java
static public void RemoveACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){

    DataLakeDirectoryClient directoryClient =
    fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathRemoveAccessControlEntry> pathRemoveAccessControlEntries = 
        new ArrayList<PathRemoveAccessControlEntry>();

    // Create named user entry.
    PathRemoveAccessControlEntry userEntry = new PathRemoveAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"); 
    
    pathRemoveAccessControlEntries.add(userEntry);
    
    directoryClient.removeAccessControlRecursive(pathRemoveAccessControlEntries);      

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
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

Para ver um exemplo que processa os ACLs de forma recursiva em lotes especificando um tamanho do lote, consulte a [amostra](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de pitão .

---

## <a name="recover-from-failures"></a>Recuperar de falhas

Pode encontrar erros de tempo de execução ou permissão. Para erros de tempo de execução, reinicie o processo desde o início. Podem ocorrer erros de permissão se o diretor de segurança não tiver permissão suficiente para modificar a ACL de um diretório ou ficheiro que está na hierarquia do diretório a ser modificado. Aborde a questão da permissão e, em seguida, opte por retomar o processo a partir do ponto de falha, utilizando um token de continuação, ou reiniciar o processo desde o início. Não tens de usar o símbolo de continuação se preferires recomeçar desde o início. Pode re-aplicar as entradas ACL sem qualquer impacto negativo.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Em caso de falha, pode devolver um token de continuação definindo o `--continue-on-failure` parâmetro para `false` . Depois de resolver os erros, pode retomar o processo a partir do ponto de falha, executando novamente o comando e, em seguida, definindo o `--continuation` parâmetro para o token de continuação. 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

## <a name="net"></a>[.NET](#tab/dotnet)

Este exemplo devolve um sinal de continuação em caso de falha. A aplicação pode voltar a chamar este método de exemplo depois de o erro ter sido abordado, e passar no token de continuação. Se este método de exemplo for chamado pela primeira vez, a aplicação pode passar num valor para `null` o parâmetro simbólico de continuação. 

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

Para ver um exemplo que define os ACLs de forma recursiva em lotes especificando um tamanho do lote, consulte a [amostra](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET .

### <a name="java"></a>[Java](#tab/java)

Este exemplo devolve um sinal de continuação em caso de falha. A aplicação pode voltar a chamar este método de exemplo depois de o erro ter sido abordado, e passar no token de continuação. Se este método de exemplo for chamado pela primeira vez, a aplicação pode passar num valor para `null` o parâmetro simbólico de continuação. 

```java
static public String ResumeSetACLRecursively(DataLakeFileSystemClient fileSystemClient,
DataLakeDirectoryClient directoryClient,
List<PathAccessControlEntry> accessControlList, 
String continuationToken){

    try{
        PathSetAccessControlRecursiveOptions options = new PathSetAccessControlRecursiveOptions(accessControlList);
        
        options.setContinuationToken(continuationToken);
    
        Response<AccessControlChangeResult> accessControlChangeResult =  
            directoryClient.setAccessControlRecursiveWithResponse(options, null, null);

        if (accessControlChangeResult.getValue().getCounters().getFailedChangesCount() > 0)
        {
            continuationToken =
                accessControlChangeResult.getValue().getContinuationToken();
        }
    
        return continuationToken;

    }
    catch(Exception ex){
    
        System.out.println(ex.toString());
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

Para ver um exemplo que processa os ACLs de forma recursiva em lotes especificando um tamanho do lote, consulte a [amostra](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de pitão .

---

Se quiser que o processo seja concluído ininterruptamente por erros de permissão, pode especificar isso.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para assegurar que o processo esteja concluído ininterruptamente, desagure o `--continue-on-failure` parâmetro para `true` . 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

### <a name="net"></a>[.NET](#tab/dotnet)

Para garantir que o processo se completa ininterruptamente, passe num objeto **AccessControlChangedOptions** e desempere a propriedade **ContinueOnFailure** desse objeto para ``true`` .

Este exemplo define as entradas ACL de forma recursiva. Se este código encontrar um erro de permissão, regista essa falha e continua a execução. Este exemplo imprime o número de falhas na consola. 

```cs
public async Task ContinueOnFailureAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient, 
    List<PathAccessControlItem> accessControlList)
{
    var accessControlChangeResult = 
        await directoryClient.SetAccessControlRecursiveAsync(
            accessControlList, null, new AccessControlChangeOptions() 
            { ContinueOnFailure = true });

    var counters = accessControlChangeResult.Value.Counters;

    Console.WriteLine("Number of directories changed: " +
        counters.ChangedDirectoriesCount.ToString());

    Console.WriteLine("Number of files changed: " +
        counters.ChangedFilesCount.ToString());

    Console.WriteLine("Number of failures: " +
        counters.FailedChangesCount.ToString());
}
```

Para ver um exemplo que define os ACLs de forma recursiva em lotes especificando um tamanho do lote, consulte a [amostra](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).NET .

### <a name="java"></a>[Java](#tab/java)

Para garantir que o processo é concluído ininterruptamente, ligue para o método **setContinueOnFailure** de um objeto [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passe num valor **verdadeiro**.

Este exemplo define as entradas ACL de forma recursiva. Se este código encontrar um erro de permissão, regista essa falha e continua a execução. Este exemplo imprime o número de falhas na consola. 

```java
static public void ContinueOnFailure(DataLakeFileSystemClient fileSystemClient,
DataLakeDirectoryClient directoryClient,
List<PathAccessControlEntry> accessControlList){
    
    PathSetAccessControlRecursiveOptions options = 
        new PathSetAccessControlRecursiveOptions(accessControlList);
        
    options.setContinueOnFailure(true);
    
    Response<AccessControlChangeResult> accessControlChangeResult =  
        directoryClient.setAccessControlRecursiveWithResponse(options, null, null);

    AccessControlChangeCounters counters = accessControlChangeResult.getValue().getCounters();

    System.out.println("Number of directories changes: " + 
        counters.getChangedDirectoriesCount());

    System.out.println("Number of files changed: " + 
        counters.getChangedDirectoriesCount());

    System.out.println("Number of failures: " + 
        counters.getChangedDirectoriesCount());
}
```

### <a name="python"></a>[Python](#tab/python)

Para garantir que o processo esteja concluído ininterruptamente, não passe um sinal de continuação no método **DataLakeDirectoryClient.set_access_control_recursive.**

Este exemplo define as entradas ACL de forma recursiva. Se este código encontrar um erro de permissão, regista essa falha e continua a execução. Este exemplo imprime o número de falhas na consola. 

```python
def continue_on_failure():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl)

        print("Summary: {} directories and {} files were updated successfully, {} failures were counted."
          .format(acl_change_result.counters.directories_successful, acl_change_result.counters.files_successful,
                  acl_change_result.counters.failure_count))
        
    except Exception as e:
     print(e)
```

Para ver um exemplo que processa os ACLs de forma recursiva em lotes especificando um tamanho do lote, consulte a [amostra](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de pitão .

---

## <a name="resources"></a>Recursos

Esta secção contém ligações a bibliotecas e amostras de código.

#### <a name="libraries"></a>Bibliotecas

- [PowerShell](https://www.powershellgallery.com/packages/Az.Storage/3.0.0)
- [CLI do Azure](/cli/azure/storage/fs/access)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Java](/java/api/overview/azure/storage-file-datalake-readme)
- [Python](https://recursiveaclpr.blob.core.windows.net/privatedrop/azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl?sv=2019-02-02&st=2020-08-24T07%3A47%3A01Z&se=2021-08-25T07%3A47%3A00Z&sr=b&sp=r&sig=H1XYw4FTLJse%2BYQ%2BfamVL21UPVIKRnnh2mfudA%2BfI0I%3D)
- [REST](/rest/api/storageservices/datalakestoragegen2/path/update)

#### <a name="code-samples"></a>Exemplos de código

- PowerShell: [Amostra de Readme](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D)  |  [Sample](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)

- Azure CLI: [Amostra](https://github.com/Azure/azure-cli/blob/2a55a5350696a3a93a13f364f2104ec8bc82cdd3/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)

- NET: [Amostra de readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%2fSc2aDVW3De4A%2fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0)  |  [Sample](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python: [Amostra de Readme](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0)  |  [Sample](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)

## <a name="best-practice-guidelines"></a>Diretrizes de boas práticas

Esta secção fornece-lhe algumas diretrizes de boas práticas para definir ACLs de forma recursiva. 

#### <a name="handling-runtime-errors"></a>Manipulação de erros de tempo de execução

Um erro de tempo de execução pode ocorrer por muitas razões (Por exemplo: uma falha ou um problema de conectividade do cliente). Se encontrar um erro de tempo de execução, reinicie o processo ACL recursivo. Os ACLs podem ser reaplicados em itens sem causar um impacto negativo. 

#### <a name="handling-permission-errors-403"></a>Erros de permissão de manuseamento (403)

Se encontrar uma exceção ao controlo de acesso durante a execução de um processo ACL recursivo, o seu [diretor de segurança](../../role-based-access-control/overview.md#security-principal) AD pode não ter autorização suficiente para aplicar um ACL a um ou mais itens infantis na hierarquia do diretório. Quando ocorre um erro de permissão, o processo para e é fornecido um sinal de continuação. Corrija o problema da permissão e, em seguida, use o token de continuação para processar o conjunto de dados restante. Os diretórios e ficheiros que já foram processados com sucesso não terão de ser processados novamente. Também pode optar por reiniciar o processo ACL recursivo. Os ACLs podem ser reaplicados em itens sem causar um impacto negativo. 

#### <a name="credentials"></a>Credenciais 

Recomendamos que você forja um principal de segurança Azure AD que foi atribuído a função [de Proprietário de Dados de Armazenamento blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) no âmbito da conta de armazenamento alvo ou do recipiente. 

#### <a name="performance"></a>Desempenho 

Para reduzir a latência, recomendamos que execute o processo ACL recursivo numa Máquina Virtual Azure (VM) que está localizada na mesma região que a sua conta de armazenamento. 

#### <a name="acl-limits"></a>Limites da ACL

O número máximo de ACLs que pode aplicar a um diretório ou ficheiro é de 32 ACLs de acesso e 32 ACLs predefinidos. Para obter mais informações, consulte [o controlo de acesso no Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

## <a name="see-also"></a>Veja também

- [Access control in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md) (Controlo de acesso no Azure Data Lake Storage Gen2)
- [Problemas conhecidos](data-lake-storage-known-issues.md)