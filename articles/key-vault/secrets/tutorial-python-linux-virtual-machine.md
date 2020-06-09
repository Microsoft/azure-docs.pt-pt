---
title: Tutorial - Use uma máquina virtual Linux e uma aplicação Python para armazenar segredos no Cofre da Chave Azure Microsoft Docs
description: Neste tutorial, você aprende a configurar uma aplicação Python para ler um segredo de Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc, tracking-python
ms.openlocfilehash: 6b3fb07322009134a75621a19cd013e2f967972a
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561622"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>Tutorial: Use um Linux VM e uma app Python para armazenar segredos no Cofre da Chave Azure

O Azure Key Vault ajuda-o a proteger segredos como as chaves API e as cadeias de ligação de base de dados necessárias para aceder às suas aplicações, serviços e recursos de TI.

Neste tutorial, você criou uma aplicação web Azure para ler informações do Azure Key Vault usando identidades geridas para recursos Azure. Saiba como:

> [!div class="checklist"]
> * Criar um cofre de chaves
> * Guarde um segredo no seu cofre
> * Criar uma máquina virtual do Linux
> * Ativar uma [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md) para a máquina virtual
> * Conceda as permissões necessárias para que a aplicação da consola leia dados a partir do cofre-chave
> * Recupere um segredo do seu cofre

Antes de ir mais longe, certifique-se de compreender os [conceitos básicos sobre o Key Vault.](../general/basic-concepts.md)

## <a name="prerequisites"></a>Pré-requisitos

* [Git.](https://git-scm.com/downloads)
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* [Versão Azure CLI 2.0.4 ou mais tarde](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ou Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Compreender identidade de serviço gerido

O Azure Key Vault pode armazenar as credenciais de forma segura para que não estejam no seu código. Para recuperá-los, precisa autenticar para a Azure Key Vault. No entanto, para autenticar no Key Vault, precisa de uma credencial. É um problema clássico de botas. Através do Azure e do Azure Ative Directory (Azure AD), a Managed Service Identity (MSI) fornece uma identidade de bootstrap que torna mais simples começar as coisas.

Quando ativa o MSI para um serviço Azure como Máquinas Virtuais, Serviço de Aplicações ou Funções, o Azure cria um principal de serviço para o exemplo do serviço em Azure AD. Injeta as credenciais do diretor de serviço no caso do serviço.

![MSI](../media/MSI.png)

Em seguida, o seu código chama um serviço de metadados local disponível no recurso Azure para obter um token de acesso. O seu código utiliza o token de acesso que obtém do ponto final da MSI local para autenticar para um serviço Azure Key Vault.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar súm na Azure utilizando o CLI Azure, insira:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos utilizando o `az group create` comando na localização dos EUA ocidentais com o seguinte código. `YourResourceGroupName`Substitua-o por um nome à sua escolha.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Você usa este grupo de recursos em todo o tutorial.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Em seguida, cria-se um cofre chave no grupo de recursos que criou no passo anterior. Forneça as seguintes informações:

* Nome do cofre chave: O nome deve ser uma sequência de 3-24 caracteres e deve conter apenas 0-9, a-z, A-Z e hífenes (-).
* Nome do grupo de recursos.
* Localização: **E.U.A. Oeste**.

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Nesta altura, a sua conta do Azure é a única autorizada a realizar quaisquer operações neste novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Estamos a adicionar um segredo para ajudar a ilustrar a forma como isto funciona. É melhor armazenar uma cadeia de ligação SQL ou qualquer outra informação que precise de ser mantida segura e disponível para a sua aplicação.

Escreva os seguintes comandos para criar um segredo no cofre de chaves designado *AppSecret*. Este segredo irá armazenar o valor **MySecret**.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Criar uma máquina virtual do Linux

Crie um VM utilizando o `az vm create` comando.

O seguinte exemplo cria uma VM com o nome **myVM** e adiciona uma conta de utilizador com o nome **azureuser**. O `--generate-ssh-keys` parâmetro gera automaticamente uma chave SSH e coloca-a no local da chave predefinido **(~/.ssh).** Para criar um conjunto específico de teclas, utilize a `--ssh-key-value` opção.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

São necessários alguns minutos para criar a VM e os recursos de suporte. O seguinte exemplo de saída mostra que a criação de VM foi bem sucedida:

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

Tome nota de si mesmo `publicIpAddress` na saída do seu VM. Você usará este endereço para aceder ao VM em etapas posteriores.

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

Tome nota do `systemAssignedIdentity` . Usa-o no próximo passo.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Dê a permissão de identidade VM para Key Vault

Agora pode dar permissão ao Key Vault para a identidade que criou. Execute o seguinte comando:

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Faça login no VM

Faça login na máquina virtual utilizando um terminal.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>Instale a biblioteca Python no VM

Faça o download e instale os [pedidos](https://pypi.org/project/requests/2.7.0/) da biblioteca Python para ese fazer chamadas HTTP GET.

## <a name="create-edit-and-run-the-sample-python-app"></a>Criar, editar e executar a aplicação Python de amostra

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

   1. Recolhe um símbolo do ponto final da MSI local no VM. O ponto final então recolhe um símbolo do Azure Ative Directory.
   1. Passa o símbolo para o cofre e vai buscar o teu segredo.

Execute o seguinte comando. Devia ver o valor secreto.

```console
python Sample.py
```

Neste tutorial, aprendeu a usar o Azure Key Vault com uma aplicação Python a funcionar numa máquina virtual Linux.

## <a name="clean-up-resources"></a>Limpar recursos

Elimine o grupo de recursos, a máquina virtual e todos os recursos relacionados quando já não precisar deles. Para tal, selecione o grupo de recursos para a VM e selecione **Eliminar**.

Elimine o cofre de chaves utilizando o `az keyvault delete` comando:

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Cofre chave Azure REST API](https://docs.microsoft.com/rest/api/keyvault/)
