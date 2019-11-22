---
title: Início rápido-biblioteca de cliente Azure Key Vault para .NET (v4)
description: Saiba como criar, recuperar e excluir segredos de um cofre de chaves do Azure usando a biblioteca de cliente .NET (v4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 05/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: 61e176314c655ef6380a196043fb3159d003cb6e
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273946"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>Início rápido: biblioteca de cliente do Azure Key Vault para .NET (SDK v4)

Introdução à biblioteca de cliente do Azure Key Vault para .NET. Siga as etapas abaixo para instalar o pacote e experimentar o código de exemplo para tarefas básicas.

O cofre de chave do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em nuvem e pelos serviços. Use a biblioteca de cliente do Key Vault para .NET para:

- Aumente a segurança e o controle sobre chaves e senhas.
- Crie e importe chaves de criptografia em minutos.
- Reduza a latência com escala de nuvem e redundância global.
- Simplifique e automatize tarefas para certificados SSL/TLS.
- Use os HSMs validados pelo FIPS 140-2 nível 2.

[Documentação de referência de API](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet) | o [código-fonte](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [pacote (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/) da biblioteca

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* O [SDK do .NET Core 2,1 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/overview)

Este início rápido pressupõe que você esteja executando os comandos `dotnet`, [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)e Windows em um terminal do Windows (como [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)ou o [Azure cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Configurando

### <a name="create-new-net-console-app"></a>Criar novo aplicativo de console .NET

Em uma janela de console, use o comando `dotnet new` para criar um novo aplicativo de console .NET com o nome `akv-dotnet`.

```console
dotnet new console -n key-vault-console-app
```

Altere o diretório para a pasta de aplicativos recém-criada. Você pode criar o aplicativo com:

```console
dotnet build
```

A saída da compilação não deve conter nenhum aviso ou erro.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Instalar o pacote

Na janela do console, instale a biblioteca de cliente do Azure Key Vault para .NET:

```console
dotnet add package Azure.Security.KeyVault.Secrets --version 4.0.0
```

Para este guia de início rápido, você precisará instalar os seguintes pacotes também:

```console
dotnet add package Azure.Identity --version 1.0.0
```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e um cofre de chaves

Este guia de início rápido usa um cofre de chaves do Azure criado previamente. Você pode criar um cofre de chaves seguindo as etapas na guia de início rápido [CLI do Azure](quick-create-cli.md), guia de início rápido [Azure PowerShell](quick-create-powershell.md)ou [portal do Azure início rápido](quick-create-portal.md). Como alternativa, você pode simplesmente executar os comandos de CLI do Azure abaixo.

> [!Important]
> Cada cofre de chaves deve ter um nome exclusivo. Substitua < seu-Unique-keyvault-Name > pelo nome do seu cofre de chaves nos exemplos a seguir.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Criar um principal de serviço

A maneira mais simples de autenticar um aplicativo .NET baseado em nuvem é com uma identidade gerenciada; consulte [usar uma identidade gerenciada do serviço de aplicativo para acessar Azure Key Vault](managed-identity.md) para obter detalhes. Para simplificar, no entanto, este guia de início rápido cria um aplicativo de console .NET. A autenticação de um aplicativo de área de trabalho com o Azure requer o uso de uma entidade de serviço e uma política de controle de acesso.

Crie uma entidade de serviço usando o comando CLI do Azure [AZ ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Essa operação retornará uma série de pares de chave/valor. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Anote o clientId, clientSecret e tenantid, pois os usaremos nas etapas a seguir.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Fornecer acesso à entidade de serviço ao cofre de chaves

Crie uma política de acesso para o cofre de chaves que concede permissão para sua entidade de serviço passando o clientId para o comando [AZ keyvault Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Conceda à entidade de serviço obter, listar e definir permissões para chaves e segredos.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Definir variáveis de ambiente

O método DefaultAzureCredential em nosso aplicativo depende de três variáveis ambientais: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`e `AZURE_TENANT_ID`. Use definir essas variáveis para os valores clientId, clientSecret e tenantid que você anotou na etapa [criar uma entidade de serviço](#create-a-service-principal) , acima.

Você também precisará salvar o nome do cofre de chaves como uma variável de ambiente chamada `KEY_VAULT_NAME`;

```console
setx AZURE_CLIENT_ID <your-clientID>

setx AZURE_CLIENT_SECRET <your-clientSecret>

setx AZURE_TENANT_ID <your-tenantId>

setx KEY_VAULT_NAME <your-key-vault-name>
````

Cada vez que você chamar `setx`, deverá obter uma resposta de "êxito: o valor especificado foi salvo".

## <a name="object-model"></a>Modelo de objeto

A biblioteca de cliente do Azure Key Vault para .NET permite que você gerencie chaves e ativos relacionados, como certificados e segredos. Os exemplos de código abaixo mostrarão como criar um cliente, definir um segredo, recuperar um segredo e excluir um segredo.

Todo o aplicativo de console está disponível em https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas à parte superior do seu código:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

A autenticação no cofre de chaves e a criação de um cliente de cofre de chaves dependem das variáveis ambientais na etapa [definir variáveis de ambiente](#set-environmental-variables) acima. O nome do cofre de chaves é expandido para o URI do Key Vault, no formato "https://\<your-key-Vault-Name\>. vault.azure.net".

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Salvar um segredo

Agora que seu aplicativo está autenticado, você pode colocar um segredo em seu cofre de chaves usando o [cliente. Método setsecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) requer um nome para o segredo – estamos usando "MySecret" neste exemplo.  

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

Você pode verificar se o segredo foi definido com o comando [AZ keyvault segredo show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Agora você pode recuperar o valor definido anteriormente com o [cliente. Método getsecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

Seu segredo agora está salvo como `secret.Value`.

### <a name="delete-a-secret"></a>Eliminar um segredo

Por fim, vamos excluir o segredo do seu cofre de chaves com o [cliente. Método DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Delete secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

Você pode verificar se o segredo desapareceu com o comando [AZ keyvault segredo show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá usar o CLI do Azure ou Azure PowerShell para remover o cofre de chaves e o grupo de recursos correspondente.

```azurecli
az group delete -g "myResourceGroup" -l "EastUS" 
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

Neste início rápido, você criou um cofre de chaves, armazenou um segredo e recuperou esse segredo. Consulte [todo o aplicativo de console no GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app).

Para saber mais sobre Key Vault e como integrá-lo a seus aplicativos, continue nos artigos abaixo.

- Implementar [a autenticação de serviço a serviço para Azure Key Vault usando o .net](service-to-service-authentication.md)
- Leia uma [visão geral do Azure Key Vault](key-vault-overview.md)
- Consulte o [Guia do desenvolvedor do Azure Key Vault](key-vault-developers-guide.md)
- Saiba mais sobre [chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- Examinar [Azure Key Vault práticas recomendadas](key-vault-best-practices.md)
