---
title: Tutorial - Use cofre de chaves Azure com uma máquina virtual Windows em Python Microsoft Docs
description: Neste tutorial, configura uma aplicação ASP.NET central para ler um segredo do seu cofre chave.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 85dc751909f61eff40ad57868bafa7ea2019ed89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422873"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>Tutorial: Use cofre de chave Azure com uma máquina virtual Windows em Python

O Azure Key Vault ajuda-o a proteger segredos como chaves API, as cordas de ligação à base de dados que precisa para aceder às suas aplicações, serviços e recursos de TI.

Neste tutorial, aprende-se a obter uma aplicação de consola para ler informações do Azure Key Vault. Para isso, utiliza-se identidades geridas para os recursos do Azure. 

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Criar um cofre de chaves.
> * Adicione um segredo ao cofre da chave.
> * Obter um segredo do cofre de chaves.
> * Crie uma máquina virtual Azure.
> * Ativar uma identidade gerida.
> * Atribuir permissões à identidade VM.

Antes de começar, leia [os conceitos básicos do Key Vault.](basic-concepts.md) 

Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="prerequisites"></a>Pré-requisitos

Para Windows, Mac e Linux:
  * [Git](https://git-scm.com/downloads)
  * Este tutorial requer que você dirija o Azure CLI localmente. Tem de ter a versão 2.0.4 do Azure CLI ou posteriormente instalada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Sobre a Identidade de Serviço Gerida

O Azure Key Vault armazena credenciais de forma segura, por isso não são exibidos no seu código. No entanto, precisa autenticar o Cofre de Chaves Azure para recuperar as suas chaves. Para autenticar o Cofre chave, precisa de uma credencial. É um dilema clássico de botas. A Identidade de Serviço Gerida (MSI) resolve este problema fornecendo uma identidade de _bootstrap_ que simplifica o processo.

Quando ativa o MSI para um serviço Azure, como máquinas virtuais Azure, Serviço de Aplicações Azure ou Funções Azure, o Azure cria um diretor de [serviço.](basic-concepts.md) A MSI faz isto por exemplo do serviço no Azure Ative Directory (Azure AD) e injeta as credenciais principais do serviço nesse caso. 

![MSI](../media/MSI.png)

Em seguida, para obter um sinal de acesso, o seu código chama um serviço de metadados local que está disponível no recurso Azure. Para autenticar um serviço Azure Key Vault, o seu código utiliza o sinal de acesso que obtém do ponto final local da MSI. 

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

Você usa o seu grupo de recursos recém-criado em todo este tutorial.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Para criar um cofre chave no grupo de recursos que criou na etapa anterior, forneça as seguintes informações:

* Nome do cofre chave: uma cadeia de 3 a 24 caracteres que pode conter apenas números (0-9), letras (a-z, A-Z) e hífenes (-)
* Nome do grupo de recursos
* Localização: **Oeste DOS EUA**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Neste ponto, a sua conta Azure é a única que está autorizada a realizar operações neste novo cofre chave.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Estamos a adicionar um segredo para ajudar a ilustrar a forma como isto funciona. O segredo pode ser uma cadeia de ligação SQL ou qualquer outra informação que você precisa para manter seguro e disponível para a sua aplicação.

Para criar um segredo no cofre de chaves chamado **AppSecret,** insira o seguinte comando:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Este segredo armazena o valor **MySecret.**

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Pode criar uma máquina virtual utilizando um dos seguintes métodos:

* [O Azure CLI](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [O portal do Azure](../../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade ao VM
Neste passo, cria-se uma identidade atribuída ao sistema para a máquina virtual, executando o seguinte comando no ClI Azure:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Note a identidade atribuída ao sistema que está exposta no seguinte código. A saída do comando anterior seria: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Atribuir permissões à identidade VM
Agora pode atribuir as permissões de identidade previamente criadas ao seu cofre chave, executando o seguinte comando:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Iniciar sessão na máquina virtual

Para iniciar sessão na máquina virtual, siga as instruções em [Connect e inicie sessão numa máquina virtual Azure que executa](../../virtual-machines/windows/connect-logon.md)o Windows .

## <a name="create-and-run-a-sample-python-app"></a>Crie e execute uma aplicação python de amostra

Na secção seguinte está um ficheiro de exemplo chamado *Sample.py*. Usa uma biblioteca de [pedidos](https://2.python-requests.org/en/master/) para fazer chamadas HTTP GET.

## <a name="edit-samplepy"></a>Editar Sample.py

Depois de criar *Sample.py,* abra o ficheiro e, em seguida, copie o código nesta secção. 

O código apresenta um processo em duas etapas:
1. Pegue um símbolo do ponto final local da MSI no VM.  
  Fazê-lo também requer um símbolo da Azure AD.
1. Passe o símbolo para o seu cofre e depois pegue o seu segredo. 

```python
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

Pode mostrar o valor secreto executando o seguinte código: 

```console
python Sample.py
```

O código anterior mostra-lhe como fazer operações com o Azure Key Vault numa máquina virtual windows. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, apague a máquina virtual e o cofre da chave.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Cofre de Chaves Azure REST API](https://docs.microsoft.com/rest/api/keyvault/)
