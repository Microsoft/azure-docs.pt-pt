---
title: Tutorial – utilizar o Azure Key Vault com uma máquina virtual do Windows em Python | Documentos da Microsoft
description: Neste tutorial, irá configurar uma aplicação ASP.NET core para ler um segredo a partir do seu Cofre de chaves.
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 133de5410d5e506c9528e2dba90dd4c00d8fcc2d
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438215"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>Tutorial: Utilizar o Azure Key Vault com uma máquina virtual do Windows em Python

O Azure Key Vault ajuda a proteger segredos, como chaves de API, as cadeias de ligação da base de dados, precisa acessar seus aplicativos, serviços e recursos de TI.

Neste tutorial, saiba como obter uma aplicação de consola para ler as informações do Azure Key Vault. Para fazer isso, o que utiliza identidades geridas na recursos do Azure. 

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Criar um cofre de chaves.
> * Adicione um segredo ao Cofre de chaves.
> * Obter um segredo do cofre de chaves.
> * Crie uma máquina virtual do Azure.
> * Ative uma identidade gerida.
> * Atribua permissões para a identidade da VM.

Antes de começar, leia [conceitos básicos do Key Vault](key-vault-whatis.md#basic-concepts). 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Pré-requisitos

Para Windows, Mac e Linux:
  * [Git](https://git-scm.com/downloads)
  * Este tutorial requer que execute a CLI do Azure localmente. Tem de ter a CLI do Azure versão 2.0.4 ou posterior instalado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI 2.0 do Azure](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Sobre a Identidade de Serviço Gerida

O Azure Key Vault armazena as credenciais em segurança, pelo que não são apresentados no seu código. No entanto, tem de autenticar para o Azure Key Vault para recuperar as suas chaves. Para autenticar para o Key Vault, precisa de uma credencial. É um dilema bootstrap clássico. Identidade de serviço gerida (MSI) resolve este problema, fornecendo uma _inicializar identidade_ que simplifica o processo.

Quando ativar o MSI para um serviço do Azure, como máquinas virtuais do Azure, serviço de aplicações do Azure ou as funções do Azure, o Azure cria um [principal de serviço](key-vault-whatis.md#basic-concepts). MSI faz isso para a instância do serviço no Azure Active Directory (Azure AD) e injeta as credenciais do principal de serviço nessa instância. 

![MSI](media/MSI.png)

Em seguida, para obter um token de acesso, o código chama um serviço de metadados local que está disponível no recurso do Azure. Para autenticar a um serviço do Azure Key Vault, o seu código utiliza o token de acesso que obtém a partir do ponto de final MSI local. 

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar sessão no Azure com a CLI do Azure, introduza:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). 

Selecione um nome do grupo de recursos e preencha o marcador de posição. O exemplo seguinte cria um grupo de recursos na localização E.U.A Oeste:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Utilize o seu grupo de recursos recentemente criada neste tutorial.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Para criar um cofre de chaves no grupo de recursos que criou no passo anterior, forneça as seguintes informações:

* Nome do Cofre de chaves: uma cadeia de caracteres de 3 a 24, que pode conter apenas números (0-9), as letras (a-z, A-Z) e hífenes (-)
* Nome do grupo de recursos
* Localização: **E.U.A. Oeste**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Neste momento, a sua conta do Azure é o único que tem autorização para executar operações neste novo cofre de chaves.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Estamos a adicionar um segredo para ajudar a ilustrar a forma como isto funciona. O segredo pode ser uma cadeia de ligação de SQL ou outras informações que precisa para manter seguros e disponíveis para a aplicação.

Para criar um segredo no Cofre de chaves chamado **AppSecret**, introduza o seguinte comando:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Este segredo armazena o valor **MySecret**.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Pode criar uma máquina virtual utilizando um dos seguintes métodos:

* [A CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli)
* [PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)
* [O portal do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

## <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade para a VM
Neste passo, vai criar uma identidade atribuída de sistema para a máquina virtual ao executar o seguinte comando na CLI do Azure:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Tenha em atenção a identidade atribuída de sistema que é apresentada no código a seguir. Seria a saída do comando anterior: 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Atribuir permissões para a identidade da VM
Agora pode atribuir as permissões de identidade criado anteriormente para o seu Cofre de chaves executando o seguinte comando:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Iniciar sessão na máquina virtual

Para iniciar sessão na máquina virtual, siga as instruções em [Connect e inicie sessão numa máquina virtual do Azure a executar o Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="create-and-run-a-sample-python-app"></a>Criar e executar uma aplicação de Python de exemplo

Na próxima secção é um arquivo de exemplo denominado *Sample.py*. Ele usa um [pedidos](http://docs.python-requests.org/en/master/) biblioteca para fazer chamadas HTTP GET.

## <a name="edit-samplepy"></a>Editar Sample.py

Depois de criar *Sample.py*, abra o ficheiro e, em seguida, copie o código nesta secção. 

O código apresenta um processo de dois passos:
1. Obter um token a partir do ponto de final MSI local na VM.  
  Também se o fizer, obtém um token do Azure AD.
1. Transmitir o token para o seu Cofre de chaves e, em seguida, obter o seu segredo. 

```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.      
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Pode exibir o valor secreto, executando o seguinte código: 

```
python Sample.py
```

O código anterior mostra como fazer operações com o Azure Key Vault numa máquina virtual do Windows. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine a máquina virtual e o seu Cofre de chaves.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [API de REST do Cofre de chaves do Azure](https://docs.microsoft.com/rest/api/keyvault/)
