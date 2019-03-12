---
title: Utilizar o armazenamento do Azure como um back-end do Terraform
description: Uma introdução ao armazenamento do Estado do Terraform no armazenamento do Azure.
services: terraform
author: tomarchermsft
ms.service: azure
ms.topic: article
ms.date: 09/13/2018
ms.author: tarcher
ms.openlocfilehash: 7145a50bc53fd28afafd3de9c724b5e5f71624fa
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762311"
---
# <a name="store-terraform-state-in-azure-storage"></a>Estado do Terraform Store no armazenamento do Azure

Estado do Terraform é usado para reconciliar os recursos implementados com o Terraform configurações. Utilizar o estado, o Terraform sabe quais recursos do Azure para adicionar, atualizar ou eliminar. Por predefinição, o estado do Terraform é armazenado localmente ao executar o *aplicam-se do Terraform*. Esta configuração não é ideal para alguns dos motivos:

- Estado local não funciona bem numa equipa ou o ambiente de colaboração
- Estado do Terraform pode incluir informações confidenciais
- Armazenamento do Estado localmente aumenta as chances de eliminação acidental

Terraform inclui o conceito de um back-end de estado, que é o armazenamento remoto para o estado do Terraform. Quando utilizar um back-end de estado, o ficheiro de estado é armazenado num arquivo de dados, como o armazenamento do Azure. Este documento fornece detalhes sobre como configurar e utilizar o armazenamento do Azure como um back-end de estado do Terraform.

## <a name="configure-storage-account"></a>Configurar a conta de armazenamento

Antes de utilizar o armazenamento do Azure como um back-end, tem de ser criada uma conta de armazenamento. A conta de armazenamento pode ser criada com o portal do Azure, PowerShell, CLI do Azure ou Terraform em si. Utilize o exemplo a seguir para configurar a conta de armazenamento com a CLI do Azure.

```azurecli-interactive
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

Anote o nome da conta de armazenamento, o nome do contentor e a chave de acesso de armazenamento. Estes valores são necessários quando configurar o estado remoto.

## <a name="configure-state-backend"></a>Configurar o back-end de estado

O back-end de estado do Terraform é configurado ao executar o *Terraform init*. Para configurar o back-end de estado, os seguintes dados são necessários.

- storage_account_name - o nome da conta do Storage do Azure.
- container_name - o nome do contentor de Blobs.
- chave - o nome do Estado de armazenar o ficheiro a ser criado.
- access_key - a chave de acesso de armazenamento.

Cada um destes valores pode ser especificada no ficheiro de configuração de Terraform ou na linha de comandos, no entanto, é recomendável utilizar uma variável de ambiente para o `access_key`. Usar uma variável de ambiente impede que a chave que estão sendo gravados no disco.

Criar uma variável de ambiente com o nome `ARM_ACCESS_KEY` com o valor da chave de acesso do armazenamento do Azure.

```console
export ARM_ACCESS_KEY=<storage access key>
```

Para proteger ainda mais a chave de acesso da conta de armazenamento do Azure, armazene-os no Azure Key Vault. A variável de ambiente, em seguida, pode ser definida com um comando semelhante ao seguinte. Para obter mais informações sobre o Azure Key Vault, consulte a [documentação do Azure Key Vault][azure-key-vault].

```console
export ARM_ACCESS_KEY=$(az keyvault secret show --name terraform-backend-key --vault-name myKeyVault --query value -o tsv)
```

Para configurar o Terraform para utilizar o back-end, inclua uma *back-end* configuração com um tipo de *azurerm* dentro da configuração do Terraform. Adicionar a *storage_account_name*, *container_name*, e *chave* valores para o bloco de configuração.

O exemplo seguinte configura um back-end do Terraform e cria e grupo de recursos do Azure. Substitua os valores pelos valores do seu ambiente.

```json
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

Agora, inicializar a configuração com o *Terraform init* e, em seguida, execute a configuração com *aplicam-se do Terraform*. Quando concluída, pode encontrar o ficheiro de estado no Blob de armazenamento do Azure.

## <a name="state-locking"></a>Estado de bloqueio

Ao usar um Blob de armazenamento do Azure para armazenamento de estado, o blob é automaticamente bloqueado antes de qualquer operação que escreve o estado. Esta configuração impede que várias operações de estado, que podem causar danos. Para obter mais informações, consulte [estado de bloqueio] [ terraform-state-lock] na documentação do Terraform.

O bloqueio pode ser visto ao examinar o blob através do portal do Azure ou outras ferramentas de gestão do Azure.

![BLOBs do Azure com bloqueio](media/terraform-backend/lock.png)

## <a name="encryption-at-rest"></a>Encriptação inativa

Por predefinição, os dados armazenados num Blob do Azure são encriptados antes de persistir a infraestrutura de armazenamento. Quando o Terraform precisa Estado, é obtido a partir do back-end e armazenado na memória no sistema de desenvolvimento. Nesta configuração, o estado é protegido no armazenamento do Azure e não é escrito no disco local.

Para obter mais informações sobre a encriptação de armazenamento do Azure, consulte [encriptação do serviço de armazenamento do Azure para dados Inativos][azure-storage-encryption].

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a configuração de back-end do Terraform no [documentação de back-end do Terraform][terraform-backend].

<!-- LINKS - internal -->
[azure-key-vault]: ../key-vault/quick-create-cli.md
[azure-storage-encryption]: ../storage/common/storage-service-encryption.md

<!-- LINKS - external -->
[terraform-azurerm]: https://www.terraform.io/docs/backends/types/azurerm.html
[terraform-backend]: https://www.terraform.io/docs/backends/
[terraform-state-lock]: https://www.terraform.io/docs/state/locking.html
