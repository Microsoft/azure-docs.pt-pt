---
title: Quickstart - Azure Key Vault secrets client library for .NET (versão 4)
description: Saiba como criar, recuperar e apagar segredos de um cofre de chaves Azure utilizando a biblioteca de clientes .NET (versão 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: ecd5fd4f5af883d26f904181796a78f61669b37a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187362"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-net-sdk-v4"></a>Quickstart: Azure Key Vault biblioteca secreta de clientes para .NET (SDK v4)

Começa com a biblioteca secreta do Azure Key Vault para .NET. [Azure Key Vault](../general/overview.md) é um serviço de nuvem que fornece uma loja segura para segredos. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Os cofres de chaves do Azure podem ser criados e geridos através do portal do Azure. Neste arranque rápido, aprende-se a criar, recuperar e apagar segredos de um cofre de chaves Azure utilizando a biblioteca de clientes .NET

Recursos da biblioteca do cliente Key Vault:

[Documentação de](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet&preserve-view=true)  |  referência da API [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

Para mais informações sobre o Cofre-Chave e segredos, consulte:
- [Visão geral do cofre de chaves](../general/overview.md)
- [Visão geral dos segredos.](about-secrets.md)

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

A partir da concha de comando, instale a biblioteca secreta do Azure Key Vault para .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
```

Para este arranque rápido, também terá de instalar a biblioteca de clientes Azure SDK para a Identidade Azure:

```dotnetcli
dotnet add package Azure.Identity
```

#### <a name="grant-access-to-your-key-vault"></a>Conceder acesso ao seu cofre chave

Crie uma política de acesso para o cofre-chave que concede permissão secreta à sua conta de utilizador

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
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

A biblioteca secreta do Azure Key Vault para .NET permite-lhe gerir segredos. A secção [de exemplos de Código](#code-examples) mostra como criar um cliente, estabelecer um segredo, recuperar um segredo e apagar um segredo.

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas ao topo de *Program.cs:*

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Neste arranque rápido, o utilizador com sessão é utilizado para autenticar o cofre de chaves, que é o método preferido para o desenvolvimento local. Para aplicações implantadas no Azure, a identidade gerida deve ser atribuída ao Serviço de Aplicações ou Máquina Virtual, para obter mais informações, consulte [a Visão Geral da Identidade Gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Por exemplo, o nome do seu cofre-chave é expandido para o cofre uri chave, no formato "https:// \<your-key-vault-name\> .vault.azure.net". Este exemplo está a usar a classe  ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential) que permite usar o mesmo código em diferentes ambientes com diferentes opções para fornecer identidade. Para mais informações, consulte [a Autenticação Credencial Azure Padrão.](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme?#defaultazurecredential) 

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Salvar um segredo

Agora que a aplicação da consola está autenticada, adicione um segredo ao cofre da chave. Para esta tarefa, utilize o método [SetSecretAsync.](/dotnet/api/azure.security.keyvault.secrets.secretclient.setsecretasync) O primeiro parâmetro do método aceita um nome para o &mdash; "mySecret" secreto nesta amostra.

```csharp
await client.SetSecretAsync(secretName, secretValue);
```

> [!NOTE]
> Se o nome secreto existir, acima do código criará uma nova versão desse segredo.


### <a name="retrieve-a-secret"></a>Recuperar um segredo

Agora pode recuperar o valor previamente definido com o método [GetSecretAsync.](/dotnet/api/azure.security.keyvault.secrets.secretclient.getsecretasync)

```csharp
var secret = await client.GetSecretAsync(secretName);
``````

O teu segredo está agora guardado `secret.Value` como.

### <a name="delete-a-secret"></a>Eliminar um segredo

Finalmente, vamos apagar o segredo do seu cofre com o método [StartDeleteSecretAsync.](/dotnet/api/azure.security.keyvault.secrets.secretclient.startdeletesecretasync)

```csharp
await client.StartDeleteSecretAsync(secretName);
```

## <a name="sample-code"></a>Código de exemplo

Modifique a aplicação de consola .NET Core para interagir com o Cofre de Chaves, completando os seguintes passos:

1. Substitua o código em *Program.cs* pelo seguinte código:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                await client.SetSecretAsync(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                var secret = await client.GetSecretAsync(secretName);
                Console.WriteLine($"Your secret is '{secret.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                DeleteSecretOperation operation = await client.StartDeleteSecretAsync(secretName);
                // You only need to wait for completion if you want to purge or recover the secret.
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
                
                Console.Write($"Purging your secret from {keyVaultName} ...");
                await client.PurgeDeletedSecretAsync(secretName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testar e verificar

1. Execute o seguinte comando para executar a aplicação.

    ```dotnetcli
    dotnet run
    ```

1. Quando solicitado, insira um valor secreto. Por exemplo, o meu Código de Passagem.

    É apresentada uma variação da seguinte saída:

    ```console
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.    
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o Azure CLI ou o Azure PowerShell para remover o cofre da chave e o grupo de recursos correspondente.

### <a name="delete-a-key-vault"></a>Apagar um cofre de chaves

```azurecli
az keyvault delete --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### <a name="purge-a-key-vault"></a>Purgue um cofre de chaves

```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name> -InRemovedState -Location eastus
```

### <a name="delete-a-resource-group"></a>Eliminar um grupo de recursos

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Key Vault e como integrá-lo com as suas apps, consulte os seguintes artigos:

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Veja um [cofre de chave de acesso a partir do tutorial de aplicações do serviço de aplicações de aplicações de aplicações](../general/tutorial-net-create-vault-azure-web-app.md)
- Veja um [cofre de chave de acesso a partir de tutorial de máquina virtual](../general/tutorial-net-virtual-machine.md)
- Consulte o [guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Rever [as melhores práticas do Azure Key Vault](../general/best-practices.md)