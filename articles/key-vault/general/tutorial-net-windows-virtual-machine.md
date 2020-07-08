---
title: Tutorial - Use Azure Key Vault com uma máquina virtual Windows em .NET / Microsoft Docs
description: Neste tutorial, você configura uma aplicação principal ASP.NET para ler um segredo do seu cofre chave.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 8db1c511ab9defb140720655588b27279a0f08be
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085485"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Tutorial: Use o cofre da chave Azure com uma máquina virtual Windows em .NET

O Azure Key Vault ajuda-o a proteger segredos como as chaves API, as cadeias de ligação de base de dados que precisa para aceder às suas aplicações, serviços e recursos de TI.

Neste tutorial, aprende-se a obter uma aplicação de consola para ler informações do Azure Key Vault. A aplicação usaria a identidade gerida por máquina virtual para autenticar o Key Vault. 

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Crie um grupo de recursos.
> * Criar um cofre de chaves.
> * Adicione um segredo ao cofre das chaves.
> * Obter um segredo do cofre de chaves.
> * Crie uma máquina virtual Azure.
> * Ativar uma [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md) para a Máquina Virtual.
> * Atribua permissões à identidade VM.

Antes de começar, leia [os conceitos básicos do Key Vault.](basic-concepts.md) 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Pré-requisitos

Para Windows, Mac e Linux:
  * [Git](https://git-scm.com/downloads)
  * O [Núcleo .NET 3.1 SDK ou mais tarde](https://dotnet.microsoft.com/download/dotnet-core/3.1).
  * [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-resources-and-assign-permissions"></a>Criar recursos e atribuir permissões

Antes de começar a codificar, precisa criar alguns recursos, colocar um segredo no cofre e atribuir permissões.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar súm na Azure utilizando o CLI Azure, insira:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). 

Este exemplo cria um grupo de recursos na localização dos EUA Ocidentais:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

O seu grupo de recursos recém-criado será utilizado durante todo este tutorial.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Crie um cofre chave e povoe-o com um segredo

Crie um cofre chave no seu grupo de recursos, fornecendo ao [keyvault az criar](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) comando com as seguintes informações:

* Nome do cofre chave: uma sequência de 3 a 24 caracteres que pode conter apenas números (0-9), letras (a-z, A-Z) e hífens (-)
* Nome do grupo de recursos
* Localização: **West US**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Neste momento, a sua conta Azure é a única que está autorizada a realizar operações neste novo cofre.

Agora adicione um segredo ao seu cofre chave usando o comando [secreto az keyvault](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)


Para criar um segredo no cofre de chaves chamado **AppSecret,** insira o seguinte comando:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Este segredo armazena o valor **MySecret.**

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Criar uma máquina virtual utilizando um dos seguintes métodos:

* [O Azure CLI](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [O portal do Azure](../../virtual-machines/windows/quick-create-portal.md)

### <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade ao VM
Criar uma identidade atribuída ao sistema para a máquina virtual com o comando [de atribuição de identidade az vm:](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign)

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Note a identidade atribuída ao sistema que é apresentada no seguinte código. A saída do comando anterior seria: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>Atribuir permissões à identidade VM
Atribua as permissões de identidade previamente criadas para o seu cofre de chaves com o comando [de definição de chave-chave az:](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Inscreva-se na máquina virtual

Para iniciar seduca na máquina virtual, siga as instruções em [Connect e inscreva-se numa máquina virtual Azure que executa o Windows](../../virtual-machines/windows/connect-logon.md).

## <a name="set-up-the-console-app"></a>Configurar a aplicação de consola

Crie uma aplicação para consolas e instale as embalagens necessárias utilizando o `dotnet` comando.

### <a name="install-net-core"></a>Instalar .NET Core

Para instalar .NET Core, aceda à página [de downloads .NET.](https://www.microsoft.com/net/download)

### <a name="create-and-run-a-sample-net-app"></a>Criar e executar uma amostra .NET app

Abra uma linha de comandos.

Pode imprimir "Hello World" na consola executando os seguintes comandos:

```console
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>Instale o pacote

A partir da janela da consola, instale a biblioteca cliente Azure Key Vault Secrets para .NET:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

Para este arranque rápido, terá de instalar o seguinte pacote de identidade para autenticar no Cofre da Chave Azure:

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>Editar a aplicação de consola

Abra o ficheiro *Program.cs* e adicione estes pacotes:

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Adicione estas linhas, atualizando o URI para refletir o `vaultUri` cofre da chave. Abaixo o código está a usar ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) para autenticação no cofre de chaves, que está a usar o token da identidade gerida pela aplicação para autenticar. Também está a usar recuo exponencial para retros em caso de adoção de cofre-chave estar a ser estrangulada.

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            var kvUri = "https://<your-key-vault-name>.vault.azure.net";
            SecretClientOptions options = new SecretClientOptions()
            {
                Retry =
                {
                    Delay= TimeSpan.FromSeconds(2),
                    MaxDelay = TimeSpan.FromSeconds(16),
                    MaxRetries = 5,
                    Mode = RetryMode.Exponential
                 }
            };

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential(),options);

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
```

O código anterior mostra-lhe como fazer operações com o Azure Key Vault numa máquina virtual Windows.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, elimine a máquina virtual e o cofre da chave.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Cofre chave Azure REST API](https://docs.microsoft.com/rest/api/keyvault/)
