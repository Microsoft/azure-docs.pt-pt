---
title: Tutorial - Use Azure Key Vault com uma máquina virtual em Python Microsoft Docs
description: Neste tutorial, você configura uma aplicação principal ASP.NET para ler um segredo do seu cofre chave.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, tracking-python
ms.openlocfilehash: 453307b304c4cb1899b1de31117c944ac66fcddb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101883"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Tutorial: Use a adóia da chave Azure com uma máquina virtual em Python

O Azure Key Vault ajuda-o a proteger segredos como as chaves API, as cadeias de ligação de base de dados que precisa para aceder às suas aplicações, serviços e recursos de TI.

Neste tutorial, aprende-se a obter uma aplicação de consola para ler informações do Azure Key Vault. Para tal, usa identidades geridas para recursos Azure. 

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Criar um cofre de chaves.
> * Adicione um segredo ao cofre das chaves.
> * Obter um segredo do cofre de chaves.
> * Crie uma máquina virtual Azure.
> * Ativar uma identidade gerida.
> * Atribua permissões à identidade VM.

Antes de começar, leia [os conceitos básicos do Key Vault.](basic-concepts.md) 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Pré-requisitos

Para Windows, Mac e Linux:
  * [Rio Git](https://git-scm.com/downloads)
  * Este tutorial requer que você gere o Azure CLI localmente. Deve ter a versão Azure CLI 2.0.4 ou posteriormente instalada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar sessão no Azure com a CLI do Azure, introduza:

```azurecli
az login
```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e cofre chave

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Estamos a adicionar um segredo para ajudar a ilustrar a forma como isto funciona. O segredo pode ser uma cadeia de ligação SQL ou qualquer outra informação que você precisa para manter seguro e disponível para a sua aplicação.

Para criar um segredo no cofre de chaves chamado **AppSecret,** insira o seguinte comando:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Este segredo armazena o valor **MySecret.**

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Pode criar uma máquina virtual utilizando um dos seguintes métodos:

* [A CLI do Azure](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [O portal Azure](../../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade ao VM
Neste passo, cria-se uma identidade atribuída ao sistema para a máquina virtual executando o seguinte comando no Azure CLI:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Note a identidade atribuída ao sistema que é apresentada no seguinte código. A saída do comando anterior seria: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Atribuir permissões à identidade VM
Agora pode atribuir as permissões de identidade previamente criadas para o seu cofre de chaves executando o seguinte comando:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Iniciar sessão na máquina virtual

Para iniciar sessão na máquina virtual, siga as instruções em [Connect e inicie sessão numa máquina virtual Azure que executa o Windows](../../virtual-machines/windows/connect-logon.md).

## <a name="create-and-run-a-sample-python-app"></a>Criar e executar uma aplicação Python de amostra

Na secção seguinte está um ficheiro de exemplo chamado *Sample.py*. Utiliza uma biblioteca [de pedidos](https://2.python-requests.org/en/master/) para estórias HTTP GET.

## <a name="edit-samplepy"></a>Editar Sample.py

Depois de criar *Sample.py,* abra o ficheiro e, em seguida, copie o código nesta secção. 

O código apresenta um processo em duas etapas:
1. Pegue um símbolo do ponto final da MSI local no VM.  
  Ao fazê-lo, também é buscar um símbolo da Azure AD.
1. Passe o símbolo para o seu cofre chave, e depois pegue o seu segredo. 

```python
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.
    # Resources with an MSI configured recieve an AAD access token by using the Azure Instance Metadata Service (IMDS)
    # IMDS provides an endpoint accessible to all IaaS VMs using a non-routable well-known IP Address
    # To learn more about IMDS and MSI Authentication see the following link: https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://{YOUR KEY VAULT NAME}.vault.azure.net/secrets/{YOUR SECRET NAME}?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Pode exibir o valor secreto executando o seguinte código: 

```console
python Sample.py
```

O código anterior mostra-lhe como fazer operações com o Azure Key Vault numa máquina virtual Windows. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, elimine a máquina virtual e o cofre da chave.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Cofre chave Azure REST API](https://docs.microsoft.com/rest/api/keyvault/)
