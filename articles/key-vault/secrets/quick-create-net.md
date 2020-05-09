---
title: Quickstart - Biblioteca de clientes Azure Key Vault para .NET (v4)
description: Saiba como criar, recuperar e apagar segredos de um cofre de chaves Azure utilizando a biblioteca de clientes .NET (v4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/12/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 46ffcfbe382a974b2e8cf465f35bf9eff77dbb7e
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983181"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>Quickstart: Biblioteca de clientes Azure Key Vault para .NET (SDK v4)

Começa com a biblioteca de clientes azure Key Vault para .NET. Siga os passos abaixo para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

O cofre de chave do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em nuvem e pelos serviços. Utilize a biblioteca de clientes Key Vault para .NET para:

- Aumentar a segurança e o controlo sobre chaves e senhas.
- Crie e importe chaves de encriptação em minutos.
- Reduza a latência com a escala de nuvens e o despedimento global.
- Simplificar e automatizar tarefas para certificados TLS/SSL.
- Utilize HSMs validados de nível 2 fips 140-2.

[Documentação de](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet) | referência API Pacote de[código fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [(NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição Azure - [crie uma gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* O [Núcleo .NET 3.1 SDK ou posterior](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/overview)

Este quickstart pressupõe `dotnet`que está a funcionar , [Azure CLI,](/cli/azure/install-azure-cli?view=azure-cli-latest)e comandos Windows num terminal Windows (como [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6), ou o [Azure Cloud Shell).](https://shell.azure.com/)

## <a name="setting-up"></a>Configuração

### <a name="create-new-net-console-app"></a>Criar uma nova aplicação de consola .NET

Numa janela de consola, utilize o `dotnet new` comando para criar `key-vault-console-app`uma nova aplicação de consola .NET com o nome .

```console
dotnet new console -n key-vault-console-app
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

A partir da janela da consola, instale a biblioteca de clientes Azure Key Vault para .NET:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

Para este arranque rápido, terá de instalar também os seguintes pacotes:

```console
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e um cofre chave

Este quickstart usa um cofre chave Azure pré-criado. Pode criar um cofre chave seguindo os passos no [quickstart Azure CLI,](quick-create-cli.md) [Azure PowerShell quickstart](quick-create-powershell.md)ou [portal Azure quickstart](quick-create-portal.md). Em alternativa, pode simplesmente executar os comandos Azure CLI abaixo.

> [!Important]
> Cada cofre deve ter um nome único. Substitua <seu> de nome único com o seu cofre único com o nome do seu cofre-chave nos seguintes exemplos.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name <your-unique-keyvault-name> -ResourceGroupName myResourceGroup -Location EastUS
```

### <a name="create-a-service-principal"></a>Criar um principal de serviço

A forma mais simples de autenticar uma aplicação .NET baseada na nuvem é com uma identidade gerida; ver Utilize um Serviço de [Aplicações gerido identidade para aceder ao Cofre de Chaves Azure](../general/managed-identity.md) para obter mais detalhes. 

No entanto, por uma questão de simplicidade, este quickstart cria uma aplicação de consola .NET, que requer a utilização de um diretor de serviço e de uma política de controlo de acesso. O seu princípio de serviço requer um&lt;nome único no formato&gt;"http:// meu nome único de princípio de serviço".

Crie um princípio de serviço utilizando o comando Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principle-name&gt;" --sdk-auth
```

Esta operação devolverá uma série de pares chave/valor. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Crie um diretor de serviço utilizando o comando Azure PowerShell [New-AzADServicePrincipal:](/powershell/module/az.resources/new-azadserviceprincipal)

```azurepowershell
# Create a new service principal
$spn = New-AzADServicePrincipal -DisplayName "http://&lt;my-unique-service-principle-name&gt;"

# Get the tenant ID and subscription ID of the service principal
$tenantId = (Get-AzContext).Tenant.Id
$subscriptionId = (Get-AzContext).Subscription.Id

# Get the client ID
$clientId = $spn.ApplicationId

# Get the client Secret
$bstr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($spn.Secret)
$clientSecret = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
```

Para mais detalhes sobre o diretor de serviço com a Azure PowerShell, consulte a Create a Azure service principal com a [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Tome nota do clienteId, clienteSecret e tenantId, pois iremos usá-los nos seguintes passos.


#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Dê ao principal serviço acesso ao seu cofre chave

Crie uma política de acesso para o seu cofre chave que concede permissão ao seu diretor de serviço, passando o clienteId para o comando [de definição de teclado az keyvault.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Dê ao diretor de serviço obter, listar e definir permissões para chaves e segredos.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions list get set delete purge
```

```azurepowershell
Set-AzKeyVaultAccessPolicy -VaultName <your-unique-keyvault-name> -ServicePrincipalName <clientId-of-your-service-principal> -PermissionsToSecrets list,get,set,delete,purge
```

#### <a name="set-environmental-variables"></a>Definir variáveis ambientais

O método DefaultAzureCredential na nossa aplicação baseia-se em três variáveis ambientais: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`e `AZURE_TENANT_ID`. utilizar definir estas variáveis para os valores clienteId, clientSecret e tenantId que você observou no passo [principal do serviço Criar um](#create-a-service-principal) serviço, acima.

Você também terá que guardar o seu nome `KEY_VAULT_NAME`chave do cofre como uma variável ambiental chamada;

```console
setx AZURE_CLIENT_ID <your-clientID>

setx AZURE_CLIENT_SECRET <your-clientSecret>

setx AZURE_TENANT_ID <your-tenantId>

setx KEY_VAULT_NAME <your-key-vault-name>
````

Cada vez `setx`que liga, deve obter uma resposta de "SUCESSO: Valor especificado foi guardado".

```shell
AZURE_CLIENT_ID=<your-clientID>

AZURE_CLIENT_SECRET=<your-clientSecret>

AZURE_TENANT_ID=<your-tenantId>

KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Modelo de objeto

A biblioteca de clientes Azure Key Vault para .NET permite-lhe gerir chaves e bens relacionados, tais como certificados e segredos. As amostras de código abaixo mostrar-lhe-ão como criar um cliente, definir um segredo, recuperar um segredo e apagar um segredo.

Toda a aplicação https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-appde consola está disponível em .

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas ao topo do seu código:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Autenticar o seu cofre chave e criar um cliente chave vault depende das variáveis ambientais nas [variáveis ambientais definidas](#set-environmental-variables) acima. O nome do seu cofre chave é expandido para o\<cofre de chaves\>URI, no formato "https:// o seu nome de cofre de chaves .vault.azure.net".

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Salvar um segredo

Agora que a sua aplicação foi autenticada, pode colocar um segredo no seu cofre usando o [cliente. Método SetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) Isto requer um nome para o segredo.  

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

Pode verificar se o segredo foi definido com o comando secreto do [az keyvault:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Agora pode recuperar o valor previamente definido com o [cliente. Método GetSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

O teu segredo `secret.Value`está agora guardado como.

### <a name="delete-a-secret"></a>Eliminar um segredo

Finalmente, vamos apagar o segredo do seu cofre com o [cliente. Método DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Delete secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

Pode verificar se o segredo se foi com o comando secreto do [az keyvault:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o Azure CLI ou o Azure PowerShell para remover o cofre da chave e o grupo de recursos correspondente.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Código de exemplo

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
            string secretName = "mySecret";

            string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

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
}
```


## <a name="next-steps"></a>Passos seguintes

Neste arranque, criaste um cofre chave, guardaste um segredo e recuperaste esse segredo. Veja toda a aplicação de [consola no GitHub.](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app)

Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Implementar [a autenticação serviço-a-serviço para o Cofre chave Azure usando .NET](../general/service-to-service-authentication.md)
- Leia uma [visão geral do Cofre chave Azure](../general/overview.md)
- Consulte o guia do desenvolvedor do Cofre de [Chaves Azure](../general/developers-guide.md)
- Rever [as melhores práticas do Cofre de Chaves Azure](../general/best-practices.md)
