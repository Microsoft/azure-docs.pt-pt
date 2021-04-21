---
title: Quickstart - Azure Key Vault certifica biblioteca cliente para .NET (versão 4)
description: Saiba como criar, recuperar e apagar certificados de um cofre de chaves Azure utilizando a biblioteca de clientes .NET (versão 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 9188079a93c9c2688a310413401e90014b278bf8
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813421"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-net-sdk-v4"></a>Quickstart: Azure Key Vault certificate client library for.NET (SDK v4)

Começa com a biblioteca de clientes do Certificado Azure Key Vault para .NET. [Azure Key Vault](../general/overview.md) é um serviço de nuvem que fornece uma loja segura para certificados. Pode armazenar chaves, palavras-passe, certificados e outros segredos em segurança. Os cofres de chaves do Azure podem ser criados e geridos através do portal do Azure. Neste arranque rápido, aprende-se a criar, recuperar e apagar certificados de um cofre de chaves Azure utilizando a biblioteca de clientes .NET

Recursos da biblioteca do cliente Key Vault:

[Documentação de](/dotnet/api/azure.security.keyvault.certificates)  |  referência da API [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)

Para obter mais informações sobre o Cofre-Chave e certificados, consulte:
- [Visão geral do cofre de chaves](../general/overview.md)
- [Visão geral dos certificados.](about-certificates.md)

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

Crie uma política de acesso para o cofre-chave que concede permissões de certificados à sua conta de utilizador

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --certificate-permissions delete get list create purge
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

A partir da concha de comando, instale a biblioteca do cliente certificado Azure Key Vault para .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Certificates
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
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS ou Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Modelo de objeto

A biblioteca de clientes do Certificado Azure Key Vault para .NET permite-lhe gerir certificados. A secção [de exemplos de Código](#code-examples) mostra como criar um cliente, definir um certificado, recuperar um certificado e apagar um certificado.

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas ao topo do *Programa.cs:*

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Certificates;
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Neste arranque rápido, o utilizador com sessão é utilizado para autenticar o cofre de chaves, que é o método preferido para o desenvolvimento local. Para aplicações implantadas no Azure, a identidade gerida deve ser atribuída ao Serviço de Aplicações ou Máquina Virtual, para obter mais informações, consulte [a Visão Geral da Identidade Gerida](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Por exemplo, o nome do seu cofre-chave é expandido para o cofre uri chave, no formato "https:// \<your-key-vault-name\> .vault.azure.net". Este exemplo está a utilizar a classe ['DefaultAzureCredential)'](/dotnet/api/azure.identity.defaultazurecredential) da [Azure Identity Library,](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)que permite utilizar o mesmo código em diferentes ambientes com diferentes opções para fornecer identidade. Para obter mais informações sobre a autenticação no cofre da chave, consulte [o Guia do Programador.](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

```csharp
string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new CertificateClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-certificate"></a>Guardar um certificado

Neste exemplo, para simplificar, pode utilizar o certificado auto-assinado com a política de emissão por defeito. Para esta tarefa, utilize o método [StartCreateCertificateAsync.](/dotnet/api/azure.security.keyvault.certificates.certificateclient.startcreatecertificateasync) Os parâmetros do método aceitam um nome de certificado e a política de [certificados.](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.certificates.certificatepolicy)

```csharp
var operation = await client.StartCreateCertificateAsync("myCertificate", CertificatePolicy.Default);
var certificate = await operation.WaitForCompletionAsync();
```

> [!NOTE]
> Se existir o nome do certificado, o código acima criará uma nova versão desse certificado.

### <a name="retrieve-a-certificate"></a>Recuperar um certificado

Agora pode recuperar o certificado previamente criado com o método [GetCertificateAsync.](/dotnet/api/azure.security.keyvault.certificates.certificateclient.getcertificateasync)

```csharp
var certificate = await client.GetCertificateAsync("myCertificate");
```

### <a name="delete-a-certificate"></a>Apagar um certificado

Finalmente, vamos apagar e limpar o certificado do seu cofre-chave com os métodos [StartDeleteCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.startdeletecertificateasync) e [PurpurDeletedCertificateAsync.](/dotnet/api/azure.security.keyvault.certificates.certificateclient.purgedeletedcertificateasync)

```csharp
var operation = await client.StartDeleteCertificateAsync("myCertificate");

// You only need to wait for completion if you want to purge or recover the certificate.
await operation.WaitForCompletionAsync();

var certificate = operation.Value;
await client.PurgeDeletedCertificateAsync("myCertificate");
```

## <a name="sample-code"></a>Código de exemplo

Modifique a aplicação de consola .NET Core para interagir com o Cofre de Chaves, completando os seguintes passos:

- Substitua o código no *programa.cs* pelo seguinte código:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Certificates;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string certificateName = "myCertificate";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new CertificateClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a certificate in {keyVaultName} called '{certificateName}' ...");
                CertificateOperation operation = await client.StartCreateCertificateAsync(certificateName, CertificatePolicy.Default);
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
    
                Console.WriteLine($"Retrieving your certificate from {keyVaultName}.");
                var certificate = await client.GetCertificateAsync(certificateName);
                Console.WriteLine($"Your certificate version is '{certificate.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your certificate from {keyVaultName} ...");
                DeleteCertificateOperation deleteOperation = await client.StartDeleteCertificateAsync(certificateName);
                // You only need to wait for completion if you want to purge or recover the certificate.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine(" done.");

                Console.Write($"Purging your certificate from {keyVaultName} ...");
                await client.PurgeDeletedCertificateAsync(certificateName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testar e verificar

Execute o seguinte comando para construir o projeto

```dotnetcli
dotnet build
```

É apresentada uma variação da seguinte saída:

```console
Creating a certificate in mykeyvault called 'myCertificate' ... done.
Retrieving your certificate from mykeyvault.
Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your certificate from mykeyvault ... done
Purging your certificate from mykeyvault ... done
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou um cofre chave, guardou um certificado e recuperou o certificado. 

Para saber mais sobre o Key Vault e como integrá-lo com as suas apps, consulte os seguintes artigos:

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Leia uma [visão geral dos certificados](about-certificates.md)
- Veja um [cofre de chave de acesso a partir do tutorial de aplicações do serviço de aplicações de aplicações de aplicações](../general/tutorial-net-create-vault-azure-web-app.md)
- Veja um [cofre de chave de acesso a partir de tutorial de máquina virtual](../general/tutorial-net-virtual-machine.md)
- Consulte o [guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Reveja a visão geral da [segurança do Cofre-Chave](../general/security-features.md)
