---
title: Tutorial - Use uma máquina virtual Linux e uma aplicação de consola ASP.NET para armazenar segredos no Cofre de Chaves Azure  Microsoft Docs
description: Neste tutorial, aprende-se a configurar uma aplicação ASP.NET Core para ler um segredo do cofre azure key.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 8c5b3fcc1cb2ac481be0b435c48ce213c716edde
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198172"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>Tutorial: Use um Linux VM e uma aplicação .NET para armazenar segredos no Cofre de Chaves Azure

O Azure Key Vault ajuda-o a proteger segredos como Chaves API e cordas de ligação de base de dados que são necessárias para aceder às suas aplicações, serviços e recursos de TI.

Neste tutorial, configurauma aplicação de consola .NET para ler informações do Azure Key Vault utilizando identidades geridas para recursos Azure. Saiba como:

> [!div class="checklist"]
> * Criar um cofre de chaves
> * Guarde um segredo no Cofre chave
> * Crie uma máquina virtual Azure Linux
> * Ativar uma [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) para a máquina virtual
> * Conceda as permissões necessárias para que a aplicação da consola leia os dados da Key Vault
> * Recuperar um segredo do Cofre chave

Antes de irmos mais longe, leia sobre [conceitos básicos](basic-concepts.md)de cofre chave.

## <a name="prerequisites"></a>Pré-requisitos

* [Git](https://git-scm.com/downloads).
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* [Azure CLI 2.0 ou mais tarde](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ou Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Compreender identidade de serviço gerido

O Azure Key Vault pode armazenar credenciais em segurança, para que não estejam no seu código. Contudo, para as obter, tem de se autenticar no serviço. No entanto, para autenticar o Cofre chave, precisa de uma credencial. É um problema clássico de botas. Com o Azure e o Azure Ative Directory (Azure AD), a Managed Service Identity (MSI) pode fornecer uma identidade de bootstrap que torna muito mais simples começar as coisas.

Quando ativa o MSI para um serviço Azure como Máquinas Virtuais, Serviço de Aplicações ou Funções, o Azure cria um diretor de serviço para a instância do serviço no Diretório Ativo Azure. Injeta as credenciais para o diretor de serviço na instância do serviço.

![MSI](media/MSI.png)

Em seguida, o seu código chama um serviço de metadados local disponível no recurso Azure para obter um sinal de acesso.
O código utiliza o token de acesso que obtém do MSI_ENDPOINT local para se autenticar no serviço Azure Key Vault.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar sessão no Azure utilizando o Azure CLI, introduza:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Crie um grupo de recursos utilizando o comando `az group create`. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos na localização dos EUA Ocidentais. Escolha um nome para o seu grupo de recursos e substitua `YourResourceGroupName` no seguinte exemplo:

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Você usa este grupo de recursos em todo o tutorial.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Em seguida, crie um cofre chave no seu grupo de recursos. Forneça as seguintes informações:

* Nome do cofre chave: uma cadeia de 3 a 24 caracteres que pode conter apenas números, letras e hífenes (0-9, a-z, A-Z e \-).
* Nome do grupo de recursos
* Localização: **Oeste DOS EUA**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Neste momento, apenas a sua conta Azure está autorizada a realizar quaisquer operações neste novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Agora, adicione um segredo. Num cenário real, poderá estar a armazenar uma cadeia de ligação SQL ou qualquer outra informação que precise de manter de forma segura, mas disponibilize-se à sua aplicação.

Para este tutorial, escreva os seguintes comandos para criar um segredo no cofre da chave. O segredo chama-se **AppSecret** e o seu valor é **MySecret.**

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Criar uma máquina virtual do Linux

Crie um VM com o comando `az vm create`.

O seguinte exemplo cria uma VM com o nome **myVM** e adiciona uma conta de utilizador com o nome **azureuser**. O parâmetro `--generate-ssh-keys` que usámos para gerar automaticamente uma chave SSH e colocá-la na localização da chave predefinida **(~/.ssh**). Para utilizar um conjunto específico de chaves em vez disso, utilize a opção `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

São necessários alguns minutos para criar a VM e os recursos de suporte. A saída de exemplo que se segue mostra que a operação de criação de VM foi bem sucedida.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

Tome nota da sua `publicIpAddress` na saída do seu VM. Usará este endereço para aceder ao VM em etapas posteriores.

## <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade ao VM

Criar uma identidade atribuída ao sistema para a máquina virtual executando o seguinte comando:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

A saída do comando deve ser semelhante a:

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Tome nota do `systemAssignedIdentity`. Usa-o no próximo passo.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Dê a autorização de identidade vM para o Cofre chave

Agora pode dar permissão ao Cofre chave para a identidade que criou. Execute o seguinte comando:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Iniciar sessão no VM

Inicie sessão na máquina virtual utilizando um terminal.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>Instalar .NET Core em Linux

No seu VM Linux:

Registe a chave do produto da Microsoft como confiável, executando os seguintes comandos:

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

Configurar o feed de pacote de pacote de versão desejada com base no sistema operativo:

```console
   # Ubuntu 17.10
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-artful-prod artful main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 17.04
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-zesty-prod zesty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 16.04 / Linux Mint 18
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 14.04 / Linux Mint 17
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
```

Instale .NET e verifique a versão:

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>Criar e executar uma aplicação de amostra .NET

Executar os seguintes comandos. Devias ver "Hello World" impresso na consola.

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>Editar a aplicação de consola para buscar o seu segredo

Abra Program.cs arquivar e adicione estes pacotes:

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

É um processo em duas etapas para alterar o ficheiro de classe para permitir que a app aceda ao segredo no cofre da chave.

1. Pegue um símbolo do ponto final local do MSI no VM que, por sua vez, vai buscar um símbolo do Azure Ative Directory.
1. Passe o símbolo para o Cofre chave e pegue o seu segredo.

   Editar o ficheiro de classe para conter o seguinte código:

   ```csharp
    class Program
       {
           static void Main(string[] args)
           {
               // Step 1: Get a token from local (URI) Managed Service Identity endpoint which in turn fetches it from Azure Active Directory
               var token = GetToken();

               // Step 2: Fetch the secret value from Key Vault
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
               WebRequest kvRequest = WebRequest.Create("https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01");
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

Agora aprendeu a realizar operações com o Azure Key Vault numa aplicação .NET a funcionar numa máquina virtual Azure Linux.

## <a name="clean-up-resources"></a>Limpar recursos

Elimine o grupo de recursos, a máquina virtual e todos os recursos relacionados quando já não precisar deles. Para isso, selecione o grupo de recursos para o VM e selecione **Eliminar**.

Elimine o cofre da chave utilizando o comando `az keyvault delete`:

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Cofre de Chaves Azure REST API](https://docs.microsoft.com/rest/api/keyvault/)
