---
title: Tutorial - Use o Cofre de Chaves Azure com uma máquina virtual Windows em .NET  Microsoft Docs
description: Neste tutorial, configura uma aplicação ASP.NET central para ler um segredo do seu cofre chave.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 5082ed06b4ce5baf3869fc035654be3c7a45f29f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845303"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Tutorial: Use o Cofre de Chaves Azure com uma máquina virtual Windows em .NET

O Azure Key Vault ajuda-o a proteger segredos como chaves API, as cordas de ligação à base de dados que precisa para aceder às suas aplicações, serviços e recursos de TI.

Neste tutorial, aprende-se a obter uma aplicação de consola para ler informações do Azure Key Vault. Para isso, utiliza-se identidades geridas para os recursos do Azure. 

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Crie um grupo de recursos.
> * Criar um cofre de chaves.
> * Adicione um segredo ao cofre da chave.
> * Obter um segredo do cofre de chaves.
> * Crie uma máquina virtual Azure.
> * Ativar uma [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) para a Máquina Virtual.
> * Atribuir permissões à identidade VM.

Antes de começar, leia [os conceitos básicos do Key Vault.](basic-concepts.md) 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Pré-requisitos

Para Windows, Mac e Linux:
  * [Git](https://git-scm.com/downloads)
  * Este tutorial requer que você execute o CLI do Azure localmente. Tem de ter a versão 2.0.4 do Azure CLI ou posteriormente instalada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI 2.0 do Azure](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Sobre a Identidade de Serviço Gerida

O Azure Key Vault armazena credenciais de forma segura, por isso não são exibidos no seu código. No entanto, precisa autenticar o Cofre de Chaves Azure para recuperar as suas chaves. Para autenticar o Cofre chave, precisa de uma credencial. É um dilema clássico de botas. A Identidade de Serviço Gerida (MSI) resolve este problema fornecendo uma identidade de _bootstrap_ que simplifica o processo.

Quando ativa o MSI para um serviço Azure, como máquinas virtuais Azure, Serviço de Aplicações Azure ou Funções Azure, o Azure cria um diretor de [serviço.](basic-concepts.md) A MSI faz isto por exemplo do serviço no Azure Ative Directory (Azure AD) e injeta as credenciais principais do serviço nesse caso. 

![MSI](media/MSI.png)

Em seguida, para obter um sinal de acesso, o seu código chama um serviço de metadados local que está disponível no recurso Azure. Para autenticar um serviço Azure Key Vault, o seu código utiliza o sinal de acesso que obtém do ponto final local da MSI. 

## <a name="create-resources-and-assign-permissions"></a>Criar recursos e atribuir permissões

Antes de começar a codificar, precisa de criar alguns recursos, colocar um segredo no seu cofre chave e atribuir permissões.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar sessão no Azure utilizando o Azure CLI, introduza:

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

O seu grupo de recursos recém-criado será utilizado ao longo deste tutorial.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Crie um cofre chave e povoei-o com um segredo

Crie um cofre chave no seu grupo de recursos, fornecendo o [cofre az criar](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) comando com as seguintes informações:

* Nome do cofre chave: uma cadeia de 3 a 24 caracteres que pode conter apenas números (0-9), letras (a-z, A-Z) e hífenes (-)
* Nome do grupo de recursos
* Localização: **Oeste DOS EUA**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Neste ponto, a sua conta Azure é a única que está autorizada a realizar operações neste novo cofre chave.

Agora adicione um segredo ao seu cofre usando o comando secreto do [cofre az keyvault](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)


Para criar um segredo no cofre de chaves chamado **AppSecret,** insira o seguinte comando:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Este segredo armazena o valor **MySecret.**

### <a name="create-a-virtual-machine"></a>Crie uma máquina virtual
Criar uma máquina virtual utilizando um dos seguintes métodos:

* [A CLI do Azure](../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../virtual-machines/windows/quick-create-powershell.md)
* [O portal do Azure](../virtual-machines/windows/quick-create-portal.md)

### <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade ao VM
Crie uma identidade atribuída ao sistema para a máquina virtual com o comando de atribuição de [identidade az vm:](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign)

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Note a identidade atribuída ao sistema que está exposta no seguinte código. A saída do comando anterior seria: 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>Atribuir permissões à identidade VM
Atribua as permissões de identidade previamente criadas para o seu cofre chave com o comando [de definição de teclado az keyvault:](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Inscreva-se na máquina virtual

Para iniciar sessão na máquina virtual, siga as instruções em [Connect e inste uma máquina virtual Azure que executa](../virtual-machines/windows/connect-logon.md)o Windows .

## <a name="set-up-the-console-app"></a>Configurar a aplicação de consola

Crie uma aplicação de consola e instale as embalagens necessárias utilizando o comando `dotnet`.

### <a name="install-net-core"></a>Instalar .NET Core

Para instalar .NET Core, vá à página de [downloads .NET.](https://www.microsoft.com/net/download)

### <a name="create-and-run-a-sample-net-app"></a>Criar e executar uma aplicação de amostra .NET

Abra uma linha de comandos.

Pode imprimir "Hello World" para a consola executando os seguintes comandos:

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

### <a name="install-the-packages"></a>Instale as embalagens

 A partir da janela da consola, instale as embalagens .NET necessárias para este arranque rápido:

 ```console
dotnet add package System.IO;
dotnet add package System.Net;
dotnet add package System.Text;
dotnet add package Newtonsoft.Json;
dotnet add package Newtonsoft.Json.Linq;
```

## <a name="edit-the-console-app"></a>Editar a aplicação de consola

Abra o ficheiro *Program.cs* e adicione estes pacotes:

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Editar o ficheiro de classe para conter o código no seguinte processo de três etapas:

1. Pegue um símbolo do ponto final local da MSI no VM. Fazê-lo também requer um símbolo da Azure AD.
2. Passe o símbolo para o seu cofre e depois pegue o seu segredo. 
3. Adicione o nome do cofre e o nome secreto ao pedido.

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
            //Step 3: Add the vault name and secret name to the request.
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

O código anterior mostra-lhe como fazer operações com o Azure Key Vault numa máquina virtual windows.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, apague a máquina virtual e o cofre da chave.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Cofre de Chaves Azure REST API](https://docs.microsoft.com/rest/api/keyvault/)
