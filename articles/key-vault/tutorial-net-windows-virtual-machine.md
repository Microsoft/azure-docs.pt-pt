---
title: Tutorial - como utilizar o Azure Key Vault com o Windows Máquina Virtual do Azure no .NET - Azure Key Vault | Documentos da Microsoft
description: Tutorial para configurar uma aplicação ASP.NET Core para ler um segredo do Key Vault
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: f12d73904b547da6531e24a899277eca7dd46660
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998766"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-windows-virtual-machine-in-net"></a>Tutorial: Como utilizar o Azure Key Vault com o Windows Máquina Virtual do Azure no .NET

O Azure Key Vault ajuda-o a proteger segredos, como chaves de APIs, cadeias de ligação de bases de dados necessárias para aceder às suas aplicações, serviços e recursos de TI.

Neste tutorial, siga os passos necessários para obter uma aplicação de consola para ler as informações do Azure Key Vault através de identidades geridas para recursos do Azure. Vai aprender a:

> [!div class="checklist"]
> * Criar um cofre de chaves.
> * Armazene um segredo no cofre de chaves.
> * Obter um segredo do cofre de chaves.
> * Crie uma Máquina Virtual do Azure.
> * Ativar uma [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) para a Máquina Virtual.
> * Conceda as permissões necessárias para a aplicação de consola para ler dados a partir do Cofre de chaves.
> * Obter segredos a partir do Key Vault

Antes de continuarmos, leia os [conceitos básicos](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Pré-requisitos
* Todas as plataformas:
  * Git ([transferir](https://git-scm.com/downloads)).
  * Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
  * [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) versão 2.0.4 ou posterior. Está disponível para Windows, Mac e Linux.

Este tutorial faz uso de identidade do serviço gerido

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>O que é a Identidade de Serviço Gerida e como funciona?

Antes de avançarmos, vamos compreender o que é o MSI. O Azure Key Vault pode armazenar credenciais em segurança, para que não estejam no seu código. Contudo, para as obter, tem de se autenticar no serviço. Para se autenticar no Key Vault, precisa de uma credencial! Trata-se de um problema clássico de arranque do sistema. Com a magia do Azure e do Azure AD, o MSI disponibiliza uma “identidade de arranque de sistema” que permite fazer tudo de forma muito mais simples.

Eis como funciona: Quando ativa o MSI para um serviço do Azure, como as Máquinas Virtuais, o Serviço de Aplicações ou as Funções do Azure, o Azure cria um [Principal de Serviço](key-vault-whatis.md#basic-concepts) para a instância do serviço no Azure Active Directory e injeta as credenciais desse Principal de Serviço na instância. 

![MSI](media/MSI.png)

Em seguida, o seu código chama um serviço de metadados locais disponível no recurso do Azure para obter um token de acesso.
O código utiliza o token de acesso que obtém do MSI_ENDPOINT local para se autenticar no serviço Azure Key Vault. 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar sessão no Azure com a CLI do Azure, introduza:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Selecione um nome do grupo de recursos e preencha o marcador de posição.
O exemplo seguinte cria um grupo de recursos na localização E.U.A Oeste:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

O grupo de recursos que acabou de criar é utilizado ao longo deste artigo.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

A seguir, vai criar um cofre de chaves no grupo de recursos criado no passo anterior. Forneça as seguintes informações:

* Nome do Cofre de chaves: O nome tem de ser uma cadeia de caracteres de 3 a 24 carateres e tem de conter apenas (0 a 9, a-z, A-Z e -).
* Nome do grupo de recursos.
* Localização: **Oeste dos E.U.A.**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Nesta altura, a sua conta do Azure é a única autorizada a realizar quaisquer operações neste novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Estamos a adicionar um segredo para ajudar a ilustrar a forma como isto funciona. Pode armazenar uma cadeia de ligação do SQL ou outras informações que precise de manter em segurança, mas mantenha disponível para a sua aplicação.

Escreva os seguintes comandos para criar um segredo no cofre de chaves designado **AppSecret**. Este segredo irá armazenar o valor **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>Criar uma Máquina Virtual
Siga as ligações para criar uma Máquina Virtual do Windows abaixo

[CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli) 

[PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)

[Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

## <a name="assign-identity-to-virtual-machine"></a>Atribuir identidade a Máquina Virtual
Neste passo, estamos criando um sistema de identidade atribuído à máquina virtual ao executar o seguinte comando na CLI do Azure

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Tenha em atenção o systemAssignedIdentity mostrado abaixo. A saída do comando acima seria 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-vm-identity-permission-to-key-vault"></a>Conceder permissões de identidade da VM no Cofre de chaves
Agora podemos dar acima criou permissão de identidade para o Key Vault ao executar o seguinte comando

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="sign-in-to-the-virtual-machine"></a>Entrar para a Máquina Virtual

Pode seguir esta [tutorial](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)

## <a name="install-net-core"></a>Instalar .NET Core

Pode instalar o .NET Core ao seguir os passos desta [artigo](https://www.microsoft.com/net/download)

## <a name="create-and-run-sample-dot-net-app"></a>Criar e executar a aplicação de rede de ponto de exemplo

Abra uma linha de comandos

Ao executar o abaixo comandos, deve ver "Hello World" impresso no console

```
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-console-app"></a>Editar aplicação de consola
Abra o ficheiro Program.cs e adicione esses pacotes
```
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```
Em seguida, altere o arquivo de classe para conter o abaixo do código. É um processo 2 passo.

1. Obter um token a partir do ponto de final MSI local na VM da qual por sua vez, obtém um token do Azure Active Directory
2. Transmitir o token para o Key Vault e obter o seu segredo 

```
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


O código acima mostra-lhe como fazer operações com o Azure Key Vault numa máquina de Virtual do Windows do Azure. 



## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [API de REST do Cofre de chaves do Azure](https://docs.microsoft.com/rest/api/keyvault/)
