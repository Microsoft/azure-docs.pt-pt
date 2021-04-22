---
title: Tutorial - Use a Azure Key Vault com uma máquina virtual em .NET | Microsoft Docs
description: Neste tutorial, você configura uma máquina virtual uma aplicação ASP.NET para ler um segredo do seu cofre chave.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: mbaldwin
ms.custom: mvc, devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: ba299ff52ccdf811d739efdb1d379889444a02b8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868398"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-net"></a>Tutorial: Use a Azure Key Vault com uma máquina virtual em .NET

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
  * [Azure CLI](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="create-resources-and-assign-permissions"></a>Criar recursos e atribuir permissões

Antes de começar a codificar, precisa criar alguns recursos, colocar um segredo no cofre e atribuir permissões.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar seduca em Azure usando o seguinte comando:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az login
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Connect-AzAccount
```
---

## <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e cofre chave

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Povoe o seu cofre com um segredo

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Crie uma máquina virtual Windows ou Linux utilizando um dos seguintes métodos:

| Windows | Linux |
|--|--|
| [CLI do Azure](../../virtual-machines/windows/quick-create-cli.md) | [CLI do Azure](../../virtual-machines/linux/quick-create-cli.md) |  
| [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) | [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) |
| [Portal do Azure](../../virtual-machines/windows/quick-create-portal.md) | [Portal do Azure](../../virtual-machines/linux/quick-create-portal.md) |

## <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade ao VM
Criar uma identidade atribuída ao sistema para a máquina virtual com o seguinte exemplo:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
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

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$vm = Get-AzVM -Name <NameOfYourVirtualMachine>
Update-AzVM -ResourceGroupName <YourResourceGroupName> -VM $vm -IdentityType SystemAssigned
```

Note o DirectorId que é apresentado no seguinte código. A saída do comando anterior seria: 


```output
PrincipalId          TenantId             Type             UserAssignedIdentities
-----------          --------             ----             ----------------------
xxxxxxxx-xx-xxxxxx   xxxxxxxx-xxxx-xxxx   SystemAssigned
```
---

## <a name="assign-permissions-to-the-vm-identity"></a>Atribuir permissões à identidade VM
Atribua as permissões de identidade previamente criadas para o seu cofre de chaves com o comando [de definição de chave-chave az:](/cli/azure/keyvault#az_keyvault_set_policy)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az keyvault set-policy --name '<your-unique-key-vault-name>' --object-id <VMSystemAssignedIdentity> --secret-permissions  get list set delete
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Set-AzKeyVaultAccessPolicy -ResourceGroupName <YourResourceGroupName> -VaultName '<your-unique-key-vault-name>' -ObjectId '<VMSystemAssignedIdentity>' -PermissionsToSecrets  get,list,set,delete
```
---

## <a name="sign-in-to-the-virtual-machine"></a>Inscreva-se na máquina virtual

Para iniciar seduca na máquina virtual, siga as instruções em [Connect e inscreva-se numa máquina virtual Azure Windows](../../virtual-machines/windows/connect-logon.md) ou Ligue e [inscreva-se numa máquina virtual Azure Linux](../../virtual-machines/linux/login-using-aad.md).

## <a name="set-up-the-console-app"></a>Configurar a aplicação de consola

Crie uma aplicação para consolas e instale as embalagens necessárias utilizando o `dotnet` comando.

### <a name="install-net-core"></a>Instalar .NET Core

Para instalar .NET Core, aceda à página [de downloads .NET.](https://dotnet.microsoft.com/download)

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

Abra o ficheiro *.cs Programa* e adicione estes pacotes:

```csharp
using System;
using Azure.Core;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Adicione estas linhas, atualizando o URI para refletir o `vaultUri` cofre da chave. Abaixo o código está a usar  ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential) para autenticação no cofre de chaves, que está a usar o token da identidade gerida pela aplicação para autenticar. Também está a usar recuo exponencial para retros em caso de adoção de cofre-chave estar a ser estrangulada.

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";
            string keyVaultName = "<your-key-vault-name>";
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

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não forem necessários, elimine a máquina virtual e o cofre da chave.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Cofre chave Azure REST API](/rest/api/keyvault/)
