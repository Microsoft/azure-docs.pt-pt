---
title: Quickstart - Biblioteca de clientes Azure Key Vault para .NET (SDK v3)
description: Saiba como criar, recuperar e apagar segredos de um cofre de chaves Azure utilizando a biblioteca de clientes .NET (v3)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 816cf8d385c12046a5363cf94ab5fa60e5d77e48
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078872"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>Quickstart: Biblioteca de clientes Azure Key Vault para .NET (SDK v3)

Começa com a biblioteca de clientes Azure Key Vault para .NET. Siga os passos abaixo para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

> [!NOTE]
> Este quickstart utiliza a versão v3.0.4 da biblioteca cliente Microsoft.Azure.KeyVault. Para utilizar a versão mais atualizada da biblioteca do cliente Key Vault, consulte a [biblioteca de clientes Azure Key Vault para .NET (SDK v4)](quick-create-net.md). 

[Documentação de](https://docs.microsoft.com/dotnet/api/overview/azure/keyvault/v3)  |  referência da API [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.KeyVault)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Cada cofre deve ter um nome único. Substitua <o seu nome único de teclado> pelo nome do seu cofre-chave nos seguintes exemplos.


## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* O [Núcleo .NET 3.1 SDK ou mais tarde](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [CLI do Azure](/cli/azure/install-azure-cli)

Este quickstart está a usar `dotnet` e o Azure CLI

## <a name="setting-up"></a>Configuração

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

1. Execute o comando `login`.

    ```azurecli-interactive
    az login
    ```

    Se o CLI conseguir abrir o seu navegador predefinido, fá-lo-á e carregará uma página de inscrição do Azure.

    Caso contrário, abra uma página do navegador [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e introduza o código de autorização exibido no seu terminal.

2. Inicie sessão com as credenciais da sua conta no browser.

### <a name="create-new-net-console-app"></a>Criar nova aplicação de consola .NET

Numa janela de consola, utilize o `dotnet new` comando para criar uma nova aplicação de consola .NET com o nome `akv-dotnet` .


```console
dotnet new console -n akvdotnet
```

Mude o seu diretório para a pasta de aplicações recém-criada. Pode construir a aplicação com:

```console
dotnet build
```

A saída de construção não deve conter avisos ou erros.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Instale o pacote

A partir da janela da consola, instale a biblioteca cliente Azure Key Vault para biblioteca .NET e [AppAuthentication:](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)

```console
dotnet add package Microsoft.Azure.KeyVault
dotnet add package Microsoft.Azure.Services.AppAuthentication
```

Para este arranque rápido, também terá de instalar os seguintes pacotes:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e cofre chave

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Conceder acesso ao seu cofre chave

Crie uma política de acesso para o cofre-chave que concede permissão secreta à sua conta de utilizador

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

## <a name="object-model"></a>Modelo de objeto

A biblioteca de clientes Azure Key Vault para .NET permite-lhe gerir chaves e ativos relacionados, tais como certificados e segredos. As amostras de código abaixo mostrar-lhe-ão como definir um segredo e recuperar um segredo.

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas ao topo do seu código:

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Azure.KeyVault;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Azure.Management.ResourceManager.Fluent;
using Microsoft.Azure.Management.ResourceManager.Fluent.Authentication;
using Microsoft.Azure.Services.AppAuthentication;
```

### <a name="authenticate-to-your-key-vault"></a>Autenticar para o seu cofre de chaves

Configure a autenticação à sua aplicação na [classe KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient). No nosso exemplo, utilizará o token fornecido pelo `AzureServiceTokenProvider` utilizador atualmente iniciado:

```csharp
var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));
```

Para mais informações `AzureServiceTokenProvider` sobre , consulte [Autenticação nos Serviços Azure](https://docs.microsoft.com/azure/key-vault/general/service-to-service-authentication#authenticating-to-azure-services)

### <a name="save-a-secret"></a>Salvar um segredo

Agora que a sua aplicação é autenticada, pode colocar um segredo no seu keyvault utilizando o [método SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) Isto requer o URL do seu cofre-chave, que está no formulário `https://<your-unique-keyvault-name>.vault.azure.net/secrets/` . Também requer um nome para o segredo. 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Pode verificar se o segredo foi definido com o comando [secreto az keyvault:](/cli/azure/keyvault/secret?#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Agora pode recuperar o valor previamente definido com o [método GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

O teu segredo está agora guardado `keyvaultSecret.Value;` como.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o Azure CLI ou o Azure PowerShell para remover o cofre da chave e o grupo de recursos correspondente.

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
using System.Threading.Tasks;
using Microsoft.Azure.KeyVault;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Management.ResourceManager.Fluent;
using Microsoft.Azure.Management.ResourceManager.Fluent.Authentication;
// </directives>

namespace akvdotnet
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Program P = new Program();
            string secretName = "mySecret";

            // kvURL must be updated to the URL of your key vault
            string kvURL = "https://myKV.vault.azure.net";

            // <authentication>

            var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
            var kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));        
            // </authentication>


            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.Write("Saving the value of your secret to your key vault ...");

            //set secret
            await kvClient.SetSecretAsync($"{kvURL}", secretName, secretValue);

            Console.WriteLine("done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");
            Console.WriteLine("Retrieving your secret from key vault.");

            //retrieve secret
            var keyvaultSecret = await kvClient.GetSecretAsync($"{kvURL}", secretName).ConfigureAwait(false);

            secretValue = keyvaultSecret.Result;
            Console.WriteLine("Your secret is " + secretValue);
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Implementar [autenticação de serviço-a-serviço para Azure Key Vault usando .NET](../general/service-to-service-authentication.md)
- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Consulte o [guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Rever [as melhores práticas do Azure Key Vault](../general/best-practices.md)
