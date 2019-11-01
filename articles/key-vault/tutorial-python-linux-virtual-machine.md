---
title: Tutorial – usar uma máquina virtual Linux e um aplicativo Python para armazenar segredos no Azure Key Vault | Microsoft Docs
description: Neste tutorial, você aprenderá a configurar um aplicativo Python para ler um segredo de Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 15650de776b481d1635b58f2b8ecf2bf2921d12f
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242412"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>Tutorial: usar uma VM do Linux e um aplicativo Python para armazenar segredos no Azure Key Vault

Azure Key Vault ajuda a proteger segredos como as chaves de API e as cadeias de conexão de banco de dados necessárias para acessar seus aplicativos, serviços e recursos de ti.

Neste tutorial, você configura um aplicativo Web do Azure para ler informações de Azure Key Vault usando identidades gerenciadas para recursos do Azure. Saiba como:

> [!div class="checklist"]
> * Criar um cofre de chaves
> * Armazenar um segredo em seu cofre de chaves
> * Criar uma Máquina Virtual do Linux
> * Habilitar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para a máquina virtual
> * Conceder as permissões necessárias para o aplicativo de console ler dados do cofre de chaves
> * Recuperar um segredo do cofre de chaves

Antes de prosseguir, certifique-se de entender os [conceitos básicos sobre Key Vault](basic-concepts.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Git](https://git-scm.com/downloads).
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* [CLI do Azure versão 2.0.4 ou posterior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ou Azure cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Entender Identidade de Serviço Gerenciada

Azure Key Vault pode armazenar credenciais com segurança para que elas não estejam em seu código. Para recuperá-los, você precisa autenticar-se no Azure Key Vault. No entanto, para autenticar no Key Vault, você precisa de uma credencial. É um problema de inicialização clássico. Por meio do Azure e do Azure Active Directory (Azure AD), o Identidade de Serviço Gerenciada (MSI) fornece uma identidade de bootstrap que torna mais simples começar as coisas.

Quando você habilita o MSI para um serviço do Azure como máquinas virtuais, serviço de aplicativo ou funções, o Azure cria uma entidade de serviço para a instância do serviço no Azure AD. Ele injeta as credenciais da entidade de serviço na instância do serviço.

![MSI](media/MSI.png)

Em seguida, seu código chama um serviço de metadados local disponível no recurso do Azure para obter um token de acesso. Seu código usa o token de acesso que obtém do ponto de extremidade MSI local para autenticar em um serviço de Azure Key Vault.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para entrar no Azure usando o CLI do Azure, digite:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos usando o comando `az group create` no local oeste dos EUA com o código a seguir. Substitua `YourResourceGroupName` pelo nome de sua escolha.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Você usa esse grupo de recursos em todo o tutorial.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Em seguida, crie um cofre de chaves no grupo de recursos que você criou na etapa anterior. Forneça as seguintes informações:

* Nome do cofre de chaves: o nome deve ser uma cadeia de 3-24 caracteres e deve conter apenas 0-9, a-z, A-Z e hifens (-).
* Nome do grupo de recursos.
* Localização: **EUA Oeste**.

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Nesta altura, a sua conta do Azure é a única autorizada a realizar quaisquer operações neste novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Estamos a adicionar um segredo para ajudar a ilustrar a forma como isto funciona. Talvez você queira armazenar uma cadeia de conexão SQL ou qualquer outra informação que precise ser mantida segura e disponível para seu aplicativo.

Escreva os seguintes comandos para criar um segredo no cofre de chaves designado *AppSecret*. Este segredo irá armazenar o valor **MySecret**.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Criar uma Máquina Virtual do Linux

Crie uma VM usando o comando `az vm create`.

O seguinte exemplo cria uma VM com o nome **myVM** e adiciona uma conta de utilizador com o nome **azureuser**. O parâmetro `--generate-ssh-keys` gera automaticamente uma chave SSH e a coloca no local de chave padrão ( **~/.ssh**). Para criar um conjunto específico de chaves em vez disso, use a opção `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

São necessários alguns minutos para criar a VM e os recursos de suporte. A saída de exemplo a seguir mostra que a criação da VM foi bem-sucedida:

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

Anote sua própria `publicIpAddress` na saída de sua VM. Você usará esse endereço para acessar a VM em etapas posteriores.

## <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade à VM

Crie uma identidade atribuída pelo sistema para a máquina virtual executando o seguinte comando:

```azurecli-interactive
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

A saída do comando é a seguinte:

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Anote o `systemAssignedIdentity`. Você o usará na próxima etapa.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Conceder permissão de identidade da VM para Key Vault

Agora você pode conceder Key Vault permissão para a identidade que você criou. Execute o seguinte comando:

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Faça logon na VM

Faça logon na máquina virtual usando um terminal.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>Instalar a biblioteca do Python na VM

Baixe e instale a biblioteca do Python de [solicitações](https://pypi.org/project/requests/2.7.0/) para fazer chamadas http Get.

## <a name="create-edit-and-run-the-sample-python-app"></a>Criar, editar e executar o aplicativo Python de exemplo

Crie um arquivo Python chamado **Sample.py**.

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

O código anterior executa um processo de duas etapas:

   1. Busca um token do ponto de extremidade MSI local na VM. Em seguida, o ponto de extremidade busca um token de Azure Active Directory.
   1. Passa o token para o cofre de chaves e busca seu segredo.

Execute o comando a seguir. Você deve ver o valor secreto.

```console
python Sample.py
```

Neste tutorial, você aprendeu a usar Azure Key Vault com um aplicativo Python em execução em uma máquina virtual Linux.

## <a name="clean-up-resources"></a>Limpar recursos

Exclua o grupo de recursos, a máquina virtual e todos os recursos relacionados quando você não precisar mais deles. Para fazer isso, selecione o grupo de recursos para a VM e selecione **excluir**.

Exclua o cofre de chaves usando o comando `az keyvault delete`:

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [API REST do Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
