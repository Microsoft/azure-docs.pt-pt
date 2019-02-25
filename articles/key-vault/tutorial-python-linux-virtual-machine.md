---
title: Tutorial - utilizar uma máquina virtual Linux e uma aplicação Python para armazenar segredos no Azure Key Vault | Documentos da Microsoft
description: Neste tutorial, saiba como configurar uma aplicação Python ao ler um segredo do Cofre de chaves do Azure.
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
ms.openlocfilehash: b7077653ec959f99491cecd71573c091772448f4
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749635"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>Tutorial: Utilizar uma VM do Linux e uma aplicação de Python para armazenar segredos no Azure Key Vault

O Azure Key Vault ajuda a proteger segredos, como as chaves de API e as cadeias de ligação necessárias para acessar seus aplicativos, serviços e recursos de TI de base de dados.

Neste tutorial, vai configurar uma aplicação web do Azure para ler as informações do Azure Key Vault através de identidades geridas para recursos do Azure. Saiba como:

> [!div class="checklist"]
> * Criar um cofre de chaves
> * Store um segredo no Cofre de chaves
> * Criar uma Máquina Virtual do Linux
> * Ativar uma [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) para a máquina virtual
> * Conceder as permissões necessárias para a aplicação de consola para ler dados a partir do Cofre de chaves
> * Obter um segredo a partir do seu Cofre de chaves

Antes de prosseguir, certifique-se de que compreende os [conceitos básicos sobre o Key Vault](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Pré-requisitos

* [Git](https://git-scm.com/downloads).
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* [CLI do Azure versão 2.0.4 ou posterior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ou o Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Compreender a identidade do serviço gerido

O Azure Key Vault pode armazenar credenciais em segurança, para que eles não estão no seu código. Para recuperá-los, tem de autenticar para o Azure Key Vault. No entanto, para autenticar para o Key Vault, terá uma credencial. É um problema de bootstrap clássico. Através do Azure e Azure Active Directory (Azure AD), a identidade de serviço gerida (MSI) fornece uma identidade de arranque de configuração que torna mais simples de dar início ao processo.

Quando ativar o MSI para um serviço do Azure como máquinas virtuais, o serviço de aplicações ou funções, o Azure cria um serviço principal para a instância do serviço no Azure AD. Ele injeta as credenciais do principal de serviço para a instância do serviço.

![MSI](media/MSI.png)

Em seguida, o seu código chama um serviço de metadados locais disponível no recurso do Azure para obter um token de acesso. O código utiliza o token de acesso que obtém a partir do ponto de final MSI local para autenticar a um serviço do Azure Key Vault.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar sessão no Azure com a CLI do Azure, introduza:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Criar um grupo de recursos com o `az group create` comando na localização E.U.A. oeste com o código a seguir. Substitua `YourResourceGroupName` com um nome à sua escolha.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Utilize este grupo de recursos ao longo do tutorial.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Em seguida, cria um cofre de chaves no grupo de recursos que criou no passo anterior. Forneça as seguintes informações:

* Nome do Cofre de chaves: O nome tem de ser uma cadeia de caracteres de 3 a 24 carateres e tem de conter apenas 0-9, a-z, A-Z e hífenes (-).
* Nome do grupo de recursos.
* Localização: **Oeste dos E.U.A.**.

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Nesta altura, a sua conta do Azure é a única autorizada a realizar quaisquer operações neste novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Estamos a adicionar um segredo para ajudar a ilustrar a forma como isto funciona. Convém armazenar uma cadeia de ligação de SQL ou outras informações que tem de ser ambos mantidos seguros e disponíveis para a aplicação.

Escreva os seguintes comandos para criar um segredo no cofre de chaves designado *AppSecret*. Este segredo irá armazenar o valor **MySecret**.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Criar uma Máquina Virtual do Linux

Criar uma VM com o `az vm create` comando.

O seguinte exemplo cria uma VM com o nome **myVM** e adiciona uma conta de utilizador com o nome **azureuser**. O `--generate-ssh-keys` parâmetro automaticamente gera uma chave SSH e coloca-o na localização predefinida da chave (**~/.ssh**). Para criar um conjunto específico de chaves em vez disso, utilize o `--ssh-key-value` opção.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

São necessários alguns minutos para criar a VM e os recursos de suporte. O resultado de exemplo seguinte mostra que a criação de VM foi concluída com êxito:

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

Tome nota do seu próprio `publicIpAddress` na saída da VM. Utilizará este endereço para aceder à VM em passos posteriores.

## <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade para a VM

Crie uma identidade do sistema atribuídos à máquina virtual ao executar o seguinte comando:

```azurecli-interactive
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Segue-se a saída do comando.

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Anote o `systemAssignedIdentity`. Utilize o passo seguinte.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Conceder a permissão de identidade VM para o Key Vault

Agora pode dar permissão de Key Vault para a identidade que criou. Execute o seguinte comando:

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Iniciar sessão VM

Inicie sessão na máquina virtual com um terminal.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>Instalar a biblioteca de Python na VM

Transferir e instalar o [pedidos](https://pypi.org/project/requests/2.7.0/) biblioteca de Python para fazer chamadas HTTP GET.

## <a name="create-edit-and-run-the-sample-python-app"></a>Criar, editar e executar a aplicação de Python de exemplo

Crie um ficheiro Python denominado **Sample.py**.

Abra Sample.py e editá-lo para conter o seguinte código:

```python
# importing the requests library
  import requests
  
# Step 1: Fetch an access token from an MSI-enabled Azure resource      
  # Note that the resource here is https://vault.azure.net for the public cloud, and api-version is 2018-02-01
  MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
  r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"})

# Extracting data in JSON format 
  # This request gets an access token from Azure Active Directory by using the local MSI endpoint
  data = r.json()

# Step 2: Pass the access token received from the previous HTTP GET call to the key vault
  KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
  kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})

print(kvSecret.json()["value"])
```

O código anterior executa um processo de dois passos:

   1. Obtém um token a partir do ponto de final MSI local na VM. O ponto final, em seguida, obtém um token do Azure Active Directory.
   1. Transmite o token para o Cofre de chaves e obtém seu segredo.

Execute o seguinte comando. Deverá ver o valor secreto.

```console
python Sample.py
```

Neste tutorial, aprendeu a utilizar o Azure Key Vault com uma aplicação de Python em execução numa máquina virtual Linux.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar, elimine o grupo de recursos, a máquina virtual e todos os recursos relacionados. Para tal, selecione o grupo de recursos para a VM e selecione **eliminar**.

Eliminar o Cofre de chaves utilizando o `az keyvault delete` comando:

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [API de REST do Cofre de chaves do Azure](https://docs.microsoft.com/rest/api/keyvault/)
