---
title: Tutorial – armazenar o estado do Terraform no armazenamento do Azure
description: Uma introdução ao armazenamento do estado Terraform no armazenamento do Azure.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 2e76da32e25451084d595b10698fe663c55b6a4b
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969528"
---
# <a name="tutorial-store-terraform-state-in-azure-storage"></a>Tutorial: armazenar o estado de Terraform no armazenamento do Azure

O estado Terraform é usado para reconciliar os recursos implantados com as configurações do Terraform. O estado permite que o Terraform saiba quais recursos do Azure adicionar, atualizar ou excluir. Por padrão, o estado Terraform é armazenado localmente ao executar o comando `terraform apply`. Essa configuração não é ideal pelos seguintes motivos:

- O estado local não funciona bem em uma equipe ou em um ambiente de colaboração
- O estado Terraform pode incluir informações confidenciais
- Armazenar o estado localmente aumenta a chance de exclusão inadvertida

O Terraform dá suporte à persistência de estado no armazenamento remoto. Um desses back-end com suporte é o armazenamento do Azure. Este documento mostra como configurar e usar o armazenamento do Azure para essa finalidade.

## <a name="configure-storage-account"></a>Configurar conta de armazenamento

Antes de usar o armazenamento do Azure como um back-end, uma conta de armazenamento deve ser criada. A conta de armazenamento pode ser criada com o portal do Azure, o PowerShell, o CLI do Azure ou o próprio Terraform. Use o exemplo a seguir para configurar a conta de armazenamento com o CLI do Azure.

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

Anote o nome da conta de armazenamento, o nome do contêiner e a chave de acesso de armazenamento. Esses valores são necessários ao configurar o estado remoto.

## <a name="configure-state-backend"></a>Configurar back-end do estado

O back-end do estado Terraform é configurado ao executar o comando `terraform init`. os dados a seguir são necessários para configurar o back-end do Estado:

- storage_account_name-o nome da conta de armazenamento do Azure.
- container_name-o nome do contêiner de BLOB.
- chave-o nome do arquivo de armazenamento de estado a ser criado.
- access_key-a chave de acesso de armazenamento.

Cada um desses valores pode ser especificado no arquivo de configuração Terraform ou na linha de comando, no entanto, é recomendável usar uma variável de ambiente para o `access_key`. O uso de uma variável de ambiente impede que a chave seja gravada no disco.

Crie uma variável de ambiente chamada `ARM_ACCESS_KEY` com o valor da chave de acesso de armazenamento do Azure.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Para proteger ainda mais a chave de acesso da conta de armazenamento do Azure, armazene-a em Azure Key Vault. A variável de ambiente pode ser definida usando um comando semelhante ao seguinte. Para obter mais informações sobre Azure Key Vault, consulte a [documentação do Azure Key Vault] [.. /key-vault/quick-create-cli.md].

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Para configurar o Terraform para usar o back-end, as etapas a seguir precisam ser feitas:
- Inclua um `backend` bloco de configuração com um tipo de `azurerm`.
- Adicione um valor de `storage_account_name` ao bloco de configuração.
- Adicione um valor de `container_name` ao bloco de configuração.
- Adicione um valor de `key` ao bloco de configuração.

O exemplo a seguir configura um back-end Terraform e cria um grupo de recursos do Azure.

```hcl
terraform {
  backend "azurerm" {
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

Inicialize a configuração executando as seguintes etapas:

1. Execute o comando `terraform init`.
1. Execute o comando `terraform apply`.

Agora você pode encontrar o arquivo de estado na Azure Storage Blob.

## <a name="state-locking"></a>Bloqueio de estado

Os blobs de armazenamento do Azure são bloqueados automaticamente antes de qualquer operação que grava o estado. Esse padrão impede operações de estado simultâneas, o que pode causar corrupção. 

Para obter mais informações, consulte [bloqueio de estado] [https://www.terraform.io/docs/state/locking.html ] na documentação do Terraform.

O bloqueio pode ser visto ao examinar o blob por meio do portal do Azure ou outras ferramentas de gerenciamento do Azure.

![Blob do Azure com bloqueio](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Encriptação inativa

Os dados armazenados em um blob do Azure são criptografados antes de serem persistidos. Quando necessário, o Terraform recupera o estado do back-end e o armazena na memória local. Usando esse padrão, o estado nunca é gravado em seu disco local.

Para obter mais informações sobre a criptografia de armazenamento do Azure, consulte [Azure Criptografia do Serviço de Armazenamento for data em repouso] [.. /storage/common/storage-service-encryption.md].

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Terraform no Azure](/azure/ansible/)