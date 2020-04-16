---
title: Tutorial - Use uma máquina virtual Linux e uma aplicação Python para armazenar segredos no Cofre chave Azure [ Microsoft Docs
description: Neste tutorial, aprende-se a configurar uma aplicação Python para ler um segredo do Cofre chave azure.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: df089f0338a177c08f4d9e88d55b501fd12f88f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423321"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>Tutorial: Use um Linux VM e uma app Python para armazenar segredos no Cofre de Chaves Azure

O Azure Key Vault ajuda-o a proteger segredos como as chaves API e as cordas de ligação à base de dados necessárias para aceder às suas aplicações, serviços e recursos de TI.

Neste tutorial, você criou uma aplicação web Azure para ler informações do Azure Key Vault usando identidades geridas para recursos Azure. Saiba como:

> [!div class="checklist"]
> * Criar um cofre de chaves
> * Guarde um segredo no seu cofre de chaves
> * Criar uma máquina virtual do Linux
> * Ativar uma [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md) para a máquina virtual
> * Conceda as permissões necessárias para que a aplicação da consola leia os dados do cofre chave
> * Recupere um segredo do seu cofre chave.

Antes de ir mais longe, certifique-se de entender os [conceitos básicos sobre o Cofre chave.](../general/basic-concepts.md)

## <a name="prerequisites"></a>Pré-requisitos

* [Git.](https://git-scm.com/downloads)
* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* [Versão Azure CLI 2.0.4 ou posterior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ou Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Compreender identidade de serviço gerido

O Cofre de Chaves Azure pode armazenar credenciais de forma segura para que não estejam no seu código. Para recuperá-los, precisa autenticar o Cofre chave Azure. No entanto, para autenticar o Cofre chave, precisa de uma credencial. É um problema clássico de botas. Através do Azure e do Azure Ative Directory (Azure AD), a Managed Service Identity (MSI) fornece uma identidade de bootstrap que torna mais simples começar as coisas.

Quando ativa o MSI para um serviço Azure como Máquinas Virtuais, Serviço de Aplicações ou Funções, o Azure cria um diretor de serviço para a instância do serviço em Azure AD. Injeta as credenciais para o diretor de serviço na instância do serviço.

![MSI](../media/MSI.png)

Em seguida, o seu código chama um serviço de metadados local disponível no recurso Azure para obter um sinal de acesso. O seu código usa o sinal de acesso que obtém do ponto final local da MSI para autenticar um serviço Azure Key Vault.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar sessão no Azure utilizando o Azure CLI, introduza:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Crie um grupo `az group create` de recursos utilizando o comando na localização dos EUA Ocidentais com o seguinte código. Substitua-o `YourResourceGroupName` por um nome à sua escolha.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Você usa este grupo de recursos em todo o tutorial.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Em seguida, cria-se um cofre chave no grupo de recursos que criou no passo anterior. Forneça as seguintes informações:

* Nome do cofre chave: O nome deve ser uma cadeia de caracteres 3-24 e deve conter apenas 0-9, a-z, A-Z e hífenes (-).
* Nome do grupo de recursos.
* Localização: **E.U.A. Oeste**.

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Nesta altura, a sua conta do Azure é a única autorizada a realizar quaisquer operações neste novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Estamos a adicionar um segredo para ajudar a ilustrar a forma como isto funciona. Pode querer armazenar uma cadeia de ligação SQL ou qualquer outra informação que precise de ser mantida segura e disponível para a sua aplicação.

Escreva os seguintes comandos para criar um segredo no cofre de chaves designado *AppSecret*. Este segredo irá armazenar o valor **MySecret**.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Criar uma máquina virtual do Linux

Crie um VM `az vm create` utilizando o comando.

O seguinte exemplo cria uma VM com o nome **myVM** e adiciona uma conta de utilizador com o nome **azureuser**. O `--generate-ssh-keys` parâmetro gera automaticamente uma tecla SSH e coloca-a na localização da chave predefinida **(~/.ssh**). Para criar um conjunto específico de `--ssh-key-value` teclas, utilize a opção.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

São necessários alguns minutos para criar a VM e os recursos de suporte. A saída de exemplo que se segue mostra que a criação de VM foi bem sucedida:

```output
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

Tome nota própria `publicIpAddress` na saída do seu VM. Usará este endereço para aceder ao VM em etapas posteriores.

## <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade ao VM

Criar uma identidade atribuída ao sistema para a máquina virtual executando o seguinte comando:

```azurecli-interactive
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

A saída do comando é a seguinte.

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Tome nota `systemAssignedIdentity`do . Usa-o no próximo passo.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Dê a autorização de identidade vM para o Cofre chave

Agora pode dar permissão ao Cofre chave para a identidade que criou. Execute o seguinte comando:

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Iniciar sessão no VM

Inicie sessão na máquina virtual utilizando um terminal.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>Instale biblioteca Python no VM

Descarregue e instale os [pedidos](https://pypi.org/project/requests/2.7.0/) da biblioteca Python para fazer chamadas HTTP GET.

## <a name="create-edit-and-run-the-sample-python-app"></a>Criar, editar e executar a app Python de amostra

Crie um ficheiro Python chamado **Sample.py.**

Abra Sample.py e edite-o para conter o seguinte código:

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

O código anterior executa um processo em duas etapas:

   1. Pega um símbolo do ponto final local da MSI no VM. O ponto final, em seguida, pega um símbolo do Diretório Ativo Azure.
   1. Passa o símbolo para o cofre e pega o teu segredo.

Execute o seguinte comando. Devia ver o valor secreto.

```console
python Sample.py
```

Neste tutorial, aprendeu a usar o Azure Key Vault com uma aplicação Python a funcionar numa máquina virtual Linux.

## <a name="clean-up-resources"></a>Limpar recursos

Elimine o grupo de recursos, a máquina virtual e todos os recursos relacionados quando já não precisar deles. Para tal, selecione o grupo de recursos para a VM e selecione **Eliminar**.

Elimine o cofre `az keyvault delete` da chave utilizando o comando:

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Cofre de Chaves Azure REST API](https://docs.microsoft.com/rest/api/keyvault/)
