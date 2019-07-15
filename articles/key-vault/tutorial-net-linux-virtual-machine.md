---
title: Tutorial – utilizar a aplicação para armazenar segredos no Azure Key Vault de consola de uma máquina virtual Linux e um ASP.NET | Documentos da Microsoft
description: Neste tutorial, saiba como configurar uma aplicação ASP.NET Core para ler um segredo do Azure Key vault.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 8c142b4c1663a7ccd462836e9aac4ee80accaae4
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67874822"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>Tutorial: Utilizar uma VM do Linux e uma aplicação .NET para armazenar segredos no Azure Key Vault

O Azure Key Vault ajuda a proteger segredos, como cadeias de ligação de base de dados que são necessárias para aceder às suas aplicações, serviços e recursos de TI e chaves de API.

Neste tutorial, vai configurar uma aplicação de consola .NET para ler as informações do Azure Key Vault através de identidades geridas para recursos do Azure. Saiba como:

> [!div class="checklist"]
> * Criar um cofre de chaves
> * Store um segredo no Key Vault
> * Criar uma máquina virtual Linux do Azure
> * Ativar uma [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) para a máquina virtual
> * Conceder as permissões necessárias para a aplicação de consola ler dados a partir do Key Vault
> * Obter um segredo do Key Vault

Antes de continuarmos, leia sobre [conceitos básicos do Cofre de chaves](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Pré-requisitos

* [Git](https://git-scm.com/downloads).
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* [CLI do Azure 2.0 ou posterior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ou o Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Compreender a identidade do serviço gerido

O Azure Key Vault pode armazenar credenciais em segurança, para que não estejam no seu código. Contudo, para as obter, tem de se autenticar no serviço. No entanto, para autenticar para o Key Vault, terá uma credencial. É um problema de bootstrap clássico. Com o Azure e Azure Active Directory (Azure AD), a identidade de serviço gerida (MSI) podem fornecer uma identidade de arranque de configuração que torna muito mais simples de dar início ao processo.

Quando ativar o MSI para um serviço do Azure como máquinas virtuais, o serviço de aplicações ou funções, o Azure cria um serviço principal para a instância do serviço no Azure Active Directory. Ele injeta as credenciais do principal de serviço para a instância do serviço.

![MSI](media/MSI.png)

Em seguida, o seu código chama um serviço de metadados locais disponível no recurso do Azure para obter um token de acesso.
O código utiliza o token de acesso que obtém do MSI_ENDPOINT local para se autenticar no serviço Azure Key Vault.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar sessão no Azure com a CLI do Azure, introduza:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um grupo de recursos com o `az group create` comando. Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos na localização E.U.A. oeste. Escolha um nome para o grupo de recursos e substitua `YourResourceGroupName` no exemplo a seguir:

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Utilize este grupo de recursos ao longo do tutorial.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Em seguida, crie um cofre de chaves no seu grupo de recursos. Forneça as seguintes informações:

* Nome do Cofre de chaves: uma cadeia de caracteres de 3 a 24 carateres que pode conter apenas números, letras e hífenes (0 a 9, a-z, A-Z, e \- ).
* Nome do grupo de recursos
* Localização: **E.U.A. Oeste**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Neste momento, apenas a sua conta do Azure está autorizada a realizar quaisquer operações neste novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Agora, adicionar um segredo. Num cenário do mundo real, poderá estar a armazenar uma cadeia de ligação de SQL ou outras informações que precisa manter de forma segura, mas disponibilizar para a sua aplicação.

Para este tutorial, escreva os seguintes comandos para criar um segredo no Cofre de chaves. O segredo é chamado **AppSecret** e o valor é **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Criar uma máquina virtual do Linux

Crie uma VM com o `az vm create` comando.

O seguinte exemplo cria uma VM com o nome **myVM** e adiciona uma conta de utilizador com o nome **azureuser**. O `--generate-ssh-keys` parâmetro nos utilizado para gerar automaticamente um SSH da chave e colocá-lo na localização predefinida da chave ( **~/.ssh**). Para utilizar um conjunto específico de chaves em vez disso, utilize a opção `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

São necessários alguns minutos para criar a VM e os recursos de suporte. A exemplo a seguir resultado mostra que a VM criar a operação foi concluída com êxito.

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

Tome nota do seu `publicIpAddress` na saída da VM. Utilizará este endereço para aceder à VM em passos posteriores.

## <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade para a VM

Crie uma identidade do sistema atribuídos à máquina virtual ao executar o seguinte comando:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

O resultado do comando deve ser:

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Anote o `systemAssignedIdentity`. Utilizá-lo no próximo passo.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Conceder a permissão de identidade VM para o Key Vault

Agora pode dar permissão de Key Vault para a identidade que criou. Execute o seguinte comando:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Iniciar sessão VM

Inicie sessão na máquina virtual com um terminal.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>Instale o .NET Core no Linux

Na sua VM do Linux:

Registe-se a chave de produto da Microsoft como fidedigno ao executar os comandos seguintes:

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

Configure o pacote de anfitrião de versão pretendida do feed com base no sistema de operativo:

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

Instale o .NET e verificar a versão:

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>Criar e executar uma aplicação .NET de exemplo

Execute os seguintes comandos. Deverá ver "Hello World" impresso no console.

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>Editar a aplicação de consola para buscar o seu segredo

Abra o ficheiro Program.cs e adicione estes pacotes:

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

É um processo de dois passos para alterar o arquivo de classe para que a aplicação para o segredo no Cofre de chaves de acesso.

1. Obter um token a partir do ponto de final MSI local na VM que por sua vez, obtém um token do Azure Active Directory.
1. Transmitir o token para o Key Vault e obter o seu segredo.

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

Agora que aprendeu como efetuar operações com o Azure Key Vault num aplicativo do .NET em execução numa máquina virtual Linux do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar, elimine o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a VM e selecione **eliminar**.

Eliminar o Cofre de chaves utilizando o `az keyvault delete` comando:

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [API de REST do Cofre de chaves do Azure](https://docs.microsoft.com/rest/api/keyvault/)
