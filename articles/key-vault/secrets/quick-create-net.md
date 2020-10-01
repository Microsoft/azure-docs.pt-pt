---
title: Quickstart - Biblioteca de clientes Azure Key Vault para .NET (v4)
description: Saiba como criar, recuperar e apagar segredos de um cofre de chaves Azure utilizando a biblioteca de clientes .NET (v4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/30/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: cc5aa7f10d83edc757e99820b9591a953df72062
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612256"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>Quickstart: Biblioteca de clientes Azure Key Vault para .NET (SDK v4)

Começa com a biblioteca de clientes Azure Key Vault para .NET. Siga os passos abaixo para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

O cofre de chave do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em nuvem e pelos serviços. Utilize a biblioteca do cliente Key Vault para .NET para:

- Aumentar a segurança e o controlo sobre chaves e senhas.
- Criar e importar chaves de encriptação em minutos.
- Reduza a latência com a escala de nuvens e a redundância global.
- Simplificar e automatizar tarefas para certificados TLS/SSL.
- Utilize OS HSMs validados FIPS 140-2.

[Documentação de](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)  |  referência da API [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK ou mais tarde](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/)

## <a name="setup"></a>Configuração

### <a name="create-a-new-console-app"></a>Criar uma nova aplicação para consolas

Complete os seguintes passos para criar e compilar uma aplicação de consola .NET Core.

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

A partir da concha de comando, instale a biblioteca de clientes Azure Key Vault para .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
```

Para este arranque rápido, também terá de instalar a biblioteca de clientes Azure SDK para a Identidade Azure:

```dotnetcli
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e cofre chave

[!INCLUDE[Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Criar um principal de serviço

[!INCLUDE[Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Dê ao serviço acesso principal ao seu cofre de chaves

<!-- TODO: need to pass -g myResourceGroup to this command too -->
[!INCLUDE[Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente

[!INCLUDE[Set environment variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>Modelo de objeto

A biblioteca de clientes Azure Key Vault para .NET permite-lhe gerir chaves e ativos relacionados. Exemplos de bens relacionados incluem certificados e segredos. A secção [de exemplos de Código](#code-examples) mostra como criar um cliente, estabelecer um segredo, recuperar um segredo e apagar um segredo.

Toda a aplicação para consolas está disponível em https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app .

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas ao topo de *Program.cs:*

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

A autenticação do cofre-chave e a criação do cliente dependem das variáveis ambientais na etapa de [variáveis ambiente set.](#set-environment-variables) O nome do seu cofre-chave é expandido para o cofre uri chave, no formato `https://<your-key-vault-name>.vault.azure.net` . O seguinte código utiliza [o DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) para autenticação no cofre da chave, que está a ler variáveis ambientais para recuperar o token de acesso.

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Salvar um segredo

Agora que a aplicação da consola está autenticada, adicione um segredo ao cofre da chave. Para esta tarefa, use o [cliente. Definir Método deSecret.](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) O primeiro parâmetro do método aceita um nome para o &mdash; "mySecret" secreto nesta amostra.

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

Pode verificar se o segredo foi definido com o comando [secreto az keyvault:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Pode agora recuperar o valor previamente definido com o [cliente. Método GetSecret.](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

O teu segredo está agora guardado `secret.Value` como.

### <a name="delete-a-secret"></a>Eliminar um segredo

Finalmente, vamos apagar o segredo do seu cofre com o [cliente. Eliminar Método Desaconsesse.](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

Pode verificar se o segredo se foi com o comando secreto do [programa az keyvault:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

## <a name="clean-up-resources"></a>Limpar os recursos

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

## <a name="sample-code"></a>Código de exemplo

Modifique a aplicação de consola .NET Core para interagir com o Cofre de Chaves, completando os seguintes passos:

1. Substitua o código em *Program.cs* pelo seguinte código:

    ```csharp
    using System;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static void Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                client.SetSecret(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                KeyVaultSecret secret = client.GetSecret(secretName);
                Console.WriteLine($"Your secret is '{secret.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                client.StartDeleteSecret(secretName);
                System.Threading.Thread.Sleep(5000);
                Console.WriteLine(" done.");
            }
        }
    }
    ```

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

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criaste um cofre, armazenaste um segredo e recuperaste esse segredo. Veja toda a [aplicação de consola no GitHub.](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app)

Para saber mais sobre o Key Vault e como integrá-lo com as suas apps, consulte os seguintes artigos:

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Consulte o [guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Rever [as melhores práticas do Azure Key Vault](../general/best-practices.md)
