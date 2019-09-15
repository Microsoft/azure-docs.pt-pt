---
title: Tutorial – usar uma máquina virtual Linux e um aplicativo de console ASP.NET para armazenar segredos no Azure Key Vault | Microsoft Docs
description: Neste tutorial, você aprenderá a configurar um aplicativo ASP.NET Core para ler um segredo do Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 65c59ba299490ee2bbef849b6f7354abc05ad885
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003350"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>Tutorial: Usar uma VM do Linux e um aplicativo .NET para armazenar segredos no Azure Key Vault

Azure Key Vault ajuda a proteger segredos como chaves de API e cadeias de conexão de banco de dados que são necessárias para acessar seus aplicativos, serviços e recursos de ti.

Neste tutorial, você configura um aplicativo de console .NET para ler informações de Azure Key Vault usando identidades gerenciadas para recursos do Azure. Saiba como:

> [!div class="checklist"]
> * Criar um cofre de chaves
> * Armazenar um segredo no Key Vault
> * Criar uma máquina virtual Linux do Azure
> * Habilitar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para a máquina virtual
> * Conceder as permissões necessárias para o aplicativo de console ler dados de Key Vault
> * Recuperar um segredo de Key Vault

Antes de continuarmos, leia sobre os [conceitos básicos do Key Vault](basic-concepts.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Git](https://git-scm.com/downloads).
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* [CLI do Azure 2,0 ou posterior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ou Azure cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Entender Identidade de Serviço Gerenciada

O Azure Key Vault pode armazenar credenciais em segurança, para que não estejam no seu código. Contudo, para as obter, tem de se autenticar no serviço. No entanto, para autenticar no Key Vault, você precisa de uma credencial. É um problema de inicialização clássico. Com o Azure e o Azure Active Directory (Azure AD), o Identidade de Serviço Gerenciada (MSI) pode fornecer uma identidade de bootstrap que torna muito mais simples começar as coisas.

Quando você habilita o MSI para um serviço do Azure como máquinas virtuais, serviço de aplicativo ou funções, o Azure cria uma entidade de serviço para a instância do serviço no Azure Active Directory. Ele injeta as credenciais da entidade de serviço na instância do serviço.

![MSI](media/MSI.png)

Em seguida, seu código chama um serviço de metadados local disponível no recurso do Azure para obter um token de acesso.
O código utiliza o token de acesso que obtém do MSI_ENDPOINT local para se autenticar no serviço Azure Key Vault.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para entrar no Azure usando o CLI do Azure, digite:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos usando o `az group create` comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos no local oeste dos EUA. Escolha um nome para seu grupo de recursos e `YourResourceGroupName` substitua no exemplo a seguir:

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Você usa esse grupo de recursos em todo o tutorial.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Em seguida, crie um cofre de chaves em seu grupo de recursos. Forneça as seguintes informações:

* Nome do cofre de chaves: uma cadeia de 3 a 24 caracteres que pode conter somente números, letras e hifens (0-9, a-z, A-Z \- e).
* Nome do grupo de recursos
* Local **E.U.A. Oeste**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Neste ponto, somente sua conta do Azure está autorizada a executar qualquer operação nesse novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Agora, você adiciona um segredo. Em um cenário do mundo real, você pode estar armazenando uma cadeia de conexão SQL ou qualquer outra informação que precise manter com segurança, mas disponibilizar para seu aplicativo.

Para este tutorial, digite os seguintes comandos para criar um segredo no cofre de chaves. O segredo é chamado de **AppSecret** e seu valor é **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Criar uma máquina virtual do Linux

Crie uma VM com o `az vm create` comando.

O seguinte exemplo cria uma VM com o nome **myVM** e adiciona uma conta de utilizador com o nome **azureuser**. O `--generate-ssh-keys` parâmetro dos EUA usado para gerar automaticamente uma chave SSH e colocá-la no local de chave padrão ( **~/.ssh**). Para utilizar um conjunto específico de chaves em vez disso, utilize a opção `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

São necessários alguns minutos para criar a VM e os recursos de suporte. A saída de exemplo a seguir mostra que a operação de criação de VM foi bem-sucedida.

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

Anote seu `publicIpAddress` na saída de sua VM. Você usará esse endereço para acessar a VM em etapas posteriores.

## <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade à VM

Crie uma identidade atribuída pelo sistema para a máquina virtual executando o seguinte comando:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

A saída do comando deve ser:

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Anote o `systemAssignedIdentity`. Você o usará na próxima etapa.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Conceder permissão de identidade da VM para Key Vault

Agora você pode conceder Key Vault permissão para a identidade que você criou. Execute o seguinte comando:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Faça logon na VM

Faça logon na máquina virtual usando um terminal.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>Instalar o .NET Core no Linux

Em sua VM Linux:

Registre a chave do produto da Microsoft como confiável executando os seguintes comandos:

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

Configure o feed de pacote do host da versão desejada com base no sistema operacional:

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

Instale o .NET e verifique a versão:

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>Criar e executar um aplicativo .NET de exemplo

Execute os comandos a seguir. Você deve ver "Olá, Mundo" impresso no console.

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>Editar o aplicativo de console para buscar seu segredo

Abra o arquivo Program.cs e adicione estes pacotes:

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

Esse é um processo de duas etapas para alterar o arquivo de classe para permitir que o aplicativo acesse o segredo no cofre de chaves.

1. Busque um token do ponto de extremidade MSI local na VM que, por sua vez, busca um token de Azure Active Directory.
1. Passe o token para Key Vault e busque seu segredo.

   Edite o arquivo de classe para conter o seguinte código:

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

Agora você aprendeu como executar operações com Azure Key Vault em um aplicativo .NET em execução em uma máquina virtual Linux do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Exclua o grupo de recursos, a máquina virtual e todos os recursos relacionados quando você não precisar mais deles. Para fazer isso, selecione o grupo de recursos para a VM e selecione **excluir**.

Exclua o cofre de chaves usando `az keyvault delete` o comando:

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [API REST do Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
