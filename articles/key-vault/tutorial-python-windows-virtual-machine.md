---
title: Tutorial-usar o Azure Key Vault com uma máquina virtual do Windows no Python | Microsoft Docs
description: Neste tutorial, você configura um aplicativo ASP.NET Core para ler um segredo do cofre de chaves.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: ae9aaea91f62a7e79835e65a5a9f9c76017afdb1
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934388"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>Tutorial: Usar Azure Key Vault com uma máquina virtual do Windows no Python

Azure Key Vault ajuda a proteger segredos como chaves de API, as cadeias de conexão de banco de dados de que você precisa para acessar seus aplicativos, serviços e recursos de ti.

Neste tutorial, você aprenderá a obter um aplicativo de console para ler informações de Azure Key Vault. Para fazer isso, você usa identidades gerenciadas para recursos do Azure. 

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Criar um cofre de chaves.
> * Adicione um segredo ao cofre de chaves.
> * Obter um segredo do cofre de chaves.
> * Crie uma máquina virtual do Azure.
> * Habilitar uma identidade gerenciada.
> * Atribua permissões à identidade da VM.

Antes de começar, leia [Key Vault conceitos básicos](key-vault-whatis.md#basic-concepts). 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Pré-requisitos

Para Windows, Mac e Linux:
  * [Git](https://git-scm.com/downloads)
  * Este tutorial requer que você execute o CLI do Azure localmente. Você deve ter o CLI do Azure versão 2.0.4 ou posterior instalado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI 2.0 do Azure](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Sobre a Identidade de Serviço Gerida

O Azure Key Vault armazena as credenciais com segurança, portanto, elas não são exibidas no seu código. No entanto, você precisa autenticar-se no Azure Key Vault para recuperar suas chaves. Para autenticar no Key Vault, você precisa de uma credencial. É um dilema de Bootstrap clássico. O Identidade de Serviço Gerenciada (MSI) resolve esse problema fornecendo uma _identidade de Bootstrap_ que simplifica o processo.

Quando você habilita o MSI para um serviço do Azure, como máquinas virtuais do Azure, serviço Azure App ou Azure Functions, o Azure cria uma [entidade de serviço](key-vault-whatis.md#basic-concepts). O MSI faz isso para a instância do serviço no Azure Active Directory (Azure AD) e injeta as credenciais da entidade de serviço nessa instância. 

![MSI](media/MSI.png)

Em seguida, para obter um token de acesso, seu código chama um serviço de metadados local que está disponível no recurso do Azure. Para autenticar em um serviço de Azure Key Vault, seu código usa o token de acesso que ele obtém do ponto de extremidade MSI local. 

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

Você usa o grupo de recursos criado recentemente ao longo deste tutorial.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Para criar um cofre de chaves no grupo de recursos que você criou na etapa anterior, forneça as seguintes informações:

* Nome do cofre de chaves: uma cadeia de 3 a 24 caracteres que pode conter apenas números (0-9), letras (a-z, A-Z) e hifens (-)
* Nome do grupo de recursos
* Localização: **E.U.A. Oeste**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Neste ponto, sua conta do Azure é a única que está autorizada a executar operações nesse novo cofre de chaves.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Estamos a adicionar um segredo para ajudar a ilustrar a forma como isto funciona. O segredo pode ser uma cadeia de conexão SQL ou qualquer outra informação que você precise manter segura e disponível para seu aplicativo.

Para criar um segredo no cofre de chaves chamado **AppSecret**, insira o seguinte comando:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Esse segredo armazena o valor **MySecret**.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Você pode criar uma máquina virtual usando um dos seguintes métodos:

* [A CLI do Azure](../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../virtual-machines/windows/quick-create-powershell.md)
* [O portal do Azure](../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade à VM
Nesta etapa, você cria uma identidade atribuída pelo sistema para a máquina virtual executando o seguinte comando no CLI do Azure:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Observe a identidade atribuída pelo sistema que é exibida no código a seguir. A saída do comando anterior seria: 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Atribuir permissões à identidade da VM
Agora você pode atribuir as permissões de identidade criadas anteriormente ao cofre de chaves executando o seguinte comando:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Iniciar sessão na máquina virtual

Para fazer logon na máquina virtual, siga as instruções em [conectar e fazer logon em uma máquina virtual do Azure que executa o Windows](../virtual-machines/windows/connect-logon.md).

## <a name="create-and-run-a-sample-python-app"></a>Criar e executar um aplicativo Python de exemplo

Na próxima seção, há um arquivo de exemplo chamado *Sample.py*. Ele usa uma biblioteca de [solicitações](http://docs.python-requests.org/en/master/) para fazer chamadas http Get.

## <a name="edit-samplepy"></a>Editar Sample.py

Depois de criar o *Sample.py*, abra o arquivo e copie o código nesta seção. 

O código apresenta um processo de duas etapas:
1. Busque um token do ponto de extremidade MSI local na VM.  
  Isso também busca um token do Azure AD.
1. Passe o token para o cofre de chaves e busque seu segredo. 

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

Você pode exibir o valor secreto executando o seguinte código: 

```
python Sample.py
```

O código anterior mostra como fazer operações com Azure Key Vault em uma máquina virtual do Windows. 

## <a name="clean-up-resources"></a>Limpar recursos

Quando eles não forem mais necessários, exclua a máquina virtual e o cofre de chaves.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [API REST do Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
