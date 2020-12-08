---
title: Quickstart - Azure Key Vault chaves biblioteca cliente para .NET (versão 4)
description: Aprenda a criar, recuperar e apagar chaves de um cofre de chaves Azure utilizando a biblioteca de clientes .NET (versão 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 77907b6e901ae074c879b4911a8ee755224a7948
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780421"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-net-sdk-v4"></a>Quickstart: Azure Key Vault key client library for .NET (SDK v4)

Começa com a biblioteca de clientes chave Azure Key Vault para .NET. [Azure Key Vault](../general/overview.md) é um serviço de nuvem que fornece uma loja segura para chaves criptográficas. Pode armazenar com segurança chaves criptográficas, senhas, certificados e outros segredos. Os cofres de chaves do Azure podem ser criados e geridos através do portal do Azure. Neste arranque rápido, aprende-se a criar, recuperar e apagar chaves de um cofre de chaves Azure utilizando a biblioteca de clientes .NET

Principais recursos da biblioteca do cliente key Vault:

[Documentação de](/dotnet/api/azure.security.keyvault.keys)  |  referência da API [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.keys/)

Para obter mais informações sobre o Cofre-Chaves e as chaves, consulte:
- [Visão geral do cofre de chaves](../general/overview.md)
- [Visão geral das chaves](about-keys.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK ou mais tarde](https://dotnet.microsoft.com/download/dotnet-core)
* [CLI do Azure](/cli/azure/install-azure-cli)
* Um Cofre-Chave - pode criar um utilizando o [portal Azure,](../general/quick-create-portal.md) [Azure CLI](../general/quick-create-cli.md)ou [Azure PowerShell](../general/quick-create-powershell.md).

Este quickstart está a usar `dotnet` e o Azure CLI

## <a name="setup"></a>Configuração

Este quickstart está a utilizar a biblioteca Azure Identity com o Azure CLI para autenticar o utilizador nos Serviços Azure. Os desenvolvedores também podem usar o Visual Studio ou Visual Studio Code para autenticar as suas chamadas, para obter mais informações, ver [Autenticar o cliente com a biblioteca de clientes da Azure Identity](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true).

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

1. Execute o comando `login`.

    ```azurecli-interactive
    az login
    ```

    Se o CLI conseguir abrir o seu navegador predefinido, fá-lo-á e carregará uma página de inscrição do Azure.

    Caso contrário, abra uma página do navegador [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e introduza o código de autorização exibido no seu terminal.

2. Inicie sessão com as credenciais da sua conta no browser.

#### <a name="grant-access-to-your-key-vault"></a>Conceder acesso ao seu cofre chave

Crie uma política de acesso para o cofre-chave que concede permissões chave à sua conta de utilizador

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
```

### <a name="create-new-net-console-app"></a>Criar nova aplicação de consola .NET

1. Numa concha de comando, executar o seguinte comando para criar um projeto chamado `key-vault-console-app` :

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Mude para o *recém-criado diretório de consola-consola-cofre-cofre,* e executar o seguinte comando para construir o projeto:

    ```dotnetcli
    dotnet build
    ```

    A saída de construção não deve conter avisos ou erros.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>Instalar as embalagens

A partir da concha de comando, instale a biblioteca do cliente chave Azure Key Vault para .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Keys
```

Para este arranque rápido, também terá de instalar a biblioteca de clientes Azure SDK para a Identidade Azure:

```dotnetcli
dotnet add package Azure.Identity
```

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente

Esta aplicação está a usar o nome do cofre como uma variável ambiental chamada `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS ou Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Modelo de objeto

A biblioteca de clientes chave Azure Key Vault para .NET permite-lhe gerir as chaves. A secção [de exemplos de Código](#code-examples) mostra como criar um cliente, definir uma chave, recuperar uma chave e apagar uma chave.

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas ao topo de *Program.cs:*

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Keys;
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Neste arranque rápido, o utilizador com sessão é utilizado para autenticar o cofre de chaves, que é o método preferido para o desenvolvimento local. Para aplicações implantadas no Azure, a identidade gerida deve ser atribuída ao Serviço de Aplicações ou Máquina Virtual, para obter mais informações, consulte [a Visão Geral da Identidade Gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Por exemplo, o nome do seu cofre-chave é expandido para o cofre uri chave, no formato "https:// \<your-key-vault-name\> .vault.azure.net". Este exemplo está a utilizar a classe ['DefaultAzureCredential)'](/dotnet/api/azure.identity.defaultazurecredential) da [Azure Identity Library,](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)que permite utilizar o mesmo código em diferentes ambientes com diferentes opções para fornecer identidade. Mais informações sobre a autenticação no cofre da chave, consulte [o Guia do Desenvolvedor.](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

```csharp
var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-key"></a>Guarde uma chave

Para esta tarefa, utilize o método [CreateKeyAsync.](/dotnet/api/azure.security.keyvault.keys.keyclient.createkeyasync) Os parâmetros do método aceitam um nome chave e o [tipo de chave](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.keys.keytype).

```csharp
var key = await client.CreateKeyAsync("myKey", KeyType.Rsa);
```

> [!NOTE]
> Se existir um nome-chave, o código acima criará uma nova versão dessa chave.

### <a name="retrieve-a-key"></a>Recuperar uma chave

Agora pode recuperar a chave previamente criada com o método [GetKeyAsync.](/dotnet/api/azure.security.keyvault.keys.keyclient.getkeyasync)

```csharp
var key = await client.GetKeyAsync("myKey");
```

### <a name="delete-a-key"></a>Eliminar uma chave

Finalmente, vamos eliminar e limpar a chave do seu cofre-chave com os métodos [StartDeleteKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.startdeletekeyasync) e [PurgeDeletedKeyAsync.](/dotnet/api/azure.security.keyvault.keys.keyclient.purgedeletedkeyasync)

```csharp
var operation = await client.StartDeleteKeyAsync("myKey");

// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

var key = operation.Value;
await client.PurgeDeletedKeyAsync("myKey");
```

## <a name="sample-code"></a>Código de exemplo

Modifique a aplicação de consola .NET Core para interagir com o Cofre de Chaves, completando os seguintes passos:

- Substitua o código em *Program.cs* pelo seguinte código:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Keys;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string keyName = "myKey";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a key in {keyVaultName} called '{keyName}' ...");
                var createdKey = await client.CreateKeyAsync(keyName, KeyType.Rsa);
                Console.WriteLine("done.");
    
                Console.WriteLine($"Retrieving your key from {keyVaultName}.");
                var key = await client.GetKeyAsync(keyName);
                Console.WriteLine($"Your key version is '{key.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your key from {keyVaultName} ...");
                var deleteOperation = await client.StartDeleteKeyAsync(keyName);
                // You only need to wait for completion if you want to purge or recover the key.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine("done.");

                Console.Write($"Purging your key from {keyVaultName} ...");
                await client.PurgeDeletedKeyAsync(keyName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testar e verificar

1.  Execute o seguinte comando para construir o projeto

    ```dotnetcli
    dotnet build
    ```

1. Execute o seguinte comando para executar a aplicação.

    ```dotnetcli
    dotnet run
    ```

1. Quando solicitado, insira um valor secreto. Por exemplo, o meu Código de Passagem.

    É apresentada uma variação da seguinte saída:

    ```console
    Creating a key in mykeyvault called 'myKey' ... done.
    Retrieving your key from mykeyvault.
    Your key version is '8532359bced24e4bb2525f2d2050738a'.
    Deleting your key from jl-kv ... done
    Purging your key from <your-unique-keyvault-name> ... done.   
    ```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criaste um cofre chave, armazenaste uma chave e recuperaste essa chave. 

Para saber mais sobre o Key Vault e como integrá-lo com as suas apps, consulte os seguintes artigos:

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Leia uma [visão geral das teclas](about-keys.md)
- Veja um [cofre de chave de acesso a partir do tutorial de aplicações do serviço de aplicações de aplicações de aplicações](../general/tutorial-net-create-vault-azure-web-app.md)
- Veja um [cofre de chave de acesso a partir de tutorial de máquina virtual](../general/tutorial-net-virtual-machine.md)
- Consulte o [guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Rever [as melhores práticas do Azure Key Vault](../general/best-practices.md)
