---
title: Tutorial-usar o Azure Key Vault com uma máquina virtual do Windows no .NET | Microsoft Docs
description: Neste tutorial, você configura um aplicativo ASP.NET Core para ler um segredo do cofre de chaves.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: fbda2f645308e30a6f408335b7a1b37095522921
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003323"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Tutorial: Usar Azure Key Vault com uma máquina virtual do Windows no .NET

Azure Key Vault ajuda a proteger segredos como chaves de API, as cadeias de conexão de banco de dados de que você precisa para acessar seus aplicativos, serviços e recursos de ti.

Neste tutorial, você aprenderá a obter um aplicativo de console para ler informações de Azure Key Vault. Para fazer isso, você usa identidades gerenciadas para recursos do Azure. 

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Crie um grupo de recursos.
> * Criar um cofre de chaves.
> * Adicione um segredo ao cofre de chaves.
> * Obter um segredo do cofre de chaves.
> * Crie uma máquina virtual do Azure.
> * Habilite uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para a máquina virtual.
> * Atribua permissões à identidade da VM.

Antes de começar, leia [Key Vault conceitos básicos](basic-concepts.md). 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Pré-requisitos

Para Windows, Mac e Linux:
  * [Git](https://git-scm.com/downloads)
  * Este tutorial requer que você execute o CLI do Azure localmente. Você deve ter o CLI do Azure versão 2.0.4 ou posterior instalado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI 2.0 do Azure](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Sobre a Identidade de Serviço Gerida

O Azure Key Vault armazena as credenciais com segurança, portanto, elas não são exibidas no seu código. No entanto, você precisa autenticar-se no Azure Key Vault para recuperar suas chaves. Para autenticar no Key Vault, você precisa de uma credencial. É um dilema de Bootstrap clássico. O Identidade de Serviço Gerenciada (MSI) resolve esse problema fornecendo uma _identidade de Bootstrap_ que simplifica o processo.

Quando você habilita o MSI para um serviço do Azure, como máquinas virtuais do Azure, serviço Azure App ou Azure Functions, o Azure cria uma [entidade de serviço](basic-concepts.md). O MSI faz isso para a instância do serviço no Azure Active Directory (Azure AD) e injeta as credenciais da entidade de serviço nessa instância. 

![MSI](media/MSI.png)

Em seguida, para obter um token de acesso, seu código chama um serviço de metadados local que está disponível no recurso do Azure. Para autenticar em um serviço de Azure Key Vault, seu código usa o token de acesso que ele obtém do ponto de extremidade MSI local. 

## <a name="create-resources-and-assign-permissions"></a>Criar recursos e atribuir permissões

Antes de iniciar a codificação, você precisa criar alguns recursos, colocar um segredo em seu cofre de chaves e atribuir permissões.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para entrar no Azure usando o CLI do Azure, digite:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). 

Este exemplo cria um grupo de recursos no local oeste dos EUA:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

O grupo de recursos criado recentemente será usado durante este tutorial.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Criar um cofre de chaves e preenchê-lo com um segredo

Crie um cofre de chaves em seu grupo de recursos fornecendo o comando [AZ keyvault Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) com as seguintes informações:

* Nome do cofre de chaves: uma cadeia de 3 a 24 caracteres que pode conter apenas números (0-9), letras (a-z, A-Z) e hifens (-)
* Nome do grupo de recursos
* Local **E.U.A. Oeste**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Neste ponto, sua conta do Azure é a única que está autorizada a executar operações nesse novo cofre de chaves.

Agora, adicione um segredo ao seu cofre de chaves usando o comando [AZ keyvault segredo Set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)


Para criar um segredo no cofre de chaves chamado **AppSecret**, insira o seguinte comando:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Esse segredo armazena o valor **MySecret**.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Crie uma máquina virtual usando um dos seguintes métodos:

* [A CLI do Azure](../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../virtual-machines/windows/quick-create-powershell.md)
* [O portal do Azure](../virtual-machines/windows/quick-create-portal.md)

### <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade à VM
Crie uma identidade atribuída pelo sistema para a máquina virtual com o comando [AZ VM Identity Assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) :

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Observe a identidade atribuída pelo sistema que é exibida no código a seguir. A saída do comando anterior seria: 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>Atribuir permissões à identidade da VM
Atribua as permissões de identidade criadas anteriormente ao cofre de chaves com o comando [AZ keyvault Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) :

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Entrar na máquina virtual

Para entrar na máquina virtual, siga as instruções em [conectar e entrar em uma máquina virtual do Azure que executa o Windows](../virtual-machines/windows/connect-logon.md).

## <a name="set-up-the-console-app"></a>Configurar o aplicativo de console

Crie um aplicativo de console e instale os pacotes necessários usando `dotnet` o comando.

### <a name="install-net-core"></a>Instalar .NET Core

Para instalar o .NET Core, vá para a página de [downloads do .net](https://www.microsoft.com/net/download) .

### <a name="create-and-run-a-sample-net-app"></a>Criar e executar um aplicativo .NET de exemplo

Abra uma linha de comandos.

Você pode imprimir "Olá, Mundo" no console executando os seguintes comandos:

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

### <a name="install-the-packages"></a>Instalar os pacotes

 Na janela do console, instale os pacotes .NET necessários para este guia de início rápido:

 ```console
dotnet add package System.IO;
dotnet add package System.Net;
dotnet add package System.Text;
dotnet add package Newtonsoft.Json;
dotnet add package Newtonsoft.Json.Linq;
```

## <a name="edit-the-console-app"></a>Editar o aplicativo de console

Abra o arquivo *Program.cs* e adicione estes pacotes:

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Edite o arquivo de classe para conter o código no seguinte processo de duas etapas:

1. Busque um token do ponto de extremidade MSI local na VM. Isso também busca um token do Azure AD.
1. Passe o token para o cofre de chaves e busque seu segredo. 

```csharp
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from the local (URI) Managed Service Identity endpoint, which in turn fetches it from Azure AD
            var token = GetToken();

            // Step 2: Fetch the secret value from your key vault
            System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
        }

        static string GetToken()
        {
            WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
            request.Headers.Add("Metadata", "true");
            WebResponse response = request.GetResponse();
            return ParseWebResponse(response, "access_token");
        }

        static string FetchSecretValueFromKeyVault(string token)
        {
            WebRequest kvRequest = WebRequest.Create("https://<YourVaultName>.vault.azure.net/secrets/<YourSecretName>?api-version=2016-10-01");
            kvRequest.Headers.Add("Authorization", "Bearer "+  token);
            WebResponse kvResponse = kvRequest.GetResponse();
            return ParseWebResponse(kvResponse, "value");
        }

        private static string ParseWebResponse(WebResponse response, string tokenName)
        {
            string token = String.Empty;
            using (Stream stream = response.GetResponseStream())
            {
                StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                String responseString = reader.ReadToEnd();

                JObject joResponse = JObject.Parse(responseString);    
                JValue ojObject = (JValue)joResponse[tokenName];             
                token = ojObject.Value.ToString();
            }
            return token;
        }
    }
```

O código anterior mostra como fazer operações com Azure Key Vault em uma máquina virtual do Windows.

## <a name="clean-up-resources"></a>Limpar recursos

Quando eles não forem mais necessários, exclua a máquina virtual e o cofre de chaves.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [API REST do Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
