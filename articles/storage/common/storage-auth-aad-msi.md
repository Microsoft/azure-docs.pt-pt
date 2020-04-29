---
title: Autorizar acesso a dados com identidade gerida
titleSuffix: Azure Storage
description: Saiba como usar identidades geridas para os recursos do Azure para autorizar o acesso a dados blob e fila de aplicações em execução em máquinas virtuais Azure, aplicações de função, conjuntos de escala de máquinas virtuais, entre outros.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f3bac0d47a53da1ec4d1fa08b5f0933f5f65dc56
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79255343"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Autorizar acesso a dados blob e fila com identidades geridas para recursos Azure

A autenticação azure Blob e de armazenamento de fila Azure Ative Directory (Azure AD) com [identidades geridas para os recursos Azure.](../../active-directory/managed-identities-azure-resources/overview.md) As identidades geridas para os recursos do Azure podem autorizar o acesso a dados de blob e fila utilizando credenciais Azure AD de aplicações em execução em máquinas virtuais Azure (VMs), aplicações de função, conjuntos de escala de máquinas virtuais e outros serviços. Ao utilizar identidades geridas para recursos Azure juntamente com a autenticação da AD Azure, pode evitar armazenar credenciais com as suas aplicações que funcionam na nuvem.  

Este artigo mostra como autorizar o acesso a dados blob ou fila de um VM Azure usando identidades geridas para os Recursos Azure. Também descreve como testar o seu código no ambiente de desenvolvimento.

## <a name="enable-managed-identities-on-a-vm"></a>Ativar identidades geridas num VM

Antes de poder utilizar identidades geridas para a Azure Resources para autorizar o acesso a bolhas e filas a partir do seu VM, tem primeiro de ativar identidades geridas para os Recursos Azure no VM. Para aprender a ativar identidades geridas para os Recursos Azure, consulte um destes artigos:

- [Portal do Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo de Gestor de Recursos Azure](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de clientes do Gestor de Recursos Azure](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Para obter mais informações sobre identidades geridas, consulte [identidades geridas para os recursos Do Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="authenticate-with-the-azure-identity-library"></a>Autenticar com a biblioteca Azure Identity

A biblioteca de clientes Azure Identity fornece suporte de autenticação token Azure Azure Azure Para o [Azure SDK.](https://github.com/Azure/azure-sdk) As versões mais recentes das bibliotecas de clientes do Azure Storage para .NET, Java, Python e JavaScript integram-se com a biblioteca Azure Identity para fornecer um meio simples e seguro para adquirir um símbolo OAuth 2.0 para autorização de pedidos de armazenamento Azure.

Uma vantagem da biblioteca de clientes Da Identidade Azure é que lhe permite usar o mesmo código para autenticar se a sua aplicação está em execução no ambiente de desenvolvimento ou no Azure. A biblioteca de clientes Azure Identity para .NET autentica um diretor de segurança. Quando o seu código está em funcionamento em Azure, o diretor de segurança é uma identidade gerida para os recursos azure. No ambiente de desenvolvimento, a identidade gerida não existe, pelo que a biblioteca do cliente autentica o utilizador ou um diretor de serviço para fins de teste.

Depois de autenticar, a biblioteca de clientes Da Identidade Azure recebe uma credencial simbólica. Esta credencial simbólica é então encapsulada no objeto cliente de serviço que cria para realizar operações contra o Armazenamento Azure. A biblioteca lida com isto sem problemas, obtendo a credencial simbólica apropriada.

Para obter mais informações sobre a biblioteca de clientes Azure Identity para .NET, consulte a [biblioteca de clientes Azure Identity para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity). Para documentação de referência para a biblioteca de clientes Da Identidade Azure, consulte [O Espaço de Nome sinuoso de Identidade Azure.Identidade](/dotnet/api/azure.identity).

### <a name="assign-role-based-access-control-rbac-roles-for-access-to-data"></a>Atribuir funções de controlo de acesso baseado em funções (RBAC) para o acesso aos dados

Quando um diretor de segurança da AD Azure tenta aceder a dados de blob ou fila, esse diretor de segurança deve ter permissões para o recurso. Quer o diretor de segurança seja uma identidade gerida no Azure ou um código de execução de conta de utilizador Azure AD no ambiente de desenvolvimento, o diretor de segurança deve ser atribuído a uma função RBAC que conceda acesso a dados blob ou fila no Armazenamento Azure. Para obter informações sobre a atribuição de permissões via RBAC, consulte a secção intitulada **Assign RBAC para direitos** de acesso em [Autorizao acesso a blobs e filas Azure utilizando o Diretório Ativo Azure](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

### <a name="authenticate-the-user-in-the-development-environment"></a>Autenticar o utilizador no ambiente de desenvolvimento

Quando o seu código estiver em funcionamento no ambiente de desenvolvimento, a autenticação pode ser manuseada automaticamente, ou pode necessitar de um login de navegador, dependendo das ferramentas que estiver a utilizar. Por exemplo, o Microsoft Visual Studio suporta um único sinal (SSO), de modo a que a conta de utilizador aD Azure ativa seja automaticamente utilizada para autenticação. Para obter mais informações sobre o SSO, consulte [o único sinal de inscrição para as aplicações](../../active-directory/manage-apps/what-is-single-sign-on.md).

Outras ferramentas de desenvolvimento podem levar-lhe a fazer login através de um navegador web.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Autenticar um principal de serviço no ambiente de desenvolvimento

Se o seu ambiente de desenvolvimento não suportar um único login ou login através de um navegador web, então pode usar um diretor de serviço para autenticar a partir do ambiente de desenvolvimento.

#### <a name="create-the-service-principal"></a>Criar o principal de serviço

Para criar um diretor de serviço com o Azure CLI e atribuir uma função RBAC, ligue para o comando [az ad sp create-for-rbac.](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) Fornecer uma função de acesso a dados de Armazenamento Azure para atribuir ao novo diretor de serviço. Além disso, fornecer o âmbito para a atribuição do papel. Para obter mais informações sobre as funções incorporadas previstas para o Armazenamento Azure, consulte [as funções incorporadas para os recursos Azure.](../../role-based-access-control/built-in-roles.md)

Se não tiver permissões suficientes para atribuir uma função ao diretor de serviço, poderá ter de pedir ao proprietário ou administrador da conta que execute a atribuição da função.

O exemplo seguinte utiliza o Azure CLI para criar um novo diretor de serviço e atribuir-lhe o papel de Leitor de **Dados blob** de armazenamento com âmbito de conta

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Reader" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

O `az ad sp create-for-rbac` comando devolve uma lista de propriedades principais de serviço em formato JSON. Copie estes valores para que possa usá-los para criar as variáveis ambientais necessárias no próximo passo.

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
> As atribuições de funções RBAC podem demorar alguns minutos a propagar-se.

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente

A biblioteca de clientes Azure Identity lê valores de três variáveis ambientais em tempo de execução para autenticar o diretor de serviço. O quadro seguinte descreve o valor a definir para cada variável ambiental.

|Variável de ambiente|Valor
|-|-
|`AZURE_CLIENT_ID`|O ID da aplicação para o diretor de serviço
|`AZURE_TENANT_ID`|A identidade do inquilino Azure AD do diretor de serviço
|`AZURE_CLIENT_SECRET`|A palavra-passe gerada para o diretor de serviço

> [!IMPORTANT]
> Depois de definir as variáveis ambientais, feche e reabra a janela da consola. Se estiver a utilizar o Visual Studio ou outro ambiente de desenvolvimento, poderá ter de reiniciar o ambiente de desenvolvimento para que registe as novas variáveis ambientais.

Para mais informações, consulte [Criar identidade para app Azure no portal](../../active-directory/develop/howto-create-service-principal-portal.md).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Exemplo de código .NET: Criar uma bolha de bloco

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

Para obter uma credencial simbólica que o seu código pode usar para autorizar pedidos de Armazenamento Azure, crie uma instância da classe [DefaultAzureCredential.](/dotnet/api/azure.identity.defaultazurecredential) O seguinte exemplo de código mostra como obter a credencial simbólica autenticada e usá-la para criar um objeto cliente de serviço, em seguida, usar o cliente de serviço para carregar uma nova bolha:

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

- [Gerir os direitos de acesso aos dados](storage-auth-aad-rbac.md)de armazenamento com rBAC .
- [Utilize o Azure AD com aplicações](storage-auth-aad-app.md)de armazenamento.
- [Executar comandos Azure CLI ou PowerShell com credenciais Azure AD para aceder a dados de blob ou fila](authorize-active-directory-powershell.md).
