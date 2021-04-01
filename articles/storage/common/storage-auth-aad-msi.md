---
title: Autorizar o acesso a dados com identidade gerida
titleSuffix: Azure Storage
description: Utilize identidades geridas para os recursos da Azure para autorizar o acesso de dados blob e de fila a partir de aplicações em execução em VMs Azure, aplicações de função, entre outras.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 552d2587f35ed391b470c6d5b1693b79fd57306b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879583"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Autorizar o acesso a dados de blob e fila com identidades geridas para recursos Azure

Azure Blob e Suporte de armazenamento de fila suportam autenticação do Azure Ative Directory (Azure AD) com [identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md). Identidades geridas para recursos Azure podem autorizar o acesso a dados de blob e fila usando credenciais AD Azure a partir de aplicações em execução em máquinas virtuais Azure (VMs), aplicações de função, conjuntos de escala de máquinas virtuais e outros serviços. Ao utilizar identidades geridas para recursos Azure juntamente com a autenticação Azure AD, pode evitar armazenar credenciais com as suas aplicações que funcionam na nuvem.  

Este artigo mostra como autorizar o acesso a dados de blob ou fila a partir de um VM Azure usando identidades geridas para recursos Azure. Também descreve como testar o seu código no ambiente de desenvolvimento.

## <a name="enable-managed-identities-on-a-vm"></a>Permitir identidades geridas num VM

Antes de poder utilizar identidades geridas para a Azure Resources para autorizar o acesso a bolhas e filas a partir do seu VM, tem primeiro de ativar identidades geridas para recursos Azure no VM. Para aprender como permitir identidades geridas para recursos Azure, consulte um destes artigos:

- [Portal do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de clientes Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Para obter mais informações sobre identidades geridas, consulte [identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library"></a>Autenticar com a biblioteca de identidade Azure

A biblioteca de clientes Azure Identity fornece suporte de autenticação simbólica Azure Azure para o [Azure SDK](https://github.com/Azure/azure-sdk). As versões mais recentes das bibliotecas de clientes do Azure Storage para .NET, Java, Python e JavaScript integram-se na biblioteca de Identidade Azure para fornecer um meio simples e seguro para adquirir um token OAuth 2.0 para autorização de pedidos de Armazenamento Azure.

Uma vantagem da biblioteca de clientes da Identidade Azure é que permite utilizar o mesmo código para autenticar se a sua aplicação está a decorrer no ambiente de desenvolvimento ou no Azure. A biblioteca cliente da Azure Identity para .NET autentica um principal de segurança. Quando o seu código está em execução em Azure, o diretor de segurança é uma identidade gerida para os recursos da Azure. No ambiente de desenvolvimento, a identidade gerida não existe, pelo que a biblioteca do cliente autentica o utilizador ou um principal de serviço para efeitos de teste.

Após a autenticação, a biblioteca de clientes da Identidade Azure obtém uma credencial simbólica. Esta credencial simbólica é então encapsulada no objeto do cliente de serviço que cria para realizar operações contra o Azure Storage. A biblioteca trata disto sem problemas, obtendo a credencial simbólica apropriada.

Para obter mais informações sobre a biblioteca de clientes Azure Identity para .NET, consulte a [biblioteca de clientes da Identidade Azure para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity). Para obter documentação de referência para a biblioteca do cliente da Identidade Azure, consulte [Azure.Identity Namespace](/dotnet/api/azure.identity).

### <a name="assign-azure-roles-for-access-to-data"></a>Atribuir funções da Azure para acesso aos dados

Quando um diretor de segurança da AZure AD tenta aceder a dados de blob ou fila, esse diretor de segurança deve ter permissões para o recurso. Se o principal de segurança é uma identidade gerida em Azure ou uma conta de utilizador Azure AD que executa código no ambiente de desenvolvimento, o principal de segurança deve ser atribuído a uma função Azure que concede acesso a dados de blob ou fila no Azure Storage. Para obter informações sobre a atribuição de permissões através do Azure RBAC, consulte a secção intitulada **Atribuir funções Azure para direitos de acesso** em Acesso autorizado a [bolhas e filas Azure utilizando o Azure Ative Directory](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights).

> [!NOTE]
> Quando cria uma conta de Armazenamento Azure, não lhe são atribuídas automaticamente permissões de acesso aos dados através do Azure AD. Deve atribuir-se explicitamente um papel Azure para o Azure Storage. Pode atribuí-lo ao nível da sua subscrição, grupo de recursos, conta de armazenamento ou contentor ou fila.
>
> Antes de atribuir uma função para o acesso aos dados, poderá aceder aos dados na sua conta de armazenamento através do portal Azure, uma vez que o portal Azure também pode utilizar a chave de conta para o acesso aos dados. Para mais informações, consulte [Escolha como autorizar o acesso aos dados blob no portal Azure.](../blobs/authorize-data-operations-portal.md)

### <a name="authenticate-the-user-in-the-development-environment"></a>Autenticar o utilizador no ambiente de desenvolvimento

Quando o seu código está a ser em execução no ambiente de desenvolvimento, a autenticação pode ser tratada automaticamente, ou pode exigir um login do navegador, dependendo das ferramentas que está a utilizar. Por exemplo, o Microsoft Visual Studio suporta uma única sactivação (SSO), de modo a que a conta de utilizador AZure AD ativa seja automaticamente utilizada para a autenticação. Para obter mais informações sobre sSO, consulte [o ''s'on's single para as aplicações](../../active-directory/manage-apps/what-is-single-sign-on.md).

Outras ferramentas de desenvolvimento podem levá-lo a iniciar sessão através de um navegador web.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Autenticar um diretor de serviço no ambiente de desenvolvimento

Se o seu ambiente de desenvolvimento não suporta um único login ou login através de um navegador web, então pode usar um principal de serviço para autenticar a partir do ambiente de desenvolvimento.

#### <a name="create-the-service-principal"></a>Criar o principal de serviço

Para criar um diretor de serviço com Azure CLI e atribuir um papel Azure, ligue para o comando [ad sp create-for-rbac.](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) Forneça uma função de acesso aos dados do Azure Storage para atribuir ao novo diretor de serviço. Além disso, forneça a margem para a atribuição de funções. Para obter mais informações sobre as funções incorporadas previstas para o Azure Storage, consulte [as funções incorporadas do Azure.](../../role-based-access-control/built-in-roles.md)

Se não tiver permissões suficientes para atribuir uma função ao titular do serviço, poderá ter de pedir ao titular ou administrador da conta para desempenhar a função.

O exemplo a seguir utiliza o CLI Azure para criar um novo diretor de serviço e atribuir-lhe a função **de Leitor de Dados blob de armazenamento** com âmbito de conta

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Contributor" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

O `az ad sp create-for-rbac` comando devolve uma lista de propriedades principais de serviço no formato JSON. Copie estes valores para que possa usá-los para criar as variáveis ambientais necessárias no passo seguinte.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> As atribuições de funções azure podem demorar alguns minutos a propagar-se.

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente

A biblioteca cliente da Identidade Azure lê valores de três variáveis ambientais em tempo de execução para autenticar o principal serviço. A tabela seguinte descreve o valor a definir para cada variável ambiente.

|Variável de ambiente|Valor
|-|-
|`AZURE_CLIENT_ID`|O ID da aplicação para o diretor de serviço
|`AZURE_TENANT_ID`|O promotor de serviço Azure AD iD inquilino
|`AZURE_CLIENT_SECRET`|A palavra-passe gerada para o principal serviço

> [!IMPORTANT]
> Depois de definir as variáveis ambientais, feche e reabriu a janela da consola. Se estiver a utilizar o Visual Studio ou outro ambiente de desenvolvimento, poderá ter de reiniciar o ambiente de desenvolvimento para que registe as novas variáveis ambientais.

Para mais informações, consulte [Criar identidade para a aplicação Azure no portal.](../../active-directory/develop/howto-create-service-principal-portal.md)

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET código exemplo: Criar uma bolha de bloco

Adicione as `using` seguintes diretivas ao seu código para utilizar as bibliotecas de clientes Azure Identity e Azure Storage.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
```

Para obter uma credencial simbólica que o seu código pode usar para autorizar pedidos para o Azure Storage, crie uma instância da classe [DefaultAzureCredential.](/dotnet/api/azure.identity.defaultazurecredential) O exemplo de código que se segue mostra como obter a credencial de token autenticada e usá-la para criar um objeto de cliente de serviço, em seguida, usar o cliente de serviço para carregar uma nova bolha:

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
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
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Para autorizar pedidos contra dados de blob ou fila com a Azure AD, deve utilizar HTTPS para esses pedidos.

## <a name="next-steps"></a>Passos seguintes

- [Gerir os direitos de acesso aos dados de armazenamento com o Azure RBAC.](./storage-auth-aad-rbac-portal.md)
- [Utilize a Azure AD com aplicações de armazenamento](storage-auth-aad-app.md).
- [Executar comandos PowerShell com credenciais AD AD Azure para aceder a dados blob](../blobs/authorize-data-operations-powershell.md)
- [Tutorial: Armazenamento de acesso a partir do Serviço de Aplicações utilizando identificações geridas](../../app-service/scenario-secure-app-access-storage.md)