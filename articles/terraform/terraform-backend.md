---
title: Tutorial - Armazenar estado terraforme em Armazenamento Azure
description: Uma introdução ao armazenamento do estado Terraform no Armazenamento Azure.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 1cc475e5070b21a7ea96585f2183c07d258acdc5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75708429"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>Tutorial: Armazenar estado terraforme em armazenamento azure

O estado terraforme é usado para conciliar recursos implantados com configurações terraformes. O Estado permite que a Terraform saiba quais os recursos do Azure para adicionar, atualizar ou eliminar. Por padrão, o estado Terraform é `terraform apply` armazenado localmente quando executa o comando. Esta configuração não é ideal pelas seguintes razões:

- O estado local não funciona bem numa equipa ou num ambiente colaborativo.
- O estado terraforme pode incluir informações sensíveis.
- Armazenar o estado localmente aumenta a possibilidade de eliminação inadvertida.

A Terraform apoia a persistência do estado no armazenamento remoto. Uma dessas traseiras apoiada é o Armazenamento Azure. Este documento mostra como configurar e utilizar o Armazenamento Azure para este fim.

## <a name="configure-storage-account"></a>Configure conta de armazenamento

Antes de utilizar o Armazenamento Azure como backback, tem de criar uma conta de armazenamento. A conta de armazenamento pode ser criada com o portal Azure, PowerShell, o Azure CLI ou a própria Terraform. Utilize a seguinte amostra para configurar a conta de armazenamento com o Azure CLI.

```azurecli
#!/bin/bash

RESOURCE_GROUP_NAME=tstate
STORAGE_ACCOUNT_NAME=tstate$RANDOM
CONTAINER_NAME=tstate

# Create resource group
az group create --name $RESOURCE_GROUP_NAME --location eastus

# Create storage account
az storage account create --resource-group $RESOURCE_GROUP_NAME --name $STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob

# Get storage account key
ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query [0].value -o tsv)

# Create blob container
az storage container create --name $CONTAINER_NAME --account-name $STORAGE_ACCOUNT_NAME --account-key $ACCOUNT_KEY

echo "storage_account_name: $STORAGE_ACCOUNT_NAME"
echo "container_name: $CONTAINER_NAME"
echo "access_key: $ACCOUNT_KEY"
```

Tome nota do nome da conta de armazenamento, nome do recipiente e chave de acesso ao armazenamento. Estes valores são necessários quando configurao o estado remoto.

## <a name="configure-state-back-end"></a>Configure o final do estado

A parte de trás do estado Terraform está configurada quando dirige o `terraform init` comando. Os seguintes dados são necessários para configurar o back back end do Estado:

- **storage_account_name**: O nome da conta De armazenamento Azure.
- **container_name:** O nome do recipiente de bolhas.
- **chave**: O nome do ficheiro da loja estatal a criar.
- **access_key:** A chave de acesso ao armazenamento.

Cada um destes valores pode ser especificado no ficheiro de configuração Terraform ou na linha de comando. Recomendamos que utilize uma variável ambiental para o `access_key` valor. A utilização de uma variável ambiental impede que a chave seja escrita em disco.

Crie uma `ARM_ACCESS_KEY` variável ambiental nomeada com o valor da chave de acesso ao Armazenamento Azure.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Para proteger ainda mais a chave de acesso à conta Azure Storage, guarde-a no Cofre de Chaves Azure. A variável ambiental pode então ser definida utilizando um comando semelhante ao seguinte. Para mais informações sobre o Cofre de Chaves Azure, consulte a documentação do Cofre de [Chaves Azure](../key-vault/quick-create-cli.md).

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Para configurar a Terrafora para utilizar a parte de trás, devem ser feitos os seguintes passos:
- Inclua `backend` um bloco de `azurerm`configuração com um tipo de .
- Adicione `storage_account_name` um valor ao bloco de configuração.
- Adicione `container_name` um valor ao bloco de configuração.
- Adicione `key` um valor ao bloco de configuração.

O exemplo seguinte configura uma extremidade terraforme e cria um grupo de recursos Azure.

```hcl
terraform {
  backend "azurerm" {
    resource_group_name   = "tstate"
    storage_account_name  = "tstate09762"
    container_name        = "tstate"
    key                   = "terraform.tfstate"
  }
}

resource "azurerm_resource_group" "state-demo-secure" {
  name     = "state-demo"
  location = "eastus"
}
```

Inicializar a configuração fazendo os seguintes passos:

1. Execute o comando `terraform init`.
1. Execute o comando `terraform apply`.

Agora pode encontrar o ficheiro estatal na bolha de armazenamento Azure.

## <a name="state-locking"></a>Bloqueio do Estado

As bolhas de armazenamento azure são automaticamente bloqueadas antes de qualquer operação que escreva estado. Este padrão previne operações estatais simultâneas, que podem causar corrupção. 

Para mais informações, consulte o [Bloqueio do Estado](https://www.terraform.io/docs/state/locking.html) na documentação terraforme.

Pode ver o bloqueio quando examina a bolha através do portal Azure ou de outras ferramentas de gestão Azure.

![Bolha azul com fechadura](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Encriptação inativa

Os dados armazenados numa bolha Azure são encriptados antes de serem persistidos. Quando necessário, a Terraform recupera o estado a partir da parte de trás e armazena-o na memória local. Usando este padrão, o estado nunca é escrito para o seu disco local.

Para obter mais informações sobre encriptação do Armazenamento Azure, consulte a encriptação do [serviço de armazenamento Azure para obter dados em repouso](../storage/common/storage-service-encryption.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Saiba mais sobre a utilização da Terraform em Azure](/azure/terraform)
