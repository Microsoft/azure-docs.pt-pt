---
title: Tutorial - Web app acede ao armazenamento usando identidades geridas Rio Azure
description: Neste tutorial aprende-se a aceder ao armazenamento do Azure em nome de uma aplicação que utiliza identidades geridas.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: de179ad1e310df1fdeaed2173a83076922f3dccc
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428943"
---
# <a name="tutorial-access-azure-storage-from-a-web-app"></a>Tutorial: aceder ao armazenamento do Azure a partir de uma aplicação web

Saiba como aceder ao armazenamento do Azure em nome de uma aplicação web (não de utilizador inscrito) que está a decorrer no Azure App Service utilizando identidades geridas.

:::image type="content" alt-text="Armazenamento de acesso" source="./media/scenario-secure-app-access-storage/web-app-access-storage.svg" border="false":::

Pretende adicionar acesso ao plano de dados Azure (Azure Storage, SQL Azure, Azure Key Vault ou outros serviços) a partir da sua aplicação web.  Podes usar uma chave partilhada, mas depois tens de te preocupar com a segurança operacional de quem pode criar, implantar e gerir o segredo.  Também é possível que a chave possa ser verificada no GitHub, que os hackers sabem como pesquisar. Uma forma mais segura de dar à sua aplicação web acesso aos dados é usar [identidades geridas.](/azure/active-directory/managed-identities-azure-resources/overview) Uma identidade gerida a partir do Azure Ative Directory permite que os Serviços de Aplicação acedam a recursos através de Role-Based Access Control (RBAC), sem necessitar de credenciais de aplicações. Depois de atribuir uma identidade gerida à sua aplicação web, a Azure cuida da criação e distribuição de um certificado.  As pessoas não têm que se preocupar em gerir segredos ou credenciais de aplicativos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Criar um sistema atribuído identidade gerida numa aplicação web
> * Criar uma conta de armazenamento e um recipiente de armazenamento Blob
> * Armazenamento de acesso a partir de uma aplicação web usando identidades geridas

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma aplicação web em execução no Azure App Service que tem o [módulo de autenticação/autorização do Serviço de Aplicações ativado.](scenario-secure-app-authentication-app-service.md)

## <a name="enable-managed-identity-on-app"></a>Ativar a identidade gerida na app

Se criar e publicar a sua aplicação web através do Visual Studio, a identidade gerida foi ativada na sua aplicação para si. No seu serviço de aplicações, selecione **Identidade** no painel de navegação esquerdo e, em seguida, **o Sistema atribuído**.  Verifique se o **estado** está definido para **On**.  Caso contrário, clique em **Guardar** e, em seguida, **Sim** para ativar a identidade gerida atribuída pelo sistema.  Quando a identidade gerida está ativada, o estado é definido para *On* e o ID do objeto está disponível.

:::image type="content" alt-text="Identidade atribuída ao sistema" source="./media/scenario-secure-app-access-storage/create-system-assigned-identity.png":::

Isto cria um novo ID de objeto, diferente do ID da aplicação criado na lâmina **de autenticação/autorização.**  Copie o ID do objeto do sistema atribuído identidade gerida, vai precisar dele mais tarde.

## <a name="create-a-storage-account-and-blob-storage-container"></a>Criar uma conta de armazenamento e um recipiente de armazenamento Blob

Agora está pronto para criar uma conta de armazenamento e um recipiente de armazenamento Blob.

Cada conta de armazenamento tem de pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contentor lógico para agrupar os seus serviços do Azure. Quando cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou utilizar um grupo de recursos existente. Este artigo mostra como criar um novo grupo de recursos.

Uma conta de armazenamento v2 para fins gerais concede acesso a todos os serviços de Armazenamento do Azure: blobs, ficheiros, filas, tabelas e discos. Os passos aqui delineados criam uma conta de armazenamento v2 para fins gerais, mas os passos para criar qualquer tipo de conta de armazenamento são semelhantes.

As bolhas no Azure Storage são organizadas em contentores. Antes de poder carregar uma bolha mais tarde neste tutorial, tem primeiro de criar um recipiente.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para criar uma conta de armazenamento para fins gerais v2 no portal do Azure, siga estes passos:

1. No menu do portal do Azure, selecione **Todos os serviços**. Na lista de recursos, escreva **Contas de Armazenamento**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Contas de Armazenamento**.

1. Na janela **Contas de Armazenamento** que é apresentada, escolha **Adicionar**.

1. Selecione a subscrição na qua pretende criar a conta de armazenamento.

1. No campo **do grupo Recursos,** selecione o grupo de recursos que contém a sua aplicação web a partir do menu dropdown.

1. A seguir, introduza um nome para a sua conta de armazenamento. O nome que escolher tem de ser exclusivo em todo o Azure. O nome também tem de ter entre 3 e 24 carateres de comprimento e apenas pode incluir números e letras minúsculas.

1. Selecione uma localização para a sua conta de armazenamento ou utilize a localização predefinida.

1. Deixe os outros campos definidos para os respetivos valores predefinidos:

|Campo|Valor|
|--|--|
|Modelo de implementação|Resource Manager|
|Desempenho|Standard|
|Tipo de conta|StorageV2 (fins gerais v2)|
|Replicação|Armazenamento georredundante com acesso de leitura (RA-GRS)|
|Camada de acesso|Frequente|

1. Selecione **Rever + Criar** para rever as definições de conta de armazenamento e criar a conta.

1. Selecione **Criar**.

Para criar um recipiente de armazenamento Blob no Azure Storage, siga estes passos:

1. No portal do Azure, navegue para a sua nova conta de armazenamento.

1. No menu esquerdo para a conta de armazenamento, percorra a secção **de serviço Blob** e, em seguida, selecione **Recipientes**.

1. Selecione o botão **+ Contentor**.

1. Escreva um nome para o novo contentor. O nome do contentor tem estar em minúsculas, tem de começar com uma letra ou um número e só pode incluir letras, números e o caráter de travessão (-).

1. Defina o nível de acesso público ao contentor. O nível predefinido é **Privado (sem acesso anónimo)**.

1. Selecione **OK** para criar o contentor.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para criar uma conta de armazenamento v2 de uso geral e um recipiente de armazenamento Blob, execute o seguinte script. Especifique o nome do grupo de recursos que contém a sua aplicação web. Introduza um nome para a conta de armazenamento. O nome que escolher tem de ser exclusivo em todo o Azure. O nome também tem de ter entre 3 e 24 carateres de comprimento e apenas pode incluir números e letras minúsculas. Especifique a localização da sua conta de armazenamento.  Para ver uma lista de locais válidos para a sua ```Get-AzLocation | select Location``` subscrição. O nome do contentor tem estar em minúsculas, tem de começar com uma letra ou um número e só pode incluir letras, números e o caráter de travessão (-).

Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores.

```powershell
Connect-AzAccount

$resourceGroup = "securewebappresourcegroup"
$location = "<location>"
$storageName="securewebappstorage"
$containerName = "securewebappblobcontainer"

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar uma conta de armazenamento v2 de uso geral e um recipiente de armazenamento Blob, execute o seguinte script. Especifique o nome do grupo de recursos que contém a sua aplicação web. Introduza um nome para a conta de armazenamento. O nome que escolher tem de ser exclusivo em todo o Azure. O nome também tem de ter entre 3 e 24 carateres de comprimento e apenas pode incluir números e letras minúsculas. Especifique a localização da sua conta de armazenamento.  O nome do contentor tem estar em minúsculas, tem de começar com uma letra ou um número e só pode incluir letras, números e o caráter de travessão (-).

O exemplo a seguir utiliza a sua conta Azure AD para autorizar a operação de criação do recipiente. Antes de criar o recipiente, atribua a função de Contribuinte de Dados de Armazenamento blob a si mesmo. Mesmo que você é o proprietário da conta, você precisa de permissões explícitas para realizar operações de dados contra a conta de armazenamento.

Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores.

```azurecli-interactive
az login

az storage account create \
    --name securewebappstorage \
    --resource-group securewebappresourcegroup \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope $storageId

az storage container create \
    --account-name securewebappstorage \
    --name securewebappblobcontainer \
    --auth-mode login
```

---

## <a name="grant-access-to-the-storage-account"></a>Conceder acesso à conta de armazenamento

Tem de conceder acesso à conta de armazenamento da sua aplicação web antes de poder criar, ler ou apagar bolhas. Num passo anterior, configuraste a aplicação web em execução no Serviço de Aplicações com uma identidade gerida.  Usando o Azure RBAC, você pode dar ao código de identidade acesso a outro recurso, assim como qualquer diretor de segurança. A função *de Colaborador de Dados blob de armazenamento* dá à aplicação web (representada pelo sistema atribuído identidade gerida) ler, escrever e apagar o acesso ao recipiente e dados blob.

# <a name="portal"></a>[Portal](#tab/azure-portal)
No [portal Azure,](https://portal.azure.com)entre na sua conta de armazenamento para garantir o acesso à sua aplicação web.  Selecione **o controlo de acesso (IAM)** na navegação esquerda e, em seguida, **atribuições de funções**.  Verá uma lista de quem tem acesso à conta de armazenamento.  Agora quer adicionar uma atribuição de funções a um robô, o serviço de aplicações que precisa de acesso à conta de armazenamento.  Selecione **Add** -> **Adicionar Ação de função**.

In **Role** , selecione **Storage Blob Data Contributor** para dar à sua aplicação web acesso a bolhas de armazenamento de leitura.  No **Acesso a Atribuir acesso a** , selecione Serviço de **Aplicações**.  Na **Subscrição,** selecione a sua subscrição.  Em seguida, selecione o Serviço de Aplicações a que pretende ter acesso comprovado.  Clique em **Guardar**.

:::image type="content" alt-text="Adicionar atribuição de função" source="./media/scenario-secure-app-access-storage/add-role-assignment.png":::

A sua aplicação web tem agora acesso à sua conta de armazenamento.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Execute o seguinte script para atribuir a sua aplicação web (representada por um sistema atribuído identidade gerida) a função *de Contribuinte de Dados blob de armazenamento* na sua conta de armazenamento.

```powershell
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp20201102125811"
$storageName="securewebappstorage"

$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid
$storageId= (Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageName).Id
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Storage Blob Data Contributor" -Scope $storageId
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Execute o seguinte script para atribuir a sua aplicação web (representada por um sistema atribuído identidade gerida) a função *de Contribuinte de Dados blob de armazenamento* na sua conta de armazenamento.

```azurecli-interactive
spID=$(az resource list -n SecureWebApp20201102125811 --query [*].identity.principalId --out tsv)

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az role assignment create --assignee $spID --role 'Storage Blob Data Contributor' --scope $storageId
```

---

## <a name="access-blob-storage-net"></a>Armazenamento de bolhas de acesso (.NET)

A classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) é usada para obter uma credencial simbólica para o seu código para autorizar pedidos para o Azure Storage.  Crie uma instância da classe [DefaultAzureCredential,](/dotnet/api/azure.identity.defaultazurecredential) que utiliza a identidade gerida para ir buscar fichas e anexá-las ao cliente de serviço. O seguinte exemplo de código obtém a credencial de token autenticada e usa-a para criar um objeto de cliente de serviço, que carrega uma nova bolha.  

### <a name="install-client-library-packages"></a>Instalar pacotes de bibliotecas de clientes

Instale o [pacote NuGet de armazenamento Blob](https://www.nuget.org/packages/Azure.Storage.Blobs/) para trabalhar com o serviço de armazenamento Blob e a biblioteca cliente [da Identidade Azure para o pacote .NET NuGet](https://www.nuget.org/packages/Azure.Identity/) para autenticar com credenciais AD AZure.  Instale as bibliotecas clientes utilizando a interface de linha de comando .NET Core ou a consola de gestores de pacotes no Estúdio Visual.

# <a name="command-line"></a>[Linha de Comando](#tab/command-line)

Abra uma linha de comando e mude para o diretório que contém o seu ficheiro de projeto.

Executar os comandos de instalação:

```dotnetcli
dotnet add package Azure.Storage.Blobs

dotnet add package Azure.Identity
```

# <a name="package-manager"></a>[Gestor de pacotes](#tab/package-manager)

Abra o projeto/solução no Estúdio Visual e abra a consola utilizando o comando de gestor de **pacotes Tools**  >  **NuGet Package**  >  **Manager.**

Executar os comandos de instalação:
```powershell
Install-Package Azure.Storage.Blobs

Install-Package Azure.Identity
```

---

### <a name="example"></a>Exemplo

```csharp
using System;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;
using System.IO;
using Azure.Identity;

// Some code omitted for brevity.

static public async Task UploadBlob(string accountName, string containerName, string blobName, string blobContents)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (Exception e)
    {
        throw e;
    }
}
```

## <a name="clean-up-resources"></a>Limpar recursos

Se terminar este tutorial e já não precisar da aplicação web ou dos recursos associados, [limpe os recursos que criou.](scenario-secure-app-clean-up-resources.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
>
> * Criar um sistema atribuído identidade gerida
> * Criar uma conta de armazenamento e um recipiente de armazenamento Blob
> * Armazenamento de acesso a partir de uma aplicação web usando identidades geridas

> [!div class="nextstepaction"]
> [Serviço de aplicações acede ao Microsoft Graph em nome do utilizador](scenario-secure-app-access-microsoft-graph-as-user.md)
