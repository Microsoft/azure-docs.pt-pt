---
title: Usar o armazenamento do Azure como um back-end Terraform
description: Uma introdução ao armazenamento do estado Terraform no armazenamento do Azure.
services: terraform
author: tomarchermsft
ms.service: azure
ms.topic: article
ms.date: 09/20/2019
ms.author: tarcher
ms.openlocfilehash: e9b447f4f4dc9d0ee090da9729e483cc17ac7c15
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169941"
---
# <a name="store-terraform-state-in-azure-storage"></a>Armazenar o estado de Terraform no armazenamento do Azure

O estado Terraform é usado para reconciliar os recursos implantados com as configurações do Terraform. Usando o estado, Terraform sabe quais recursos do Azure adicionar, atualizar ou excluir. Por padrão, o estado Terraform é armazenado localmente quando a execução de *Terraform é aplicada*. Essa configuração não é ideal por alguns motivos:

- O estado local não funciona bem em uma equipe ou em um ambiente de colaboração
- O estado Terraform pode incluir informações confidenciais
- Armazenar o estado localmente aumenta a chance de exclusão inadvertida

Terraform inclui o conceito de um back-end de estado, que é o armazenamento remoto para o estado Terraform. Ao usar um back-end de estado, o arquivo de estado é armazenado em um armazenamento de dados, como o armazenamento do Azure. Este documento fornece detalhes sobre como configurar e usar o armazenamento do Azure como um back-end de estado Terraform.

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

O back-end do estado Terraform é configurado ao executar a *inicialização do Terraform*. Para configurar o back-end de estado, os dados a seguir são necessários.

- storage_account_name-o nome da conta de armazenamento do Azure.
- container_name-o nome do contêiner de BLOB.
- chave-o nome do arquivo de armazenamento de estado a ser criado.
- access_key-a chave de acesso de armazenamento.

Cada um desses valores pode ser especificado no arquivo de configuração Terraform ou na linha de comando, no entanto, é recomendável usar uma variável de `access_key`ambiente para o. O uso de uma variável de ambiente impede que a chave seja gravada no disco.

Crie uma variável de ambiente `ARM_ACCESS_KEY` denominada com o valor da chave de acesso de armazenamento do Azure.

```bash
export ARM_ACCESS_KEY=<storage access key>
```

Para proteger ainda mais a chave de acesso da conta de armazenamento do Azure, armazene-a em Azure Key Vault. A variável de ambiente pode ser definida usando um comando semelhante ao seguinte. Para obter mais informações sobre Azure Key Vault, consulte a [documentação do Azure Key Vault][azure-key-vault].

```bash
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Para configurar o Terraform para usar o back-end, inclua uma configuração de *back-end* com um tipo de *azurerm* dentro da configuração do Terraform. Adicione os valores *storage_account_name*, *container_name*e *Key* ao bloco de configuração.

O exemplo a seguir configura um back-end Terraform e cria um grupo de recursos do Azure. Substitua os valores por valores do seu ambiente.

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

Agora, inicialize a configuração com *Terraform init* e, em seguida, execute a configuração com *Terraform Apply*. Quando concluído, você pode encontrar o arquivo de estado no Azure Storage Blob.

## <a name="state-locking"></a>Bloqueio de estado

Ao usar um Azure Storage Blob para o armazenamento de estado, o blob é bloqueado automaticamente antes de qualquer operação que grava o estado. Essa configuração impede várias operações de estado simultâneas, o que pode causar corrupção. Para obter mais informações, consulte [bloqueio de estado][terraform-state-lock] na documentação do Terraform.

O bloqueio pode ser visto ao examinar o blob por meio do portal do Azure ou outras ferramentas de gerenciamento do Azure.

![Blob do Azure com bloqueio](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Encriptação inativa

Por padrão, os dados armazenados em um blob do Azure são criptografados antes de serem persistidos na infraestrutura de armazenamento. Quando o Terraform precisa de um estado, ele é recuperado do back-end e armazenado na memória no sistema de desenvolvimento. Nessa configuração, o estado é protegido no armazenamento do Azure e não é gravado no disco local.

Para obter mais informações sobre a criptografia de armazenamento do Azure, consulte [criptografia do serviço de armazenamento do Azure para dados em repouso][azure-storage-encryption].

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a configuração de back-end do Terraform na [documentação de back-end do Terraform][terraform-backend].

<!-- LINKS - internal -->
[azure-key-vault]: ../key-vault/quick-create-cli.md
[azure-storage-encryption]: ../storage/common/storage-service-encryption.md

<!-- LINKS - external -->
[terraform-azurerm]: https://www.terraform.io/docs/backends/types/azurerm.html
[terraform-backend]: https://www.terraform.io/docs/backends/
[terraform-state-lock]: https://www.terraform.io/docs/state/locking.html
